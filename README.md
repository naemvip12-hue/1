<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Task Pay Ads</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">

<!-- Telegram WebApp -->
<script src="https://telegram.org/js/telegram-web-app.js"></script>

<!-- Monetag SDK -->
<script src="//libtl.com/sdk.js"
        data-zone="10387801"
        data-sdk="show_10387801"></script>

<style>
body{
  margin:0;
  font-family:Arial,Helvetica,sans-serif;
  background:linear-gradient(135deg,#0f1224,#1b1f4a);
  color:#fff;
  display:flex;
  justify-content:center;
  align-items:center;
  min-height:100vh;
}
.box{
  width:100%;
  max-width:380px;
  background:#181c3a;
  border-radius:22px;
  padding:25px;
  text-align:center;
  box-shadow:0 0 35px rgba(0,0,0,.5);
}
h1{
  margin:0 0 8px;
  font-size:24px;
}
.user{
  opacity:.8;
  font-size:14px;
  margin-bottom:15px;
}
.counter{
  background:#101433;
  padding:12px;
  border-radius:14px;
  margin-bottom:18px;
  font-size:16px;
}
.counter b{
  font-size:22px;
  display:block;
}
.btn{
  width:100%;
  padding:16px;
  border:none;
  border-radius:16px;
  background:linear-gradient(135deg,#6a5cff,#8b7bff);
  color:#fff;
  font-size:18px;
  font-weight:bold;
  cursor:pointer;
  animation:pulse 2s infinite;
}
.btn:disabled{
  opacity:.6;
  animation:none;
}
.status{
  margin-top:15px;
  font-size:14px;
  min-height:18px;
  opacity:.85;
}
@keyframes pulse{
  0%{box-shadow:0 0 0 0 rgba(138,123,255,.6)}
  70%{box-shadow:0 0 0 14px rgba(138,123,255,0)}
  100%{box-shadow:0 0 0 0 rgba(138,123,255,0)}
}
</style>
</head>

<body>

<div class="box">
  <h1>▶ Watch Ads</h1>
  <div class="user" id="userInfo">Loading...</div>

  <div class="counter">
    Total Ads Watched
    <b id="totalAds">0</b>
  </div>

  <button class="btn" id="watchBtn">WATCH AD</button>

  <div class="status" id="status">Ready</div>
</div>

<script>
const tg = window.Telegram.WebApp;
tg.ready();
tg.expand();

const user = tg.initDataUnsafe.user;
document.getElementById("userInfo").innerText =
  user
    ? `@${user.username || user.first_name} | ID: ${user.id}`
    : "Telegram User";

const btn = document.getElementById("watchBtn");
const status = document.getElementById("status");
const totalAdsEl = document.getElementById("totalAds");

const COOLDOWN = 30; // seconds

let data = JSON.parse(localStorage.getItem("ads_only_bot")) || {
  totalAds: 0,
  lastAdTime: 0
};

function save(){
  localStorage.setItem("ads_only_bot", JSON.stringify(data));
}

function updateCounter(){
  totalAdsEl.innerText = data.totalAds;
}

function checkCooldown(){
  const now = Date.now();
  const diff = Math.floor((now - data.lastAdTime)/1000);
  if(diff < COOLDOWN){
    btn.disabled = true;
    status.innerText = `Wait ${COOLDOWN - diff}s before next ad`;
    setTimeout(checkCooldown,1000);
    return true;
  }
  btn.disabled = false;
  status.innerText = "Ready";
  return false;
}

btn.onclick = () => {
  if(checkCooldown()) return;

  if(typeof show_10387801 !== "function"){
    status.innerText = "Ad not ready. Try again later.";
    return;
  }

  btn.disabled = true;
  status.innerText = "Loading ad...";

  show_10387801()
    .then(()=>{
      data.totalAds++;
      data.lastAdTime = Date.now();
      save();
      updateCounter();
      status.innerText = "Ad watched successfully ✅";
      checkCooldown();
    })
    .catch(()=>{
      status.innerText = "Ad failed. Try later ❌";
      btn.disabled = false;
    });
};

updateCounter();
checkCooldown();
</script>

</body>
</html>
