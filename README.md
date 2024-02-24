# IOT_Train
#include <SPI.h>
#include <MFRC522.h>
#define SS_PIN 10
#define RST_PIN 9
MFRC522 mfrc522(SS_PIN, RST_PIN);   // Create MFRC522 instance.
void setup()
{
  pinMode(7,1);
  pinMode(6,1);
  Serial.begin(9600);
  SPI.begin();      
  mfrc522.PCD_Init();  
  Serial.println("Waiting...");
  Serial.println();
}
void loop()
{
  if ( ! mfrc522.PICC_IsNewCardPresent())
  {
    return;
  }
  if ( ! mfrc522.PICC_ReadCardSerial())
  {
    return;
  }
  Serial.print("Train 1 identified");
  String content= "";
  byte letter;
  for (byte i = 0; i < mfrc522.uid.size; i++)
  {
     Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
     Serial.print(mfrc522.uid.uidByte[i], HEX);
     content.concat(String(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " "));
     content.concat(String(mfrc522.uid.uidByte[i], HEX));
  }
  Serial.println();
  Serial.print("Train 1 in Lane 1 ");
  content.toUpperCase();
  if (content.substring(1) == "F9 D1 F3 6E")
  {
    Serial.println("Authorized access");
    Serial.println();
    //delay(3000);
    digitalWrite(6,1);
    delay(200);
    digitalWrite(6,0);
    delay(200);
    digitalWrite(6,1);
    delay(200);
    digitalWrite(6,0);
    delay(200);
    digitalWrite(6,1);
    delay(200);
    digitalWrite(6,0);
    delay(1000);
  }
  else
  {
    Serial.println(" Train 2 Access denied");
    Serial.println();
    //delay(3000);
    digitalWrite(7,1);
    delay(200);
    digitalWrite(7,0);
    delay(200);
    digitalWrite(7,1);
    delay(200);
    digitalWrite(7,0);
    delay(200);
    digitalWrite(7,1);
    delay(200);
    digitalWrite(7,0);
    delay(1000);
  } 
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
