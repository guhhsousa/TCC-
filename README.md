## Sobre o Projeto

#### Esse projeto visa melhorar a adesão e regimes medicamentosos de pessoas idosas e portadores de Alzheimer. Foi desenvolvido um dispositivo com compartimentos separados para medicamentos que eram controlados para abrir em horários pré-determinados, a configuração desses horários era feita por um sistema de distribuição via aplicativo móvel.
#### O Arduino foi utilizado para executar os controles do dispositivo e se comunicar com o aplicativo. As gavetas abriam por intermédio de micro servos motores após receber um sinal de que um alarme pré-definido havia soado. 
#### Essa programação foi desenvolvida no próprio software do Arduino em linguagem C++. 



## Linguagem de Programação Utilizada
* C++



## Código

```
#include <Wire.h>
#include <RTClib.h>
#include <Servo.h>

RTC_DS3231 rtc;

#define pinServo1 9
#define pinServo2 10 
#define pinServo3 11
#define pinBuzzer 12

Servo servo1;
Servo servo2;
Servo servo3;

short arrayAlarme[12] = {9, 9, 9, 9, 9, 9, 9, 9, 9, 9, 9, 9};
void leituraBluetooth (); 
void compararHorario (int Ngaveta);
void abreGaveta(int Ngaveta);

void setup() {
  Serial.begin(9600);

  if (!rtc.begin()) {
    Serial.println("Couldn't find RTC");
    while (1);
  }

  if (rtc.lostPower()) {
    Serial.println("RTC lost power, let's set the time!");
    rtc.adjust(DateTime(F(__DATE__), F(__TIME__)));
  }

  servo1.attach(pinServo1);  // Pinos PWM
  servo2.attach(pinServo2);
  servo3.attach(pinServo3);

  pinMode (pinBuzzer, OUTPUT);
}

void loop() {
  // Receber mensagens Bluetooth
  leituraBluetooth();
  compararHorario(1);
  compararHorario(2);
  compararHorario(3);
 }
 
void compararHorario (int Ngaveta) {
  DateTime now = rtc.now();
  int horaAtual = now.hour();
  int minutoAtual = now.minute();
  int horaAlarme = arrayAlarme[(Ngaveta -1)*4]*10 + arrayAlarme[(Ngaveta -1)*4 + 1];
  int minutoAlarme = arrayAlarme[(Ngaveta -1)*4 + 2]*10 + arrayAlarme[(Ngaveta -1)*4 + 3];
    if (horaAtual == horaAlarme && minutoAtual == minutoAlarme){
      abreGaveta(Ngaveta);
        
 }
}


void leituraBluetooth () { 
   if (Serial.available() > 0) {
    String codigo = Serial.readStringUntil('#');
    int codigoNumerico[5];
    for(int i=0; i<5 ; i++){
      codigoNumerico[i] = codigo[i] - '0';
    }

    for( int i=0; i<4 ; i++){
      arrayAlarme[(codigoNumerico[0]-1) * 4 + i] = codigoNumerico[i+1];
    }
   }
  }

 void abreGaveta(int Ngaveta) {
    digitalWrite (pinBuzzer, HIGH); 
    switch (Ngaveta){
      case 1:
        servo1.write (90);
        delay(15000);
        servo1.write (0);

      case 2:
        servo2.write (90);
        delay(15000);
        servo2.write (0);

      case 3:
        servo3.write (90);
        delay(15000);
        servo3.write (0);
      
  }
     digitalWrite (pinBuzzer, LOW);   
}

```
