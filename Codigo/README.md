# Código do Arduino/ESP
#include <ESP32Servo.h>

Servo sg90;

const int LDR1_PIN = 34; 
const int LDR2_PIN = 35; 
const int SERVO_PIN = 33; 

int posAtual = 90;          
const int erroDeadband = 5;   
const int passoGraus = 1;    
const float speedDegPerSec = 60.0; 

const unsigned long intervaloPassoMs = (unsigned long)((passoGraus / speedDegPerSec) * 1000.0);

const int ANG_MIN = 0;
const int ANG_MAX = 180;

unsigned long ultimoPassoMs = 0;

void setup() {
  Serial.begin(115200); 
  
  pinMode(LDR1_PIN, INPUT);
  pinMode(LDR2_PIN, INPUT);

  sg90.attach(SERVO_PIN);
  sg90.write(posAtual);   
  Serial.println("Rastreador Solar Ativado! Centralizando...");
  delay(800);      
}

void loop() {
  int R1 = analogRead(LDR1_PIN);
  int R2 = analogRead(LDR2_PIN);

  int diff = R1 - R2; 

  int direcao = 0;
  if (abs(diff) > erroDeadband) {
    direcao = (diff > 0) ? -1 : +1; 
  }

  unsigned long agora = millis();
  if (direcao != 0 && (agora - ultimoPassoMs) >= intervaloPassoMs) {
    posAtual += direcao * passoGraus;
    posAtual = constrain(posAtual, ANG_MIN, ANG_MAX);
    sg90.write(posAtual);
    ultimoPassoMs = agora;

    
    Serial.print("LDR1: "); Serial.print(R1);
    Serial.print(" | LDR2: "); Serial.print(R2);
    Serial.print(" | Diff: "); Serial.print(diff);
    Serial.print(" | Pos: "); Serial.println(posAtual);
  }
}
