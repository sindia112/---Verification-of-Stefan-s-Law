# ---Verification-of-Stefan-s-Law
Physics practical tool to enter Voltage and Current values, automatically compute R, P, logR, logP, and generate a logR vs logP graph with slope and best-fit line.
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>𝐒⚡︎𝐈𝐍𝐃𝐈𝐀- Verification of Stefan’s Law</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/jspdf"></script>

<style>
body{
font-family:Arial, sans-serif;
margin:20px;
background:#f4f6fb;
}

h2{
text-align:center;
}

.container{
max-width:1100px;
margin:auto;
}

table{
width:100%;
border-collapse:collapse;
background:white;
}

th,td{
border:1px solid #ccc;
padding:6px;
text-align:center;
}

th{
background:#e9eefc;
}

td input{
width:90%;
border:none;
text-align:center;
}

.controls{
margin:15px 0;
display:flex;
flex-wrap:wrap;
gap:10px;
justify-content:center;
}

button{
padding:8px 14px;
border:none;
background:#3b6df6;
color:white;
border-radius:5px;
cursor:pointer;
}

button:hover{
background:#244ad1;
}

#slope{
margin-top:10px;
font-weight:bold;
text-align:center;
}

canvas{
background:white;
margin-top:20px;
}

@media(max-width:700px){
table{
font-size:12px;
}
}
</style>
</head>

<body>

<div class="container">

<h2> Verification of Stefan’s Law <br><br>Physics Practical Observation Sheet</h2>

<div class="controls">
<label>Current Unit:</label>
<select id="unit">
<option value="A">Ampere (A)</option>
<option value="mA">Milliampere (mA)</option>
</select>

<button onclick="generateGraph()">Generate Graph</button>
<button onclick="exportPDF()">𝐒⚡︎𝐈𝐍𝐃𝐈𝐀</button>
<button onclick="downloadPNG()">Download Graph PNG</button>
<button onclick="resetAll()">Reset</button>
</div>

<table id="dataTable">
<thead>
<tr>
<th>S.No</th>
<th>Volt (V)</th>
<th>Current (I)</th>
<th>R = V/I</th>
<th>P = VI</th>
<th>log R</th>
<th>log P</th>
</tr>
</thead>
<tbody id="tableBody"></tbody>
</table>

<canvas id="graph" height="120"></canvas>

<div id="slope"></div>

</div>

<script>

const rows = 10;
const body = document.getElementById("tableBody");

for(let i=1;i<=rows;i++){
let tr=document.createElement("tr");

tr.innerHTML=`
<td>${i}</td>
<td><input type="number" step="any" oninput="calculate(this)"></td>
<td><input type="number" step="any" oninput="calculate(this)"></td>
<td class="r"></td>
<td class="p"></td>
<td class="logr"></td>
<td class="logp"></td>
`;

body.appendChild(tr);
}

function calculate(el){

let row=el.parentElement.parentElement;
let v=row.cells[1].children[0].value;
let i=row.cells[2].children[0].value;
let unit=document.getElementById("unit").value;



if(v && i){

if(unit=="mA"){
i=i/1000;
}

let r=v/i;
let p=v*i;

let logR=Math.log10(r);
let logP=Math.log10(p);

row.querySelector(".r").innerText=r.toFixed(4);
row.querySelector(".p").innerText=p.toFixed(4);
row.querySelector(".logr").innerText=logR.toFixed(4);
row.querySelector(".logp").innerText=logP.toFixed(4);

}

}

let chart;

function generateGraph(){

let x=[];
let y=[];

document.querySelectorAll("#tableBody tr").forEach(r=>{

let logR=r.querySelector(".logr").innerText;
let logP=r.querySelector(".logp").innerText;

if(logR && logP){
x.push(parseFloat(logR));
y.push(parseFloat(logP));
}

});

if(x.length<2){
alert("Need at least 2 points");
return;
}

let n=x.length;

let sumx=0,sumy=0,sumxy=0,sumx2=0;

for(let i=0;i<n;i++){
sumx+=x[i];
sumy+=y[i];
sumxy+=x[i]*y[i];
sumx2+=x[i]*x[i];
}

let slope=(n*sumxy-sumx*sumy)/(n*sumx2-sumx*sumx);
let intercept=(sumy-slope*sumx)/n;

document.getElementById("slope").innerText="Slope = "+slope.toFixed(4);

let lineX=[Math.min(...x),Math.max(...x)];
let lineY=lineX.map(v=>slope*v+intercept);

if(chart){
chart.destroy();
}

chart=new Chart(document.getElementById("graph"),{
type:"scatter",
data:{
datasets:[
{
label:"Data Points",
data:x.map((v,i)=>({x:v,y:y[i]}))
},
{
type:"line",
label:"Best Fit Line",
data:lineX.map((v,i)=>({x:v,y:lineY[i]})),
fill:false
}
]
},
options:{
scales:{
x:{title:{display:true,text:"log R"}},
y:{title:{display:true,text:"log P"}}
}
}
});

}

function downloadPNG(){

let link=document.createElement("a");
link.download="graph.png";
link.href=document.getElementById("graph").toDataURL();
link.click();

}

function exportPDF(){

let pdf=new jsPDF();

pdf.text("Physics Practical Observation Sheet",20,10);

let y=20;

document.querySelectorAll("#dataTable tr").forEach(tr=>{
let row=[];
tr.querySelectorAll("th,td").forEach(td=>{
row.push(td.innerText);
});
pdf.text(row.join("   "),10,y);
y+=8;
});

pdf.save("observation.pdf");

}

function resetAll(){

document.querySelectorAll("input").forEach(i=>i.value="");
document.querySelectorAll(".r,.p,.logr,.logp").forEach(c=>c.innerText="");

document.getElementById("slope").innerText="";

if(chart){
chart.destroy();
}

}

</script>

</body>
</html>
