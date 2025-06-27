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


