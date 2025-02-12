# Short-Circuit-Induction-Task
This repository contains the screen rocording of task 1 and the tinkercad file link of task02 (which is upgraded version version of task01)
*LINK OF TINKERCAD FILE* - https://www.tinkercad.com/things/du4uAi9p2Pt-powerful-stantia/editel?returnTo=https%3A%2F%2Fwww.tinkercad.com%2Fdashboard&sharecode=pvf-COBhbJHJ4T1mG9XdDKDJPGG_05KuJ13LWNbV96w

task 01
code-
// C++ code
//

const int redLED = 3;
const int yellowLED = 2;
const int greenLED = 1;
const int buttonPin = 6;

const int segmentPins[] = {7, 8, 9, 10, 11, 12, 13};
const byte segmentMap[] = {
    0b00111111, // 0
    0b00000110, // 1
    0b01011011, // 2
    0b01001111, // 3
    0b01100110, // 4
    0b01101101, // 5
    0b01111101, // 6
    0b00000111, // 7
    0b01111111, // 8
    0b01101111  // 9
};

bool overrideActive = false;
int timer=0;


void setup()
{
   pinMode(redLED, OUTPUT);
   pinMode(yellowLED, OUTPUT);
   pinMode(greenLED, OUTPUT);
   pinMode(buttonPin, INPUT_PULLUP);
  
    for (int i = 0; i < 7; i++) {
        pinMode(segmentPins[i], OUTPUT);
        digitalWrite(segmentPins[i], LOW);
    }
}
  
  

  void loop(){

  if (digitalRead(buttonPin) == LOW) {
        overrideActive = true;
    }
  if (overrideActive) {
        
        digitalWrite(redLED, HIGH);
        digitalWrite(yellowLED, LOW);
        digitalWrite(greenLED, LOW);
        displayNumber(0);
    while (digitalRead(buttonPin) == LOW) {
      
      delay(10); }
    
         
      overrideActive = false;
    }
   digitalWrite(redLED, HIGH);
   digitalWrite(yellowLED, LOW);
   digitalWrite(greenLED, LOW);
   countdown(5);
  
  digitalWrite(redLED, LOW);
  digitalWrite(yellowLED, HIGH);
  digitalWrite(greenLED, LOW);
   countdown(1);   
   
    digitalWrite(redLED, LOW);
    digitalWrite(yellowLED, LOW);
    digitalWrite(greenLED, HIGH);
    countdown(9);
    

    
  
   
}
void displayNumber(int num) {
    if (num < 0 || num > 9) return; 
    byte pattern = segmentMap[num];

    for (int i = 0; i < 7; i++) {
        digitalWrite(segmentPins[i], pattern & (1 << i));
    }
}

void countdown(int seconds) {
    for (int i = seconds; i > 0; i--) {
        displayNumber(i);
        delay(1000);     

        if (digitalRead(buttonPin) == LOW) {
            overrideActive = true;
            break;
        }
    }
}

This Arduino-based project simulates a traffic light system with an integrated 7-segment display countdown timer and a manual override button for emergency stops. The system consists of three LEDs (Red, Yellow, Green), a push button, and a 7-segment display controlled via an Arduino board. The program first initializes the LEDs as outputs and the button as an input with an internal pull-up resistor, while also setting up the 7-segment display pins. In the main loop, the system continuously cycles through a standard traffic light sequence: Red (5 seconds) → Yellow (1 second) → Green (9 seconds), with the 7-segment display counting down for each phase. The displayNumber() function maps numbers (0-9) to binary signals for the 7-segment display, while the countdown() function updates the display in real time and allows for an override check. If the override button is pressed, the red LED is immediately activated, traffic is stopped, and the 7-segment display shows "0", holding this state until the button is released. Once released, the system resumes normal operation. The project ensures a realistic traffic signal simulation while also incorporating manual control for emergencies, making it a practical prototype for smart traffic management systems. Future improvements could include adjustable timing settings, pedestrian signals, and LCD-based information displays for enhanced functionality.

task 02
code-
// C++ code for traffic light with ultrasonic sensor and pedestrian button

const int redLED = 3;
const int yellowLED = 2;
const int greenLED = 1;
const int buttonPin = 6;
const int trigPin = 4;
const int echoPin = 5;

const int segmentPins[] = {7, 8, 9, 10, 11, 12, 13};
const byte segmentMap[] = {
    0b00111111, // 0
    0b00000110, // 1
    0b01011011, // 2
    0b01001111, // 3
    0b01100110, // 4
    0b01101101, // 5
    0b01111101, // 6
    0b00000111, // 7
    0b01111111, // 8
    0b01101111  // 9
};

bool overrideActive = false;
bool vehicleDetected = false;
unsigned long lastVehicleTime = 0;
const int vehicleTimeout = 30000; // 30 seconds
const int distanceThreshold = 40; // Distance in cm

void setup() {
    pinMode(redLED, OUTPUT);
    pinMode(yellowLED, OUTPUT);
    pinMode(greenLED, OUTPUT);
    pinMode(buttonPin, INPUT_PULLUP);
    pinMode(trigPin, OUTPUT);
    pinMode(echoPin, INPUT);
    
    for (int i = 0; i < 7; i++) {
        pinMode(segmentPins[i], OUTPUT);
        digitalWrite(segmentPins[i], LOW);
    }
}

