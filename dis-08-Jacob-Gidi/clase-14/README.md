# clase-14

Yo trabajo solo y lo entrego aquí mismo: https://github.com/Jacob-Gidi/audiv027-2025-1/edit/main/dis-08-Jacob-Gidi/clase-14/README.md

# Serpiente arcoiris.

Mi proyecto final se trata de hacer un juego de la serpiente que se mueve mediante los movimientos de la mano mediante la punta de los dedos. 

![Drawing Lines](https://github.com/user-attachments/assets/bfba8557-0657-4211-955a-cbef42a86163)

![Snake Game](https://github.com/user-attachments/assets/f5975d47-e974-4cfb-966f-1f9152a2dd2d)

Mi programa base sería el juego original de la serpiente combinado a uno llamado Drawing Lines, el cual concibió a la serpiente pintada, donde la serpiente cambia de color al moverse.  

# Serpiente Pintada.

let gridWidth = 30;
let gridHeight = 30;

let gameStarted = false;
let startingSegments = 5;
let xStart = 0;
let yStart = 15;
let startDirection = 'right';
let direction = startDirection;

let segments = [];
let score = 0;
let highScore;
let fruit;

function setup() {
  createCanvas(500, 500);
  colorMode(HSB); // ← color en modo HSB
  frameRate(10);
  textAlign(CENTER, CENTER);
  textSize(2);
  highScore = getItem('high score');
}

function draw() {
  background(0);
  scale(width / gridWidth, height / gridHeight);

  if (!gameStarted) {
    showStartScreen();
  } else {
    translate(0.5, 0.5);
    showFruit();
    showSegments();
    updateSegments();
    checkForCollision();
    checkForFruit();
  }
}

function showStartScreen() {
  noStroke();
  fill(32);
  rect(2, gridHeight / 2 - 5, gridWidth - 4, 10, 2);
  fill(255);
  text('Click to play.\nUse arrow keys to move.', gridWidth / 2, gridHeight / 2);
  noLoop();
}

function mousePressed() {
  if (!gameStarted) {
    startGame();
  }
}

function startGame() {
  updateFruitCoordinates();
  segments = [];

  for (let x = xStart; x < xStart + startingSegments; x += 1) {
    let segmentPosition = createVector(x, yStart);
    segments.unshift(segmentPosition);
  }

  direction = startDirection;
  score = 0;
  gameStarted = true;
  loop();
}

function showFruit() {
  stroke(0, 100, 100); // rojo en HSB
  point(fruit.x, fruit.y);
}

function showSegments() {
  noFill();
  strokeWeight(1.2);
  beginShape();
  for (let segment of segments) {
    // Color dinámico según posición tipo línea de color
    let hueVal = ((segment.x * 10) - (segment.y * 10)) % 360;
    if (hueVal < 0) hueVal += 360; // asegurar que no sea negativo
    stroke(hueVal, 100, 100);
    vertex(segment.x, segment.y);
  }
  endShape();
}

function updateSegments() {
  segments.pop();
  let head = segments[0].copy();
  segments.unshift(head);

  switch (direction) {
    case 'right':
      head.x += 1;
      break;
    case 'up':
      head.y -= 1;
      break;
    case 'left':
      head.x -= 1;
      break;
    case 'down':
      head.y += 1;
      break;
  }
}

function checkForCollision() {
  let head = segments[0];
  if (
    head.x >= gridWidth || head.x < 0 ||
    head.y >= gridHeight || head.y < 0 ||
    selfColliding()
  ) {
    gameOver();
  }
}

function gameOver() {
  noStroke();
  fill(32);
  rect(2, gridHeight / 2 - 5, gridWidth - 4, 12, 2);
  fill(255);
  highScore = max(score, highScore);
  storeItem('high score', highScore);
  text(
    `Game over!
Your score: ${score}
High score: ${highScore}
Click to play again.`,
    gridWidth / 2,
    gridHeight / 2
  );
  gameStarted = false;
  noLoop();
}

function selfColliding() {
  let head = segments[0];
  let rest = segments.slice(1);
  for (let segment of rest) {
    if (segment.equals(head)) {
      return true;
    }
  }
  return false;
}

function checkForFruit() {
  let head = segments[0];
  if (head.equals(fruit)) {
    score++;
    let tail = segments[segments.length - 1];
    let newSegment = tail.copy();
    segments.push(newSegment);
    updateFruitCoordinates();
  }
}

function updateFruitCoordinates() {
  fruit = createVector(floor(random(gridWidth)), floor(random(gridHeight)));
}

function keyPressed() {
  switch (keyCode) {
    case LEFT_ARROW:
      if (direction !== 'right') direction = 'left';
      break;
    case RIGHT_ARROW:
      if (direction !== 'left') direction = 'right';
      break;
    case UP_ARROW:
      if (direction !== 'down') direction = 'up';
      break;
    case DOWN_ARROW:
      if (direction !== 'up') direction = 'down';
      break;
  }
}



https://github.com/user-attachments/assets/f23b24b2-01eb-4004-a9e7-8082caae8a88


Esta seria mi primera fase para el trabajo final.

# Movimiento indicado por la mano.


https://github.com/user-attachments/assets/5a91bf70-0606-47a8-b66e-29679517afdf



Para la siguiente parte busque en varias paginas como en ml5js para ayudarme en hacer otro programa donde se pueda identificar las manos y en qué dirección apunta (arriba, abajo, izquierda y derecha). Con el fin de combinar este programa para que reemplace a la parte del programa donde la serpiente es controlada por el teclado por la detección de dirección de la mano. Otro detalle del programa es que desactive la función de mostrar lo que ve la cámara, ya que al momento de juntarla con Serpiente Pintada solo estorbaría.

# Otras ideas descartadas.

Durante el proceso se me ocurrió un par de ideas de como hacer mover la serpiente. Estas fueron los otros intentos.
1.	Movimiento con un solo dedo.
2.	Movimiento con la Voz.
Ninguno funciono muy bien ya que siempre la serpiente se termina confusa o no reaccionaba a tiempo de la orden.


https://github.com/user-attachments/assets/d8e6bbc7-ed94-4f8d-b82e-73dcb4afd4f0


![Captura de pantalla 2025-06-26 185719](https://github.com/user-attachments/assets/cd62a9fd-fe31-422f-be4a-1f74131e4687)


# Tropiezos.


https://github.com/user-attachments/assets/b6867151-1301-499a-ba0a-82a8588a01e1




![Captura de pantalla 2025-06-20 232431](https://github.com/user-attachments/assets/b45ce25f-bf0e-4000-9bb9-d8c613f88506)



He tenido varios tropiezos que involucraría tanto como lograr el desplazamiento de la serpiente como el fondo donde se ubica la pantalla.
Como por ejemplo cuando la serpiente por voz no responde de inmediato, sino mucho después.
En otro cuando tanto la pantalla y fondo al ser negra me confundía y no sabia cual era el limite de la pantalla. 
En otras a veces tuve que arreglar o ajustar lo mejor posible la parte del movimiento porque no reaccionaba a tiempo cuando cambiaba de dirección con la mano.

# Detalles finales.

Después de muchos procesos y al notar ciertos detalles que afectarían al proyecto, ya que la reacción del cambio de movimiento demora un poco más decidí quitar la limitante de perder al chocar con el final de la pantalla, para que uno siga jugando sin estresarse al chocar y que sea más fácil, y se la serpiente termine al otro lado de la pantalla.
Luego de solucionar los problemas ya tengo todo listo. 

# Serpiente Arcoiris Final.

Index.html

<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8" />
    <title>Detector de Dirección con Mano</title>
    <script src="https://cdn.jsdelivr.net/npm/p5@1.6.0/lib/p5.min.js"></script>
   <script src="https://cdn.jsdelivr.net/npm/ml5@0.6.0/dist/ml5.min.js"></script>
    <style>
      html, body {
        margin: 0;
        padding: 0;
        background: #fff;
        overflow: hidden;
      }
      canvas {
        display: block;
        margin: 0;
      }
    </style>
  </head>
  <body>
    <script src="sketch.js"></script>
  </body>
</html>

Sketch.js

let gridWidth = 50;
let gridHeight = 50;

let gameStarted = false;
let startingSegments = 5;
let xStart = 0;
let yStart = 15;
let startDirection = 'right';
let direction = startDirection;

let segments = [];
let score = 0;
let highScore;
let fruit;

// Variables para handpose
let handposeModel;
let video;
let predictions = [];
let lastDetectedDirection = "Detectando...";

function setup() {
  createCanvas(500, 500);
  colorMode(HSB);
  frameRate(10);
  textAlign(CENTER, CENTER);
  textSize(2);
  highScore = getItem('high score');

  // Cámara para el modelo handpose
  video = createCapture(VIDEO);
  video.size(320, 240);
  video.hide();

  // Carga del modelo de mano
  handposeModel = ml5.handpose(video, () => {
    console.log("Modelo cargado");
  });

  handposeModel.on("predict", (results) => {
    predictions = results;
  });
}

function draw() {
  background(0);
  scale(width / gridWidth, height / gridHeight);

  detectDirectionFromHand();

  if (!gameStarted) {
    showStartScreen();
  } else {
    translate(0.5, 0.5);
    showFruit();
    showSegments();
    updateSegments();
    checkForCollision();
    checkForFruit();
  }

  // Mostrar dirección detectada (solo debug)
  resetMatrix();
  fill(255);
  textSize(12);
  textAlign(LEFT, TOP);
  text("Dirección: " + lastDetectedDirection, 10, 10);
}

function showStartScreen() {
  noStroke();
  fill(32);
  rect(2, gridHeight / 2 - 5, gridWidth - 4, 10, 2);
  fill(255);
  text('Muestra el dedo para iniciar.\nMueve el dedo índice para jugar.', gridWidth / 2, gridHeight / 2);
  noLoop();
}

function mousePressed() {
  if (!gameStarted) {
    startGame();
  }
}

function startGame() {
  updateFruitCoordinates();
  segments = [];

  for (let x = xStart; x < xStart + startingSegments; x += 1) {
    let segmentPosition = createVector(x, yStart);
    segments.unshift(segmentPosition);
  }

  direction = startDirection;
  score = 0;
  gameStarted = true;
  loop();
}

function showFruit() {
  stroke(0, 100, 100);
  point(fruit.x, fruit.y);
}

function showSegments() {
  noFill();
  strokeWeight(1.2);
  beginShape();
  for (let segment of segments) {
    let hueVal = ((segment.x * 10) - (segment.y * 10)) % 360;
    if (hueVal < 0) hueVal += 360;
    stroke(hueVal, 100, 100);
    vertex(segment.x, segment.y);
  }
  endShape();
}

function updateSegments() {
  segments.pop();
  let head = segments[0].copy();
  segments.unshift(head);

  switch (direction) {
    case 'right': head.x += 1; break;
    case 'up': head.y -= 1; break;
    case 'left': head.x -= 1; break;
    case 'down': head.y += 1; break;
  }
}

function checkForCollision() {
  let head = segments[0];

  if (head.x >= gridWidth) head.x = 0;
  if (head.x < 0) head.x = gridWidth - 1;
  if (head.y >= gridHeight) head.y = 0;
  if (head.y < 0) head.y = gridHeight - 1;

  if (selfColliding()) {
    gameOver();
  }
}

function gameOver() {
  noStroke();
  fill(32);
  rect(2, gridHeight / 2 - 5, gridWidth - 4, 12, 2);
  fill(255);
  highScore = max(score, highScore);
  storeItem('high score', highScore);
  text(
    `Game over!
Tu puntuación: ${score}
Récord: ${highScore}
Haz clic para jugar de nuevo.`,
    gridWidth / 2,
    gridHeight / 2
  );
  gameStarted = false;
  noLoop();
}

function selfColliding() {
  let head = segments[0];
  let rest = segments.slice(1);
  for (let segment of rest) {
    if (segment.equals(head)) {
      return true;
    }
  }
  return false;
}

function checkForFruit() {
  let head = segments[0];
  if (head.equals(fruit)) {
    score++;
    let tail = segments[segments.length - 1];
    let newSegment = tail.copy();
    segments.push(newSegment);
    updateFruitCoordinates();
  }
}

function updateFruitCoordinates() {
  fruit = createVector(floor(random(gridWidth)), floor(random(gridHeight)));
}

// Detectar dirección de la mano
function detectDirectionFromHand() {
  if (predictions.length > 0) {
    const hand = predictions[0];
    const indexBase = hand.landmarks[5];
    const indexTip = hand.landmarks[8];

    const dx = indexTip[0] - indexBase[0];
    const dy = indexTip[1] - indexBase[1];
    const threshold = 10;

    if (abs(dx) > abs(dy)) {
      if (dx > threshold && direction !== 'right') direction = 'left';
      else if (dx < -threshold && direction !== 'left') direction = 'right';
    } else {
      if (dy > threshold && direction !== 'up') direction = 'down';
      else if (dy < -threshold && direction !== 'down') direction = 'up';
    }

    // Solo para mostrar en pantalla
    if (abs(dx) > abs(dy)) {
      lastDetectedDirection = dx > threshold ? "Derecha" : dx < -threshold ? "Izquierda" : "Centro";
    } else {
      lastDetectedDirection = dy > threshold ? "Abajo" : dy < -threshold ? "Arriba" : "Centro";
    }
  }
}

Pagina p5.js: https://editor.p5js.org/Jacob-Gidi/sketches/4qaAd7CBP

# Conclusion

Quería modificar un juego clásico con métodos de programación actuales de hoy en día que no existía en aquellos tiempos. Serpiente Arcoíris moderniza el clásico al integrar control por movimiento de la mano usando IA con ml5.js. A pesar de los desafíos técnicos —como problemas de respuesta y visibilidad— he de decir que disfruté el proceso y aún más cuando conseguí al final una jugabilidad fluida, eliminando colisiones con los bordes y dando prioridad a la experiencia del usuario. Aún faltan alguno que otro detalle, pero el resultado final me satisface. El juego resultante es visualmente atractivo, accesible e innovador, que combina creatividad y tecnología de forma efectiva.

# Procesos.

1) Serpiente + Drawing Line = Serpiente Pintada 1  https://editor.p5js.org/Jacob-Gidi/sketches/yKwW7nZJu
2) Eliminación de muros limites: Serpiente Pintada 2  https://editor.p5js.org/Jacob-Gidi/sketches/9MG3Mp_Fw
3) Mov. de la Mano: https://editor.p5js.org/Jacob-Gidi/sketches/YimEcobOk
4) Serpiente Pintada 2 + Mov. de la Mano = Serpiente Arcoiris  https://editor.p5js.org/Jacob-Gidi/sketches/4qaAd7CBP

# Ideas y procesos descartados.

1) Dirección con Voz: https://editor.p5js.org/Jacob-Gidi/sketches/m83u5jSFD
2) Dirección con Voz + Serpiente Pintada = Serpiente por Voz: https://editor.p5js.org/Jacob-Gidi/sketches/h9Ko_LVk9
3) Snake 2, movimiento Descoordinado: https://editor.p5js.org/Jacob-Gidi/sketches/Qi8nVwqjJ
4) Mov. con el Dedo, Muy difícil de direccionar: https://editor.p5js.org/Jacob-Gidi/sketches/SnzKzTvhN
# Referentes.

Snake Game: https://p5js.org/examples/games-snake/
Drawing Line: https://p5js.org/examples/animation-and-variables-drawing-lines/
Handpose de ml5js: https://docs.ml5js.org/#/reference/handpose
