import requests
from bs4 import BeautifulSoup
import pandas as pd

# Plik z linkami do ogłoszeń (jeden link w każdej linii)
LINKS_FILE = "links.txt"
OUTPUT_FILE = "dzialki.xlsx"

def extract_data(url):
    data = {
        "Link": url,
        "Lokalizacja": "",
        "Cena": "",
        "Metraż": "",
        "Numer oferty": "",
        "Telefon": ""
    }

    try:
        r = requests.get(url, timeout=10, headers={"User-Agent": "Mozilla/5.0"})
        if r.status_code != 200:
            print(f"❌ Nie udało się pobrać strony: {url}")
            return data

        soup = BeautifulSoup(r.text, "html.parser")

        # Przybliżone selektory (mogą wymagać dostosowania do strony)
        text = soup.get_text(" ", strip=True)

        # Szukanie słów kluczowych
        if "m²" in text:
            idx = text.find("m²")
            data["Metraż"] = text[idx-10:idx+3].strip()

        if "zł" in text:
            idx = text.find("zł")
            data["Cena"] = text[idx-15:idx+2].strip()

        if "tel" in text.lower() or "telefon" in text.lower():
            idx = text.lower().find("tel")
            data["Telefon"] = text[idx:idx+30].strip()

        # Czasem numer oferty
        if "Oferta" in text or "Nr oferty" in text:
            idx = text.find("ofert")
            data["Numer oferty"] = text[idx:idx+30].strip()

        # Lokalizacja
        for word in ["Brwinów", "Owczarnia", "Pruszków", "Kanie", "Nowa Wieś", "Otrębusy", "Granica"]:
            if word.lower() in text.lower():
                data["Lokalizacja"] = word
                break

    except Exception as e:
        print(f"⚠️ Błąd przy {url}: {e}")

    return data

def main():
    with open(LINKS_FILE, "r") as f:
        links = [line.strip() for line in f if line.strip()]

    results = []
    for url in links:
        print(f"🔍 Przetwarzam: {url}")
        results.append(extract_data(url))

    # Zapis do Excela
    df = pd.DataFrame(results)
    df.to_excel(OUTPUT_FILE, index=False)
    print(f"✅ Zapisano dane do pliku: {OUTPUT_FILE}")

if __name__ == "__main__":
    main()
