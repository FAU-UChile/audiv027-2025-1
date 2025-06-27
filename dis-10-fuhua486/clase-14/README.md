# clase-14
viernes 27 junio 2025

# DetectSeñas

integrantes:

* Fuhua Huang <LINK [A GITHUB](https://github.com/fuhua486) >
* Ignacio Castro C. <LINK [A GITHUB](https://github.com/nachofau)>
* Patricio Merino R. <LINK [A GITHUB](https://github.com/PatoMerino)>

```md
mi equipo de trabajo es <https://github.com/PatoMerino> y <https://github.com/nachofau>, entregamos en el repositorio en este enlace [https://github.com/fuhua486]
```

## Acerca del proyecto

El proyecto DetectSeña surgió de la idea de TeachSeñas. Su propósito es permitir que personas, con o sin conocimientos de Lengua de Señas Chilena, la comprendan y utilicen mediante el método interactivo del proyecto, combinando código y entrenamiento con modelos.
A diferencia del anterior, DetectSeña se centra en detectar las letras de la Lengua de Señas Chilena, es decir, las letras que representa cada gesto. Los usuarios pueden usar la cámara para realizar gestos en Lengua de Señas Chilena frente a la pantalla, y esta les indicará qué letra representa el gesto en un cuadro de texto en la esquina superior izquierda, según el gesto.

![image](https://github.com/user-attachments/assets/3182edfc-9db0-4532-815c-3d5124f30c48)


Las herramientas que utilizamos son: **ml5.js（Modelo NeuralNetwork Train and Save)** y **p5.js Web Editor**.

![image](https://github.com/user-attachments/assets/04a271ca-d325-4e9b-a09c-df27a66e0261)
![image](https://github.com/user-attachments/assets/0cc6c770-3580-4f7e-9302-97539654adb2)

## Procesos

Nuestro proyecto se desarrolla a partir de entrenamiento de modelo, en este caso es el modelo **NeuralNetwork Train and Save** de ml5, que sirve para entrena y guardar datos para ser usados más adelante.

#### Código base

Este código sirve para entrenar y detectar el gesto de cachipún: Piedra, papel y tijera

```javascript
let classifier;
let handPose;
let video;
let hands = [];
let rockDataCount = 0;
let paperDataCount = 0;
let scissorsDataCount = 0;
let isTrained = false;
let classification = "";

//UI elements
let instructionP;
let dataCountsP;
let rockButton;
let paperButton;
let scissorsButton;
let trainButton;

function preload() {
  // Load the handPose model, we will use the keypoints form handPose to train the neural network
  handPose = ml5.handPose();
}

function setup() {
  createCanvas(640, 480);

  // Create the webcam video and hide it
  video = createCapture(VIDEO);
  video.size(640, 480);
  video.hide();

  // Setup the UI buttons for training
  instructionP = createP(
    'To add data, hold up hand gesture for rock while pressing the "Add Rock Data" button. Likewise for paper and scissors. When you have a good amount of data for each gesture class, press the "Train and Save Model" button to train the model and save it for future use.'
  );
  instructionP.style("width", "640px");
  dataCountsP = createP(
    "Rock data: " +
      rockDataCount +
      "<br>Paper data: " +
      paperDataCount +
      "<br>Scissors data: " +
      scissorsDataCount
  );
  rockButton = createButton("Add Rock Data");
  rockButton.mousePressed(addRockData);
  paperButton = createButton("Add Paper Data");
  paperButton.mousePressed(addPaperData);
  scissorsButton = createButton("Add Scissors Data");
  scissorsButton.mousePressed(addScissorsData);
  trainButton = createButton("Train and Save Model");
  trainButton.mousePressed(train);

  // For this example to work across all browsers
  // "webgl" or "cpu" needs to be set as the backend
  ml5.setBackend("webgl");

  // Set up the neural network
  let classifierOptions = {
    task: "classification",
    debug: true,
  };
  classifier = ml5.neuralNetwork(classifierOptions);

  // Start the handPose detection
  handPose.detectStart(video, gotHands);
}

function draw() {
  // Display the webcam video
  image(video, 0, 0, width, height);

  // Draw the handPose keypoints
  if (hands[0]) {
    let hand = hands[0];
    for (let i = 0; i < hand.keypoints.length; i++) {
      let keypoint = hand.keypoints[i];
      fill(0, 255, 0);
      noStroke();
      circle(keypoint.x, keypoint.y, 10);
    }
  }

  // If the model is trained, make a classification and display the result
  if (isTrained && hands[0]) {
    let inputData = flattenHandData();
    classifier.classify(inputData, gotClassification);
    textSize(64);
    fill(0, 255, 0);
    text(classification, 20, 60);
  }
}

// Callback function for when handPose outputs data
function gotHands(results) {
  hands = results;
}

// Callback function for when the classifier makes a classification
function gotClassification(results) {
  classification = results[0].label;
}

// convert the handPose data to a 1D array
function flattenHandData() {
  let hand = hands[0];
  let handData = [];
  for (let i = 0; i < hand.keypoints.length; i++) {
    let keypoint = hand.keypoints[i];
    handData.push(keypoint.x);
    handData.push(keypoint.y);
  }
  return handData;
}

// Add the current handPose data to the classifier as "Rock"
function addRockData() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["Rock"];
    classifier.addData(inputData, outputData);
    rockDataCount++;
  }
  updateDataCountUI();
}

// Add the current handPose data to the classifier as "Paper"
function addPaperData() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["Paper"];
    classifier.addData(inputData, outputData);
    paperDataCount++;
  }
  updateDataCountUI();
}

// Add the current handPose data to the classifier as "Scissors"
function addScissorsData() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["Scissors"];
    classifier.addData(inputData, outputData);
    scissorsDataCount++;
  }
  updateDataCountUI();
}

// Update the HTML UI with the current data counts
function updateDataCountUI() {
  dataCountsP.html(
    "Rock data: " +
      rockDataCount +
      "<br>Paper data: " +
      paperDataCount +
      "<br>Scissors data: " +
      scissorsDataCount
  );
}

// Train the data when 'Train abd Save Model' button is pressed
function train() {
  // The data should be normalized before training
  classifier.normalizeData();

  // Train the model
  let trainingOptions = {
    epochs: 50,
  };
  classifier.train(trainingOptions, finishedTraining);
}

// When the model is trained, save the model
function finishedTraining() {
  console.log("Training finished!");
  classifier.save();
  isTrained = true;
}

```

**Nuestro primer paso fue cambiar las variables（de piedra, papel y tijera a Letra A, Letra B y Letra C)**

```Javascript

let classifier;
let handPose;
let video;
let hands = [];
// agregar variable para conexiones esqueléticas de la mano
let connections;
//cambiar variables de Piedra,Papel y Tijera a Letra A, Letra B y Letra C
let ADataCount = 0;
let BDataCount = 0;
let CDataCount = 0;
let isTrained = false;
let classification = "";

//UI elements
let instructionP;
let dataCountsP;
let AButton;
let BButton;
let CButton;
let trainButton;

```
**Aquí cambiamos los nombres de botones**
```Javascript

  dataCountsP = createP(
    "A data: " +
      ADataCount +
      "<br>B data: " +
      BDataCount +
      "<br>C data: " +
      CDataCount
  );
  AButton = createButton("Agregar Datos de A");
  AButton.mousePressed(AgregarDatosdeA);
  BButton = createButton("Agregar Datos de B");
  BButton.mousePressed(AgregarDatosdeB);
  CButton = createButton("Agregar Datos de C");
  CButton.mousePressed(AgregarDatosdeC);
  trainButton = createButton("Entrenar y guardar los datos");
  trainButton.mousePressed(train);
```
![image](https://github.com/user-attachments/assets/422c82c3-9973-4cde-9475-805d975cbdc1)



**Aquí hemos agregado parte de código del modelo HandPose Skeletal Connections para detectar los puntos clave de la mano y las líneas de conexión entre ellos para un mejor reconocimiento de gestos, también cambiamos el color y el tamaño de las líneas y puntos.**

```Javascript
function draw() {
  // Display the webcam video
  image(video, 0, 0, width, height);

  // Detectar puntos clave de la mano y líneas de conexión entre ellos
  for (let i = 0; i < hands.length; i++) {
    let hand = hands[i];
    for (let j = 0; j < connections.length; j++) {
      let pointAIndex = connections[j][0];
      let pointBIndex = connections[j][1];
      let pointA = hand.keypoints[pointAIndex];
      let pointB = hand.keypoints[pointBIndex];
      stroke(255, 102, 0);
      strokeWeight(3);
      line(pointA.x, pointA.y, pointB.x, pointB.y);
    }
  }

  // Draw all the tracked hand points
  for (let i = 0; i < hands.length; i++) {
    let hand = hands[i];
    for (let j = 0; j < hand.keypoints.length; j++) {
      let keypoint = hand.keypoints[j];
      fill(0, 255, 255);
      noStroke();
      circle(keypoint.x, keypoint.y, 8);
    }
  }
}
```
![image](https://github.com/user-attachments/assets/a417c8bf-4024-4ae2-9bc6-8c57007ef77d)



**Cambiamos el nombre del gesto de piedra-papel-tijera a Letra A, Letra B, Letra C**
```Javascript
//Recopilar datos de entrenamiento de diferentes categorías de gestos y agregarlos al clasificador como Letra A, Letra B y Letra C
function AgregarDatosdeA() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["A"];
    classifier.addData(inputData, outputData);
    LetraADataCount++;
  }
  updateDataCountUI();
}

function AgregarDatosdeB() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["B"];
    classifier.addData(inputData, outputData);
    LetraBDataCount++;
  }
  updateDataCountUI();
}

function AgregarDatosdeC() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["C"];
    classifier.addData(inputData, outputData);
    LetraCDataCount++;
  }
  updateDataCountUI();
}

// Update the HTML UI with the current data counts
function updateDataCountUI() {
  dataCountsP.html(
    "A data: " +
      ADataCount +
      "<br>B data: " +
      BDataCount +
      "<br>C data: " +
      CDataCount
  );
}

```

### Código final de modelo

```Javascript
let classifier;
let handPose;
let video;
let hands = [];
// agregar variable para conexiones esqueléticas de la mano
let connections;
//cambiar variables de Piedra,Papel y Tijera a Letra A, Letra B y Letra C
let ADataCount = 0;
let BDataCount = 0;
let CDataCount = 0;
let DDataCount = 0;
let EDataCount = 0;
let isTrained = false;
let classification = "";

//UI elements
let instructionP;
let dataCountsP;
let AButton;
let BButton;
let CButton;
let DButton;
let EButton;
let trainButton;

function preload() {
  // Load the handPose model, we will use the keypoints form handPose to train the neural network
  handPose = ml5.handPose();
}

function setup() {
  createCanvas(640, 480);

  // Create the webcam video and hide it
  video = createCapture(VIDEO);
  video.size(640, 480);
  video.hide();
  

  // Aquí remplazamos los nombres de botones

dataCountsP = createP(
    "A data: " +
    ADataCount +
    "<br>B data: " +
    BDataCount +
    "<br>C data: " +
    CDataCount +
    "<br>D data: " +
    DDataCount +
    "<br>E data: " +
    EDataCount
);
  
  AButton = createButton("Agregar Datos de A");
  AButton.mousePressed(AgregarDatosdeA);
  
  BButton = createButton("Agregar Datos de B");
  BButton.mousePressed(AgregarDatosdeB);
  
  CButton = createButton("Agregar Datos de C");
  CButton.mousePressed(AgregarDatosdeC);
  
  DButton = createButton("Agregar Datos de D");
  DButton.mousePressed(AgregarDatosdeD);
  
  EButton = createButton("Agregar Datos de E");
  EButton.mousePressed(AgregarDatosdeE);
  
  trainButton = createButton("Entrenar y guardar los datos");
  trainButton.mousePressed(train);

  // For this example to work across all browsers
  // "webgl" or "cpu" needs to be set as the backend
  ml5.setBackend("webgl");

  // Set up the neural network
  let classifierOptions = {
    task: "classification",
    debug: true,
  };
  classifier = ml5.neuralNetwork(classifierOptions);

  // Start the handPose detection
  handPose.detectStart(video, gotHands);
  
  connections = handPose.getConnections();
}

function draw() {
  // Display the webcam video
  image(video, 0, 0, width, height);

  // Detectar puntos clave de la mano y líneas de conexión entre ellos
  for (let i = 0; i < hands.length; i++) {
    let hand = hands[i];
    for (let j = 0; j < connections.length; j++) {
      let pointAIndex = connections[j][0];
      let pointBIndex = connections[j][1];
      let pointA = hand.keypoints[pointAIndex];
      let pointB = hand.keypoints[pointBIndex];
      stroke(255, 102, 0);
      strokeWeight(3);
      line(pointA.x, pointA.y, pointB.x, pointB.y);
    }
  }

  // Draw all the tracked hand points
  for (let i = 0; i < hands.length; i++) {
    let hand = hands[i];
    for (let j = 0; j < hand.keypoints.length; j++) {
      let keypoint = hand.keypoints[j];
      fill(0, 255, 255);
      noStroke();
      circle(keypoint.x, keypoint.y, 8);
    }
  }
}

// Callback function for when handPose outputs data
function gotHands(results) {
  // save the output to the hands variable
  hands = results;
}

  // If the model is trained, make a classification and display the result
  if (isTrained && hands[0]) {
    let inputData = flattenHandData();
    classifier.classify(inputData, gotClassification);
    textSize(64);
    fill(0, 255, 0);
    text(classification, 20, 60);
}

// Callback function for when handPose outputs data
function gotHands(results) {
  hands = results;
}

// Callback function for when the classifier makes a classification
function gotClassification(results) {
  classification = results[0].label;
}

// convert the handPose data to a 1D array
function flattenHandData() {
  let hand = hands[0];
  let handData = [];
  for (let i = 0; i < hand.keypoints.length; i++) {
    let keypoint = hand.keypoints[i];
    handData.push(keypoint.x);
    handData.push(keypoint.y);
  }
  return handData;
}
//Recopilar datos de entrenamiento de diferentes categorías de gestos y agregarlos al clasificador como Letra A, Letra B y Letra C
function AgregarDatosdeA() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["A"];
    classifier.addData(inputData, outputData);
    ADataCount++;
  }
  updateDataCountUI();
}

function AgregarDatosdeB() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["B"];
    classifier.addData(inputData, outputData);
    BDataCount++;
  }
  updateDataCountUI();
}

function AgregarDatosdeC() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["C"];
    classifier.addData(inputData, outputData);
    CDataCount++;
  }
  updateDataCountUI();
}
  function AgregarDatosdeD() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["D"];
    classifier.addData(inputData, outputData);
    DDataCount++;
  }
  updateDataCountUI();
}
    function AgregarDatosdeE() {
  if (hands[0]) {
    let inputData = flattenHandData();
    let outputData = ["E"];
    classifier.addData(inputData, outputData);
    EDataCount++;
  }
  updateDataCountUI();
}

// Update the HTML UI with the current data counts
function updateDataCountUI() {
  dataCountsP.html(
    "A data: " +
      ADataCount +
      "<br>B data: " +
      BDataCount +
      "<br>C data: " +
      CDataCount +
      "<br>D data: " +
      DDataCount +
      "<br>E data: " +
      EDataCount
  );
}

// Train the data when 'Train abd Save Model' button is pressed
function train() {
  // The data should be normalized before training
  classifier.normalizeData();

  // Train the model
  let trainingOptions = {
    epochs: 100,
  };
  classifier.train(trainingOptions, finishedTraining);
}

// When the model is trained, save the model
function finishedTraining() {
  console.log("Training finished!");
  classifier.save();
  isTrained = true;
}
```

**Datos guardados de entrenamiento**
![image](https://github.com/user-attachments/assets/ba2309a3-e926-489a-a285-ef9490e76a5d)

**Nos entrega 3 archivos de modelo para usar en otro código**

![image](https://github.com/user-attachments/assets/a4c4b998-24ae-481b-a504-3cdcbeaa0280)

#### Código base (The Coding Train)
link: https://editor.p5js.org/codingtrain/sketches/HPiy5aW-Z
```Javascript
let classifier;
let handPose;
let video;
let hands = [];
let isModelLoaded = false;
let label = "";

function preload() {
  // Load the handPose model
  handPose = ml5.handPose({ flipped: true });
}

function setup() {
  createCanvas(640, 480);

  // Create the webcam video and hide it
  video = createCapture(VIDEO, { flipped: true });
  video.size(640, 480);
  video.hide();

  // For this example to work across all browsers
  // "webgl" or "cpu" needs to be set as the backend
  ml5.setBackend("webgl");

  // Set up the neural network
  let classifierOptions = {
    task: "classification",
  };
  classifier = ml5.neuralNetwork(classifierOptions);

  const modelDetails = {
    model: "model/model.json",
    metadata: "model/model_meta.json",
    weights: "model/model.weights.bin",
  };

  classifier.load(modelDetails, modelLoaded);

  // Start the handPose detection
  handPose.detectStart(video, gotHands);
}

function draw() {
  //Display the webcam video
  image(video, 0, 0, width, height);

  // Draw the handPose keypoints
  if (hands[0]) {
    let hand = hands[0];

    // let connections = handPose.getConnections();
    let connections = getConnections();
    for (let c of connections) {
      stroke(252, 238, 33);
      let a = c[0];
      let b = c[1];
      strokeWeight(2);
      beginShape();
      vertex(hand.keypoints[a].x, hand.keypoints[a].y);
      vertex(hand.keypoints[b].x, hand.keypoints[b].y);
      endShape();
    }

    for (let i = 0; i < hand.keypoints.length; i++) {
      let keypoint = hand.keypoints[i];
      fill(240, 99, 164);
      stroke(112, 50, 126);
      circle(keypoint.x, keypoint.y, 16);
    }
  }
  let x = 20;
  let y = 20;
  let w = textWidth(label) + 20;
  let h = 60;

  // Draw the transparent rectangle as background
  fill(0, 0, 0, 150); // Black with transparency
  noStroke();
  rect(x, y, w, h, 10); // Rounded rectangle

  // Draw the label text
  textSize(32);
  fill(255); // White text
  textAlign(LEFT, CENTER);
  text(label, x + 10, y + h / 2);

  // If the model is loaded, make a classification and display the result
  if (isModelLoaded && hands[0]) {
    let inputData = flattenHandData();
    classifier.classify(inputData, gotClassification);
  }
}

// convert the handPose data to a 1D array
function flattenHandData() {
  let hand = hands[0];
  let handData = [];
  for (let i = 0; i < hand.keypoints.length; i++) {
    let keypoint = hand.keypoints[i];
    handData.push(keypoint.x);
    handData.push(keypoint.y);
  }
  return handData;
}

// Callback function for when handPose outputs data
function gotHands(results) {
  hands = results;
}

// Callback function for when the classifier makes a classification
function gotClassification(results) {
  label = results[0].label;
}

// Callback function for when the pre-trained model is loaded
function modelLoaded() {
  isModelLoaded = true;
}

// Until new ml5 is released
function getConnections() {
  return [
    [0, 1],
    [1, 2],
    [2, 3],
    [3, 4],
    [0, 5],
    [5, 6],
    [6, 7],
    [7, 8],
    [9, 10],
    [10, 11],
    [11, 12],
    [13, 14],
    [14, 15],
    [15, 16],
    [0, 17],
    [17, 18],
    [18, 19],
    [19, 20],
    [5, 9],
    [9, 13],
    [13, 17],
  ];
}

```
**Remplazamos los archivos anterior a nuestro modelo entrenado para detectar lengua de seña chilena**

![image](https://github.com/user-attachments/assets/e7557808-0dbe-4c05-9e83-c432bd0974e2)

## Resultado

![image](https://github.com/user-attachments/assets/a7a4460d-0d82-4471-b06a-2d9debd6a7ed)
![image](https://github.com/user-attachments/assets/d8f5dd69-872d-44eb-ac4c-bf0481a2d4b3)
![image](https://github.com/user-attachments/assets/de83edba-88c9-4bc0-b7a9-daf5400967ab)

## Enlace del proyecto
Lo hicimos en p5js: https://editor.p5js.org/fuhua486/sketches/sxcFfUT38

## Bibliografía
Nos basamos en el código de [The Coding Train](https://editor.p5js.org/codingtrain/sketches/HPiy5aW-Z) y modelos de ml5: [HandPose Skeletal Connections](https://editor.p5js.org/ml5/sketches/fnboooD-K) y [NeuralNetwork Train and Save](https://editor.p5js.org/ml5/sketches/rR51vvi-u) 

Fuentes de información sobre Lengua de Señas Chilena usada en nuestro proyecto: https://www.biobiochile.cl/noticias/servicios/toma-nota/2024/09/24/12-palabras-en-lengua-de-senas-chilena-que-deberias-aprender-te-sabes-alguna.shtml

## Conclusión

Comparado con el modelo de imagen de Teachable Machine utilizado en el proyecto TeachSeñas, el modelo de HandPose Skeletal Connections y NeuralNetwork Train and Save que hemos utilizado esta vez tiene una detección mucho más precisa. Ya no se ve afectado por factores como el color, el tamaño o el fondo, sino que detecta con precisión la mano.

Este proyecto solo seleccionó cinco letras del lenguaje de señas (A, B, C, D, E) para la prueba. En el futuro, según el desarrollo del proyecto y las necesidades, se pueden agregar más letras (solamente es necesario copiar y modificar parte del código según los requisitos de la letra).

Al principio, el proyecto también tenía un plan para mostrar las letras detectadas en un cuadro de texto y formar gradualmente una oración, pero debido a las dificultades técnicas y limitaciones de tiempo, finalmente pudimos implementar esta característica. Pero gracias a este proyecto, pudimos comprender mejor qué partes del código pueden modificarse según nuestras necesidades, y empezar a ver la programación como una herramienta conveniente más que una lenguaje complejo.

Muchas gracias!!!
