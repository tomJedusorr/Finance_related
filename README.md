# Finance_related
Find all kind of financial models and formulas you need

import yfinance as yf

def calculate_avg_annual_return_and_std_dev(ticker, start_date, end_date):
    # Download historical market data including dividends
    stock_data = yf.download(ticker, start=start_date, end=end_date)
    
    # Ensure there's data available
    if stock_data.empty:
        return None, None
    
    # Calculate daily returns
    stock_data['Daily Return'] = stock_data['Close'].pct_change()

    # Download dividend data
    dividends = yf.Ticker(ticker).dividends
    dividends = dividends[start_date:end_date]  

    # Calculate the total dividends received during the period
    total_dividends = dividends.sum()

    # Calculate the total capital gains
    initial_price = stock_data['Close'].iloc[0]
    final_price = stock_data['Close'].iloc[-1]
    capital_gains = (final_price - initial_price) + total_dividends
    
    # Calculate total return
    total_return = capital_gains / initial_price  
    
    # Take the length of the dataset for calculate the timeframe
    nb_days = len(stock_data) - 1
    nb_years =  nb_days / 252 # Widely used as the number of trading days in a year
    
    # Calculate average annual return
    avg_annual_return = (1 + total_return) ** (1 / nb_years) - 1
    
    avg_annual_return = float(avg_annual_return.iloc[0])
    
    # Calculate standard deviation of daily returns
    std_dev = stock_data['Daily Return'].std() * (252 ** 0.5)  # Annualize the standard deviation
    
    # calculate the sharpe ratio simplified
    sharpe_ratio_simplified = avg_annual_return / std_dev
    
    print(f"The average annual return of {ticker}, from {start_date} to {end_date} is: {avg_annual_return:.4%}\n")
    print(f"The standard deviation of {ticker}, from {start_date} to {end_date} is: {std_dev:.4%}\n")
    print(f"The simplified sharpe ratio from this period is then: {sharpe_ratio_simplified:.4f}")

# Example usage
ticker = 'MCD'  # Carrefour ticker on Euronext
start_date = '2009-10-23'
end_date = '2024-10-23'
calculate_avg_annual_return_and_std_dev(ticker, start_date, end_date)
