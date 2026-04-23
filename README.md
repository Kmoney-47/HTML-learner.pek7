# HTML-learner.pek7
A HTML learner website for people to learn HTML 
<!DOCTYPE html><html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>HTML Mastery Ultra++</title>
<script src="https://cdn.tailwindcss.com"></script>
</head><body class="bg-slate-900 text-white flex"><!-- Sidebar --><div class="w-64 bg-slate-800 h-screen p-4">
  <h1 class="text-2xl font-bold text-sky-400 mb-6">HTML Ultra++</h1>
  <button onclick="show('lesson')" class="btn">Lessons</button>
  <button onclick="show('editor')" class="btn">Practice</button>
  <button onclick="show('quiz')" class="btn">Quiz</button>
  <button onclick="show('challenge')" class="btn">Challenges</button>
  <button onclick="show('dashboard')" class="btn">Dashboard</button>
</div><!-- Main --><div class="flex-1 p-6"><!-- LESSONS --><div id="lesson">
  <h2 class="text-xl mb-3">Lessons</h2>
  <div id="lessonList"></div>
</div><!-- EDITOR --><div id="editor" class="hidden">
  <h2 class="text-xl mb-2">Live Editor</h2>
  <textarea id="code" class="w-full h-40 bg-black p-2">&lt;h1&gt;Hello&lt;/h1&gt;</textarea>
  <button onclick="run()" class="btn bg-blue-500">Run</button>
  <iframe id="out" class="w-full h-40 bg-white mt-2"></iframe>
</div><!-- QUIZ --><div id="quiz" class="hidden">
  <h2 class="text-xl">Quiz</h2>
  <div id="quizBox"></div>
</div><!-- CHALLENGE --><div id="challenge" class="hidden">
  <h2 class="text-xl">Challenge</h2>
  <p id="challengeText"></p>
  <textarea id="challengeCode" class="w-full h-32 bg-black p-2"></textarea>
  <button onclick="checkChallenge()" class="btn bg-purple-500">Submit</button>
  <p id="feedback" class="mt-2"></p>
</div><!-- DASHBOARD --><div id="dashboard" class="hidden">
  <h2 class="text-xl mb-2">Your Stats</h2>
  <p>Total XP: <span id="dXp"></span></p>
  <p>Level: <span id="dLevel"></span></p>
  <p>Streak: <span id="dStreak"></span> days</p>
  <p>Completed Lessons: <span id="dLessons"></span></p>
</div><!-- PROGRESS --><div class="mt-6">
  <h3>Level: <span id="level">1</span></h3>
  <h4>🔥 Streak: <span id="streak">0</span> days</h4>
  <div class="w-full bg-slate-700 h-3 rounded">
    <div id="bar" class="bg-green-500 h-3 rounded" style="width:0%"></div>
  </div>
  <p id="xpText">XP: 0</p>
  <p id="badge" class="mt-2 text-yellow-400"></p>
</div></div><style>
.btn{display:block;width:100%;margin-bottom:8px;padding:8px;background:#334155;border-radius:6px}
</style><script>
let xp = parseInt(localStorage.getItem('xp')) || 0;
let streak = parseInt(localStorage.getItem('streak')) || 0;
let lessonsDone = parseInt(localStorage.getItem('lessonsDone')) || 0;
let lastVisit = localStorage.getItem('lastVisit');
let today = new Date().toDateString();

if(lastVisit !== today){
  streak++;
  localStorage.setItem('streak', streak);
  localStorage.setItem('lastVisit', today);
}

let level = Math.floor(xp/100)+1;

const lessons = [
 {title:'Intro to HTML', xp:20},
 {title:'Tags & Elements', xp:25},
 {title:'Forms', xp:30},
 {title:'Tables', xp:30}
];

const quizzes = [
 {q:'HTML stands for?', a:'HyperText Markup Language'},
 {q:'Tag for link?', a:'<a>'}
];

const challenges = [
 {q:'Create a button with text "Click"', check:(doc)=> doc.querySelector('button')?.innerText.includes('Click')},
 {q:'Create a link to google.com', check:(doc)=> doc.querySelector('a')?.href.includes('google')}
];

function show(id){
 ['lesson','editor','quiz','challenge','dashboard'].forEach(s=>{
  document.getElementById(s).classList.add('hidden');
 });
 document.getElementById(id).classList.remove('hidden');
 updateDashboard();
}

function renderLessons(){
 let html='';
 lessons.forEach(l=>{
  html+=`<div class='bg-slate-800 p-3 mb-2 rounded'>${l.title} <button onclick="completeLesson(${l.xp})" class='ml-2 bg-green-500 px-2'>Done</button></div>`;
 });
 document.getElementById('lessonList').innerHTML=html;
}

function completeLesson(val){
 xp+=val;
 lessonsDone++;
 localStorage.setItem('lessonsDone', lessonsDone);
 save();
}

function renderQuiz(){
 let q=quizzes[Math.floor(Math.random()*quizzes.length)];
 document.getElementById('quizBox').innerHTML=`${q.q}<br><input id='ans' class='text-black p-1 mt-2'><br><button onclick="checkQuiz('${q.a}')" class='btn mt-2'>Submit</button>`;
}

function renderChallenge(){
 let c=challenges[Math.floor(Math.random()*challenges.length)];
 window.currentChallenge=c;
 document.getElementById('challengeText').innerText=c.q;
 document.getElementById('feedback').innerText='';
}

function run(){
 document.getElementById('out').srcdoc=document.getElementById('code').value;
}

function checkQuiz(ans){
 let val=document.getElementById('ans').value;
 if(val.includes(ans)){ alert('Correct'); xp+=30; }
 else alert('Wrong');
 save(); renderQuiz();
}

// SMART VALIDATION
function checkChallenge(){
 let code=document.getElementById('challengeCode').value;
 let parser = new DOMParser();
 let doc = parser.parseFromString(code, 'text/html');

 if(currentChallenge.check(doc)){
  document.getElementById('feedback').innerText='✅ Correct';
  xp+=50;
 } else {
  document.getElementById('feedback').innerText='❌ Try again';
 }
 save(); renderChallenge();
}

function save(){
 localStorage.setItem('xp', xp);
 level=Math.floor(xp/100)+1;
 updateUI();
}

function getBadge(){
 if(xp < 100) return 'Beginner 🟢';
 if(xp < 300) return 'Intermediate 🔵';
 if(xp < 600) return 'Advanced 🟣';
 return 'HTML Master 🔥';
}

function updateUI(){
 document.getElementById('bar').style.width=(xp%100)+'%';
 document.getElementById('xpText').innerText='XP: '+xp;
 document.getElementById('level').innerText=level;
 document.getElementById('streak').innerText=streak;
 document.getElementById('badge').innerText='Badge: ' + getBadge();
}

function updateDashboard(){
 document.getElementById('dXp').innerText=xp;
 document.getElementById('dLevel').innerText=level;
 document.getElementById('dStreak').innerText=streak;
 document.getElementById('dLessons').innerText=lessonsDone;
}

renderLessons();
renderQuiz();
renderChallenge();
updateUI();
</script></body>
</html>
