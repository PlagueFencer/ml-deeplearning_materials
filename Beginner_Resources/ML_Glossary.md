# Machine Learning Glossary for Beginners

Quick reference guide for ML terminology you'll encounter.

## General ML Terms

**Machine Learning (ML)**
Teaching computers to learn from data without being explicitly programmed.

**Model**
The "brain" that learns patterns from data. After training, it makes predictions.

**Training**
The process of teaching a model by showing it examples from data.

**Prediction**
Using a trained model to make guesses about new, unseen data.

**Feature**
An input variable used by the model. For sensors: vibration, temperature, etc.

**Label**
The "answer" or output you want to predict. Example: "normal" vs "failure"

## Types of Learning

**Supervised Learning**
Learning from labeled data (you tell it the answers).
Example: "This sensor reading = failure"

**Unsupervised Learning**
Finding patterns in data without labels.
Example: "Find unusual patterns in this data"

**Anomaly Detection**
Finding data points that don't fit the normal pattern.
Perfect for predictive maintenance!

## Common Algorithms

**Isolation Forest**
An algorithm for finding anomalies. Works by "isolating" unusual points.
- **Pros:** Fast, works with unlabeled data
- **Best for:** Predictive maintenance, fraud detection

**Random Forest**
An ensemble of decision trees. Great for classification.
- **Pros:** Accurate, handles messy data
- **Best for:** When you have labeled failure data

**SVM (Support Vector Machine)**
Finds the best boundary between different classes.
- **Pros:** Works well with limited data
- **Best for:** Binary classification (normal vs. abnormal)

**Neural Network**
Inspired by brain structure. Can learn very complex patterns.
- **Pros:** Extremely powerful
- **Cons:** Needs lots of data and computing power

## Model Concepts

**Overfitting**
Model memorizes training data but fails on new data.
Like studying only past exam questions but failing on new ones.

**Underfitting**
Model is too simple to capture patterns.
Like using a straight line to model a curve.

**Training Set**
Data used to train the model (usually 70-80% of data).

**Test Set**
Data held back to evaluate model performance (20-30% of data).

**Validation Set**
Additional data used to tune model parameters during development.

## Performance Metrics

**Accuracy**
Percentage of correct predictions.
Formula: (Correct predictions) / (Total predictions)

**Precision**
Of all predicted failures, how many were actual failures?
Answers: "How trustworthy are the alerts?"

**Recall (Sensitivity)**
Of all actual failures, how many did we catch?
Answers: "Are we missing failures?"

**F1 Score**
Balances precision and recall. Good overall metric.

**Confusion Matrix**
Table showing true positives, false positives, true negatives, false negatives.

## Data Terms

**Dataset**
Collection of data used for training or testing.

**CSV (Comma Separated Values)**
Simple file format for storing table data.

**Data Cleaning**
Removing errors, duplicates, and handling missing values.

**Feature Engineering**
Creating new, more useful features from raw data.
Example: Creating "average vibration" from individual readings.

**Normalization**
Scaling features to similar ranges (e.g., 0 to 1).

**Time Series**
Data collected over time (like sensor readings).

## Predictive Maintenance Specific

**RUL (Remaining Useful Life)**
Estimated time until a component fails.

**Prognostics**
Predicting future health state of equipment.

**Diagnostics**
Identifying current problems or faults.

**Predictive Maintenance**
Scheduling maintenance based on predicted failures.

**Condition Monitoring**
Continuously tracking equipment health via sensors.

**Failure Mode**
Specific way something can break (bearing failure, belt wear, etc.).

## Python/Programming Terms

**Library**
Pre-written code you can use. Examples: pandas, scikit-learn.

**DataFrame**
Table-like structure in pandas for holding data.

**Array**
List of numbers. Foundation of numerical computing.

**Function**
Reusable block of code that performs a specific task.

**Variable**
Named container holding a value.

**Loop**
Repeating a block of code multiple times.

## Arduino Terms

**Microcontroller**
Small computer that controls electronic devices.

**Sensor**
Device that measures physical properties (temp, vibration, etc.).

**Analog Signal**
Continuous signal (voltage from 0-5V becomes 0-1023 in Arduino).

**Digital Signal**
On/off signal (HIGH or LOW, 1 or 0).

**Serial Communication**
Sending data one bit at a time over USB or other connection.

**Pin**
Connection point on Arduino for inputs/outputs.

**Baud Rate**
Speed of serial communication (commonly 9600).

## Quick Reference: When to Use What

**Isolation Forest**
- ✅ Only have normal operation data
- ✅ Need fast results
- ✅ Simple implementation

**Random Forest Classifier**
- ✅ Have labeled failure data
- ✅ Multiple failure types
- ✅ Need interpretable results

**LSTM Neural Network**
- ✅ Have lots of data
- ✅ Time-sequence patterns matter
- ✅ Need highest accuracy

**One-Class SVM**
- ✅ Only have normal data
- ✅ High-dimensional features
- ✅ Clear boundary between normal/abnormal

## Common Mistakes to Avoid

❌ **Training and testing on same data** → Always split your data!
❌ **Ignoring class imbalance** → Failures are rare; use appropriate metrics
❌ **Not normalizing features** → Scale your data
❌ **Overfitting to noise** → Keep models simple initially
❌ **Trusting accuracy blindly** → Use precision/recall for imbalanced data

## Helpful Analogies

**Model = Recipe**
Training = Learning to cook by tasting many dishes
Prediction = Cooking a new dish using your recipe

**Features = Symptoms**
Temperature, vibration = symptoms a doctor checks
Model = Doctor's experience diagnosing problems

**Anomaly Detection = Security Guard**
Normal patterns = Regular visitors
Anomalies = Suspicious behavior requiring attention

---

**Pro Tip:** Don't try to memorize all terms. Refer back when you see unfamiliar words!