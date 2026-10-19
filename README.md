The Climate Trend Analyzer: Global Temperature Trend is a Python-based data visualization project designed to analyze and visualize temperature patterns over time. It uses"""
Project Title: Climate Trend Analyzer
Environment: Pydroid 3 / Python 3.x
Description: Generates synthetic climate simulation data, cleans it, 
             analyzes long-term trends, detects anomalies, and visualizes results.
"""

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# -------------------------------------------------------------------------
# STEP 1: VIRTUAL SIMULATION - Generate Realistic Climate Dataset
# -------------------------------------------------------------------------
print("--- STEP 1: Generating Virtual Climate Simulation Data ---")

# Set random seed for reproducibility
np.random.seed(42)

# Generate a monthly date range over 10 years (2014 - 2023)
dates = pd.date_range(start="2014-01-01", end="2023-12-31", freq="ME")
n_samples = len(dates)

# Simulate Base Temperature with a slight upward warming trend + seasonal sine wave
time_idx = np.arange(n_samples)
warming_trend = 0.02 * (time_idx / 12)  # Gradual rise over years
seasonal_cycle = 5 * np.sin(2 * np.pi * time_idx / 12)  # Hot summers, cold winters
base_temp = 20 + warming_trend + seasonal_cycle
temperature = base_temp + np.random.normal(loc=0, scale=0.8, size=n_samples) # Add noise

# Simulate Rainfall (High variance, occasional heavy monsoon/storms)
rainfall = np.maximum(0, 100 + 50 * np.cos(2 * np.pi * time_idx / 12) + np.random.normal(loc=0, scale=40, size=n_samples))

# Simulate CO2 Levels (Steady annual increase: Keeling Curve simulation)
co2_levels = 400 + (time_idx * 0.2) + np.random.normal(loc=0, scale=1.5, size=n_samples)

# Create DataFrame
df = pd.DataFrame({
    'Date': dates,
    'Temperature': temperature,
    'Rainfall': rainfall,
    'CO2': co2_levels
})

# Introduce a few missing values and outliers artificially to demonstrate Data Cleaning
df.loc[15, 'Temperature'] = np.nan  # Missing value
df.loc[42, 'Rainfall'] = 500.0      # Extreme outlier (storm simulation)

print(f"Dataset successfully created with {len(df)} rows.\n")

# -------------------------------------------------------------------------
# STEP 2: Data Preprocessing & Cleaning
# -------------------------------------------------------------------------
print("--- STEP 2: Preprocessing and Cleaning Data ---")

# Check missing values
print("Missing values before cleaning:\n", df.isnull().sum())

# Handle missing values using forward fill or mean imputation
df['Temperature'] = df['Temperature'].fillna(df['Temperature'].mean())

# Handle outliers using the Interquartile Range (IQR) method for Rainfall
Q1 = df['Rainfall'].quantile(0.25)
Q3 = df['Test'] = df['Rainfall'].quantile(0.75) if 'Test' in df else df['Rainfall'].quantile(0.75)
IQR = Q3 - Q1
upper_bound = Q3 + 1.5 * IQR

# Cap extreme outliers instead of dropping to preserve time-series continuity
df['Rainfall'] = np.where(df['Rainfall'] > upper_bound, upper_bound, df['Rainfall'])

# Extract Year and Month for seasonal grouping
df['Year'] = df['Date'].dt.year
df['Month'] = df['Date'].dt.month

print("\nData cleaning completed successfully.\n")

# -------------------------------------------------------------------------
# STEP 3: Trend Analysis & Anomaly Detection
# -------------------------------------------------------------------------
print("--- STEP 3: Analyzing Trends & Detecting Anomalies ---")

# Calculate 12-month Rolling Average to smooth out short-term seasonal noise
df['Temp_Rolling_Avg'] = df['Temperature'].rolling(window=12).mean()

# Anomaly Detection: Temperatures deviating more than 2 Standard Deviations from the mean
temp_mean = df['Temperature'].mean()
temp_std = df['Temperature'].std()
df['Temp_Anomaly'] = np.abs(df['Temperature'] - temp_mean) > (2 * temp_std)

anomaly_count = df['Temp_Anomaly'].sum()
print(f"Detected {anomaly_count} temperature anomalies based on statistical threshold.")

# Display summary statistics
print("\n--- Summary Statistics ---")
print(df[['Temperature', 'Rainfall', 'CO2']].describe())

# -------------------------------------------------------------------------
# STEP 4: Visualization (Optimized for Pydroid 3)
# -------------------------------------------------------------------------
print("\n--- STEP 4: Generating Visualizations ---")
print("Plots will open sequentially. Close each plot window to proceed.")

# Plot 1: Temperature Trend with Rolling Average
plt.figure(figsize=(10, 5))
plt.plot(df['Date'], df['Temperature'], label='Monthly Temp (°C)', color='orange', alpha=0.6)
plt.plot(df['Date'], df['Temp_Rolling_Avg'], label='12-Month Rolling Avg', color='red', linewidth=2)
plt.title('Climate Trend Analyzer: Global Temperature Trend')
plt.xlabel('Year')
plt.ylabel('Temperature (°C)')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()

# Plot 2: Rainfall Pattern
plt.figure(figsize=(10, 4))
plt.plot(df['Date'], df['Rainfall'], label='Rainfall (mm)', color='blue', alpha=0.7)
plt.title('Monthly Rainfall Variation')
plt.xlabel('Year')
plt.ylabel('Rainfall (mm)')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()

# Plot 3: CO2 Concentration Growth
plt.figure(figsize=(10, 4))
plt.plot(df['Date'], df['CO2'], label='CO2 Levels (ppm)', color='green', linewidth=2)
plt.title('Atmospheric CO₂ Concentration Growth')
plt.xlabel('Year')
plt.ylabel('CO₂ (ppm)')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()

print("\nProject execution completed successfully! All trends and anomalies analyzed.") a line graph to display monthly temperature variations and a 12-month rolling average to make long-term trends easier to understand.
