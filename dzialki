import os
import requests
from hashlib import sha256
from datetime import datetime
from bs4 import BeautifulSoup
import openpyxl

STATE_FILE = "state.txt"
LINKS_FILE = "links.txt"
EXCEL_FILE = "dzialki.xlsx"

def fetch_announcements():
    data_list = []
    with open(LINKS_FILE, "r") as f:
        links = [line.strip() for line in f if line.strip()]

    for url in links:
        try:
            r = requests.get(url, timeout=10)
            r.raise_for_status()
            soup = BeautifulSoup(r.text, "html.parser")

            # Poniżej przykładowe selektory – trzeba dopasować do każdej strony
            location = soup.select_one(".location")   # klasa przykładowa
            price = soup.select_one(".price")        # klasa przykładowa
            area = soup.select_one(".area")          # klasa przykładowa
            offer_number = soup.select_one(".offer-number")  # klasa przykładowa
            contact = soup.select_one(".contact")             # klasa przykładowa

            data_list.append({
                "link": url,
                "location": location.text.strip() if location else "",
                "price": price.text.strip() if price else "",
                "area": area.text.strip() if area else "",
                "offer_number": offer_number.text.strip() if offer_number else "",
                "contact": contact.text.strip() if contact else ""
            })
        except Exception as e:
            print(f"Błąd przy pobieraniu {url}: {e}")

    return data_list

def save_to_excel(data_list):
    wb = openpyxl.Workbook()
    ws = wb.active
    ws.append(["Link", "Lokalizacja", "Cena", "Metraż", "Numer oferty", "Kontakt"])
    for item in data_list:
        ws.append([
            item["link"],
            item["location"],
            item["price"],
            item["area"],
            item["offer_number"],
            item["contact"]
        ])
    wb.save(EXCEL_FILE)

def get_hash(data):
    combined = "".join([str(d) for d in data])
    return sha256(combined.encode("utf-8")).hexdigest()

def main():
    data_list = fetch_announcements()
    current_hash = get_hash(data_list)

    if os.path.exists(STATE_FILE):
        with open(STATE_FILE, "r") as f:
            old_hash = f.read().strip()
    else:
        old_hash = ""

    if current_hash != old_hash:
        save_to_excel(data_list)
        with open(STATE_FILE, "w") as f:
            f.write(current_hash)
        print(f"[{datetime.now()}] Nowe ogłoszenia zapisane do {EXCEL_FILE}")
    else:
        print(f"[{datetime.now()}] Brak zmian.")

if __name__ == "__main__":
    main()
