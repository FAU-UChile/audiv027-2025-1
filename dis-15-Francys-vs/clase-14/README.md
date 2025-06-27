## clase-14

# Juego: Kyu Fight! 

Integrantes:

* Vicente Tapia https://github.com/VicentilloTF  
* Francys Vásquez https://github.com/Francys-vs

mi equipo de trabajo es <https://github.com/Francys-vs> y <https://github.com/VicentilloTF>, entregamos en el repositorio en este enlace <https://github.com/Francys-vs/Kyu-Fight-/blob/main/README.md>  

## Acerca del proyecto.

El juego consiste en que el jugador podrá simular ser un peleador profesional, en el que mediante la detección de movimiento de brazos, el jugador podrá pelear contra "Kyu" un muñeco de práctica (bastante "adorable"), Kyu tiene una barra de vida, el objetivo será dar la mayor cantidad de golpes durante un tiempo en específico, si logras derrotar a Kyu, se gana la partida, de lo contrario pierdes. 

link: <https://www.youtube.com/watch?v=T99fNXTUUaQ> (detección de cuerpo)  
link: <https://docs.ml5js.org/#/reference/bodypose> (bodypose ml5js)  
link: <https://www.youtube.com/watch?v=bPD8lL0hiLs> (detección cuerpo e interacción con cosas, aplica la física)

