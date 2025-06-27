# clase-14

# Asistente para personas tímidas en conciertos

## Introducción
En un comienzo, nuestra intención era trabajar con una adaptación de la obra Shape Shifter de la artista Maya Man, ya que nos interesaba su enfoque lúdico sobre el cuerpo y las formas generativas. Sin embargo, al analizar su estructura, nos dimos cuenta de que la implementación era más compleja de lo que esperábamos. Consideramos como alternativa utilizar la Basic Demo de ml5.js, pero tampoco logramos encontrar una dirección clara a partir de ella.
Durante la clase siguiente decidimos cambiar el enfoque del proyecto y orientarnos hacia algo más cercano a nuestras posibilidades técnicas y creativas. Retomamos el uso de Teachable Machine, como en el proyecto anterior, pero esta vez usando el modelo de imagen para trabajar directamente con la cámara en tiempo real.
Así surgió la idea de crear un asistente para personas tímidas en conciertos, una especie de representación sonora y gestual para quienes sienten incomodidad al expresarse en público. La propuesta combina tecnología de aprendizaje automático con una interfaz visual y sonidos pregrabados, generando una experiencia humorística e interactiva.

## Primera fase
Decidimos construir una interfaz muy simple, con un mensaje inicial claro y un solo botón, para enfatizar la idea de que el “asistente” es quien toma el protagonismo: es este quien, una vez activado, interpretará por ti acciones como aplaudir, gritar o celebrar. Es una forma irónica de “delegar” la emoción en una máquina.
Durante el desarrollo, optamos por combinar entradas de imagen con salidas de audio. Aunque el modelo solo reconoce imágenes, estas activan sonidos según la acción detectada. Para lograrlo, grabamos cerca de mil imágenes por cada categoría que el asistente puede identificar:
aplauso

1. abucheo
2. celebración
3. chiflido
4. grito
5. asombro
6. nada (neutro)


Este volumen de datos permitió entrenar el modelo con suficiente precisión para distinguir expresiones similares entre sí, como aplaudir y celebrar, que a menudo se confunden porque implican movimientos parecidos con las manos.


## Proceso de entrenamiento - Teachable machine

