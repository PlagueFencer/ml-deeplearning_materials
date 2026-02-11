# Python & Machine Learning Basics for Predictive Maintenance

## Day 2-3: Python Crash Course

### Why Python?
- Easy to learn
- Powerful ML libraries
- Industry standard for data science

### Setup (30 minutes)

**Option 1: Anaconda (Recommended)**
1. Download: https://www.anaconda.com/download
2. Install (includes Python + all ML libraries)
3. Open "Jupyter Notebook" from Anaconda Navigator

**Option 2: Google Colab (Zero Install)**
1. Go to: https://colab.research.google.com
2. Sign in with Google account
3. Click "New Notebook"
4. Start coding immediately!

### Python Essentials (2 hours)

```python
# Variables
sensor_value = 512
temperature = 25.5
is_normal = True

# Lists
readings = [100, 102, 105, 103, 101]

# Loops
for reading in readings:
    print(reading)

# Functions
def calculate_average(values):
    return sum(values) / len(values)

avg = calculate_average(readings)
print(f"Average: {avg}")

# Conditionals
if avg > 100:
    print("High reading detected!")
```

### Loading Arduino Data (1 hour)

```python
import pandas as pd

# Load CSV file from Arduino
df = pd.read_csv('sensor_data.csv')

# View first 5 rows
print(df.head())

# Basic statistics
print(df.describe())

# Access columns
vibration = df['vibration']
temperature = df['temperature']

# Filter data
high_vibration = df[df['vibration'] > 200]
print(f"High vibration events: {len(high_vibration)}")
```

### Data Visualization (1-2 hours)

```python
import matplotlib.pyplot as plt

# Line plot over time
plt.figure(figsize=(12, 4))
plt.plot(df['timestamp'], df['vibration'])
plt.xlabel('Time (ms)')
plt.ylabel('Vibration')
plt.title('Vibration Over Time')
plt.show()

# Multiple sensors
plt.figure(figsize=(12, 6))
plt.subplot(2, 1, 1)
plt.plot(df['vibration'], label='Vibration')
plt.legend()

plt.subplot(2, 1, 2)
plt.plot(df['temperature'], label='Temperature', color='red')
plt.legend()
plt.show()

# Scatter plot
plt.scatter(df['vibration'], df['temperature'])
plt.xlabel('Vibration')
plt.ylabel('Temperature')
plt.title('Vibration vs Temperature')
plt.show()
```

## Day 3-4: Machine Learning Fundamentals

### What is Machine Learning?

**Traditional Programming:**
- You write rules: "If vibration > 500, alert"
- Problem: Hard to know the right threshold

**Machine Learning:**
- Computer learns patterns from data
- Finds complex relationships automatically
- Adapts as it sees more data

### Types of ML for Predictive Maintenance

#### 1. Anomaly Detection (What we'll use!)
- **Goal:** Find unusual patterns
- **Training:** Only needs "normal" operation data
- **Use case:** Detect when machine behaves abnormally

#### 2. Classification
- **Goal:** Predict failure type
- **Training:** Needs labeled data (normal, bearing failure, belt failure, etc.)
- **Use case:** "This looks like a bearing failure"

#### 3. Regression
- **Goal:** Predict time until failure
- **Training:** Needs data with known failure times
- **Use case:** "Motor will fail in 72 hours"

### Your First ML Model: Anomaly Detection (2-3 hours)

```python
import pandas as pd
from sklearn.ensemble import IsolationForest
import matplotlib.pyplot as plt

# Step 1: Load your Arduino data
df = pd.read_csv('sensor_data.csv')

# Step 2: Prepare features (input to ML model)
# Use only the sensor columns, drop timestamp
features = df[['vibration', 'temperature', 'current']].dropna()

# Step 3: Create and train model
model = IsolationForest(
    contamination=0.05,  # Expect 5% of data to be anomalies
    random_state=42       # For reproducible results
)

# Train on your data (learns what "normal" looks like)
model.fit(features)
print("Model trained!")

# Step 4: Make predictions
# -1 = anomaly, 1 = normal
predictions = model.predict(features)
df['prediction'] = predictions

# Step 5: Analyze results
num_anomalies = (predictions == -1).sum()
print(f"Anomalies detected: {num_anomalies} out of {len(predictions)}")

# Step 6: Visualize
df['color'] = df['prediction'].map({1: 'green', -1: 'red'})

plt.figure(figsize=(12, 6))
plt.scatter(df['vibration'], df['temperature'], 
            c=df['color'], alpha=0.5)
plt.xlabel('Vibration')
plt.ylabel('Temperature')
plt.title('Anomaly Detection: Green=Normal, Red=Anomaly')
plt.show()
```

