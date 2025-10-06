# -*- coding: utf-8 -*-
"""NIFTY 50 Stock Data Analysis

This script analyzes the NIFTY 50 stock dataset, including visualization
of candlestick charts and price movement histograms.
"""

import pandas as pd
import mplfinance as mpf
from matplotlib import pyplot as plt

# Load the dataset
nifty_df = pd.read_csv('archive (8).zip')  # Ensure this file is in your project directory

print("Dataset Columns:", nifty_df.columns.tolist())
print("\nFirst 5 rows:")
print(nifty_df.head())

# Data Preprocessing for Candlestick Chart
# Simulate a date range (assuming daily data starting from a specific date)
nifty_df['Date'] = pd.date_range(start='2023-01-01', periods=len(nifty_df), freq='D')

# Set the 'Date' column as the index
nifty_df.set_index('Date', inplace=True)

# Rename 'LTP' to 'Close' for mplfinance compatibility
nifty_df.rename(columns={'LTP': 'Close'}, inplace=True)

# Convert the relevant columns to numeric, coercing errors to NaN (if any)
numeric_columns = ['Open', 'High', 'Low', 'Close']
for col in numeric_columns:
    nifty_df[col] = pd.to_numeric(nifty_df[col], errors='coerce')

# Handle missing data by dropping rows with NaNs in OHLC columns
nifty_df.dropna(subset=numeric_columns, inplace=True)

# Prepare the OHLC data (Open, High, Low, Close)
ohlc_data = nifty_df[['Open', 'High', 'Low', 'Close']]

# Plotting the candlestick chart
print("\nGenerating Candlestick Chart...")
mpf.plot(ohlc_data, 
         type='candle', 
         style='charles', 
         title="NIFTY 50 Candlestick Chart", 
         ylabel="Price (INR)", 
         volume=False,
         savefig='nifty50_candlestick.png')  # Save the chart as an image

# Plotting histograms for various metrics
print("\nGenerating Histograms...")

# Histogram for 'Chng'
plt.figure(figsize=(10, 6))
nifty_df['Chng'].plot(kind='hist', bins=20, title='Price Change Distribution')
plt.gca().spines[['top', 'right']].set_visible(False)
plt.savefig('chng_histogram.png')  # Save the histogram
plt.show()

# Histogram for '% Chng'
plt.figure(figsize=(10, 6))
nifty_df['% Chng'].plot(kind='hist', bins=20, title='Percentage Change Distribution')
plt.gca().spines[['top', 'right']].set_visible(False)
plt.savefig('pct_chng_histogram.png')  # Save the histogram
plt.show()

# Histogram for 'Volume (lacs)'
plt.figure(figsize=(10, 6))
nifty_df['Volume (lacs)'].plot(kind='hist', bins=20, title='Volume Distribution (in Lacs)')
plt.gca().spines[['top', 'right']].set_visible(False)
plt.savefig('volume_histogram.png')  # Save the histogram
plt.show()

print("\nAnalysis complete! Check the generated PNG files for charts.")
