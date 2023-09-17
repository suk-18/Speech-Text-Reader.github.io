# Speech-Text-Reader

## HTML

```

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Speech-Text-Reader</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <div class="container">
      <h1>Speech Text Reader</h1>
      <button id="toggle" class="btn btn-toggle">Toggle Text Box</button>
    </div>
    <div id="text-box" class="text-box">
      <div id="close" class="close"><i class="fas fa-times"></i></div>
      <h3>Choose Voice</h3>
      <select id="voices"></select>
      <textarea id="text" placeholder="Enter text to read..."></textarea>
      <button class="btn" id="read">Read Text</button>
    </div>
    <main></main>
    <script src="index.js"></script>
  </body>
</html>

```

## CSS

```

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background-color: #f4f4fb;
  font-family: "Open Sans Condensed", sans-serif;
  min-height: 100vh;
  margin: 0;
}

h1 {
  text-align: center;
  color: #272156;
}

.container {
  margin: auto;
  padding: 20px;
}

.btn {
  background-color: #6b6fa9;
  color: #f4f4fb;
  border: 0;
  border-radius: 1.5rem;
  font-size: 1rem;
  font-family: inherit;
  font-weight: bold;
  padding: 0.75rem 2rem;
  cursor: pointer;
}

.btn:hover {
  opacity: 0.9;
}

.btn:active {
  transform: scale(0.98);
}

.btn:focus,
select:focus {
  outline: none;
}

.btn-toggle {
  display: block;
  margin: auto;
  margin-bottom: 20px;
}

.text-box {
  background-color: #272156;
  color: #f4f4fb;
  width: 500px;
  max-width: 90vw;
  position: absolute;
  top: 30%;
  left: 50%;
  border-radius: 1.5rem;
  padding: 1.25rem;
  transform: translate(-50%, -800px);
  transition: all 1s ease-in-out;
}

.text-box.show {
  transform: translate(-50%, 0);
}

.text-box select {
  background-color: #6b6fa9;
  color: #f4f4fb;
  border-radius: 1.5rem;
  border: 0;
  font-size: 0.75rem;
  padding: 0.75rem 1rem;
  width: 100%;
}

.text-box textarea {
  border: 1px #e2ddea solid;
  border-radius: 1.5rem;
  font-size: 1rem;
  padding: 0.75rem 1rem;
  margin: 1rem 0;
  height: 150px;
  width: 100%;
}

.text-box .btn {
  width: 100%;
}

.text-box .close {
  float: right;
  text-align: right;
  cursor: pointer;
}

main {
  display: grid;
  grid-template-columns: 1fr;
  gap: 1rem;
  max-width: 90vw;
  margin: 0 auto;
  padding-bottom: 2rem;
}

.box {
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  border-radius: 1.5rem;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  cursor: pointer;
  transition: box-shadow 0.2s ease-out;
}

.box.active {
  box-shadow: 0 0 10px 5px #6b6fa9;
}

.box img {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.box .info {
  background-color: #6b6fa9;
  color: #f4f4fb;
  font-size: 1.125rem;
  letter-spacing: 1px;
  text-transform: uppercase;
  text-align: center;
  margin: 0;
  padding: 10px;
  height: 100%;
}

@media (min-width: 576px) {
  main {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (min-width: 768px) {
  main {
    grid-template-columns: repeat(3, 1fr);
  }
}

@media (min-width: 1200px) {
  main {
    grid-template-columns: repeat(4, 1fr);
  }
}

```

## JS

```

const main = document.querySelector("main");
const voicesSelect = document.getElementById("voices");
const textarea = document.getElementById("text");
const readButton = document.getElementById("read");
const toggleButton = document.getElementById("toggle");
const closeButton = document.getElementById("close");

const data = [
  {
    image: "drink",
    text: "I'm Thirsty",
  },
  {
    image: "food",
    text: "I'm Hungry",
  },
  {
    image: "tired",
    text: "I'm Tired",
  },
  {
    image: "hurt",
    text: "I'm Hurt",
  },
  {
    image: "happy",
    text: "I'm Happy",
  },
  {
    image: "angry",
    text: "I'm Angry",
  },
  {
    image: "sad",
    text: "I'm Sad",
  },
  {
    image: "scared",
    text: "I'm Scared",
  },
  {
    image: "outside",
    text: "I Want To Go Outside",
  },
  {
    image: "home",
    text: "I Want To Go Home",
  },
  {
    image: "school",
    text: "I Want To Go To School",
  },
  {
    image: "grandma",
    text: "I Want To Go To Grandmas",
  },
];

function createBox(item) {
  const box = document.createElement("div");
  const { image, text } = item;
  box.classList.add("box");
  box.innerHTML = `
    <img src='https://github.com/bradtraversy/vanillawebprojects/blob/master/speech-text-reader/img/${image}.jpg?raw=true' alt="${text}"/>
    <p class="info">${text}</p>
    `;
  box.addEventListener("click", () => handleSpeech(text, box));
  main.appendChild(box);
}

data.forEach(createBox);

let voices = [];

function getVoices() {
  voices = speechSynthesis.getVoices();
  voices.forEach((voice) => {
    const option = document.createElement("option");
    option.value = voice.name;
    option.innerText = `${voice.name} ${voice.lang}`;
    voicesSelect.appendChild(option);
  });
}

function handleSpeech(text, box) {
  setTextMessage(text);
  speakText();
  box.classList.add("active");
  setTimeout(() => box.classList.remove("active"), 800);
}

const message = new SpeechSynthesisUtterance();

function setTextMessage(text) {
  message.text = text;
}

function speakText() {
  speechSynthesis.speak(message);
}

function setVoice(e) {
  message.voice = voices.find((voice) => voice.name === e.target.value);
}

// Event Listeners
toggleButton.addEventListener("click", () => {
  document.getElementById("text-box").classList.toggle("show");
});
closeButton.addEventListener("click", () => {
  document.getElementById("text-box").classList.remove("show");
});
speechSynthesis.addEventListener("voiceschanged", getVoices);
voicesSelect.addEventListener("change", setVoice);
readButton.addEventListener("click", () => {
  setTextMessage(textarea.value);
  speakText();
});

getVoices();

```

**Hosted Link** :https://suk-18.github.io/Speech-Text-Reader.github.io/

