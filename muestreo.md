#Muestreo con EDUKIT10

EDUKIT10 cuenta con 2 sensores integrados a la placa, el sensor de temperatura LM35 y el sensor de luz LDR. 

##LM35

 El LM35 es un sensor de temperatura digital. A diferencia de otros dispositivos como los termistores en los que la medición de temperatura se obtiene de la medición de su resistencia eléctrica, el LM35 es un integrado con su propio circuito de control, que proporciona una salida de voltaje proporcional a la temperatura.

La salida del LM35 es lineal con la temperatura, incrementando el valor a razón de 10mV por cada grado centígrado. El rango de medición es de -55ºC (-550mV) a 150ºC (1500 mV). Su precisión a temperatura ambiente es de 0,5ºC.

Comenzaremos con un código simple que solo muestra lo que mide el sensor por el puerto serial.

´´´ sketch
int pinLM35= 6;
float muestra=0;

void setup() {
	Serial.begin(9600);
}

void loop() {
	muestra=analogRead(pinLM35);
	Serial.println(muestra);
}
´´´
Los valores que obtenemos con la función *analogRead()* van de 0 a 1023, si tenemos 0V a la entrada nos devolverá 0 y si tenemos 5V nos devolverá 1023.
Es decir que todavía nos falta convertir estos valores a grados centígrados.

Como la relación voltaje-grados centígrados esta dada en mV, vamos a convertir los valores tomados por *analogRead()* a milivoltios (mV), mediante una regla de tres simple.

1023 - 5V=5000mV 
*muestra* - ? 

Entonces *millivolts=muestra.5000/1023*.
Solo nos queda pasar los milivoltios a °C. Pero 1°C=10mV entonces, 
*tempC=millivolts/10*

Solo queda agregar esto a nuestro código.

´´´ sketch
int pinLM35= 6;
float muestra=0;
float millivolts;
float celsius;

void setup() {
	Serial.begin(9600);
}

void loop() {
	muestra=analogRead(pinLM35);
	Serial.print("Valor tomado por analogRead: ");
	Serial.println(muestra);

	millivolts = (muestra / 1023.0) * 5000;
	celsius = millivolts / 10; 
	Serial.print("Grados centígrados: ");
	Serial.println(celsius);
	Serial.println("");
	delay(1000);
}
´´´
___

Si bien ya sabemos tomar muestras de temperatura en °C, no hemos hecho mas que imprimirlas por puerto serial. 
¿Y si hacemos un código para analizar un poco mas a fondo las muestras?

´´´ sketch
int pinLM35= 6;
float muestra=0;
float muestras[20];
float millivolts;
float celsius;
int i=0;
float sum=0;
float tmax=-70;
float tmin=200;

void setup() {
	Serial.begin(9600);
}

void loop() {
	Serial.print("Tomando muestras");
	while(i<20){
	    Serial.print(".");
	    muestra=analogRead(pinLM35);
	    millivolts = (muestra / 1023.0) * 5000;
	    celsius = millivolts / 10; 
	        
	    muestras[i]=celsius; 
	    sum=sum+celsius;
	    tmax=max(tmax, celsius);
	    tmin=min(tmin, celsius);
	    
	    i++;
	    delay(1000);
  	}
	if(i=20) {
		Serial.println("");
		Serial.print("Temperatura máxima: ");
		Serial.println(tmax);
		Serial.print("Temperatura mínima: ");
		Serial.println(tmin);
		Serial.print("Temperatura promedio: ");
		Serial.println(sum/20);
		Serial.println("");
		delay(10000);
		i=0;
	}
}
´´´
La idea en este caso es guardar las muestras tomadas en intervalos de 1 segundo (esto viene dado por *delay(1000)*) en un arreglo, para luego poder analizarlas.
Si observamos el código presentado, las muestras se almacenan en un arreglo de reales definido como *float muestras[20]*. Si bien en este caso tomamos 20 muestras, podemos tomar más o menos según necesitemos, e incluso modificar el intervalo de tiempo entre la toma de una muestra y la siguiente.

Otra observación importante es que el análisis corre en tiempo real, es decir, a medida que tomamos cada muestra vamos generando *tmax*, *tmin* y acumulamos las temperaturas en *sum*, para luego calcular la media. 
Si bien no necesitamos el arreglo *muestras* en nuestro análisis, es importante que tengamos un registro de las muestras tomadas para el caso en el que necesitemos acceder nuevamente a ellas.

Para recorrer un arreglo de tamaño fijo, podemos usar la estructura *for*.

´´´ sketch
for (int j=0; j<20; j++) {
	tmax=max(tmax, muestras[j]);
	tmin=min(tmin, muestras[j]);
	sum=sum+muestras[j];
}
´´´
____
##LDR

Un fotoresistor, o LDR (light-dependent resistor) es un dispositivo cuya resistencia varia en función de la luz recibida. Podemos usar esta variación para medir, a través de las entradas analógicas, una estimación del nivel del luz.
Por tanto, un fotoresistor disminuye su resistencia a medida que aumenta la luz sobre él. Los valores típicos son de 1 Mohm en total oscuridad, a 50-100 Ohm bajo luz brillante.

Por otro lado, la variación de la resistencia es relativamente lenta, de 20 a 100 ms en función del modelo. Esta lentitud hace que no sea posible registrar variaciones rápidas, como las producidas en fuentes de luz artificiales alimentadas por corriente alterna. Este comportamiento puede ser beneficioso, ya que dota al sensor de una gran estabilidad.

Finalmente, los fotoresistores no resultan adecuados para proporcionar una medición de la iluminancia, es decir, para servir como luxómetro Esto es debido a su baja precisión, su fuerte dependencia con la temperatura y, especialmente, a que su distribución espectral no resulta adecuada para la medición de iluminancia.

Por tanto, un LDR es una sensor que resulta adecuado para proporcionar medidas cuantitativas sobre el nivel de luz, tanto en interiores como en exteriores, y reaccionar, por ejemplo, encendiendo una luz, subiendo una persiana, u orientando un robot.

En este caso simularemos el encendido de una luz, con los leds de la EDUKIT10.

´´´ sketch
int led1 = 5; 
int led2 = 6;
int led3 = 7;
int LDR = A7;
int value;

void lights(String cmd){
	if(cmd=="on") {
		digitalWrite(led1, HIGH);
		digitalWrite(led2, HIGH);
		digitalWrite(led3, HIGH);
	}
	if(cmd=="on") {
		digitalWrite(led1, LOW);
		digitalWrite(led2, LOW);
		digitalWrite(led3, LOW);
	}
}

void setup() {
	pinMode(led1, OUTPUT);
	pinMode(led2, OUTPUT);
	pinMode(led3, OUTPUT);
}

void loop() {
	value=analogRead(LDR);
	if(value<300)
		lights("on");
	else
		lights("off");

}
´´´
De esta manera cuando disminuyamos la cantidad de luz recibida por el sensor, los leds se encenderán. Para hacer esto de manera mas eficiente es recomendable tener una idea de los valores que el sensor lee. Para ello debemos tomar algunas muestras antes de cargar nuestro código a la placa, así podremos ajustar de mejor manera el valor necesario para encender nuestros leds.
No entraremos en detalle de la toma de muestras de luz, pues es análogo al muestreo hecho con el LM35.