### Understanding the Results

```python
# View anomalous readings
anomalies = df[df['prediction'] == -1]
print("Anomalous data points:")
print(anomalies[['timestamp', 'vibration', 'temperature', 'current']])

# Statistics
print("\nNormal data statistics:")
print(df[df['prediction'] == 1][['vibration', 'temperature']].describe())

print("\nAnomalous data statistics:")
print(df[df['prediction'] == -1][['vibration', 'temperature']].describe())
```

### Feature Engineering (Advanced - Day 4)

Make your model smarter by creating better features:

```python
# Rolling statistics (captures trends)
window_size = 10

df['vibration_mean'] = df['vibration'].rolling(window=window_size).mean()
df['vibration_std'] = df['vibration'].rolling(window=window_size).std()
df['vibration_max'] = df['vibration'].rolling(window=window_size).max()
df['vibration_min'] = df['vibration'].rolling(window=window_size).min()

# Rate of change
df['vibration_change'] = df['vibration'].diff()

# Use these new features for training
enhanced_features = df[['vibration_mean', 'vibration_std', 
                         'vibration_max', 'temperature']].dropna()

model.fit(enhanced_features)
predictions = model.predict(enhanced_features)
```

### Saving and Loading Your Model

```python
import pickle

# Save trained model
with open('predictive_maintenance_model.pkl', 'wb') as f:
    pickle.dump(model, f)
print("Model saved!")

# Load model later
with open('predictive_maintenance_model.pkl', 'rb') as f:
    loaded_model = pickle.load(f)
print("Model loaded!")

# Use loaded model
new_prediction = loaded_model.predict([[450, 78, 2.5]])  # vibration, temp, current
print(f"Prediction: {'ANOMALY' if new_prediction[0] == -1 else 'NORMAL'}")
```

## Common ML Algorithms for Predictive Maintenance

### 1. Isolation Forest (Easiest - Start Here!)
```python
from sklearn.ensemble import IsolationForest
model = IsolationForest(contamination=0.05)
```
**Pros:** Works with only normal data, fast, easy
**Cons:** Less accurate than complex methods

### 2. One-Class SVM
```python
from sklearn.svm import OneClassSVM
model = OneClassSVM(nu=0.05)
```
**Pros:** Good for high-dimensional data
**Cons:** Slower training

### 3. Random Forest (If you have labeled data)
```python
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier(n_estimators=100)
model.fit(X_train, y_train)  # Needs labels!
```
**Pros:** Very accurate, handles complex patterns
**Cons:** Needs labeled failure data

## Evaluation Metrics

```python
# If you have some labeled data for testing
from sklearn.metrics import classification_report, confusion_matrix

# Assume you labeled some data: 0=normal, 1=anomaly
y_true = [0, 0, 0, 1, 1, 0, 1, 0]  # True labels
y_pred = predictions  # Model predictions

print(confusion_matrix(y_true, y_pred))
print(classification_report(y_true, y_pred))
```

## Practice Exercises

1. **Load your Arduino CSV** and plot the data
2. **Train an Isolation Forest** on your data
3. **Visualize** normal vs. anomalous points
4. **Create rolling averages** and retrain
5. **Save your model** to a file

## Troubleshooting

**Problem:** "Module not found"
```python
!pip install scikit-learn pandas matplotlib
```

**Problem:** "All predictions are normal"
- Increase `contamination` parameter (try 0.1 or 0.15)
- Check if data has actual variation

**Problem:** "Too many anomalies detected"
- Decrease `contamination` (try 0.01 or 0.02)
- Add more normal operation data

## Next Steps

You now have:
- ✅ Arduino collecting data
- ✅ Python analyzing data
- ✅ ML model detecting anomalies

**Next:** Connect them in real-time! → See `04_Integration_Guide.md`

---
**Key Takeaway:** ML finds patterns humans can't see. Isolation Forest is perfect for predictive maintenance with limited data!