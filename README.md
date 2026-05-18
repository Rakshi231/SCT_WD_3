<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Interactive Quiz Game</title>

<style>
  body {
    font-family: Arial, sans-serif;
    background: #f4f6f9;
    margin: 0;
    padding: 20px;
    display: flex;
    justify-content: center;
  }

  .container {
    width: 600px;
    background: white;
    padding: 25px;
    border-radius: 12px;
    box-shadow: 0 5px 20px rgba(0,0,0,0.1);
  }

  h1 {
    text-align: center;
    color: #333;
  }

  .question {
    font-size: 20px;
    margin-bottom: 20px;
  }

  .options {
    margin-bottom: 20px;
  }

  label {
    display: block;
    margin: 10px 0;
    padding: 10px;
    background: #f0f0f0;
    border-radius: 8px;
    cursor: pointer;
    transition: 0.3s;
  }

  label:hover {
    background: #e0e0e0;
  }

  input[type="text"] {
    width: 100%;
    padding: 10px;
    font-size: 16px;
    margin-top: 10px;
  }

  .btns {
    display: flex;
    justify-content: space-between;
  }

  button {
    padding: 10px 20px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
  }

  .next {
    background: #007bff;
    color: white;
  }

  .prev {
    background: #6c757d;
    color: white;
  }

  .submit {
    background: #28a745;
    color: white;
  }

  .score {
    text-align: center;
    font-size: 24px;
    margin-top: 20px;
    color: #333;
  }
</style>
</head>

<body>

<div class="container">
  <h1>Quiz Game</h1>

  <div id="quiz"></div>

  <div class="btns">
    <button class="prev" onclick="prevQuestion()">Previous</button>
    <button class="next" onclick="nextQuestion()">Next</button>
  </div>

  <button class="submit" onclick="submitQuiz()" style="width:100%; margin-top:15px;">
    Submit Quiz
  </button>

  <div id="score" class="score"></div>
</div>

<script>
const questions = [
  {
    type: "single",
    question: "What is the capital of India?",
    options: ["Mumbai", "Delhi", "Kolkata", "Chennai"],
    answer: "Delhi"
  },
  {
    type: "multi",
    question: "Which are programming languages?",
    options: ["HTML", "Python", "CSS", "JavaScript"],
    answer: ["Python", "JavaScript"]
  },
  {
    type: "blank",
    question: "Fill in the blank: The largest planet is ____.",
    answer: "Jupiter"
  },
  {
    type: "single",
    question: "Which is a frontend framework?",
    options: ["Django", "React", "Flask", "Spring"],
    answer: "React"
  }
];

let current = 0;
let userAnswers = {};

const quizDiv = document.getElementById("quiz");

function loadQuestion() {
  const q = questions[current];
  let html = `<div class='question'>Q${current + 1}. ${q.question}</div><div class='options'>`;

  if (q.type === "single") {
    q.options.forEach(opt => {
      html += `
        <label>
          <input type="radio" name="option" value="${opt}"
          ${userAnswers[current] === opt ? "checked" : ""}>
          ${opt}
        </label>
      `;
    });
  }

  else if (q.type === "multi") {
    q.options.forEach(opt => {
      const selected = userAnswers[current] || [];
      html += `
        <label>
          <input type="checkbox" value="${opt}"
          ${selected.includes(opt) ? "checked" : ""}>
          ${opt}
        </label>
      `;
    });
  }

  else if (q.type === "blank") {
    html += `
      <input type="text" id="blankAnswer"
      value="${userAnswers[current] || ""}"
      placeholder="Type your answer here">
    `;
  }

  html += "</div>";
  quizDiv.innerHTML = html;
}

function saveAnswer() {
  const q = questions[current];

  if (q.type === "single") {
    const selected = document.querySelector("input[name='option']:checked");
    if (selected) userAnswers[current] = selected.value;
  }

  else if (q.type === "multi") {
    const selected = Array.from(document.querySelectorAll("input[type='checkbox']:checked"))
      .map(cb => cb.value);
    userAnswers[current] = selected;
  }

  else if (q.type === "blank") {
    const val = document.getElementById("blankAnswer").value;
    userAnswers[current] = val;
  }
}

function nextQuestion() {
  saveAnswer();
  if (current < questions.length - 1) {
    current++;
    loadQuestion();
  }
}

function prevQuestion() {
  saveAnswer();
  if (current > 0) {
    current--;
    loadQuestion();
  }
}

function submitQuiz() {
  saveAnswer();
  let score = 0;

  questions.forEach((q, i) => {
    const ans = userAnswers[i];

    if (q.type === "single") {
      if (ans === q.answer) score++;
    }

    else if (q.type === "multi") {
      if (JSON.stringify((ans || []).sort()) === JSON.stringify(q.answer.sort())) {
        score++;
      }
    }

    else if (q.type === "blank") {
      if ((ans || "").trim().toLowerCase() === q.answer.toLowerCase()) {
        score++;
      }
    }
  });

  document.getElementById("score").innerHTML =
    `🎉 Your Score: ${score} / ${questions.length}`;
}

// Initial load
loadQuestion();
</script>

</body>
</html>
