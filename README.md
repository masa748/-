<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>時を越えるフォトラリー📷</title>

<style>
body{
    font-family:sans-serif;
    background:#f3e9d2;
    margin:0;
    text-align:center;
    color:#2e4a2e;
}

h1{ padding:20px 0 5px; }

.progress{
    font-size:20px;
    font-weight:bold;
    margin-bottom:15px;
}

.grid{
    display:grid;
    grid-template-columns: repeat(2,1fr);
    gap:25px;
    padding:20px;
}

.photo-slot{
    width:130px;
    height:130px;
    border-radius:50%;
    background:#e8dcc3;
    overflow:hidden;
    margin:auto;
    position:relative;
    cursor:pointer;
    border:4px solid #355e3b;
}

.photo-slot img{
    width:100%;
    height:100%;
    object-fit:cover;
}

.camera-icon{
    font-size:40px;
    position:absolute;
    top:50%;
    left:50%;
    transform:translate(-50%,-50%);
}

.spot-name{ margin-top:8px; font-weight:bold; }

.map-btn{
    margin-top:6px;
    padding:6px 12px;
    background:#355e3b;
    color:white;
    border-radius:15px;
    font-size:12px;
    text-decoration:none;
    display:inline-block;
}

/* NICE SHOT */
#niceShot{
    position:fixed;
    top:50%;
    left:50%;
    transform:translate(-50%,-50%);
    font-size:40px;
    font-weight:bold;
    color:#fff;
    background:#355e3b;
    padding:20px 40px;
    border-radius:20px;
    display:none;
    z-index:1000;
    animation:pop 1s ease forwards;
}
@keyframes pop{
0%{opacity:0;transform:translate(-50%,-50%) scale(.5);}
50%{opacity:1;transform:translate(-50%,-50%) scale(1.2);}
100%{opacity:0;transform:translate(-50%,-50%) scale(1);}
}

/* 共通達成画面 */
.overlay{
position:fixed;
top:0;left:0;
width:100%;height:100%;
display:none;
flex-direction:column;
align-items:center;
overflow:auto;
padding:30px;
z-index:3000;
}

.overlay h2{margin:20px 0;}

.btn{
padding:12px 25px;
border-radius:25px;
text-decoration:none;
font-weight:bold;
margin:10px;
border:none;
cursor:pointer;
}

/* 4ヶ所 */
#complete4{
background:#355e3bee;
color:white;
}
#complete4 .btn{
background:white;
color:#355e3b;
}

/* 6ヶ所 和風豪華 */
#complete6{
background:linear-gradient(#0b1d3a,#1c2f5a);
color:#ffd700;
}
#complete6 h2{
font-size:36px;
letter-spacing:3px;
}
#complete6 .btn{
background:#ffd700;
color:#1c2f5a;
}

/* 桜風アニメ */
.sakura{
position:absolute;
font-size:20px;
animation:fall 5s linear infinite;
}
@keyframes fall{
0%{transform:translateY(-10%);opacity:1;}
100%{transform:translateY(110vh);opacity:0;}
}

/* 写真一覧 */
.photo-gallery{
display:grid;
grid-template-columns:repeat(2,1fr);
gap:10px;
margin-top:20px;
}
.photo-gallery img{
    width:100%;
    aspect-ratio: 1 / 1;
    object-fit: cover;
    border-radius:50%;
    border:3px solid #fff;
}

/* 昔風（レトロ）フォトエフェクト */
.retro {
    filter:
        sepia(0.6)
        contrast(1.1)
        saturate(0.8)
        brightness(0.95);
}

</style>
</head>
<body>

<h1>時を越えるフォトラリー</h1>
<div class="progress" id="progress">0 / 6</div>
<div class="grid" id="photoGrid"></div>

<input type="file" accept="image/*" capture="environment" id="cameraInput" style="display:none">
<div id="niceShot">📸 NICE SHOT!</div>

<!-- 4ヶ所 -->
<div id="complete4" class="overlay">
<h2>🏆 4ヶ所達成！</h2>

<div id="gallery4" class="photo-gallery"></div>

