## Project Overview
This project focuses on extracting and analyzing stock and revenue data for Tesla and GameStop using various data science tools and techniques. 
The tasks include obtaining stock data from yfinance, scraping revenue data from the web, and visualizing the insights through dashboards. The assignment also involves sharing the final notebook via GitHub.

## Breakdown of the Tasks:
Question 1: Extracting Tesla Stock Data using yfinance 

Question 2: Extracting Tesla Revenue Data using Web Scraping

Question 3: Extracting GameStop Stock Data using yfinance 

Question 4: Extracting GameStop Revenue Data using Web Scraping 

Question 5: Creating a Dashboard for Tesla Stock and Revenue Data

Question 6: Creating a Dashboard for GameStop Stock and Revenue Data

Question 7: Sharing the Assignment Notebook via GitHub 

This introduction helps set the context for each of the tasks and the overall project goal

## Extracting Tesla Stock Data Using yfinance
In this section, we will use the yfinance library to extract Tesla's stock data. 
The data will include:

- Historical market data and  dividends
- Stock splits and  financials
- Major holders and institutional holders
- Balance sheet and  cashflow
- Earnings and sustainability
- Analyst recommendations and options data.
  
## The following code demonstrates how to retrieve and display these datasets.
  # Import yfinance
import yfinance as yf

# Create a Ticker object for Tesla
tesla = yf.Ticker("TSLA")

# Get stock info
print("Tesla Stock Information:")
print(tesla.info)

# Get historical market data
print("\nTesla Historical Market Data:")
hist = tesla.history(period="max")
print(hist)

# Show actions (dividends, splits)
print("\nTesla Actions (Dividends and Splits):")
print(tesla.actions)

# Show dividends
print("\nTesla Dividends:")
print(tesla.dividends)

# Show stock splits
print("\nTesla Splits:")
print(tesla.splits)

# Show financials
print("\nTesla Financials:")
print(tesla.financials)

# Show major holders
print("\nTesla Major Holders:")
print(tesla.major_holders)

# Show institutional holders
print("\nTesla Institutional Holders:")
print(tesla.institutional_holders)

# Show balance sheet
print("\nTesla Balance Sheet:")
print(tesla.balance_sheet)

# Show cashflow
print("\nTesla Cashflow:")
print(tesla.cashflow)

# Show earnings
# Updated: Ticker.earnings is deprecated, use income statement
print("\nTesla Earnings (Net Income from Income Statement):")
print(tesla.income_stmt)

# Show sustainability
print("\nTesla Sustainability Metrics:")
print(tesla.sustainability)

# Show analysts' recommendations
print("\nTesla Analysts Recommendations:")
print(tesla.recommendations)

# Show next event (earnings, etc.)
print("\nTesla Upcoming Events:")
print(tesla.calendar)

# Show ISIN code - *experimental*
print("\nTesla ISIN (International Securities Identification Number):")
print(tesla.isin)

# Show options expirations
print("\nTesla Options Expirations:")
print(tesla.options)

# Get option chain for a valid expiration date (use a valid future date)
opt = tesla.option_chain('2024-10-18')  # Use the earliest available date from the list
print("\nTesla Options Chain for 2024-10-18:")
print(opt.calls)
print(opt.puts)

To reset the index of the `tesla_revenue` DataFrame, save the modified DataFrame, and display the first five rows using the `head` function, you can follow these steps in your code:

1. Use the `reset_index()` method on the DataFrame.
2. Save the DataFrame to a CSV file (or another format if desired).
3. Use the `head()` method to display the first five rows.

Here’s the code that includes these steps:

