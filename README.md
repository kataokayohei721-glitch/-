<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">

<title>分数タイムアタック</title>

<style>

body{
    font-family:sans-serif;
    background:#eef7ff;
    margin:0;
    padding:20px;
}

.container{
    max-width:900px;
    margin:auto;
    background:white;
    border-radius:20px;
    padding:25px;
    box-shadow:0 0 15px rgba(0,0,0,.15);
    text-align:center;
}

h1{
    color:#0066cc;
}

#timer{
    font-size:36px;
    color:#ff4081;
    font-weight:bold;
}

#progress{
    margin-top:10px;
    font-size:20px;
}

#question{
    font-size:44px;
    margin:30px 0;
}

.choice{
    width:100%;
    font-size:28px;
    padding:18px;
    margin:10px 0;
    border:none;
    border-radius:12px;
    background:#d8ebff;
}

.choice:hover{
    background:#b9daff;
}

.correct{
    background:#90ee90 !important;
}

.wrong{
    background:#ffb3b3 !important;
}

#result{
    display:none;
}

.rank{
    font-size:60px;
    color:#ff6600;
    font-weight:bold;
}

/* 分数表示 */

.fraction{
    display:inline-block;
    vertical-align:middle;
    text-align:center;
    line-height:1;
}

.numerator{
    display:block;
    border-bottom:3px solid black;
    padding:0 8px 4px;
}

.denominator{
    display:block;
    padding-top:4px;
}

</style>
</head>
<body>

<div class="container">

<h1>分数タイムアタック</h1>

<div id="game">

<div id="timer">0.0 秒</div>

<div id="progress"></div>

<div id="question"></div>

<div id="choices"></div>

</div>

<div id="result">

<h2>結果発表</h2>

<p id="timeResult"></p>

<p id="scoreResult"></p>

<p id="bestResult"></p>

<div id="rankResult" class="rank"></div>

<button class="choice" onclick="restart()">
もう一度チャレンジ
</button>

</div>

</div>

<script>

const questionBank = [

["0.2","2/10"],
["0.3","3/10"],
["0.4","4/10"],
["0.5","5/10"],
["0.6","6/10"],
["0.7","7/10"],
["0.8","8/10"],
["0.9","9/10"],

["0.12","12/100"],
["0.14","14/100"],
["0.21","21/100"],
["0.25","25/100"],
["0.32","32/100"],
["0.35","35/100"],
["0.48","48/100"],
["0.64","64/100"],
["0.72","72/100"],
["0.84","84/100"],

["1","1/1"],
["2","2/1"],
["3","3/1"],
["4","4/1"],
["5","5/1"],

["1.2","12/10"],
["2.3","23/10"],
["3.4","34/10"],
["4.5","45/10"],
["5.6","56/10"],
["6.7","67/10"],
["7.06","706/100"]

];

let questions=[];
let current=0;
let correct=0;

let timerId;
let startTime;

function fractionHTML(str){

if(!str.includes("/")) return str;

const parts=str.split("/");

return `
<span class="fraction">
<span class="numerator">${parts[0]}</span>
<span class="denominator">${parts[1]}</span>
</span>
`;

}

function makeChoices(answer){

let choices=[answer];

const wrongPool=[
"1/10","2/10","3/10","5/10",
"12/10","25/10","84/10",
"1/100","2/100","14/100",
"5/1","7/1","10/1",
"100/1","120/100"
];

while(choices.length<4){

let w=wrongPool[
Math.floor(Math.random()*wrongPool.length)
];

if(!choices.includes(w)){
choices.push(w);
}
}

return choices.sort(()=>Math.random()-0.5);

}

function startGame(){

questions=[...questionBank]
.sort(()=>Math.random()-0.5);

current=0;
correct=0;

startTime=Date.now();

timerId=setInterval(()=>{

let t=(Date.now()-startTime)/1000;

document.getElementById("timer").innerHTML=
t.toFixed(1)+" 秒";

},100);

showQuestion();

}

function showQuestion(){

if(current>=questions.length){

finishGame();

return;

}

const q=questions[current];

document.getElementById("progress").innerHTML=
`${current+1} / ${questions.length} 問`;

document.getElementById("question").innerHTML=
`${q[0]} を分数で表すと？`;

const area=document.getElementById("choices");

area.innerHTML="";

const choices=makeChoices(q[1]);

choices.forEach(choice=>{

const btn=document.createElement("button");

btn.className="choice";

btn.innerHTML=fractionHTML(choice);

btn.onclick=()=>{

if(choice===q[1]){

correct++;

btn.classList.add("correct");

}else{

btn.classList.add("wrong");

}

setTimeout(()=>{

current++;

showQuestion();

},250);

};

area.appendChild(btn);

});

}

function finishGame(){

clearInterval(timerId);

const time=(Date.now()-startTime)/1000;

const percent=Math.round(
correct/questions.length*100
);

let rank="D";

if(percent===100 && time<40){

rank="S";

}else if(percent>=90){

rank="A";

}else if(percent>=80){

rank="B";

}else if(percent>=70){

rank="C";

}

let best=
localStorage.getItem("fractionBest");

if(best===null){

best=time.toFixed(1);

localStorage.setItem(
"fractionBest",
best
);

}
else{

if(time<Number(best)){

best=time.toFixed(1);

localStorage.setItem(
"fractionBest",
best
);

}

}

document.getElementById("game").style.display="none";
document.getElementById("result").style.display="block";

document.getElementById("timeResult").innerHTML=
`タイム：${time.toFixed(1)} 秒`;

document.getElementById("scoreResult").innerHTML=
`正答率：${percent}%（${correct}/${questions.length}）`;

document.getElementById("bestResult").innerHTML=
`自己ベスト：${best} 秒`;

document.getElementById("rankResult").innerHTML=
`ランク ${rank}`;

}

function restart(){

document.getElementById("result").style.display="none";
document.getElementById("game").style.display="block";

startGame();

}

startGame();

</script>

</body>
</html>
# -