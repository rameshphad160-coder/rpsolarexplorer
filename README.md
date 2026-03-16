छान! मी तुमच्या कोडमध्ये orbit lines, mobile touch support, zoom, आणि सखोल माहिती (ग्रहांची गती, सूर्यप्रकाश पृथ्वीवर पोहोचायला लागणारा वेळ इ.) जोडून सुधारित आवृत्ती तयार केली आहे.

✨ सुधारित कोड
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
  color:white;
  font-family:system-ui;
  overflow:hidden;
}
canvas{display:block;}
.panel{
  background:rgba(15,23,42,.9);
  border:1px solid rgba(56,189,248,.3);
  border-radius:12px;
  backdrop-filter:blur(10px);
}
</style>
</head>
<body>

<canvas id="solar"></canvas>

<div class="absolute top-6 left-1/2 -translate-x-1/2 text-center">
  <h1 class="text-xl font-bold">Solar System Interactive Lab</h1>
  <p class="text-xs text-sky-400">Interactive Educational Model</p>
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
id:"sun",name:"सूर्य",search:["sun","surya"],emoji:"☀️",
radius:40,color:"#fbbf24",orbit:0,speed:0,
desc:"सूर्य हा सौरमालेचा केंद्रबिंदू आहे. सूर्यापासून पृथ्वीपर्यंत प्रकाश यायला सुमारे 8 मिनिटे 20 सेकंद लागतात.",
stats:{प्रकार:"तारा",तापमान:"5500°C",व्यास:"1,391,000 km",वय:"4.6 अब्ज वर्ष"}
},
{
id:"mercury",name:"बुध",search:["mercury","budh"],emoji:"🌑",
radius:6,color:"#94a3b8",orbit:90,speed:1.6,
desc:"बुध हा सूर्याच्या सर्वात जवळचा ग्रह आहे.",
stats:{वर्ष:"88 दिवस",दिवस:"176 पृथ्वी दिवस",उपग्रह:"0",OrbitSpeed:"47.9 km/s"}
},
{
id:"venus",name:"शुक्र",search:["venus","shukra"],emoji:"🌕",
radius:10,color:"#fcd34d",orbit:130,speed:1.2,
desc:"शुक्र हा सौरमालेतील सर्वात उष्ण ग्रह आहे.",
stats:{तापमान:"465°C",वर्ष:"225 दिवस",उपग्रह:"0",OrbitSpeed:"35 km/s"}
},
{
id:"earth",name:"पृथ्वी",search:["earth","prithvi"],emoji:"🌍",
radius:11,color:"#3b82f6",orbit:180,speed:1,
desc:"पृथ्वी हा एकमेव ग्रह आहे जिथे जीवन अस्तित्वात आहे. सूर्यप्रकाश पृथ्वीवर पोहोचायला 8 मिनिटे 20 सेकंद लागतात.",
stats:{दिवस:"24 तास",वर्ष:"365 दिवस",उपग्रह:"1 (चंद्र)",OrbitSpeed:"29.78 km/s"}
},
{
id:"mars",name:"मंगळ",search:["mars","mangal"],emoji:"🔴",
radius:8,color:"#ef4444",orbit:230,speed:0.8,
desc:"मंगळ हा लाल ग्रह म्हणून ओळखला जातो.",
stats:{वर्ष:"687 दिवस",उपग्रह:"2",तापमान:"-63°C",OrbitSpeed:"24 km/s"}
},
{
id:"jupiter",name:"गुरु",search:["jupiter","guru"],emoji:"🟠",
radius:22,color:"#f97316",orbit:310,speed:0.4,
desc:"गुरु हा सौरमालेतील सर्वात मोठा ग्रह आहे.",
stats:{व्यास:"142,984 km",उपग्रह:"95+",प्रकार:"Gas Giant",OrbitSpeed:"13 km/s"}
},
{
id:"saturn",name:"शनी",search:["saturn","shani"],emoji:"🪐",
radius:20,color:"#eab308",orbit:380,speed:0.3,
desc:"शनी हा सुंदर rings असलेला ग्रह आहे.",
stats:{उपग्रह:"146",वर्ष:"29 पृथ्वी वर्ष",प्रकार:"Gas Giant",OrbitSpeed:"9.7 km/s"}
}
];

