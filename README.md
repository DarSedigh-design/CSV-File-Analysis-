import pandas as pd

# Prompt user to enter the name of the CSV file
file_name = input("Enter the name of the CSV file (including .csv extension): ")

# Load the dataset specified by the user
try:
    data = pd.read_csv(file_name)
    print(f"Successfully loaded {file_name}")
except FileNotFoundError:
    print("Error: File not found. Please check the file name and try again.")
    exit()

# Remove rows with missing values
data = data.dropna()

# Group by 'Product_Category' and sum the 'Quantity'
if 'Product_Category' in data.columns and 'Quantity' in data.columns:
    product_quantity = data.groupby('Product_Category')['Quantity'].sum()
    print(product_quantity)
else:
    print("Warning: Required columns 'Product_Category' or 'Quantity' are missing.")

# Aggregate 'Shipping_Cost' and 'Profit' statistics per 'Customer_Segment'
if 'Customer_Segment' in data.columns and 'Shipping_Cost' in data.columns and 'Profit' in data.columns:
    shipping_profit_stats = data.groupby('Customer_Segment').agg({'Shipping_Cost': 'mean', 'Profit': ['max', 'min']})
    print(shipping_profit_stats)
else:
    print("Warning: Required columns for shipping and profit statistics are missing.")

# Function to count discount occurrences within a specific range
def count_discount_range(discounts):
    return ((discounts >= 0.03) & (discounts <= 0.07)).sum()

# Group by 'Ship_Mode' and 'Region' to analyze discount range count
if 'Ship_Mode' in data.columns and 'Region' in data.columns and 'Discount' in data.columns:
    discount_range_count = data.groupby(['Ship_Mode', 'Region']).agg({'Discount': count_discount_range})
    print(discount_range_count)
else:
    print("Warning: Required columns for discount analysis are missing.")

# Calculate Z-score for 'Profit' grouped by 'State'
if 'State' in data.columns and 'Profit' in data.columns:
    data['Profit_Zscore'] = data.groupby('State')['Profit'].transform(lambda x: (x - x.mean()) / x.std())
    print(data[['State', 'Profit', 'Profit_Zscore']])
else:
    print("Warning: Required columns for profit Z-score calculation are missing.")

# Identify customers with negative profit
if 'Profit' in data.columns:
    negative_profit_customers = data[data['Profit'] < 0]
    print(negative_profit_customers)
else:
    print("Warning: 'Profit' column is missing.")

# Convert 'OrderDate' and 'ShipDate' to datetime format and calculate shipping duration
if 'OrderDate' in data.columns and 'ShipDate' in data.columns:
    data['OrderDate'] = pd.to_datetime(data['OrderDate'], errors='coerce')
    data['ShipDate'] = pd.to_datetime(data['ShipDate'], errors='coerce')
    data['Duration'] = (data['ShipDate'] - data['OrderDate']).dt.days
    print(data['Duration'])
else:
    print("Warning: Required date columns are missing.")
