# Coding
<!DOCTYPE html>
<html>
<head>
<title>Ultimate Fun Quiz</title>
<style>
body{
font-family:Arial;
display:flex;
justify-content:center;
align-items:center;
height:100vh;
margin:0;
transition:0.5s;
}

/* 🌈 START PAGE BACKGROUND */
.start-bg{
background:linear-gradient(135deg,#ff9a9e,#fad0c4);
}

.quiz-box{
background:#fff;
width:420px;
padding:25px;
border-radius:15px;
box-shadow:0 10px 25px rgba(0,0,0,0.3);
text-align:center;
}

input,select{
width:85%;
padding:8px;
margin:8px 0;
}

.btn{
width:100%;
padding:10px;
margin:8px 0;
border:none;
border-radius:8px;
color:white;
cursor:pointer;
transition:0.3s;
}

.btn:hover{
transform:scale(1.05);
box-shadow:0 5px 12px rgba(0,0,0,0.3);
}

.progress-bar{
width:100%;
height:8px;
background:#ddd;
border-radius:10px;
overflow:hidden;
margin-bottom:10px;
}

.progress{
height:100%;
width:0%;
background:#4caf50;
transition:0.3s;
}

.correct{background:green!important;}
.wrong{background:red!important;}

#leaderboard{
text-align:left;
margin-top:10px;
font-size:14px;
}

#timer{
font-weight:bold;
margin-bottom:8px;
}

#timeup-msg{
color:red;
font-weight:bold;
display:none;
margin-bottom:5px;
}
</style>
</head>

<body id="body" class="start-bg">

<div class="quiz-box">

<h2>Fun Quiz 🎉</h2>

<!-- START -->
<div id="start">
<input type="text" id="name" placeholder="Enter your name"><br>
<select id="category">
<option value="">Select Category</option>
<option value="gk">General Knowledge</option>
<option value="math">Maths</option>
<option value="coding">Coding</option>
</select>
<button class="btn" style="background:#ff9800;" onclick="startQuiz()">Start Quiz</button>
</div>

<!-- QUIZ -->
<div id="quiz" style="display:none;">
<div class="progress-bar"><div class="progress" id="progress"></div></div>
<div id="timer">Time: 10</div>
<div id="timeup-msg"></div>
<div id="question"></div>
<div id="answers"></div>
<button id="next" class="btn" style="display:none;">Next</button>
</div>

<!-- RESULT -->
<div id="result" style="display:none;">
<h3 id="scoreText"></h3>
<div id="leaderboard"></div>
<button class="btn" style="background:#ff5722;" onclick="restart()">Play Again</button>
</div>

</div>

<!-- SOUND -->
<audio id="correctSound" src="https://www.soundjay.com/buttons/sounds/button-4.mp3"></audio>
<audio id="wrongSound" src="https://www.soundjay.com/buttons/sounds/button-10.mp3"></audio>

<script>
const questionBank={
gk:[
{q:"Capital of Japan?",a:[{t:"Tokyo",c:true},{t:"Seoul",c:false},{t:"Beijing",c:false},{t:"Bangkok",c:false}]},
{q:"Largest ocean?",a:[{t:"Pacific",c:true},{t:"Indian",c:false},{t:"Atlantic",c:false},{t:"Arctic",c:false}]},
{q:"National bird of India?",a:[{t:"Peacock",c:true},{t:"Eagle",c:false},{t:"Parrot",c:false},{t:"Crow",c:false}]},
{q:"How many continents?",a:[{t:"7",c:true},{t:"5",c:false},{t:"6",c:false},{t:"8",c:false}]},
{q:"Which gas we breathe?",a:[{t:"Oxygen",c:true},{t:"CO2",c:false},{t:"Hydrogen",c:false},{t:"Nitrogen",c:false}]}
],
math:[
{q:"12 × 8 = ?",a:[{t:"96",c:true},{t:"88",c:false},{t:"108",c:false},{t:"86",c:false}]},
{q:"√81 = ?",a:[{t:"9",c:true},{t:"8",c:false},{t:"7",c:false},{t:"6",c:false}]},
{q:"5² = ?",a:[{t:"25",c:true},{t:"20",c:false},{t:"15",c:false},{t:"30",c:false}]},
{q:"100 ÷ 4 = ?",a:[{t:"25",c:true},{t:"20",c:false},{t:"30",c:false},{t:"40",c:false}]},
{q:"15 + 27 = ?",a:[{t:"42",c:true},{t:"38",c:false},{t:"40",c:false},{t:"45",c:false}]}
],
coding:[
{q:"HTML stands for?",a:[{t:"Hyper Text Markup Language",c:true},{t:"HighText Machine",c:false},{t:"Hyperlinks",c:false},{t:"None",c:false}]},
{q:"CSS is used for?",a:[{t:"Styling",c:true},{t:"Logic",c:false},{t:"Database",c:false},{t:"Structure",c:false}]},
{q:"JS means?",a:[{t:"JavaScript",c:true},{t:"JavaSource",c:false},{t:"JustStyle",c:false},{t:"None",c:false}]},
{q:"Backend language?",a:[{t:"Python",c:true},{t:"HTML",c:false},{t:"CSS",c:false},{t:"Photoshop",c:false}]},
{q:"Symbol for ID in CSS?",a:[{t:"#",c:true},{t:".",c:false},{t:"*",c:false},{t:"@",c:false}]}
]
};

