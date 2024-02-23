# IOT_Train

#include <Wire.h>
#include <LIDARLite.h>

// Lidar initialization
LIDARLite myLidar;
#define LIDAR_ADDRESS 0x62

// Laser initialization
#define LASER_PIN 2

// Threshold value for Lidar sensor
#define THRESHOLD 55

void setup() {
  // Initialize serial communication
  Serial.begin(9600);

  // Initialize Lidar sensor
  myLidar.begin(0, LIDAR_ADDRESS);
  myLidar.configure(0);

  // Initialize laser sensor
  pinMode(LASER_PIN, INPUT);
}

void loop() {
  // Read data from Lidar sensor
  float distance = myLidar.distance();

  // Lidar sensor detects an object within the threshold distance
  if (distance < THRESHOLD) {
    // If so, check if laser sensor also detects an object
    if (digitalRead(LASER_PIN) == HIGH) {
      //Sensors detect an object, trigger an alarm
      Serial.println("Collision Alert!");
    }
  }

  // Delay for 100 milliseconds
  delay(100);
}
