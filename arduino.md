# Core Arduino I/O Cheat Sheet

| Category | Function | Syntax | Typical Use Case |
| --- | --- | --- | --- |
| **Setup** | `pinMode()` | `pinMode(pin, OUTPUT/INPUT_PULLUP);` | Defining a pin's role once at boot. |
| **Digital** | `digitalWrite()` | `digitalWrite(pin, HIGH/LOW);` | Flipping a switch (LED, Relay, Buzzer). |
| **Digital** | `digitalRead()` | `int val = digitalRead(pin);` | Checking a state (Button, Limit Switch). |
| **Analog** | `analogRead()` | `int val = analogRead(pin);` | Reading sensors (0 to 1023). |
| **Analog** | `analogWrite()` | `analogWrite(pin, 0-255);` | Dimming LEDs or Motor speed (PWM). |
| **Debug** | `Serial.print()` | `Serial.println(data);` | Seeing what the "brain" is thinking. |
| **Logic** | `map()` | `map(val, inMin, inMax, outMin, outMax);` | Rescaling sensor data to output data. |
| **Timing** | `millis()` | `unsigned long now = millis();` | Multi-tasking without freezing the CPU. |


# The "Pro" Implementation Template

```cpp
// 1. Define Pins & Constants (Easy to change later)
const int SENSOR_PIN = A0;
const int ACTUATOR_PIN = 9;

void setup() {
  Serial.begin(9600);           // Start Debugging
  pinMode(ACTUATOR_PIN, OUTPUT); // Configure I/O
}

void loop() {
  // 2. Sense
  int rawData = analogRead(SENSOR_PIN);
  
  // 3. Process (The Map function)
  int outputVal = map(rawData, 0, 1023, 0, 255);
  
  // 4. Act
  analogWrite(ACTUATOR_PIN, outputVal);
  
  // 5. Report
  Serial.println(outputVal);
}

```
