# MontoyaRush

Integrantes:

* [Benjamin Gonzalez](https://github.com/FAU-UChile/audiv027-2025-1/tree/main/arq-05-BenjaminGonzalezFAU)
* [Ignacia Sepulveda](https://github.com/FAU-UChile/audiv027-2025-1/tree/main/arq-09-IgnaciaUch)
* [Francisco Morande](https://github.com/FAU-UChile/audiv027-2025-1/tree/main/arq-07-franciscomorande)


Mi equipo de trabajo es <https://github.com/FAU-UChile/audiv027-2025-1/tree/main/arq-05-BenjaminGonzalezFAU> y <https://github.com/FAU-UChile/audiv027-2025-1/tree/main/arq-09-IgnaciaUch>, entregamos en el repositorio en este [enlace](https://github.com/franciscomorande/audiv027-2025-1/tree/main/arq-07-franciscomorande/clase-14).

## Acerca del proyecto

El proyecto comenzó con una idea base: reutilizar el juego basado en detectar el dedo índice para mover al “jugador”. La herramienta seleccionada de ml5.js continuó siendo su apartado de “handPose”, por su accesibilidad y fluidez al momento de limitarse al monitoreo específico de puntos de la mano.  El sistema detecta en tiempo real los puntos extremos de cada dedo del usuario mediante una cámara web, y coloca sobre él la imagen para representar al jugador, en este caso para continuar con la narrativa del trabajo pasado, una imagen del profesor que simule que lo estamos controlando. 

El objetivo principal del juego es acumular la mayor cantidad de puntos posible mientras se sobrevive el mayor tiempo en pantalla. Para lograrlo, el jugador debe esquivar una serie de obstáculos en movimiento, que rebotan de forma impredecible en los bordes del escenario. Cada vez que uno de estos obstáculos toca al jugador, se reduce su “tiempo de vida”, una cuenta regresiva que marca cuánto tiempo le queda. Sin embargo, en el camino también aparecerán relojes que permiten recuperar segundos y extender la partida. El juego plantea un sistema de riesgo y recompensa: se introdujeron ciertos elementos que, si bien aumentan momentáneamente la dificultad, también otorgan bonificaciones de puntaje al ser agarrados. A esto se suma una mecánica que modifica la jugabilidad definida para el trabajo anterior: ahora cada ciertos segundos, el jugador deberá controlar el juego usando un dedo diferente de su mano, lo que obliga a adaptarse constantemente y añade un nivel extra de desafío físico y cognitivo. Además, cada componente del juego ha sido diseñado con parámetros modificables, permitiendo ajustar individualmente la dificultad de cada variable. Esta estructura flexible facilita realizar pruebas y ajustes progresivos, buscando siempre una configuración óptima que equilibre el desafío del juego con una experiencia accesible y amigable para el usuario.

Lista de componentes nuevos agregados ordenados según:

1. Funcionamiento base
* Constante - Bombas 
* Constante - Tiempo y Puntuación
* Variable - Dedos 
* Variable - Relojes

2. Objetos de dificultad y puntuación
* Variable - Mirror eje x
* Variable - Mirror eje y
* Variable - Visibilidad
* Variable - Tamaño del jugador
* Variable - Tamaño del jugador según distancia de cámara
* Variable - Enemigo que te sigue

Herramientas utilizadas:
Para trabajar aún más el dinamismo al momento de la jugabilidad, se pensó una modificación a la mecánica: hacer que cada cierto tiempo el jugador se maneje con una detección de un dedo distinto. 
* p5.js: librería creativa de JavaScript para gráficos y animaciones, usada para controlar el canvas, imágenes, y la interacción visual.

* ml5.js: librería de inteligencia artificial accesible para artistas y principiantes, basada en TensorFlow.js. Se utilizó su modelo HandPose para detectar la posición de los dedos.

* Cámara web: como sensor de entrada para captar el movimiento en tiempo real.

## Constante Bombas


Se crean obstáculos (pelotas blancas) que están en constante movimiento alrededor del mapa, las cuales rebotan al tocar los muros. Se crea un patrón constructor en el cual se definen los parámetros de los objetos, ya sean velocidad, posición y diámetros. Se define también un delay para que las pelotas aparezcan una vez se inicie el juego. El proceso se hizo mediante:
* generación de una pelota móvil

![pelota hitbox](https://github.com/user-attachments/assets/e3eb3881-389a-4feb-8f0a-eaddc78fbab1)

* multiplicación de estas mediante aplicación de parámetros
* se añaden parámetros para velocidad, diámetros y spawn inicial

![20 pelotas](https://github.com/user-attachments/assets/0e128b17-a4cf-4027-a937-ac8006b59705)

* se agrega delays para el spawn inicial
* se agrega efecto de colisión (esto posteriormente, una vez combinado con el hitbox de los dedos)
para llegar al siguiente código: 

~~~ javascript

        // comportamiento y tamaño
        class Ball {
            constructor() {
                this.x = random(width);      // Posición X aleatoria
                this.y = random(height);     // Posición Y aleatoria
                this.d = random(15, 40);     // Diámetro aleatorio
                this.xspeed = random(-2, 2); // Velocidad X aleatoria
                this.yspeed = random(-2, 2); // Velocidad Y aleatoria
                this.color = [255, 255, 255]; // Color de la pelota es blanco fijo
            }

            // movimiento de las pelotas
            move() {
                this.xspeed += random(-0.1, 0.1);
                this.yspeed += random(-0.1, 0.1);
                this.xspeed = constrain(this.xspeed, -5, 5);
                this.yspeed = constrain(this.yspeed, -5, 5);
                this.x += this.xspeed;
                this.y += this.yspeed;
            }

            // rebotes al tocar el muro
            bounce() {
                let r = this.d / 2;
                if (this.x < r) {
                    this.x = r;
                    this.xspeed *= -1;
                } else if (this.x > width - r) {
                    this.x = width - r;
                    this.xspeed *= -1;
                }
                if (this.y < r) {
                    this.y = r;
                    this.yspeed *= -1;
                } else if (this.y > height - r) {
                    this.y = height - r;
                    this.yspeed *= -1;
                }
            }

            // dibujo
            display() {
                fill(...this.color);
                noStroke();
                ellipse(this.x, this.y, this.d);
            }
        }

       
        function setup() {
            createCanvas(windowWidth, windowHeight);
            gameStartTime = millis(); // Inicializa el tiempo de inicio
        }

   
        function draw() {

            background(0); // Limpia el fondo en cada frame

            // spawn de las pelotas
            if (!ballsSpawned && millis() - gameStartTime >= BALL_SPAWN_DELAY) {
                for (let i = 0; i < 20; i++) { // Crea 20 pelotas
                    balls.push(new Ball());
                }
                ballsSpawned = true;
            } 
                for (let ball of balls) {
                    ball.move();
                    ball.bounce();
                    ball.display();
                }
            }

~~~
Las bombas al ser tocadas por los dedos generan una pérdida de -5 puntos, para esto también se les asigna un cooldown para que el jugador no pierda instantáneamente. 

~~~ javascript
let gameTime = 30; // tiempo del juego
let lastBallHitTime = 0; // hace cuanto se tocó la pelota
const BALL_HIT_COOLDOWN = 1000; // cooldown

if (ball.isCollidingWithFinger(playerDisplayX, playerDisplayY, currentDedoRadius)) {
    // Aplicar retraso a la reducción del temporizador para evitar múltiples penalizaciones rápidas
    if (millis() - lastBallHitTime > BALL_HIT_COOLDOWN) {
        gameTime = max(0, gameTime - 5); // reducción de 5 segundos 
        lastBallHitTime = millis(); // actualiza tiempo del ultimo toque
    }
}


~~~









## Constante Tiempo Y Puntuación
Lo primero que se desarrolla es el tiempo base del juego, este reloj comienza en 30 seg y va disminuyendo a medida que transcurre el juego, ademas se implementarán elementos extras que aumentarán o disminuirán el tiempo, igualmente los buff y debuff se ordenan (aparecen) con respectos a los segundos transcurridos y aparecen durante una cierta cantidad de tiempo, con respecto a la puntuación, esta comienza en cero y va aumentando con cada segundo que pasa, además los buff y debuff tienen efecto sobre la puntuación, el código se comienza a armar de la siguiente forma: 

~~~ javascript
// Se hará un temporizador base de 30 segundos para el inicio del jugo que irá disminuyendo (el juego termina cuando el contador llega a 0).
// Se agregan variables que suman (+4), estas aparecen cada cierto tiempo en el juego para que el jugador las tome.
// En caso de que el jugador toque algunas de las bombas o sea alcanzado por el enemigo se le restaron -3 segundos.
// El puntaje comienza en 0 y aumentará en +1 por cada segundo que pase 
// Si el jugador toma el tiempo extra (buffo), se penaliza: -5 puntos.
//Si se toma un ítem “negativo” (debuffo) se aplica un x2 en el multiplicador en el puntaje por la cantidad de tiempo que dure el efecto 

// == VARIABLES (let) == 
/let gameTime = 30;
let lastSecond = 0;
let score = 0;
let multiplier = 1;
let multiplierEndTime = 0;
let specialItems = [];
let lastSpecialAdd = 0;

// == TEMPORIZADOR + PUNTUACIÓN
if (millis() - lastSecond >= 1000 && gameTime > 0) {
    gameTime--;
    score += 1 * multiplier;
    lastSecond = millis();
}
if (multiplier > 1 && millis() > multiplierEndTime) {
    multiplier = 1;
}
if (gameTime <= 0) {
      noLoop();
}
// == CONFIGURACIÓN DEL TIEMPO (setup)  == 
lastSecond = millis();
lastBuffTime = millis();
lastNegativeTime = millis();

// == ENTRADAS DEL TIEMPO (draw) ==
if (millis() - lastSecond > 1000 && gameTime > 0) {
    gameTime—;
    lastSecond = millis();
if (multiplier > 1 && millis() > multiplierEndTime) {
    multiplier = 1;
}
//  AGREGAR VARIABLES 
function applyItemEffect(type) {
    switch (type) {
        case "extraTime":
            gameTime += 4;
            break;
        case "bomb":
            gameTime = max(0, gameTime - 3);
            break;
        case "multiplier":
            multiplier = 2;
            multiplierEndTime = millis() + 5000;
            break;
        case "timePenalty":
            gameTime += 10;
            score = max(0, score - 5);
            break;
    }
}

~~~

Durante el desarrollo del sistema de tiempo y puntuación del juego, uno de los principales desafíos fue lograr un equilibrio adecuado entre los distintos elementos que afectan la duración y la puntuación final. Se debieron realizar múltiples sincronizaciones para evitar que el juego se volviera demasiado fácil o injustamente difícil. Coordinar que los efectos se activen y desactiven en los momentos adecuados exigió múltiples pruebas y ajustes. Además, asegurar que el puntaje refleje fielmente el desempeño del jugador, sin errores de suma o acumulación indebida, fue clave para garantizar una experiencia de juego justa y funcional. Los elementos de tiempo y puntuación se transforman en parámetros que regulan todo el juego. Esta fue la base sobre la cual se trabajó, a medida que el código fue avanzando estos valores cambiaron , se juntaron y se agregaron o sacaron cosas para poder adecuarse a las nuevas necesidades del código completo



## Variable Detección de Dedos En Secuencia

El proceso completo pasó por cuatro etapas claves: 

* Detección general de los cinco dedos .

* Variación secuencial a través del tiempo.

* Transiciones.

* Optimización.

# Etapa 1: Detección Básica de Dedos

![01 dedos](https://github.com/user-attachments/assets/05b0e1c7-723c-4395-8b14-ef47c980b182)

La primera versión se enfocó en detectar las puntas de los cinco dedos usando handPose y proyectar una imagen sobre cada uno. Además, se trabajó la variable de “smoothedPositions” para almacenar coordenadas suavizadas que permitirían una futura interpolación. Luego, se utilizó un bucle para recorrer los puntos claves detectados y actualizar esas coordenadas con “lerp” para suavizar el movimiento. Este proceso permitió una visualización inmediata y efectiva, preparando la siguiente parte encargada de ir intercalando las detecciones.

# Etapa 2: Secuenciación de Dedos

![01 dedos 2](https://github.com/user-attachments/assets/41951fd0-e89e-4456-86aa-54bc06ec3aef)

![01 dedo 3](https://github.com/user-attachments/assets/24e20c6e-ae17-4be4-a4fa-1fba894fa58e)

Se introdujo la mecánica de detectar un solo dedo a la vez, cambiando cada cierto intervalo de tiempo. Cada 10 segundos, el sistema cambia aleatoriamente a otro dedo garantizando que no se repita el anterior. Esto a partir de otorgar a cada punto clave de cada dedo un valor entre el 1 al 5:

~~~ javascript
if (elapsed >= changeInterval) {
  let newFinger;
  do {
    newFinger = floor(random(1, 6));
  } while (newFinger === activeFinger);
  activeFinger = newFinger;
  lastChangeTime = millis();
}
~~~

Se trabajó además la interfaz para una experiencia fluida, una barra de progreso visual que indica el tiempo restante para el cambio. Esta versión permitió guiar al usuario de forma clara sobre qué dedo debía posicionar.

# Etapa 3: Transiciones Suaves

![01 dedo 3](https://github.com/user-attachments/assets/24e20c6e-ae17-4be4-a4fa-1fba894fa58e)

Para evitar cambios bruscos, se introdujo un sistema de fade in y fade out de las imágenes del dedo activo. Esto se implementó interpolando la opacidad (alpha) dependiendo del tiempo transcurrido desde el cambio:

~~~ javascript
let alpha = 255;
if (elapsed < fadeDuration) {
  alpha = map(elapsed, 0, fadeDuration, 0, 255);
} else if (elapsed > changeInterval - fadeDuration) {
  alpha = map(elapsed, changeInterval - fadeDuration, changeInterval, 255, 0);
}
tint(255, alpha);
~~~

Además, se añadió una lógica de “invulnerabilidad” durante esta transición para evitar detecciones erráticas. Esto dio un salto cualitativo en usabilidad y percepción visual, haciendo la experiencia más fluida:

![01 dedos 4](https://github.com/user-attachments/assets/97219d55-8ad7-43ef-b8a5-a534369b835d)

# Etapa 4: Optimización y Limpieza

Al enfrentarnos al problema de que todo iba pegado, nos dimos cuenta que la culpa era principalmente por el código de detección de dedos. Es por esto que cambiamos a una única estructura más eficiente. Se reemplazaron múltiples variables individuales por un objeto centralizado:

~~~ javascript
let smoothedPos = { x: 0, y: 0 };
const fingerKeypoints = {
  1: "thumb_tip",
  2: "index_finger_tip",
  3: "middle_finger_tip",
  4: "ring_finger_tip",
  5: "pinky_finger_tip"
};
~~~

Esto facilitó escalar y reutilizar el código para las futuras variables a definir. Finalmente se añadieron logs de validación para depurar errores de carga o modelo.

## Variable Escalado por Profundidad

![03 tamano](https://github.com/user-attachments/assets/9a5a384f-0a1a-4a72-b4d1-fa85e3cf0bcc)

Esta extensión del sistema de detección de dedos tuvo como objetivo dinamizar la percepción espacial del usuario al visualizar el tamaño del dedo activo más grande o más pequeño dependiendo de su distancia a la cámara. En lugar de mantener un tamaño estático para la imagen del dedo, se incorporó una lógica de escalado dinámico basado en profundidad relativa, utilizando los puntos de la muñeca (`wrist`) y del índice (`index_finger_tip`) como referencia. Se conservó la base de detección secuencial y se añadió una capa adicional de cálculo que transforma visualmente la interfaz en función del acercamiento del dedo.

# Etapa 1: Definición de parámetros de escalado  

Para facilitar el control de esta lógica, se crearon variables declarativas al inicio del código que permiten modificar su comportamiento sin alterar el cuerpo principal. Esto permite un control completo sobre cuándo y cómo se activa la función de escalado según la distancia percibida en píxeles.

~~~javascript
let usarEscaladoPorProfundidad = true;
let escalaBase = 1.;             // Tamaño por defecto
let escalaMaxima = 2.5;          // Tamaño si está muy cerca
let escalaMinima = 0.1;          // Tamaño si está muy lejos
let distanciaCerca = 250;        // px (si es mayor a esto, está muy cerca)
let distanciaLejos = 80;         // px (si es menor a esto, está muy lejos)
~~~

# Etapa 2: Suavizado de coordenadas y almacenamiento  

Como condición previa al cálculo de distancia, los puntos relevantes del cuerpo (en este caso, muñeca e índice) deben estar disponibles y suavizados para obtener una medida estable. Este fragmento construye un objeto llamado smoothedPoints que almacena las posiciones suavizadas de todos los keypoints reconocidos por el modelo ml5.

# Etapa 3: Cálculo de profundidad relativa  

Una vez identificadas las posiciones suavizadas de los puntos wrist e index_finger_tip, se calcula su distancia en píxeles como estimación de la “profundidad” del dedo frente a la cámara:

~~~javascript
let d = dist(
  smoothedPoints["wrist"].x, smoothedPoints["wrist"].y,
  smoothedPoints["index_finger_tip"].x, smoothedPoints["index_finger_tip"].y
);
~~~

Esta distancia actúa como un proxy de la cercanía del dedo al lente, ya que el ángulo de apertura de la cámara tiende a separar más esos dos puntos cuando el dedo se acerca físicamente al dispositivo. Gracias a que el punto de la muñeca es constante, permite dar el efecto que a medida que se aleja de la cámara, la distancia entre el punto de la muñeca y el dedo disminuye (en valores de píxeles detectados).

# Etapa 4: Aplicación del escalado sobre la imagen  

Con la distancia ya estimada, se utiliza la función “map()” para traducirla en un factor de escala dentro de los rangos permitidos por el sistema:

~~~javascript
escala = map(d, distanciaLejos, distanciaCerca, escalaMinima, escalaMaxima, true);
~~~

Este valor reemplaza al tradicional tamaño fijo de la imagen, haciendo que la imagen del dedo aparezca más grande o más pequeña dependiendo de la cercanía física:

~~~javascript
image(dedoImg, x, y, 80 * escala, 80 * escala);
~~~

Como resultado final se observa que el sistema responde correctamente a movimientos de acercamiento y alejamiento del dedo respecto a la cámara. El suavizado evitar saltos bruscos o parpadeos de tamaño y el escalado se detiene en los límites establecidos por “escalaMaxima” y “escalaMinima”. En conclusión, la experiencia visual resulta coherente incluso durante transiciones o cambios de dedo.

## Variable Visibilidad Reducida

![04 visibilidad](https://github.com/user-attachments/assets/c1b77b5b-a497-46bf-9413-8d63d24ca01f)

 
La implementación de la variable surge como una estrategia para condicionar la percepción del jugador, limitando de manera parcial el campo visual en el entorno interactivo. Esta restricción no solo añade dificultad, sino que introduce una capa sensorial al comportamiento del jugador, centrando la atención únicamente en el área que rodea al dedo actualmente activo.

El efecto se consigue mediante la superposición de una máscara oscura (con transparencia modificable) sobre todo el lienzo, salvo en una región circular centrada en la posición del dedo. Esta región, suavemente difuminada en sus bordes, simula un haz de luz o “linterna visual”. 

# Etapa 1: Declaración de parámetros globales  
Se optó por parametrizar todos los valores esenciales de esta variable para facilitar su activación, desactivación y calibración futura:

~~~javascript
let visionReducidaActiva = true;  // Activar o desactivar el modo
let radioVision = 200;            // Radio visible alrededor del dedo
let opacidadOscuridad = 220;      // Opacidad del fondo oscuro (0 a 255)
let suavizadoBorde = 100;         // Transición gradual entre zona visible y oscura
~~~

# Etapa 2: Localización del punto de enfoque  

Para posicionar correctamente la máscara circular, fue necesario identificar las coordenadas del dedo actualmente activo. Pero esto era facilitado por el proceso de detección de dedos, el cual el apartado para interpolar el movimiento ya realizaba esta función.

~~~javascript
smoothedPos.x = lerp(smoothedPos.x, kp.x, 0.2);
smoothedPos.y = lerp(smoothedPos.y, kp.y, 0.2);
~~~

# Etapa 3: Generación del gradiente radial  

La implementación del efecto visual se llevó a cabo mediante el uso del API de lienzo HTML5. Se utilizó un gradiente radial que parte desde el centro del dedo activo y se expande hacia fuera, con una transición progresiva hacia la oscuridad.

~~~ javascript
let gradient = drawingContext.createRadialGradient(
  smoothedPos.x, smoothedPos.y, radioVision - suavizadoBorde,
  smoothedPos.x, smoothedPos.y, radioVision
);
gradient.addColorStop(0, `rgba(0, 0, 0, 0)`);
gradient.addColorStop(1, `rgba(0, 0, 0, ${opacidadOscuridad / 255})`);
~~~

Este gradiente se aplica sobre el lienzo mediante un rectángulo que cubre la totalidad de la pantalla. Tras su implementación, se documenta su correcta activación y desactivación mediante “visionReducidaActiva” y un correcto funcionamiento del suavizado y opacidad, evitando cortes abruptos o efectos inconsistentes.

# Variable Enemigo que Persigue

![02 enemigo 2](https://github.com/user-attachments/assets/904b184b-c6b9-49d0-9cee-cd6323c29c3f)
   

Esta funcionalidad surge como una extensión dinámica del sistema interactivo, con el propósito de introducir un comportamiento externo que reacciona al movimiento del jugador y lo obliga a moverse por la cámara. Se implementó un "enemigo virtual" que persigue constantemente al dedo activo, si logra alcanzarlo, desencadena una acción (restar tiempo de vida) y desaparece, de lo contrario desaparece por agotamiento del tiempo pre-definido.

## Etapa 1: Parametrización de comportamiento  

Se estructuró la lógica del enemigo a través de variables parametrizadas al comienzo del código para garantizar claridad, personalización y facilidad de prueba.

~~~javascript
let enemigoActivo = true;         // Activar o desactivar enemigo
let enemigoColor = [255, 0, 0];   // Color del enemigo
let enemigoRadio = 40;            // Radio del enemigo
let enemigoVelocidad = 2.5;       // Velocidad de persecución
let enemigoDuracion = 5000;       // Tiempo antes de desaparecer (ms)
~~~

## Etapa 2: Generación y activación  

Si la variable “enemigoActivo” está en true, se llama a una función que posiciona al enemigo en una coordenada aleatoria del canvas y registra su tiempo de inicio.

~~~javascript
function generarEnemigo() {
  enemigo.x = random(width);
  enemigo.y = random(height);
  enemigo.startTime = millis();
  enemigo.activo = true;
}
~~~

## Etapa 3: Movimiento hacia el jugador  

![02 enemigo 1](https://github.com/user-attachments/assets/77ecb34a-7cb7-4461-bd10-0e8e92ec9dfb)

Cada frame, si el enemigo sigue activo, este se mueve en dirección al dedo detectado utilizando trigonometría básica. Se calcula el ángulo entre las coordenadas del enemigo y las del jugador, y se actualiza su posición en función de ese ángulo y la velocidad definida:

~~~javascript
let dx = jugadorX - enemigo.x;
let dy = jugadorY - enemigo.y;
let angle = atan2(dy, dx);
enemigo.x += cos(angle) * enemigoVelocidad;
enemigo.y += sin(angle) * enemigoVelocidad;
~~~

## Etapa 4: Visualización y colisión  

![02 enemigo 2](https://github.com/user-attachments/assets/904b184b-c6b9-49d0-9cee-cd6323c29c3f)

El enemigo se dibuja como un círculo de color. Se evalúa la distancia con respecto al dedo (o jugador), y si esta es menor que su radio, se considera una colisión.

~~~ javascript
fill(...enemigoColor);
noStroke();
ellipse(enemigo.x, enemigo.y, enemigoRadio * 2);

if (dist(enemigo.x, enemigo.y, jugadorX, jugadorY) < enemigoRadio) {
  enemigo.activo = false;
  enemigoActivo = false;
  console.log("¡Golpeado por el enemigo!");
}
~~~

También se incluye una condición alternativa para desactivar al enemigo si su tiempo de vida ha expirado:

~~~ javascript
else if (millis() - enemigo.startTime > enemigoDuracion) {
  enemigo.activo = false;
  enemigoActivo = false;
  console.log("El enemigo desapareció por tiempo");
}
~~~

Al probar el código, el seguimiento es fluido y estable hacia el dedo detectado y la desaparición por contacto o tiempo funciona. El comportamiento en la colisión actualmente genera un mensaje de consola y está listo para expandirse a una acción definida en futuro, cuando se fusione con la variable de tiempo de vida.

## Spawn de objetos

![spawn set interval](https://github.com/user-attachments/assets/debf6f72-f972-46b8-ab68-893503f6819a) 

Primero se prueba como se pueden spawnear objetos mediante un setinterval diferente para cada una de las pelotas. Para esto se coloca un rango de tiempo en el que puede spawnear una pelota y un tiempo que establezca el delete de pelotas tras pasado cierto tiempo de spawn. Usando el ejemplo de una sola:

~~~ javascript
function draw() {
  background(0);

  // dibuja circulos
  for (let i = circles.length - 1; i >= 0; i--) {
    let c = circles[i];
    fill(c.col);
    noStroke();
    circle(c.x, c.y, c.d);

    // eliminar cada 5 segundos
    if (millis() - c.birthTime > 2000) {
      circles.splice(i, 1);
    }
  }

  // nuevo ciruclo
  if (millis() - lastAddTime > 5000) {
    addRandomCircle();
    lastAddTime = millis();
  }
}
function generarElipses() {
  // Programar la creación de elipses aleatoriamente
  setTimeout(() => {
    crearElipseRoja();
    generarElipses();
  }, random(8000, 20000));
function crearElipseRoja() {
  ellipses.push({
    x: random(width),
    y: random(height),
    tiempo: millis(),
    color: color(255, 0, 0)
  });
}
~~~ 

![objeto simple respawns](https://github.com/user-attachments/assets/b9038f57-94f7-4e1d-80ba-cf17fa1387e8)


Sin embargo, para simplificar el juego, esto se reduce a crear una funcion de generar un circulo aleatorio cada cierto tiempo establecido (anteriormente aleatorio) en el cual genera una pelota random del conjunto de pelotas

~~~ javascript

function draw() {
  background(0);

  // dibuja circulos
  for (let i = circles.length - 1; i >= 0; i--) {
    let c = circles[i];
    fill(c.col);
    noStroke();
    circle(c.x, c.y, c.d);

    // eliminar cada 5 segundos
    if (millis() - c.birthTime > 2000) {
      circles.splice(i, 1);
    }
  }

  // nuevo ciruclo
  if (millis() - lastAddTime > 5000) {
    addRandomCircle();
    lastAddTime = millis();
  }
}

function addRandomCircle() {
  // Posición aleatoria
  let x = random(width);
  let y = random(height);

  // Diámetros predefinidos
  let diameters = [50, 50, 50, 50];
  let d = random(diameters);

  // Color aleatorio
  let col = random(colors);

  // Crear círculo con tiempo de nacimiento
  circles.push({ x, y, d, col, birthTime: millis() });
}
~~~
Finalmente, se decide que para mayor intuición del usuario, todos los objetos serian camuflados en forma de estrella, y que el bufo aleatorio sería descrito en la pantalla mediante texto

![objeto estrella](https://github.com/user-attachments/assets/dc192d71-da39-4c68-8c65-d254782ef969)


## Aplicación de efectos al tocar objetos

![objeto estrella  y bufo](https://github.com/user-attachments/assets/edf19c8c-8359-4aec-9a74-a869208588de)

Se utiliza la misma lógica de los conjuntos de los dedos. Una vez al tocar una estrella, está activa el switch, el cual elige un valor aleatorio de las constantes de los efectos (buffs/debuffs)

~~~javascript
// Función para activar un buff aleatorio al tocar una estrella
function activateRandomBuff() {
    const buffs = ['enemyFollower', 'lowVisibility', 'mirrorX', 'mirrorY', 'cameraDistance'];
    let newBuff = random(buffs);
    activeBuff = newBuff;
    buffStartTime = millis();

    // Desactivar todos los buffs primero
    mirrorXActive = false;
    mirrorYActive = false;
    lowVisibilityActive = false;
    cameraDistanceActive = false;
    usarEscaladoPorProfundidad = false;
    enemy.active = false;

    // Activar el buff elegido
    switch (newBuff) {
        case 'mirrorX':
            mirrorXActive = true;
            buffDisplayName = "eje x invertido";
            break;
        case 'mirrorY':
            mirrorYActive = true;
            buffDisplayName = "eje y invertido";
            break;
        case 'lowVisibility':
            lowVisibilityActive = true;
            buffDisplayName = "Visibilidad Reducida";
            break;
        case 'cameraDistance':
            cameraDistanceActive = true;
            usarEscaladoPorProfundidad = true;
            buffDisplayName = "Tamaño Variable";
            break;
        case 'enemyFollower':
            enemy.active = true;
            enemy.startTime = millis();
            enemy.x = random(width);
            enemy.y = random(height);
            buffDisplayName = "enemigo acechando";
            break;
        default:
            buffDisplayName = "Ninguno";
            break;
    }
~~~

## Adición de textos
![7 textos](https://github.com/user-attachments/assets/e72de891-a43b-4850-92ae-ff5e9f6150da)

Se añaden textos al interactuar con objetos, estrellas y obstáculos para que el usuario tenga un mejor claridad sobre qué está sucediendo en el juego. Esto es solamente para hacer que el juego sea más intuitivo.
*Ejemplo con el texto de la estrella ("¡+20 PUNTOS!")
~~~javascript
if (showStarText && millis() - starTextStartTime < STAR_TEXT_DURATION) {
    // Si la variable está activa Y el tiempo transcurrido es menor a la duración:
    fill(255, 255, 0); // color del texto 
    textSize(36); // tamaño del texto
    textAlign(CENTER, CENTER); // Centra el texto
} else if (showStarText) {
    // Si la variable está activa pero el tiempo ya excedió la duración:
    showStarText = false; // Desactiva la bandera para que el texto ya no se dibuje
}
~~~

## Problemas generales y de unificación de código
Al unificar el código en sus diversas partes nos encontramos con varios problemas los cuales tuvimos que solucionarlos usando un método de ocultar el problema mediante una nueva función que le dimos al juego.

*A pesar de que los cambios de dedos fueran aleatorios, siempre se parte con el índice.

![problema carga](https://github.com/user-attachments/assets/ed42b8ae-79fe-48ff-8896-84fc0e356579)

*Problema de carga: al tener tantas líneas de código, el handpose tardaba bastante en cargar. Llegamos a una decisión que solucionaba de forma simultánea este problema con el problema anterior. Esto fue hace un delay de 10 segundos para que inicie el temporizador y el spawn de cada objeto. Dejando de esta forma el dedo índice inicial como pantalla de carga con un delay y temporizador de 10 segundos. Esto simultáneamente permite cargar el handpose sin problema durante este rango de tiempo.

![problema carga solucion](https://github.com/user-attachments/assets/a11de62d-5879-4db3-bc24-e171911f9ffe)

*Problemas con efecto de cambio de tamaño: esto nos generó un atraso en la unificación de los códigos debido a que no funcionaba como quisiéramos. Llegamos a la conclusión que era una limitación del navegador edge, por lo cual a partir de ese momento desechamos el uso de ese navegador y sugerimos al usuario no usarlo.
*Tuvimos dos sistemas de colisión, uno que nacía del código de las pelotas, mientras que otro nacía del código del dedo. Decidimos usar el del dedo ya que se haría más fácil la replicación para tanto los relojes, objetos y cofres de puntajes



## Datos del Proyecto
* Proyecto realizado en lenguaje JavaScript, mediante el editor de p5.js en version v1.10.0
* [Proyecto](https://editor.p5js.org/francisco.morande/full/OqCtrRSXB)
* [Código del proyecto](https://editor.p5js.org/francisco.morande/sketches/OqCtrRSXB)
<details>
<summary> Código del proyecto </summary>

    
~~~javascript

let video;
let hands = [];
let dedoImg;
let handPoseModel;

// Dedo que comienza a ser detectado
let activeFinger = 2;

// Intervalo de tiempo para cambiar el dedo activo (en ms)
let changeInterval = 10000;
let lastChangeTime = 0;

// Duración del efecto de fade in/out del dedo (en ms)
let fadeDuration = 1000;

// Nombres de los dedos para mostrar en pantalla
const fingerNames = {
    1: "Pulgar",
    2: "Índice",
    3: "Medio",
    4: "Anular",
    5: "Meñique"
};

// Puntos clave de los dedos usados por ml5.handPose
const fingerKeypoints = {
    1: "thumb_tip",
    2: "index_finger_tip",
    3: "middle_finger_tip",
    4: "ring_finger_tip",
    5: "pinky_finger_tip"
};

// Colores asociados a cada dedo
const fingerColors = {
    1: [255, 100, 100],    // Pulgar
    2: [100, 255, 100],    // Índice
    3: [100, 100, 255],    // Medio
    4: [255, 255, 100],    // Anular
    5: [255, 100, 255]     // Meñique
};

//  VARIABLES GLOBALES PARA ESTRELLAS (BUFFS/DEBUFFS) 
let stars = []; // Arreglo para almacenar las estrellas
let yellowColor; // Color amarillo fijo para las estrellas
let lastStarAddTime = 0; // Última vez que se añadió una estrella
const STAR_SPAWN_DELAY_INITIAL = 15000; // 15 segundos para la primera estrella
let firstStarSpawned = false; // Bandera para la primera estrella
const SUBSEQUENT_STAR_SPAWN_DELAY = 20000; // Estrellas subsiguientes aparecen cada 20 segundos
const STAR_BORDER_PADDING = 100; // 100 píxeles de límite desde el borde para estrellas

// Arreglo donde se guardarán todas las pelotas
let balls = [];

// Variable booleana que indica si el juego terminó
let juegoTerminado = false;

// Variables para el retardo de spawn de las pelotas
const BALL_SPAWN_DELAY = 10000; // 10 segundos
let gameStartTime; // Tiempo en que el juego realmente comienza
let ballsSpawned = false; // Bandera para saber si las pelotas ya fueron creadas
let displayGameInfo = false; // Controla cuándo se muestra el tiempo y el puntaje
let showLoadingText = true; // Controla la visibilidad del texto de carga

// TIEMPO Y PUNTUACIÓN 
let gameTime = 30; // Temporizador que parte desde 30 segundos
let lastSecondUpdate = 0; // Para el incremento de puntaje por segundo
let score = 0; // Puntaje del juego

//  MECÁNICAS DE BUFF/DEBUFF 
let activeBuff = null; // Almacena el buff activo: 'mirrorX', 'mirrorY', 'lowVisibility', 'cameraDistance', 'enemyFollower'
const BUFF_DURATION = 10000; // Duración para todos los buffs (10 segundos)
let buffStartTime = 0; // Tiempo en que comenzó el buff actual
let buffDisplayName = "Ninguno"; // Texto para mostrar el buff activo

// Buff: Eje X invertido
let mirrorXActive = false;
// Buff: Eje Y invertido
let mirrorYActive = false;
// Buff: Visibilidad Reducida
let lowVisibilityActive = false;
let radioVision = 200;    // Tamaño del círculo visible
let opacidadOscuridad = 220;    // Opacidad del fondo oscuro (0 a 255)
let suavizadoBorde = 100;       // Difuminado del borde visible

// Buff: Tamaño Variable (cambio de tamaño del jugador según la distancia a la cámara)
let cameraDistanceActive = false;
let usarEscaladoPorProfundidad = false;
let escalaBase = 1.0;          // Tamaño por defecto
let escalaMaxima = 2.5;        // Tamaño si está muy cerca
let escalaMinima = 0.1;        // Tamaño si está muy lejos
let distanciaCerca = 200;      // px (si es mayor a esto, está muy cerca)
let distanciaLejos = 100;       // px (si es menor a esto, está muy lejos)
let currentScale = 1;          // Almacena la escala actual del dedo

// Buff: Enemigo Acechando
let enemy = {
    x: 0,
    y: 0,
    startTime: 0,
    active: false, // Estado inicial, se activará con el buff
    speed: 2, // Velocidad de persecución
    radius: 40, // Radio del enemigo
    color: [255, 0, 0] // Color del enemigo
};

// dELAYS Y TEXTOS DE RETROALIMENTACIÓN 
// Delay para la reducción del temporizador al tocar una pelota blanca
let lastBallHitTime = 0;
const BALL_HIT_COOLDOWN = 1000; // 1 segundo

// Variables para el texto de colisión de pelota
let showCollisionText = false;
let collisionTextStartTime = 0;
const COLLISION_TEXT_DURATION = 1000; // 1 segundo
const COLLISION_DISPLAY_COOLDOWN = 1000; // 1 segundo de enfriamiento para el texto

//  RELOJES DE ARENA 
let hourglasses = [];
let lastHourglassAddTime = 0;
const HOURGLASS_SPAWN_DELAY_INITIAL = 10000; // La primera aparece a los 10 segundos
let firstHourglassSpawned = false; // Bandera para controlar la primera aparición
const SUBSEQUENT_HOURGLASS_SPAWN_DELAY = 2000; // Aparecen cada 2 segundos después
const HOURGLASS_LIFETIME = 5000; // 5 segundos antes de desaparecer
let showHourglassText = false;
let hourglassTextStartTime = 0;
const HOURGLASS_TEXT_DURATION = 1000; // 1 segundo
const HOURGLASS_BORDER_PADDING = 30; // 30 píxeles de límite desde el borde

//  BARRAS DE ORO 
let goldBars = [];
let lastGoldBarAddTime = 0;
const GOLDBAR_SPAWN_DELAY = 10000; // 10 segundos
const GOLDBAR_LIFETIME = 5000; // 5 segundos antes de desaparecer
let showGoldBarText = false;
let goldBarTextStartTime = 0;
const GOLDBAR_TEXT_DURATION = 1000; // 1 segundo
const GOLDBAR_BORDER_PADDING = 50; // 50 píxeles de límite desde el borde

// Variables para el texto de estrella (puntos)
let showStarText = false;
let starTextStartTime = 0;
const STAR_TEXT_DURATION = 1000; // 1 segundo

// Variables para el texto de nuevo efecto
let showNewEffectText = false;
let newEffectTextStartTime = 0;
let newEffectName = "";
const NEW_EFFECT_TEXT_DURATION = 3000; // 3 segundos

// Objeto para almacenar los puntos clave suavizados de todas las partes de la mano
let smoothedPoints = {};
const BASE_DEDO_RADIO = 40; // Radio base del dedo para colisiones


function preload() {
    dedoImg = loadImage("profesonriendo.png",
        () => console.log("Imagen cargada correctamente"),
        (err) => console.error("Error cargando la imagen:", err)
    );
}

function setup() {
    createCanvas(windowWidth, windowHeight);
    
    video = createCapture(VIDEO, () => {
        console.log("Video capturado correctamente");
        handPoseModel = ml5.handPose(video, () => {
            console.log("Modelo de handPose cargado");
        });
    });

    video.size(windowWidth, windowHeight);
    video.hide();
    lastChangeTime = millis();
    gameStartTime = millis(); // Almacenar el tiempo de inicio del juego para el spawn de pelotas

    // Inicializar temporizador y puntaje
    gameTime = 30; // Inicia en 30 segundos
    lastSecondUpdate = millis(); // Inicializar para actualizaciones de puntaje/tiempo
    score = 0; // Inicia el puntaje en 0

    yellowColor = color(255, 255, 0); // Color amarillo para las estrellas
    lastStarAddTime = millis(); // Inicializar el tiempo para añadir estrellas
    lastCollisionTextDisplayTime = millis(); // Inicializar el enfriamiento para el texto de colisión
    lastHourglassAddTime = millis(); // Inicializar para el spawn de relojes de arena
    lastGoldBarAddTime = millis(); // Inicializar para el spawn de barras de oro
}

// Callback cuando se detectan manos
function gotHands(results) {
    hands = results;
    if (hands.length > 0) {
        let hand = hands[0];
        // Suavizar todos los puntos clave de la mano
        for (let keypoint of hand.keypoints) {
            let name = keypoint.name;
            if (!smoothedPoints[name]) {
                smoothedPoints[name] = { x: keypoint.x, y: keypoint.y };
            } else {
                smoothedPoints[name].x = lerp(smoothedPoints[name].x, keypoint.x, 0.2);
                smoothedPoints[name].y = lerp(smoothedPoints[name].y, keypoint.y, 0.2);
            }
        }
    }
}

// Función auxiliar para verificar la inmunidad del dedo
function isFingerImmune() {
    let elapsedReal = millis() - lastChangeTime;
    // El dedo es inmune durante el fade-in después de un cambio y durante el fade-out antes de un cambio.
    if (elapsedReal < fadeDuration) return true;
    if (elapsedReal > changeInterval - fadeDuration && elapsedReal < changeInterval) return true;
    return false;
}

// Función para activar un buff aleatorio al tocar una estrella
function activateRandomBuff() {
    const buffs = ['enemyFollower', 'lowVisibility', 'mirrorX', 'mirrorY', 'cameraDistance'];
    let newBuff = random(buffs);
    activeBuff = newBuff;
    buffStartTime = millis();

    // Desactivar todos los buffs primero para asegurar un estado limpio
    mirrorXActive = false;
    mirrorYActive = false;
    lowVisibilityActive = false;
    cameraDistanceActive = false;
    usarEscaladoPorProfundidad = false;
    enemy.active = false;

    console.log("Activando buff: " + newBuff);

    // Activar el buff elegido
    switch (newBuff) {
        case 'mirrorX':
            mirrorXActive = true;
            buffDisplayName = "eje x invertido";
            break;
        case 'mirrorY':
            mirrorYActive = true;
            buffDisplayName = "eje y invertido";
            break;
        case 'lowVisibility':
            lowVisibilityActive = true;
            buffDisplayName = "Visibilidad Reducida";
            break;
        case 'cameraDistance':
            cameraDistanceActive = true;
            usarEscaladoPorProfundidad = true;
            buffDisplayName = "Tamaño Variable";
            break;
        case 'enemyFollower':
            enemy.active = true;
            enemy.startTime = millis();
            enemy.x = random(width);
            enemy.y = random(height);
            buffDisplayName = "enemigo acechando";
            break;
        default:
            buffDisplayName = "Ninguno";
            break;
    }
    // Establecer texto para mostrar el nuevo efecto
    newEffectName = buffDisplayName;
    showNewEffectText = true;
    newEffectTextStartTime = millis();
}

function draw() {
    // Verificar y desactivar el buff si su duración ha pasado
    if (activeBuff !== null && millis() - buffStartTime >= BUFF_DURATION) {
        console.log("Desactivando buff: " + activeBuff);
        activeBuff = null;
        buffDisplayName = "Ninguno"; // Reiniciar nombre para mostrar
        mirrorXActive = false;
        mirrorYActive = false;
        lowVisibilityActive = false;
        cameraDistanceActive = false;
        usarEscaladoPorProfundidad = false;
        enemy.active = false;
    }

    // === VERIFICACIÓN DE FIN DE JUEGO ===
    if (gameTime <= 0) { // Verificar si el tiempo de juego llegó a 0
        juegoTerminado = true;
    }

    // Si el juego ha terminado, detener el dibujo y mostrar la pantalla de fin de juego.
    if (juegoTerminado) {
        background(0); // Fondo negro
        fill(255); // Color de texto blanco
        textSize(64);
        textAlign(CENTER, CENTER);
        text("¡Juego Terminado!\nPuntaje: " + score, width / 2, height / 2); // Mostrar puntaje final
        noLoop(); // Detener el bucle de dibujo
        return; // Salir de la función draw
    }

    background(0); // Pintar fondo negro en cada frame

    // Verificar si las pelotas deben aparecer y si la información del juego debe mostrarse
    if (!ballsSpawned && millis() - gameStartTime >= BALL_SPAWN_DELAY) {
        // Crear 10 pelotas y añadirlas al arreglo
        for (let i = 0; i < 6; i++) {
            balls.push(new Ball());
        }
        ballsSpawned = true; // Marcar que las pelotas han sido creadas
        displayGameInfo = true; // Empezar a mostrar el tiempo y el puntaje
        showLoadingText = false; // Ocultar texto de carga
    }

    translate(width, 0); // Efecto espejo horizontal para la cámara
    scale(-1, 1);

    let elapsedReal = millis() - lastChangeTime;

    // Cambiar dedo activo cuando se complete el intervalo
    if (elapsedReal >= changeInterval) {
        let newFinger;
        do {
            newFinger = floor(random(1, 6));
        } while (newFinger === activeFinger);
        activeFinger = newFinger;
        lastChangeTime = millis();
        elapsedReal = 0; // Reiniciar para propósitos de visualización
    }

    // Barra visual del tiempo restante
    let barHeight = 200;
    let barWidth = 20;
    noStroke();
    fill(...fingerColors[activeFinger]);
    let currentHeight = barHeight * (1 - elapsedReal / changeInterval);
    let currentY = height / 2 - barHeight / 2 + (barHeight - currentHeight);
    rect(width - 30, currentY, barWidth, currentHeight);

    resetMatrix(); // Reiniciar matriz para dibujar texto sin espejo
    fill(...fingerColors[activeFinger]);
    textSize(24);
    textAlign(CENTER, TOP);
    text("Dedo Activo: " + fingerNames[activeFinger], width / 2, 20);

    textAlign(LEFT, TOP);
    textSize(64);
    fill(...fingerColors[activeFinger]);
    text(activeFinger, 10, 10);

    // === MOSTRAR TIEMPO Y PUNTAJE ===
    if (displayGameInfo) { // Solo mostrar si la bandera es verdadera
        fill(255);
        textSize(24);
        textAlign(LEFT, TOP);
        text("Tiempo: " + gameTime, 20 + 60, 20);
        text("Puntaje: " + score, 20 + 60, 50);
    } else if (showLoadingText) { // Mostrar texto de carga solo antes de que aparezca la info del juego
        fill(255);
        textSize(48);
        textAlign(CENTER, CENTER);
        let timeToStart = Math.ceil((BALL_SPAWN_DELAY - (millis() - gameStartTime)) / 1000);
        if (timeToStart < 0) timeToStart = 0;
        text("Cargando en " + timeToStart + "...", width / 2, height / 2);
    }

    // Mostrar nombre del buff activo
    fill(255); // Color blanco para el texto del buff
    textSize(20);
    textAlign(RIGHT, TOP);
    text("Efecto: " + buffDisplayName, width - 20, 20); // Esquina superior derecha

    translate(width, 0); // Volver al espejo horizontal para la detección
    scale(-1, 1);

    let playerDisplayX = 0;
    let playerDisplayY = 0;
    let currentDedoRadius = BASE_DEDO_RADIO; // Radio actual del dedo para colisiones

    if (handPoseModel && handPoseModel.model) {
        handPoseModel.detect(video, gotHands);
    }

    if (hands.length > 0) {
        let targetKey = fingerKeypoints[activeFinger];

        if (smoothedPoints[targetKey]) {
            let xFinger = smoothedPoints[targetKey].x;
            let yFinger = smoothedPoints[targetKey].y;

            // Calcular escala si el buff de Tamaño Variable está activo
            currentScale = escalaBase;
            if (usarEscaladoPorProfundidad && smoothedPoints["wrist"] && smoothedPoints["index_finger_tip"]) {
                let d = dist(
                    smoothedPoints["wrist"].x, smoothedPoints["wrist"].y,
                    smoothedPoints["index_finger_tip"].x, smoothedPoints["index_finger_tip"].y
                );
                currentScale = map(d, distanciaLejos, distanciaCerca, escalaMinima, escalaMaxima, true);
            }
            currentDedoRadius = BASE_DEDO_RADIO * currentScale; // Actualizar radio según la escala

            // Aplicar buff de espejo en X si está activo
            if (mirrorXActive) {
                playerDisplayX = width - xFinger;
            } else {
                playerDisplayX = xFinger;
            }
            // Aplicar buff de espejo en Y si está activo
            if (mirrorYActive) {
                playerDisplayY = height - yFinger;
            } else {
                playerDisplayY = yFinger;
            }

            // Calcular alfa para fade in y fade out
            let alpha = 255;
            if (elapsedReal < fadeDuration) {
                alpha = map(elapsedReal, 0, fadeDuration, 0, 255);
            } else if (elapsedReal > changeInterval - fadeDuration) {
                alpha = map(elapsedReal, changeInterval - fadeDuration, changeInterval, 255, 0);
            }

            imageMode(CENTER);
            tint(255, alpha);
            image(dedoImg, playerDisplayX, playerDisplayY, 80 * currentScale, 80 * currentScale);
            noTint();

            // Círculo de colisión
            noFill();
            stroke(...fingerColors[activeFinger]);
            strokeWeight(2);
            ellipse(playerDisplayX, playerDisplayY, 80 * currentScale);
        }
    }

    // --- LÓGICA DE COLISIÓN Y ACTUALIZACIÓN DE PELOTAS ---
    // Solo procesar pelotas si ya han aparecido
    if (ballsSpawned) {
        // Verificar si el DEDO detectado está tocando alguna pelota
        // Aplicar la verificación de inmunidad aquí
        if (hands.length > 0 && !juegoTerminado && !isFingerImmune()) {
            for (let ball of balls) {
                // Ahora verificar colisión con la posición suavizada del dedo
                if (ball.isCollidingWithFinger(playerDisplayX, playerDisplayY, currentDedoRadius)) {
                    // Aplicar retardo a la reducción del temporizador
                    if (millis() - lastBallHitTime > BALL_HIT_COOLDOWN) {
                        gameTime = max(0, gameTime - 5); // Reducir tiempo en 5 segundos
                        lastBallHitTime = millis(); // Actualizar último tiempo de golpe
                        
                        // Aplicar retardo a la visualización del texto de colisión
                        if (millis() - lastCollisionTextDisplayTime > COLLISION_DISPLAY_COOLDOWN) {
                            showCollisionText = true; // Mostrar texto de colisión
                            collisionTextStartTime = millis(); // Registrar tiempo
                            lastCollisionTextDisplayTime = millis(); // Actualizar enfriamiento del texto
                        }
                        console.log("¡Colisión con pelota blanca! Tiempo restante: " + gameTime);
                    }
                }
            }
        }

        // Si no hay colisión, actualizar y dibujar las pelotas
        for (let ball of balls) {
            ball.move();      // Actualizar posición
            ball.bounce();    // Rebota si toca los bordes
            ball.display();   // Dibujar la pelota
        }
    }

    // --- LÓGICA DE ESTRELLAS (BUFFS/DEBUFFS) ---
    // Añadir nueva estrella si ha pasado suficiente tiempo y el retardo inicial ha terminado
    if (!firstStarSpawned && millis() - gameStartTime >= STAR_SPAWN_DELAY_INITIAL) {
        addRandomStar();
        lastStarAddTime = millis(); // Inicializar lastStarAddTime para estrellas subsiguientes
        firstStarSpawned = true;
    } else if (firstStarSpawned && millis() - lastStarAddTime > SUBSEQUENT_STAR_SPAWN_DELAY) { // Estrellas subsiguientes cada 20 segundos
        addRandomStar();
        lastStarAddTime = millis();
    }

    // Iterar, dibujar, verificar colisión y eliminar estrellas
    for (let i = stars.length - 1; i >= 0; i--) {
        let s = stars[i];
        fill(s.col);
        noStroke();
        star(s.x, s.y, s.d / 2); // Llamar a la función de dibujo de estrella

        // Eliminar después de 5 segundos si no es tocada
        if (millis() - s.birthTime > 5000) {
            stars.splice(i, 1);
        }
        
        // Verificar colisión con el dedo
        if (hands.length > 0 && !isFingerImmune()) { 
            const starOuterRadius = s.d / 2; // Radio exterior de la estrella para colisión
            if (dist(playerDisplayX, playerDisplayY, s.x, s.y) < (currentDedoRadius + starOuterRadius)) {
                console.log("¡Colisión con estrella de color!");
                stars.splice(i, 1); // Eliminar la estrella al colisionar
                activateRandomBuff(); // Activar un buff aleatorio al recoger la estrella
                score += 20; // Sumar +20 puntaje al tocar una estrella
                showStarText = true; // Mostrar texto "+20 puntos!"
                starTextStartTime = millis(); // Registrar tiempo
            }
        }
    }

    // --- LÓGICA DEL ENEMIGO ACECHANDO ---
    if (enemy.active) {
        let dx = playerDisplayX - enemy.x;
        let dy = playerDisplayY - enemy.y;
        let angle = atan2(dy, dx);
        enemy.x += cos(angle) * enemy.speed;
        enemy.y += sin(angle) * enemy.speed;

        fill(...enemy.color);
        noStroke();
        ellipse(enemy.x, enemy.y, enemy.radius * 2); // Dibujar enemigo como un círculo

        // Verificar colisión con el jugador (dedo)
        if (!isFingerImmune() && dist(enemy.x, enemy.y, playerDisplayX, playerDisplayY) < (enemy.radius + currentDedoRadius)) {
            console.log("¡Golpeado por el enemigo!");
            gameTime = max(0, gameTime - 5); // Reducir tiempo en 5 segundos
            
            // Aplicar retardo a la visualización del texto de colisión
            if (millis() - lastCollisionTextDisplayTime > COLLISION_DISPLAY_COOLDOWN) {
                showCollisionText = true; // Mostrar texto de colisión
                collisionTextStartTime = millis(); // Registrar tiempo
                lastCollisionTextDisplayTime = millis(); // Actualizar enfriamiento del texto
            }
            enemy.active = false; // El enemigo desaparece después de un golpe
        } else if (millis() - enemy.startTime > BUFF_DURATION) { // El enemigo desaparece después de la duración del buff
            enemy.active = false;
            console.log("El enemigo desapareció por tiempo");
        }
    }

    // --- VISIBILIDAD REDUCIDA: oscurecer todo menos el área del dedo activo ---
    if (lowVisibilityActive) {
        resetMatrix(); // Reiniciar transformaciones para aplicar la oscuridad a todo el canvas
        drawingContext.save();

        // Usar playerDisplayX y playerDisplayY para el centro del círculo visible
        let gradient = drawingContext.createRadialGradient(playerDisplayX, playerDisplayY, radioVision - suavizadoBorde, playerDisplayX, playerDisplayY, radioVision);
        gradient.addColorStop(0, `rgba(0, 0, 0, 0)`);
        gradient.addColorStop(1, `rgba(0, 0, 0, ${opacidadOscuridad / 255})`);

        drawingContext.fillStyle = gradient;
        drawingContext.fillRect(0, 0, width, height);

        drawingContext.restore();
        translate(width, 0); // Restaurar la transformación de espejo original
        scale(-1, 1);
    }

    // --- LÓGICA DE RELOJ DE ARENA ---
    // Añadir nuevo reloj de arena si ha pasado suficiente tiempo y el retardo inicial ha terminado
    if (!firstHourglassSpawned && millis() - gameStartTime >= HOURGLASS_SPAWN_DELAY_INITIAL) {
        addRandomHourglass();
        lastHourglassAddTime = millis(); // Inicializar lastHourglassAddTime para relojes subsiguientes
        firstHourglassSpawned = true;
    } else if (firstHourglassSpawned && millis() - lastHourglassAddTime > SUBSEQUENT_HOURGLASS_SPAWN_DELAY) { // Aparece cada 2 segundos
        addRandomHourglass();
        lastHourglassAddTime = millis();
    }

    // Iterar, dibujar, verificar colisión y eliminar relojes de arena
    for (let i = hourglasses.length - 1; i >= 0; i--) {
        let hr = hourglasses[i];
        fill(255, 165, 0); // Color naranja
        noStroke();
        drawHourglass(hr.x, hr.y, hr.d); // Dibujar el reloj de arena

        // Eliminar después de 5 segundos si no es tocado
        if (millis() - hr.birthTime > HOURGLASS_LIFETIME) {
            hourglasses.splice(i, 1);
        }
        
        // Verificar colisión con el dedo
        if (hands.length > 0 && !isFingerImmune()) { 
            const hourglassRadius = hr.d / 2; // Radio aproximado para colisión
            if (dist(playerDisplayX, playerDisplayY, hr.x, hr.y) < (currentDedoRadius + hourglassRadius)) {
                console.log("¡Colisión con reloj de arena!");
                gameTime = gameTime + 3; // Sumar 3 segundos al tiempo de juego
                hourglasses.splice(i, 1); // Eliminar el reloj de arena al colisionar
                
                showHourglassText = true; // Mostrar texto "+3 segundos!"
                hourglassTextStartTime = millis(); // Registrar tiempo
            }
        }
    }

    // --- LÓGICA DE BARRAS DE ORO ---
    if (millis() - lastGoldBarAddTime > GOLDBAR_SPAWN_DELAY) {
        addRandomGoldBar();
        lastGoldBarAddTime = millis();
    }

    for (let i = goldBars.length - 1; i >= 0; i--) {
        let gb = goldBars[i];
        fill(255, 215, 0); // Color dorado
        noStroke();
        drawGoldBar(gb.x, gb.y, gb.width, gb.height); // Dibujar la barra de oro

        // Eliminar después de 5 segundos si no es tocada
        if (millis() - gb.birthTime > GOLDBAR_LIFETIME) {
            goldBars.splice(i, 1);
        }
        
        // Verificar colisión con el dedo
        if (hands.length > 0 && !isFingerImmune()) { 
            // Radio aproximado para colisión rectangular. Usando la diagonal/2 para simplicidad
            const goldBarCollisionRadius = dist(0, 0, gb.width / 2, gb.height / 2); 
            if (dist(playerDisplayX, playerDisplayY, gb.x, gb.y) < (currentDedoRadius + goldBarCollisionRadius)) {
                console.log("¡Colisión con barra de oro!");
                score += 10; // Sumar 10 puntos
                goldBars.splice(i, 1); // Eliminar la barra de oro al colisionar
                
                showGoldBarText = true; // Mostrar texto "+10 puntos!"
                goldBarTextStartTime = millis(); // Registrar tiempo
            }
        }
    }

    // === ACTUALIZACIÓN DE PUNTAJE Y TIEMPO ===
    // Solo actualizar si la información del juego se muestra (después del retardo inicial) y el juego no ha terminado
    if (displayGameInfo && millis() - lastSecondUpdate >= 1000 && gameTime > 0) {
        gameTime--; // Reducir tiempo en 1 segundo
        score += 1; // Incrementar puntaje en 1 por segundo
        lastSecondUpdate = millis();
    }

    // Mostrar texto de colisión si está activo
    if (showCollisionText && millis() - collisionTextStartTime < COLLISION_TEXT_DURATION) {
        resetMatrix();
        fill(255, 0, 0); // Color rojo
        textSize(36);
        textAlign(CENTER, CENTER);
        text("¡COLISIÓN -5 SEGUNDOS!", width / 2, height / 2 + 100);
        translate(width, 0);
        scale(-1, 1);
    } else if (showCollisionText) {
        showCollisionText = false;
    }

    // Mostrar texto de reloj de arena si está activo
    if (showHourglassText && millis() - hourglassTextStartTime < HOURGLASS_TEXT_DURATION) {
        resetMatrix();
        fill(0, 255, 0); // Color verde
        textSize(36);
        textAlign(CENTER, CENTER);
        text("¡+3 SEGUNDOS!", width / 2, height / 2 + 150);
        translate(width, 0);
        scale(-1, 1);
    } else if (showHourglassText) {
        showHourglassText = false;
    }

    // Mostrar texto de estrella si está activo
    if (showStarText && millis() - starTextStartTime < STAR_TEXT_DURATION) {
        resetMatrix();
        fill(255, 255, 0); // Color amarillo
        textSize(36);
        textAlign(CENTER, CENTER);
        text("¡+20 PUNTOS!", width / 2, height / 2 + 200);
        translate(width, 0);
        scale(-1, 1);
    } else if (showStarText) {
        showStarText = false;
    }

    // Mostrar texto de nuevo efecto si está activo
    if (showNewEffectText && millis() - newEffectTextStartTime < NEW_EFFECT_TEXT_DURATION) {
        resetMatrix();
        fill(255, 0, 0); // Color rojo
        textSize(72);
        textAlign(CENTER, CENTER);
        text("NUEVO EFECTO:\n" + newEffectName, width / 2, height / 2);
        translate(width, 0);
        scale(-1, 1);
    } else if (showNewEffectText) {
        showNewEffectText = false;
    }

    // Mostrar texto de barra de oro si está activo
    if (showGoldBarText && millis() - goldBarTextStartTime < GOLDBAR_TEXT_DURATION) {
        resetMatrix();
        fill(255, 255, 0); // Color amarillo
        textSize(36);
        textAlign(CENTER, CENTER);
        text("¡+10 PUNTOS!", width / 2, height / 2 + 250);
        translate(width, 0);
        scale(-1, 1);
    } else if (showGoldBarText) {
        showGoldBarText = false;
    }
}

// Función para terminar el juego
function terminarJuego() {
    juegoTerminado = true;
    noLoop();
    console.log("¡Juego Terminado!");
}

// Función para añadir una estrella aleatoria
function addRandomStar() {
    // Posición aleatoria, respetando el padding del borde
    let x = random(STAR_BORDER_PADDING, width - STAR_BORDER_PADDING);
    let y = random(STAR_BORDER_PADDING, height - STAR_BORDER_PADDING);

    // Diámetros predefinidos
    let d = random(50, 70); // Tamaño aleatorio
    let col = yellowColor; // Color amarillo fijo

    stars.push({ x, y, d, col, birthTime: millis() });
}

// Función auxiliar para dibujar una estrella
function star(x, y, radius) {
  let points = 5; // Estrella de 5 puntas
  let innerRadius = radius * 0.4; // Radio de los puntos interiores
  let outerRadius = radius; // Radio de los puntos exteriores

  let angle = TWO_PI / points;
  let halfAngle = angle / 2.0;
  beginShape();
  for (let a = 0; a < TWO_PI; a += angle) {
    let sx = x + cos(a) * outerRadius;
    let sy = y + sin(a) * outerRadius;
    vertex(sx, sy);
    sx = x + cos(a + halfAngle) * innerRadius;
    sy = y + sin(a + halfAngle) * innerRadius;
    vertex(sx, sy);
  }
  endShape(CLOSE);
}

// Función auxiliar para dibujar un reloj de arena
function drawHourglass(x, y, size) {
    let halfSize = size / 2;
    let quarterSize = size / 4;

    beginShape();
    // Esquina superior izquierda
    vertex(x - halfSize, y - halfSize);
    // Esquina superior derecha
    vertex(x + halfSize, y - halfSize);
    // Centro derecho (cintura)
    vertex(x + quarterSize, y);
    // Esquina inferior derecha
    vertex(x + halfSize, y + halfSize);
    // Esquina inferior izquierda
    vertex(x - halfSize, y + halfSize);
    // Centro izquierdo (cintura)
    vertex(x - quarterSize, y);
    endShape(CLOSE);
}

// Función para añadir un reloj de arena aleatorio
function addRandomHourglass() {
    // Asegurar que el reloj de arena aparezca dentro de los límites
    let x = random(HOURGLASS_BORDER_PADDING, width - HOURGLASS_BORDER_PADDING);
    let y = random(HOURGLASS_BORDER_PADDING, height - HOURGLASS_BORDER_PADDING);
    let d = random(40, 60); // Tamaño aleatorio para el reloj de arena
    hourglasses.push({ x, y, d, birthTime: millis() });
}

// Función auxiliar para dibujar una barra de oro (rectángulo horizontal)
function drawGoldBar(x, y, barWidth, barHeight) {
    rectMode(CENTER);
    rect(x, y, barWidth, barHeight, 5); // Esquinas redondeadas
    rectMode(CORNER); // Restablecer al modo predeterminado
}

// Función para añadir una barra de oro aleatoria
function addRandomGoldBar() {
    let barWidth = random(50, 70); // Ancho similar al tamaño del reloj de arena
    let barHeight = random(30, 50); // Altura más pequeña para ser horizontal
    // Asegurar que aparezca dentro de los límites, considerando el ancho y la altura
    let x = random(GOLDBAR_BORDER_PADDING + barWidth / 2, width - GOLDBAR_BORDER_PADDING - barWidth / 2);
    let y = random(GOLDBAR_BORDER_PADDING + barHeight / 2, height - GOLDBAR_BORDER_PADDING - barHeight / 2);
    goldBars.push({ x, y, width: barWidth, height: barHeight, birthTime: millis() });
}

// Clase que define el comportamiento de cada pelota
class Ball {
    constructor() {
        this.x = random(width);      // Posición X aleatoria
        this.y = random(height);     // Posición Y aleatoria
        this.d = random(15, 40);     // Diámetro aleatorio
        this.xspeed = random(-2, 2); // Velocidad X aleatoria
        this.yspeed = random(-2, 2); // Velocidad Y aleatoria
        this.color = [255, 255, 255]; // Color de la pelota es blanco fijo
    }

    move() {
        // Añadir una pequeña variación aleatoria a la velocidad
        this.xspeed += random(-0.1, 0.1);
        this.yspeed += random(-0.1, 0.1);

        // Limitar la velocidad para evitar que sea demasiado alta
        this.xspeed = constrain(this.xspeed, -3, 3);
        this.yspeed = constrain(this.yspeed, -3, 3);

        // Actualizar la posición usando la velocidad
        this.x += this.xspeed;
        this.y += this.yspeed;
    }

    bounce() {
        // Calcular el radio para la colisión
        let r = this.d / 2;

        // Invertir la dirección X y ajustar la posición si golpea los bordes horizontales
        if (this.x < r) {
            this.x = r; // Establecer la posición exactamente en el borde
            this.xspeed *= -1;
        } else if (this.x > width - r) {
            this.x = width - r; // Establecer la posición exactamente en el borde
            this.xspeed *= -1;
        }

        // Invertir la dirección Y y ajustar la posición si golpea los bordes verticales
        if (this.y < r) {
            this.y = r; // Establecer la posición exactamente en el borde
            this.yspeed *= -1;
        } else if (this.y > height - r) {
            this.y = height - r; // Establecer la posición exactamente en el borde
            this.yspeed *= -1;
        }
    }

    display() {
        fill(...this.color);       // Color de la pelota (siempre blanco)
        noStroke();                // Sin contorno
        ellipse(this.x, this.y, this.d); // Dibujar la elipse
    }

    // Método adaptado para verificar colisión con la posición del dedo
    isCollidingWithFinger(fingerX, fingerY, fingerRadius) {
        // Verificar si la distancia entre el dedo y el centro de la pelota es menor que la suma de sus radios
        return dist(fingerX, fingerY, this.x, this.y) < (this.d / 2 + fingerRadius);
    }
}
~~~
</details>

        
## Repartición del Trabajo

De forma inicial, el desarrollo del proyecto se estructuró en torno a una fase base destinada a implementar y validar las funciones esenciales sobre las que se construiría el resto de la experiencia interactiva:

1. Detección puntos de dedos

2. Definición de obstáculos constantes.

3. Tiempo límite y sistema de puntuación

4. Aparición procesual de objetos para extender el tiempo límite o añadir puntaje

5. Adición de textos

Una vez validado el correcto funcionamiento de la experiencia base, se procedió a la segunda etapa: el diseño e implementación de las distintas variables de dificultad. Finalmente, se integraron todos los módulos en un único código, resolviendo inconsistencias y asegurando una ejecución fluida y coherente del conjunto del sistema, realizando el manejo final de las configuraciones en los parámetros de variables beneficiando el sentimiento de una jugabilidad amigable al jugador.

## Bibliografía 
* Usamos la biblioteca p5.js v1.10.0. para hacer el código del proyecto, buscando ejemplos y/o referencias para el correcto funcionamiento; y la biblioteca ml5.js, sobre todo las secciones:
1. [ml5js HandPose](https://docs.ml5js.org/#/reference/handpose)
2. [p5js Mask](https://p5js.org/reference/p5.Image/mask/)
3. [p5js circle](https://p5js.org/reference/p5/circle/)
4. [Shake Ball Bounce](https://p5js.org/examples/classes-and-objects-shake-ball-bounce/)
 
* Entre otras bibliotecas tambien usamos las siguientes fuentes de informacion: 
[MDN web docs](https://developer.mozilla.org/en-US/)
[W3Schools](https://www.w3schools.com/)
[The Coding Train](https://www.youtube.com/@TheCodingTrain)

* Tambien inspiramos el codigo en el juego [Circle Clicker](https://p5js.org/examples/games-circle-clicker/)
  
* Para la pantalla de inicio, nos basamos en un tutorial desarrollado por Coding Adventures en [Codeguppy](https://codeguppy.com/code.html?QlkPptXLAzVpXxnR3Qy7)  

* El código usado para la primera fase de detección es ["HandPose-Draw with Index Finger" by re7l](https://editor.p5js.org/re7l/sketches/pd-SZ8lfA)
  
* Apartado de optimizar y suavizar flujo de movimiento: [referencia código lerp() en repositorio p5](https://p5js.org/reference/p5/lerp/?utm_source=chatgpt.com)
  
* [Ejemplo de suavizar movimiento con código lerp() en repositorio p5](https://p5js.org/examples/calculating-values-interpolate/?utm_source=chatgpt.com)
* Uso de diversos foros y vídeos de creación de videojuegos
* Uso de apuntes en clases
* Dentro de otras fuentes de información nos basamos en proyectos vistos en clases como lo fue el de Don Francisco (if/boolean) y también en preguntas realizadas en clases (colisión de los círculos)


## Conclusiones
El desarrollo de este proyecto dejó en evidencia que muchas de las problemáticas anticipadas en las etapas iniciales no solo persisten, sino que se intensificaron a medida que el sistema fue ganando complejidad. En primer lugar, la dependencia de factores externos como la calidad de la cámara y la iluminación del entorno continuó afectando significativamente la precisión en la detección de dedos. Estos elementos, si bien previsibles, generaron confusiones que impactaron directamente en la experiencia del usuario. Además, surgieron problemas inesperados vinculados al entorno de ejecución, especialmente relacionados con las diferencias entre navegadores. En ciertas pruebas, algunas variables dejaron de comportarse como se esperaba, debido a restricciones internas que limitaban la cantidad máxima de puntos que podían ser detectados de forma simultánea. Esta incompatibilidad ralentizó parte del proceso de trabajo hasta que se encontró cuál era verdaderamente el problema y se consiguió solucionar. Otra dificultad relevante fue la necesidad de optimizar minuciosamente cada uno de los módulos desarrollados para asegurar un rendimiento fluido en el ensamblaje final. Esto implicó realizar pruebas constantes, establecer tiempos de espera adecuados para la carga completa de los modelos y sincronizar correctamente la ejecución de las distintas variables a lo largo del juego para evitar interrupciones o errores en tiempo real. Como idea final, rescatar el aspecto clave que facilitó la resolución de estos desafíos fue la el poder definir todo el proyecto en distintas fases y trabajar de manera independiente las variables. Esta estrategia no solo ayudó a mantener la claridad del proceso, sino que también permitió implementar soluciones alternativas no contempladas en un inicio, pero que resultaron eficaces para cumplir con los objetivos funcionales y de diseño del proyecto definidas en un inicio.

La falta de una optimización óptima (valga la redundancia), nos vio limitados a tener que dejar de lado conceptos que tuvimos en el proyecto inicial (entrega 1) como la ambientación de mundo, incluyendo el background; el darle un una caracterización visual a los elementos como los enemigos, objetos y obstáculo; y la adición de un nivel bonus. Esto se debe a que de por si el código comenzaba a fallar o a funcionar de manera no deseada, por lo cual como decisión dejamos este concepto de lado.
