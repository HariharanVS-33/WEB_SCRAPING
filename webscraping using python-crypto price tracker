# Cryptocurrency Price Tracker (Auto-Updating + Replace Mode)
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
import pandas as pd
import time
from datetime import datetime
import os

HEADLESS_MODE = True        # Set to False to see the browser
TOP_COINS = 10              # Number of cryptocurrencies to scrape
OUTPUT_FILE = "crypto_prices.csv"
REFRESH_INTERVAL = 300      # 5 minutes = 300 seconds

chrome_options = Options()
if HEADLESS_MODE:
    chrome_options.add_argument("--headless")
chrome_options.add_argument("--disable-gpu")
chrome_options.add_argument("--no-sandbox")
chrome_options.add_argument("--log-level=3")

def scrape_crypto_data():
    driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=chrome_options)
    driver.get("https://coinmarketcap.com/")
    time.sleep(5)  # Wait for JS content to load

    coins = []
    rows = driver.find_elements(By.XPATH, '//table[contains(@class, "cmc-table")]/tbody/tr')

    if not rows:
        print("No rows found — CoinMarketCap layout may have changed.")
        driver.quit()
        return coins

    for row in rows[:TOP_COINS]:
        try:
            name = row.find_element(By.XPATH, './/td[3]//p').text
            price = row.find_element(By.XPATH, './/td[4]//span').text
            change_24h = row.find_element(By.XPATH, './/td[5]//span').text
            market_cap = row.find_element(By.XPATH, './/td[8]//span').text

            coins.append({
                "Name": name,
                "Price": price,
                "24h Change": change_24h,
                "Market Cap": market_cap,
                "Last Updated": datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            })
        except Exception as e:
            print("Skipping a row due to parsing error:", e)

    driver.quit()
    return coins

def save_to_csv(coins):
    if not coins:
        print("No data scraped. Skipping save.")
        return

    df = pd.DataFrame(coins)
    try:
        df.to_csv(OUTPUT_FILE, index=False)
        print(f"File '{OUTPUT_FILE}' replaced with latest data at {datetime.now().strftime('%H:%M:%S')}")
    except PermissionError:
        print("Excel file is open — close it to allow updates.")

if __name__ == "__main__":
    print("Starting Live Cryptocurrency Price Tracker (Replace Mode)")
    print("Output file:", OUTPUT_FILE)
    print("Auto-refresh every 5 minutes\n")

    while True:
        data = scrape_crypto_data()
        save_to_csv(data)
        print("Next update in 5 minutes...\n")
        time.sleep(REFRESH_INTERVAL)
