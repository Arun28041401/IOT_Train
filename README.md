# IOT_Train
#include <SPI.h>
#include <MFRC522.h>
#define SS_PIN 10
#define RST_PIN 9
MFRC522 rfid(SS_PIN, RST_PIN);
int prevCardID = -1;
unsigned long prevCardTime = 0;
const unsigned long debounceTime = 500; // debounce time in milliseconds
void setup() {
  Serial.begin(9600);
  SPI.begin();
  rfid.PCD_Init();
}
void loop() {
  if (!rfid.PICC_IsNewCardPresent() || !rfid.PICC_ReadCardSerial()) {
    return;
  }
  int cardID = rfid.uid.uidByte[0] * 256 + rfid.uid.uidByte[1];
  if (cardID != prevCardID) {
    prevCardID = cardID;
    prevCardTime = millis();
    Serial.println("Train 2 IN: " + String(cardID));
  } else {
    unsigned long currentTime = millis();
    if (currentTime - prevCardTime > debounceTime) {
      Serial.println("Train 1 still present: " + String(cardID));
    }
  }
  rfid.PICC_HaltA();
  rfid.PCD_StopCrypto1();
}
#include <Wire.h>
#include <LIDARLite.h>
LIDARLite myLidar;
#define LIDAR_ADDRESS 0x62
#define LASER_PIN 2
// Threshold value for Lidar sensor
#define THRESHOLD 55
void setup() {
  Serial.begin(9600);
  myLidar.begin(0, LIDAR_ADDRESS);
  myLidar.configure(0);
  pinMode(LASER_PIN, INPUT);
}
void loop() {
  float distance = myLidar.distance();
  // Lidar sensor detects an object within the threshold distance
  if (distance < THRESHOLD) {
    if (digitalRead(LASER_PIN) == HIGH) {
      //Sensors detect an object, trigger an alarm
      Serial.println("Collision Alert!");
    }
  }
  // Delay for 100 milliseconds
  delay(100);
}
