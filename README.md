# Use-SQL-inside-Python-to-pull-simple-sales-info-
This task demonstrates how to use Python and SQL together to extract, analyze, and visualize basic sales data from a small SQLite database. The objective is to pull simple sales metrics—specifically total quantity sold and total revenue per product—and present the results both as text output and a bar chart.# I

code-

import sqlite3
import pandas as pd
import matplotlib.pyplot as plt

# -----------------------
# 1. Create SQLite Database
# -----------------------

# Connect to a new SQLite database (or create if not exists)
conn = sqlite3.connect("sales_data.db")
cursor = conn.cursor()

# Create sales table
cursor.execute("""
CREATE TABLE IF NOT EXISTS sales (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    product TEXT,
    quantity INTEGER,
    price REAL
)
""")

# Insert sample data
sample_data = [
    ("Apples", 30, 2.5),
    ("Bananas", 50, 1.0),
    ("Cherries", 25, 3.0),
    ("Apples", 40, 2.5),
    ("Bananas", 30, 1.0),
    ("Cherries", 20, 3.0)
]

cursor.executemany("INSERT INTO sales (product, quantity, price) VALUES (?, ?, ?)", sample_data)
conn.commit()

# -----------------------
# 2. SQL Query for Summary
# -----------------------

query = """
SELECT 
    product, 
    SUM(quantity) AS total_qty, 
    SUM(quantity * price) AS revenue 
FROM sales 
GROUP BY product
"""

# Load result into pandas DataFrame
df = pd.read_sql_query(query, conn)

# -----------------------
# 3. Display Output
# -----------------------
print("Sales Summary:\n")
print(df)

# -----------------------
# 4. Plot Bar Chart
# -----------------------
df.plot(kind='bar', x='product', y='revenue', legend=False)
plt.title("Total Revenue by Product")
plt.xlabel("Product")
plt.ylabel("Revenue")
plt.tight_layout()
plt.show()

# Close DB connection
conn.close()


output-


Sales Summary:

   product  total_qty  revenue
0   Apples         70    175.0
1  Bananas         80     80.0
2 Cherries         45    135.0
