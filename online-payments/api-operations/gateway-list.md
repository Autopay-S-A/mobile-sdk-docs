# Lista metod płatności – gatewayList

<!-- TODO MIG-031: Źródło: README-2.md:3740–3890. Problem: Tabela używa gatewayID i iconUrl; przykład ma także id i iconURL. Opisy currencies wspominają Hash odpowiedzi, choć przykład go nie zawiera. Wymagana decyzja/materiał: Potwierdzić nazwy pól i zakres podpisywania odpowiedzi v3. -->

> TODO MIG-031: Tabela używa gatewayID i iconUrl; przykład ma także id i iconURL. Opisy currencies wspominają Hash odpowiedzi, choć przykład go nie zawiera. Potwierdzić nazwy pól i zakres podpisywania odpowiedzi v3.

## Odpytywanie o listę aktualnie dostępnych Kanałów Płatności

### Opis

Aby zbudować w Serwisie widok wyboru metody płatności, System umożliwia zdalne odpytanie o aktualną listę kanałów płatności.
W tym celu należy wywołać metodę **gatewayList** ([https://{host_bramki}/gatewayList/v3](https://{host_bramki}/gatewayList/v3)) z
odpowiednimi parametrami (w formacie JSON). Wszystkie parametry przekazywane są za pomocą technologii REST. Protokół rozróżnia wielkość
liter zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych parametrów powinny być kodowane w UTF-8.

### Lista dostępnych parametrów

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | integer | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Wartość pola musi być unikalna dla Serwisu Partnera. |
| 3 | Currencies | TAK | string{0,1000} | Lista walut, których lista dostępnych kanałów ma być zwrócona. <BR> Lista powinna być minimum jednoelementowa. Dopuszczalne są wartości: PLN, EUR, GBP, USD. |
| 4 | Language | TAK | string{2} | Język w jakim będą zwrócone opisy metod płatnosci.<br />Dopuszczalne wartości: PL,EN,DE,FR,IT,ES,CS,RO,SK,HU,UK,EL,HR,SL,TR,BG. |
| 5 | Hash | TAK | string{64} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#przykładowe-obliczenia-wartości-funkcji-skrótu-w-odpytaniu-o-listę-kanałów-płatności) |

Przykładowy komunikat

```json
{
	"ServiceID": 47498,
	"MessageID": "11111111111111111111111111111111",
	"Currencies":"PLN,EUR",
	"Language": "PL",
	"Hash": "306519f632e53a5e662de0125da7ac3f8135c7e4080900f2b145d4b25ff1b55d"
}
```

### Odpowiedź na żądanie

W odpowiedzi na żądanie, w tej samej sesji HTTP, zwracane są 2 listy z definicjami:

a)  Kanałów płatności (węzeł `gatewayList`)

b)  Grup płatności (węzeł `gatewayGroups`)

Poniżej szczegółowy opis zwracanego komunikatu:

