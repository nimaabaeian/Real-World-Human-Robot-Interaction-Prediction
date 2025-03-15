# **Real-World Human-Robot Interaction Prediction**

This repository contains the code and data preprocessing pipeline for predicting human-robot interaction behaviors using sensor data collected from robotic pets. The goal of this project is to enable robotic pets to autonomously adapt their behaviors based on how users interact with them, enhancing their therapeutic effects.

---

## **Project Overview**

### **Problem Statement**
Robotic pets are increasingly being used for companionship and therapy, especially for individuals living alone. To make these robots more effective, they need to **understand and adapt to human interactions** (e.g., petting, talking, playing). However, predicting these interactions is challenging because:
1. **Multiple behaviors can occur simultaneously** (e.g., a user might pet the robot while talking to it).
2. **Sensor data is noisy and high-frequency** (e.g., motion, temperature, sound are collected 9 times per second).
3. **Interaction data is self-reported** and only available in 15-minute windows.

This project tackles these challenges by building a **machine learning model** that predicts human-robot interaction behaviors using **sensor data**.

---

## **Dataset**

### **Data Sources**
The dataset consists of two main components:
1. **Sensor Data**: Collected from robotic pets, including:
   - **Motion**: Accelerometer data (`accX`, `accY`, `accZ`).
   - **Environment**: Temperature, humidity, air quality (`iaq`), CO2 levels (`co2Equivalent`).
   - **Sound**: Ambient noise levels (`audioLevel`).
   - **Light**: Infrared and ambient light readings (`ir`, `full`).
2. **Interaction Data**: Self-reported by users via an app during 15-minute windows. Users could report multiple behaviors (e.g., "petting" and "talking").

### **Dataset Structure**
- **Two Datasets**: One for participants in the United States (`US.csv`) and one for South Korea (`Korea.csv`). This project uses the US dataset.
- **Time Alignment**: Sensor data is collected 9 times per second, while interaction data is reported every 15 minutes. The sensor data is aggregated to match the 15-minute windows.

For more details on the dataset, visit the [Dryad repository](https://doi.org/10.5061/dryad.tb2rbp078).

---

## **Approach**

### **1. Data Preprocessing**
- **Time Alignment**: Sensor data is aggregated into 15-minute windows to match the interaction reports. For example:
  - **Mean/Median**: For continuous values like temperature.
  - **Sum**: For cumulative motion (`chord`, `arc`).
  - **Max**: For categorical features like sound levels (`sound_cat`).
- **Handling Multiple Behaviors**: Since users can report multiple behaviors in a single window, the target variable (`Modality_cat`) is converted into **binary labels** for each behavior using `MultiLabelBinarizer`.
- **Categorical Encoding**: Columns like `orientation_cat` (robot’s orientation) and `sound_cat` (noise level) are converted to numerical codes using `LabelEncoder`.
- **Missing Values**: Missing sensor data (e.g., temperature readings) are filled with the **mean value** of the column.

### **2. Feature Engineering**
- **Aggregated Metrics**: Sensor data is summarized for each 15-minute window using:
  - **Mean/Median**: For continuous values (e.g., temperature, humidity).
  - **Sum**: For cumulative motion (`chord`, `arc`).
  - **Max**: For categorical features (e.g., loudest sound category in the window).
- **Dropped Columns**: Non-predictive columns like `RowID` (unique identifier) and `Real_time` (timestamp) are removed.

### **3. Model Training**
Three tree-based models are used for **multi-label classification**:
1. **Random Forest**: A robust ensemble method that works well with many features.
2. **XGBoost**: A highly optimized gradient boosting algorithm.
3. **LightGBM**: A fast and memory-efficient gradient boosting framework.

All models are wrapped in `MultiOutputClassifier` to predict multiple behaviors simultaneously.

### **4. Hyperparameter Tuning**
- **RandomizedSearchCV**: Tests combinations of parameters (e.g., tree depth, learning rate) to find the best settings for each model.
- Example parameters tuned:
  - `n_estimators`: Number of trees in the forest.
  - `max_depth`: How deep each tree can grow.
  - `learning_rate`: Step size for optimization.

### **5. Evaluation**
Models are evaluated using:
- **Accuracy**: Percentage of correctly predicted behaviors.
- **F1 Score**: Balances precision (correct predictions) and recall (missed predictions).
- **AUC-ROC**: Measures how well the model distinguishes between classes.
- **Confusion Matrix**: Shows which behaviors are often confused with others.

### **Results**
Model Performance: The best-performing model is LightGBM, achieving ~88% accuracy.

### **Visualizations:**

Bar Charts: Show the frequency of interaction behaviors.

Heatmaps: Display correlations between sensor features.

Confusion Matrices: Highlight which behaviors are often confused.

### **Citation**
If you use this dataset or code, please cite the following paper:

```
Bennett C.C., Sabanovic S., Stanojevic C., Henkel Z., Kim S., Lee J., Baugus K., Piatt J.A., Yu J., Oh J., Collins S., and Bethel C. (2023). 
Enabling robotic pets to autonomously adapt their own behaviors to enhance therapeutic effects: A data-driven approach. 
IEEE International Symposium on Robot and Human Interactive Communication (RO-MAN), pp.1-8.
Contributing
Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.
```
