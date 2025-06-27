# PRESENTACION FINAL

## Primera Aproximacion

IDEA INICIAL PROYECTO PROGRAMACION

*pagina pagina relacionada a sismologia nacional
conectar API´s con p5
desarrollar lenguaje grafico de interes*
-- *idea grafica inicialfondo horizontal 1080x720 fondo negro,cada vez que se registra un sismo se dibuja en el centro de la pantalla una elipse de radio relacionado a la magnitud del sismo. esta circunferencia se desbanece lentamente hasta desaparecer 24 h despues. los limites de la pantalla son equivalentes a un sismo de grado considerable* (?????) 

links

https://earthquake.usgs.gov/fdsnws/event/1/
https://www.getambee.com/api/earthquake
https://www.sismologia.cl/sismicidad/catalogo/2025/04/20250411.html
.

https://commonvoice.mozilla.org/en <- web entrenar reconocimiento de voz personalizado

https://editor.p5js.org/montoyamoraga/sketches/5zLOhCZta <- link modelo reconocimiento voz don francisco

## Idea Inicial

Representacion en tiempo real de los sismos de Chile, en un lenguaje que represente su magnitud y de alguna manera la fecha y lugar, al menos de los producidos en las ultimas 24h con el fin de representar Chile como un pais sismico

## Utilizacion de API