void loop() {
    checkVehiclePresence();

    if (digitalRead(buttonPin) == LOW) {
        overrideActive = true;
    }
    
    if (overrideActive) {
        digitalWrite(redLED, HIGH);
        digitalWrite(yellowLED, LOW);
        digitalWrite(greenLED, LOW);
        displayNumber(0);
        while (digitalRead(buttonPin) == LOW) {
            delay(10);
        }
        overrideActive = false;
    }
    
    if (vehicleDetected || millis() - lastVehicleTime < vehicleTimeout) {
        normalTrafficCycle();
    } else {
        digitalWrite(redLED, HIGH);
        digitalWrite(yellowLED, LOW);
        digitalWrite(greenLED, LOW);
        displayNumber(0);
    }
}

void normalTrafficCycle() {
    digitalWrite(redLED, HIGH);
    digitalWrite(yellowLED, LOW);
    digitalWrite(greenLED, LOW);
    countdown(5);

    digitalWrite(redLED, LOW);
    digitalWrite(yellowLED, HIGH);
    digitalWrite(greenLED, LOW);
    countdown(1);

    digitalWrite(redLED, LOW);
    digitalWrite(yellowLED, LOW);
    digitalWrite(greenLED, HIGH);
    countdown(9);
}

void displayNumber(int num) {
    if (num < 0 || num > 9) return;
    byte pattern = segmentMap[num];
    for (int i = 0; i < 7; i++) {
        digitalWrite(segmentPins[i], pattern & (1 << i));
    }
}

void countdown(int seconds) {
    for (int i = seconds; i > 0; i--) {
        displayNumber(i);
        delay(1000);
        checkVehiclePresence();
        if (digitalRead(buttonPin) == LOW) {
            overrideActive = true;
            break;
        }
    }
}

void checkVehiclePresence() {
    digitalWrite(trigPin, LOW);
    delayMicroseconds(2);
    digitalWrite(trigPin, HIGH);
    delayMicroseconds(10);
    digitalWrite(trigPin, LOW);
    
    long duration = pulseIn(echoPin, HIGH);
    int distance = duration * 0.034 / 2;
    
    if (distance > 0 && distance < distanceThreshold) {
        vehicleDetected = true;
        lastVehicleTime = millis();
    } else {
        vehicleDetected = false;
    }
}
This Arduino-based smart traffic light system integrates a 7-segment countdown timer, an ultrasonic sensor for vehicle detection, and a pedestrian override button. The system controls red, yellow, and green LEDs to simulate a real-world traffic signal. A 7-segment display shows the remaining time for each light phase, enhancing visibility.

The ultrasonic sensor (HC-SR04) detects vehicles by measuring the distance between the sensor and an approaching object. If a vehicle is detected within 40 cm, the system activates the normal traffic light cycle (Red → Yellow → Green) and resets a 30-second timeout to maintain smooth traffic flow. If no vehicles are detected for 30 seconds, the system defaults to a red light with a "0" countdown, ensuring safety and energy efficiency.

A pedestrian button allows manual override. When pressed, it immediately activates the red light and stops traffic until the button is released. The override mode takes priority over normal operation, ensuring pedestrian safety.

The countdown function updates the 7-segment display in real time, showing the remaining seconds before light transitions. It also continuously checks for vehicle presence and pedestrian override requests, ensuring adaptive and responsive traffic control.

This project demonstrates a real-world smart traffic system that can adapt to traffic conditions, prioritize pedestrian safety, and optimize traffic flow. Future improvements could include wireless communication for IoT integration, adaptive timing based on real-time traffic conditions, and pedestrian walk signals. 

FLAWS-

The system only checks if a vehicle is present but does not consider traffic density.
A single vehicle triggers the green light, even if traffic is low.

IMPROVEMENTS THAT CAN BE DONE-

To improve the smart traffic light system, several enhancements can be made to increase efficiency, adaptability, and energy savings. Adaptive traffic control can be implemented by measuring vehicle density instead of just presence. By setting multiple distance thresholds, the system can dynamically adjust the green light duration based on the number of detected vehicles, ensuring smoother traffic flow during peak hours. Additionally, replacing the delay() function with millis() allows for non-blocking operations, ensuring that vehicle detection and pedestrian inputs are continuously monitored in real time without unnecessary waiting periods.

Another significant improvement is the pedestrian crossing signal, which adds a "walk/don’t walk" indicator alongside the manual override button, making it clear when pedestrians can cross safely. To reduce power consumption, the system can enter low-power mode if no vehicles are detected for an extended period, using sleep mode to deactivate LEDs and sensors while waiting for an interrupt (such as a button press or detected vehicle) to wake the system.

For remote monitoring and traffic management, integrating wireless communication with an ESP8266/ESP32 module enables real-time traffic updates to be sent to a server, allowing authorities to track traffic congestion and adjust light timings accordingly. Additionally, incorporating a Real-Time Clock (RTC) module would allow the system to adjust signal durations based on the time of day, optimizing traffic flow for rush hours, nighttime, or weekends.

By implementing these upgrades, the traffic light system becomes more responsive, efficient, and intelligent, effectively reducing congestion, prioritizing pedestrian safety, and conserving energy, making it a viable smart city solution

 

