# TakeDrop API

Dokumentacja TakeDrop API.

## Informacje

Panel administracyjny dostępny jest pod adresem [https://app2.takedrop.pl/](https://app2.takedrop.pl)
W celu uzyskania konta testowego skontaktuj się obsługą TakeDrop.

Po otrzymaniu konta testowego Twój sklep będzie dostępny pod adresem **nazwa_sklepu**.takedrop.pl
W sklepie domyślnie załączona jest jedna metoda płatności (PayU) w testowej konfiguracji sandbox. Po złożeniu testowego zamówienia i przekierowaniu należy wybrać dowolną metodę płatności i wybrać "pozytywna autoryzacja". Zamówienie zostanie oznaczone jako opłacone i będzie dostępne pod adresem API do pobierania zamówień.

# Rest API

API znajduje się pod adresem `https://api.takedrop.pl`

### Pobieranie zamówień

API umożliwia pobieranie zamówień. Określone w parametrze apiKey identyfikuje daną hurtownię i zwraca zamówienia tylko dla tej hurtowni.

##### Zapytanie

`GET /wholesaler/order`

Parametry (query string):

- apiKey - klucz dostępu unikatowy dla każdej hurtowni
- paymentDateFrom - określa zamówienia opłacone tylko od tej daty (**strefa UTC**) w formacie ISO 8601 z dokładnością jak w przykładzie.

Przykład:

```
https://api.takedrop.pl/wholesaler/order?apiKey=sdfkjh23asdkjhk&paymentDateFrom=2020-11-26T11:34:31.810414
```

##### Odpowiedź:

Tablica obiektów gdzie każdy obiekt zawiera:

- id - id zamówienia w TakeDrop
- delivery - obiekt z danymi do dostawy
  - methodName - nazwa wybranej metody dostawy
  - fullName - imię i nazwisko
  - email - email
  - phone - telefon
  - address1 - pierwsza linia adresu (ulica i numer)
  - address2 - druga linia adresu (jeśli istnieje)
  - postCode - kod pocztowy
  - city - miasto
  - countryCode - dwuliterowy kod kraju
- merchant - obiekt z emailem sprzedawcy
  - email - email sprzedawcy
- items - tablica obiektów gdzie kazdy obiekt zawiera:
  - externalId - id produktu w hurtowni
  - quantity - ilosc danego produktu
  - variants - tablica stringow słownie określająca dany warianty (jeśli istnieje dla danego produktu)
  - combinationReference - identyfikator danej kombinacji (jeśli istnieje dla danego produktu)

Przykład odpowiedzi:

```
[{
    id: 6158,
    delivery: {
        methodName: "Darmowa",
        fullName: "Andrzej Kowalski",
        email: "andrzej@kowalski.pl ",
        phone: "123456788",
        address1: "Aleja Śliwowa 5",
        address2: "",
        postCode: "44-100",
        city: "Wrocław",
        countryCode: "PL"
    },
    merchant: {
        email: "anna@sklep.pl"
    },
    items: [
        {
            externalId: 99450,
            quantity: 1,
            variants: ["Zielony"],
            combinationReference: "AB3313GREEN"
        }
    ]
}]
```

### Wysyłanie zamówień

API przyjmuje informacje o statusie realizacji zamówienia i dodaje odpowiednie wpisy w widoku edycji zamówienia w sekcji "Szczegóły realizacji".
Uwaga: **Nie są obsługiwane zamówienia zrealizowane częściowo**. Takie zamówienia zostaną odrzucone i zostanie zwrócony błąd.
Wszystkie produkty podane w zapytaniu muszą się zgadzać i być w odpowiedniej ilości.

##### Zapytanie

`PUT /wholesaler/order`

Parametry (query string):

- apiKey - klucz dostępu unikatowy dla każdej hurtowni

Request body (json)

- orderId - id zamówienia w TakeDrop
- wholesalerOrderId - id zamówienia w hurtowni
- status - dostępne wartości:
  - "Sent" - zamówienie wysłane z hurtowni
  - "Cancelled" - zamówienie zostało anulowane przez hurtownię
- trackingNumber - numer śledzenia paczki (jeśli istnieje)
- value - kwota zamówienia w hurtowni
- items - tablica obiektów, gdzie każdy obiekt zawiera:
  - externalId - id produktu w hurtowni
  - quantity - ilośc danego produktu
  - combinationReference - identyfikator danej kombinacji

Przykład:

```
{
    "orderId": 5694,
    "wholesalerOrderId": 8838383,
    "status":"Sent",
    "trackingNumber":"RR2244220233",
    "value": 120.50,
    "items": [
            {
                "externalId": 99450,
                "quantity": 1,
                "combinationReference": "AB3313GREEN"
            }
        ]
}
```

##### Odpowiedź:

Status 200 OK w przypadku poprawnego zapisania danych. W przeciwnym razie status błędu.

### Pobieranie sprzedawców

API umożliwia pobieranie listy sprzedawców, którzy wybrali integracje z daną hurtownią.

##### Zapytanie

`GET /wholesaler/merchant`

Parametry (query string):

- apiKey - klucz dostępu unikatowy dla każdej hurtowni
- dateFrom - określa sprzedawców, którzy załączyli integrację tylko od tej daty (**strefa UTC**) w formacie ISO 8601 z dokładnością jak w przykładzie.

Przykład:

```https://api.takedrop.pl/wholesaler/merchant?apiKey=sdfkjh23asdkjhk&dateFrom=2020-11-26T11:34:31.810414```

##### Odpowiedź:

Tablica obiektów gdzie każdy obiekt zawiera:

- id - id sprzedawcy w TakeDrop
- email - email
- fullName - imię i nazwisko
- companyName - nazwa firmy
- address - ulica i numer
- city - miasto
- postCode - kod pocztowy
- phone - telefon
- taxId - identyfikator podatkowy (NIP)
- countryCode - dwuliterowy kod kraju

Przykład odpowiedzi:

```
[
    {
        id: 1160,
        email: "superadmin@takedrop.pl",
        fullName: "4444",
        companyName: "SuperFirma",
        address: "Aleja Sliwowa 5",
        city: "Gliwice",
        postCode: "44-100",
        countryCode: "PL"
        phone: "123123123",
        taxId: "6285554442"
    }
]
```

# Pomoc

W przypadku pytań należy kontaktować się bezpośrednio z obsługą TakeDrop.

Data aktualizacji: 21.12.2020
