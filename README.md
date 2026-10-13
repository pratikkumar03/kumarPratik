"""KrishiSmart backend. Run:  uvicorn app:app --reload   then open http://localhost:8000"""
import json, random, sqlite3, urllib.request, datetime as dt
from pathlib import Path
from fastapi import FastAPI, HTTPException
from fastapi.staticfiles import StaticFiles
from pydantic import BaseModel

ROOT = Path(__file__).resolve().parent.parent
DB = ROOT / "db" / "krishismart.db"
app = FastAPI(title="KrishiSmart")

KC = {"rice": 1.15, "tomato": 1.05}   # crop coefficient (FAO-56 style, mid-stage; replace with your Kc table)
RAW_MM = 40          # readily available soil water (assumption, tune per soil)
BASELINE_MM = 25     # depth a fixed schedule would apply on a day we skip (assumption)
KWH_PER_M3 = 0.35    # pump energy per cubic metre (assumption)

MSG = {
 "green": {"title": {"en": "Do not irrigate today", "hi": "आज सिंचाई न करें", "or": "ଆଜି ପାଣି ଦିଅନ୍ତୁ ନାହିଁ"},
           "text": {"en": "Soil has enough water or rain is coming. Save water and pump energy.",
                    "hi": "मिट्टी में पानी है या बारिश आने वाली है। पानी और बिजली बचाएं।",
                    "or": "ମାଟିରେ ପାଣି ଅଛି ବା ବର୍ଷା ଆସୁଛି। ପାଣି ଓ ବିଜୁଳି ବଞ୍ଚାନ୍ତୁ।"}},
 "yellow": {"title": {"en": "Irrigate lightly tomorrow", "hi": "कल हल्की सिंचाई करें", "or": "କାଲି ହାଲୁକା ପାଣି ଦିଅନ୍ତୁ"},
            "text": {"en": "Soil is getting dry. Water is needed soon.", "hi": "मिट्टी सूख रही है। जल्द पानी चाहिए।",
                     "or": "ମାଟି ଶୁଖୁଛି। ଶୀଘ୍ର ପାଣି ଦରକାର।"}},
 "red": {"title": {"en": "Irrigate today", "hi": "आज सिंचाई करें", "or": "ଆଜି ପାଣି ଦିଅନ୍ତୁ"},
         "text": {"en": "Soil is dry and no rain is coming.", "hi": "मिट्टी सूखी है और बारिश नहीं आ रही।",
                  "or": "ମାଟି ଶୁଖିଲା, ବର୍ଷା ଆସୁନାହିଁ।"}},
}
MKT = {"sell": ({"en": "Sell now", "hi": "अभी बेचें", "or": "ଏବେ ବିକନ୍ତୁ"},
                {"en": "Today's price is above the 30-day average.", "hi": "आज का भाव 30 दिन के औसत से ज़्यादा है।", "or": "ଆଜିର ଦର ୩୦ ଦିନର ହାରାହାରିଠାରୁ ଅଧିକ।"}),
       "store": ({"en": "Store for now", "hi": "अभी रखें", "or": "ଏବେ ରଖନ୍ତୁ"},
                 {"en": "Today's price is below the 30-day average.", "hi": "आज का भाव 30 दिन के औसत से कम है।", "or": "ଆଜିର ଦର ୩୦ ଦିନର ହାରାହାରିଠାରୁ କମ୍।"}),
       "hold": ({"en": "Wait and watch", "hi": "रुकें और देखें", "or": "ଅପେକ୍ଷା କରନ୍ତୁ"},
                {"en": "Price is near the 30-day average.", "hi": "भाव 30 दिन के औसत के पास है।", "or": "ଦର ୩୦ ଦିନର ହାରାହାରି ନିକଟରେ।"})}

def db():
    DB.parent.mkdir(exist_ok=True)
    c = sqlite3.connect(DB); c.row_factory = sqlite3.Row
    c.executescript("""CREATE TABLE IF NOT EXISTS plot(plot_id INTEGER PRIMARY KEY, crop TEXT, district TEXT, lat REAL, lon REAL, sowing_date TEXT, area_ha REAL);
    CREATE TABLE IF NOT EXISTS advisory(plot_id INTEGER, day TEXT, level TEXT, litres REAL, energy REAL, PRIMARY KEY(plot_id, day));""")
    if not c.execute("SELECT 1 FROM plot").fetchone():
        c.execute("INSERT INTO plot VALUES(1,'rice','Khordha',20.18,85.62,?,0.5)",
                  ((dt.date.today() - dt.timedelta(days=42)).isoformat(),)); c.commit()
    return c

def plot(plot_id):
    with db() as c:
        r = c.execute("SELECT * FROM plot WHERE plot_id=?", (plot_id,)).fetchone()
    if not r: raise HTTPException(404, "plot not found")
    return dict(r)