![image](https://github.com/user-attachments/assets/02433120-4fb2-41b3-9db3-c06647a0ea95)

Se trabajaría con los números 10 y 6 (brazo derecho) y 5 y 9 (brazo izquierdo), esto para detectar solo hombros y muñecas, enfocándose en que al subir la muñeca se realiza un golpe.  

## Proceso de creación de código del proyecto.
### Primer acercamiento a detección de brazos.

``` javascript
let video;
let bodyPose;
let connections;
let poses = [];

function preload() {
  bodyPose = ml5.bodyPose("MoveNet", { flipped: true });
}

function mousePressed() {
  console.log(poses);
}

function gotPoses(results) {
  poses = results;
}

function setup() {
  createCanvas(640, 480);

  video = createCapture(VIDEO, { flipped: true });
  video.hide();

  bodyPose.detectStart(video, gotPoses);
}

function draw() {
  image(video, 0, 0);

  if (poses.length > 0) {
    let pose = poses[0];
    
    // Aqui dibujará puntitos en los hombros der e izq, codos der e izq y muñecas der e izq
    fill(146, 83, 161);
    circle(pose.right_shoulder.x, pose.right_shoulder.y, 64);
    circle(pose.left_shoulder.x, pose.left_shoulder.y, 64);
    
    fill(255,0,0);
    circle(pose.right_elbow.x, pose.right_elbow.y, 64);
    circle(pose.left_elbow.x, pose.left_elbow.y, 64);
    
    fill(255,0,0);
    circle(pose.right_wrist.x, pose.right_wrist.y, 64);
    circle(pose.left_wrist.x, pose.left_wrist.y, 64);   
  }
}
```
Aquí se logra detectar brazos izquierdo y derecho, enfocándose en hombros, codos y muñecas, se realizan testeos de perspectivas para concluir que la mejor perspectiva para realizar una simulación depende de luz y atuendos, en este caso era mejor estar de lado que de frente.  

Ahora se realizará la definición de personaje que golpea y el que es golpeado, para poder realizar esto se debe utilizar archivos como png para crear animaciones, sin embargo, se usará el formato gif para que sea más sencillo de leer dentro del código y así evitamos sobrecargarlo de información y archivos.  
Utilizaremos de referencia este código, creado por: eeleye, para poder crear un estilo de animación más fluido de las posiciones de los personajes. Esto siendo aplicado a imágenes png, sin embargo, es extrapolará hacia el formato gif.

``` javascript
var numFrames = 6;
var frame = 0;
var images = new Array (numFrames);

function preload() {
  images[0] = loadImage('dove01.png');
  images[1] = loadImage('dove02.png');
  images[2] = loadImage('dove03.png');
  images[3] = loadImage('dove04.png');
  images[4] = loadImage('dove05.png');
  images[5] = loadImage('dove06.png');
}

function setup() {
  createCanvas(400, 400);
  background(0);
  frameRate(15);
}

function draw() {
  background(225);
  frame++;
  if (frame == numFrames) frame = 0;
    image(images[frame], mouseX - 75, mouseY - 100);
}

```
Dentro del análisis de esta referencia, utilizaremos el formato de creación de animación de juegos como Mortal Kombat 1 o 2, que en base a los famosos "sprites" podemos generar movimiento en base a varias imágenes, algo útil para nosotros poder utilizarlo.

![image](https://github.com/user-attachments/assets/ef6b2200-9955-454f-98cc-5545dbc183e9)


<https://www.mortalkombatwarehouse.com/umk3/rain/sprites/>

Utilizaremos pruebas de gifs e imágenes para testear la base del movimiento del personaje. Utilizamos la información obtenida de la misma página de referencia de p5.js.  
link referencia Gif: <https://p5js.org/reference/p5/saveGif/>  

Para aprender a utilizar los gif e imágenes correctamente utilizaremos esta base, sin embargo, posee errores, que arreglamos para que funcione correctamente basándonos en el programa anterior.  

``` javascript
var mouseImages = [];
var frameNumber = 0;

function preload(){
  for (var i = 0; i < 4; i++){
    mouseImages[i] = loadImage('assets/'+i+'.gif');  
  }
}

function setup() {
  createCanvas(400, 400);
}

//sdd
function draw() {
  background(220);
  image(mouseImages[1],0,0);
  
  frameNumber++;
  if (frameNumber > 2){
     frameNumber = 0; 
  }
}
```

Ya corregido... 

``` javascript
var frameNumber = 3;
var frame = 0;
var mouseImages = new Array (frameNumber);

function preload(){
  for (var i = 0; i < 4; i++){
    mouseImages[i] = loadImage('assets/'+i+'.png');  
  }
}

function setup() {
  createCanvas(1000, 1000);
  frameRate(15);
}


function draw() {
  background(220);
//  image(mouseImages[2],0,0);
  
  frame++;
  if (frame == frameNumber) frame = 0;
     image(mouseImages[frame], mouseX-300 , mouseY-300);
  
}
```
En base a la creación de gif, crearemos animación de personaje para: posición de comienzo, golpe con brazo izquierdo y golpe con brazo derecho. El gif se realizó mediante un diseño vectorizado, comenzamos la prueba de testeo de detección de movimiento de persona + animación del personaje.  

![ezgif-86bd6767c25bad](https://github.com/user-attachments/assets/4ed7df50-9233-483d-818e-cdcba16c229e) 

Se realizó un nuevo arreglo sobre la detección entre cuerpo y acción, en este caso, se hizo un cambio en el que debe aparecer un texto sobre la acción correspondiente los cuales son: "golpe izquierdo", "golpe derecho" y "quieto", sin embargo, existen problema para detectar la acción de estar quieto puesto que se confunde con movimiento de brazos aunque estén arriba o abajo.  

### Segundo acercamiento a detección de brazos.

``` javascript
let video; //carga el video

let bodyPose; //lectura de cuerpo completo

let poses = []; //será el array para trabajar con ciertas partes en específico del cuerpo humano.

let personaje; //aqui irá el peleador, la animacion sera un gif.

let gifgolpeder;
let leftWrist;
let rightWrist;
let leftShoulder;
let rightShoulder;

function preload() {
  bodyPose = ml5.bodyPose("MoveNet", {flipped: true});
  gifgolpeder = loadImage("golpeder.gif");  
}

function gotPoses(results) { 
  poses = results;

} 

function setup() {
  createCanvas(600, 400);
  video = createCapture(VIDEO, {flipped: true});
  video.hide();
  
  bodyPose.detectStart(video, gotPoses);
//  connections = bodyPose.getSkeleton();
//  console.log(connections);
  personaje = new Personaje();
}

function draw() {
  image(video, 0, 0);
  if (poses.length > 0) {
    let pose = poses[0];

    leftWrist = pose.left_wrist;
    rightWrist = pose.right_wrist;

    leftShoulder = pose.left_shoulder;
    rightShoulder = pose.right_shoulder;
  
    if (leftWrist.y < leftShoulder.y - 20) {
      personaje.golpearIzquierda();
     }
  
    if (rightWrist.y < rightShoulder.y - 20) {
      personaje.golpearDerecha();
    }
    
    personaje.mostrar();
 }
}

class Personaje { //aqui será la creación del personaje y sus estados.
  constructor() {
    this.estado = 'quieto';
  }

  golpearIzquierda() {
    this.estado = 'golpeIzquierda';
  }

  golpearDerecha() {
    this.estado = 'golpeDerecha';
  }
  mostrar() {
   fill(0,255,0);
   textSize(32);
   textAlign(CENTER, CENTER);
    if (this.estado === 'quieto') {
      text('está quieto', width/2, height/2);
      //aqui va el sprite del personaje quieto
    } else if (this.estado === 'golpeIzquierda') {
       text('golpe izquierdo', width/2, height/2);
      //aqui va el sprite del personaje golpeando con puño izquierdo
    } else if (this.estado === 'golpeDerecha') {
       text('golpe derecho', width/2, height/2);
       image(gifgolpeder, width, height);   
       
  }
  }
}
```
Ahora el enfoque antes de cualquier cosa, es que se detecte correctamente el movimiento de brazos y, a su vez, que pueda generar un movimiento, nos enfocamos en generar el sprite correspondiente a su movimiento: golpe izquierdo, golpe derecho o estar quieto, confirmamos si el movimiento se relaciona correctamente.

``` javascript
let video; 
let bodyPose;
let poses = [];

let personaje; 

let gifgolpeder;
let gifgolpeizq;
let gifquieto;
let leftWrist;
let rightWrist;
let leftShoulder;
let rightShoulder;

function preload() {
  bodyPose = ml5.bodyPose("MoveNet", {flipped: true});
  gifgolpeder = loadImage("golpeder.gif");
  gifgolpeizq = loadImage("golpeizq.gif");
  gifquieto = loadImage("quieto.gif");
}

function gotPoses(results) { 
  poses = results;
} 

function setup() {
  createCanvas(600, 400);
  video = createCapture(VIDEO, {flipped: true});
  video.hide();
  
  bodyPose.detectStart(video, gotPoses);
  personaje = new Personaje();
}

function draw() {
  image(video, 0, 0);

  if (poses.length > 0) {
    let pose = poses[0];

    leftWrist = pose.left_wrist;
    rightWrist = pose.right_wrist;

    leftShoulder = pose.left_shoulder;
    rightShoulder = pose.right_shoulder;
  
    if (leftWrist.y < leftShoulder.y - 20) {
      personaje.golpearIzquierda();
    }
  
    if (rightWrist.y < rightShoulder.y - 20) {
      personaje.golpearDerecha();
    }
  }

  personaje.mostrar();
}

class Personaje {
  constructor() {
    this.estado = 'quieto';
    this.tiempoGolpe = 0;
  }

  golpearIzquierda() {
    this.estado = 'golpeIzquierda';
    this.tiempoGolpe = millis();
  }

  golpearDerecha() {
    this.estado = 'golpeDerecha';
    this.tiempoGolpe = millis();
  }

  mostrar() {
    textAlign(CENTER, CENTER);
    textSize(32);
    fill(0, 255, 0);

    if (this.estado === 'quieto') {
      text('está quieto', width / 2, height / 2);
      image(gifquieto, width / 2 - 75, height / 2 + 50, 150, 150);
    } 
    else if (this.estado === 'golpeIzquierda') {
      text('golpe izquierdo', width / 2, height / 2);
      image(gifgolpeizq, width / 2 - 75, height / 2 + 50, 150, 150);
    } 
    else if (this.estado === 'golpeDerecha') {
      text('golpe derecho', width / 2, height / 2);
      image(gifgolpeder, width / 2 - 75, height / 2 + 50, 150, 150); 
    }

    if (millis() - this.tiempoGolpe > 500) {
      this.estado = 'quieto';
    }
  }
}
```
link para observarlo en funcionamiento: <https://editor.p5js.org/francys.vasquez/full/1Alfmpiuu>  

Aquí encontramos que los personajes y movimientos se relacionan correctamente, sin embargo, se detectan errores que tienen relación a la asociación que tiene la cámara y el programa de ml5js con la detección correcta de brazo, por lo que se deben tomar ciertas precauciones.

> [!TIP]
> * Debe poseer un lugar con buena iluminación, esta no puede dar desde la espalda, siempre de frente.  
> * Las muñecas se deben poder ver e identificar.  
> * La ropa no puede ser oscura, de ser así, se debe poseer una buena iluminación.  
> * La distancia entre la cámara y la persona debe ser prudente, considerando que se vea desde la altura de la cintura hacia arriba, mientras más cerca, dificulta que se identifique correctamente el brazo, lo mismo ocurre al alejarse demasiado, el movimiento del personaje se puede ver perjudicado.  

Ahora es cuando planteamos crear con Class y Constructor() el personaje que será golpeado, en este punto ya se crearon el diseño de personajes que fueron adaptados a ser sprites, tendremos a nuestro luchador, a nuestro Kyu y las reacciones correspondientes.  

![Peleador en estado normal/quieto.](https://github.com/user-attachments/assets/661d6b63-1911-4137-9a01-e24ebe21c419)  

![Peleador dando un golpe derecho.](https://github.com/user-attachments/assets/834b22ab-e1de-497d-84fa-c7c651afe4a5)    

![Peleador dando un golpe izquierdo pero con un arma.](https://github.com/user-attachments/assets/cdd19f34-4b6e-4437-8f49-675a57ffc712)  

![Kyu en estado normal.](https://github.com/user-attachments/assets/f4dbaaaa-bbf0-43fe-bb00-75573da86283)  

![kyu recibiendo un golpe.](https://github.com/user-attachments/assets/ef9f8c85-a9d7-4a94-a97b-fd33e639d5dc)  

Y de paso, creamos un fondo en donde ocurre el contexto de la pelea.  

![fondojuego](https://github.com/user-attachments/assets/8181c01c-ca36-47ed-b814-edf922776190)  

También se agrega mediante un archivo formato mp3, el sonido de efecto de golpe, al principio generaba un problema al activarse en bucle al levantar un brazo, sin embargo, fue corregido posteriormente para que suene con la condición de que la acción sea ejecutada.  

``` javascript
//todo lo técnico de video + movimiento
let video; 
let bodyPose;
let poses = [];

//personaje y sus movimientos
let personaje; 
let practica;

let gifgolpeder;
let gifgolpeizq;
let gifquieto;

//diseño juego
let fondo;
let sonidogolpe;

//movimiento de hombros y muñecas
let leftWrist;
let rightWrist;
let leftShoulder;
let rightShoulder;

function preload() {
  bodyPose = ml5.bodyPose("MoveNet", {flipped: true});
  gifgolpeder = loadImage("golpeder.gif");
  gifgolpeizq = loadImage("golpeizq.gif");
  gifquieto = loadImage("quieto.gif");
  fondo = loadImage('fondojuego.jpg');
  sonidogolpe = loadSound("golpe.mp3");
}

function gotPoses(results) {
  poses = results;
} 

function setup() {
  createCanvas(600, 400);
  video = createCapture(VIDEO, {flipped: true});
  video.hide();
  
  bodyPose.detectStart(video, gotPoses);
  personaje = new Personaje();
  practica = new porfiado(360, 300);
}

function draw() {
  image(fondo, 0, 0);
  image(video, 0, 0);

  if (poses.length > 0) {
    let pose = poses[0];

    leftWrist = pose.left_wrist;
    rightWrist = pose.right_wrist;

    leftShoulder = pose.left_shoulder;
    rightShoulder = pose.right_shoulder;

    if (leftWrist.y < leftShoulder.y - 20) {
      personaje.golpearIzquierda();
    }

    if (rightWrist.y < rightShoulder.y - 20) {
      personaje.golpearDerecha();
    }
  }

  personaje.mostrar();
  practica.mostrar();

  if (practica.visible && personaje.estado === 'golpeDerecha' && !personaje.golpeDetectado) {
    let golpeX = width / 2 + 75;
    let golpeY = height / 2 + 80;
    let d = dist(golpeX, golpeY, practica.x, practica.y);

    fill(0, 0, 255);
    ellipse(golpeX, golpeY, 10);

    if (d < practica.tamaño / 2 + 30) {
      practica.recibirGolpe();
      personaje.golpeDetectado = true;
    }
  }
  if (practica.visible && personaje.estado === 'golpeIzquierda' && !personaje.golpeDetectado) {
    let golpeX = width / 2 - 75;
    let golpeY = height / 2 + 80;
    let d = dist(golpeX, golpeY, practica.x, practica.y);

    fill(0, 0, 255);
    ellipse(golpeX, golpeY, 10);

    if (d < practica.tamaño / 2 + 30) {
      practica.recibirGolpe();
      personaje.golpeDetectado = true;
    }
  }
}

class Personaje {
  constructor() {
    this.estado = 'quieto';
    this.tiempoGolpe = 0;
    this.golpeDetectado = false;
  }

  golpearIzquierda() {
    this.estado = 'golpeIzquierda';
    this.tiempoGolpe = millis();
    this.golpeDetectado = false;
  }

  golpearDerecha() {
    this.estado = 'golpeDerecha';
    this.tiempoGolpe = millis();
    this.golpeDetectado = false;
  }

  mostrar() {
    textAlign(CENTER, CENTER);
    textSize(32);
    fill(0, 255, 0);

    if (this.estado === 'quieto') {
      text('está quieto', width / 2, height / 2);
      image(gifquieto, width / 2 - 75, height / 2 + 50, 150, 150);
    } else if (this.estado === 'golpeIzquierda') {
      text('golpe izquierdo', width / 2, height / 2);
      image(gifgolpeizq, width / 2 - 75, height / 2 + 50, 150, 150);
      if (!sonidogolpe.isPlaying()) {
        sonidogolpe.play();
      }
    } else if (this.estado === 'golpeDerecha') {
      text('golpe derecho', width / 2, height / 2);
      image(gifgolpeder, width / 2 - 75, height / 2 + 50, 150, 150);
      if (!sonidogolpe.isPlaying()) {
        sonidogolpe.play();
      }
    }

    if (millis() - this.tiempoGolpe > 500) {
      this.estado = 'quieto';
      this.golpeDetectado = false;
    }
  }
}

class porfiado {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.tamaño = 50;
    this.salud = 50;
    this.visible = true;
  }

  recibirGolpe() {
    this.salud--;
    if (this.salud <= 0) {
      this.visible = false;
    }
  }

  mostrar() {
    if (this.visible) {
      fill(255, 0, 0);
      ellipse(this.x, this.y, this.tamaño);
    }
  }
}
```
Link para observarlo en movimiento: <https://editor.p5js.org/francys.vasquez/full/vRB-ViYDW>    
Aquí antes de generar la interacción entre Kyu y el peleador, se hace la prueba con una ellipse que simulará ser Kyu, esto con el propósito de lograr el contacto entre peleador y Kyu. 

### Acercamiento casi final del juego completo.

``` javascript
//todo lo técnico de video + movimiento
let video; 
let bodyPose;
let poses = [];

//personaje y sus movimientos
let personaje; 
let practica;

let gifgolpeder;
let gifgolpeizq;
let gifquieto;
let kyuchill;
let kyudolor;

//diseño juego
let fondo;
let sonidogolpe;

//movimiento de hombros y muñecas
let leftWrist;
let rightWrist;
let leftShoulder;
let rightShoulder;

function preload() {
  bodyPose = ml5.bodyPose("MoveNet", {flipped: true});
  gifgolpeder = loadImage("golpeder.gif");
  gifgolpeizq = loadImage("golpeizq.gif");
  gifquieto = loadImage("quieto.gif");
  fondo = loadImage('fondojuego.jpg');
  sonidogolpe = loadSound("golpe.mp3");
  kyuchill = loadImage("kyunormal.png");
  kyudolor = loadImage("kyuauch.png");
}

function gotPoses(results) {
  poses = results;
} 

function setup() {
  createCanvas(600, 400);
  video = createCapture(VIDEO, {flipped: true});
  video.hide();
  
  bodyPose.detectStart(video, gotPoses);
  personaje = new Personaje();
  practica = new porfiado(390, 320);
}

function draw() {
  image(fondo, 0, 0);
  image(video, 0, 0);

  if (poses.length > 0) {
    let pose = poses[0];

    leftWrist = pose.left_wrist;
    rightWrist = pose.right_wrist;

    leftShoulder = pose.left_shoulder;
    rightShoulder = pose.right_shoulder;

    if (leftWrist.y < leftShoulder.y - 20) {
      personaje.golpearIzquierda();
    }

    if (rightWrist.y < rightShoulder.y - 20) {
      personaje.golpearDerecha();
    }
  }

  personaje.mostrar();
  practica.mostrar();

  
  if (practica.visible && personaje.estado === 'golpeDerecha' && !personaje.golpeDetectado) {
    let golpeX = width / 2 + 75;
    let golpeY = height / 2 + 80;
    let d = dist(golpeX, golpeY, practica.x, practica.y);
    if (d < practica.tamaño / 2 + 30) {
  practica.recibirGolpe();
  personaje.golpeDetectado = true;
}


    fill(0, 0, 255);
    ellipse(golpeX, golpeY, 10);

    if (d < practica.tamaño / 2 + 30) {
      practica.recibirGolpe();
      personaje.golpeDetectado = true;
    }
  }

  if (practica.visible && personaje.estado === 'golpeIzquierda' && !personaje.golpeDetectado) {
    let golpeX = width / 2 - 75;
    let golpeY = height / 2 + 80;
    let d = dist(golpeX, golpeY, practica.x, practica.y);

    fill(0, 0, 255);
    ellipse(golpeX, golpeY, 10);

    if (d < practica.tamaño / 2 + 30) {
      practica.recibirGolpe();
      personaje.golpeDetectado = true;
    }
  }
}

class Personaje {
  constructor() {
    this.estado = 'quieto';
    this.tiempoGolpe = 0;
    this.golpeDetectado = false;
  }

  golpearIzquierda() {
    this.estado = 'golpeIzquierda';
    this.tiempoGolpe = millis();
    this.golpeDetectado = false;
  }

  golpearDerecha() {
    this.estado = 'golpeDerecha';
    this.tiempoGolpe = millis();
    this.golpeDetectado = false;
  }

  mostrar() {
    textAlign(CENTER, CENTER);
    textSize(32);
    fill(0, 255, 0);

    if (this.estado === 'quieto') {
      text('está quieto', width / 2, height / 2);
      image(gifquieto, width / 2 - 75, height / 2 + 50, 150, 150);
    } else if (this.estado === 'golpeIzquierda') {
      text('golpe izquierdo', width / 2, height / 2);
      image(gifgolpeizq, width / 2 - 75, height / 2 + 50, 150, 150);
      if (!sonidogolpe.isPlaying()) {
        sonidogolpe.play();
      }
    } else if (this.estado === 'golpeDerecha') {
      text('golpe derecho', width / 2, height / 2);
      image(gifgolpeder, width / 2 - 75, height / 2 + 50, 150, 150);
      if (!sonidogolpe.isPlaying()) {
        sonidogolpe.play();
      }
    }

    if (millis() - this.tiempoGolpe > 500) {
      this.estado = 'quieto';
      this.golpeDetectado = false;
    }
  }
}

class porfiado {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.tamaño = 80;
    this.salud = 50;
    this.visible = true;
    this.golpeado = false;
    this.ultgolp = 0;
  }

  recibirGolpe() {
    this.salud--;
    this.golpeado = true;
    this.ultgolp = millis();
    
    if (this.salud <= 0) {
      this.visible = false;
    }
  }

  mostrar() {
    if (this.visible) {
      let tdesdegolp = millis() - this.tultgolp;
      if (this.golpeado && tdesdegolp < 200) {
        image(kyudolor, this.x - this.tamaño / 2, this.y - this.tamaño / 2, this.tamaño, this.tamaño);
      } else {
        this.golpeado = false;
        image(kyuchill, this.x - this.tamaño / 2, this.y - this.tamaño / 2, this.tamaño, this.tamaño);
      }      
    }
  }
}
```
Ya esta parte del código es la más completa, poseemos personajes actualizados, interacción de golpes que responden al movimiento de brazos y Kyu posee una cantidad de vida en específico que se le acaba con cada golpe, lo que nos da el objetivo principal poder derrotarlo. Ahora queda ajustar elementos como barras de vida, fondo bien visualizado, y visualizar mejor los objetivos del juego.

#### Cosas que se agregaron.  

* Barra de vida de Kyu.
* Temporizador, condiciona la victoria o la derrota.
* Mensajes de interacción de juego.

## Resultado final.

``` javascript
//todo lo técnico de video + movimiento
let video; 
let bodyPose;
let poses = [];

//personaje y sus movimientos
let personaje; 
let practica;

let gifgolpeder;
let gifgolpeizq;
let gifquieto;
let kyuchill;
let kyudolor;

//diseño juego
let fondo;
let sonidogolpe;

//movimiento de hombros y muñecas
let leftWrist;
let rightWrist;
let leftShoulder;
let rightShoulder;

//tiempo de juego
let tiempoInicio;
let tiempoLimite = 10000; // 10 segundos (milisegundos)
let estadoJuego = "jugando";

function preload() {
  bodyPose = ml5.bodyPose("MoveNet", {flipped: true});
  gifgolpeder = loadImage("golpeder.gif");
  gifgolpeizq = loadImage("golpeizq.gif");
  gifquieto = loadImage("quieto.gif");
  fondo = loadImage('fondojuego.jpg');
  sonidogolpe = loadSound("golpe.mp3");
  kyuchill = loadImage("kyunormal.png");
  kyudolor = loadImage("kyuauch.png");
}

function gotPoses(results) {
  poses = results;
} 

function setup() {
  createCanvas(600, 400);
  video = createCapture(VIDEO, {flipped: true});
  video.hide();
  
  bodyPose.detectStart(video, gotPoses);
  personaje = new Personaje();
  practica = new porfiado(390, 320);
  
  tiempoI = millis();
}

function draw() {
  image(fondo, 0, 0);
  tint(255,255,255, 60);
  image(video, 0, 0);
  noTint();

  if (poses.length > 0) {
    let pose = poses[0];

    leftWrist = pose.left_wrist;
    rightWrist = pose.right_wrist;

    leftShoulder = pose.left_shoulder;
    rightShoulder = pose.right_shoulder;

    if (leftWrist.y < leftShoulder.y - 20) {
      personaje.golpearIzquierda();
    }

    if (rightWrist.y < rightShoulder.y - 20) {
      personaje.golpearDerecha();
    }
  }

  personaje.mostrar();
  practica.mostrar();

  
  if (practica.visible && personaje.estado === 'golpeDerecha' && !personaje.golpeDetectado) {
    let golpeX = width / 2 + 75;
    let golpeY = height / 2 + 80;
    let d = dist(golpeX, golpeY, practica.x, practica.y);
    if (d < practica.tamaño / 2 + 30) {
  practica.recibirGolpe();
  personaje.golpeDetectado = true;
}


    fill(0, 0, 255);
    ellipse(golpeX, golpeY, 10);

    if (d < practica.tamaño / 2 + 30) {
      practica.recibirGolpe();
      personaje.golpeDetectado = true;
    }
  }

  if (practica.visible && personaje.estado === 'golpeIzquierda' && !personaje.golpeDetectado) {
    let golpeX = width / 2 - 75;
    let golpeY = height / 2 + 80;
    let d = dist(golpeX, golpeY, practica.x, practica.y);

    fill(0, 0, 255);
    ellipse(golpeX, golpeY, 10);

    if (d < practica.tamaño / 2 + 30) {
      practica.recibirGolpe();
      personaje.golpeDetectado = true;
    }
  }
  if (!practica.visible && !practica.derrotado) {
    practica.derrotado = true;
    practica.momentoDerrota = millis();
  }

  if (practica.derrotado) {
    let tiempoDesdeKO = millis() - practica.momentoDerrota;
  
    if (tiempoDesdeKO < 2000) {
      textAlign(CENTER, CENTER);
      textSize(48);
      fill(255, 0, 0);
      text("FATALITY!!", width / 2, height / 2 - 100);
  }
}
  if (estadoJuego === "jugando") {
    let tiempoTranscurrido = millis() - tiempoI;
    if (tiempoTranscurrido > tiempoLimite && practica.visible) {
      estadoJuego = "perdido";
  }
    if (!practica.visible && estadoJuego === "jugando") {
      estadoJuego = "ganado";
  }
    let segundosRestantes = max(0, floor((tiempoLimite - tiempoTranscurrido) / 1000));
     fill(255);
     textSize(24);
     textAlign(LEFT, TOP);
     text("Tiempo: " + segundosRestantes + "s", 10, 10);
    
}
  if (estadoJuego === "ganado") {
    fill(0, 255, 0);
    textSize(48);
    textAlign(CENTER, CENTER);
    text("¡GANASTE!", width / 2, height / 2 - 50);
}

  if (estadoJuego === "perdido") {
    fill(255, 0, 0);
    textSize(48);
    textAlign(CENTER, CENTER);
    text("¡PERDISTE!", width / 2, height / 2 - 50);
}
}

class Personaje {
  constructor() {
    this.estado = 'quieto';
    this.tiempoGolpe = 0;
    this.golpeDetectado = false;
    this.derrotado = false;
    this.momentoDerrota = 0;
  }

  golpearIzquierda() {
    this.estado = 'golpeIzquierda';
    this.tiempoGolpe = millis();
    this.golpeDetectado = false;
  }

  golpearDerecha() {
    this.estado = 'golpeDerecha';
    this.tiempoGolpe = millis();
    this.golpeDetectado = false;
  }

  mostrar() {
    textAlign(CENTER, CENTER);
    textSize(32);
    fill(0, 255, 0);

    if (this.estado === 'quieto') {
      text('está quieto', width / 2, height / 2);
      image(gifquieto, width / 2 - 75, height / 2 + 50, 150, 150);
    } else if (this.estado === 'golpeIzquierda') {
      text('golpe izquierdo', width / 2, height / 2);
      image(gifgolpeizq, width / 2 - 75, height / 2 + 50, 150, 150);
      if (!sonidogolpe.isPlaying()) {
        sonidogolpe.play();
      }
    } else if (this.estado === 'golpeDerecha') {
      text('golpe derecho', width / 2, height / 2);
      image(gifgolpeder, width / 2 - 75, height / 2 + 50, 150, 150);
      if (!sonidogolpe.isPlaying()) {
        sonidogolpe.play();
      }
    }

    if (millis() - this.tiempoGolpe > 500) {
      this.estado = 'quieto';
      this.golpeDetectado = false;
    }
  }
}

class porfiado {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.tamaño = 80;
    this.salud = 50;
    this.samax = 50;
    this.visible = true;
    this.golpeado = false;
    this.ultgolp = 0;
  }

  recibirGolpe() {
    this.salud--;
    this.golpeado = true;
    this.ultgolp = millis();
    
    if (this.salud <= 0) {
      this.visible = false;
      this.derrotado = false;
    }
  }

  mostrar() {
    if (this.visible) {
      let tdesdegolp = millis() - this.tultgolp;
      if (this.golpeado && tdesdegolp < 200) {
        image(kyudolor, this.x - this.tamaño / 2, this.y - this.tamaño / 2, this.tamaño, this.tamaño);
      } else {
        this.golpeado = false;
        image(kyuchill, this.x - this.tamaño / 2, this.y - this.tamaño / 2, this.tamaño, this.tamaño);
      }
     let barancho = 60;
     let baralto = 8;
     let porcentaje = this.salud / this.samax;
     let barraX = this.x - barancho / 2;
     let barraY = this.y - this.tamaño / 2 - 15;

     noStroke();
     fill(150, 0, 0);
     rect(barraX, barraY, barancho, baralto);

     fill(0, 255, 0);
     rect(barraX, barraY, barancho * porcentaje, baralto);
    }
  }
}
```
# Enlace de Kyu Fight!  

El juego se realizó en la versión 1.11.7 de p5.js junto a la función BodyPose de ml5.js, este es el link para poder ver:  
Trabajo en funcionamiento: <https://editor.p5js.org/francys.vasquez/full/CioDICJc5>  
Juego y código: <https://editor.p5js.org/francys.vasquez/sketches/CioDICJc5>  

# Conclusión.  

El juego posee una semi parte de éxito, lamentablemente fue más difícil de realizar a comparación de nuestro primer proyecto, puesto que muy pocas personas se han atrevido a realizar juegos en base a los movimientos de brazos, una vez que encontramos a esas personas, resulta que los códigos no estaban ni cerca de funcionar debido a las recientes actualizaciones tanto p5.js como de ml5js. 

A pesar de esto, podemos concordar en que nos gustó demasiado el adentrarnos en el mundo de la experimentación del "gaming" junto a la detección del cuerpo humano, esto recordándonos elemtos de consolas de videojuegos tales como la "Kinect" de la Xbox360, o los mandos de la consola "Wii". Sin dudarlo es un mundo muy abierto de posibilidades, en donde la creatividad que permite p5.js para poder crear una variedad de juegos es impresionante, definitivamente es para evaluar muchos más estilos, no solo enfocados en movimiento corporal, también con el click o cosas tan simples como el teclado.  

Lo negativo de este trabajo, es que fue una dificultad poder hacer que detecte correctamente los brazos el BodyPose, lo cual es curioso, puesto que parece que se utiliza con propósitos más "sencillos" dentro de las investigaciones de referentes que buscamos, lo que cuesta que al pedirle a otra cosa que reaccione a la acción, muchas veces, no detecta nada o confunde con otras partes del cuerpo por más que se trabajen con "arrays".  

Finalmente, como dúo comprendemos la responsabilidad que conlleva el crear un juego de este estilo, ya que, como nosotros no tuvimos ningún referente cercano, nuestro propio trabajo puede ser de utilidad a otras personas y que, incluso, pueden lograr llevar el juego a una mejor versión del mismo, ya sea utilizando el mismo método o no de BodyPose.
