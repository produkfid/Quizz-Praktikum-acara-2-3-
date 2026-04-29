<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Quiz Elektronika</title>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js"></script>
</head>

<body>

<h2>Form Peserta</h2>
<input type="text" id="nama" placeholder="Nama"><br><br>
<input type="text" id="nim" placeholder="NIM"><br><br>
<input type="text" id="kelas" placeholder="Kelas"><br><br>
<button onclick="mulaiQuiz()">Mulai Quiz</button>

<div id="quiz" style="display:none;">
  <h3 id="question"></h3>
  <div id="options"></div>
  <button onclick="nextQuestion()">Next</button>
</div>

<h3 id="result"></h3>

<script>
const firebaseConfig = {
  apiKey: "API_KEY_KAMU",
  authDomain: "PROJECT.firebaseapp.com",
  projectId: "PROJECT_ID",
};

const app = firebase.initializeApp(firebaseConfig);
const db = firebase.firestore();

let peserta = {};
let currentQuestion = 0;
let score = 0;

const quizData = [
  {
    question: "Komponen penghambat arus?",
    options: ["Resistor", "Transistor", "Dioda", "IC"],
    answer: 0
  },
  {
    question: "Arus DC adalah?",
    options: ["Bolak balik", "Satu arah", "Berubah", "PLN"],
    answer: 1
  }
];

function mulaiQuiz() {
  peserta.nama = document.getElementById("nama").value;
  peserta.nim = document.getElementById("nim").value;
  peserta.kelas = document.getElementById("kelas").value;

  document.getElementById("quiz").style.display = "block";
  loadQuestion();
}

function loadQuestion() {
  let q = quizData[currentQuestion];
  document.getElementById("question").innerText = q.question;

  let optionsHTML = "";
  q.options.forEach((opt, i) => {
    optionsHTML += `<button onclick="pilih(${i})">${opt}</button><br>`;
  });

  document.getElementById("options").innerHTML = optionsHTML;
}

function pilih(i) {
  if (i === quizData[currentQuestion].answer) {
    score++;
  }
}

function nextQuestion() {
  currentQuestion++;
  if (currentQuestion < quizData.length) {
    loadQuestion();
  } else {
    selesai();
  }
}

function selesai() {
  let nilai = (score / quizData.length) * 100;

  document.getElementById("quiz").innerHTML = "";
  document.getElementById("result").innerText =
    `Skor: ${score} | Nilai: ${nilai}`;

  // SIMPAN KE FIREBASE
  db.collection("hasil_quiz").add({
    nama: peserta.nama,
    nim: peserta.nim,
    kelas: peserta.kelas,
    skor: score,
    nilai: nilai,
    waktu: new Date()
  });
}
</script>

</body>
</html>
