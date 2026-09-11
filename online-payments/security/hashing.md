# Hash i uwierzytelnianie komunikatów



## Bezpieczeństwo transakcji

### Opis bezpieczeństwa transakcji

W Systemie płatności online zastosowano kilka mechanizmów zwiększających
bezpieczeństwo realizowanych przy jego użyciu transakcji. Transmisja
między wszystkimi stronami transakcji realizowana jest z użyciem
bezpiecznego połączenia opartego na protokole TLS z 2048-bitowym kluczem.

Dodatkowo, komunikacja zabezpieczana jest funkcją skrótu obliczoną z wartości pól komunikatu i współdzielonego klucza (sam klucz współdzielony przechowywany jest w Systemie w postaci zaszyfrowanej algorytmem AES-ECB).

Jako funkcja skrótu wykorzystywany jest algorytm SHA256 lub SHA512 (metoda
ustalana na etapie konfigurowania danego Serwisu Partnera w Systemie
płatności online). Domyślna funkcja to SHA256.

### Obliczanie wartości funkcji skrótu

Opis sposobu obliczania wartości funkcji skrótu oraz przykłady obliczeń
dla podstawowych komunikatów.

**UWAGA:** Przykłady nie uwzględniają wszystkich możliwych pól
opcjonalnych, dlatego w razie występowania takich pól w konkretnym
komunikacie, należy uwzględnić je w funkcji skrótu w kolejności zgodnej
z numerem obok pola.

### Sposób obliczania wartości funkcji skrótu – pole Hash

Wartość funkcji skrótu, służąca do autentykacji komunikatu, obliczana
jest od łańcucha zawierającego sklejone pola komunikatu (konkatenacja
pól). Sklejane są wartości pól, bez nazw parametrów, a pomiędzy
kolejnymi (niepustymi) wartościami wstawiany jest separator (w postaci
znaku \|). Kolejność sklejania pól jest zgodna z kolejnością ich
występowania na liście parametrów w dokumentacji.

**WAŻNE!** W przypadku braku opcjonalnego parametru w komunikacie lub w
przypadku pustej wartości parametru, nie należy używać separatora!

Do powstałego w powyższy sposób łańcucha doklejany jest na jego końcu
klucz, współdzielony między Serwis Partnera i System płatności online. Z
tak powstałego łańcucha obliczana jest wartość funkcji skrótu i stanowi
ona wartość pola Hash komunikatu.

*Hash = funkcja(wartości_pola_1\_komunikatu + \"\|" +
wartości_pola_2\_komunikatu + \"\|" + ... + \"\|" +
wartości_pola_n\_komunikatu + \"\|" + klucz_współdzielony);*

### Przykładowe obliczenia wartości funkcji skrótu podczas rozpoczęcia transakcji

