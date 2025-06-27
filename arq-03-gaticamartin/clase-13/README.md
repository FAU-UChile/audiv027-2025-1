# clase-13
 ## Ajustes Modelo terremotos

 Codigo con ajustes de visualizacion: de texto a circunferencias
 -las circunferencias tienen un radio segun la magnitud del sismo
 -la opacidad de la circunferencia va bajando, del 100% apenas se reporta, hasta el 0% 24 horas despues de transcurrido.

```python

 let sismos = [];
let datosCargados = false;
let cargando = false;

let sismosVisuales = [];
let modelo;
let prediccion = null;

function setup() {
  createCanvas(1080, 720);
  textAlign(CENTER, CENTER);
  textSize(16);
  fill(255);
}

function draw() {
  background(0);

  if (!datosCargados && !cargando) {
    text("Click para cargar datos sísmicos", width / 2, height / 2);
    return;
  }

  if (cargando) {
    text("Cargando datos sísmicos...", width / 2, height / 2);
    return;
  }

  // Dibujar sismos activos
  sismosVisuales = sismosVisuales.filter(sv => sv.draw());

  // Mostrar texto del último sismo
  if (sismosVisuales.length > 0) {
    let ultimo = sismosVisuales[sismosVisuales.length - 1];
    fill(255);
    textAlign(CENTER, TOP);
    text(`Último sismo: ${ultimo.sismo.Fecha} - M${ultimo.sismo.Magnitud}`, width / 2, 10);
  }

  // Mostrar predicción
  if (prediccion !== null) {
    fill(255, 100, 100);
    textAlign(CENTER, BOTTOM);
    text(`Predicción magnitud próxima: ${prediccion.toFixed(2)}`, width / 2, height - 30);
  }
}

function mousePressed() {
  if (!datosCargados && !cargando) {
    cargando = true;
    let url = 'https://corsproxy.io/?https://api.gael.cloud/general/public/sismos';
    loadJSON(url, gotData, 'json', errorCarga);
  }
}

function gotData(data) {
  sismos = data;
  datosCargados = true;
  cargando = false;

  // Filtrar últimos 24h
  let ahora = new Date();
  let hace24Horas = new Date(ahora.getTime() - 24 * 60 * 60 * 1000);
  sismosVisuales = [];

  for (let sismo of sismos) {
    let fechaSismo = new Date(sismo.Fecha);
    if (fechaSismo > hace24Horas) {
      sismosVisuales.push(new SismoVisual(sismo));
    }
  }

  // Modelo de IA
  crearModelo()
    .then(() => entrenarModelo())
    .then(() => {
      prediccion = predecirProximoSismo();
      console.log("Predicción próxima magnitud:", prediccion);
    });
}

function errorCarga(err) {
  cargando = false;
  datosCargados = false;
  console.error("Error al cargar datos:", err);
}


// Clase visual sismo

class SismoVisual {
  constructor(sismo) {
    this.sismo = sismo;
    this.inicio = new Date(sismo.Fecha).getTime();
    this.radio = map(sismo.Magnitud, 0, 8, 20, width); // Más separación
  }

  draw() {
    let ahora = Date.now();
    let dif = ahora - this.inicio;
    let duracion = 24 * 60 * 60 * 1000;

    if (dif > duracion) return false;

    // Opacidad más extrema (curva cuadrática)
    let progreso = dif / duracion;
    let alpha = map(progreso * progreso, 0, 1, 255, 0);

    // Grosor: más reciente = más grueso
    let grosor = map(progreso, 0, 1, 6, 1);

    noFill();
    stroke(255, alpha);
    strokeWeight(grosor);
    ellipse(width / 2, height / 2, this.radio * 2);

    return true;
  }
}


```

##Modificar:



*Circunferencias segun Magnitud:Magnitud 8 = Ancho maximo de pantalla.
-20 px cuando la magnitud ≈ 0
-1080 px cuando la magnitud = 8.0
*Agregar Lenguaje distintivo al registro mas reciente pulsacion(?)
*Mover los textos al centro de la pantalla.

