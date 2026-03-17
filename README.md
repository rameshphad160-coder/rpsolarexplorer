<!DOCTYPE html>
<html lang="mr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>RP Solar Lab</title>

<script src="https://cdn.tailwindcss.com"></script>

<style>

body{
margin:0;
background:#020617;
color:white;
font-family:sans-serif;
overflow:hidden;
}

/* STAR BACKGROUND */
body::before{
content:"";
position:absolute;
width:100%;
height:100%;
background:
radial-gradient(2px 2px at 20% 30%, white, transparent),
radial-gradient(2px 2px at 70% 60%, white, transparent),
radial-gradient(1px 1px at 40% 80%, white, transparent);
opacity:0.3;
}

/* ORBIT STYLE */
.orbit{
position:absolute;
border:1px solid rgba(255,255,255,0.08);
border-radius:50%;
}

/* PANEL */
.panel{
background:rgba(15,23,42,0.9);
border:1px solid rgba(56,189,248,0.3);
border-radius:14px;
backdrop-filter:blur(12px);
}

/* CANVAS FIX */
canvas{
display:block;
}

</style>

</head>

<body>

<!-- TITLE -->
<div class="absolute top-5 left-1/2 -translate-x-1/2 text-center z-10">
<h1 class="text-xl font-bold">Solar System Interactive Lab</h1>
<p class="text-xs text-sky-400">Interactive Educational Model</p>

<input id="search"
class="mt-3 px-4 py-2 rounded-full bg-slate-800 text-sm"
placeholder="Search planet / ग्रह शोधा">
</div>

<!-- RIGHT INFO PANEL -->
<div id="infoPanel"
class="absolute right-6 top-1/2 -translate-y-1/2 w-80 panel p-5">

<h2 class="text-lg text-sky-400">ग्रह निवडा</h2>
<p class="text-sm text-gray-400">Hover किंवा click करा</p>

</div>

<!-- CANVAS -->
<canvas id="solar"></canvas>

<script>

const PLANETS=[

{
id:"sun",
name:"सूर्य",
search:["sun","surya"],
emoji:"☀️",
r:40,
c:"#fbbf24",
or:0,
sp:0,
desc:"सूर्य हा सौरमालेचा केंद्रबिंदू आहे. प्रकाश पृथ्वीवर यायला 8 मिनिटे 20 सेकंद लागतात.",
stats:{
प्रकार:"तारा",
तापमान:"5500°C",
व्यास:"1.39 million km"
}
},

{
id:"earth",
name:"पृथ्वी",
search:["earth","prithvi"],
emoji:"🌍",
r:10,
c:"#3b82f6",
or:150,
sp:1,
desc:"पृथ्वी हा जीवन असलेला एकमेव ग्रह आहे.",
stats:{
पाणी:"71%",
उपग्रह:"1",
वर्ष:"365 दिवस"
}
},

{
id:"mars",
name:"मंगळ",
search:["mars","mangal","red"],
emoji:"🔴",
r:8,
c:"#ef4444",
or:220,
sp:0.8,
desc:"मंगळावर भविष्यात मानव राहू शकतो का यावर संशोधन चालू आहे.",
stats:{
वर्ष:"687 दिवस",
उपग्रह:"2"
}
}

]

const canvas=document.getElementById("solar")
const ctx=canvas.getContext("2d")

let w,h,time=0
let screen=[]

function resize(){

const dpr=window.devicePixelRatio||1

canvas.width=innerWidth*dpr
canvas.height=innerHeight*dpr

canvas.style.width=innerWidth+"px"
canvas.style.height=innerHeight+"px"

ctx.setTransform(dpr,0,0,dpr,0,0)

w=innerWidth
h=innerHeight

}

resize()
window.onresize=resize

function draw(){

ctx.clearRect(0,0,w,h)
screen=[]

PLANETS.forEach(p=>{

let a=time*p.sp*0.01

let x=Math.cos(a)*p.or
let y=Math.sin(a)*p.or

let px=w/2+x
let py=h/2+y

// orbit
if(p.or>0){
ctx.strokeStyle="rgba(255,255,255,0.08)"
ctx.beginPath()
ctx.arc(w/2,h/2,p.or,0,Math.PI*2)
ctx.stroke()
}

// planet
ctx.fillStyle=p.c
ctx.beginPath()
ctx.arc(px,py,p.r,0,Math.PI*2)
ctx.fill()

// name
ctx.fillStyle="white"
ctx.font="10px sans-serif"
ctx.textAlign="center"
ctx.fillText(p.name,px,py+p.r+12)

screen.push({p,px,py,r:p.r})

})

time++
requestAnimationFrame(draw)

}

draw()

// HOVER
canvas.addEventListener("mousemove",e=>{

let mx=e.clientX
let my=e.clientY

for(let obj of screen){

let dx=mx-obj.px
let dy=my-obj.py

if(Math.sqrt(dx*dx+dy*dy)<obj.r+5){

updatePanel(obj.p)
return

}

}

})

// PANEL UPDATE
function updatePanel(p){

let html=`
<h2 class="text-lg mb-2">${p.emoji} ${p.name}</h2>
<p class="text-sm mb-3">${p.desc}</p>
`

for(let k in p.stats){

html+=`
<div class="flex justify-between text-xs border-b border-slate-700 py-1">
<span class="text-sky-400">${k}</span>
<span>${p.stats[k]}</span>
</div>
`

}

document.getElementById("infoPanel").innerHTML=html

}

// SEARCH
document.getElementById("search").oninput=e=>{

let v=e.target.value.toLowerCase()

let p=PLANETS.find(x=>
x.name.toLowerCase().includes(v)||
x.search.some(s=>s.includes(v))
)

if(p) updatePanel(p)

}

</script>

</body>
</html>