API DE GAEL #GRACIASGAEL
![image](https://github.com/user-attachments/assets/86d265d0-ef93-4769-ba3a-ef73160e8227)

#### Datos que entrega:

    "Fecha": "2025-06-06 15:04:30",
    "Profundidad": "253",
    "Magnitud": "3.5",
    "RefGeografica": "53 km al S de Socaire",
    "FechaUpdate": "2025-06-06T15:55:01.343Z"

#### Reales datos de interes:

    "Fecha": "2025-06-06 15:04:30",
    "Magnitud": "3.5",

### Primer Intento de Codigo

```python
    //En base a la base de datos API de Gael Cloud "https://api.gael.cloud/general/public/sismos"

    let sismos = [];

    function setup() {
      // Lienzo en 1080p
      createCanvas(1920,1080);

      //Investigar Bien lo siguiente: No lo entiendo.
  
      // URL del proxy para evitar problemas de CORS al acceder a la API externa
      // CORSProxy.io actúa como intermediario entre el navegador y la API real
      let url = 'https://corsproxy.io/?https://api.gael.cloud/general/public/sismos';

      // Cargamos el JSON desde la URL (con proxy) y especificamos qué hacer cuando cargue
      // 'gotData' es la función que se ejecutará si carga bien
      // 'errorCarga' se ejecuta si hay un error
      loadJSON(url, gotData, 'json', errorCarga);
    }

    // Esta función se ejecuta cuando los datos JSON se han cargado correctamente
    function gotData(data) {
      // Guardamos los datos de sismos en la variable global
      sismos = data;
      // Mostramos los datos en la consola del navegador para inspeccionarlos
      console.log(sismos);
    }

    // si ocurre un error al cargar los datos
    function errorCarga(err) {
      // Mostramos el error en la consola para diagnóstico
      console.error("Error al cargar los sismos:", err);
    }

     // Visualizacion

    function draw() {
      // color Lienzo
      background(0); //negro(?)

       // estilo texto
      textSize(22);
      fill(255); //blanco(?)
      textAlign(LEFT, TOP);

      // Verificar si ya se han cargado los datos de sismos
      if (sismos.length > 0) {
        // Iterar primeros 10 sismos para mostrarlos 
        for (let i = 0; i < sismos.length; i++) {
          let sismo = sismos[i]; // Obtenemos el sismo actual

     //Seleccion de datos     
      
          // Texto en pantalla de fecha y magnitud
          //  para agregar ubicación: - ${sismo.RefGeografica}
          text(`${sismo.Fecha} - M${sismo.Magnitud}`, 10, 30 + i * 20);
      

          // Limitar la cantidad de sismos, en este caso hasta 10
          if (i > 10) break;
        }
      } else {
        // Mensaje de carga, PENSAR UNO MEJOR.
        text("Siguiendo las vibraciones en la tierra...", 10, 10);
          // estilo texto
  
      }
    }
```
![image](https://github.com/user-attachments/assets/a9b88b45-a228-4a6e-8f2f-f1ca3748a70f)


   #### ACOTACIONES

   - en este codigo ya se reproducen los datos de interes y se solucionaron posibles problemas de proxy
   - hay que reemplazar la seleccion de sismos mostrados de los ultimos 10 a todos los sismos registrados durante las ultimas 24h.
   - para esto falta agregar a la definicion la nocion de hora y fecha actual para generar la relacion entre el tiempo transcurrido y la fecha/hora de el sismo
   - centrar los textos
   - aun hay que convertir estos datos a el lenguaje visual deseado.
   - quizas programar que los datos se muestren a partir de un click o tap en la pantalla, y antes de eso solo mostrar una determinada cita que haga referencia a la relacion de el paisaje nacional con su naturaleza sismica.
   - 

### Segundo Intento

    // En base a la base de datos API de Gael Cloud "https://api.gael.cloud/general/public/sismos"

```python
    function draw() {
      background(0); // fondo negro

      // Si aún no se ha hecho click
      if (!datosCargados && !cargando) {
        text("Otra vez, otra vez el caballo iracundo patea el planeta\n" +
                  "y escoge la patria delgada, la orilla del páramo andino,\n" +
                  "la tierra que dio en su angostura la uva celeste y el cobre absoluto,\n" +
                  "otra vez...\n" +
                  "...y padece otra vez el espanto y la grieta.", width / 2, height / 2);
        return;
      }

      // Si está cargando datos
      if (cargando) {
        text("Cargando datos sísmicos...", width / 2, height / 2);
        return;
      }

      // Si los datos ya están cargados y listos
      if (sismos.length > 0) {
        let ahora = new Date();
        let hace24Horas = new Date(ahora.getTime() - (24 * 60 * 60 * 1000));

    // Filtrar sismos de las últimas 24 horas
    let recientes = sismos.filter(sismo => {
      let fecha = new Date(sismo.Fecha);
      return fecha > hace24Horas;
    });

    let lineaAltura = 30;
    let bloqueAltura = recientes.length * lineaAltura;
    let startY = (height - bloqueAltura) / 2;

    if (recientes.length > 0) {
      for (let i = 0; i < recientes.length; i++) {
        let sismo = recientes[i];
      // Texto en pantalla de fecha y magnitud
      //  para agregar ubicación: - ${sismo.RefGeografica}
        let texto = `${sismo.Fecha} - M${sismo.Magnitud}`;
        text(texto, width / 2, startY + i * lineaAltura); //texto centrado
      }
      //en el caso remoto de que no se registren sismos en las ultimas 24h
    } else {
      text(":o", width / 2, height / 2);
        }
      }
    }
    
    // responde al hacer click
    function mousePressed() {
      if (!datosCargados && !cargando) {
        cargando = true; // empezamos a cargar
        let url = 'https://corsproxy.io/?https://api.gael.cloud/general/public/sismos';
        loadJSON(url, gotData, 'json', errorCarga);
      }
    }

    // API responde exitosamente
    function gotData(data) {
      sismos = data;
      datosCargados = true;
      cargando = false;
      console.log(sismos);
    }

    // en caso de error
    function errorCarga(err) {
      cargando = false;
      datosCargados = true;
      console.error("Error :c", err);
    }
```

  #### ACOTACIONES

  - falta convertir datos en lenguaje grafico

## Desarrollo de Lenguaje Grafico

#### se añade pantalla de inicio

```python
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

    ```
```
![image](https://github.com/user-attachments/assets/55950772-3e70-4f21-a172-1ccd0923f6a6)

#### Se implementa traduccion de magnitudes a circunferencias en pantalla 

```python

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
```

 #### Y se suman Datos filtrados: Mayor Magnitud y Ultimo Sismo

```python
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

        ```
```
![image](https://github.com/user-attachments/assets/f8169366-9c5e-4d78-9de6-f42e497df74e)
```
## Implementacion de IA (?)

Entrenamiento de modelo TensorFlow.js

Prediccion en base a ultimos 3 - 5 registros 
Predecir MAgnitud, Fecha, Hora y ubicacion (?)

https://www.google.com/search?q=tutorial+tensorflow+training&rlz=1C1YTUH_esCL1001CL1001&oq=tutorial+tensorflow+training&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIICAEQABgWGB4yBwgCEAAY7wUyBwgDEAAY7wUyBwgEEAAY7wUyBwgFEAAY7wUyBwgGEAAY7wXSAQg2MTQ1ajBqOagCALACAA&sourceid=chrome&ie=UTF-8#fpstate=ive&vld=cid:cf4662cc,vid:6_2hzRopPbQ,st:0 (????)

```python
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
```

#### Falló. Lamentable.
![marcelolagos](https://github.com/user-attachments/assets/5bb2d73d-8a56-4803-9c52-c2d71ef2159c)


### Sustitucion con modelo Dummy :(

Simula la predicción completa (PREDICCION FALSA POR AHORA)


```python
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
```
![image](https://github.com/user-attachments/assets/a41afe1f-6c48-49e1-8c7e-1096f6a0f378)


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
[Stylish_height_copy_2025_06_27_19_52_58.zip](https://github.com/user-attachments/files/20956034/Stylish_height_copy_2025_06_27_19_52_58.zip)

https://editor.p5js.org/gaticamartin/full/3Hf9OdA9Y Proyecto Fullscreen

## GRACIAS
![Marcelo_Alta-pxdpl71rxuyjmwvjruno24himeoppciny15x1bcyhw](https://github.com/user-attachments/assets/4a24fb0e-fcc5-4a5d-b630-4d715137d393)