```python
let sismos = [];
let datosCargados = false;
let cargando = false;

let sismosVisuales = [];
let modelo;
let prediccion = null;

function setup() {
  createCanvas(1080, 720);
  textAlign(CENTER, CENTER);
  textSize(16);
  fill(255);
}

function draw() {
  background(0);

  if (!datosCargados && !cargando) {
  text("...La patria delgada,\n la orilla del páramo andino,\n la tierra que dio en su angostura la uva celeste\n y el cobre absoluto...\n padece otra vez el espanto y la grieta.", width / 2, height / 2);
    return;
  }

  if (cargando) {
    text("Siguiendo las vibraciones...", width / 2, height / 2);
    return;
  }

  sismosVisuales = sismosVisuales.filter(sv => sv.draw());

  // Mostrar info en el centro
  if (sismosVisuales.length > 0) {
    let ultimo = sismosVisuales[sismosVisuales.length - 1];
    let masFuerte = sismosVisuales.reduce((max, sv) => {
      return (sv.sismo.Magnitud > max.sismo.Magnitud) ? sv : max;
    }, sismosVisuales[0]);

    fill(255);
    textAlign(CENTER, CENTER);
    textSize(16);
    text(`Último Sismo: ${ultimo.sismo.Fecha} - M${ultimo.sismo.Magnitud}`, width / 2, height / 2 - 10);

    text(`\nMayor Magnitud: M${masFuerte.sismo.Magnitud} \n ${masFuerte.sismo.RefGeografica}`, width / 2, height / 2 + 15);
  }

  // Predicción IA (opcional)
  if (prediccion !== null) {
    fill(255, 100, 100);
    textAlign(CENTER, BOTTOM);
    textSize(16);
    text(`Predicción magnitud próxima: ${prediccion.toFixed(2)}`, width / 2, height - 30);
  }
}

function mousePressed() {
  if (!datosCargados && !cargando) {
    cargando = true;
    let url = 'https://corsproxy.io/?https://api.gael.cloud/general/public/sismos';
    loadJSON(url, gotData, 'json', errorCarga);
  }
}

function gotData(data) {
  sismos = data;
  datosCargados = true;
  cargando = false;

  let ahora = new Date();
  let hace24Horas = new Date(ahora.getTime() - 24 * 60 * 60 * 1000);
  sismosVisuales = [];

  for (let sismo of sismos) {
    let fechaSismo = new Date(sismo.Fecha);
    if (fechaSismo > hace24Horas) {
      sismosVisuales.push(new SismoVisual(sismo));
    }
  }

  crearModelo()
    .then(() => entrenarModelo())
    .then(() => {
      prediccion = predecirProximoSismo();
      console.log("Predicción próxima magnitud:", prediccion);
    });
}

function errorCarga(err) {
  cargando = false;
  datosCargados = false;
  console.error("Error al cargar datos:", err);
}


// visual sismo

class SismoVisual {
  constructor(sismo) {
    this.sismo = sismo;
    this.inicio = new Date(sismo.Fecha).getTime();
    this.radioBase = map(sismo.Magnitud, 0, 8, 10, width / 2);
  }

  draw() {
    let ahora = Date.now();
    let dif = ahora - this.inicio;
    let duracion = 24 * 60 * 60 * 1000;

    if (dif > duracion) return false;

    let progreso = dif / duracion;
    let alpha = map(progreso * progreso, 0, 1, 255, 0);
    let grosor = map(progreso, 0, 1, 6, 1);

    let esUltimo = this === sismosVisuales[sismosVisuales.length - 1];
    let radioActual = this.radioBase;

    // Agregar latido si es el más reciente
    if (esUltimo) {
      let pulso = 1 + 0.03 * sin(frameCount * 0.05); // pulso suave: primer indicador regula el rango de movimiento, framecount la velocidad con la que pulsa.
      radioActual *= pulso;
    }

    noFill();
    stroke(255, alpha);
    strokeWeight(grosor);
    ellipse(width / 2, height / 2, radioActual * 2);

    return true;
  }
}
```

##Agregar: IA

