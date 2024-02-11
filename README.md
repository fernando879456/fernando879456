#include <SoftwareSerial.h>
SoftwareSerial SwSerial(10, 11); // RX, TX

#define BLYNK_PRINT SwSerial
#include <BlynkSimpleStream.h>
#include <BlynkTimer.h>

char auth[] = "lHzm_LJkCUHz2rmrSmrb8KBH35JDz94L";

BlynkTimer timer;

int pinSalida1 = 2; // Asigna el pin de salida para la primera salida
int pinSalida2 = 3; // Asigna el pin de salida para la segunda salida
int pinSalida3 = 4; // Asigna el pin de salida para la tercera salida

void prenderSalida(int pin) {
  digitalWrite(pin, HIGH); // Enciende la salida
}

void apagarSalida(int pin) {
  digitalWrite(pin, LOW); // Apaga la salida
}

// Función para asignar un valor basado en el porcentaje recibido
String asignar_valor_porcentaje(int porcentaje) {
  if (porcentaje >= 0 && porcentaje <= 25) {
    return "uno";
  } else if (porcentaje > 25 && porcentaje <= 50) {
    return "dos";
  } else if (porcentaje > 50 && porcentaje <= 75) {
    return "tres";
  } else if (porcentaje > 75 && porcentaje <= 100) {
    return "cuatro";
  } else {
    return "Valor fuera de rango";
  }
}

void myTimerEvent() {
  // Lee el valor analógico de la entrada A0
  int valor_analogico = analogRead(A0);
  
  // Convierte el valor analógico a un porcentaje (0-100)
  int porcentaje = map(valor_analogico, 0, 1023, 0, 100);

  // Asigna el valor a la variable correspondiente
  String variable = asignar_valor_porcentaje(porcentaje);

  // Envía el valor a la aplicación Blynk
  Blynk.virtualWrite(V5, variable);

  // Control de las salidas
  if (variable == "dos") {
    prenderSalida(pinSalida1);
    apagarSalida(pinSalida2);
    apagarSalida(pinSalida3);
  } else if (variable == "tres") {
    prenderSalida(pinSalida2);
    apagarSalida(pinSalida1);
    apagarSalida(pinSalida3);
  } else if (variable == "cuatro") {
    prenderSalida(pinSalida3);
    apagarSalida(pinSalida1);
    apagarSalida(pinSalida2);
  } else {
    apagarSalida(pinSalida1);
    apagarSalida(pinSalida2);
    apagarSalida(pinSalida3);
  }
}

void setup() {
  pinMode(pinSalida1, OUTPUT);
  pinMode(pinSalida2, OUTPUT);
  pinMode(pinSalida3, OUTPUT);

  SwSerial.begin(115200);
  Serial.begin(9600);
  Blynk.begin(Serial, auth);
  timer.setInterval(1000L, myTimerEvent);
}

void loop() {
  Blynk.run();
  timer.run();
}

