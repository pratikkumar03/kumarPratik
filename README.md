<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>KrishiSmart – Farm advice for today</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Mukta:wght@400;600;800&family=Noto+Sans+Oriya:wght@400;600;800&display=swap" rel="stylesheet">
<style>
:root{
  --paddy:#1f5a32; --paddy-d:#133a21; --field:#f3f7ee; --card:#fff; --ink:#1c2a1f; --muted:#52615a;
  --line:#d6e0cf; --marigold:#f0a81a; --go:#2e8b4a; --wait:#e6a700; --stop:#c8382d; --soil:#6b4a2f;
  --r:18px; --safe-t:env(safe-area-inset-top,0px); --safe-b:env(safe-area-inset-bottom,0px);
}
@media (prefers-color-scheme:dark){
  :root:not([data-theme="light"]){--field:#101a13;--card:#18261c;--ink:#eef4ea;--muted:#a9b9ae;--line:#2b4031;--paddy:#6cc58a;--paddy-d:#0d1a11}
}
:root[data-theme="dark"]{--field:#101a13;--card:#18261c;--ink:#eef4ea;--muted:#a9b9ae;--line:#2b4031;--paddy:#6cc58a;--paddy-d:#0d1a11}
*{box-sizing:border-box}
html{height:100%;scroll-padding-top:calc(var(--safe-t) + 70px);scroll-behavior:smooth}
body{margin:0;min-height:100%;background:var(--field);color:var(--ink);
  font:400 18px/1.55 "Mukta","Noto Sans Oriya",system-ui,sans-serif;padding-top:var(--safe-t);padding-bottom:calc(76px + var(--safe-b))}
.lang-radio{position:absolute;opacity:0;pointer-events:none}
/* language switch (CSS only) */
.hi,.or{display:none}
#l-hi:checked ~ .page .en,#l-or:checked ~ .page .en{display:none}
#l-hi:checked ~ .page .hi,#l-or:checked ~ .page .or{display:inline}
#l-or:checked ~ .page .hi{display:none}
#l-or:checked ~ .page{font-family:"Noto Sans Oriya","Mukta",sans-serif}
.page .block-l{display:block}
a{color:inherit}
:focus-visible{outline:4px solid var(--marigold);outline-offset:2px}
.wrap{max-width:980px;margin:0 auto;padding:0 16px}

/* top bar */
.top{position:sticky;top:0;z-index:5;background:var(--paddy-d);color:#fff;padding:calc(var(--safe-t) + 8px) 0 8px;margin-top:calc(var(--safe-t) * -1)}
.top .wrap{display:flex;align-items:center;justify-content:space-between;gap:10px}
.brand{display:flex;align-items:center;gap:10px;font-weight:800;font-size:22px;text-decoration:none}
.brand i{width:38px;height:38px;border-radius:50% 50% 50% 8px;background:var(--marigold);display:grid;place-items:center;font-style:normal;font-size:21px}
.langs{display:flex;background:rgba(255,255,255,.12);border-radius:999px;padding:3px}
.langs label{cursor:pointer;padding:6px 14px;border-radius:999px;font-weight:600;font-size:16px;min-height:40px;display:grid;place-items:center}
#l-en:checked ~ .page label[for=l-en],#l-hi:checked ~ .page label[for=l-hi],#l-or:checked ~ .page label[for=l-or]{background:#fff;color:var(--paddy-d)}
#l-en:focus-visible ~ .page label[for=l-en],#l-hi:focus-visible ~ .page label[for=l-hi],#l-or:focus-visible ~ .page label[for=l-or]{outline:4px solid var(--marigold)}

/* hero: the traffic-light advice card */
.hero{padding:22px 0 8px}
.who{display:flex;flex-wrap:wrap;gap:8px;margin:0 0 14px}
.chip{background:var(--card);border:1px solid var(--line);border-radius:999px;padding:4px 14px;font-size:16px;font-weight:600}
.advice{display:grid;grid-template-columns:96px 1fr;gap:18px;background:var(--card);border:3px solid var(--go);border-radius:26px;padding:20px;box-shadow:0 10px 0 -4px var(--go)}
.lamp{width:96px;background:#22262a;border-radius:48px;padding:10px;display:grid;gap:8px;align-self:start}
.lamp b{aspect-ratio:1;border-radius:50%;background:#3b4147}
.advice[data-level=green] .lamp b:nth-child(3){background:var(--go);box-shadow:0 0 18px var(--go)}
.advice h1{margin:0 0 4px;font-size:clamp(28px,6vw,42px);line-height:1.15;font-weight:800;color:var(--paddy)}
.advice p{margin:0 0 14px;font-size:20px}
.save{display:flex;flex-wrap:wrap;gap:10px;margin:0 0 16px;padding:0;list-style:none}
.save li{background:var(--field);border-radius:14px;padding:8px 14px;font-weight:600}
.save strong{font-size:24px;color:var(--paddy)}
.btn{display:inline-flex;align-items:center;gap:10px;min-height:56px;padding:0 22px;border-radius:16px;font-weight:800;font-size:19px;text-decoration:none;background:var(--paddy);color:#fff;border:0}
.btn.alt{background:transparent;color:var(--paddy);border:3px solid var(--paddy)}
.btns{display:flex;flex-wrap:wrap;gap:10px}

/* sections */
section{padding:26px 0 6px}
h2{margin:0 0 4px;font-size:28px;font-weight:800;color:var(--paddy)}
.sub{margin:0 0 14px;color:var(--muted);font-size:17px}
.grid{display:grid;gap:14px;grid-template-columns:repeat(auto-fit,minmax(260px,1fr))}
.card{background:var(--card);border:1px solid var(--line);border-radius:var(--r);padding:18px}
.card h3{margin:0 0 8px;font-size:21px}
.big{font-size:40px;font-weight:800;line-height:1.1;color:var(--paddy)}

/* 7-day harvest strip */
.days{display:grid;grid-template-columns:repeat(7,1fr);gap:6px;margin:10px 0 0;padding:0;list-style:none}
.days li{text-align:center;border-radius:12px;padding:8px 2px;background:var(--field);font-size:15px;font-weight:600;border:2px solid transparent}
.days li span{display:block;font-size:22px}
.days .best{border-color:var(--go);background:color-mix(in srgb,var(--go) 18%,var(--card))}
.days .rain{border-color:var(--stop);background:color-mix(in srgb,var(--stop) 14%,var(--card))}

/* bars and meters */
.bar{height:16px;border-radius:99px;background:var(--field);overflow:hidden;margin:4px 0 12px}
.bar i{display:block;height:100%;background:var(--wait);border-radius:99px}
.row{display:flex;justify-content:space-between;font-weight:600;font-size:17px}
.meter{display:grid;grid-template-columns:repeat(3,1fr);gap:6px;margin-top:8px}
.meter b{height:18px;border-radius:9px;background:var(--line)}
.meter b:nth-child(1){background:var(--go)}
.meter.low b:nth-child(1){box-shadow:0 0 0 3px var(--ink)}
.tag{display:inline-block;border-radius:99px;padding:2px 14px;font-weight:800;color:#fff;background:var(--wait)}
.tag.go{background:var(--go)}
.spark{display:block;width:100%;height:70px;margin-top:6px}
.sms{font-family:ui-monospace,Menlo,monospace;font-size:16px;background:var(--field);border-left:6px solid var(--marigold);border-radius:10px;padding:10px 12px;margin:0}
.tips{margin:0;padding-left:20px}
.note{font-size:15px;color:var(--muted);margin-top:10px}

/* dashboard strip */
.impact{background:var(--paddy-d);color:#fff;border-radius:26px;padding:22px;display:grid;gap:14px;grid-template-columns:repeat(auto-fit,minmax(180px,1fr))}
.impact div{border-left:4px solid var(--marigold);padding-left:12px}
.impact strong{display:block;font-size:34px;line-height:1.1}

/* footer */
footer{padding:30px 0 10px;color:var(--muted);font-size:16px}

/* bottom navigation for thumbs */
.nav{position:fixed;left:0;right:0;bottom:0;z-index:6;background:var(--card);border-top:1px solid var(--line);padding:6px 6px calc(6px + var(--safe-b));display:grid;grid-template-columns:repeat(4,1fr);gap:4px}
.nav a{text-decoration:none;text-align:center;font-weight:600;font-size:15px;padding:6px 2px;border-radius:12px;min-height:54px;display:grid;align-content:center}
.nav a span.i{font-size:22px;line-height:1}
.nav a:hover{background:var(--field)}

@media (max-width:520px){
  body{font-size:17px}
  .advice{grid-template-columns:1fr}
  .lamp{width:auto;display:flex;border-radius:40px;padding:8px;justify-content:center;gap:12px}
  .lamp b{width:44px}
  .langs label{padding:6px 11px}
  .brand span.t{display:none}
}
@media (min-width:900px){ .nav{display:none} body{padding-bottom:30px} }
@media (prefers-reduced-motion:reduce){html{scroll-behavior:auto}}
</style>
</head>
<body>
<!-- Language state: radios are siblings placed before .page so CSS can switch text. JS later can set the checked one from localStorage. -->
<input class="lang-radio" type="radio" name="lang" id="l-en" checked aria-label="English">
<input class="lang-radio" type="radio" name="lang" id="l-hi" aria-label="हिन्दी">
<input class="lang-radio" type="radio" name="lang" id="l-or" aria-label="ଓଡ଼ିଆ">

<div class="page">
<header class="top">
  <div class="wrap">
    <a class="brand" href="#today"><i aria-hidden="true">🌾</i><span class="t">KrishiSmart</span></a>
    <div class="langs" role="group" aria-label="Language">
      <label for="l-or">ଓଡ଼ିଆ</label><label for="l-hi">हिन्दी</label><label for="l-en">English</label>
    </div>
  </div>
</header>

<main class="wrap">

<!-- PHASE 2 · irrigate-or-skip card. BACKEND: fill from GET /api/advice?plot_id= (level, message, litres, energy, sms) -->
<div class="hero" id="today">
  <p class="who">
    <span class="chip">🌾 <span class="en">Rice</span><span class="hi">धान</span><span class="or">ଧାନ</span></span>
    <span class="chip">📍 <span class="en">Khordha</span><span class="hi">खोरधा</span><span class="or">ଖୋର୍ଦ୍ଧା</span></span>
    <span class="chip">🌱 <span class="en">Day 42 · tillering</span><span class="hi">42वां दिन · कल्ले फूटना</span><span class="or">୪୨ତମ ଦିନ · ଗଣ୍ଠି ବାହାରିବା</span></span>
  </p>
  <article class="advice" id="advice-card" data-level="green" aria-live="polite">
    <div class="lamp" aria-hidden="true"><b></b><b></b><b></b></div>
    <div>
      <h1><span class="en">Do not irrigate today</span><span class="hi">आज सिंचाई न करें</span><span class="or">ଆଜି ପାଣି ଦିଅନ୍ତୁ ନାହିଁ</span></h1>
      <p><span class="en">Rain is likely tomorrow. Save water and pump energy.</span><span class="hi">कल बारिश की संभावना है। पानी और पंप की बिजली बचाएं।</span><span class="or">କାଲି ବର୍ଷା ହେବାର ସମ୍ଭାବନା ଅଛି। ପାଣି ଓ ପମ୍ପ ବିଜୁଳି ବଞ୍ଚାନ୍ତୁ।</span></p>
      <ul class="save">
        <li>💧 <strong>1,800</strong> <span class="en">litres saved</span><span class="hi">लीटर बचा</span><span class="or">ଲିଟର ବଞ୍ଚିଲା</span></li>
        <li>⚡ <strong>2.1</strong> <span class="en">units of power</span><span class="hi">यूनिट बिजली</span><span class="or">ୟୁନିଟ୍ ବିଜୁଳି</span></li>
      </ul>
      <div class="btns">
        <!-- F3 voice clip: AIML/backend later swap for <audio controls src="audio/advice-or.mp3"> -->
        <a class="btn" href="#voice" id="play-advice">🔊 <span class="en">Listen</span><span class="hi">सुनें</span><span class="or">ଶୁଣନ୍ତୁ</span></a>
        <a class="btn alt" href="#sms"><span class="en">See SMS</span><span class="hi">SMS देखें</span><span class="or">SMS ଦେଖନ୍ତୁ</span></a>
      </div>
    </div>
  </article>
  <p class="note"><span class="en">Sample data for the design. Real advice will come from the server.</span><span class="hi">यह डिज़ाइन का नमूना डेटा है। असली सलाह सर्वर से आएगी।</span><span class="or">ଏହା ଡିଜାଇନ୍‌ର ନମୁନା ତଥ୍ୟ। ପ୍ରକୃତ ପରାମର୍ଶ ସର୍ଭରରୁ ଆସିବ।</span></p>
</div>

<!-- PHASE 3 · harvest and market. BACKEND: GET /api/harvest, GET /api/market -->
<section id="harvest">
  <h2><span class="en">Harvest days</span><span class="hi">कटाई के दिन</span><span class="or">ଅମଳ ଦିନ</span></h2>
  <p class="sub"><span class="en">Green days are best. Red days may have rain.</span><span class="hi">हरे दिन सबसे अच्छे हैं। लाल दिन पर बारिश हो सकती है।</span><span class="or">ସବୁଜ ଦିନ ଭଲ। ନାଲି ଦିନରେ ବର୍ଷା ହୋଇପାରେ।</span></p>
  <div class="grid">
    <div class="card" id="harvest-card">
      <h3><span class="en">Next 7 days</span><span class="hi">अगले 7 दिन</span><span class="or">ଆଗାମୀ ୭ ଦିନ</span></h3>
      <ol class="days">
        <li class="rain"><span>🌧</span>Mon</li><li class="rain"><span>🌧</span>Tue</li><li><span>⛅</span>Wed</li>
        <li class="best"><span>☀️</span>Thu</li><li class="best"><span>☀️</span>Fri</li><li><span>⛅</span>Sat</li><li class="best"><span>☀️</span>Sun</li>
      </ol>
    </div>
    <div class="card" id="market-card">
      <h3><span class="en">Sell or store?</span><span class="hi">बेचें या रखें?</span><span class="or">ବିକିବେ ନା ରଖିବେ?</span></h3>
      <p class="big"><span class="en">Store for now</span><span class="hi">अभी रखें</span><span class="or">ଏବେ ରଖନ୍ତୁ</span></p>
      <p style="margin:6px 0 0"><span class="en">Today's price is below the 30-day average.</span><span class="hi">आज का भाव 30 दिन के औसत से कम है।</span><span class="or">ଆଜିର ଦର ୩୦ ଦିନର ହାରାହାରିଠାରୁ କମ୍।</span></p>
      <svg class="spark" viewBox="0 0 200 70" role="img" aria-label="Price trend"><polyline fill="none" stroke="var(--paddy)" stroke-width="4" stroke-linecap="round" stroke-linejoin="round" points="4,40 30,34 55,38 80,26 105,30 130,20 155,28 180,44 196,50"/></svg>
      <p class="note">Mandi price · 90 days</p>
    </div>
    <div class="card" id="spoilage-card">
      <h3><span class="en">Spoilage risk</span><span class="hi">खराब होने का खतरा</span><span class="or">ନଷ୍ଟ ହେବାର ଆଶଙ୍କା</span></h3>
      <span class="tag go"><span class="en">Low</span><span class="hi">कम</span><span class="or">କମ୍</span></span>
      <div class="meter low" aria-hidden="true"><b></b><b style="background:var(--line)"></b><b style="background:var(--line)"></b></div>
      <p class="note"><span class="en">Cool, dry store. 6 days in storage.</span><span class="hi">ठंडा, सूखा भंडार। 6 दिन से रखा है।</span><span class="or">ଥଣ୍ଡା, ଶୁଖିଲା ଗୋଦାମ। ୬ ଦିନ ହେଲା ରଖାଯାଇଛି।</span></p>
    </div>
  </div>
</section>

<!-- PHASE 2/3 · climate. BACKEND/R: GET /api/climate?district= -->
<section id="weather">
  <h2><span class="en">Weather risks</span><span class="hi">मौसम का जोखिम</span><span class="or">ପାଣିପାଗ ଆଶଙ୍କା</span></h2>
  <p class="sub"><span class="en">Chances based on 30 years of rain history.</span><span class="hi">30 साल के बारिश के रिकॉर्ड पर आधारित संभावना।</span><span class="or">୩୦ ବର୍ଷର ବର୍ଷା ତଥ୍ୟ ଆଧାରିତ ସମ୍ଭାବନା।</span></p>
  <div class="grid">
    <div class="card" id="dryspell-card">
      <h3>🌵 <span class="en">Dry spell over 7 days</span><span class="hi">7 दिन से लंबा सूखा</span><span class="or">୭ ଦିନରୁ ଅଧିକ ଖରା</span></h3>
      <div class="row"><span>Aug</span><span>18%</span></div><div class="bar"><i style="width:18%;background:var(--go)"></i></div>
      <div class="row"><span>Sep</span><span>34%</span></div><div class="bar"><i style="width:34%"></i></div>
      <div class="row"><span>Oct</span><span>52%</span></div><div class="bar"><i style="width:52%;background:var(--stop)"></i></div>
    </div>
    <div class="card" id="heat-card">
      <h3>🔥 <span class="en">Heat at flowering</span><span class="hi">फूल आने पर गर्मी</span><span class="or">ଫୁଲ ଫୁଟିବା ବେଳେ ଗରମ</span></h3>
      <p class="big">22%</p>
      <p style="margin:6px 0 0"><span class="en">Chance of days above 35 °C.</span><span class="hi">35 °C से ऊपर के दिनों की संभावना।</span><span class="or">୩୫ °C ଉପରେ ଦିନ ହେବାର ସମ୍ଭାବନା।</span></p>
    </div>
    <div class="card" id="soil-card">
      <h3>🪱 <span class="en">Soil tips</span><span class="hi">मिट्टी की सलाह</span><span class="or">ମାଟି ପରାମର୍ଶ</span></h3>
      <ul class="tips">
        <li><span class="en">Add compost before the next crop.</span><span class="hi">अगली फसल से पहले खाद डालें।</span><span class="or">ପରବର୍ତ୍ତୀ ଫସଲ ପୂର୍ବରୁ କମ୍ପୋଷ୍ଟ ଦିଅନ୍ତୁ।</span></li>
        <li><span class="en">Mulch to keep moisture.</span><span class="hi">नमी बचाने के लिए मल्च बिछाएं।</span><span class="or">ଆର୍ଦ୍ରତା ରଖିବାକୁ ମଲଚ୍ ଦିଅନ୍ତୁ।</span></li>
      </ul>
    </div>
  </div>
</section>

<!-- Phone message preview (F5). BACKEND: sms field from /api/advice -->
<section id="sms">
  <h2>✉️ <span class="en">Message on your phone</span><span class="hi">आपके फ़ोन का संदेश</span><span class="or">ଆପଣଙ୍କ ଫୋନ୍ ମେସେଜ୍</span></h2>
  <p class="sub"><span class="en">Works even without internet. 160 letters or less.</span><span class="hi">बिना इंटरनेट भी चलता है। 160 अक्षर या कम।</span><span class="or">ଇଣ୍ଟରନେଟ୍ ବିନା ମଧ୍ୟ ଚାଲେ। ୧୬୦ ଅକ୍ଷର ବା କମ୍।</span></p>
  <p class="sms" id="sms-preview">KrishiSmart: Rice, Khordha. Do NOT irrigate today. Rain likely tomorrow. Saved 1800 L water.</p>
  <p class="note" id="voice"><span class="en">Voice clips for each message will be added here.</span><span class="hi">हर संदेश की आवाज़ यहाँ जोड़ी जाएगी।</span><span class="or">ପ୍ରତ୍ୟେକ ମେସେଜ୍‌ର ଅଡିଓ ଏଠାରେ ଯୋଡ଼ାଯିବ।</span></p>
</section>

<!-- PHASE 3 · impact dashboard (P1). BACKEND: GET /api/impact -->
<section id="impact">
  <h2><span class="en">What we saved together</span><span class="hi">हमने मिलकर क्या बचाया</span><span class="or">ଆମେ ମିଶି କ'ଣ ବଞ୍ଚାଇଲୁ</span></h2>
  <div class="impact" id="impact-totals">
    <div><strong>1,24,000</strong><span class="en">litres of water</span><span class="hi">लीटर पानी</span><span class="or">ଲିଟର ପାଣି</span></div>
    <div><strong>146</strong><span class="en">units of power</span><span class="hi">यूनिट बिजली</span><span class="or">ୟୁନିଟ୍ ବିଜୁଳି</span></div>
    <div><strong>312</strong><span class="en">advice given</span><span class="hi">सलाह दी गई</span><span class="or">ପରାମର୍ଶ ଦିଆଗଲା</span></div>
  </div>
  <p class="note"><span class="en">Example numbers. They will be replaced by our simulation results.</span><span class="hi">उदाहरण के आंकड़े। इन्हें हमारे सिमुलेशन के नतीजों से बदला जाएगा।</span><span class="or">ଉଦାହରଣ ସଂଖ୍ୟା। ଏଗୁଡ଼ିକୁ ଆମ ସିମୁଲେସନ୍ ଫଳାଫଳରେ ବଦଳାଯିବ।</span></p>
</section>
</main>

<footer class="wrap">
  <p><strong>KrishiSmart</strong> · Team 25DDS012 · Ravenshaw University<br>
  <span class="en">Advice uses Open-Meteo, NASA POWER and Agmarknet data. Please check with your local KVK before big decisions.</span><span class="hi">सलाह Open-Meteo, NASA POWER और Agmarknet के डेटा पर आधारित है। बड़े फैसलों से पहले अपने KVK से पूछें।</span><span class="or">ପରାମର୍ଶ Open-Meteo, NASA POWER ଓ Agmarknet ତଥ୍ୟ ଆଧାରିତ। ବଡ଼ ନିଷ୍ପତ୍ତି ପୂର୍ବରୁ ଆପଣଙ୍କ KVK ସହ ପରାମର୍ଶ କରନ୍ତୁ।</span></p>
</footer>

<nav class="nav" aria-label="Main">
  <a href="#today"><span class="i">💧</span><span class="en">Water</span><span class="hi">पानी</span><span class="or">ପାଣି</span></a>
  <a href="#harvest"><span class="i">🌾</span><span class="en">Harvest</span><span class="hi">कटाई</span><span class="or">ଅମଳ</span></a>
  <a href="#harvest"><span class="i">💰</span><span class="en">Market</span><span class="hi">बाज़ार</span><span class="or">ବଜାର</span></a>
  <a href="#weather"><span class="i">⛅</span><span class="en">Weather</span><span class="hi">मौसम</span><span class="or">ପାଣିପାଗ</span></a>
</nav>
</div>
</body>
</html>
