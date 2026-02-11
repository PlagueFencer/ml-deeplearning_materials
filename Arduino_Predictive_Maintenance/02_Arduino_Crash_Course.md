# Arduino Crash Course for Predictive Maintenance

## Day 1: Arduino Basics

### What is Arduino?
Arduino is a small computer (microcontroller) that can:
- Read sensors (temperature, vibration, etc.)
- Control outputs (LEDs, motors, etc.)
- Communicate with computers
- Run 24/7 on low power

### Setup (30 minutes)

1. **Download Arduino IDE**
   - Go to: https://www.arduino.cc/en/software
   - Install for your operating system
   - Connect Arduino via USB

2. **First Program: Blink LED**
   ```cpp
   void setup() {
     pinMode(13, OUTPUT);  // Pin 13 is built-in LED
   }
   
   void loop() {
     digitalWrite(13, HIGH);  // Turn LED on
     delay(1000);             // Wait 1 second
     digitalWrite(13, LOW);   // Turn LED off
     delay(1000);             // Wait 1 second
   }
   ```
   
   **Upload this**: Click ✓ (Verify) then → (Upload)

### Reading Sensors (1-2 hours)

#### Analog Sensor Reading
```cpp
void setup() {
  Serial.begin(9600);  // Start serial communication
}

void loop() {
  int sensorValue = analogRead(A0);  // Read from pin A0
  Serial.println(sensorValue);        // Print to computer
  delay(100);                         // Wait 100ms
}
```

**What this does:**
- Reads voltage from pin A0 (0-5V becomes 0-1023)
- Sends value to computer via USB
- Repeats every 100ms

#### Open Serial Monitor
- Tools → Serial Monitor
- Set to "9600 baud"
- See live sensor data!

### Data Logging for ML (2-3 hours)

```cpp
// Multi-sensor data collector for ML
// This is the foundation of your predictive maintenance system

void setup() {
  Serial.begin(9600);
  Serial.println("timestamp,vibration,temperature,current");
}

void loop() {
  // Read multiple sensors
  int vibration = analogRead(A0);
  int temperature = analogRead(A1);
  int current = analogRead(A2);
  
  // Format as CSV (Comma Separated Values)
  Serial.print(millis());        // Timestamp in milliseconds
  Serial.print(",");
  Serial.print(vibration);
  Serial.print(",");
  Serial.print(temperature);
  Serial.print(",");
  Serial.println(current);
  
  delay(100);  // Sample rate: 10 times per second
}
```

### Saving Data to File

**Method 1: Copy-Paste** (Easiest)
1. Open Serial Monitor
2. Let it run for 2-3 minutes
3. Copy all text
4. Paste into a text file called `sensor_data.csv`

**Method 2: Python Script** (Better)
```python
import serial
import time

# Connect to Arduino
ser = serial.Serial('COM3', 9600)  # Change COM3 to your port
time.sleep(2)  # Wait for connection

# Open file for writing
with open('sensor_data.csv', 'w') as f:
    for i in range(1000):  # Collect 1000 samples
        line = ser.readline().decode('utf-8')
        f.write(line)
        print(line.strip())

ser.close()
print("Data collection complete!")
```

## Essential Sensors for Predictive Maintenance

### 1. Vibration Sensor (SW-420 or Piezo)
```cpp
int vibrationPin = A0;
int vibrationValue = analogRead(vibrationPin);
```
**Detects:** Motor imbalance, bearing wear, loose parts

### 2. Temperature Sensor (DHT11)
```cpp
#include <DHT.h>
DHT dht(2, DHT11);

void setup() {
  dht.begin();
}

void loop() {
  float temp = dht.readTemperature();
  Serial.println(temp);
}
```
**Detects:** Overheating, friction, electrical issues

### 3. Current Sensor (ACS712)
```cpp
int currentPin = A0;
float voltage = analogRead(currentPin) * (5.0 / 1023.0);
float current = (voltage - 2.5) / 0.185;  // For 5A version
Serial.println(current);
```
**Detects:** Motor load changes, electrical faults

## Troubleshooting

**Problem:** "Port not found"
- **Solution:** Check USB connection, install drivers

**Problem:** "Sketch too big"
- **Solution:** Remove Serial.print statements or use smaller code

**Problem:** Noisy sensor readings
- **Solution:** Add averaging:
```cpp
int getAverageReading(int pin) {
  long sum = 0;
  for(int i = 0; i < 10; i++) {
    sum += analogRead(pin);
    delay(10);
  }
  return sum / 10;
}
```

## Practice Exercises

1. **Exercise 1:** Make LED blink at different speeds
2. **Exercise 2:** Read a sensor and print values
3. **Exercise 3:** Collect 1000 sensor readings to CSV
4. **Exercise 4:** Use 2-3 sensors simultaneously

## Next Steps
Once you can collect sensor data to CSV → Move to Python & ML!

---
**Key Takeaway:** Arduino reads sensors and sends data. That's 90% of what you need for this project!