# E-commerce-price-comparison-tools
import requests
from bs4 import BeautifulSoup

class PriceScraper:
    """
    PriceScraper class for scraping and comparing product prices across multiple e-commerce platforms.
    """

    def _init_(self):
        self.platform_urls = {
            'Amazon': 'https://www.amazon.in/s?k=',  # Amazon Search URL
            'eBay': 'https://www.ebay.com/sch/i.html?_nkw=',
            'Walmart': 'https://www.walmart.com/search?q='
        }
        self.headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36'
        }

    def fetch_page(self, url):
        """Fetch the HTML content of a page."""
        try:
            response = requests.get(url, headers=self.headers, timeout=10)
            response.raise_for_status()  # Raise error for HTTP issues
            return response.text
        except requests.exceptions.RequestException as e:
            print(f"Failed to fetch page: {url} | Error: {e}")
            return None

    def scrape_amazon(self, product_name):
        """Scrape Amazon for product prices (Limited due to restrictions)."""
        search_url = self.platform_urls['Amazon'] + product_name.replace(' ', '+')
        html = self.fetch_page(search_url)
        if html:
            soup = BeautifulSoup(html, 'html.parser')
            results = soup.find_all('span', {'class': 'a-price-whole'})
            prices = [price.get_text(strip=True) for price in results]
            return prices if prices else ["No prices found"]
        return ["Failed to fetch data"]

    def scrape_ebay(self, product_name):
        """Scrape eBay for product prices."""
        search_url = self.platform_urls['eBay'] + product_name.replace(' ', '+')
        html = self.fetch_page(search_url)
        if html:
            soup = BeautifulSoup(html, 'html.parser')
            results = soup.find_all('span', {'class': 's-item__price'})
            prices = [price.get_text(strip=True) for price in results]
            return prices if prices else ["No prices found"]
        return ["Failed to fetch data"]

    def scrape_walmart(self, product_name):
        """Scrape Walmart for product prices."""
        search_url = self.platform_urls['Walmart'] + product_name.replace(' ', '+')
        html = self.fetch_page(search_url)
        if html:
            soup = BeautifulSoup(html, 'html.parser')
            results = soup.find_all('span', {'class': 'price-main'})  # Adjust based on Walmart HTML
            prices = [price.get_text(strip=True) for price in results]
            return prices if prices else ["No prices found"]
        return ["Failed to fetch data"]

    def compare_prices(self, product_name):
        """Compare prices across platforms."""
        print(f"Comparing prices for {product_name}...\n")

        amazon_prices = self.scrape_amazon(product_name)
        ebay_prices = self.scrape_ebay(product_name)
        walmart_prices = self.scrape_walmart(product_name)

        print(f" Amazon Prices: {amazon_prices}")
        print(f" eBay Prices: {ebay_prices}")
        print(f" Walmart Prices: {walmart_prices}")

if _name_ == '_main_':
    scraper = PriceScraper()
    scraper.compare_prices('Laptop')
