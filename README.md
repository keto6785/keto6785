#include <BluetoothSerial.h>
#include <SD.h>
#include <NeoSWSerial.h>

const int pinSensor = 2;
const int pinSD = 5;
const int RX = 10;
const int TX = 11;

NeoSWSerial serialBT(RX, TX);
BluetoothSerial SerialBT(Serial, true);

bool reproduciendo = false;

void setup() {
  Serial.begin(9600);
  serialBT.begin(9600);
  SerialBT.begin("BT-SPEAKER");
  SD.begin(pinSD);
  pinMode(pinSensor, INPUT);
}

void loop() {
  int valorSensor = digitalRead(pinSensor);

  if (valorSensor == HIGH && !reproduciendo) {
    reproduciendo = true;
    reproducirAudio();
  }

  delay(50);
}

void reproducirAudio() {
  File archivo = SD.open("/Audio/AUD-20241104-WA0026.wav");
  if (archivo) {
    serialBT.println("AT+PLAY");
    while (archivo.available()) {
      serialBT.write(archivo.read());
    }
    archivo.close();
    reproduciendo = false;
    serialBT.println("AT+STOP");
  }
}
