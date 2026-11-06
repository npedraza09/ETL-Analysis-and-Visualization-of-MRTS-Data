# ETL, Analysis and Visualization of MRTS Data

An ETL and analysis project on the U.S. Census Bureau's Monthly Retail Trade Survey (MRTS). Built for my MIT Professional Certificate in Data Engineering (2024–2025).

## What I did

1. **Extract and load:** loaded the MRTS data into MySQL and cleaned and reshaped it in Python.
2. **Query:** ran the analysis queries in MySQL Workbench and again from Python.
3. **Analyze:** compared sales trends, each category's percentage of total retail sales, and 4- and 12-month rolling averages with pandas and matplotlib.

## What the data showed

- Of the categories I compared, sporting goods drew the most spending and kept growing, while bookstores drew the least and declined.
- Men's and women's clothing stores both took a shrinking share of total retail sales, and the gap between them narrowed.
- A 12-month rolling window smoothed out seasonal swings and made the long-term trends easier to see than a 4-month window.

The full write-up, with queries and charts, is in [index.md](index.md). The notebook is `Module 8_Final_Project.ipynb`.

**Built with:** Python, MySQL, SQL, pandas, matplotlib, Jupyter
