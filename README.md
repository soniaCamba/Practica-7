# Practica-7

# Practica 7A

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

# Practica 7B

## CODIGO
```
#include "Audio.h"
#include "SD.h"
#include "FS.h"
// Digital I/O used
#define SD_CS 5
#define SPI_MOSI 23
#define SPI_MISO 19
#define SPI_SCK 18
#define I2S_DOUT 25
#define I2S_BCLK 27
#define I2S_LRC 26
Audio audio;
void setup(){
pinMode(SD_CS, OUTPUT);
digitalWrite(SD_CS, HIGH);
SPI.begin(SPI_SCK, SPI_MISO, SPI_MOSI);
Serial.begin(115200);
SD.begin(SD_CS);
audio.setPinout(I2S_BCLK, I2S_LRC, I2S_DOUT);
audio.setVolume(10); // 0...21
audio.connecttoFS(SD, "Ensoniq-ZR-76-01-Dope-77.wav");
}
void loop(){
audio.loop();
}
// optional
void audio_info(const char *info){
Serial.print("info "); Serial.println(info);
}
void audio_id3data(const char *info){ //id3 metadata
Serial.print("id3data ");Serial.println(info);
}
void audio_eof_mp3(const char *info){ //end of file
Serial.print("eof_mp3 ");Serial.println(info);
}
void audio_showstation(const char *info){
Serial.print("station ");Serial.println(info);
}
void audio_showstreaminfo(const char *info){
Serial.print("streaminfo ");Serial.println(info);
}
void audio_showstreamtitle(const char *info){
Serial.print("streamtitle ");Serial.println(info);
}
void audio_bitrate(const char *info){
Serial.print("bitrate ");Serial.println(info);
}
void audio_commercial(const char *info){ //duration in sec
Serial.print("commercial ");Serial.println(info);
}
void audio_icyurl(const char *info){ //homepage
Serial.print("icyurl ");Serial.println(info);
}
void audio_lasthost(const char *info){ //stream URL played
Serial.print("lasthost ");Serial.println(info);
}
void audio_eof_speech(const char *info){
Serial.print("eof_speech ");Serial.println(info);
}
```

# FUNCIONAMIENTO

> Declaramos librerias, pines y generamos una variable audio
```
#include "Audio.h"
#include "SD.h"
#include "FS.h"
// Digital I/O used
#define SD_CS 5
#define SPI_MOSI 23
#define SPI_MISO 19
#define SPI_SCK 18
#define I2S_DOUT 25
#define I2S_BCLK 27
#define I2S_LRC 26
Audio audio;
```
> En el setup inicializamos el pin, digitalWrite, el bus, el Serial com en otras practicas y el SD.begin que inicializa la libreria
```
void setup(){
pinMode(SD_CS, OUTPUT);
digitalWrite(SD_CS, HIGH);
SPI.begin(SPI_SCK, SPI_MISO, SPI_MOSI);
Serial.begin(115200);
SD.begin(SD_CS);
audio.setPinout(I2S_BCLK, I2S_LRC, I2S_DOUT);
audio.setVolume(10); // 0...21
audio.connecttoFS(SD, "Ensoniq-ZR-76-01-Dope-77.wav");
}
```
> Y finalmente el el loop reproduciomos el audio
```
void loop(){
audio.loop();
}
```
> En cuanto a los voids del final los añadiremos opcionalmente en el loop si queremos mostrar la información que nos dan
```
void audio_info(const char *info){
Serial.print("info "); Serial.println(info);
}
void audio_id3data(const char *info){ //id3 metadata
Serial.print("id3data ");Serial.println(info);
}
void audio_eof_mp3(const char *info){ //end of file
Serial.print("eof_mp3 ");Serial.println(info);
}
void audio_showstation(const char *info){
Serial.print("station ");Serial.println(info);
}
void audio_showstreaminfo(const char *info){
Serial.print("streaminfo ");Serial.println(info);
}
void audio_showstreamtitle(const char *info){
Serial.print("streamtitle ");Serial.println(info);
}
void audio_bitrate(const char *info){
Serial.print("bitrate ");Serial.println(info);
}
void audio_commercial(const char *info){ //duration in sec
Serial.print("commercial ");Serial.println(info);
}
void audio_icyurl(const char *info){ //homepage
Serial.print("icyurl ");Serial.println(info);
}
void audio_lasthost(const char *info){ //stream URL played
Serial.print("lasthost ");Serial.println(info);
}
void audio_eof_speech(const char *info){
Serial.print("eof_speech ");Serial.println(info);
}
```
