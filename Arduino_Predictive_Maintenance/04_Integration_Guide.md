# Real-Time Integration: Arduino → Python → ML

## Day 4-5: Connecting Everything

### Architecture Overview

```
[Arduino + Sensors] 
       ↓ USB/Serial
[Python Script] 
       ↓ 
[ML Model]
       ↓
[Alert/Dashboard]
```

### Real-Time Prediction Script

```python
import serial
import pickle
import numpy as np
from collections import deque
import time

# Load your trained model
with open('predictive_maintenance_model.pkl', 'rb') as f:
    model = pickle.load(f)

# Connect to Arduino
SERIAL_PORT = 'COM3'  # Change to your port (COM3, /dev/ttyUSB0, etc.)
BAUD_RATE = 9600

ser = serial.Serial(SERIAL_PORT, BAUD_RATE)
time.sleep(2)  # Wait for connection to establish

print("🔌 Connected to Arduino")
print("📊 Starting real-time anomaly detection...\n")

# Buffer for rolling averages
window_size = 10
vibration_buffer = deque(maxlen=window_size)
temp_buffer = deque(maxlen=window_size)

def calculate_features(vibration, temperature, current):
    """Calculate features from sensor readings"""
    vibration_buffer.append(vibration)
    temp_buffer.append(temperature)
    
    if len(vibration_buffer) < window_size:
        return None  # Not enough data yet
    
    features = [
        np.mean(vibration_buffer),  # Average vibration
        np.std(vibration_buffer),   # Vibration variation
        np.max(vibration_buffer),   # Peak vibration
        np.mean(temp_buffer),       # Average temperature
        current                      # Current reading
    ]
    return features

try:
    while True:
        # Read line from Arduino
        line = ser.readline().decode('utf-8').strip()
        
        # Skip header line
        if line.startswith('timestamp'):
            continue
        
        # Parse CSV line
        try:
            timestamp, vibration, temperature, current = line.split(',')
            vibration = float(vibration)
            temperature = float(temperature)
            current = float(current)
        except ValueError:
            continue  # Skip malformed lines
        
        # Calculate features
        features = calculate_features(vibration, temperature, current)
        
        if features is None:
            print(f"⏳ Collecting data... ({len(vibration_buffer)}/{window_size})")
            continue
        
        # Make prediction
        prediction = model.predict([features])[0]
        
        # Display result
        if prediction == -1:
            print(f"⚠️  ANOMALY DETECTED! | Vib: {vibration:6.1f} | Temp: {temperature:5.1f}°C | Current: {current:4.2f}A")
        else:
            print(f"✅ Normal operation  | Vib: {vibration:6.1f} | Temp: {temperature:5.1f}°C | Current: {current:4.2f}A")
        
        time.sleep(0.1)  # Small delay

except KeyboardInterrupt:
    print("\n🛑 Stopping monitoring...")
    ser.close()
    print("Disconnected.")
```

### Finding Your Arduino Port

**Windows:**
```python
import serial.tools.list_ports
ports = serial.tools.list_ports.comports()
for port in ports:
    print(port.device)
```

**Mac/Linux:**
```bash
ls /dev/tty.*  # Mac
ls /dev/ttyUSB*  # Linux
```

### Adding Alert System

```python
import serial
import pickle
import time
from datetime import datetime

# ... (previous code for loading model and connecting) ...

# Alert configuration
ALERT_THRESHOLD = 3  # Number of consecutive anomalies before alert
anomaly_counter = 0

# Log file
log_file = open('maintenance_alerts.log', 'a')

def send_alert(severity, message):
    """Send maintenance alert"""
    timestamp = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
    alert_msg = f"[{timestamp}] {{severity}}: {{message}}"
    
    print(f"\n🚨 {{alert_msg}}\n")
    log_file.write(alert_msg + '\n')
    log_file.flush()
    
    # Could also:
    # - Send email
    # - Send SMS
    # - Trigger alarm sound
    # - Update database

t try:
    while True:
        # ... (read and parse sensor data) ...
        
        prediction = model.predict([features])[0]
        
        if prediction == -1:
            anomaly_counter += 1
            print(f"⚠️  ANOMALY {{anomaly_counter}}/{{ALERT_THRESHOLD}} | Vib: {{vibration:6.1f}} | Temp: {{temperature:5.1f}}°C")
            
            if anomaly_counter >= ALERT_THRESHOLD:
                send_alert("HIGH", f"Persistent anomaly detected! Vib={{vibration:.1f}}, Temp={{temperature:.1f}}°C")
                anomaly_counter = 0  # Reset after alert
        else:
            anomaly_counter = 0  # Reset on normal reading
            print(f"✅ Normal operation  | Vib: {{vibration:6.1f}} | Temp: {{temperature:5.1f}}°C")

except KeyboardInterrupt:
    log_file.close()
    ser.close()
```

## Day 6: Creating a Demo

### Demo Checklist

✅ **Arduino setup:**
- Sensors connected and working
- Collecting clean data
- Serial output formatted correctly

✅ **Python script:**
- Loads trained model successfully
- Connects to Arduino
- Shows predictions in real-time

✅ **Demonstration:**
- Show normal operation (green/OK)
- Trigger anomaly (shake sensor, heat it up, etc.)
- Show alert system responding

### Demo Tips