<a class="btn" href="https://twitter.com/intent/tweet?text=川越フォトラリー4ヶ所達成しました！" target="_blank">🐦 X投稿</a>
<a class="btn" href="https://www.instagram.com/" target="_blank">📷 Instagram</a>
<button class="btn" onclick="closeOverlay('complete4')">まだまだ巡る</button>
</div>

<!-- 6ヶ所 -->
<div id="complete6" class="overlay">
<h2>👑 完全制覇 👑</h2>
<p>川越マスター認定</p>

<div id="gallery6" class="photo-gallery"></div>

<a class="btn" href="https://twitter.com/intent/tweet?text=川越フォトラリー完全制覇しました！" target="_blank">🐦 X投稿</a>
<a class="btn" href="https://www.instagram.com/" target="_blank">📷 Instagram</a>
</div>

<script>
const spots=[
{name:"時の鐘",map:"https://www.google.com/maps/search/?api=1&query=時の鐘+川越"},
{name:"川越氷川神社",map:"https://www.google.com/maps/search/?api=1&query=川越氷川神社"},
{name:"川越りそなテラス",map:"https://www.google.com/maps/search/?api=1&query=川越りそなテラス"},
{name:"川越城本丸御殿",map:"https://www.google.com/maps/search/?api=1&query=川越城本丸御殿"},
{name:"喜多院",map:"https://www.google.com/maps/search/?api=1&query=喜多院+川越"},
{name:"川越熊野神社",map:"https://www.google.com/maps/search/?api=1&query=川越熊野神社"}
];

let currentIndex=null;
let completed=0;
let shown4=false;
let photos=[];

const grid=document.getElementById("photoGrid");
const progress=document.getElementById("progress");
const cameraInput=document.getElementById("cameraInput");
const niceShot=document.getElementById("niceShot");

spots.forEach((spot,index)=>{
const card=document.createElement("div");

const slot=document.createElement("div");
slot.className="photo-slot";
slot.dataset.index=index;

const icon=document.createElement("div");
icon.className="camera-icon";
icon.innerHTML="📷";
slot.appendChild(icon);

slot.onclick=()=>{
currentIndex=index;
cameraInput.value="";
cameraInput.click();
};

const name=document.createElement("div");
name.className="spot-name";
name.innerText=spot.name;

const mapBtn=document.createElement("a");
mapBtn.className="map-btn";
mapBtn.href=spot.map;
mapBtn.target="_blank";
mapBtn.innerText="📍 マップで案内";

card.appendChild(slot);
card.appendChild(name);
card.appendChild(mapBtn);
grid.appendChild(card);
});

cameraInput.addEventListener("change",(e)=>{
const file=e.target.files[0];
if(!file) return;

const reader=new FileReader();
reader.onload=(event)=>{
const slot=document.querySelector(`.photo-slot[data-index='${currentIndex}']`);
if(!slot.querySelector("img")) completed++;

slot.innerHTML=`<img src="${event.target.result}" class="retro">`;
photos[currentIndex]=event.target.result;

progress.innerText=`${completed} / 6`;

niceShot.style.display="block";
setTimeout(()=>{niceShot.style.display="none";},1000);

if(completed>=4 && !shown4){
shown4=true;
showGallery("complete4","gallery4");
}

if(completed===6){
showGallery("complete6","gallery6");
createSakura();
}
};
reader.readAsDataURL(file);
});

function showGallery(overlayId,galleryId){
const gallery=document.getElementById(galleryId);
gallery.innerHTML="";
photos.forEach(p=>{
if(p){
const img=document.createElement("img");
img.src=p;
gallery.appendChild(img);
}
});
document.getElementById(overlayId).style.display="flex";
}

function closeOverlay(id){
document.getElementById(id).style.display="none";
}

function createSakura(){
for(let i=0;i<20;i++){
const s=document.createElement("div");
s.className="sakura";
s.innerHTML="🌸";
s.style.left=Math.random()*100+"%";
s.style.animationDuration=3+Math.random()*3+"s";
document.body.appendChild(s);
}
}
</script>

</body>
</html>
