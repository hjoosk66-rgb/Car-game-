<!DOCTYPE html>  
<html lang="ar" dir="rtl">  
<head>  
<meta charset="UTF-8">  
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">  
<title>سباق السرعة — لعبة سباق سيارات</title>  
<style>  
  * { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color:transparent; }  
  html,body { height:100%; }  
  body {  
    font-family:"Segoe UI", Tahoma, Arial, sans-serif;  
    background: radial-gradient(1200px 800px at 50% -10%, #1b2340 0%, #0a0d18 55%, #05060c 100%);  
    color:#fff; min-height:100vh; display:flex; align-items:center; justify-content:center;  
    padding:16px; overflow-x:hidden;  
  }  
  .screen { display:none; width:100%; max-width:860px; flex-direction:column; align-items:center; gap:16px; animation:fadeIn .35s ease; }  
  .screen.active { display:flex; }  
  @keyframes fadeIn { from{opacity:0; transform:translateY(10px);} to{opacity:1; transform:none;} }  
  
  h1 { font-size:44px; font-weight:800; letter-spacing:1px;  
       background:linear-gradient(90deg,#00e5ff,#ff2d78);  
       -webkit-background-clip:text; background-clip:text; color:transparent;  
       text-shadow:0 0 40px rgba(0,229,255,.25); }  
  .subtitle { color:#9aa4c0; font-size:16px; }  
  
  .card { background:rgba(255,255,255,.05); border:1px solid rgba(255,255,255,.1);  
          border-radius:16px; padding:20px 24px; width:100%; max-width:480px;  
          display:flex; flex-direction:column; gap:12px; backdrop-filter:blur(6px); }  
  
  .btn { border:none; border-radius:12px; padding:14px 18px; font-size:17px; font-weight:700;  
         cursor:pointer; transition:transform .12s ease, box-shadow .12s ease; font-family:inherit; }  
  .btn:active { transform:scale(.97); }  
  .btn-primary { background:linear-gradient(90deg,#00e5ff,#00b0d8); color:#04222b;  
                 box-shadow:0 0 24px rgba(0,229,255,.35); }  
  .btn-primary:hover { box-shadow:0 0 34px rgba(0,229,255,.55); }  
  .btn-secondary { background:linear-gradient(90deg,#ff2d78,#d81b60); color:#fff;  
                   box-shadow:0 0 24px rgba(255,45,120,.3); }  
  .btn-ghost { background:transparent; border:1px solid rgba(255,255,255,.25); color:#cdd5ea; font-weight:600; }  
  .btn-ghost:hover { background:rgba(255,255,255,.08); }  
  
  .swatches { display:flex; gap:12px; justify-content:center; }  
  .swatch { width:44px; height:44px; border-radius:50%; cursor:pointer; border:3px solid transparent; transition:.15s; }  
  .swatch.selected { border-color:#fff; transform:scale(1.12); box-shadow:0 0 18px rgba(255,255,255,.4); }  
  
  .note { font-size:12px; color:#8b94ad; text-align:center; line-height:1.6; }  
  .best { font-size:14px; color:#ffd54f; font-weight:700; text-align:center; }  
  
  .hud { display:flex; gap:20px; align-items:center; justify-content:space-between; width:100%; max-width:640px; flex-wrap:wrap; }  
  .stat { text-align:center; }  
  .stat .label { font-size:12px; color:#9aa4c0; }  
  .stat .value { font-size:26px; font-weight:800; font-variant-numeric:tabular-nums; color:#00e5ff; }  
  
  .game-area { display:flex; gap:16px; align-items:flex-start; flex-wrap:wrap; justify-content:center; }  
  canvas { background:#111; border-radius:14px; border:1px solid rgba(255,255,255,.12);  
           box-shadow:0 0 40px rgba(0,229,255,.12); max-width:100%; touch-action:none; }  
  
  .lb { width:180px; background:rgba(255,255,255,.05); border:1px solid rgba(255,255,255,.1);  
        border-radius:14px; padding:14px; }  
  .lb h3 { font-size:14px; margin-bottom:10px; color:#cdd5ea; }  
  .lb-row { display:flex; align-items:center; gap:8px; padding:5px 6px; border-radius:8px; font-size:13px; }  
  .lb-row.me { background:rgba(0,229,255,.12); font-weight:700; }  
  .lb-dot { width:9px; height:9px; border-radius:50%; flex:none; }  
  .lb-row .nm { flex:1; }  
  .lb-row .ds { color:#9aa4c0; font-variant-numeric:tabular-nums; }  
  
  .controls-row { display:flex; justify-content:space-between; align-items:center; width:100%; max-width:400px; }  
  .touch-btn { width:56px; height:56px; border-radius:50%; border:1px solid rgba(255,255,255,.25);  
               background:rgba(255,255,255,.08); color:#fff; font-size:22px; user-select:none; touch-action:none; }  
  .hint { font-size:13px; color:#8b94ad; }  
  
  .big-score { font-size:52px; font-weight:800; color:#00e5ff; font-variant-numeric:tabular-nums; }  
  .end-title { font-size:30px; font-weight:800; }  
  .end-title.win { color:#4ade80; } .end-title.lose { color:#ff2d78; }  
  .end-msg { color:#9aa4c0; font-size:15px; text-align:center; }  
  .row { display:flex; gap:10px; }  
  
  /* ============ أنماط الأصدقاء والمحادثة ============ */  
  .friend-input-group { display:flex; gap:8px; }  
  .input-field { flex:1; padding:10px 14px; border-radius:10px; border:1px solid rgba(255,255,255,.2); background:rgba(0,0,0,.3); color:#fff; outline:none; font-family:inherit; }  
  .input-field:focus { border-color:#00e5ff; }  
  .friends-list { display:flex; flex-direction:column; gap:8px; max-height:180px; overflow-y:auto; margin-top:6px; }  
  .friend-item { display:flex; align-items:center; justify-content:space-between; background:rgba(255,255,255,.05); padding:8px 12px; border-radius:10px; font-size:14px; }  
  .friend-status { width:8px; height:8px; border-radius:50%; background:#4ade80; display:inline-block; margin-left:6px; }  
  .friend-actions { display:flex; gap:6px; }  
  .btn-sm { padding:6px 10px; font-size:12px; border-radius:8px; }  
  
  /* الشات */  
  .chat-box { background:rgba(0,0,0,.4); border:1px solid rgba(255,255,255,.1); border-radius:12px; height:180px; display:flex; flex-direction:column; padding:10px; margin-top:10px; }  
  .chat-messages { flex:1; overflow-y:auto; display:flex; flex-direction:column; gap:6px; font-size:13px; text-align:right; }  
  .chat-msg { background:rgba(255,255,255,.08); padding:6px 10px; border-radius:8px; width:fit-content; max-width:80%; }  
  .chat-msg.me { background:rgba(0,229,255,.2); align-self:flex-end; }  
  .chat-msg .sender { font-size:10px; color:#9aa4c0; display:block; margin-bottom:2px; }  
  
  @media (max-width:640px){ h1{font-size:34px;} .lb{width:100%;} }  
</style>  
</head>  
<body>  
  
<!-- ============ القائمة الرئيسية ============ -->  
<div id="menu" class="screen active">  
  <h1>🏁 سباق السرعة</h1>  
  <p class="subtitle">اختر سيارتك ونمط اللعب وانطلق!</p>  
  
  <div class="card">  
    <div class="swatches" id="swatches"></div>  
    <button class="btn btn-primary" onclick="startGame('online')">🌐 العب أونلاين — سباق ضد ٤ منافسين</button>  
    <button class="btn btn-secondary" onclick="startGame('solo')">🎮 العب منفرد — سباق لا نهائي</button>  
    <button class="btn btn-ghost" onclick="show('friendsScreen')">👥 الأصدقاء والتحديات والمحادثة</button>  
    <div class="best" id="bestLine">🏆 أفضل مسافة: 0 م</div>  
  </div>  
</div>  
  
<!-- ============ شاشة الأصدقاء والمحادثة ============ -->  
<div id="friendsScreen" class="screen">  
  <h2>👥 قائمة الأصدقاء والتحدي</h2>  
  <div class="card">  
    <div class="friend-input-group">  
      <input type="text" id="friendNameInput" class="input-field" placeholder="اكتب اسم الصديق...">  
      <button class="btn btn-primary btn-sm" onclick="addFriend()">إضافة صديق</button>  
    </div>  
  
    <div class="friends-list" id="friendsList"></div>  
  
    <!-- نافذة المحادثة -->  
    <div class="chat-box" id="chatBox">  
      <div class="chat-messages" id="chatMessages">  
        <div class="chat-msg"><span class="sender">النظام</span>مرحباً بك! اختر صديقاً لبدء المحادثة أو التحدي.</div>  
      </div>  
      <div class="friend-input-group" style="margin-top:8px;">  
        <input type="text" id="chatInput" class="input-field" placeholder="اكتب رسالة..." onkeypress="if(event.key==='Enter') sendChatMessage()">  
        <button class="btn btn-primary btn-sm" onclick="sendChatMessage()">إرسال</button>  
      </div>  
    </div>  
  
    <button class="btn btn-ghost" onclick="show('menu')">↩ العودة للقائمة</button>  
  </div>  
</div>  
  
<!-- ============ شاشة اللعب ============ -->  
<div id="game" class="screen">  
  <div class="hud">  
    <div class="stat"><div class="label">المسافة</div><div class="value"><span id="dist">0</span> م</div></div>  
    <div class="stat"><div class="label">السرعة</div><div class="value"><span id="spd">0</span> كم/س</div></div>  
    <div class="row">  
      <button class="btn btn-ghost" style="padding:8px 14px;font-size:13px" onclick="togglePause()">⏸ إيقاف</button>  
      <button class="btn btn-ghost" style="padding:8px 14px;font-size:13px" onclick="quitToMenu()">خروج</button>  
    </div>  
  </div>  
  
  <div class="game-area">  
    <div>  
      <canvas id="cv" width="400" height="640"></canvas>  
      <div class="controls-row" style="margin-top:10px">  
        <span class="hint" id="modeHint"></span>  
        <div class="row">  
          <button class="touch-btn" id="btnL">▶</button>  
          <button class="touch-btn" id="btnR">◀</button>  
        </div>  
      </div>  
    </div>  
    <div class="lb" id="lbWrap" style="display:none">  
      <h3>📊 الترتيب — حتى ٥٠٠٠ م</h3>  
      <div id="lb"></div>  
    </div>  
  </div>  
</div>  
  
<!-- ============ شاشة النهاية ============ -->  
<div id="end" class="screen">  
  <div class="end-title" id="endTitle"></div>  
  <div class="end-msg" id="endMsg"></div>  
  <div class="big-score" id="endScore"></div>  
  <div class="row">  
    <button class="btn btn-primary" onclick="startGame(lastMode)">🔁 العب مجددًا</button>  
    <button class="btn btn-ghost" onclick="quitToMenu()">القائمة الرئيسية</button>  
  </div>  
</div>  
  
<script>  
"use strict";  
/* ================= الإعدادات ================= */  
const $ = id => document.getElementById(id);  
const cv = $("cv"), ctx = cv.getContext("2d");  
const W = cv.width, H = cv.height;  
const ROAD_X = 50, ROAD_W = 300, LANE = ROAD_W / 4, TARGET = 5000;  
  
const CAR_COLORS = ["#00e5ff", "#ff2d78", "#ffd54f", "#4ade80"];  
const RIVALS = [  
  { n: "صقر الصحراء", c: "#ff2d78" },  
  { n: "برق الليل",   c: "#4ade80" },  
  { n: "ذئب الطريق",  c: "#ffd54f" },  
  { n: "نسر الجنوب",  c: "#b18cff" },  
];  
  
let mode = "solo", lastMode = "solo", running = false, paused = false;  
let dist = 0, pspeed = 5, enemies = [], spawnT = 0, lastT = 0, lbT = 0, tG = 0;  
let ai = [], playerColor = CAR_COLORS[0];  
let currentChallengedFriend = null;  
const keys = { l: false, r: false };  
const player = { x: W/2 - 24, y: H - 120, w: 48, h: 80 };  
  
/* ================= بيانات الأصدقاء والتحديات ================= */  
let friends = JSON.parse(localStorage.getItem("race_friends") || '["أحمد", "سارة", "فيصل"]');  
let activeChatFriend = friends[0] || null;  
  
function saveFriends() {  
  localStorage.setItem("race_friends", JSON.stringify(friends));  
}  
  
function renderFriends() {  
  const list = $("friendsList");  
  list.innerHTML = "";  
  if (friends.length === 0) {  
    list.innerHTML = `<div style="text-align:center; color:#8b94ad; font-size:13px; padding:10px;">لا يوجد أصدقاء بعد. أضف صديقاً!</div>`;  
    return;  
  }  
  friends.forEach(f => {  
    const item = document.createElement("div");  
    item.className = "friend-item";  
    item.innerHTML = `  
      <div>  
        <span class="friend-status"></span>  
        <strong>${f}</strong>  
      </div>  
      <div class="friend-actions">  
        <button class="btn btn-secondary btn-sm" onclick="challengeFriend('${f}')">⚔️ تحدَّ</button>  
        <button class="btn btn-ghost btn-sm" onclick="selectChatFriend('${f}')">💬 شات</button>  
      </div>  
    `;  
    list.appendChild(item);  
  });  
}  
  
function addFriend() {  
  const inp = $("friendNameInput");  
  const name = inp.value.trim();  
  if (name && !friends.includes(name)) {  
    friends.push(name);  
    saveFriends();  
    renderFriends();  
    inp.value = "";  
    addChatMessage("النظام", `تمت إضافة ${name} إلى قائمة أصدقائك.`);  
  }  
}  
  
function challengeFriend(name) {  
  currentChallengedFriend = name;  
  alert(`🏁 تم إرسال التحدي لـ ${name}! جاري بدء السباق...`);  
  startGame('challenge');  
}  
  
function selectChatFriend(name) {  
  activeChatFriend = name;  
  addChatMessage("النظام", `بدأت المحادثة مع ${name}.`);  
}  
  
function addChatMessage(sender, text, isMe = false) {  
  const box = $("chatMessages");  
  const msg = document.createElement("div");  
  msg.className = "chat-msg" + (isMe ? " me" : "");  
  msg.innerHTML = `<span class="sender">${sender}</span>${text}`;  
  box.appendChild(msg);  
  box.scrollTop = box.scrollHeight;  
}  
  
function sendChatMessage() {  
  const inp = $("chatInput");  
  const text = inp.value.trim();  
  if (!text) return;  
  addChatMessage("أنت", text, true);  
  inp.value = "";  
  
  // رد آلي لمحاكاة الشات مع الصديق  
  setTimeout(() => {  
    const replies = [  
      "جاهز للتحدي في أي وقت! 🔥",  
      "كفو! تعال نلعب سباق أونلاين.",  
      "رقمي القياسي صعب تكسره! 😎",  
      "أهلا بك، بالتوفيق في السباق!"  
    ];  
    const randomReply = replies[Math.floor(Math.random() * replies.length)];  
    addChatMessage(activeChatFriend || "الصديق", randomReply);  
  }, 1000);  
}  
  
/* ================= الصوت ================= */  
let AC = null;  
function beep(freq, dur, type) {  
  try {  
    AC = AC || new (window.AudioContext || window.webkitAudioContext)();  
    const o = AC.createOscillator(), g = AC.createGain();  
    o.type = type || "square"; o.frequency.value = freq;  
    g.gain.setValueAtTime(0.12, AC.currentTime);  
    g.gain.exponentialRampToValueAtTime(0.001, AC.currentTime + dur);  
    o.connect(g); g.connect(AC.destination);  
    o.start(); o.stop(AC.currentTime + dur);  
  } catch (e) {}  
}  
  
/* ================= القائمة ================= */  
function buildSwatches() {  
  const box = $("swatches");  
  box.innerHTML = "";  
  CAR_COLORS.forEach((c) => {  
    const s = document.createElement("div");  
    s.className = "swatch" + (c === playerColor ? " selected" : "");  
    s.style.background = c;  
    s.onclick = () => { playerColor = c; buildSwatches(); };  
    box.appendChild(s);  
  });  
}  
function refreshBest() {  
  const b = +(localStorage.getItem("race_best") || 0);  
  $("bestLine").textContent = "🏆 أفضل مسافة: " + b + " م";  
}  
function show(id) {  
  document.querySelectorAll(".screen").forEach(s => s.classList.remove("active"));  
  $(id).classList.add("active");  
  if (id === 'friendsScreen') renderFriends();  
}  
  
/* ================= منطق اللعبة ================= */  
function startGame(m) {  
  lastMode = m; mode = m;  
  dist = 0; pspeed = 5; enemies = []; spawnT = 0; lbT = 0; paused = false;  
  player.x = W/2 - player.w/2;  
  $("lbWrap").style.display = (m === "online" || m === "challenge") ? "block" : "none";  
    
  if (m === "challenge") {  
    $("modeHint").textContent = `تحدي خاص ضد ${currentChallengedFriend} ⚔️`;  
    ai = [  
      { n: currentChallengedFriend, c: "#ff2d78", d: 0, v: 85 + Math.random() * 20 },  
      { n: "صقر الصحراء", c: "#ffd54f", d: 0, v: 75 }  
    ];  
    renderLB();  
  } else if (m === "online") {  
    $("modeHint").textContent = "سباق حتى خط النهاية 🏁";  
    ai = RIVALS.map(r => ({ n: r.n, c: r.c, d: 0, v: 74 + Math.random() * 40 }));  
    renderLB();  
  } else {  
    $("modeHint").textContent = "تجنب السيارات وامشِ أبعد ما تقدر!";  
  }  
  
  show("game");  
  running = true; lastT = performance.now();  
  requestAnimationFrame(loop);  
}  
  
function quitToMenu() { running = false; refreshBest(); show("menu"); }  
  
function togglePause() {  
  if (!running) return;  
  paused = !paused;  
  if (!paused) { lastT = performance.now(); requestAnimationFrame(loop); }  
  else { drawPause(); }  
}  
  
function spawnEnemy() {  
  for (let t = 0; t < 5; t++) {  
    const l = Math.floor(Math.random() * 4);  
    const x = ROAD_X + LANE * l + (LANE - 48) / 2;  
    if (!enemies.some(e => Math.abs(e.x - x) < 12 && e.y < 190)) {  
      enemies.push({ x, y: -95, w: 48, h: 80,  
        vy: pspeed * 0.45 + 1.2 + Math.random() * 1.8,  
        col: RIVALS[Math.floor(Math.random() * 4)].c });  
      return;  
    }  
  }  
}  
  
function hit(a, b) {  
  const i = 8;  
  return a.x + i < b.x + b.w - i && a.x + a.w - i > b.x + i &&  
         a.y + i < b.y + b.h - i && a.y + a.h - i > b.y + i;  
}  
  
/* ================= الرسم ================= */  
function rr(x, y, w, h, r) {  
  ctx.beginPath();  
  ctx.moveTo(x + r, y);  
  ctx.arcTo(x + w, y, x + w, y + h, r);  
  ctx.arcTo(x + w, y + h, x, y + h, r);  
  ctx.arcTo(x, y + h, x, y, r);  
  ctx.arcTo(x, y, x + w, y, r);  
  ctx.closePath();  
}  
function drawCar(x, y, w, h, col, me) {  
  ctx.fillStyle = "#000";  
  ctx.fillRect(x - 4, y + 9, 7, 18); ctx.fillRect(x + w - 3, y + 9, 7, 18);  
  ctx.fillRect(x - 4, y + h - 28, 7, 18); ctx.fillRect(x + w - 3, y + h - 28, 7, 18);  
  ctx.fillStyle = col; rr(x, y, w, h, 9); ctx.fill();  
  ctx.strokeStyle = "rgba(255,255,255,.35)"; ctx.lineWidth = 1.5; ctx.stroke();  
  ctx.fillStyle = "rgba(10,15,30,.85)";  
  rr(x + 8, y + 14, w - 16, 17, 4); ctx.fill();  
  rr(x + 8, y + h - 33, w - 16, 13, 4); ctx.fill();  
  if (me) {  
    ctx.fillStyle = "#fff";  
    ctx.fillRect(x + 7, y + h - 3, 10, 3); ctx.fillRect(x + w - 17, y + h - 3, 10, 3);  
  }  
}  
function draw() {  
  ctx.fillStyle = "#0d1020"; ctx.fillRect(0, 0, W, H);  
  ctx.fillStyle = "#232842"; ctx.fillRect(ROAD_X, 0, ROAD_W, H);  
  ctx.fillStyle = "#e8ecff";  
  ctx.fillRect(ROAD_X - 5, 0, 5, H); ctx.fillRect(ROAD_X + ROAD_W, 0, 5, H);  
  ctx.fillStyle = "#8f9ac0";  
  const off = (dist * 2.2) % 58;  
  for (let i = 1; i < 4; i++) {  
    const x = ROAD_X + LANE * i;  
    for (let y = -58 + off; y < H; y += 58) ctx.fillRect(x - 2, y, 4, 32);  
  }  
  if (mode === "online" || mode === "challenge") {  
    ctx.fillStyle = "rgba(255,255,255,.15)"; ctx.fillRect(56, 12, 288, 7);  
    ctx.fillStyle = playerColor; ctx.fillRect(56, 12, Math.min(288, dist / TARGET * 288), 7);  
  }  
  enemies.forEach(e => drawCar(e.x, e.y, e.w, e.h, e.col, false));  
  drawCar(player.x, player.y, player.w, player.h, playerColor, true);  
}  
function drawPause() {  
  ctx.fillStyle = "rgba(0,0,0,.6)"; ctx.fillRect(0, 0, W, H);  
  ctx.fillStyle = "#fff"; ctx.font = "bold 34px Tahoma"; ctx.textAlign = "center";  
  ctx.fillText("⏸ إيقاف مؤقت", W/2, H/2);  
  ctx.font = "16px Tahoma"; ctx.fillStyle = "#9aa4c0";  
  ctx.fillText("اضغط P للمتابعة", W/2, H/2 + 34);  
}  
  
/* ================= لوحة الترتيب ================= */  
function renderLB() {  
  const rows = [{ n: "أنت", d: dist, c: playerColor, me: true }]  
    .concat(ai.map(a => ({ n: a.n, d: a.d, c: a.c })));  
  rows.sort((a, b) => b.d - a.d);  
  $("lb").innerHTML = rows.map((r, i) =>  
    `<div class="lb-row${r.me ? " me" : ""}">  
       <span style="width:16px;color:#8b94ad">${i + 1}</span>  
       <span class="lb-dot" style="background:${r.c}"></span>  
       <span class="nm">${r.n}</span>  
       <span class="ds">${Math.floor(r.d)}م</span>  
     </div>`).join("");  
}  
  
/* ================= الحلقة الرئيسية ================= */  
function loop(t) {  
  if (!running || paused) return;  
  const dt = Math.min(40, t - lastT); lastT = t; tG += dt;  
  
  pspeed = Math.min(13.5, pspeed + dt * 0.0013);  
  if (keys.l) player.x -= 6.5 * (dt / 16.7);  
  if (keys.r) player.x += 6.5 * (dt / 16.7);  
  player.x = Math.max(ROAD_X + 8, Math.min(ROAD_X + ROAD_W - 8 - player.w, player.x));  
  
  dist += pspeed * (dt / 16.7) * 0.22;  
  
  spawnT += dt;  
  if (spawnT > Math.max(380, 1080 - dist * 0.05)) { spawnT = 0; spawnEnemy(); }  
  
  enemies.forEach(e => e.y += e.vy * (dt / 16.7));  
  enemies = enemies.filter(e => e.y < H + 110);  
  
  for (const e of enemies) if (hit(player, e)) { finish("crash"); return; }  
  
  if (mode === "online" || mode === "challenge") {  
    ai.forEach((a, i) => { a.d += (a.v + Math.sin(tG / 900 + i * 1.7) * 7) * dt / 1000; });  
    if (dist >= TARGET) { finish("win"); return; }  
    const w = ai.find(a => a.d >= TARGET);  
    if (w) { finish("lose", w.n); return; }  
    if (t - lbT > 150) { lbT = t; renderLB(); }  
  }  
  
  $("dist").textContent = Math.floor(dist);  
  $("spd").textContent = Math.round(pspeed * 14);  
  draw();  
  requestAnimationFrame(loop);  
}  
  
/* ================= النهاية ================= */  
function finish(type, extra) {  
  running = false;  
  const best = +(localStorage.getItem("race_best") || 0);  
  if (mode === "solo" && dist > best) localStorage.setItem("race_best", Math.floor(dist));  
  const rank = 1 + ai.filter(a => a.d > dist).length;  
  const T = $("endTitle"), M = $("endMsg");  
    
  if (type === "crash") {  
    T.textContent = "💥 حادث! انتهى السباق"; T.className = "end-title lose";  
    M.textContent = (mode === "online" || mode === "challenge") ? "مركزك النهائي: " + rank : "حاول تجنب السيارات مرة أخرى";  
    beep(120, .4, "sawtooth");  
  } else if (type === "win") {  
    T.textContent = mode === "challenge" ? `⚔️ هدمت ${currentChallengedFriend}!` : "🏆 فزت بالسباق!";   
    T.className = "end-title win";  
    M.textContent = "وصلت خط النهاية قبل الجميع — أنت بطل الطريق";  
    beep(523, .15); setTimeout(() => beep(659, .15), 150); setTimeout(() => beep(784, .3), 300);  
  } else {  
    T.textContent = "😞 خسرت السباق"; T.className = "end-title lose";  
    M.textContent = "وصل " + extra + " إلى خط النهاية قبلك";  
    beep(300, .3, "sawtooth");  
  }  
  $("endScore").textContent = Math.floor(dist) + " م";  
  show("end");  
}  
  
/* ================= التحكم ================= */  
window.addEventListener("keydown", e => {  
  if (e.key === "ArrowLeft"  || e.key === "a" || e.key === "A") { keys.l = true; e.preventDefault(); }  
  if (e.key === "ArrowRight" || e.key === "d" || e.key === "D") { keys.r = true; e.preventDefault(); }  
  if (e.key === "p" || e.key === "P" || e.key === "Escape") togglePause();  
});  
window.addEventListener("keyup", e => {  
  if (e.key === "ArrowLeft"  || e.key === "a" || e.key === "A") keys.l = false;  
  if (e.key === "ArrowRight" || e.key === "d" || e.key === "D") keys.r = false;  
});  
[["btnL", "l"], ["btnR", "r"]].forEach(([id, k]) => {  
  const b = $(id);  
  b.addEventListener("pointerdown", e => { e.preventDefault(); keys[k] = true; });  
  ["pointerup", "pointerleave", "pointercancel"].forEach(ev => b.addEventListener(ev, () => keys[k] = false));  
  b.addEventListener("contextmenu", e => e.preventDefault());  
});  
  
/* ================= تشغيل ================= */  
buildSwatches();  
refreshBest();  
</script>  
</body>  
</html>  
