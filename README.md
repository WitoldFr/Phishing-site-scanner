import requests
from bs4 import BeautifulSoup
from urllib.parse import urlparse


def is_phishing_url(url):
    
    phishing_domains = ["example-phishing.com", "malicious-site.com"]  
    parsed_url = urlparse(url)
    domain = parsed_url.netloc
    return domain in phishing_domains


def analyze_page(url):
    try:
        response = requests.get(url, timeout=5)
        soup = BeautifulSoup(response.content, "html.parser")

        suspicious_found = False  

        
        forms = soup.find_all('form')
        if forms:
            print(f"Strona {url} zawiera formularze. To może być podejrzane.")
            suspicious_found = True

       
        suspicious_keywords = ["login", "password", "account", "verify"]
        for keyword in suspicious_keywords:
            if keyword in soup.get_text().lower():
                print(f"Strona {url} zawiera podejrzane słowo kluczowe: {keyword}")
                suspicious_found = True

        if not suspicious_found:
            print(f"Strona {url} wydaje się być w porządku.")

    except requests.RequestException as e:
        print(f"Błąd podczas łączenia się z {url}: {e}")

def main():
    url = input("Wprowadź URL do sprawdzenia: ")
    
    if is_phishing_url(url):
        print(f"Uwaga! Strona {url} jest znana jako phishingowa.")
    else:
        print(f"Sprawdzanie strony {url}...")
        analyze_page(url)

if __name__ == "__main__":
    main()