let questions=[],index=0,score=0,timer,seconds=10;
let player="",category="";
const body=document.getElementById("body");
const progress=document.getElementById("progress");
const nextBtn=document.getElementById("next");

function shuffle(arr){return arr.sort(()=>Math.random()-0.5);}

function startQuiz(){
player=document.getElementById("name").value.trim();
category=document.getElementById("category").value;
if(player===""||category===""){alert("Enter name & category");return;}

questions=shuffle([...questionBank[category]]).slice(0,5);
index=0;score=0;

if(category==="gk"){body.style.background="linear-gradient(135deg,#8e2de2,#4a00e0)";nextBtn.style.background="#ff4db8";}
if(category==="math"){body.style.background="linear-gradient(135deg,#11998e,#38ef7d)";nextBtn.style.background="#00c853";}
if(category==="coding"){body.style.background="linear-gradient(135deg,#232526,#414345)";nextBtn.style.background="#607d8b";}

document.getElementById("start").style.display="none";
document.getElementById("quiz").style.display="block";

showQuestion();
}

function showQuestion(){
reset();
progress.style.width=(index/questions.length)*100+"%";

let q=questions[index];
document.getElementById("question").innerText=q.q;

shuffle([...q.a]).forEach(ans=>{
let btn=document.createElement("button");
btn.innerText=ans.t;
btn.className="btn";
btn.style.background="#444";
if(ans.c)btn.dataset.correct=true;
btn.onclick=selectAnswer;
document.getElementById("answers").appendChild(btn);
});

startTimer();
}

function reset(){
clearInterval(timer);
seconds=10;
document.getElementById("timer").innerText="Time: 10";
document.getElementById("answers").innerHTML="";
document.getElementById("timeup-msg").style.display="none";
nextBtn.style.display="none";
}

function startTimer(){
timer=setInterval(()=>{
seconds--;
document.getElementById("timer").innerText="Time: "+seconds;
if(seconds<=0){clearInterval(timer);handleTimeUp();}
},1000);
}

function handleTimeUp(){
let msg=document.getElementById("timeup-msg");
msg.innerText="⏰ Time’s Up! You lost this question!";
msg.style.display="block";

Array.from(document.getElementById("answers").children).forEach(b=>{
if(b.dataset.correct==="true")b.classList.add("correct");
b.disabled=true;
});

setTimeout(()=>{nextBtn.click();},2000);
}

function selectAnswer(e){
clearInterval(timer);
if(e.target.dataset.correct==="true"){
score++;
document.getElementById("correctSound").play();
e.target.classList.add("correct");
}else{
document.getElementById("wrongSound").play();
e.target.classList.add("wrong");
}
Array.from(document.getElementById("answers").children).forEach(b=>{
if(b.dataset.correct==="true")b.classList.add("correct");
b.disabled=true;
});
nextBtn.style.display="block";
}

nextBtn.onclick=()=>{
index++;
if(index<questions.length){showQuestion();}
else{showResult();}
};

function showResult(){
document.getElementById("quiz").style.display="none";
document.getElementById("result").style.display="block";
document.getElementById("scoreText").innerText=
`${player}, ${category.toUpperCase()} Score: ${score}/5`;
saveScore();
showLeaderboard();
}

function saveScore(){
let key="leaderboard_"+category;
let data=JSON.parse(localStorage.getItem(key))||[];
data.push({name:player,score:score});
data.sort((a,b)=>b.score-a.score);
data=data.slice(0,5);
localStorage.setItem(key,JSON.stringify(data));
}

function showLeaderboard(){
let key="leaderboard_"+category;
let data=JSON.parse(localStorage.getItem(key))||[];
let html="<h4>🏆 "+category.toUpperCase()+" Leaderboard</h4>";
data.forEach((d,i)=>{html+=`${i+1}. ${d.name} - ${d.score}<br>`});
document.getElementById("leaderboard").innerHTML=html;
}

function restart(){
location.reload();
}
</script>

</body>
</html>

# Website
<img width="1358" height="713" alt="image" src="https://github.com/user-attachments/assets/42c75753-b5aa-4973-ab52-2a8c252b0eca" />
<img width="1097" height="521" alt="image" src="https://github.com/user-attachments/assets/2e625942-9d22-4490-8ea1-102d9532b105" />



# Output
https://rajeshwari69514-boop.github.io/quiz/
