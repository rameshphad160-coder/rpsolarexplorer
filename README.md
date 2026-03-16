<!doctype html>
<html lang="mr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ToolxRP Solar System Lab</title>

<script src="https://cdn.tailwindcss.com"></script>
<script src="https://cdn.jsdelivr.net/npm/lucide@latest/dist/umd/lucide.min.js"></script>

<link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600&display=swap" rel="stylesheet">

<style>
body{margin:0;height:100vh;overflow:hidden;background:#020617;color:white;font-family:Outfit}
canvas{display:block}
.panel{background:rgba(15,23,42,.9);border:1px solid rgba(56,189,248,.3);border-radius:12px}
</style>
</head>

<body>

<canvas id="solar"></canvas>

<div class="absolute top-6 left-1/2 -translate-x-1/2 text-center">
<h1 class="text-xl font-bold">Solar System Interactive Lab</h1>
<p class="text-xs text-sky-400">Accurate Educational Model</p>
</div>

<input id="search"
class="absolute top-20 left-1/2 -translate-x-1/2 w-72 px-4 py-2 rounded-full bg-slate-800 text-sm"
placeholder="ग्रह शोधा / Search planet">

<div id="info" class="absolute right-6 top-1/2 -translate-y-1/2 w-80 panel p-5 hidden"></div>

<script>

const PLANETS=[
{
id:"sun",
name:"सूर्य",
search:["sun","surya"],
emoji:"☀️",
radius:50,
color:"#fbbf24",
orbit:0,
speed:0,
desc:"सूर्य हा G-type main sequence तारा आहे आणि संपूर्ण सौरमालेच्या 99.86% वस्तुमानाचा स्रोत आहे.",
stats:{
"प्रकार":"तारा",
"व्यास":"1,391,000 km",
"तापमान":"5500 °C",
"वय":"4.6 अब्ज वर्ष"
}
},

{
id:"mercury",
name:"बुध",
search:["mercury","budh"],
emoji:"🌑",
radius:8,
color:"#9ca3af",
orbit:90,
speed:1.6,
desc:"सूर्याच्या सर्वात जवळचा ग्रह. वातावरण जवळजवळ नाही.",
stats:{
"सूर्यापासून अंतर":"57.9 million km",
"वर्ष":"88 पृथ्वी दिवस",
"दिवस":"176 पृथ्वी दिवस",
"उपग्रह":"0"
}
},

{
id:"venus",
name:"शुक्र",
search:["venus","shukra"],
emoji:"🌕",
radius:14,
color:"#fcd34d",
orbit:130,
speed:1.2,
desc:"सौरमालेतील सर्वात उष्ण ग्रह. दाट CO₂ atmosphere मुळे greenhouse effect.",
stats:{
"तापमान":"465 °C",
"वर्ष":"225 दिवस",
"Rotation":"Retrograde",
"उपग्रह":"0"
}
},

{
id:"earth",
name:"पृथ्वी",
search:["earth","prithvi"],
emoji:"🌍",
radius:15,
color:"#3b82f6",
orbit:180,
speed:1,
desc:"आपला ग्रह. सौरमालेतील जीवन असलेला एकमेव ज्ञात ग्रह.",
stats:{
"पाणी":"71%",
"वर्ष":"365 दिवस",
"चंद्र":"1",
"Orbit speed":"29.78 km/s"
}
},

{
id:"mars",
name:"मंगळ",
search:["mars","mangal"],
emoji:"🔴",
radius:11,
color:"#ef4444",
orbit:230,
speed:0.8,
desc:"लाल ग्रह. येथे Olympus Mons हा सौरमालेतील सर्वात मोठा ज्वालामुखी आहे.",
stats:{
"वर्ष":"687 दिवस",
"उपग्रह":"2",
"ज्वालामुखी":"Olympus Mons",
"तापमान":"-63 °C"
}
},

{
id:"jupiter",
name:"गुरु",
search:["jupiter","guru"],
emoji:"🟠",
radius:32,
color:"#f97316",
orbit:310,
speed:0.5,
desc:"सौरमालेतील सर्वात मोठा ग्रह. Gas giant.",
stats:{
"व्यास":"142,984 km",
"उपग्रह":"95+",
"वादळ":"Great Red Spot",
"वर्ष":"11.8 पृथ्वी वर्ष"
}
},

{
id:"saturn",
name:"शनी",
search:["saturn","shani"],
emoji:"🪐",
radius:28,
color:"#eab308",
orbit:400,
speed:0.4,
desc:"त्याच्या सुंदर rings मुळे प्रसिद्ध.",
stats:{
"उपग्रह":"146",
"वर्ष":"29 पृथ्वी वर्ष",
"Density":"पाण्यापेक्षा कमी",
"Rings":"Ice particles"
}
},

{
id:"uranus",
name:"युरेनस",
search:["uranus"],
emoji:"💎",
radius:20,
color:"#22d3ee",
orbit:480,
speed:0.3,
desc:"हा ग्रह अक्षावर 98° झुकलेला आहे.",
stats:{
"तापमान":"-224 °C",
"वर्ष":"84 पृथ्वी वर्ष",
"उपग्रह":"27",
"प्रकार":"Ice Giant"
}
},

{
id:"neptune",
name:"नेपच्यून",
search:["neptune"],
emoji:"🔵",
radius:19,
color:"#6366f1",
orbit:550,
speed:0.2,
desc:"सौरमालेतील सर्वात वादळी ग्रह.",
stats:{
"वारा":"2100 km/h",
"वर्ष":"165 पृथ्वी वर्ष",
"उपग्रह":"14",
"प्रकार":"Ice Giant"
}
}

]

const canvas=document.getElementById("solar")
const ctx=canvas.getContext("2d")

let w,h,time=0

function resize(){
w=canvas.width=window.innerWidth
h=canvas.height=window.innerHeight
}

window.onresize=resize
resize()

function draw(){

ctx.clearRect(0,0,w,h)

PLANETS.forEach(p=>{

let a=time*p.speed*0.01
let x=Math.cos(a)*p.orbit
let y=Math.sin(a)*p.orbit

ctx.fillStyle=p.color
ctx.beginPath()
ctx.arc(w/2+x,h/2+y,p.radius,0,Math.PI*2)
ctx.fill()

ctx.fillStyle="white"
ctx.font="10px Outfit"
ctx.textAlign="center"
ctx.fillText(p.name,w/2+x,h/2+y+p.radius+12)

})

time++

requestAnimationFrame(draw)

}

draw()

// SEARCH

document.getElementById("search").oninput=e=>{

let v=e.target.value.toLowerCase()

let p = PLANETS.find(x =>
  x.name.includes(v) ||
  x.search.some(s => s.includes(v))
)

if(!p)return

let html=`<h2 class="text-xl mb-2">${p.emoji} ${p.name}</h2>
<p class="text-sm mb-3">${p.desc}</p>`

for(let k in p.stats){

html+=`<div class="flex justify-between text-xs border-b border-slate-700 py-1">
<span class="text-sky-400">${k}</span>
<span>${p.stats[k]}</span>
</div>`

}

let box=document.getElementById("info")
box.innerHTML=html
box.style.display="block"

}

</script>
</body>
</html>