![unnamed](https://github.com/user-attachments/assets/840674ef-72c4-4fae-b173-65f6ae9339f7)

![unnamed (1)](https://github.com/user-attachments/assets/b8d305f7-a3ad-46d6-90df-555a154eb5da)


## División del trabajo
El proyecto, durante la primera fase, lo trabajamos colaborativamente para definir y entrenar el modelo de Teachable Machine con las imágenes necesarias. Posteriormente, dividimos las tareas para avanzar en paralelo:
@RafaFerrari: responsable del manejo de audio, selección y asociación de sonidos para cada gesto reconocido.


@antoniapozo: encargada de diseñar y programar la interfaz visual sencilla, así como la integración con la cámara y la lógica de interacción.


Esta división permitió un desarrollo más eficiente y un aporte equilibrado de ambos integrantes.

![unnamed (2)](https://github.com/user-attachments/assets/8f9b25b5-cf30-486e-8b11-10127f13aaff)


![unnamed (3)](https://github.com/user-attachments/assets/576247b0-dbf6-4dd4-9269-7b1e7117abfa)



## Código

// Variables globales para cámara, clasificación y sonidos
let cam; // Entrada de video desde la cámara
let classifier; // Clasificador de imágenes (modelo de Teachable Machine)
let label = "waiting..."; // Etiqueta actual que retorna el modelo
let lastLabel = ""; // Última etiqueta detectada (para no repetir sonidos)
let modelURL = 'https://teachablemachine.withgoogle.com/models/lCQDVXK5O/';

// Variables para los sonidos asociados a cada etiqueta
let soundaplauso, soundabucheo, soundcelebración;
let soundchiflido, soundgrito, soundsorprendido;

// Variables para la interfaz de bienvenida
let boton; // Botón para comenzar
let mostrarCamara = false; // Estado de la cámara (encendida o no)
let mostrarInstruccion = false; // Indica si se muestra la instrucción inicial
let colores = []; // Colores para el texto animado
let t = 0; // Variable de tiempo para animaciones

// Precarga del modelo de ML y de los sonidos antes de iniciar el sketch
function preload() {
  classifier = ml5.imageClassifier(modelURL + 'model.json'); // Carga del modelo desde Teachable Machine

  // Carga de archivos de sonido
  soundaplauso = loadSound('aplauso.mp3');
  soundabucheo = loadSound('abucheo.mp3');
  soundcelebración = loadSound('celebración.mp3');
  soundchiflido = loadSound('chiflido.mp3');
  soundgrito = loadSound('grito.mp3');
  soundsorprendido = loadSound('sorprendido.mp3');
}

function setup() {
  createCanvas(1080, 720); // Crear lienzo principal
  frameRate(30); // Controla la velocidad de actualización del sketch

  // Crear botón "Comenzar" y asignarle una función al hacer clic
  boton = createButton('Comenzar');
  boton.position(width / 2 - 50, height / 2 + 120);
  boton.mousePressed(iniciarAsistente);

  // Estilización del botón
  boton.style('font-family', 'Caveat Brush, cursive');
  boton.style('font-size', '24px');
  boton.style('color', '#fff');
  boton.style('background', 'transparent');
  boton.style('border', '2px solid #fff');
  boton.style('padding', '10px 20px');
  boton.style('cursor', 'pointer');

  // Estilo general del texto
  textFont('Caveat Brush');
  textAlign(CENTER, CENTER);
  textSize(28);

  // Paleta de colores para el texto animado
  colores = [
    color('#FF69B4'), // rosado
    color('#87CEFA'), // celeste
    color('#FFFF66')  // amarillo claro
  ];
}

function draw() {
  background(0); // Fondo negro

  // Si la cámara aún no ha sido activada, mostrar la pantalla de bienvenida
  if (!mostrarCamara) {
    // Mensajes informativos para la introducción
    let mensajes = [
      "¿Eres una persona tímida y te da vergüenza",
      "interactuar en un concierto?",
      "¡No te preocupes! Este asistente está diseñado",
      "especialmente para ayudarte"
    ];

    // Mostrar mensajes con colores animados y oscilación vertical
    for (let i = 0; i < mensajes.length; i++) {
      fill(colores[floor((frameCount / 10 + i) % colores.length)]);
      let yOffset = sin(t + i) * 5; // Movimiento oscilante
      text(mensajes[i], width / 2, height / 2 - 90 + i * 30 + yOffset);
    }

    // Animación del botón para dar efecto de vibración
    let vibX = sin(t * 3) * 3;
    let vibY = cos(t * 2) * 3;
    boton.position(width / 2 - 50 + vibX, height / 2 + 120 + vibY);

    t += 0.05; // Aumentar el tiempo para animaciones

  } else {
    // Mostrar imagen de la cámara una vez activada
    if (cam) {
      image(cam, 0, 0, width, height);
    }

    fill(255); // Color blanco para el texto

    // Mostrar la etiqueta detectada por el modelo
    textSize(32);
    text(label, width / 2, height - 20);

    // Mostrar instrucción temporal para guiar al usuario
    if (mostrarInstruccion) {
      textSize(22);
      text("Intenta aplaudir", width / 2, height - 60);
    }
  }
}

// Función que se ejecuta al hacer clic en el botón
function iniciarAsistente() {
  boton.hide(); // Oculta el botón
  mostrarCamara = true; // Activa la visualización de la cámara

  // Configuración de la cámara
  cam = createCapture(VIDEO);
  cam.size(width, height);
  cam.hide();

  classifyVideo(); // Inicia la clasificación del video

  // Mostrar la instrucción inicial por unos segundos
  mostrarInstruccion = true;
  setTimeout(() => {
    mostrarInstruccion = false;
  }, 5000); // Oculta el texto después de 5 segundos
}

// Función que ejecuta la clasificación de cada frame del video
function classifyVideo() {
  classifier.classify(cam, gotResults);
}

// Función de callback que maneja los resultados del clasificador
function gotResults(error, results) {
  if (error) {
    console.error(error); // Imprime el error en la consola
    return;
  }

  let currentLabel = results[0].label; // Toma la etiqueta con mayor probabilidad
  label = currentLabel;

  // Reproduce sonido solo si la etiqueta cambió
  if (currentLabel !== lastLabel) {
    playSoundForLabel(currentLabel);
    lastLabel = currentLabel;
  }

  classifyVideo(); // Llama recursivamente para seguir clasificando
}

// Función que reproduce el sonido correspondiente a la etiqueta detectada
function playSoundForLabel(label) {
  // Detener cualquier sonido que esté en reproducción
  soundaplauso.stop();
  soundabucheo.stop();
  soundcelebración.stop();
  soundchiflido.stop();
  soundgrito.stop();
  soundsorprendido.stop();

  // Reproducir el sonido correcto según la etiqueta
  if (label === "Aplauso") {
    soundaplauso.play();
  } else if (label === "Abucheo") {
    soundabucheo.play();
  } else if (label === "Celebración") {
    soundcelebración.play();
  } else if (label === "Chiflido") {
    soundchiflido.play();
  } else if (label === "Grito") {
    soundgrito.play();
  } else if (label === "Asombro") {
    soundsorprendido.play();
  }
}


## Link

https://editor.p5js.org/rafael.ferrari/sketches/46bJhTYnd

## Conclusiones

Una de las decisiones más importantes que tomamos fue priorizar la cantidad y calidad de información que entregamos al modelo de Teachable Machine, ya que varias de las expresiones que queríamos representar se parecen entre sí (por ejemplo, aplaudir y celebrar, o gritar y asombrarse). Por eso, grabamos cerca de mil muestras por categoría, buscando generar una base de datos sólida y variada. Esto ayudó a mejorar la precisión del asistente y facilitó que muchas de las acciones se reconocen correctamente en tiempo real.
Aun así, sabemos que el proyecto no es perfecto. El modelo todavía presenta algunas confusiones, especialmente entre gestos que implican movimientos similares o posiciones corporales poco diferenciadas. Reconocemos que este tipo de errores son parte del límite técnico de trabajar con un modelo de imagen simple, pero también son parte del aprendizaje del proceso.
Más allá de eso, consideramos que el asistente es capaz de captar varias expresiones con éxito, y que en conjunto, el sistema logra comunicar su intención de manera clara. La propuesta funciona como una experiencia directa, humorística y ligera, y sentimos que logramos traducir bien esa idea a través del código, la interfaz y el entrenamiento del modelo.
Finalmente, fue muy enriquecedor experimentar con la combinación de imagen y audio, algo que no habíamos explorado en el proyecto anterior. Nos divertimos mucho entrenando al asistente, buscando sonidos representativos y diseñando una interfaz sencilla que acompañará el concepto de una tecnología que “habla por ti”.