```python
# Import necessary libraries 
import requests
from bs4 import BeautifulSoup
import pandas as pd

# Define the URL of the page to scrape
url = 'https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue'

# Specify headers to mimic a browser request
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3"
}

# Send a request to the URL with headers
response = requests.get(url, headers=headers)

# Check if the page was fetched correctly
if response.status_code != 200:
    print(f"Failed to retrieve data: {response.status_code}")
else:
    # Parse the HTML content of the page
    soup = BeautifulSoup(response.text, 'html.parser')

    # Find all tables on the page
    tables = soup.find_all('table')

    # Debug: Print the number of tables found
    print(f"Number of tables found: {len(tables)}")

    # Check if there are enough tables
    if len(tables) > 1:
        # The revenue data is typically in the second table
        revenue_table = tables[1]

        # Extract the rows from the revenue table
        rows = revenue_table.find_all('tr')

        # Create a list to store the revenue data
        revenue_data = []

        # Loop through each row and extract the columns (Date and Revenue)
        for row in rows[1:]:
            cols = row.find_all('td')
            date = cols[0].text.strip()
            revenue = cols[1].text.strip().replace('$', '').replace(',', '')
            revenue_data.append([date, revenue])

        # Convert the data into a DataFrame for better readability
        tesla_revenue = pd.DataFrame(revenue_data, columns=['Date', 'Revenue (in million USD)'])

        # Reset the index of the DataFrame
        tesla_revenue.reset_index(drop=True, inplace=True)

        # Save the DataFrame to a CSV file
        tesla_revenue.to_csv('tesla_revenue.csv', index=False)

        # Display the first five rows of the DataFrame
        print("\nFirst five rows of the Tesla Revenue DataFrame:")
        print(tesla_revenue.head())
        
    else:
        print("Could not find the revenue table. The page structure might have changed.")

# Send the request to Nasdaq Financials page with headers
nasdaq_url = 'https://www.nasdaq.com/market-activity/stocks/tsla/financials'
response = requests.get(nasdaq_url, headers=headers)

# Check if request was successful
if response.status_code == 200:
    soup = BeautifulSoup(response.text, 'lxml')
    print("Nasdaq data retrieved successfully!")
    # Continue with web scraping for Nasdaq financial data
else:
    print(f"Failed to retrieve Nasdaq data: {response.status_code}")
```
Similarly to tesla, Gamestop approach is adopted for the analysis as the code describes:

Here’s the code include a function for graphing GameStop stock data and the `gme_revenue` DataFrame, along with a title for the graph. 

A `make_graph` function takes the stock data as input, plots the data, and provides a title for the graph.

### Full Code to Fetch, Store, and Graph GameStop Revenue Data:

```python
# Import necessary libraries
import requests
from bs4 import BeautifulSoup
import pandas as pd
import matplotlib.pyplot as plt

# Function to graph stock data
def make_graph(df, title):
    """
    This function creates a plot for the provided DataFrame.
    
    Parameters:
    df (pd.DataFrame): DataFrame containing 'Date' and 'Revenue' columns
    title (str): Title for the graph
    """
    df['Date'] = pd.to_datetime(df['Date'])  # Convert 'Date' column to datetime format
    df['Revenue (in million USD)'] = pd.to_numeric(df['Revenue (in million USD)'], errors='coerce')  # Convert 'Revenue' to numeric
    
    plt.figure(figsize=(10, 6))
    plt.plot(df['Date'], df['Revenue (in million USD)'], label='Revenue over Time', color='b', marker='o')
    plt.title(title)
    plt.xlabel('Date')
    plt.ylabel('Revenue (in million USD)')
    plt.xticks(rotation=45)
    plt.grid(True)
    plt.tight_layout()
    plt.show()

# Define the URL of the page to scrape GameStop data
url = 'https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue'

# Specify headers to mimic a browser request
headers = {
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3"
}

# Send a request to the URL with headers
response = requests.get(url, headers=headers)

# Check if the page was fetched correctly
if response.status_code != 200:
    print(f"Failed to retrieve data: {response.status_code}")
else:
    # Parse the HTML content of the page
    soup = BeautifulSoup(response.text, 'html.parser')

    # Find all tables on the page
    tables = soup.find_all('table')

    # Debug: Print the number of tables found
    print(f"Number of tables found: {len(tables)}")

    # Check if there are enough tables
    if len(tables) > 1:
        # The revenue data is typically in the second table
        revenue_table = tables[1]

        # Extract the rows from the revenue table
        rows = revenue_table.find_all('tr')

        # Create a list to store the revenue data
        revenue_data = []

        # Loop through each row and extract the columns (Date and Revenue)
        for row in rows[1:]:
            cols = row.find_all('td')
            date = cols[0].text.strip()
            revenue = cols[1].text.strip().replace('$', '').replace(',', '')
            revenue_data.append([date, revenue])

        # Convert the data into a DataFrame for better readability
        gme_revenue = pd.DataFrame(revenue_data, columns=['Date', 'Revenue (in million USD)'])

        # Reset the index of the DataFrame
        gme_revenue.reset_index(drop=True, inplace=True)

        # Save the DataFrame to a CSV file
        gme_revenue.to_csv('gme_revenue.csv', index=False)

        # Display the first five rows of the DataFrame
        print("\nFirst five rows of the GameStop Revenue DataFrame:")
        print(gme_revenue.head())

        # Graph the GameStop revenue data
        make_graph(gme_revenue, title="GameStop Revenue Over Time")
        
    else:
        print("Could not find the revenue table. The page structure might have changed.")
```

