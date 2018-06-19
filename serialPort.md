#Programacion en arduino

Un programa de Arduino se denomina sketch o proyecto y tiene la extensión .ino. Importante: para que funcione el sketch, el nombre del fichero debe estar en un directorio con el mismo nombre que el sketch.

No es necesario que un sketch esté en un único fichero, pero si es imprescindible que todos los ficheros estén dentro del mismo directorio que el fichero principal.

La estructura básica de un sketch de Arduino es bastante simple y se compone de al menos dos partes. Estas dos partes son obligatorios y encierran bloques que contienen declaraciones, estamentos o instrucciones.

´´´ c
void setup() {
 // put your setup code here, to run once:
}
 
void loop() {
 // put your main code here, to run repeatedly:
}
´´´

**Setup**

Es la primera que ejecuta el programa. Es la encargada de recoger las configuraciones, entre ellas, declarar los pines como de entrada/salida, establecer el tipo de comunicación en serie y otras. Se ejecuta una sola vez.

**Loop**

Es ejecutada cíclicamente (de ahí su nombre, bucle en inglés). Dentro del bloque de la misma se coloca el código que va a ser ejecutado continuamente (lectura de entradas, activación de salidas y demás). Es el núcleo del programa y el que realiza la mayor parte del trabajo.

## Primer programa

Como una introducción a la programación de Arduino, haremos un programa simple. El famoso "Hola Mundo".

´´´ sketch
void setup() {

  Serial.begin(9600); //Iniciamos el puerto serial en 9600 baudios
  Serial.write("Hola mundo"); //Escribimos por puerto serial "Hola mundo"
}

void loop() {
}
´´´
___
Siempre que necesitemos utilizar el puerto serial, lo iniciaremos con la función *Serial.begin()*.
El parámetro que recibe es la velocidad de comunicacion y se mide en baudios. Lo iniciamos en 9600 porque es la veolocidad default de comunicacion con el conversor serial. 

Para escribir por *serial port* lo haremos con *Serial.write()* o bien *Serial.print()* para variables de la clase String(). 

Antes de ver el siguiente ejercicio, haremos la siguiente observación.
En un ***sketch*** no tenemos el tipo *string* como tal, sino que utilizamos cadenas de caracteres para declarar variables de este tipo. 
En este caso usaremos la clase *String()*, pues si utilizaramos *char nombre[10]* nos generaría problemas de incopatibilidad de tipos en la línea 9: *nombre=Serial.readString()*.

´´´ sketch
String nombre = String(10);

void setup() {
  Serial.begin(9600); //Iniciamos el puerto serial en 9600 baudios
  Serial.write("¿Saludos, cual es su nombre?\n"); //con "\n" pasamos al siguiente renglon!
}

void loop() {
  nombre=Serial.readString(); //Leemos el nombre del usuario
  if (nombre != 0) { //Imprime solo si el usuario ingresa una string
    Serial.write("Bienvenido al puerto serial ");
    Serial.print(nombre);
  }
}

___
Ya manejamos lectura y escritura por puerto serial, ahora hagamos algo mas interesante. Vamos a controlar los leds de la placa mediante puerto serial.

´´´ sketch
String command = String(5);
int led1=5;
int led2=6;
int led3=7;

void setup() {

  Serial.begin(9600); //Iniciamos el puerto serial en 9600 baudios
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);
  Serial.println(" Comandos: l1, l2 y l3 para encender los leds 1, 2 y 3 respectivamente\n");
  Serial.println(" Y lo1, lo2 y lo3 para apagar los leds 1, 2 y 3 respectivamente\n");
  

}

void loop() {
 
  command=Serial.readString();
  if (command=="l1")
    digitalWrite(led1, HIGH);
  if (command=="l2")
    digitalWrite(led2, HIGH);
  if (command=="l3")
    digitalWrite(led3, HIGH);
  if (command=="lo1")
    digitalWrite(led1, LOW);
  if (command=="lo2")
    digitalWrite(led2, LOW);
  if (command=="lo3")
    digitalWrite(led3, LOW);
}
´´´
___
Por último haremos una implementación para el sensor de temperatura **LM35**

´´´ sketch
float readT; 
float tempC;
int i;
int pinLM35 = 6; 
char unidad;
 
void setup() {
  Serial.begin(9600);
  Serial.println("Comandos: c, f y k para Celcius Fahrenheit y Kelvin respectivamente");
  Serial.println("Para tomar nueva muestra utilizar el comando r!");
  Serial.println("");

}
 
void loop() {
  while (i<20) {
    readT=analogRead(pinLM35); 
    tempC=tempC + (5.0 * readT * 100.0)/1024.0; 
    i++;
  }  
  unidad=char(Serial.read());
  if (unidad=='c') {
    Serial.println("Temperatura en grados Celcius: ");
    Serial.print(tempC/i);
    Serial.println(" °C");    
    Serial.println("");
  }

  if (unidad=='f') {
    Serial.println("Temperatura en grados Fahrenheit: ");
    Serial.print(tempC/i*1.8+32);
    Serial.println(" °F");
    Serial.println("");
  }
  if (unidad=='k') {
    Serial.println("Temperatura absoluta: ");
    Serial.print(tempC/i+273.15);
    Serial.println(" K");
    Serial.println("");
  }
  if (unidad=='r') {
    i=0;
    tempC=0;
  }
}
´´´






