def weather(lat, lon):
    """5 past days + today + 6 forecast days (index 0-4 past, 5 today). Falls back to sample data offline."""
    url = ("https://api.open-meteo.com/v1/forecast?latitude=%s&longitude=%s&timezone=auto&past_days=5&forecast_days=7"
           "&daily=precipitation_sum,precipitation_probability_max,et0_fao_evapotranspiration,temperature_2m_max") % (lat, lon)
    try:
        with urllib.request.urlopen(url, timeout=6) as r:
            d = json.load(r)["daily"]
        d = {k: [x or 0 for x in v] for k, v in d.items()}; d["live"] = True
    except Exception:
        n = 12
        d = {"time": [(dt.date.today() + dt.timedelta(days=i - 5)).isoformat() for i in range(n)],
             "precipitation_sum": [0, 0, 3, 0, 0, 0, 1, 9, 12, 0, 0, 0],
             "precipitation_probability_max": [10] * 5 + [10, 40, 80, 90, 20, 10, 15],
             "et0_fao_evapotranspiration": [4.5] * n, "temperature_2m_max": [33] * n, "live": False}
    return d

@app.get("/api/advice")
def advice(plot_id: int = 1):
    p = plot(plot_id); w = weather(p["lat"], p["lon"])
    etc = sum(w["et0_fao_evapotranspiration"][:5]) * KC.get(p["crop"], 1.0)      # ETc = ET0 x Kc
    dr = max(0.0, etc - 0.8 * sum(w["precipitation_sum"][:5]))                   # root-zone depletion (mm)
    rain_soon = sum(w["precipitation_sum"][5:8])
    level = "green" if (rain_soon >= 8 or dr < 0.6 * RAW_MM) else ("yellow" if dr < RAW_MM else "red")
    litres = round(BASELINE_MM * p["area_ha"] * 10000) if level == "green" else 0
    energy = round(litres / 1000 * KWH_PER_M3, 1)
    m = MSG[level]; day = dt.date.today().isoformat()
    sms = f"KrishiSmart: {p['crop']}, {p['district']}. {m['title']['en']}. Saved {litres} L water."[:160]
    with db() as c:
        c.execute("INSERT OR REPLACE INTO advisory VALUES(?,?,?,?,?)", (plot_id, day, level, litres, energy))
    age = (dt.date.today() - dt.date.fromisoformat(p["sowing_date"])).days
    return {"level": level, "title": m["title"], "text": m["text"], "litres": litres, "energy": energy,
            "sms": sms, "day_of_crop": age, "depletion_mm": round(dr, 1), "live_weather": w["live"]}

@app.get("/api/harvest")
def harvest(plot_id: int = 1):
    p = plot(plot_id); w = weather(p["lat"], p["lon"])
    days = [{"date": w["time"][i], "rain_pct": w["precipitation_probability_max"][i], "mm": w["precipitation_sum"][i]} for i in range(5, 12)]
    good = sorted([d for d in days if d["rain_pct"] < 40 and d["mm"] < 1], key=lambda d: d["rain_pct"])[:3]
    for d in days: d["tag"] = "best" if d in good else ("rain" if d["rain_pct"] >= 60 else "ok")
    return {"days": days}

def prices(crop):  # SAMPLE series. Replace with Agmarknet rows from the market_price table.
    r = random.Random(crop); p = 2200 if crop == "rice" else 1800; out = []
    for _ in range(90): p = max(500, p + r.gauss(0, 25)); out.append(round(p))
    return out

@app.get("/api/market")
def market(crop: str = "rice"):
    s = prices(crop); today, a7, a30 = s[-1], sum(s[-7:]) / 7, sum(s[-30:]) / 30
    k = "sell" if today > a30 * 1.03 else ("store" if today < a30 * 0.97 else "hold")
    return {"prices": s, "today": today, "avg7": round(a7), "avg30": round(a30), "advice": k,
            "title": MKT[k][0], "text": MKT[k][1], "sample": True}

@app.get("/api/impact")
def impact():
    with db() as c:
        r = c.execute("SELECT COALESCE(SUM(litres),0) l, COALESCE(SUM(energy),0) e, COUNT(*) n FROM advisory").fetchone()
    return {"litres": r["l"], "energy": round(r["e"], 1), "advisories": r["n"]}

class PlotIn(BaseModel):
    crop: str = "rice"; district: str; lat: float; lon: float; sowing_date: str; area_ha: float = 0.5

@app.post("/api/plots")
def add_plot(p: PlotIn):
    with db() as c:
        cur = c.execute("INSERT INTO plot(crop,district,lat,lon,sowing_date,area_ha) VALUES(?,?,?,?,?,?)",
                        (p.crop, p.district, p.lat, p.lon, p.sowing_date, p.area_ha))
    return {"plot_id": cur.lastrowid}

app.mount("/", StaticFiles(directory=ROOT / "web", html=True), name="web")
