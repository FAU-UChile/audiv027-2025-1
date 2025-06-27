# clase-14

viernes 27 de Junio 2025

## SÚPER DORITO MISIÓN: CHURU

integrantes:

* Valentina Abarcia <LINK [https://github.com/ValentinaAbarcia]>
* Annais Bustamante <LINK [https://github.com/annibustamante]>
* Erlea Fuentealba  <LINK [https://github.com/erleafuentealba]>

```md
mi equipo de trabajo es <https://github.com/ValentinaAbarcia>, <LINK [https://github.com/annibustamante> y <https://github.com/erleafuentealba>, entregamos en el repositorio en este enlace <https://github.com/ETC>.
```

## acerca del proyecto

El proyecto busca lograr un juego que pueda jugarse usando los movimientos de las manos, utilizando la cámara sin tener que tocar la pantalla.

Se desarrolló el juego utilizando a Dorito, el gato de FAU, por ser un miembro muy querido e icónico de la comunidad estudiantil.

## código del proyecto

el código original que citamos es

1. Juego Original, juego dónde se debe atrapar un copo de nieve.
```javascript
/*  DXB211 A2 Brief: Creative coding project, mini game

    Group: Jessie Ting Hsuan Su
    
    Sketch by: Ting Hsuan Su N10162453
    
    
    About: This code will let the player catches snowflake with their mouse moving. It is the game designed for young children around 6-10 years old based on the popular movie “Frozen”. And the aim is to promtoe the movie thorhg this game. 
    
    FAVPNG. April, 2019. Olaf image. [image]. Retrieved from https://favpng.com/png_view/olaf-snowman-face-olaf-gif-frozen-elsa-anna-png/B7eAe2Lx

    Pinterest. 2019. Frozen wallpaper. [image]. Retrieved from https://www.pinterest.com.au/pin/336292297149555271/
    Pinterest. 2017. Snowflake. [image]. Retrieved from https://www.pinterest.com.au/pin/493636809153842920/
    
    Ashamaluevmusic. N.d. Kids. [music]. Retrieved from  https://www.ashamaluevmusic.com/happy-music
    
    Free song clip bites effect. N.d. Button click sound. [music]. Retrieved from file:///Users/jessie/Desktop/Button%20Click%20Off%20Sounds%20%7C%20Effects%20%7C%20Sound%20Bites%20%7C%20Sound%20Clips%20from%20SoundBible.com.webarchive


*/
var screen = 0;
var y=-20;
var x=200;
var speed = 2;
var score= 0;
var snowflake;
var olaf;
var bg;
var gameover;
var music;
var fade;
var fadeAmount = 1
var catchingsound;

function preload() {
    soundFormats('mp3','ogg');
	snowflake = loadImage('data/snowflake.png');
	olaf = loadImage('data/olaf.png');
	bg = loadImage('data/bg.jpg');
    //gameover = loadImage('data/ olafbye.jpg');
    
    
    
	catchingsound = loadSound('Button Click Off.mp3');
	music = loadSound('data/kid.mp3');
    



}

function setup() {
  createCanvas(600, 400);
  //music. play();
 
  addSnowflakes(10);
}

function draw() {
	if(screen == 0){
    startScreen()
  }else if(screen == 1){
  	gameOn()
  }else if(screen==2){
  	endScreen()
  }	
}

function startScreen(){
		catchingsound.pause();
		background(0)
		image(bg,0,0,600,400)
        imageMode(CORNER);
		fill(255)
  
		textAlign(CENTER);
		textSize(35)
		text('Frozen- Snowflake challenge', width / 2, height / 2)
		text('click to start', width / 2, height / 2 + 30);
		reset();
        //if (fade<0) fadeAmount=1; 
        //if (fade>255) fadeAmount=-10; 
 
       // fade += fadeAmount; 
        //print(fade)
}

function gameOn(){
	  imageMode(CENTER);
	 catchingsound.setVolume(0.1);
	image(bg,width/2,height/2,600,500)
			textSize(15)

  text("score = " + score, 50,20)
  rectMode(CENTER)
  
	image(olaf,mouseX,height-50,70,100)
	
		image(snowflake,x,y,60,50)

//when catching more iceball, the falling speed goes up
	y+= speed;
  if(y>height){
  	screen =2
	 catchingsound.stop();

	 }
  if(y>height-50 && x>mouseX-35 && x<mouseX+35){
  	y=-20
    speed+=.5
    score+= 1
    if( !music.isPlaying() ){
		music.play();
        catchingsound.play();
    }

  }
	if(y==-20){
  	pickRandom();
    }
}

function pickRandom(){
	x= random(20,width-20)
}

let snowflakes = [];

function endScreen(){
		background(0,10,150)
		textAlign(CENTER);
        textSize(25)
        fill(25);
		text('GAME OVER', width / 2, height / 2)
  	text("SCORE = " + score, width / 2, height / 2 + 20)
		text('click to play again', width / 2, height / 2 + 40);
        
  //snowflake falling effect at the end
  for (let snowflake of snowflakes) {
    snowflake.show();
    snowflake.update();
  }

	addSnowflakes(10);
}

function addSnowflakes(num) {
  for (let count = 0; count < num; count++) {
    let tempSnowflake = new Snowflake(random(width), -10 + random(20), random(0.5, 5));
    snowflakes.push(tempSnowflake);
  }
}

class Snowflake {
  constructor(x, y, speed) {
    this.x = x;
    this.y = y;
    this.speed = speed;
    this.falling = true;
  }

  update() {
    if (this.falling) {
      // this.x += 10*sin(this.y/10);
      this.y += this.speed;
      if (this.y > height - random(10)) {
        this.falling = false;
      }
    }
  }

  show() {
    noStroke();
    fill(255, 150);
    ellipse(this.x, this.y, 10);
  }
  
  
}

function mousePressed(){
	if(screen==0){
  	screen=1
		catchingsound.loop();
	    catchingsound.play();

  }else if(screen==2){
  	screen=0
  }
}

function reset(){
	  score=0;
  	speed=2;
  	y=-20;
}
```

2. Modelo Hand Pose de ML5
```javascript
/*
 * 👋 Hello! This is an ml5.js example made and shared with ❤️.
 * Learn more about the ml5.js project: https://ml5js.org/
 * ml5.js license and Code of Conduct: https://github.com/ml5js/ml5-next-gen/blob/main/LICENSE.md
 *
 * This example demonstrates hand tracking on live video through ml5.handPose.
 */

let handPose;
let video;
let hands = [];

function preload() {
  // Load the handPose model
  handPose = ml5.handPose();
}

function setup() {
  createCanvas(640, 480);
  // Create the webcam video and hide it
  video = createCapture(VIDEO);
  video.size(640, 480);
  video.hide();
  // start detecting hands from the webcam video
  handPose.detectStart(video, gotHands);
}

function draw() {
  // Draw the webcam video
  image(video, 0, 0, width, height);

  // Draw all the tracked hand points
  for (let i = 0; i < hands.length; i++) {
    let hand = hands[i];
    for (let j = 0; j < hand.keypoints.length; j++) {
      let keypoint = hand.keypoints[j];
      fill(0, 255, 0);
      noStroke();
      circle(keypoint.x, keypoint.y, 10);
    }
  }
}

// Callback function for when handPose outputs data
function gotHands(results) {
  // save the output to the hands variable
  hands = results;
}
```

Introducción del Juego, sacada de un tutorial de Youtube
```javascript
let config = {
    x : 50,
    y : 50,
    w : 700,
    h : 500,
    noBalls : 75,
    firstX : 0,
    speed : 2
};

let colors = ["red", "blue", "orange", "green", "magenta", "teal", "pink", "yellow", "lime"];

setupDecor();

// Consider that the rect is a linear path and there is a dot on the path at coordinates linearX
// Function will find out the dot coordinates on the rect, starting from the top-left corner
function linearToRect(linearX, rectX, rectY, rectW, rectH)
{
    let w = (rectW + rectH) * 2;
    
    // Make sure that the dot is always on the rectangle
    linearX = linearX % w;
    
    // Check if the dot is on the top side
    if (linearX <= rectW)
        return { x : rectX + linearX, y : rectY};
        
    // Check if the dot is on the right side
    if (linearX <= rectW + rectH)
        return { x : rectX + rectW, y : rectY + linearX - rectW };
        
    // Check if the dot is on the bottom side
    if (linearX <= 2 * rectW + rectH )
        return { x : rectX + rectW - ( linearX - rectH - rectW ), y: rectY + rectH }
        
    // Otherwise the dot must by on the left side
    return { x : rectX, y : rectY + rectH - (linearX - 2 * rectW - rectH) }
}

function loop()
{
    update();
    display();
}

function update()
{
    let w = (config.w + config.h) * 2;
    config.firstX = (config.firstX + config.speed) % w;
}

function display()
{
    clear();
    
    noStroke();
    
    displayTitle();
    
    for(let i = 0; i < config.noBalls; i++)
    {
        displayBall(i);
    }
}

function displayBall(noBall)
{
    // Calculate the space between balls and then the linearX of current ball
    let space = (config.w + config.h) * 2 / config.noBalls;
    let ballX = config.firstX + noBall * space;
    
    let o = linearToRect(ballX, config.x, config.y, config.w, config.h);

    let hue = map(noBall, 0, config.noBalls, 10, 350);
    fill( colors[noBall % colors.length] );
    
    circle(o.x, o.y, 8);
}

function displayTitle()
{
    textSize(20);
    fill("Lime");
    
    text("Press S to start the game", 300, 500);
}

function setupDecor()
{
    background('GreenPool');
    
    sprite('blueLetter.C', 140, 180, 0.2);
    sprite('blueLetter.A', 200, 180, 0.2);
    sprite('blueLetter.N', 260, 180, 0.2);
    sprite('blueLetter.D', 320, 180, 0.2);
    sprite('blueLetter.Y', 380, 180, 0.2);
    
    sprite('blueLetter.M', 490, 180, 0.2);
    sprite('blueLetter.A', 550, 180, 0.2);
    sprite('blueLetter.S', 610, 180, 0.2);
    sprite('blueLetter.H', 670, 180, 0.2);
    
    sprite('dino.idle', 200, 500, 0.5);
    sprite('dino.idle', 600, 500, 0.5).mirrorX(-1);
}
```




## enlace del proyecto

Lo hicimos en editor de p5.js <https://editor.p5js.org/annais.bustamante/full/NRCjxHU75>

## documentación multimedia / audiovisual del proyecto funcionando

Introducción al Juego
1. Código inicial para la introducción del juego, en esta etapa solo habían círculos en blanco.
![a1](https://github.com/user-attachments/assets/ec29c0a2-4863-459a-87e1-5e47de92f700)

3. Se reemplazaron los círculos por Churus.
![a2](https://github.com/user-attachments/assets/742f322b-9e06-4361-a867-47ca1b77e20f)

5. Se intentó cambiar el fondo.
![a3](https://github.com/user-attachments/assets/8d3d490a-5096-4430-bf0d-3addd092eddf)

7. Se logró cambiar el fondo, probándose diferentes opciones de fondo.
![a6](https://github.com/user-attachments/assets/877e4915-f4c4-4949-96e1-7a60c0d81b35)
![a5](https://github.com/user-attachments/assets/72bb531e-29ec-4e36-9dd0-82298bae4849)
![a4](https://github.com/user-attachments/assets/72cb58a0-3eba-476c-a6a5-d2f61aca77c3)


Juego
1. Combinación entre el código de Hand Pose y el Juego de Olaf.
![b1](https://github.com/user-attachments/assets/e3f98e30-c9f5-4edc-8be1-f3f3839252aa)

3. Introducción y cierre del juego con Hand Pose ya aplicado.
![b2](https://github.com/user-attachments/assets/9e4f3379-886c-4da6-82e3-3ac1cc1e21d4)
![b3](https://github.com/user-attachments/assets/a4f4df43-690d-444c-ae5e-0e4eca72cdab)

5. Cambios de apariencia en el juego, esta vez se incorpora a Dorito y la FAU de fondo.
![c2](https://github.com/user-attachments/assets/28755286-bf3a-4a7a-8331-c42e87109df4)
![c1](https://github.com/user-attachments/assets/52827cf5-e571-4cca-8f23-be554d609ccd)

7. Segundo cambio de apariencia, esta es la estética final.
![c4](https://github.com/user-attachments/assets/1f28f3f4-b784-49cf-8644-d5a3a31d0e6c)
![c3](https://github.com/user-attachments/assets/e9382adb-cd13-4182-9cbf-b0d1d42c0be5)

9. Juego en Funcionamiento.
https://drive.google.com/file/d/1q_ukgBaikke4XZKunoK-XCXBVan144k-/view

Fusión de la Introducción y el Juego
1. Código de la animación del incio del juego.
![d1](https://github.com/user-attachments/assets/e189c05c-7b9f-4819-a2af-8f4d82053c72)

3. Código del juego mezclado con Hand Pose.
![d2](https://github.com/user-attachments/assets/f074f8e9-f768-4346-90fa-898bcc61889d)

5. Transición entre el código de la introducción y el código del juego.
![d3](https://github.com/user-attachments/assets/9129a9a3-de7f-4b01-a47c-42e6659cf313)

6. Ejemplo del juego funcionando con la introducción.
https://drive.google.com/file/d/16mnzvoNd2vD48e18xnRg6AhEp3O6CWTy/view

7. Se agregó en el código musica de fondo, un efecto de sonido para cuando Dorito se come los churus y sonido para cuando se pierde el juego
![d4](https://github.com/user-attachments/assets/8a0804a7-85de-4d82-a7be-80686aa4022e)

8. Video del juego funcionando con la música.
https://drive.google.com/file/d/1KeeddPRjgaHC9BJNsTTVfQW_t40SRXvu/view



## bibliografía

Utilizamos este tutorial: <https://www.youtube.com/watch?v=CIBKUC4TR18>

tomamos el código base de <https://editor.p5js.org/Cardenb/sketches/PatqtOUlk> y de <https://editor.p5js.org/ml5/sketches/QGH3dwJ1A>

Las ideas las sacamos de los siguientes videos: <https://www.youtube.com/watch?v=vfNHdVbE-l4>, <https://www.youtube.com/watch?v=K7b5MEhPCuo>, <https://www.youtube.com/watch?v=72pAzuD8tqE>, <https://github.com/AVAniketh0905/HandPose_Scrolling?utm_source=chatgpt.com>

Las imágenes utilizadas son de internet.


## conclusiones

1. Entre el lenguaje que utilizaba el tutorial y el lenguaje de pj5 había ligeras diferencias que cambiaban completamente la programación.
2. El Hand Pose tiene programado un efecto espejo que afecta dependiendo del tipo de cámara que se utilice.
3. Para que el código funcione correctamente es necesario mantener un orden claro en los nombres de los archivos, imágenes, tipografías, etc.
4. Para lograr que Dorito se mueva la mano se tiene que enfocar correctamente y sin ruido de fondo.
5. Se logró el objetivo de lograr un juego en el que para interactuar no es necesario tocar la pantalla.

## dimensión etica

Comprendiendo que se trata de un juego que manipula datos sensibles al tener acceso a la cámara de un dispositivo resulta de vital importancia la protección de éstos para que el usuario del juego no vea comprometida su privacidad o su imagen. También es importante recalcar que no se lleve a cabo ningún tipo de almacenamiento de lo que sea registrado por la cámara o que dichos registros no sean usados con ningún fin, a menos que esto sea declarado con anterioridad de forma clara y directa (véase para ayudar a entrenar al modelo incorporado en el juego o cualquier otro).
