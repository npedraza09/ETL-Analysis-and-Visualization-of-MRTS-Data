# Index

- [Abstract](#Abstract)
- [1. Introduction](#1.-Introduction)
- [2. Extract-Transform-Load](#2.-Extract-Transform-Load)
    - [2.1 The ETL Process](#2.1-The-ETL-Process)
    - [2.2 Data Exploration](#2.2-Data-Exploration)
    - [2.3 Data Preparation](#2.3-Data-Preparation)
    - [2.4 Read the Data Using Python](#2.4-Reading-the-Data-Using-Python)
         - [2.4.1 Reading Sample Data](#2.4.1-Reading-Sample-Data)
         - [2.4.2 Reading the MRST Data](#2.4.2-Reading-the-MRST-Data)
    - [2.5 Writing an Installation Script](#2.5-Writing-an-Installation-Script)
- [3. Analysis and Visualization](#3.-Project-Description)
    - [3.1 Running Queries in MySQL Workbench](#3.1-Running-Queries-in-MySQL-Workbench)
    - [3.2 Running Queries From Python](#3.2-Running-Queries-From-Python)
    - [3.3 Explore Trends](#3.3-Explore-Trends)
    - [3.4 Explore Percentage Change](#3.4-Explore-Percentage-Change)
    - [3.5 Explore Rolling Time Windows](#3.5-Explore-Rolling-Time-Windows)
- [Conclusion](#Conclusion)
- [References](#References)


[Back to top](#Index)

##  Abstract

This project focuses on the analysis of the Monthly Retail Trade Survey (MRTS) data to uncover trends in U.S. retail sales across various business categories. The analysis was conducted through an ETL process, beginning with SQL installation, querying, and extraction of the MRTS data, followed by cleaning and transformation using Python. Key metrics such as total sales, percentage contributions, and rolling averages were computed for categories like food services, sporting goods, and clothing stores. The data visualization revealed key insights, including the rise in spending on sporting goods, the decline of bookstores, and the shrinking gap between men’s and women’s clothing stores in terms of contribution to retail totals. The use of rolling time windows smoothed the data, providing clearer long-term trends. This comprehensive analysis demonstrates how retail sectors have evolved, with notable shifts in consumer behavior over time.


[Back to top](#Index)

## 1. Introduction

In this project, I performed an ETL (Extract, Transform, Load) process and conducted a detailed analysis of the Monthly Retail Trade Survey (MRTS) data. The primary goal was to extract key insights from retail categories, including multiple store categories, and visualize trends over time.

I started by cleaning and formatting my CSV file. I then connected to a MySQL database where the MRTS data was stored. The extraction process involved writing SQL queries to retrieve data for selected retail categories over multiple years. Then, I filtered the data for key categories such as food services, sporting goods, hardware stores, toy stores, men’s and women’s clothing, and bookstores.

After extraction, I transformed the data using Python's pandas library. This step included cleaning the data by handling NULL values, reshaping the data from wide to long format, and calculating important metrics like total sales and percentage contribution of each category. To handle missing values, I used interpolation methods and calculated rolling averages for better trend analysis.

For analysis, I first plotted multiple graphs for trend analysis. I then calculated percentage changes and the contribution of each category to total retail sales. I also explored rolling time windows (4-month and 12-month), which helped smooth out seasonal and short-term fluctuations, providing a clearer view of long-term trends.

Finally, I visualized the data using Matplotlib. Graphs of trends, percentage changes, rolling averages, standard deviations, maximums, and minimums were created for each retail category to illustrate spending trends, volatility, and sales peaks. The visualizations showed key trends, such as the rise in sporting goods sales, the declining share of traditional bookstores, and the narrowing contribution gap between men’s and women’s clothing categories.

This project provided valuable insights into how consumer spending behaviors are changing over time across various retail sectors.


[Back to top](#Index)

## 2. Extract-Transform-Load

[Back to top](#Index)

### 2.1 The ETL Process

#### 1. Extract:  
Identify and research the source of data. Take the raw data from the Excel file.

#### 2. Transform:  
Clean, adjust, and transform the data for better readability and adaptation to an SQL database.

#### 3. Load:  
Insert the clean data to a MySQL database by writing code that allows you to read the data properly and querying over it.


[Back to top](#Index)

### 2.2 Data Exploration

The MRTS Dataset produces the most comprehensive data available on retail economic activity in the United States. These data are widely used throughout government, academic, and business communities. The Bureau of Economic Analysis uses the estimates to calculate Gross Domestic Product. The Bureau of Labor Statistics uses the estimates to develop consumer price indexes and productivity measurements. The Council of Economic Advisers uses the estimates to analyze current economic activity. The Federal Reserve Board uses the estimates to assess recent trends in consumer purchases. The media use the estimates to report news of recent consumer activity. Financial and investment companies use the estimates to measure recent economic trends.

Default fields for each year tab of the original .XLSX file include the NAICS Code, Kind of Business, the month and year each value represents, and a total column for the year. Estimates with a coefficient of variation greater than 30% or with a total quantity response rate less than 50% have been suppressed from publication. These estimates have been replaced with an "S" in the published table. "GAFO" represents stores classified in the following NAICS codes:  442, 443, 448, 451, 452, and 4532. NAICS code 4532 includes office supplies, stationery, and gift stores.) Estimates are adjusted for seasonal variations and holiday and trading-day differences, but not for price changes. Cumulative seasonally adjusted sales estimates are not tabulated.


[Back to top](#Index)

### 2.3 Data Preparation

1. I began by editing the Excel file. I deleted all columns that I found unnecessary and extra info that was not needed in my database. I only took the "Not Adjusted" values, as recommended in one of the previous videos by Dr. Sanchez.
2. I followed this by creating a new .XLSX file called "MRTS_Master", where I joined the data from every year. The columns I kept were: "NAICS Code", "Kind of Business", the months of the year(each month is one column), "Total", and I added a new column called "Year" to represent every year as an index.
3. Once I organized all the data into one file, I transformed this file into a .csv file.


[Back to top](#Index)

### 2.4 Read the Data Using Python

#### Option 1 ####
One way that Python can read a .csv file is by using its CSV package. This package takes each line of the CSV file as a list object. CSV files have a series of delimited values. The most common delimiter is the comma, but in some other scenarios, we can see a semi-colon.

#### Option 2 ####
Another way to read the data from the CSV file is by using the pandas library from Python. This library allows you to read the CSV file and immediately convert it to a data frame.

[Back to top](#Index)

### 2.4.1 Reading Sample Data

I have created a sample dataset called "users". This is a CSV file. This dataset has some normal entries, but other entries have an "(S)", "(NA)", or an empty cell to simulate these scenarios from the MRTS dataset.

In my project, I will be using option 2 to read the data from my sample dataset. 
```python
import pandas as pd # Import Pandas library

sample_df = pd.read_csv('users.csv') # Read CSV file and convert to a dataframe

sample_df # Check the sample dataframe
```


[Back to top](#Index)

### 2.4.2 Reading the MRTS Data

We will open the MRTS CSV file the same way we opened the "users.csv" file.
```python
import pandas as pd # Import Pandas library

MRTS_df= pd.read_csv('MRTS_Master.csv') # Read CSV file and convert to a dataframe

MRTS_df.head() # Check the top five rows of the MRTS dataframe
```


```python
import pandas as pd
import numpy as np
import mysql.connector

# Step 1: Read CSV file
MRTS_df = pd.read_csv('MRTS_Master.csv')

# List of columns that should have commas replaced (month columns + Total)
columns_to_modify = ['January', 'February', 'March', 'April', 'May', 'June',
                     'July', 'August', 'September', 'October', 'November', 'December', 'Total']

# Replace unwanted strings (like (NA), (S), np.nan, and empty strings) with None (to insert as NULL in SQL)
MRTS_df.replace(['(NA)', '(S)', np.nan, ''], None, inplace=True)

# Remove commas from specified numeric columns and convert to proper types
for col in columns_to_modify:
    if col in MRTS_df.columns:
        MRTS_df[col] = MRTS_df[col].str.replace(',', '', regex=False)  # Remove commas from the target columns
        MRTS_df[col] = pd.to_numeric(MRTS_df[col], errors='coerce')  # Convert to numeric, handling errors as NaN

# Replace all NaN values in the DataFrame with None (to match SQL NULLs)
MRTS_df = MRTS_df.where(pd.notnull(MRTS_df), None)

# Step 2: Extract column information from the DataFrame
columns = MRTS_df.columns

# Step 3: Infer data types for SQL, using backticks for column names
column_types = []
for col in columns:
    if pd.api.types.is_integer_dtype(MRTS_df[col]):
        column_types.append(f"`{col}` INT")
    elif pd.api.types.is_float_dtype(MRTS_df[col]):
        column_types.append(f"`{col}` DOUBLE")
    else:
        column_types.append(f"`{col}` VARCHAR(150)")

# Step 4: Connect to the MySQL database
db_connection = mysql.connector.connect(
    host="127.0.0.1",
    user="root",
    password="PASSWORD",
    database="Project 8"
)

cursor = db_connection.cursor()

# Step 5: Create the SQL table
table_name = "MRTS"

# Drop table if exists
drop_table_sql = f"DROP TABLE IF EXISTS `{table_name}`;"
cursor.execute(drop_table_sql)  # Ensure the table is dropped first

# Create table SQL script
create_table_sql = f"CREATE TABLE `{table_name}` (\n"
create_table_sql += "    `id` INT AUTO_INCREMENT PRIMARY KEY,\n"
create_table_sql += "    " + ",\n    ".join(column_types) + "\n"
create_table_sql += ");\n"

# Execute the create table query
cursor.execute(create_table_sql)

# Step 6: Generate and execute INSERT statements using parameterized queries
insert_sql = f"INSERT INTO `{table_name}` ({', '.join([f'`{col}`' for col in columns])}) VALUES ({', '.join(['%s'] * len(columns))});"

for i, row in MRTS_df.iterrows():
    # Convert row values to a tuple for parameterized queries
    values = tuple(None if pd.isna(x) else x for x in row)  # Replace NaN with None to avoid SQL errors
    
    # Execute the insert query with parameterized values
    cursor.execute(insert_sql, values)

# Commit the changes
db_connection.commit()

# Step 7: Close the database connection
cursor.close()
db_connection.close()
```


[Back to top](#Index)

## 3. Analysis and Visualization

We will start this section by comparing MySQL Workbench to a Python environment.

* #### Differences:  
MySQL Workbench is a dedicated graphical interface for interacting with MySQL databases, primarily focused on database design, queries,      and data management tasks. It provides a visual way to manage databases and tables, inspect schemas, and visualize query results in    tables.  On the other hand, A Python environment is primarily a programming environment that allows you to integrate SQL queries into broader data analysis pipelines, manipulate data in memory, and visualize results with libraries like Pandas and Matplotlib. It offers flexibility to not only query but also perform advanced data manipulations, custom transformations, and visualizations after querying data.

* #### Advantages and Disadvantages of MySQL Workbench:  
MySQL Workbench is specifically built for database management tasks, so it's optimized for performing queries and managing schemas efficiently. Results are displayed in a tabular format immediately after running a query, making it easy to view, edit, or export data. The problem with MySQL Workbench is that it is not suited for performing complex data manipulations, aggregations, or analyses beyond the SQL language. For more sophisticated data science or analytics tasks, you’ll need to export the data for further processing.

* #### Advantages and Disadvantages of Python:  
With Python, after retrieving data with an SQL query, you can manipulate it using libraries like Pandas. This gives you access to functions for complex transformations, aggregations, and cleaning that SQL doesn’t easily support. Python also provides advanced visualization libraries like Matplotlib, Seaborn, or Plotly, which allow for creating customized plots and charts directly from the queried data. Additionally, Python scripts can be scheduled to run automatically, making it useful for tasks like automated reporting, ETL (extract-transform-load), or updating databases regularly. Python allows you to integrate SQL queries with other programming logic, apply machine learning models, or combine results from multiple sources (API, JSON, CSV files). Python is cross-platform and widely supported, allowing for easy sharing and deployment across systems. The disadvantages of Python come to setting up and using it with SQL, which requires additional knowledge of libraries like SQLAlchemy or mysql-connector-python. The learning curve can be steeper, especially for users unfamiliar with Python. Also, Python doesn’t natively provide database management features like Workbench. Monitoring database performance, optimizing queries, and managing tables need to be done outside of Python.


[Back to top](#Index)

### 3.1 Running Queries in MySQL Workbench

1. The first query I ran was a simple query to get a description of the table I uploaded into my database.
```sql
DESCRIBE MRTS;
```

2. The second query I ran was to check the bottom of the table, where the first year of surveyed values are; 1992.
```sql
SELECT * FROM MRTS WHERE `Year` = 1992;
```

3. At this point I started to run queries that could perform a function on the table and output data that can be used to start the analysis. I ran a query to get the average of totals from every year grouped by the kind of business.
```sql
SELECT `Kind of Business`, AVG(Total)
FROM MRTS
GROUP BY 1
ORDER BY 2 DESC
```

4. Following the previous query, I queried the total sales per year to look at the increase or decrease per year and tested another SQL function to check everything was in order.
```sql
SELECT 
    Year, 
    SUM(Total) AS Yearly_Total
FROM 
    MRTS
WHERE 
    Total IS NOT NULL
GROUP BY 
    Year
ORDER BY 
    Year DESC;
```

5. The last query I ran in MySQL Workbench, was a query to get the total sales from every month of every year from the "Retail and food services sales, total" business.
```sql
SELECT 
        STR_TO_DATE(CONCAT(Year, '-01', '-01'), '%Y-%m-%d') AS Date, `January` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `January` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-02', '-01'), '%Y-%m-%d') AS Date, `February` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `February` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-03', '-01'), '%Y-%m-%d') AS Date, `March` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `March` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-04', '-01'), '%Y-%m-%d') AS Date, `April` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `April` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-05', '-01'), '%Y-%m-%d') AS Date, `May` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `May` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-06', '-01'), '%Y-%m-%d') AS Date, `June` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `June` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-07', '-01'), '%Y-%m-%d') AS Date, `July` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `July` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-08', '-01'), '%Y-%m-%d') AS Date, `August` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `August` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-09', '-01'), '%Y-%m-%d') AS Date, `September` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `September` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-10', '-01'), '%Y-%m-%d') AS Date, `October` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `October` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-11', '-01'), '%Y-%m-%d') AS Date, `November` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `November` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-12', '-01'), '%Y-%m-%d') AS Date, `December` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `December` IS NOT NULL
    ORDER BY Date;
```

[Back to top](#Index)

### 3.2 Running Queries From Python

1.
```python
import mysql.connector

# Step 1: Connect to the MySQL database
db_connection = mysql.connector.connect(
    host="127.0.0.1",
    user="root",  
    password="PASSWORD",  
    database="Project 8"  
)

# Step 2: Create a cursor object
cursor = db_connection.cursor()

# Step 3: Define the query to describe the table
describe_table_query = "DESCRIBE MRTS;"

# Step 4: Execute the query
cursor.execute(describe_table_query)

# Step 5: Fetch and print the results
table_description = cursor.fetchall()

# Step 6: Display the description of the table in a readable format
print(f"{'Field':<20} {'Type':<30} {'Null':<10} {'Key':<10} {'Default':<20} {'Extra':<10}")
print("="*100)

# Step 7: Loop through the result and handle None values
for row in table_description:
    # Replace None values with an empty string for better formatting
    row = [str(col) if col is not None else '' for col in row]
    print(f"{row[0]:<20} {row[1]:<30} {row[2]:<10} {row[3]:<10} {row[4]:<20} {row[5]:<10}")

# Step 8: Close the cursor and connection
cursor.close()
db_connection.close()
```

2. 
```python
import mysql.connector

# Step 1: Connect to the MySQL database
db_connection = mysql.connector.connect(
    host="127.0.0.1",      
    user="root",          
    password="PASSWORD",  
    database="Project 8"   
)

# Step 2: Create a cursor object
cursor = db_connection.cursor()

# Step 3: Define the query to select all records where Year is 1992
query = "SELECT * FROM MRTS WHERE `Year` = 1992;"

# Step 4: Execute the query
cursor.execute(query)

# Step 5: Fetch all the results
results = cursor.fetchall()

# Step 6: Display the results in a readable format
# Fetch column names
column_names = [desc[0] for desc in cursor.description]

# Print the column names
print(f"{' | '.join(column_names)}")
print("="*100)

# Print each row in the result
for row in results:
    print(f"{' | '.join(str(col) for col in row)}")

# Step 7: Close the cursor and connection
cursor.close()
db_connection.close()
```

3.
```python
import mysql.connector

# Step 1: Connect to the MySQL database
db_connection = mysql.connector.connect(
    host="127.0.0.1",      
    user="root",           
    password="PASSWORD",   
    database="Project 8"   
)

# Step 2: Create a cursor object
cursor = db_connection.cursor()

# Step 3: Define the query to group by `Kind of Business` and calculate the average `Total`
query = """
    SELECT `Kind of Business`, AVG(Total)
    FROM MRTS
    GROUP BY `Kind of Business`
    ORDER BY AVG(Total) DESC;
"""

# Step 4: Execute the query
cursor.execute(query)

# Step 5: Fetch all the results
results = cursor.fetchall()

# Step 6: Display the results in a readable format
# Fetch column names
column_names = [desc[0] for desc in cursor.description]

# Print the column names
print(f"{' | '.join(column_names)}")
print("=" * 80)

# Print each row in the result
for row in results:
    print(f"{row[0]:<50} | {row[1]:.2f}")

# Step 7: Close the cursor and connection
cursor.close()
db_connection.close()

```

4.
```python
import mysql.connector

# Step 1: Connect to the MySQL database
db_connection = mysql.connector.connect(
    host="127.0.0.1",    
    user="root",        
    password="PASSWORD", 
    database="Project 8" 
)

cursor = db_connection.cursor()

# Step 2: Write the SQL query
query = """
    SELECT 
        Year, 
        SUM(Total) AS Yearly_Total
    FROM 
        MRTS
    WHERE 
        Total IS NOT NULL
    GROUP BY 
        Year
    ORDER BY 
        Year DESC;
"""

# Step 3: Execute the query
cursor.execute(query)

# Step 4: Fetch all the results
results = cursor.fetchall()

# Step 5: Print the results
print(f"{'Year':<10} | {'Yearly Total':<15}")
print("-" * 30)

for row in results:
    print(f"{row[0]:<10} | {row[1]:,.2f}")

# Step 6: Close the cursor and the connection
cursor.close()
db_connection.close()

```

5. 
```python
from sqlalchemy import create_engine
import pandas as pd
import matplotlib.pyplot as plt

# Create a connection engine using SQLAlchemy
db_connection_str = 'mysql+mysqlconnector://root:PASSWORD@127.0.0.1/Project 8'
engine = create_engine(db_connection_str)

# SQL query to get the date and sales data for all months
query = """
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-01', '-01'), '%Y-%m-%d') AS Date, `January` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `January` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-02', '-01'), '%Y-%m-%d') AS Date, `February` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `February` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-03', '-01'), '%Y-%m-%d') AS Date, `March` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `March` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-04', '-01'), '%Y-%m-%d') AS Date, `April` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `April` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-05', '-01'), '%Y-%m-%d') AS Date, `May` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `May` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-06', '-01'), '%Y-%m-%d') AS Date, `June` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `June` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-07', '-01'), '%Y-%m-%d') AS Date, `July` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `July` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-08', '-01'), '%Y-%m-%d') AS Date, `August` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `August` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-09', '-01'), '%Y-%m-%d') AS Date, `September` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `September` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-10', '-01'), '%Y-%m-%d') AS Date, `October` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `October` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-11', '-01'), '%Y-%m-%d') AS Date, `November` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `November` IS NOT NULL
    UNION ALL
    SELECT 
        STR_TO_DATE(CONCAT(Year, '-12', '-01'), '%Y-%m-%d') AS Date, `December` AS Sales FROM MRTS WHERE `Kind of Business` = 'Retail and food services sales, total' AND `December` IS NOT NULL
    ORDER BY Date;
"""

# Use pandas to execute the query and read the results into a DataFrame
df = pd.read_sql(query, engine)

# Ensure the Date column is in datetime format (for plotting purposes)
df['Date'] = pd.to_datetime(df['Date'], errors='coerce')

# Convert the Sales column to numeric (just in case there are non-numeric values)
df['Sales'] = pd.to_numeric(df['Sales'], errors='coerce')

# Drop rows with missing Sales data
df = df.dropna(subset=['Sales'])

# Print the rows with Month, Year, and Sales
print("Month-Year and Sales Data:")
for index, row in df.iterrows():
    print(f"{row['Date'].strftime('%Y-%m-%d')}: {row['Sales']} million USD")

# Plot the data
plt.plot(df['Date'], df['Sales'], color='blue', label='Retail and Food Services Sales, Total')
plt.title('Retail and Food Services Sales, Total Over Time')
plt.xlabel('Date')
plt.ylabel('Sales (in millions)')
plt.legend(loc='best')

# Set the xticks to show only the years
years = df['Date'].dt.year.unique()
plt.xticks(pd.to_datetime([f'{year}-01-01' for year in years]), years, rotation=45)

plt.show()

```


[Back to top](#Index)

### 3.3 Explore Trends

An economic trend is a pattern in data that shows how a time series changes in comparison to relatively higher or lower values over a period of time. This can be the direction (upward, downward, or stationary) of a particular economic indicator or the general movement of the economy as a whole.   

Why are economic trends important for predicting quantities like spending patterns?

- Historical Patterns: Economic trends often repeat themselves. By studying past patterns, economists can identify potential future scenarios and make educated predictions.   
- Early Warning Signals: Economic trends can serve as early warning signals of impending economic changes. For example, a rise in unemployment rates might indicate a slowdown in the economy, leading to decreased consumer spending.   
- Informed Decision-Making: Businesses, governments, and individuals can use economic trends to make more informed decisions. For instance, a business might decide to invest in a new product line if it anticipates a growing demand based on economic trends.   
- Policy Formulation: Governments can use economic trends to develop policies that support economic growth and stability. For example, if the government predicts a recession, it might implement measures to stimulate the economy, such as lowering interest rates.

---
In the MRTS data, I decided to explore different trends across multiple kinds of businesses. I started by analyzing the trend of the retail and food services categories; these categories have a huge impact on the total sales per year. 

Query number 5, seen in the [Running Queries Section](#3.1-Running-Queries-in-MySQL-Workbench), is the query I used to get the total sales of each month of each year in the table from the retail and food services categories. In the query I included in this document, you can only see the query for 'Retail and food services sales, total', but I just had to change the name of the category to output the data from other categories. I then wrote a python code and integrated the query that I ran in MySQL Workbench to plot a graph that showed me the trend for each retail and food services category; this script can be seen as number 5 in [Running Queries From Python](#3.2-Running-Queries-From-Python)

To display the data clearly, I had to do some organization and cleanup to have a clear understanding of the trend. First, I grouped each year with their corresponding months. This was done to have an output of each month of each year. I then added the sales per month to each date row. The table had some null values for month sale totals, which would cause the plot to look messy. Therefore, I specified in my code that all values that were not null were not going to be taken into account. Additionally, I read the results from the query into a dataframe for better readability. I added a code line that made the date column I created into a datetime format; this eased the plotting process. At last, I only had the years as the tick marks in the x axis; this made the x axis readable and not crowded by every month of every year.

1. Retail and Food Services Sales, Total
<img width="1332" alt="Screenshot 2024-10-29 at 4 45 55 PM" src="https://github.com/user-attachments/assets/1a33f5ff-abb5-4838-bc00-1974c3a9c81c">

2. Retail sales and food services excl motor vehicle and parts
<img width="1333" alt="Screenshot 2024-10-29 at 4 46 05 PM" src="https://github.com/user-attachments/assets/abd77e63-f3fb-449e-8848-b82b5196c680">

3. Retail sales and food services excl gasoline stations
<img width="1338" alt="Screenshot 2024-10-29 at 4 46 15 PM" src="https://github.com/user-attachments/assets/73edf9e8-6de4-4d4f-8463-a5b25e116799">

4. Retail sales and food services excl motor vehicle and parts and gasoline stations
<img width="1377" alt="Screenshot 2024-10-29 at 4 46 27 PM" src="https://github.com/user-attachments/assets/dcfd68ac-18e3-41e0-a8de-5ba00688f11c">

5. Retail sales, Total
<img width="1305" alt="Screenshot 2024-10-29 at 4 46 36 PM" src="https://github.com/user-attachments/assets/88f24dc1-1233-4b37-9732-4f68d60e30b2">


After plotting every retail category, it is very clear that this category has an upward trend. In part, this is due to the regular inflation that happens as time passes. It is a good indicator of an economy that is constantly growing; of course, to a certain extent. I also noticed that The Great Recession is very noticeable. Between 2008 and 2009, there is a short downtrend that demonstrates the effects of the recession period. Another short downtrend that I spotted in my plots is the negative effects of the COVID-19 pandemic on the economy. At the end of 2019 and the beginning of 2020, there is a steep downtrend. But then, in the end of 2020, there is a strong recuperation from the economy which restores the uptrend of the US. One last thing I want to add is the fact that there is also an uptrend during each year. This may be due to the Christmas season which normally increases sales. But, when the next year starts, it always starts below the last value of the last month of the last year.


---
I wanted to do some further analysis by comparing other businesses such as sporting good stores, hobby and toys stores, and book stores. By analyzing these businesses I can get a better idea of how each business behaves through seasons and economic historical times. To plot the graphs that helped me visualize the trends, I used the same query and code script that was used for the retail and food services analysis.

1. Sporting Good Stores
<img width="1323" alt="Screenshot 2024-10-29 at 4 46 46 PM" src="https://github.com/user-attachments/assets/63a2061f-14d8-4202-a0f7-a3ecd912e318">

This industry has overall shown an uptrend, the same as the retail and food services industry. But, between the years 2016-2020, I noticed a downtrend. After some investigation, I realized that the sporting goods industry had a negative impact because of multiple factors. One factor was the E-commerce growth. Online shopping became increasingly popular, leading to a shift in sales away from traditional brick-and-mortar stores. Another factor was the rise of direct-to-consumer brands which bypassed traditional retailers. The last big factor, was the COVID-19 pandemic which led to widespread lockdowns and restrictions on outdoor activities, causing a sharp decline in demand for sporting goods. As gyms closed, consumers turned to home fitness equipment, leading to a surge in demand for products like treadmills, dumbbells, and yoga mats. The demand of these products at people's homes caused the industry to return to an uptrend, as seen in the plot. Also, once the lockdowns were over, people were eager to go back into sports, fitness, and outdoor activities.


2. Hobby, Toy, and Game Stores
<img width="1346" alt="Screenshot 2024-10-29 at 4 46 55 PM" src="https://github.com/user-attachments/assets/412f7a94-0e79-47fa-9d55-b2cac110b691">

The category of hobby, toy, and game stores has a stationary trend between 1992-2021. There is an all-time peak in 1997, which surprised me. I could also easily notice a seasonal pattern for the Christmas season. Between the months of November to January the sales reach high peaks, an exponential growth from the average sales of the past months, But, it then drops back down to the stationary trend. This pattern was repeated all the way until 2018. There is a downtrend between 2018-2020. After some doing some research, I understood that this downtrend had happened because of multiple factors, as usual.

First, "Toys 'R' Us", a major retailer in the toy industry, went bankrupt on 2017 and closed all their stores in 2018. This caused fewer major players in the toy retail market. It also reduced competition and potentially allowed other retailers to increase prices or decrease product offerings. Additionally, the closure of Toys 'R' Us forced consumers to seek toys from alternative retailers, such as Walmart, Target, and Amazon. This led to a shift in market share among these retailers. Many toy manufacturers relied on Toys 'R' Us as a significant customer. Their bankruptcy led to reduced orders and potential financial difficulties for these suppliers. The closure of this company limited the variety of toys available to consumers, especially in brick-and-mortar stores. This could have discouraged some shoppers from purchasing toys altogether. 

Secondly, another factor that played a big role in the downtrend of these industries  was the economic downturn faced by the entire world. The US economy experienced a recession in 2020 due to the pandemic, leading to decreased consumer spending on discretionary items like toys and games.

Lastly, online retailers like Amazon and Walmart gained significant market share, offering a wider selection of products, competitive prices, and convenient delivery options. This shift led to increased competition for traditional brick-and-mortar stores. Consumers increasingly prefer shopping online, especially for convenience and price comparison. This trend accelerated during the COVID-19 pandemic, as lockdowns and social distancing measures limited in-store shopping.

3. Book stores
<img width="1330" alt="Screenshot 2024-10-29 at 4 47 05 PM" src="https://github.com/user-attachments/assets/ffa84ae1-ca89-4107-aa18-80c501d3da92">

There is a clear upward trend in sales from 1992 until around 2007-2008. This was likely a time of growth in retail book sales, particularly with the rise of large bookstore chains. Sales appear to peak sharply around the holiday season (likely November, December, and January), which is typical in retail sectors due to holiday shopping. Also, there are peaks in August, this indicates that this increase in sales might be due to schools starting their academic years, and having professors and students purchase all necessary books for the year.

Post-2008, there is a noticeable downward trend. This could be attributed to a few factors: The economic downturn after the 2008 financial crisis affected retail sales overall. The rise of e-commerce, with consumers increasingly purchasing books online rather than in physical stores. The growing popularity of e-books during this period, which might have contributed to the decline in physical bookstore sales.

From around 2016 onward, the decline in sales became steeper, likely influenced by continued competition from digital book platforms and a shift in consumer behavior. Additionally, in 2020 we noticed a big downfall in this industry. Like all other industries, bookstores were highly affected by the pandemic. Although the trend headed downwards, the sharp peaks in sales that occur annually remained and highlighted a seasonal effect, where bookstores experience their highest sales during the holiday season.


Overall, the highest trend between the last three plots is the uptrend from the Sporting Goods category. The trend keeps its upward path and it almost reached a monthly sales peak of 7000 million dollars. Additionally, this category has grown the fastest. Although, it does not have the fastest-growing sales in a season. In 1997, the Hobby, Toy, and Game Stores categories recorded an increase in sales of almost 3000 million dollars in just a month. There is an obvious seasonal pattern to notice in the holiday seasons which are the months of December and January. Every industry had the same pattern. Another pattern that was seen across all categories was the impact of the 2020 COVID-19 pandemic. This caused a downtrend in all categories, although some got back up faster than others, such as the Sporting Goods category. At last, the business that did the best monthly and yearly is the Sporting Goods business. It keeps a steady behavior, with some positive peaks in the Holiday seasons, but overall just keeps increasing. The Toy and Book businesses kept a more stationary behavior with some steep peaks that happened for just a month or two but did not keep an uptrend.


[Back to top](#Index)

### 3.4 Explore Percentage Change

In economics, the percentage change is a way to quantify how much a particular economic variable has increased or decreased relative to its initial value over a specific period. It is calculated using the formula:

- Percentage Change =((New Value − Old Value) / Old Value) × 100%

This metric expresses the change as a proportion of the original value, allowing for a standardized comparison regardless of the absolute sizes of the variables involved.

Importance of Percentage Change in Predicting Spending Patterns:

- Standardization Across Variables:
    - Comparability: Percentage changes normalize data, making it easier to compare changes across different sectors, time periods, or regions, even if the absolute values differ significantly.
    - Relative Impact: They show the relative impact of changes, which is crucial when assessing variables like consumer spending, where absolute values might be misleading due to inflation or population growth.
- Analyzing Trends:
    - Consumer Behavior: Understanding the percentage change in spending helps economists identify trends in consumer behavior, such as increased spending during economic booms or decreased spending during recessions.
    - Seasonal Adjustments: It aids in adjusting for seasonal variations, providing a clearer picture of underlying economic conditions.
- Forecasting and Modeling:
    - Predictive Analysis: Percentage changes are essential inputs in economic models that forecast future spending patterns based on   historical data.
    - Elasticities: They are used to calculate elasticities, which measure how sensitive consumers are to changes in prices or income.
- Policy Formulation:
    - Economic Planning: Governments and central banks use percentage changes in spending to make informed decisions about monetary and fiscal policies.
    - Intervention Strategies: Identifying significant percentage changes can signal the need for policy interventions to stabilize the economy.
- Inflation and Real Terms Adjustment:
    - Real vs. Nominal Values: Percentage changes help distinguish between nominal changes (which include inflation) and real changes (which are adjusted for inflation), providing a more accurate assessment of economic health.

---

There are two categories that I was highly interested in analyzing because the only aspect that separates them is gender; the Men's and Women's clothing stores categories. To understand the percentage change and relation of these two categories, I first wrote a query in MySQL Workbench that extracted all sales from these categories per month and year. The query is the following:
```sql
SELECT 
    Year, 
    CONCAT(Year, '-', '01', '-01') as Date,  -- Date for further along to pass on to datetime format to ease the plotting process
    `Kind of Business`, 
    `January`, `February`, `March`, `April`, `May`, `June`, `July`, `August`, `September`, `October`, `November`, `December` 
FROM MRTS 
WHERE `Kind of Business` = "Men's clothing stores" OR `Kind of Business`= "Women's clothing stores"
ORDER BY Year;
```
Once I checked the query outputted the data it was intended to output, I went to Visual Studio Code to write a Python code script that gave me the percentage change of these two categories and then plotted this percentage changes in a graph.
```python
import pandas as pd
import matplotlib.pyplot as plt
from sqlalchemy import create_engine

# Step 1: Connect to the MySQL database and query the data
db_connection = 'mysql+mysqlconnector://root:PASSWORD@127.0.0.1/Project 8'
engine = create_engine(db_connection)

# Step 2: Write the SQL query to extract data for Men's and Women's clothing stores
query = """
SELECT 
    Year, 
    CONCAT(Year, '-', '01', '-01') as Date,  
    `Kind of Business`, 
    `January`, `February`, `March`, `April`, `May`, `June`, `July`, `August`, `September`, `October`, `November`, `December` 
FROM MRTS 
WHERE `Kind of Business` = "Men's clothing stores" OR `Kind of Business`= "Women's clothing stores"
ORDER BY Year;
"""

# Step 3: Read the query result into a pandas DataFrame
df = pd.read_sql(query, engine)

# Step 4: Reshape the DataFrame
# Melt the data so that we can convert month columns into rows
df_melted = df.melt(id_vars=['Year', 'Date', 'Kind of Business'], 
                    value_vars=['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'], 
                    var_name='Month', 
                    value_name='Sales')

# Step 5: Remove any rows with null sales data
df_melted = df_melted.dropna(subset=['Sales'])

# Step 6: Sort the values by Date
df_melted['Date'] = pd.to_datetime(df_melted['Date'])
df_melted = df_melted.sort_values(by=['Date'])

# Step 7: Calculate percentage change for each category per year
df_men = df_melted[df_melted['Kind of Business'] == "Men's clothing stores"].copy()
df_women = df_melted[df_melted['Kind of Business'] == "Women's clothing stores"].copy()

# Group data by year and calculate percentage change for men's and women's clothing stores
df_men_grouped = df_men.groupby('Year')['Sales'].sum().pct_change() * 100  # Convert to percentage
df_women_grouped = df_women.groupby('Year')['Sales'].sum().pct_change() * 100

# Step 8: Plot the percentage change
plt.figure(figsize=(10,6))
plt.plot(df_men_grouped.index, df_men_grouped, label="Men's clothing stores", color='blue', marker='o')
plt.plot(df_women_grouped.index, df_women_grouped, label="Women's clothing stores", color='red', marker='o')

# Adding labels and title
plt.title("Percentage Change in Men's and Women's Clothing Stores Over Time")
plt.xlabel("Year")
plt.ylabel("Percentage Change (%)")
plt.xticks(rotation=45)
plt.legend()

# Show the plot
plt.tight_layout()
plt.show()
```
The result of this code script is the following plot:
<img width="1385" alt="Screenshot 2024-10-29 at 4 47 21 PM" src="https://github.com/user-attachments/assets/d0f62b3b-f61a-4a14-b4a1-caccdad99085">

The results surprised me because I thought these categories would have a constant positive percentage change throughout time. The Men's and Women's clothing store businesses keep a steady line. Their percentage change never really increases or decreases drastically, until 2020 and 2021. During these two years, there is a massive drop in percentage change, mainly affected by the COVID-19 pandemic which severely impacted retail sales across most sectors, particularly clothing. Overall, the Women's clothing businesses kept a higher percentage change, but not too far away from the men. These two categories show an incredible relationship; they both keep a stationary trend and if one drops, the other one as well. This relationship is not really affected through time. During the 2008 financial crisis, the Men's category suffered a slightly larger percentage drop, but not too far away from the women. 

To confirm the credibility of the last plot, I decided to use again the script from point 5 in [Running Queries From Python](#3.2-Running-Queries-From-Python). I just had to change the Kind of Business to Men's and Women's clothing stores. The results were the following:
<img width="1254" alt="Screenshot 2024-10-29 at 4 47 35 PM" src="https://github.com/user-attachments/assets/b190fe05-85bc-41ea-97be-7d8f4d3935a1">

<img width="1288" alt="Screenshot 2024-10-29 at 4 47 47 PM" src="https://github.com/user-attachments/assets/788070e5-32d5-48bb-bb1e-b3043eabb9eb">

These two plots indicate mostly a stationary trend and then a steep drop in 2020, which goes hand by hand with the results form the percentage change graph. Additionally, the holiday seasons cause peaks in the plot again. The percentage change graph does not take them into account as it adjusts for seasonal variations and provides a clearer picture of underlying economic conditions. I also noticed the Women's clothing stores category sales more as the graph indicates that the sales vary from an average of 2500-3500 million dollars in comparison to the Men's category which it only varies on average from 700-900 million dollars. Although one sale more than the other, they still keep a relationship.

I wanted to further analyze what is the percentage contribution of each category to the whole ('Retail and food services sales, total'). Therefore, I had to write a script that allowed me to visualize their contribution. I forst noticed that the Men's clothing stores category had a couple NULL values in 2020, including the total sum for the year. I corrected this in MySQL Workbenhc by writing the query:

```sql
SET SQL_SAFE_UPDATES = 0;
UPDATE MRTS
SET 
    `October` = IFNULL(`October`, (`September` + `December`) / 2),
    `November` = IFNULL(`November`, (`October` + `December`) / 2),
    `Total` = `January` + `February` + `March` + `April` + `May` + `June` + `July` + `August` + `September` + `October` + `November` + `December`
WHERE `Kind of Business` = "Men's clothing stores" AND Year = 2020;
```
After I wrote this query, I had to write the Pythons cript to plot the percentage contribution of each business to the whole. The code I wrote is the following:


```python
import pandas as pd
import matplotlib.pyplot as plt
from sqlalchemy import create_engine

# Step 1: Connect to the MySQL database and query the data
db_connection = 'mysql+mysqlconnector://root:PASSWORD@127.0.0.1/Project 8'
engine = create_engine(db_connection)

# Step 2: Write the SQL query to extract data for Men's and Women's clothing stores and total retail sales
query = """
SELECT 
    Year, 
    `Kind of Business`, 
    `Total`
FROM MRTS 
WHERE `Kind of Business` IN ("Men's clothing stores", "Women's clothing stores", "Retail and food services sales, total")
ORDER BY Year;
"""

# Step 3: Read the query result into a pandas DataFrame
df = pd.read_sql(query, engine)

# Step 4: Reshape the DataFrame for easier analysis
df_pivot = df.pivot(index='Year', columns='Kind of Business', values='Total')

# Step 5: Calculate the percentage contribution for Men's and Women's clothing stores
df_pivot['Men\'s Percentage'] = (df_pivot["Men's clothing stores"] / df_pivot["Retail and food services sales, total"]) * 100
df_pivot['Women\'s Percentage'] = (df_pivot["Women's clothing stores"] / df_pivot["Retail and food services sales, total"]) * 100

# Step 6: Plot the percentage contribution over time
plt.figure(figsize=(10,6))
plt.plot(df_pivot.index, df_pivot['Men\'s Percentage'], label="Men's Clothing Percentage", color='blue', marker='o')
plt.plot(df_pivot.index, df_pivot['Women\'s Percentage'], label="Women's Clothing Percentage", color='red', marker='o')

# Adding labels and title
plt.title("Percentage Contribution of Men's and Women's Clothing Stores to Total Retail Sales")
plt.xlabel("Year")
plt.ylabel("Percentage Contribution (%)")
plt.xticks(rotation=45)
plt.legend()

# Show the plot
plt.tight_layout()
plt.show()
```
The results from the script written above is the graph:
<img width="1372" alt="Screenshot 2024-10-29 at 4 47 58 PM" src="https://github.com/user-attachments/assets/c3a5e93f-aee5-4f25-806f-6b0641be97d8">

The Women's clothing stores had a higher contribution to the whole, as expected. The Men's category always contributed less, but as time passed, the gap between the contribution from one another decreased. Both categories gradually decrease their percentage contribtuion to the whole as years pass by. In the past twenty years, both categories contribute under 1% of the total of retail and food services sales.


[Back to top](#Index)

### 3.5 Explore Rolling Time Windows

- In economics, what is the rolling time window and why is it considered an important measure to predict quantities like spending patterns?

A rolling time window (also known as a moving or sliding window) is a statistical technique used to analyze data over a specified period that moves forward in time. In economics, it involves calculating metrics or performing analyses on a subset of data within a fixed time frame that "rolls" forward, incorporating new data points while discarding the oldest ones. This method allows economists and analysts to observe how certain variables evolve over time in a dynamic fashion.

How It Works:

- Fixed Window Size: Determine a specific period (e.g., days, months, quarters) for the window. For instance, a 12-month rolling window would always consider data from the most recent 12 months.

- Moving Forward: As new data becomes available, the window moves forward by one unit of time, updating the analysis to include the newest data point and exclude the oldest.

- Continuous Analysis: This process creates a series of overlapping subsets of the data, enabling continuous monitoring and analysis.

Importance in Predicting Spending Patterns:

1. Capturing Recent Trends:

    - Relevance of Data: Recent data often provides more relevant information for predicting future behavior. A rolling time window emphasizes the most recent observations, which may better reflect current economic conditions affecting spending patterns.

    - Dynamic Adjustment: It allows models to adjust dynamically to changes in the market or consumer behavior, improving the accuracy of predictions.

2. Smoothing Out Volatility:

    - Reducing Noise: Short-term fluctuations or anomalies can obscure underlying trends. Rolling averages help smooth out this volatility, making it easier to identify genuine patterns in spending behavior.

    - Trend Identification: By averaging over a rolling window, analysts can detect long-term trends and cyclical patterns that are crucial for forecasting.

3. Detecting Structural Changes:

    - Adaptation to Shifts: Economies are subject to structural changes due to technological advancements, policy shifts, or unexpected events. Rolling windows can help detect these changes by showing how relationships between variables evolve over time.

    - Model Updating: This detection allows economists to update their models to account for new dynamics, enhancing predictive capabilities.

4. Improving Forecasting Models:

    - Parameter Stability: Economic models often rely on parameters estimated from historical data. Rolling windows ensure these parameters are regularly updated, maintaining their relevance and stability over time.

    - Outlier Management: By continuously updating the dataset, rolling windows can mitigate the impact of outliers or sudden shocks, preventing them from unduly influencing long-term predictions.

5. Seasonal and Cyclical Analysis:

    - Seasonality Adjustment: Consumer spending often exhibits seasonal patterns. Rolling windows can help isolate and analyze these effects by comparing like periods over successive years.

    - Cyclical Trends: They enable the study of economic cycles (e.g., boom and bust periods) by highlighting recurring patterns in spending behavior.

To see an example of how rolling windows smooth out monthly or yearly volatility and highlight underlying trends, I will plot a graph with rolling time windows of 12 months  and calculate the 12-month rolling average on the Sporting goods stores category. Previously, I had plotted the regular sales by year graph. Now, I used the following script to produce the plot with rolling time windows:
```python
import pandas as pd
import matplotlib.pyplot as plt
from sqlalchemy import create_engine

# Step 1: Connect to the MySQL database and query the data
db_connection = 'mysql+mysqlconnector://root:PASSWORD@127.0.0.1/Project 8'
engine = create_engine(db_connection)

# Step 2: Write the SQL query to extract data for 'Food services and drinking places'
query = """
SELECT 
    Year, 
    CONCAT(Year, '-', '01', '-01') as Date,  
    `Kind of Business`, 
    `January`, `February`, `March`, `April`, `May`, `June`, `July`, `August`, `September`, `October`, `November`, `December` 
FROM MRTS 
WHERE `Kind of Business` = "Sporting goods stores"
ORDER BY Year;
"""

# Step 3: Read the query result into a pandas DataFrame
df = pd.read_sql(query, engine)

# Step 4: Reshape the DataFrame
# Melt the data so that we can convert month columns into rows
df_melted = df.melt(id_vars=['Year', 'Date', 'Kind of Business'], 
                    value_vars=['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'], 
                    var_name='Month', 
                    value_name='Sales')

# Step 5: Remove any rows with null sales data
df_melted = df_melted.dropna(subset=['Sales'])

# Step 6: Sort the values by Date
df_melted['Date'] = pd.to_datetime(df_melted['Date'])
df_melted = df_melted.sort_values(by=['Date'])

# Step 7: Calculate rolling averages for 4-month windows
df_melted['Rolling_Sales'] = df_melted['Sales'].rolling(window=12).mean() # Change .mean() for .std(), .max(), or .min() to analyze different components

# Step 8: Plot the 4-month rolling sales over time
plt.figure(figsize=(10,6))
plt.plot(df_melted['Date'], df_melted['Rolling_Sales'], label='12-Month Rolling Sales', color='blue', marker='o')

# Adding labels and title
plt.title("12-Month Rolling Average Sales for Sporting goods stores")
plt.xlabel("Date")
plt.ylabel("Rolling Sales (in millions)")
plt.xticks(rotation=45)
plt.legend()

# Show the plot
plt.tight_layout()
plt.show()
```
This code produced the following graph:

<img width="1374" alt="Screenshot 2024-10-29 at 4 48 14 PM" src="https://github.com/user-attachments/assets/f99ae1e6-6b5b-4fae-9304-ece06f2224c6">

This plot indicates a clearer visualization of the trend as it does get greatly affected by the seasonal peaks because of the 12-month rolling window that is being used.

---

The 'Food Services and Drinking Places' category is a vital component of the retail economy, reflecting consumer spending habits in dining and entertainment. Analyzing this category using a 4-month rolling time window helps smooth out short-term fluctuations and highlights underlying trends, providing clearer insights into spending patterns.

To analyze this category I first plotted the original graph of sales per year. This will allow me to differentiate the seasonal peaks affecting objective visualization of the trend, and the difference with the smooth-out process that the rolling windows cause to determine underlying trends. I used the same script from point 5 in [Running Queries From Python](#3.2-Running-Queries-From-Python) and replaced the kind of business for 'Food Services and Drinking Places', the result is the following:
<img width="1338" alt="Screenshot 2024-10-29 at 4 48 26 PM" src="https://github.com/user-attachments/assets/fada5c1d-b3d6-4555-8313-99bf1fdfa40f">


The graph for "Food services and drinking places" shows a steady increase in sales from the early 1990s to around 2019. This trend is characteristic of economic growth and expansion in the food and beverage industry, reflecting greater consumer spending on dining out as household incomes increased, and as the economy grew during this period.

However, a sharp and significant drop is observed in 2020, likely due to the global COVID-19 pandemic. The restrictions on dining, closures of restaurants and bars, and shifts in consumer behavior led to a massive decline in sales during this time. The industry faced a sharp contraction as social distancing measures and lockdowns were imposed, and this is reflected in the dip seen on the graph.

After 2020, there is a visible recovery in sales, though still below pre-pandemic levels. This suggests that while the industry started to bounce back, challenges such as changes in dining habits, economic uncertainty, and ongoing restrictions may have continued to suppress full recovery into 2021.

Now I will further analyze this kind of business by setting 4 and 12-month rolling windows, and then determine what are the rolling average values, the standard deviations, and the maximum and minimum values per year.

1. 4-month rolling averages:
<img width="1347" alt="Screenshot 2024-10-29 at 4 48 36 PM" src="https://github.com/user-attachments/assets/9bddbf2a-a59f-4fa5-b2b0-5bf39647ee6f">

This graph shows the short-term trends in sales data with more granular detail. While the overall trajectory remains upward, there are visible fluctuations that represent short-term changes in the industry. The graph shows noticeable dips, especially during economic downturns (like the one in 2020), and spikes around recovery periods. The 4-month window captures more of these variations, providing insights into seasonal or temporary shifts in sales patterns.

2. 12-month rolling averages:
<img width="1354" alt="Screenshot 2024-10-29 at 4 48 46 PM" src="https://github.com/user-attachments/assets/71997edb-ce9d-44c5-b7dd-e446c04f0c91">

In contrast, this graph smooths out those short-term fluctuations and provides a broader view of long-term trends. The general growth of sales from 1992 onwards is clearer in this graph. The sharp drop observed around 2020 is less spiky compared to the 4-month graph, but the overall decline due to the COVID-19 pandemic remains evident.

In conclusion, the 12-month rolling average helps to understand long-term trends and reduce noise from short-term fluctuations, while the 4-month rolling average highlights more immediate or short-term variations in sales data. Both graphs show steady growth over the years, with notable declines around significant economic events like the 2020 pandemic.

3. 4-month standard deviations:
<img width="1344" alt="Screenshot 2024-10-29 at 4 48 55 PM" src="https://github.com/user-attachments/assets/3c4fa9ea-ebae-4fdb-86b7-d1416a72b983">

This graph shows more frequent fluctuations in the variability of sales within shorter time frames. From the early 1990s to 2019, the deviations generally increase as sales grow, reflecting more variability as the industry expands. However, the spike in variability during 2020 is particularly notable, indicating a significant disruption, likely linked to the COVID-19 pandemic and its effects on the food services industry.

4. 12-month standard deviations:
<img width="1352" alt="Screenshot 2024-10-29 at 4 49 03 PM" src="https://github.com/user-attachments/assets/f3452213-86cd-4aa8-818d-9cb73abae8f7">

This graph smooths out the deviations over a longer period (12 months), showing more gradual changes in variability. Like the 4-month graph, it shows consistent growth in standard deviation over time as sales increase. The impact of 2020 is still highly visible but less sharp, reflecting the cumulative effects of the pandemic over a longer time frame. The overall increase in deviation over time suggests growing instability or variability in sales in the food services industry, particularly in response to macroeconomic disruptions.

In summary, both graphs highlight increasing variability in sales over time, with 2020 representing an outlier due to the pandemic. The 12-month window smooths the data, providing a broader view of trends, while the 4-month window captures more immediate fluctuations.

5. 4-month maximums:
<img width="1355" alt="Screenshot 2024-10-29 at 4 49 16 PM" src="https://github.com/user-attachments/assets/6846d6e4-d98f-4f28-85d7-a1c2c07e1bf0">

This graph demonstrates consistent growth in sales starting from the early 1990s until the period leading up to the COVID-19 pandemic in 2020. The rolling 4-month maximum sales steadily increase, reflecting a rise in food services demand, reaching a peak just before the sharp decline in 2020.

6. 12-month maximums:
<img width="1338" alt="Screenshot 2024-10-29 at 4 49 31 PM" src="https://github.com/user-attachments/assets/4fce5358-9cae-482f-b2e1-254b58e3dbba">

The 12-month rolling maximum exhibits a similar overall trend to the 4-month maximum but smoothens the fluctuations across a broader period. The 12-month curve appears less volatile, showing a more stable long-term increase in sales until the sudden drop in 2020. The graph highlights the pandemic's significant impact, as both shorter and longer rolling windows reflect a sharp decrease in sales.

Overall, both graphs indicate a robust upward trend in food service sales until 2020, after which there was a dramatic decline due to external shocks such as the pandemic. The 12-month window provides a smoother view of the long-term trend, while the 4-month window captures more frequent fluctuations within the year as expected.

7. 4-month minimums:
<img width="1329" alt="Screenshot 2024-10-29 at 4 49 42 PM" src="https://github.com/user-attachments/assets/d793956a-75e6-40a7-af9e-44bac63f4fef">

The graph shows a steadily increasing trend in minimum sales from the early 1990s until around 2019. Post-2019, there is a sharp drop in minimum sales, likely influenced by the COVID-19 pandemic and its impact on the food service industry. The minima hit a significant low around 2020, highlighting the major downturn during this period.

8. 12-month minimums:
<img width="1330" alt="Screenshot 2024-10-29 at 4 49 52 PM" src="https://github.com/user-attachments/assets/35fa060b-9df1-48f3-9f78-831b629a85a0">

The 12-month minima follow a similar pattern to the 4-month window, though the variations appear smoother due to the longer rolling window. The general trend is upward until the sharp dip in 2020, consistent with the global disruptions in food service sectors during the pandemic.

Both minima and maxima trends show a similar overall growth trajectory over time, though the maxima are consistently higher than the minima (as expected). The major divergence comes in 2020, where both maximum and minimum values experience sharp declines. The drop is more pronounced for the minima, indicating that even the lowest-performing months in 2020 were affected substantially more than usual.

---

Additionally, another business that I wanted to analyze was the Hardware stores category. My analysis started the same way, plotting the sales per year graph.

<img width="1310" alt="Screenshot 2024-10-29 at 4 50 03 PM" src="https://github.com/user-attachments/assets/fe45b11d-b489-470e-bd6d-29023927fab5">

This graph shows the sales trends over time for hardware stores from 1992 to 2021. It demonstrates a steady upward trend, with periodic seasonal fluctuations. The sales appear to peak annually, particularly in the summer months, which is typical for hardware-related activities such as home improvement projects that tend to increase during warmer seasons.

Over time, the sales figures grow significantly, except for a short downtrend seen during the period of the 2008 financial crisis. Sales reached their highest points in 2018 and 2020. However, there is a notable dip in 2021, possibly influenced by external economic factors such as the COVID-19 pandemic, which caused disruptions across many retail sectors. Despite these fluctuations, the overall trajectory indicates a long-term increase in hardware store sales, reflecting growing consumer demand in this sector.

1. 4-month rolling averages:
<img width="1329" alt="Screenshot 2024-10-29 at 4 50 13 PM" src="https://github.com/user-attachments/assets/5665999e-66c2-42cd-86b8-6ce84442bcd8">

The 4-month rolling average graph exhibits higher variability due to the shorter time window, highlighting the more frequent fluctuations in sales. There is a clear long-term upward trend, indicating that hardware store sales have been steadily growing over the decades. The plot also shows some significant dips in sales, especially towards the year 2020, likely due to the economic impacts of the COVID-19 pandemic.

2. 12-month rolling averages:
<img width="1347" alt="Screenshot 2024-10-29 at 4 50 22 PM" src="https://github.com/user-attachments/assets/e120be3d-4b26-453c-9c31-26a8db71bab4">

In contrast, the 12-month rolling average smooths out these fluctuations further, offering a more stable view of the overall trend. This graph shows a more gradual and consistent increase in sales over the years, with a less pronounced, but still noticeable, dip around 2020. The overall trend remains positive, indicating strong growth in hardware store sales, with major peaks in the late 2010s.

Both graphs reflect the strong seasonality of hardware store sales and a general upward trajectory over time, with the sharp decline in 2020 likely due to the pandemic's disruptions.

3. 4-month standard deviations:
<img width="1328" alt="Screenshot 2024-10-29 at 4 50 41 PM" src="https://github.com/user-attachments/assets/8e3783a7-c537-4461-a8e6-68e2e625564c">

I see relatively stable variability between 1990 and 2010, with fluctuations but not substantial increases. However, post-2010, the standard deviation begins to rise gradually, and it becomes significantly higher around 2020, likely reflecting the economic uncertainty and volatility associated with the COVID-19 pandemic. The standard deviation peaks in 2020, indicating a substantial increase in the variability of sales during that period.

4. 12-month standard deviations:
<img width="1338" alt="Screenshot 2024-10-29 at 4 50 52 PM" src="https://github.com/user-attachments/assets/fb5706df-fd64-4472-b862-90e3c6e3f0ee">

This graph follows a similar pattern but with smoother fluctuations due to the longer window. There is a steady increase in variability after 2010, followed by a sharp spike around 2020. The smoother nature of this graph compared to the 4-month graph reflects that the longer rolling window smooths out short-term volatility, but the same overall trend of increased variability in recent years is apparent.

Both graphs indicate that the hardware store sector experienced increased volatility during 2020, likely driven by external market factors. The sharp increase in standard deviation during that time demonstrates that sales became more unpredictable. This reflects broader economic disruptions that affected consumer spending patterns during the pandemic.

5. 4-month maximums:
<img width="1322" alt="Screenshot 2024-10-29 at 4 51 02 PM" src="https://github.com/user-attachments/assets/fbca67fa-338e-498c-a2e1-43b954f53879">

The graph shows a steady increase in rolling sales from the early 1990s to 2020, with occasional fluctuations reflecting short-term peaks and dips. A noticeable increase is seen around 2020, where rolling sales reach their highest point, peaking near 3,500 million. This aligns with the hardware sales boom seen during the COVID-19 pandemic, driven by a surge in DIY projects as people spent more time at home. However, after the peak, there is a notable decline, reflecting a potential market correction as pandemic-related consumer behavior started to normalize.

6. 12-month maximums:
<img width="1333" alt="Screenshot 2024-10-29 at 4 51 11 PM" src="https://github.com/user-attachments/assets/03f54fff-92f1-4182-b72b-e7b0de8e3614">

Sales in hardware stores steadily grow throughout the entire period, reaching a maximum of approximately 3,500 million by the end of the timeline. The impact of the pandemic is also visible here, but the 12-month window makes the fluctuations less pronounced, offering a longer-term perspective on sales trends.

7. 4-month minimums:
<img width="1323" alt="Screenshot 2024-10-29 at 4 51 24 PM" src="https://github.com/user-attachments/assets/22b709a1-0aff-414d-b856-f5d08bfddf3a">

The graph shows a consistent upward trajectory in sales over time, with more fluctuations in the earlier periods (especially before 2000) and smoothing out in the more recent years. The sharp drop in 2020 is still evident, though less pronounced than in the maximums graph. This indicates that the minimums are increasing steadily, showing resilience even during economic downturns, but the fluctuations in sales still cause significant variability.

8. 12-month minimums:
<img width="1332" alt="Screenshot 2024-10-29 at 4 51 34 PM" src="https://github.com/user-attachments/assets/9b2db5ad-c243-49b1-bcc6-bfdade8b5c16">

The 12-month rolling minimums also follow a similar pattern, with steady growth over time, particularly post-2000. The trend is smoother than the 4-month graph, indicating that while shorter-term fluctuations affect hardware sales significantly, the overall long-term trend is upward. The 2020 drop is visible here too but less dramatic compared to the short-term data, suggesting that while the sector experienced disruption, it was cushioned over the longer time frame.

Both the minimum and maximum graphs highlight an overall increase in hardware store sales, but the fluctuations (especially during crises like the 2020 pandemic) are more noticeable in the maximums than in the minimums. This suggests that while sales reached high peaks during certain periods, these peaks were also vulnerable to sharp declines. The minimums, on the other hand, tend to show a more stable foundation, indicating consistent baseline performance that is less prone to extreme variability, even during economic slowdowns.


[Back to top](#Index)

## Conclusion

The sporting goods business category appears to attract the most spending due to the increasing focus on health, fitness, and recreational activities. Over recent decades, there has been a shift toward active lifestyles, especially with the rise of athleisure, home workout equipment, and outdoor activities. This trend became even more pronounced during the COVID-19 pandemic when people sought new ways to stay active while gyms were closed. As a result, sporting goods have seen consistent growth in consumer spending, benefiting from this sustained demand.

In contrast, the bookstore category attracts the least spending due to the shift in consumer behavior toward digital media. With the rise of e-books, online retailers, and digital content platforms, traditional bookstores have struggled to maintain their market share. Consumers increasingly prefer the convenience and lower cost of digital books and online shopping, leading to a significant decline in spending on physical bookstores. The decline of brick-and-mortar book retailers is also reflective of broader changes in the retail landscape, where physical stores face stiff competition from e-commerce giants.

The percentage contribution of men’s and women’s clothing stores to total retail sales has been decreasing over time. Several factors contribute to this decline. As more consumers shift to online shopping, physical clothing stores have seen their share of retail sales shrink. Online platforms offer more convenience, variety, and often lower prices. Additionally, there is a growing trend toward sustainability, minimalism, and second-hand clothing. Consumers are becoming more conscious of their fashion consumption, often choosing to buy fewer new clothes, which contributes to the overall decline in sales at traditional clothing stores. The gap between men’s and women’s clothing stores is also closing, likely due to the increasing unisex or gender-neutral fashion trends. Additionally, men’s fashion consumption has been growing, narrowing the traditional spending disparity between the two categories.

Longer rolling time windows, such as 12-month periods, help smooth out the trend by averaging out the short-term fluctuations and seasonal variations. These windows provide a clearer picture of long-term trends and underlying growth patterns, filtering out noise from short-term events like promotions, holiday seasons, or economic shocks. As shown in the graphs, shorter windows reflect more volatility, while longer windows provide more stability and make it easier to identify the sustained growth or decline in different categories.

In conclusion, the MRTS data reveals evolving consumer behavior, where preferences for health and fitness drive sporting goods sales, digital disruption impacts bookstores, and shifting trends in fashion consumption affect clothing sales. By analyzing rolling windows, we can better understand these trends and how they are influenced by both short-term events and long-term shifts in the retail market.



[Back to top](#Index
)
## References

Add all references you used to complete this project.

- Amazon. "What is ETL (Extract Transform Load)". AWS Amazon. https://aws.amazon.com/what-is/etl/
- A&G Statworks. "Converting CSV to SQL with Python". A&G Statworks, 2019. https://www.youtube.com/watch?v=x8i9haDHR0s&ab_channel=A%26GStatworks
- Stock, James H. "International Encyclopedia of the Social & Behavioral Sciences(Second Edition)". Time Series: Economic Forecasting, 2015: 337-340.
- The Wall Street Journal. "How Toys 'R' Us Went Bankrupt | WSJ", 2019. https://www.youtube.com/watch?v=W9CxiNsX0zs&ab_channel=TheWallStreetJournal
- Tighe, D. "Sporting goods store sales in the U.S. from 1992 to 2022". Statista, 2024. https://www.statista.com/statistics/197704/annual-sporting-goods-store-sales-in-the-us-since-1992/
- Page, Lucy E. "The Effect of E-commerce Expansion on Local Retail". National Bureau of Economic Research, 2022. https://www.nber.org/digest/202208/effect-e-commerce-expansion-local-retail
- ProjectPro - Data Science Projects. "Rolling Time Window in Python". 2019. https://www.youtube.com/watch?v=_buD5zGD230&ab_channel=ProjectPro-DataScienceProjects 
