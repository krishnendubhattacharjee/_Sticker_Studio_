# _Sticker_Studio_
It is a website where you can create interesting stickers
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sticker Studio</title>

<!-- PWA META -->
<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#8b5cf6">
<link rel="icon" href="icon.png">

<style>
body{
margin:0;
font-family:sans-serif;
background:#0f0f11;
color:white;
display:flex;
flex-direction:column;
height:100vh;
}
header{
padding:15px;
background:#18181b;
text-align:center;
font-weight:bold;
}
.container{display:flex;flex:1;}
.sidebar{
width:280px;
background:#18181b;
padding:15px;
overflow:auto;
}
button,input,select{
width:100%;
margin-bottom:10px;
padding:8px;
border-radius:6px;
border:none;
}
button{background:#3f3f46;color:white;}
button.primary{background:#8b5cf6;}
.workspace{
flex:1;
display:flex;
justify-content:center;
align-items:center;
background:#111;
}
canvas{
max-width:95%;
max-height:95%;
box-shadow:0 0 20px black;
}
</style>
</head>

<body>

<header>Sticker Studio</header>

<div class="container">
<div class="sidebar">

<input type="file" id="fileInput" accept="image/*">

<input type="text" id="txtInput" placeholder="Add text">
<select id="fontSelect">
<option value="Arial">Bold</option>
<option value="Courier New">Typewriter</option>
<option value="Impact">Impact</option>
</select>

<input type="color" id="txtColor" value="#ffffff">
<input type="number" id="txtSize" value="40">

<button class="primary" onclick="addTextToCanvas()">Add Text</button>
<button class="primary" onclick="downloadImage()">Download</button>

</div>

<div class="workspace">
<canvas id="canvas"></canvas>
</div>
</div>

<script>
const canvas=document.getElementById("canvas");
const ctx=canvas.getContext("2d");
const fileInput=document.getElementById("fileInput");

let image=new Image();
let texts=[];
let dragging=false;
let selected=null;

fileInput.addEventListener("change",e=>{
const reader=new FileReader();
reader.onload=ev=>{
image.onload=()=>{
canvas.width=image.width;
canvas.height=image.height;
ctx.drawImage(image,0,0);
};
image.src=ev.target.result;
};
reader.readAsDataURL(e.target.files[0]);
});

function redraw(){
ctx.clearRect(0,0,canvas.width,canvas.height);
ctx.drawImage(image,0,0);
texts.forEach((t,i)=>{
ctx.font=`bold ${t.size}px ${t.font}`;
ctx.textAlign="center";
ctx.textBaseline="middle";
ctx.strokeStyle="black";
ctx.lineWidth=t.size/15;
ctx.strokeText(t.text,t.x,t.y);
ctx.fillStyle=t.color;
ctx.fillText(t.text,t.x,t.y);
});
}

function addTextToCanvas(){
texts.push({
text:document.getElementById("txtInput").value,
font:document.getElementById("fontSelect").value,
color:document.getElementById("txtColor").value,
size:parseInt(document.getElementById("txtSize").value),
x:canvas.width/2,
y:canvas.height/2
});
redraw();
}

canvas.addEventListener("mousedown",e=>{
const rect=canvas.getBoundingClientRect();
const x=e.clientX-rect.left;
const y=e.clientY-rect.top;
texts.forEach((t,i)=>{
ctx.font=`bold ${t.size}px ${t.font}`;
let w=ctx.measureText(t.text).width;
let h=t.size;
if(x>t.x-w/2&&x<t.x+w/2&&y>t.y-h/2&&y<t.y+h/2){
selected=i;
dragging=true;
}
});
});

canvas.addEventListener("mousemove",e=>{
if(!dragging) return;
const rect=canvas.getBoundingClientRect();
texts[selected].x=e.clientX-rect.left;
texts[selected].y=e.clientY-rect.top;
redraw();
});

canvas.addEventListener("mouseup",()=>dragging=false);

function downloadImage(){
const link=document.createElement("a");
link.download="sticker.png";
link.href=canvas.toDataURL();
link.click();
}

/* REGISTER SERVICE WORKER */
if("serviceWorker" in navigator){
navigator.serviceWorker.register("service-worker.js");
}
</script>

</body>
</html>
