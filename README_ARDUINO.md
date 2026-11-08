Temperature Monitor with LED Indicator
An Arduino project that reads temperature data from an analog sensor and provides immediate visual feedback using a bank of indicator LEDs.

Live Hardware Simulation
You can view and interact with the live hardware circuit simulation directly inside your web browser:
![Open in Wokwi](https://img.shields.io/badge/Wokwi-Open_Simulation-blue?style=for-the-badge&logo=data:image/svg%2bxml;base64,PHN2ZyByb2xlPSJpbWciIHZpZXdCb3g9IjAgMCAyNCAyNCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48dGl0bGU+V29rd2k8L3RpdGxlPjxwYXRoIGQ9Ik0xMiAyTDQgNnYxMmw4IDQgOC00VjZ6bTAgMi44M2w0LjY0IDIuMzJMMTIgOS40NyA3LjM2IDcuIMF6TTggMTUuNTRWOS43NWw0IDJ2NS44em04IDBsLTQgMnYtNS44bDQgLTJ6IiBmaWxsPSJ3aGl0ZSIvPjwvc3ZnPg==)
---
Project Objective
This project acts as a safety tracking setup designed to:
Sample raw data values from an analog temperature sensor.
Convert raw 10-bit values into Voltages and Celsius degrees.
Send real-time data over the Serial connection.
Light up a three-tier LED array as temperature changes step up relative to a baseline threshold.
---
Hardware Components Used
Microcontroller: Arduino Uno (or compatible board)
Temperature Sensor: Analog Temperature Sensor (TMP36 style)
Visual Outputs: 3x Indicator LEDs
Resistors: 3x $220\Omega$ resistors for LED protection
Interconnects: Breadboard and jumper wires
---
How It Works
1. Temperature Threshold Logic
The code tracks changes relative to a `baselineTemp` ($20.0^\circ	ext{C}$). As the temperature hits specific offset levels ($+2^\circ	ext{C}$ gaps), incremental LEDs light up:
Temperature Range	Pin 2	Pin 3	Pin 4	Status
$< 22.0^\circ	ext{C}$	LOW	LOW	LOW	Normal / Baseline
$\ge 22.0^\circ	ext{C}$ and $< 24.0^\circ	ext{C}$	HIGH	LOW	LOW	Warning Level 1
$\ge 24.0^\circ	ext{C}$ and $< 26.0^\circ	ext{C}$	HIGH	HIGH	LOW	Warning Level 2
$\ge 26.0^\circ	ext{C}$	HIGH	HIGH	HIGH	Critical Overheating
2. Conversions
Voltage Calculation: Maps the standard 10-bit resolution ($0 - 1023$) to $5	ext{V}$ steps.
$$	ext{Voltage} = rac{	ext{sensorVal}}{1024.0} 	imes 5.0$$
Temperature Calculation: Drops a $0.5	ext{V}$ offset and scales by 100 to extract degrees Celsius.
$$	ext{Temperature} = (	ext{Voltage} - 0.5) 	imes 100$$
---
How to Run or Reproduce
Connect the temperature sensor output pin to Arduino analog input A0.
Connect three indicator LEDs to digital pins 2, 3, and 4 through protection resistors.
Open the file inside your Arduino IDE.
Upload the sketch directly to your connected microcontroller.
Open your Serial Monitor at a baud rate of 9600 to view output data.
---
Code
```cpp
const int sensorPin = A0;
const float baselineTemp  = 20.0;


void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);

  for (int pinNumber = 2; pinNumber < 5; pinNumber++)
  {
    pinMode(pinNumber, OUTPUT);
    digitalWrite(pinNumber, LOW);
  }
}

void loop() {
  // put your main code here, to run repeatedly:
  int sensorVal = analogRead(sensorPin);
  Serial.print("Sensor Value: ");
  Serial.print(sensorVal);

  //Convert sensor value to volts
  float voltage = (sensorVal/1024.0) * 5.0;
  Serial.print(", Volts: ");
  Serial.print(voltage);

  //Convert voltage to temperature in degrees celsius
  float temperature = (voltage - 0.5) * 100;
  Serial.print(", degrees C, ");
  Serial.println(temperature);

  if (temperature < baselineTemp+2)
  {
    digitalWrite(2, LOW);
    digitalWrite(3, LOW);
    digitalWrite(4, LOW);
  }

  else if (temperature >= baselineTemp+2 && temperature < baselineTemp+4)
  {
    digitalWrite(2,HIGH);
    digitalWrite(3, LOW);
    digitalWrite(4, LOW);
  }

  else if (temperature >= baselineTemp+4 && temperature < baselineTemp+6)
  {
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, LOW);
  }

  else if (temperature >= baselineTemp+6)
  {
    digitalWrite(2, HIGH);
    digitalWrite(3, HIGH);
    digitalWrite(4, HIGH);
  }

  delay(1);
}
```