### Explanation:
1. **`make_graph` function**: This function takes in a DataFrame and a title. It converts the `Date` column to datetime format and the `Revenue (in million USD)` to a numeric format. It then plots the data using `matplotlib`, setting the graph title, labels, and formatting the x-axis.

2. **Web Scraping and Data Processing**: The scraping part is unchanged from your original code. After retrieving the data from the website and storing it in a DataFrame, the graph function is called to visualize the data.

3. **Displaying the Graph**: The graph is generated by the `make_graph` function, which uses the `gme_revenue` DataFrame and gives the graph the title "GameStop Revenue Over Time".

4. **Output**: Once the script runs, you should see the first five rows of the `gme_revenue` DataFrame, followed by the generated plot of GameStop's revenue over time.

This code will scrape GameStop's revenue data, save it, and visualize it with a line graph.


### Explanation of Changes:
1. **Resetting the Index**: 
   - `tesla_revenue.reset_index(drop=True, inplace=True)` resets the DataFrame's index without adding the old index as a new column.
   
2. **Saving the DataFrame**: 
   - `tesla_revenue.to_csv('tesla_revenue.csv', index=False)` saves the DataFrame to a CSV file named `tesla_revenue.csv`. The `index=False` argument ensures the index is not included in the CSV file.

3. **Displaying the First Five Rows**: 
   - `print(tesla_revenue.head())` displays the first five rows of the DataFrame after resetting the index. 

This code will now successfully scrape the Tesla revenue data, reset the index, save it to a CSV file, and display the first five rows of the DataFrame.

  ### Project Summary

This project involves extracting stock and revenue data for Tesla and GameStop using two main techniques: **yfinance** for stock data and **web scraping** for revenue data. 
The project is broken down into several tasks:

1. **Extracting Tesla Stock Data Using yfinance**: Tesla's stock information, historical data, and financial statistics are retrieved using the yfinance library. This allows us to analyze Tesla's market performance over time.
   
2. **Extracting Tesla Revenue Data Using Webscraping**: Tesla's revenue data is scraped from an online source, allowing for further analysis of the company's financial performance.
   
3. **Extracting GameStop Stock Data Using yfinance**: Similarly to Tesla, GameStop's stock data is extracted using yfinance, providing valuable insights into its stock history and performance.

4. **Extracting GameStop Revenue Data Using Webscraping**: GameStop's revenue data is obtained via web scraping, allowing for a comparison of its financial growth alongside its stock performance.

5. **Tesla Stock and Revenue Dashboard**: A dashboard is created to visualize Tesla’s stock price and revenue data, providing a comprehensive overview of the company's financial health.

6. **GameStop Stock and Revenue Dashboard**: GameStop's stock and revenue data are visualized in a similar dashboard, enabling easy interpretation of trends and patterns in the company’s financial journey.

7. **Sharing the Assignment Notebook**: The final task involves sharing the assignment notebook via GitHub to make the work accessible and ready for review.

The entire project demonstrates a clear understanding of financial data extraction and visualization, combining Python libraries and web scraping techniques to create meaningful insights.

---

### Author
Jemael Nzihou
