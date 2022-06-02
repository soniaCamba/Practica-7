# Practica-7

## CODIGO
```
#include "Arduino.h"
#include "FS.h"
#include "HTTPClient.h"
#include "SPIFFS.h"
#include "SD.h"
#include "SPI.h"
#include "AudioGeneratorAAC.h"
#include "AudioOutputI2S.h"
#include "AudioFileSourcePROGMEM.h"
#include "sampleaac.h"

AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;

void setup(){
  Serial.begin(115200);
  in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
  aac = new AudioGeneratorAAC();
  out = new AudioOutputI2S();
  out -> SetGain(0.125);
  out -> SetPinout(26,25,22);
  aac->begin(in, out);
}

void loop(){
  if (aac->isRunning()) {
    aac->loop();
    } else {

      aac -> stop();
      Serial.printf("Sound Generator\n");
      delay(1000);
  }
}
```

## DESCRIBIR LA SALIDA POR EL PUERTO SERIE

Compilamos el programa. Al darle a Monitor nos sale por pantalla "Sound generator". Y en el altavoz se debería escuchar la radio o el audio introducido

## FUNCIONAMIENTO

> Añadimos las librerias necesarias e inicializaremos lo necesario para luego poder escuchar por el altavoz.
```
#include "Arduino.h"
#include "FS.h"
#include "HTTPClient.h"
#include "SPIFFS.h"
#include "SD.h"
#include "SPI.h"
#include "AudioGeneratorAAC.h"
#include "AudioOutputI2S.h"
#include "AudioFileSourcePROGMEM.h"
#include "sampleaac.h"

AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;
```
> En el setup establecemos la velocidad en baudios en 115200 como llevamos haciendo en todas las practicas ,e inicializamos los archivos de encabezado:
> 1. AudioFileSourcePROGMEM: es el que importa el archivo de audio en el formato '.acc'
> 2.  AudioGeneratorAAC(): descodifica el audio
> 3. AudioOutputI2S: establece la ganancia del audio 
> 4. SetGai y SetPinout: son los pines de salida
> 5. begin: es para la entrada y la salida.
```
void setup(){
  Serial.begin(115200);
  in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
  aac = new AudioGeneratorAAC();
  out = new AudioOutputI2S();
  out -> SetGain(0.125);
  out -> SetPinout(26,25,22);
  aac->begin(in, out);
```
> Luego en el loop se realiza la decodificación del audio y si es apto para reproducirse imprime por pantalla "Sound Generator\n"
```
void loop(){
  if (aac->isRunning()) {
    aac->loop();
    } else {

      aac -> stop();
      Serial.printf("Sound Generator\n");
      delay(1000);
  }
}
```
