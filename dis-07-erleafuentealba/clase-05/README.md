# clase-05/

```javascript
function setup() {
  createCanvas(400, 400);
  //frameRate (1)
}

function draw() {
  background(0);
  
  if (mouseIsPressed ) { 
    noStroke ();
    ellipse (
    random (width),
    random (height),
    random (50),
    random(50)
  );

  } else {
    fill (200);
    rect (0, 0, width, height);
  }
 
  
//if (frameCount % 2 == 1) {
//ellipse (width/2, height/2, 50, 50, 50); 
//} else {
// rect(width/2, height/2, 50, 50);
// }
  
}



Ejercicio en Clases: la idea es que haya un circulo en el centro del canvas, y al apresionar con mouse, el fondo cambia de color (uno esperaría que cambie el circulo, pero cambia el fondo)/
(No funciona :'C)/
```javascript
let rojo = [];
let verde = [];
let azul = [];

function setup() {
  createCanvas(500, 500);

  background(0);

  circle(250, 250, 100);
  
  if (mouseIsPressed) {
    background (
     rojo.push(random (0, 255)),
     verde.push(random (0, 255)),
     azul.push(random(0, 255))
    )
    
  }
}

Codigo del Profe
```javascript
/*
 * 👋 Hello! This is an ml5.js example made and shared with ❤️.
 * Learn more about the ml5.js project: https://ml5js.org/
 * ml5.js license and Code of Conduct: https://github.com/ml5js/ml5-next-gen/blob/main/LICENSE.md
 *
 * This example demonstrates Sound classification using SpeechCommands18w
 */

let imageYes;
let imageNo;

let boolYes = false;
let boolNo = false;


// Initialize a sound classifier method with SpeechCommands18w model. A callback needs to be passed.
let classifier;

// Array containing the 18 words of SpeechCommands18w
let words = [
  "zero",
  "one",
  "two",
  "three",
  "four",
  "five",
  "six",
  "seven",
  "eight",
  "nine",
  "up",
  "down",
  "left",
  "right",
  "go",
  "stop",
  "yes",
  "no",
];

// Variable for displaying the results on the canvas
let predictedWord = "";

function preload() {
  // Options for the SpeechCommands18w model, the default probabilityThreshold is 0
  let options = { probabilityThreshold: 0.7 };
  // Load SpeechCommands18w sound classifier model
  classifier = ml5.soundClassifier("SpeechCommands18w", options);
  
  imageYes = loadImage("yes.jpg");
  imageNo = loadImage("no.jpg");
  
}

function setup() {
  createCanvas(650, 450);
  // Classify the sound from microphone in real time
  classifier.classifyStart(gotResult);
}

function draw() {
  // background(250);
  // Call function for displaying background words
  // displayWords();
  displayDon();

  // Once the model outputs results start displaying the predicted word on the canvas
  if (predictedWord !== "") {
    fill(211, 107, 255);
    textAlign(CENTER, CENTER);
    textSize(64);
    text(predictedWord, width / 2, 90);
  }
}

// Function to display the 18 words on the canvas
function displayWords() {
  textAlign(CENTER, CENTER);
  textSize(32);
  fill(96);
  text("Say one of these words!", width / 2, 40);

  let x = 125;
  let y = 150;
  // Words appear in 3 columns of 6 rows
  for (let i = 0; i < words.length; i++) {
    fill(158);
    text(words[i], x, y);
    y += 50;
    if ((i + 1) % 6 === 0) {
      x += 200;
      y = 150;
    }
  }
}

function displayDon() {
  if (boolYes) {
        image(imageYes, 0, 0, width, height);
  }
  if (boolNo) {
        image(imageNo, 0, 0, width, height);
    
  }

}

// A function to run when we get any errors and the results
function gotResult(results) {
  // The results are in an array ordered by confidence
  console.log(results);
  // Load the first label to the text variable displayed on the canvas
  predictedWord = results[0].label;
  
  // console.log(predictedWord);
  
  if (predictedWord == "yes") {
    
    
  boolYes = true;
  boolNo = false;


  }
  if (predictedWord == "no") {
    
  boolYes = false;
  boolNo = true;

  }
  
}


















