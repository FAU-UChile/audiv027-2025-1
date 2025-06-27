# clase-13
javascript
// Variables globales para cámara, botón, estado y colores
let cam;
let boton;
let mostrarCamara = false;      // Controla si se muestra la cámara o no
let mostrarInstruccion = false; // Controla si se muestra la instrucción en pantalla
let colores = [];               // Array para guardar colores tipo luces de concierto
let t = 0;                     // Variable para animar el texto y vibración

function setup() {
  createCanvas(640, 480);      // Crear lienzo de dibujo
  frameRate(30);               // Limitar a 30 cuadros por segundo

  // Crear botón "Comenzar" y posicionarlo en el canvas
  boton = createButton('Comenzar');
  boton.position(width / 2 - 50, height / 2 + 120);
  boton.mousePressed(iniciarAsistente); // Función que se ejecuta al presionar el botón

  // Estilizar botón para que tenga la fuente Caveat Brush y estilo visual
  boton.style('font-family', 'Caveat Brush, cursive');
  boton.style('font-size', '24px');
  boton.style('color', '#fff');
  boton.style('background', 'transparent');
  boton.style('border', '2px solid #fff');
  boton.style('padding', '10px 20px');
  boton.style('cursor', 'pointer');

  // Configurar texto con la fuente Caveat Brush y centrado
  textFont('Caveat Brush');
  textAlign(CENTER, CENTER);
  textSize(28);

  // Definir colores para animar el texto con tonos rosado, celeste y amarillo
  colores = [
    color('#FF69B4'), // Rosado
    color('#87CEFA'), // Celeste
    color('#FFFF66')  // Amarillo
  ];
}

function draw() {
  background(0); // Fondo negro para dar sensación de escenario

  if (!mostrarCamara) {
    // Mostrar mensajes iniciales animados antes de activar la cámara
    let mensajes = [
      "¿Eres una persona tímida y te da vergüenza",
      "interactuar en un concierto?",
      "¡No te preocupes! Este asistente está diseñado",
      "especialmente para ayudarte 😊"
    ];

    // Ciclo para mostrar cada línea de texto con color y movimiento
    for (let i = 0; i < mensajes.length; i++) {
      // Elegir color cíclico para cada línea, usando floor para índice entero
      fill(colores[floor((frameCount / 10 + i) % colores.length)]);
      // Efecto de movimiento vertical sutil con función seno
      let yOffset = sin(t + i) * 5;
      // Mostrar texto centrado en pantalla con desplazamiento vertical
      text(mensajes[i], width / 2, height / 2 - 90 + i * 30 + yOffset);
    }

    // Animar vibración suave del botón para hacerlo más dinámico
    let vibX = sin(t * 3) * 3;
    let vibY = cos(t * 2) * 3;
    boton.position(width / 2 - 50 + vibX, height / 2 + 120 + vibY);

    t += 0.05; // Incrementar variable para animaciones

  } else {
    // Mostrar la cámara si ya se activó
    if (cam) {
      image(cam, 0, 0, width, height);
    }

    // Mostrar instrucción para animar a aplaudir por un tiempo limitado
    if (mostrarInstruccion) {
      fill(255); // Texto blanco
      textFont('Caveat Brush');
      textSize(22);
      text("intenta aplaudir", width / 2, height - 40);
    }
  }
}

// Función que se llama al presionar el botón "Comenzar"
function iniciarAsistente() {
  boton.hide();           // Ocultar botón para dejar espacio a la cámara
  mostrarCamara = true;   // Cambiar estado para mostrar cámara

  // Crear y configurar la cámara
  cam = createCapture(VIDEO);
  cam.size(width, height);
  cam.hide();             // Ocultar el elemento HTML de la cámara (se muestra en canvas)

  // Mostrar la instrucción de aplaudir por 5 segundos
  mostrarInstruccion = true;
  setTimeout(() => {
    mostrarInstruccion = false;
  }, 5000);
}


https://editor.p5js.org/antoniapozo/full/NdoeBJN_d

Esta es sólo la primera parte de la interfaz, para luego dar comienzo a la interacción (cámara y audio) 