> Dane Serwisu Partnera:\
> ServiceID = 2
>
> klucz_współdzielony = 2test2
>
> Adres bramki
> [https://{host_bramki}/sciezka](https://{host_bramki}/sciezka)

a.  Rozpoczęcie transakcji.

Wywołanie POST bez koszyka, z parametrami:
> ServiceID=2\
> OrderID=100\
> Amount=1.50

```text
	Hash=2ab52e6918c6ad3b69a8228a2ab815f11ad58533eeed963dd990df8d8c3709d1
```

gdzie

```text
	Hash=SHA256(“2|100|1.50|2test2”)
```

b.  Rozpoczęcie transakcji. Wywołanie POST z koszykiem.

**WSKAZÓWKA:** Opcja szczegółowo omówiona w części [Koszyk produktów](../transaction-data/product-basket.md#koszyk-produktów).

> ServiceID = 2
>
> OrderID = 100
>
> Amount = 1.50
>
> Product (opisany niżej)
>
> klucz_współdzielony = 2test2



Koszyk produktów (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<productList>
	   <product>
		  <subAmount>1.00</subAmount>
		  <params>
			 <param name="productName" value="Nazwa produktu 1" />
		  </params>
	   </product>
	   <product>
		  <subAmount>0.50</subAmount>
		  <params>
			 <param name="productType" value="ABCD" />
			 <param name="ID" value="EFGH" />
		  </params>
	   </product>
	</productList>
```
Po zakodowaniu funkcją base64 otrzymujemy wartość parametru Product:

```text
	PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz48cHJvZHVjdExpc3Q+PHByb2R1Y3Q+PHN1YkFtb3VudD4xLjAwPC9zdWJBbW91bnQ+PHBhcmFtcz48cGFyYW0gbmFtZT0icHJvZHVjdE5hbWUiIHZhbHVlPSJOYXp3YSBwcm9kdWt0dSAxIiAvPjwvcGFyYW1zPjwvcHJvZHVjdD48cHJvZHVjdD48c3ViQW1vdW50PjAuNTA8L3N1YkFtb3VudD48cGFyYW1zPjxwYXJhbSBuYW1lPSJwcm9kdWN0VHlwZSIgdmFsdWU9IkFCQ0QiIC8+PHBhcmFtIG5hbWU9IklEIiB2YWx1ZT0iRUZHSCIgLz48L3BhcmFtcz48L3Byb2R1Y3Q+PC9wcm9kdWN0TGlzdD4=
```

Wartość Hash liczona jest w następujący sposób:

```text
	Hash=SHA256(“2|100|1.50|PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz48cHJvZHVjdExpc3Q+PHByb2R1Y3Q+PHN1YkFtb3VudD4xLjAwPC9zdWJBbW91bnQ+PHBhcmFtcz48cGFyYW0gbmFtZT0icHJvZHVjdE5hbWUiIHZhbHVlPSJOYXp3YSBwcm9kdWt0dSAxIiAvPjwvcGFyYW1zPjwvcHJvZHVjdD48cHJvZHVjdD48c3ViQW1vdW50PjAuNTA8L3N1YkFtb3VudD48cGFyYW1zPjxwYXJhbSBuYW1lPSJwcm9kdWN0VHlwZSIgdmFsdWU9IkFCQ0QiIC8+PHBhcmFtIG5hbWU9IklEIiB2YWx1ZT0iRUZHSCIgLz48L3BhcmFtcz48L3Byb2R1Y3Q+PC9wcm9kdWN0TGlzdD4=|2test2”)
```


### Przykładowe obliczenia wartości funkcji skrótu podczas powrotu Klienta do Serwisu Partnera

Dane Serwisu Partnera:

> ServiceID = 2
>
> klucz_współdzielony = 2test2

```text
	<https://sklep_nazwa/strona_powrotu?ServiceID=2>&OrderID=100&Hash=254eac9980db56f425acf8a9df715cbd6f56de3c410b05f05016630f7d30a4ed
```


> gdzie
>
> *Hash=SHA256("2|100|2test2")*

### Przykładowe obliczenia wartości funkcji skrótu w komunikacie ITN

Dane Serwisu Partnera:

> serviceID = 1
>
> klucz_współdzielony = 1test1

ITN (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<transactionList>
	   <serviceID>1</serviceID>
	   <transactions>
		  <transaction>
			 <orderID>11</orderID>
			 <remoteID>91</remoteID>
			 <amount>11.11</amount>
			 <currency>PLN</currency>
			 <gatewayID>1</gatewayID>
			 <paymentDate>20010101111111</paymentDate>
			 <paymentStatus>SUCCESS</paymentStatus>
			 <paymentStatusDetails>AUTHORIZED</paymentStatusDetails>
		  </transaction>
	   </transactions>
	   <hash>a103bfe581a938e9ad78238cfc674ffafdd6ec70cb6825e7ed5c41787671efe4</hash>
	</transactionList>
```
gdzie

```text
	Hash=SHA256(“1|11|91|11.11|PLN|1|20010101111111|SUCCESS|AUTHORIZED|1test1”)
```

Przykładowa odpowiedź (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<confirmationList>
	   <serviceID>1</serviceID>
	   <transactionsConfirmations>
		  <transactionConfirmed>
			 <orderID>11</orderID>
			 <confirmation>CONFIRMED</confirmation>
		  </transactionConfirmed>
	   </transactionsConfirmations>
	   <hash>c1e9888b7d9fb988a4aae0dfbff6d8092fc9581e22e02f335367dd01058f9618</hash>
	</confirmationList>
```
gdzie wartość
>
> Hash=SHA256("1|11|CONFIRMED|1test1");

### Przykładowe obliczenia wartości funkcji skrótu w odpytaniu o listę Kanałów Płatności

Dane Serwisu Partnera:

> ServiceID = 100
>
> MessageID = 11111111111111111111111111111111
>
> Currencies = PLN,EUR
>
> Language = PL
>
> klucz_współdzielony = 1test1
>
> gdzie wartość
>
> Hash=SHA256('100|11111111111111111111111111111111|PLN,EUR|PL|1test1')

Odpowiedź na powyższe wywołanie może być następująca (uwaga: brak pola hash w odpowiedzi):

```json
{
    "result": "OK",
    "errorStatus": null,
    "description": null,
    "gatewayGroups": [
        {
            "type": "PBL",
            "title": "Przelew internetowy",
            "shortDescription": "Wybierz bank, z którego chcesz zlecić płatność",
            "description": null,
            "order": 1,
            "iconUrl": null
        },
        {
            "type": "BNPL",
            "title": "Kup teraz, zapłać później",
            "shortDescription": "Kup teraz, zapłać później",
            "description": null,
            "order": 2,
            "iconUrl": null
        }
    ],
    "serviceID": "10000",
    "messageID": "2ca19ceb5258ce0aa3bc815e80240000",
    "gatewayList": [
        {
            "gatewayID": 106,
            "name": "Płatność testowa PBL",
            "groupType": "PBL",
            "bankName": "NONE",
            "iconURL": "https://testimages.autopay.eu/pomoc/grafika/106.gif",
            "state": "OK",
            "stateDate": "2023-10-03 14:35:01",
            "description": "Płatność testowa",
            "shortDescription": null,
            "descriptionUrl": null,
            "availableFor": "BOTH",
            "requiredParams": ["Nip"],
            "mcc": {
                "allowed": [1234, 9876],
                "disallowed": [1111]
            },
            "inBalanceAllowed": true,
            "minValidityTime": null,
            "order": 1,
            "currencies": [
                {
                    "currency": "PLN",
                    "minAmount": 0.01,
                    "maxAmount": 5000.00
                }
            ],
            "buttonTitle": "Płacę"
        },
		{
            "gatewayID": 701,
            "name": "Zapłać później z Payka",
            "groupType": "BNPL",
            "bankName": "NONE",
            "iconUrl": "https://testimages.autopay.eu/pomoc/grafika/701.png",
            "state": "OK",
            "stateDate": "2023-10-03 14:37:10",
            "description": "<div class=\"payway_desc\"><h1>Dane dotyczące kosztu</h1><p>Zapłać później - jednorazowo do 45 dni (...). Szczegóły oferty na: <a href=\"https://payka.pl\" target=\"_blank\">Payka.pl</a></p></div>",
            "shortDescription": "Zapłać później - jednorazowo do 45 dni lub w kilku równych ratach",
            "descriptionUrl": null,
            "availableFor": "B2C",
            "requiredParams": [],
            "mcc": null,
            "inBalanceAllowed": false,
            "minValidityTime": 60,
            "order": 2,
            "currencies": [
                {
                    "currency": "PLN",
                    "minAmount": 49.99,
                    "maxAmount": 7000.00
                }
            ],
            "buttonTitle": "Płacę"
        }
    ]
}
```
