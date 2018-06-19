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
    
    i=i+1;
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
Si observamos el código presentado, las muestras se almacenan en un arreglo de reales definido como *float muestras[20]*. Si bien en este caso tomamos 20 muestras, podemos tomar más o menos según necesitemos, e incluso modificar el intervalo de tiempo entre una muestra y la siguiente.
Otra observación importante es que el análisis corre en tiempo real, es decir, a medida que tomamos cada muestra vamos generando *tmax*, *tmin* y acumulamos todo en *sum*. Por ende, el arreglo no nos haría falta para informar los datos por puerto serial.
Si quisieramos usar el arreglo para calcular los datos de salida, deberiamos recorrerlo con otro ciclo e ir procesando las temperaturas una por una.
