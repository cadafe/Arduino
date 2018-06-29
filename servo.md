#Servo Motor

Hay varios modelos de servomotor con Arduino, nosotros utilizaremos un Micro Servo 9g SG90 de Tower Pro. Todos tienen un funcionamiento muy parecido y la programación puede variar muy poco, pero siempre debemos msirar la ficha técnica del producto.

Lo primero que debemos tener en cuenta es el ángulo de giro, en este caso nos permite hacer un barrido entre -90º y 90º. Lo que viene a ser un ángulo de giro de 180º.

Aunque el servo puede moverse con una resolución de más de 1 grado, este es el máximo de resolución que vamos a conseguir debido a la limitación de la señal PWM que es capaz de generar Arduino.

Estos motores funcionan con una señal PWM, con un pulso de trabajo entre 1 ms y 2 ms y con un periodo de 20 ms (50 Hz). ¿Qué quiere decir todo esto? Este dato nos indica la velocidad máxima a la que podemos mover el servomotor con Arduino. Solo podremos cambiar de posición cada 20 ms. Esto dependerá del tipo y marca de nuestro servo.

El elegir una salida PWM u otra da lo mismo, todas las salidas de este tipo funcionan igual.
___
Ahora vamos a ir a lo interesante, la programación. Para ello tenemos que hacer uso de una librería externa para poder controlar el servo desde Arduino. Para incorporar la librería tenemos que ir a Programa/Include Library/Servo.

Para introducirnos en el tema, comenzaremos programando el servo para que se mueva entre 0° y 90°.

´´´ sketch
// Incluímos la librería para poder controlar el servo
#include <Servo.h>

// Declaramos la variable para controlar el servo
Servo servoMotor;
int pinServo=10;

void setup() {
  // Iniciamos el monitor serie para mostrar el resultado
  Serial.begin(9600);

  // Iniciamos el servo para que empiece a trabajar con el pin 9
  servoMotor.attach(pinServo);
}

void loop() {
  // Desplazamos a la posición 0º
  servoMotor.write(0);
  // Esperamos 1 segundo
  delay(1000);
  
  // Desplazamos a la posición 90º
  servoMotor.write(90);
  // Esperamos 1 segundo
  delay(1000);
}
´´´
___
A partir de este punto, sabiendo modificar el ángulo del servo, podemos programarlo para muchos usos. Por ejemplo, abrir una puerta cuando alguien se aproxima, abrir o cerrar una persiana de acuerdo a la cantidad de luz del día, levantar una barrera cuando se aproxima un vehículo, etc.

Haciendo uso de los recursos de la EDUKIT10, podemos simular el último ejemplo. Con el sensor LDR y en un ambiente donde la luz no varíe demasiado, podemos simular un sensor de movimiento. Si lo ubicamos estratégicamente, podemos saber si un objeto esta próximo, detectando los cambios de luz. 
Primero debemos conocer los valores que lee el LDR en nuestro ambiente, cuando no hay ningún objeto cerca del sensor. A partir de esto, cuando los valores leídos bajen de la media, sabremos que algún objeto obstaculiza el sensor y por ende debemos levantar nuestra barrera.

En nuestro caso, en condiciones normales, el sensor toma valores próximos a 750. Cuando hacemos algo de sombra, los valores leídos bajan a 620. Usaremos esto para crear nuestra condición.

´´´ sketch
#include <Servo.h>

Servo servoMotor;
int pinServo= 10;
int LDR= A7;
int value;

void setup() {
  // Iniciamos el monitor serie para mostrar el resultado
  Serial.begin(9600);

  // Iniciamos el servo para que empiece a trabajar con el pin 9
  servoMotor.attach(pinServo);
}

void loop() {
  value=analogRead(LDR);

  if(value < 650)
    servoMotor.write(90); //Barrera levantada.
  else 
    servoMotor.write(0);  //Asumimos 0° como la posición horizontal de la barrera
    
  delay(500);
}
´´´
Agreguemos una luz verde para el paso, y una luz roja para cuando baja la barrera. 

´´´ sketch
#include <Servo.h>

Servo servoMotor;
int pinServo= 10;
int LDR= A7;
int verde= 5;
int rojo= 7;
int value;

void setup() {
  // Iniciamos el monitor serie para mostrar el resultado
  Serial.begin(9600);
  pinMode(verde, OUTPUT);
  pinMode(rojo, OUTPUT);
  // Iniciamos el servo para que empiece a trabajar con el pin 9
  servoMotor.attach(pinServo);
}

void loop() {
  value=analogRead(LDR);

  if(value < 650){
    servoMotor.write(80);
    digitalWrite(verde, HIGH);
    digitalWrite(rojo, LOW);
  }
  else { 
    servoMotor.write(0);
    digitalWrite(verde, LOW);
    digitalWrite(rojo, HIGH);
  }
  delay(500);
}
´´´
Más allá de los detalles, el trabajo con servos se reduce a construir condiciones, para modificar ángulos de acuerdo a nuestras necesidades. Para ello, vale aclarar que podemos necesitar sensores diferentes a los utilizados en los ejemplos.


