# clase-13

link: <https://www.youtube.com/watch?v=T99fNXTUUaQ> (detección de cuerpo)  
link: <https://docs.ml5js.org/#/reference/bodypose> (bodypose ml5js)  
link: <https://www.youtube.com/watch?v=bPD8lL0hiLs> (detección cuerpo e interacción con cosas, aplica la física)

![image](https://github.com/user-attachments/assets/02433120-4fb2-41b3-9db3-c06647a0ea95)

Se trabajaría con los números 10,8, 6 (brazo derecho) y 5,7,9 (brazo izquierdo), detección de parte superior de cuerpo para el juego de pelea (FASE DE TESTEO)

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
Aquí se logra detectar brazos izquierdo y derecho, enfocándose en hombros, codos y muñecas, se realizan testeos de perspectivas para concluir que la mejor perspectiva para realizar una simulación de pelea es la persona posicionada de lado, no de frente, puesto que (especialmente en un cuerpo femenino) los puntos se suelen confundir, en especialmente el de los codos, con las costillas del cuerpo.  

Ahora vamos a intentar que un objeto haga cierta acción cuando la persona haga eso.  
Utilizaremos de referencia este código, creado por: eeleye, para poder crear un estilo de animación más fluido de las posiciones de los personajes. 

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
Dentro del análisis se buscará investigar formar de crear una secuencia de imágenes pero que sean mayores a 10, sin sobrecargar el código principal (usando carpetas, variantes, etc).
Se utiliza de referencia:  

![image](https://github.com/user-attachments/assets/ef6b2200-9955-454f-98cc-5545dbc183e9)


<https://www.mortalkombatwarehouse.com/umk3/rain/sprites/>

Utilizaremos pruebas de gifs e imágenes para testear la base del movimiento del personaje.  
<https://p5js.org/reference/p5/saveGif/>  
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