1. **Have backup data:** If live demo fails, use pre-recorded CSV
2. **Practice transitions:** "Now watch what happens when I simulate a failure..."
3. **Explain simply:** "Green means normal, red means maintenance needed"
4. **Show the value:** "This prevents unexpected downtime"

### Troubleshooting Live Demo

**Problem:** No serial data
```python
# Add debugging
print(f"Waiting for data on {{SERIAL_PORT}}...")
ser.flush()
time.sleep(2)
```

**Problem:** Model predicts all normal
- Increase sensor variation (shake it!)
- Lower model threshold: `contamination=0.15`

**Problem:** Too many false alarms
- Use consecutive anomaly threshold (3-5 in a row)
- Add smoothing to sensor readings

## Advanced Features (If Time Permits)

### 1. Web Dashboard with Streamlit

```python
import streamlit as st
import pandas as pd
import time

st.title("🔧 Predictive Maintenance Dashboard")

# Create placeholders
status_placeholder = st.empty()
chart_placeholder = st.empty()

data_buffer = []

while True:
    # Read sensor data
    # ... (your serial reading code) ...
    
    # Update status
    if prediction == -1:
        status_placeholder.error("⚠️ ANOMALY DETECTED - Maintenance Required!")
    else:
        status_placeholder.success("✅ System Operating Normally")
    
    # Update chart
    data_buffer.append({'Vibration': vibration, 'Temperature': temperature})
    df = pd.DataFrame(data_buffer[-100:])
    chart_placeholder.line_chart(df)
    
time.sleep(0.1)
```

Run with: `streamlit run dashboard.py`

### 2. Data Logging to CSV

```python
import csv
from datetime import datetime

with open('sensor_log.csv', 'a', newline='') as csvfile:
    writer = csv.writer(csvfile)
    
    while True:
        # ... read sensors and predict ...
        
        # Log everything
        writer.writerow([
            datetime.now().isoformat(),
            vibration,
            temperature,
            current,
            prediction,
            'ANOMALY' if prediction == -1 else 'NORMAL'
        ])
        csvfile.flush()  # Save immediately
```

### 3. Email Alerts

```python
import smtplib
from email.mime.text import MIMEText

def send_email_alert(subject, body):
    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = 'your_email@gmail.com'
    msg['To'] = 'maintenance_team@company.com'
    
    with smtplib.SMTP('smtp.gmail.com', 587) as server:
        server.starttls()
        server.login('your_email@gmail.com', 'your_password')
        server.send_message(msg)

# Use in your monitoring loop
if anomaly_counter >= ALERT_THRESHOLD:
    send_email_alert(
        "Maintenance Alert - Wrapping Machine #3",
        f"Anomaly detected at {{datetime.now()}}. Vibration: {{vibration}}, Temp: {{temperature}}°C"
    )
```

## Testing Your System

### Test Scenarios

1. **Normal Operation**
   - Sensors at rest
   - Should show: ✅ Normal

2. **Vibration Anomaly**
   - Shake vibration sensor
   - Should show: ⚠️ Anomaly

3. **Temperature Anomaly**
   - Heat temperature sensor (hand warmth)
   - Should show: ⚠️ Anomaly

4. **Recovery**
   - Return to normal
   - Alerts should stop

### Creating Test Data

```python
# Simulate failure for testing
import numpy as np

# Normal operation: generate fake but realistic data
normal_vibration = np.random.normal(100, 10, 1000)
normal_temp = np.random.normal(25, 2, 1000)

# Failure simulation: higher vibration and temperature
failure_vibration = np.random.normal(200, 30, 100)
failure_temp = np.random.normal(45, 5, 100)

# Combine and save
all_vibration = np.concatenate([normal_vibration, failure_vibration])
all_temp = np.concatenate([normal_temp, failure_temp])

df = pd.DataFrame({
    'timestamp': range(len(all_vibration)),
    'vibration': all_vibration,
    'temperature': all_temp,
    'current': 2.5  # Constant for simplicity
})

df.to_csv('test_data.csv', index=False)
```

## Presentation Script

**Slide 1:** Problem
- "Machine downtime costs Octomeca $X per hour"
- "Current maintenance is reactive or scheduled"

**Slide 2:** Solution
- "Predictive maintenance using Arduino + ML"
- "Detect problems before failures occur"

**Slide 3:** How It Works
- Arduino sensors → Real-time data → ML model → Alerts

**Slide 4:** LIVE DEMO
- Show normal operation
- Trigger anomaly
- Show alert system

**Slide 5:** Impact
- Reduce downtime by X%
- Prevent catastrophic failures
- Optimize maintenance scheduling

## Final Checklist

- [ ] Arduino code tested and working
- [ ] Trained ML model saved to file
- [ ] Real-time prediction script working
- [ ] Alert system functional
- [ ] Demo practiced 3+ times
- [ ] Backup plan ready (pre-recorded data)
- [ ] Presentation slides complete
- [ ] Can explain system in 2 minutes

---

## You Did It! 🎉

You've built a complete predictive maintenance system from scratch in one week. This is a real, valuable skill that companies pay for.

**What you learned:**
- Arduino sensor interfacing
- Python data analysis
- Machine learning anomaly detection
- Real-time system integration

**Next steps after the workshop:**
- Add more sensors
- Try different ML algorithms
- Deploy on actual machines
- Learn deep learning (LSTM for sequences)

Good luck with your presentation! 🚀