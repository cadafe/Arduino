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
Siempre que necesitemos utilizar el puerto serial, lo iniciaremos con la función *Serial.begin()*.
El parámetro que recibe es la velocidad de comunicacion y se mide en baudios. Lo iniciamos en 9600 porque es la veolocidad default de comunicacion con el conversor serial. 

Para escribir por *serial port* lo haremos con *Serial.write()* o bien *Serial.writeln()*. 

Veamos otro ejemplo

´´´ sketch

string nombre;

void setup() {

  Serial.begin(9600); //Iniciamos el puerto serial en 9600 baudios
  Serial.write("¿Saludos, cual es su nombre?"); 
  nombre=Serial.read();
  Serial.writeln("Bienvenido al puerto serial ");
  Serial.write(nombre);

}

void loop() {
}
´´´
En este caso incluimos una variable *nombre* de tipo string para almacenar el nombre del usuario mediante la función *Serial.read()*.
___

Utilizando las funciones vistas de *Serial*, controlaremos algunos componentes de la EDUKIT10 mediante puerto serial.

´´´ sketch

string command;
int num;
int led1=5;
int led2=6;
int led3=7;

void setup() {
	Serial.begin(9600);
	Serial.write("Commands: led");	
}

void loop() {
	command=Serial.read();
	if (command == led) {
		Serial.write("Input led/s number/s. Ej 1 for led 1, or 12 for leds 1 & 2");
		num=Serial.read();
		//Consideramos todas las posibilidades que el usuario pueda ingresar, desde 1 nro a 3 y todas sus combinaciones
		
		if (num == 1)
			digitalWrite(led1, HIGH);
		if (num == 2)
			digitalWrite(led2, HIGH);
		if (num == 3)
			digitalWrite(led3, HIGH);
		if (num == 12 or num == 21){
			digitalWrite(led1, HIGH);
			digitalWrite(led2, HIGH);
		}
		if (num == 13 or num == 31){}
			digitalWrite(led1, HIGH);
			digitalWrite(led3, HIGH);
		}
		if (num == 23 or num == 32){
			digitalWrite(led2, HIGH);
			digitalWrite(led3, HIGH);
		}
		if (num == 123 or num == 132 or num == 312 or num == 321 or num == 231 or num == 213) {
			digitalWrite(led1, HIGH);
			digitalWrite(led2, HIGH);
			digitalWrite(led3, HIGH);
		}
	}		
}
´´´

































