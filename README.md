# naam-mudhalvan2k25
IOT  project 
Smart-Greenhouse-System/
│
├── Arduino/
│   └── SmartGreenhouse.ino        # Your Arduino FreeRTOS code
│
├── Python/
│   └── serial_reader.py           # Python script to read and log serial data
│
├── assets/
│   └── COMPONENT Screenshot.png   # Circuit diagram (Fritzing image)
│
├── README.md                      # Project overview and setup instructions
├── LICENSE                        # (optional) Add an open-source license
└── .gitignore                     # (optional) Ignore build/temp files



       # 🌿 Smart Greenhouse Monitoring System (Arduino + Python)

This project is a smart greenhouse automation system built using **Arduino UNO** and **Python**. It monitors soil moisture, light intensity, and temperature in real-time, and automates lighting and alerts based on sensor readings.

---

## 🛠️ Hardware Components
- Arduino UNO
- DHT11 or Thermistor (for temperature)
- LDR (Light sensor)
- Soil Moisture Sensor
- Potentiometer (threshold adjustment)
- Relay Module (control water pump/fan/lights)
- Buzzer & LED (for alerts)

---

## ⚙️ Features
- Real-time monitoring using FreeRTOS tasks on Arduino
- Light-based automation with relay control
- Soil dryness buzzer alerts
- Python integration for serial monitoring and data logging

---

## 📸 Circuit Diagram
![Circuit Diagram](assets/COMPONENT%20Screenshot.png)
*Wiring layout for sensors and actuators with Arduino UNO.*

---

## 🚀 Getting Started

### 1. Arduino Setup
- Upload `SmartGreenhouse.ino` using Arduino IDE.
- Libraries required:
  - `Arduino_FreeRTOS`
  - `SPI.h`
  - `Wire.h`

### 2. Python Setup
Install dependencies:
```bash
pip install pyserial


coding.......
#include <Arduino_FreeRTOS.h>
#include <SPI.h>
#include <Wire.h>
#define OLED_RESET 4
#define pot_in A0
#define ldr_in A1
#define relay_out 3
#define SPEAKER_PIN 8
#define temp_in A2
const float BETA = 3950; // should match the Beta Coefficient of the thermistor
void setup()
{
  Serial.begin(9600);
  //LEDs' initialization!
  pinMode(pot_in, INPUT);
  pinMode(ldr_in, INPUT);
  pinMode(relay_out, OUTPUT);
  pinMode(SPEAKER_PIN, OUTPUT);
    int moist=analogRead(pot_in);
    int moist_percentage = map (moist, 1024, 0, 0, 25);
    Serial.print("Starting Soil moisture:");
    Serial.print(moist_percentage);
    Serial.print("%");
    Serial.println(F(" [Moisture sensor]"));
    int light=analogRead(ldr_in);
    int light_percentage = map (light, 1024, 0, 0, 100);
    Serial.print("Starting Illumination:");
    Serial.print(light_percentage);
    Serial.print("%");
    Serial.println(F(" [Light sensor]"));
    int temp=analogRead(temp_in);
    float temp_celsius = 1 / (log(1 / (1023. / temp - 1)) / BETA + 1.0 / 298.15) - 273.15;
    //int temp_percentage = map (light, 0, 1024, 0, 100);
    Serial.print("Starting Temperature:");
    Serial.print(temp_celsius);
    Serial.println(F(" [Temperature sensor]"));
    if (light_percentage <= 50) {
    digitalWrite(relay_out, LOW);
     } 
    else if (light_percentage > 50){
    digitalWrite(relay_out, HIGH);}
    else if (moist_percentage <= 10) {
    tone(SPEAKER_PIN, 330);
    delay(150);
    tone(SPEAKER_PIN, 392);
    delay(150);
    tone(SPEAKER_PIN, 659);
    delay(150);
    tone(SPEAKER_PIN, 523);
    delay(150);
    tone(SPEAKER_PIN, 587);
    delay(150);
    tone(SPEAKER_PIN, 784);
    delay(150);
    noTone(SPEAKER_PIN);
     } 
  xTaskCreate(Task_1, "Task no. 1!", 100, NULL, 1, NULL);
  xTaskCreate(Task_2, "Task no.  2!", 100, NULL, 2, NULL);
  xTaskCreate(Task_3, "Task no.  3!", 100, NULL, 3, NULL);
}

static void Task_1(void* pvParameters) {
  while (1) {
    int moist=analogRead(pot_in);
    int moist_percentage = map (moist, 1024, 0, 0, 25);
    Serial.print("Soil moisture:");
    Serial.print(moist_percentage);
    Serial.print("%");
    Serial.println(F(" [Moisture sensor]"));
    if (moist_percentage <= 10) {
    tone(SPEAKER_PIN, 330);
    delay(150);
    tone(SPEAKER_PIN, 392);
    delay(150);
    tone(SPEAKER_PIN, 659);
    delay(150);
    tone(SPEAKER_PIN, 523);
    delay(150);
    tone(SPEAKER_PIN, 587);
    delay(150);
    tone(SPEAKER_PIN, 784);
    delay(150);
    noTone(SPEAKER_PIN);
     } 
    //else if (light_percentage > 50){
    //digitalWrite(relay_out, HIGH);}
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}

//Task 2
static void Task_2(void* pvParameters) {
  while (1) {
    int light=analogRead(ldr_in);
    int light_percentage = map (light, 0, 1024, 0, 100);
    Serial.print("Illumination:");
    Serial.print(light_percentage);
    Serial.print("%");
    Serial.println(F(" [Light sensor]"));
    if (light_percentage <= 50) {
    digitalWrite(relay_out, HIGH);
     } 
    else if (light_percentage > 50){
    digitalWrite(relay_out, LOW);}
    vTaskDelay(1100 / portTICK_PERIOD_MS);
  }
}

static void Task_3(void* pvParameters) {
  while (1) {
    int temp=analogRead(temp_in);
    float temp_celsius = 1 / (log(1 / (1023. / temp - 1)) / BETA + 1.0 / 298.15) - 273.15;
    //int temp_percentage = map (temp, 0, 1024, 0, 100);
    Serial.print("Temperature:");
    Serial.print(temp_celsius);
    //Serial.print("%");
    Serial.println(F(" [Temperature sensor]"));
    vTaskDelay(1100 / portTICK_PERIOD_MS);
  }
}

void loop()
{
}