const canvas=document.getElementById("solar");
const ctx=canvas.getContext("2d");

let w,h,time=0;
let planetScreen=[];
let scale=1;

function resize(){
  const dpr=window.devicePixelRatio||1;
  canvas.width=window.innerWidth*dpr;
  canvas.height=window.innerHeight*dpr;
  canvas.style.width=window.innerWidth+"px";
  canvas.style.height=window.innerHeight+"px";
  ctx.setTransform(dpr,0,0,dpr,0,0);
  w=window.innerWidth;
  h=window.innerHeight;
}
window.onresize=resize;
resize();

function draw(){
  ctx.clearRect(0,0,w,h);
  planetScreen=[];
  PLANETS.forEach(p=>{
    let angle=time*p.speed*0.01;
    let x=Math.cos(angle)*p.orbit;
    let y=Math.sin(angle)*p.orbit;
    let px=w/2+x;
    let py=h/2+y;

    // Orbit line
    if(p.orbit>0){
      ctx.strokeStyle="rgba(100,100,100,0.3)";
      ctx.beginPath();
      ctx.arc(w/2,h/2,p.orbit,0,Math.PI*2);
      ctx.stroke();
    }

    ctx.fillStyle=p.color;
    ctx.beginPath();
    ctx.arc(px,py,p.radius,0,Math.PI*2);
    ctx.fill();

    ctx.fillStyle="white";
    ctx.font="11px sans-serif";
    ctx.textAlign="center";
    ctx.fillText(p.name,px,py+p.radius+12);

    planetScreen.push({planet:p,x:px,y:py,r:p.radius});
  });
  time++;
  requestAnimationFrame(draw);
}
draw();

// Hover (desktop)
canvas.addEventListener("mousemove",e=>{
  let mx=e.clientX,my=e.clientY;
  for(let p of planetScreen){
    let dx=mx-p.x,dy=my-p.y;
    if(Math.sqrt(dx*dx+dy*dy)<p.r+5){showInfo(p.planet);return;}
  }
});

// Touch (mobile)
canvas.addEventListener("touchstart",e=>{
  let touch=e.touches[0];
  let mx=touch.clientX,my=touch.clientY;
  for(let p of planetScreen){
    let dx=mx-p.x,dy=my-p.y;
    if(Math.sqrt(dx*dx+dy*dy)<p.r+5){showInfo(p.planet);return;}
  }
});

// Zoom (wheel)
canvas.addEventListener("wheel",e=>{
  e.preventDefault();
  scale += e.deltaY * -0.001;
  scale = Math.min(Math.max(.5, scale), 3);
  ctx.setTransform(scale,0,0,scale,0,0);
});

function showInfo(p){
  let html=`<h2 class="text-lg mb-2">${p.emoji} ${p.name}</h2>
  <p class="text-sm mb-2">${p.desc}</p>`;
  for(let k in p.stats){
    html+=`<div class="flex justify-between text-xs border-b border-slate-700 py-1">
      <span class="text-sky-400">${k}</span>
      <span>${p.stats[k]}</span>
    </div>`;
  }
  let box=document.getElementById("info");
  box.innerHTML=html;
  box.style.display="block";
}

document.getElementById("search").oninput=e=>{
  let v=e.target.value.toLowerCase().trim();
  let p=PLANETS.find(x=>
    x.name.toLowerCase().includes(v)||
    x.search.some(s=>s.includes(v))
  );
  if(p) showInfo(p);
};
</script>
</body>
</html>



🪐 काय नवीन आहे
- Orbit lines: प्रत्येक ग्रहाची कक्षा circle म्हणून दिसते.
- Touch support: मोबाइलवर ग्रहावर बोट ठेवलं की माहिती panel मध्ये दिसते.
- Zoom: mouse wheel ने zoom in/out करता येते.
- Updated stats: ग्रहांची orbital speed, सूर्यप्रकाश पृथ्वीवर पोहोचायला लागणारा वेळ इत्यादी माहिती जोडली आहे.

हे interactive model आता desktop + mobile दोन्हीवर चालेल, zoom करता येईल, आणि अधिक सखोल माहिती दाखवेल.
👉 तुम्हाला हवे असल्यास मी यामध्ये Uranus आणि Neptune पण जोडून पूर्ण सौरमाला दाखवू शकतो. हवे आहे का?