-Agregar ia que pueda predecir magnitud, locacion y fecha y hora en base a x sismos anteriores.
-que esta funcion se despliegue al clickear la pan9talla nuevamente, siguiendo un texto en rojo(?) en la parte inferior de pantalla.
-al hacer click se pasa a una pantalla de carga por tres segundos con una imagen de fondo y el texto "pensando..."


## Codigo Final

```python

let sismos = [];
let datosCargados = false;
let cargando = false;
let tiempoInicioCarga = 0;
let estado = 0; // 0 = poema, 1 = sismos, 2 = cargando predicción, 3 = predicción

let sismosVisuales = [];
let modelo;
let prediccion = null;
let fechaPredicha = "";
let lugarPredicho = "";

function setup() {
  createCanvas(1080, 720);
  textAlign(CENTER, CENTER);
  textSize(16);
  fill(255);
}

function draw() {
  background(0);

  switch (estado) {
    case 0:
      // Pantalla Poema Vibrante
      let amplitud = 2;
      let frecuencia = 0.1;
      let offsetX = sin(frameCount * frecuencia * 1.3) * amplitud;
      let offsetY = sin(frameCount * frecuencia * 0.9) * amplitud;
      text(
        
        "...La patria delgada,\n la orilla del páramo andino,\n la tierra que dio en su angostura la uva celeste\n y el cobre absoluto...\n padece otra vez el espanto y la grieta.",
        
        width / 2 + offsetX,
        height / 2 + offsetY
        
      );
          text("...La patria delgada,\n la orilla del páramo andino,\n la tierra que dio en su angostura la uva celeste\n y el cobre absoluto...\n padece otra vez el espanto y la grieta.", width / 2, height / 2);
      break;

    case 1:
      // Pantalla de Sismos
      sismosVisuales = sismosVisuales.filter((sv) => sv.draw());

      if (sismosVisuales.length > 0) {
        let ultimo = sismosVisuales[sismosVisuales.length - 1];
        let masFuerte = sismosVisuales.reduce((max, sv) => {
          return sv.sismo.Magnitud > max.sismo.Magnitud ? sv : max;
        }, sismosVisuales[0]);

        fill(255);
        textAlign(CENTER, CENTER);
        textSize(16);
        text(
          `Último Sismo: ${ultimo.sismo.Fecha} - M${ultimo.sismo.Magnitud}`,
          width / 2,
          height / 2 - 10
        );
        text(
          `\nMayor Magnitud: M${masFuerte.sismo.Magnitud} \n ${masFuerte.sismo.RefGeografica}`,
          width / 2,
          height / 2 + 15
        );
                fill(255, 0, 0);
        textAlign(CENTER, CENTER);
        textSize(20);
        text("\ncuando será el proximo?", width / 2, height - 50);
      }
      break;

 case 2:
  let tiempoTranscurrido = millis() - tiempoInicioCarga;
  if (tiempoTranscurrido < 3000) {
    // Imagen de fondo
   // imageMode(CORNER);
    //image(imgCarga, 0, 0, width, height);

    // Texto encima
    fill(255);
    textAlign(CENTER, CENTER);
    textSize(24);
    text("*piensa en Marcelo Lagos*", width / 2, height / 2);
  } else if (prediccion !== null) {
    estado = 3; // Ir a pantalla de predicción
  }
  break;

    case 3:
      // Pantalla de Predicción
      fill(255, 0, 0);
      textAlign(CENTER, CENTER);
      textSize(45);
      text("Prediccion del Próximo Sismo:", width / 2, height / 2 - 60);
      textSize(30);
      text(`\nMagnitud estimada: M${prediccion.toFixed(2)}`, width / 2, height / 2 - 20);
      text(`\nFecha estimada: ${fechaPredicha}`, width / 2, height / 2 + 10);
      text(`\n\nUbicación estimada: ${lugarPredicho}`, width / 2, height / 2 + 40);
      textSize(30);
      text("...", width / 2, height - 40);
      break;
  }
}

function mousePressed() {
  if (estado === 0 && !datosCargados && !cargando) {
    cargando = true;
    tiempoInicioCarga = millis();
    let url = "https://corsproxy.io/?https://api.gael.cloud/general/public/sismos";
    loadJSON(url, gotData, "json", errorCarga);
  } else if (estado === 1) {
    // Ir a pantalla de carga de predicción
    estado = 2;
    tiempoInicioCarga = millis();
    prediccion = null;

    setTimeout(() => {
      let prediccionCompleta = simularPrediccion();
      prediccion = prediccionCompleta.magnitud;
      fechaPredicha = prediccionCompleta.fecha.toLocaleString();
      lugarPredicho = prediccionCompleta.lugar;
    }, 1200);
  } else if (estado === 3) {
    estado = 1; // Volver a visualización
  }
}

function gotData(data) {
  let tiempoTranscurrido = millis() - tiempoInicioCarga;
  let tiempoRestante = 1000 - tiempoTranscurrido;

  if (tiempoRestante > 0) {
    setTimeout(() => {
      procesarDatos(data);
    }, tiempoRestante);
  } else {
    procesarDatos(data);
  }
}

function procesarDatos(data) {
  sismos = data;
  datosCargados = true;
  cargando = false;
  estado = 1; // Cambiar a pantalla de visualización

  let ahora = new Date();
  let hace24Horas = new Date(ahora.getTime() - 24 * 60 * 60 * 1000);
  sismosVisuales = [];

  for (let sismo of sismos) {
    let fechaSismo = new Date(sismo.Fecha);
    if (fechaSismo > hace24Horas) {
      sismosVisuales.push(new SismoVisual(sismo));
    }
  }

  crearModelo()
    .then(() => entrenarModelo())
    .then(() => {
      // predicción al hacer clic
    });
}

function errorCarga(err) {
  cargando = false;
  datosCargados = false;
  console.error("Error al cargar datos:", err);
}

// Simula la predicción completa (PREDICCION FALSA POR AHORA): magnitud, fecha y ubicación
function simularPrediccion() {
  let magnitud = random(4, 7);

  // Fecha futura entre 2 y 24 horas desde ahora
  let fechaFutura = new Date();
  let horasAdelanto = floor(random(2, 36));
  fechaFutura.setHours(fechaFutura.getHours() + horasAdelanto);

  // Array con posibles ubicaciones ficticias
  let lugares = [
    "Zona costera",
    "Altiplano",
    "Región central",
    "Norte de Chile",
    "Sur de Chile",
    "Talca",
    "Falla de San Ramón",
    "Antartica Chilena"
  ];
  let lugar = random(lugares);

  return {
    magnitud: magnitud,
    fecha: fechaFutura,
    lugar: lugar,
  };
}

// Clase visual sismo
class SismoVisual {
  constructor(sismo) {
    this.sismo = sismo;
    this.inicio = new Date(sismo.Fecha).getTime();
    this.radioBase = map(sismo.Magnitud, 0, 8, 10, width / 2);
  }

  draw() {
    let ahora = Date.now();
    let dif = ahora - this.inicio;
    let duracion = 24 * 60 * 60 * 1000;

    if (dif > duracion) return false;

    let progreso = dif / duracion;
    let alpha = map(progreso * progreso, 0, 1, 255, 0);
    let grosor = map(progreso, 0, 1, 6, 1);

    let esUltimo = this === sismosVisuales[sismosVisuales.length - 1];
    let radioActual = this.radioBase;

    if (esUltimo) {
      let pulso = 1 + 0.03 * sin(frameCount * 0.05);
      radioActual *= pulso;
    }

    noFill();
    stroke(255, alpha);
    strokeWeight(grosor);
    ellipse(width / 2, height / 2, radioActual * 2);

    return true;
  }
}

// Funciones dummy para crearModelo y entrenarModelo (para evitar errores)
async function crearModelo() {
  // Aquí iría la lógica para crear modelo TensorFlow.js
  return;
}
async function entrenarModelo() {
  // Aquí iría la lógica para entrenar modelo TensorFlow.js
  return;
}
```