|  | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | result | TAK | string{1,5} | Status odpowiedzi. <BR> Dopuszczalne wartości: <BR> - OK <BR> - ERROR |
| 2 | errorStatus | TAK | string{1,100} | Status błędu, wypełniany w przypadku błędu (w przeciwnym wypadku null). |
| 3 | description | TAK | string{1,500} | Opis błędu, wypełniany w przypadku błędu (w przeciwnym wypadku null). |
| 4 | gatewayGroups | TAK | list | Lista zawierająca grupy płatności. |
| 4.1 | type | TAK | string{1,20} | Typ grupy płatności. Każda definicja płatności przypisana jest do jednego z typów. |
| 4.2 | title | TAK | string{1,50} | Nazwa grupy płatności. |
| 4.3 | shortDescription | NIE | string{1,200} | Krótki opis grupy płatności. |
| 4.4 | description | NIE | string{1,1000} | Szczegółowy opis grupy płatności. |
| 4.5 | order | TAK | integer | Rekomendowana kolejność wyświetlania grup płatności. |
| 4.6 | iconUrl | NIE | string{1,100} | Adres, z którego można pobrać logotyp grupy płatności. |
| 5 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera; pochodzi z żądania metody. |
| 6 | messageID | TAK | string{32} | Identyfikator komunikatu pochodzący z żądania metody. |
| 7 | gatewayList | NIE | list | Lista zawierająca kolejne kanały płatności (pusta w przypadku braku skonfigurowanych kanałów płatności). |
| 7.1 | gatewayID | TAK | integer{1,5} | Identyfikator Kanału Płatności, za pomocą którego Klient może uregulować płatność. |
| 7.2 | name | TAK | string{1,200} | Nazwa Kanału Płatności, którą można wyświetlić na liście dostępnych banków. |
| 7.3 | groupType | NIE | string{1,30} | Typ, służący do grupowania Kanałów Płatności na ich liście. <BR> Parametr przyjmuje wartości z węzła `gatewayGroups`. |
| 7.4 | bankName | NIE | string{1,32} | Nazwa banku. |
| 7.5 | iconUrl | NIE | string{1,100} | Adres, z którego można pobrać logotyp Kanału Płatności. |
| 7.6 | state | TAK | string{1,64} | Informacja o stanie dostępności kanału. <BR> Przyjmuje wartości: <BR> - OK – kanał dostępny <BR> - TEMPORARY_DISABLED – kanał chwilowo niedostępny (np. z powodu prac po stronie banku) <BR> - DISABLED – kanał niedostępny (usługa zawieszona na dłuższy okres) |
| 7.7 | stateDate | NIE | string{1,19} | Moment ostatniej aktualizacji statusu Kanału Płatności; przykładowa wartość: 2023-08-28 00:00:01. (Czas CET) |
| 7.8 | shortDescription | NIE | string{1,200} | Opcjonalne pole zawierające krótki opis kanału płatności. Można wyświetlić po jego zaznaczeniu. |
| 7.9 | description | NIE | string{1,1000} | Opcjonalne pole opisujące szczegółowo kanał płatności (może być z użyciem znaczników HTML). |
| 7.10 | descriptionUrl | NIE | string{1,200} | Opcjonalne pole zawirające link do zewnętrznej strony opisującej szczegółowo kanał płatności. |
| 7.11 | availableFor | TAK | string{2,10} | Wartość tego pola wskazuje dla jakiego Klienta przeznaczony jest kanał płatności:<br />`B2C` - metoda płatności przeznaczona dla osób fizycznych <br />`B2B` - metoda płatności dla firm <br/>`BOTH` - metoda płatności dla wszystkich klientów.<br/> Na podstawie tego parametru należy decydować czy kanał płatności powinien być zaprezentowany Klientowi. |
| 7.12 | requiredParams | NIE | lista | Lista parametrów wymaganych przy wybraniu danej metody płatności. Dla przykładu, start transakcji dla metody płatności z grupy B2B powinien zawierać parametr `Nip`. Wymagane parametry opisane są w sekcji: [Rozpoczęcie transakcji z dodatkowymi parametrami](../transaction-data/additional-parameters.md#rozpoczęcie-transakcji-z-dodatkowymi-parametrami).<br />Aktualnie takimi parametrami mogą być: `Nip` oraz `AccountHolderName`. |
| 7.13 | mcc | NIE | object | Merchant Category Code. Węzeł opcjonalny, dodatkowo konfigurowalny. W szczególnych przypadkach, dla serwisów zawierających produkty z różnych kategorii możemy zwracać listę dozwolonych i zabronionych kodów MCC tak by Merchant po swojej stronie mógł zdecydować czy metodę płatności może zaprezentować czy nie. |
| 7.13.1 | allowed | NIE | list | Lista dozwolonych kodów MCC. |
| 7.13.2 | disallowed | NIE | list | Lista zabronionych kodów MCC. |
| 7.14 | inBalanceAllowed | NIE | boolean | Informacja czy kanał może być użyty (po uzgodnieniach biznesowych) do zasilania salda przedpłaconego (start transakcji z użyciem parametru TransactionSettlementMode=NONE). |
| 7.15 | minValidityTime | NIE | integer | Minimalny czas ważności transakcji w minutach. Pojawia się dla kanałów gdzie ustalenie statusu płatności trwa dłużej niż zwykle. |
| 7.16 | order | TAK | integer | Rekomendowana kolejność wyświetlania metody płatności. |
| 7.17 | currencies | TAK | lista | Lista zawierająca waluty dostępne dla kanału płatności, wraz z ograniczeniami kwot. |
| 7.17.1 | currency | TAK | string{3} | Waluta, którą można opłacić tym kanałem. W przypadku dostępności dla danego kanału płatności w wielu walutach, lista będzie zawierać więcej niż jeden element. <BR> Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD. <BR> Do liczenia wartości Hash pobierane są wartości kolejnych węzłów **currencies**. |
| 7.17.2 | minAmount | NIE | amount | Minimalna kwota transakcji, którą można opłacić tym kanałem. Jako separator dziesiętny używana jest kropka - '.' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. Pole występuje tylko dla niektórych kanałów, wartość jest wyrażona w walucie pola **currency**. <BR> Do liczenia wartości Hash pobierane są wartości kolejnych węzłów **currencies**. |
| 7.17.3 | maxAmount | NIE | amount | Maksymalna kwota transakcji, którą można opłacić tym kanałem. Jako separator dziesiętny używana jest kropka - '.' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. Pole występuje tylko dla niektórych kanałów, wartość jest wyrażona w walucie pola **currency**. <BR> Do liczenia wartości Hash pobierane są wartości kolejnych węzłów **currencies**. |
| 7.18 | buttonTitle | TAK | string | Sugerowany komunikat jaki powinien zaprezentować na przycisku "zapłać" po wyborze kanału płatności. |

Przykładowa odpowiedź

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
            "type": "FR",
            "title": "Dane do przelewu",
            "shortDescription": "Zleć przelew wykorzystując podane dane",
            "description": null,
            "order": 2,
            "iconUrl": null
        },
        {
            "type": "BNPL",
            "title": "Kup teraz, zapłać później",
            "shortDescription": "Kup teraz, zapłać później",
            "description": null,
            "order": 3,
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
            "gatewayID": 9,
            "name": "Przelew z innego banku",
            "groupType": "FR",
            "bankName": "BANK TEST",
            "iconURL": "https://testimages.autopay.eu/pomoc/grafika/9.gif",
            "state": "OK",
            "stateDate": "2023-10-03 14:35:02",
            "description": "<b>Szybki przelew</b>",
            "shortDescription": "Szybki przelew",
            "descriptionUrl": null,
            "availableFor": "BOTH",
            "requiredParams": [],
            "mcc": null,
            "inBalanceAllowed": true,
            "minValidityTime": null,
            "order": 2,
            "currencies": [
                {
                    "currency": "PLN"
                }
            ],
            "buttonTitle": "Wygeneruj dane do przelewu"
        },
		{
            "id": 701,
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
            "order": 3,
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

**UWAGA:** Wynik odpytania metody powinno się zapisywać i odświeżać co minutę celem zbadania dostępności kanału.
W przypadku braku lub nieprawidłowej odpowiedzi, należy wyświetlić ostatnią znaną i poprawną konfigurację
Kanałów Płatności. Jest to drugi powód do przechowywania tymczasowej kopii **gatewayList** w Serwisie Partnera.
Jako nieprawidłową odpowiedź należy traktować pustą odpowiedź, timeout, bądź pustą listę węzłów **gatewayGroups** czy **gatewayList**.
