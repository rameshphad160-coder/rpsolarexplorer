<!DOCTYPE html>
<html lang="mr">
<head>

<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">

<title>RP Solar System Explorer</title>

<script src="https://cdn.tailwindcss.com"></script>

<style>

body{
margin:0;
background:#020617;
font-family:Outfit, sans-serif;
color:white;
overflow:hidden;
}

canvas{
display:block;
}

.panel{
background:rgba(15,23,42,.9);
border:1px solid rgba(56,189,248,.3);
border-radius:12px;
}

</style>

</head>

<body>

<canvas id="solar"></canvas>

<div class="absolute top-6 left-1/2 -translate-x-1/2 text-center">

<h1 class="text-xl font-bold">
Solar System Interactive Lab
</h1>

<p class="text-xs text-sky-400">
Educational Model
</p>

<input id="search"
class="mt-3 px-4 py-2 rounded-full bg-slate-800 text-sm"
placeholder="ग्रह शोधा / Search planet">

</div>

<div id="info"
class="absolute right-6 top-1/2 -translate-y-1/2 w-80 panel p-4 hidden">
</div>

<script>

const PLANETS=[

{
id:"sun",
name:"सूर्य",
emoji:"☀️",
radius:40,
color:"#fbbf24",
orbit:0,
speed:0,
desc:"सूर्य हा सौरमालेचा केंद्रबिंदू आहे.",
stats:{
प्रकार:"तारा",
व्यास:"1,391,000 km",
तापमान:"5500°C"
}
},

{
id:"mercury",
name:"बुध",
emoji:"🌑",
radius:6,
color:"#94a3b8",
orbit:80,
speed:1.6,
desc:"सूर्याच्या सर्वात जवळचा ग्रह.",
stats:{
वर्ष:"88 दिवस",
उपग्रह:"0"
}
},

{
id:"venus",
name:"शुक्र",
emoji:"🌕",
radius:10,
color:"#fcd34d",
orbit:120,
speed:1.2,
desc:"सौरमालेतील सर्वात उष्ण ग्रह.",
stats:{
तापमान:"465°C",
वर्ष:"225 दिवस"
}
},

{
id:"earth",
name:"पृथ्वी",
emoji:"🌍",
radius:11,
color:"#3b82f6",
orbit:170,
speed:1,
desc:"आपला ग्रह जिथे जीवन आहे.",
stats:{
पाणी:"71%",
उपग्रह:"1"
}
},

{
id:"mars",
name:"मंगळ",
emoji:"🔴",
radius:8,
color:"#ef4444",
orbit:220,
speed:0.8,
desc:"लाल ग्रह.",
stats:{
वर्ष:"687 दिवस",
उपग्रह:"2"
}
},

{
id:"jupiter",
name:"गुरु",
emoji:"🟠",
radius:22,
color:"#f97316",
orbit:300,
speed:0.4,
desc:"सौरमालेतील सर्वात मोठा ग्रह.",
stats:{
उपग्रह:"95+",
प्रकार:"Gas Giant"
}
},

{
id:"saturn",
name:"शनी",
emoji:"🪐",
radius:20,
color:"#eab308",
orbit:370,
speed:0.3,
desc:"सुंदर rings असलेला ग्रह.",
stats:{
उपग्रह:"146",
वर्ष:"29 वर्ष"
}
}

]

const canvas=document.getElementById("solar")
const ctx=canvas.getContext("2d")

let w,h
let time=0

let planetScreen=[]

function resize(){

w=canvas.width=window.innerWidth
h=canvas.height=window.innerHeight

}

window.onresize=resize
resize()

function draw(){

ctx.clearRect(0,0,w,h)

planetScreen=[]

PLANETS.forEach(p=>{

let angle=time*p.speed*0.01

let x=Math.cos(angle)*p.orbit
let y=Math.sin(angle)*p.orbit

let px=w/2+x
let py=h/2+y

ctx.fillStyle=p.color

ctx.beginPath()
ctx.arc(px,py,p.radius,0,Math.PI*2)
ctx.fill()

ctx.fillStyle="white"
ctx.font="11px sans-serif"
ctx.textAlign="center"

ctx.fillText(p.name,px,py+p.radius+12)

planetScreen.push({
planet:p,
x:px,
y:py,
r:p.radius
})

})

time++

requestAnimationFrame(draw)

}

draw()

canvas.addEventListener("mousemove",e=>{

let mx=e.clientX
let my=e.clientY

for(let p of planetScreen){

let dx=mx-p.x
let dy=my-p.y

if(Math.sqrt(dx*dx+dy*dy)<p.r+4){

showInfo(p.planet)
return

}

}

})

function showInfo(p){

let html=

`<h2 class="text-lg mb-2">${p.emoji} ${p.name}</h2>

<p class="text-sm mb-2">${p.desc}</p>`

for(let k in p.stats){

html+=

`<div class="flex justify-between text-xs border-b border-slate-700 py-1">

<span class="text-sky-400">${k}</span>

<span>${p.stats[k]}</span>

</div>`

}

let box=document.getElementById("info")

box.innerHTML=html

box.style.display="block"

}

document.getElementById("search").oninput=e=>{

let v=e.target.value.toLowerCase()

let p=PLANETS.find(x=>x.name.includes(v))

if(p) showInfo(p)

}

</script>

</body>
</html>
