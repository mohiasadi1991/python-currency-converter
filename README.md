import json
import urllib.reques

API_URL = "https://open.er-api.com/v6/latest/"

def get_rates(base_currency):
    url = API_URL + base_currency.upper()

    try:
        request = urllib.request.Request(
            url,
            headers={"User-Agent": "PythonCurrencyConverter"}
        )

        with urllib.request.urlopen(
            request, timeout=10
        ) as response:
            data = json.loads(response.read())

        if data.get("result") != "success":
            print("Could not retrieve exchange rates.")
            return None

        return data["rates"]

    except Exception as error:
        print("API connection failed:", error)
        return None

def convert_currency(amount, source, target):
    rates = get_rates(source)

    if rates is None:
        return

    target = target.upper()

    if target not in rates:
        print("Invalid target currency.")
        return

    result = amount * rates[target]

    print("\n=== Conversion Result ===")
    print(f"{amount:.2f} {source.upper()}")
    print(f"= {result:.2f} {target}")
    print(f"Exchange rate: 1 {source.upper()} = {rates[target]} {target}")

def main():
    print("=== Python Currency Converter ===")

    try:
        amount = float(input("Enter amount: "))

        if amount <= 0:
            print("Amount must be positive.")
            return

        source = input("From currency (e.g. USD): ").strip()
        target = input("To currency (e.g. EUR): ").strip()

        if not source or not target:
            print("Currency codes cannot be empty.")
            return

        convert_currency(amount, source, target)

    except ValueError:
        print("Please enter a valid amount.")

if __name__ == "__main__":
    main()
