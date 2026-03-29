<!DOCTYPE html><html lang="vi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Panel TKing Mod</title><style>
body{
margin:0;
font-family:Arial;
background:black;
color:#00ffe1;
overflow:hidden;
}

canvas{
position:fixed;
top:0;
left:0;
z-index:-1;
}

.box{
position:absolute;
top:50%;
left:50%;
transform:translate(-50%,-50%);
background:#000000cc;
padding:40px;
border-radius:20px;
box-shadow:0 0 40px #00ffe1;
width:340px;
text-align:center;
}

input{
width:90%;
padding:12px;
border:none;
border-radius:10px;
margin-top:10px;
}

button{
padding:12px;
margin-top:10px;
width:95%;
border:none;
border-radius:12px;
background:#00ffe1;
color:black;
font-weight:bold;
cursor:pointer;
}

.feature{
margin-top:10px;
padding:10px;
border-radius:10px;
background:#001b1b;
}

.notice{
margin-top:10px;
font-size:13px;
color:yellow;
}

.menu{ display:none }

#loading{
position:fixed;
width:100%;
height:100%;
background:black;
display:flex;
align-items:center;
justify-content:center;
font-size:30px;
color:#00ffe1;
z-index:9999;
}
</style></head>
<body><div id="loading">⚡ ĐANG TẢI PANEL TKing Mod...</div>
<canvas id="matrix"></canvas><div class="box" id="loginBox">
<h2>🔐 PANEL LOGIN</h2>
<input id="keyInput" placeholder="Nhập Key">
<button onclick="login()">Đăng nhập</button>
<button onclick="getKey()">GET KEY 24H</button>
<button onclick="copyZalo()">📩 Mua Key Vĩnh Viễn</button>
<div class="notice" id="msg"></div>
</div><div class="box menu" id="menuBox">
<h2>👑 PANEL TKing Mod</h2>
<div id="expireInfo"></div><div class="feature">🎯 Chức năng 1</div>
<div class="feature">🔥 Chức năng 2</div>
<div class="feature">🚀 Chức năng 3</div>
<div class="feature">🛡️ Chức năng 4</div>
<div class="feature">⚡ Chức năng 5</div><button onclick="logout()">Đăng xuất</button>

</div><audio id="music" src="https://files.catbox.moe/8xqg5x.mp3"></audio>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
import { getFirestore, doc, getDoc, setDoc } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";

const firebaseConfig = {
apiKey: "AIzaSyAXjLZO2KZZ9cyQrxNOXq4fdf8Re2V34Oc",
authDomain: "panel-tking-mod.firebaseapp.com",
projectId: "panel-tking-mod",
storageBucket: "panel-tking-mod.firebasestorage.app",
messagingSenderId: "373967213010",
appId: "1:373967213010:web:82ab784494c8890deb780b",
measurementId: "G-HL7QV9KM7S"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

const ADMIN_KEY="TKing-Mod-82hGal2";
const KEY_TIME=24*60*60*1000;
const COOLDOWN=3*24*60*60*1000;

function now(){return Date.now();}
function format(t){return new Date(t).toLocaleString();}

// FIX lỗi nút GETKEY không bấm được
window.getKey = async function(){
try{
let device = navigator.userAgent.replace(/[^a-zA-Z0-9]/g,"_");

let ref = doc(db,"devices",device);
let snap = await getDoc(ref);

if(snap.exists()){
let last = snap.data().lastGet;

if(now()-last<COOLDOWN){
document.getElementById("msg").innerHTML="⛔ Chờ tới: "+format(last+COOLDOWN);
return;
}
}

let newKey="TKING-"+Math.random().toString(36).substring(2,8).toUpperCase();
let expire=now()+KEY_TIME;

await setDoc(doc(db,"keys",newKey),{
expire:expire
});

await setDoc(ref,{lastGet:now()});

await navigator.clipboard.writeText(newKey);

document.getElementById("msg").innerHTML="✅ Đã copy key: "+newKey+" | hết hạn: "+format(expire);

}catch(e){
document.getElementById("msg").innerHTML="❌ Lỗi GETKEY: bật lại Firestore Security hoặc mạng";
console.log(e);
}
}

// LOGIN FIX
window.login = async function(){
let input=document.getElementById("keyInput").value.trim();

if(input===ADMIN_KEY){
openMenu("ADMIN VĨNH VIỄN");
return;
}

let ref=doc(db,"keys",input);
let snap=await getDoc(ref);

if(!snap.exists()){
document.getElementById("msg").innerHTML="❌ Key không hợp lệ";
return;
}

let data=snap.data();

if(now()>data.expire){
document.getElementById("msg").innerHTML="❌ Key đã hết hạn";
return;
}

localStorage.setItem("userKey",input);
openMenu(format(data.expire));
}

function openMenu(expire){
document.getElementById("loginBox").style.display="none";
document.getElementById("menuBox").style.display="block";

if(expire==="ADMIN VĨNH VIỄN"){
document.getElementById("expireInfo").innerHTML="👑 KEY ADMIN VĨNH VIỄN";
}else{
document.getElementById("expireInfo").innerHTML="⏰ Hết hạn: "+expire;
}
}

window.logout=function(){
document.getElementById("menuBox").style.display="none";
document.getElementById("loginBox").style.display="block";
}

window.copyZalo=function(){
navigator.clipboard.writeText("0366462632");
document.getElementById("msg").innerHTML="✔ Đã copy Zalo mua key";
}

window.addEventListener("click",()=>{
document.getElementById("music").play();
},{once:true});

setTimeout(()=>document.getElementById("loading").style.display="none",2000);

let canvas=document.getElementById("matrix");
let ctx=canvas.getContext("2d");
canvas.height=window.innerHeight;
canvas.width=window.innerWidth;

let letters="01TKING";
letters=letters.split("");
let fontSize=14;
let columns=canvas.width/fontSize;
let drops=[];

for(let x=0;x<columns;x++) drops[x]=1;

function draw(){
ctx.fillStyle="rgba(0,0,0,0.05)";
ctx.fillRect(0,0,canvas.width,canvas.height);

ctx.fillStyle="#00ffe1";
ctx.font=fontSize+"px monospace";

for(let i=0;i<drops.length;i++){
let text=letters[Math.floor(Math.random()*letters.length)];
ctx.fillText(text,i*fontSize,drops[i]*fontSize);

if(drops[i]*fontSize>canvas.height && Math.random()>0.975)
drops[i]=0;

drops[i]++;
}
}

setInterval(draw,33);

</script></body>
</html>
