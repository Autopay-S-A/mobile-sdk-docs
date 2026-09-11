# Zwroty

<!-- TODO MIG-037: Źródło: README-2.md:4298,4432–4461,4728–4832. Problem: Wspólne definicje zwrotów i outDetails zawierają pola lub błędy Punktów Rozliczeń. Opis Amount starszej usługi mówi o wypłacie całego salda, mimo opisu zwrotu transakcji. Wymagana decyzja/materiał: Potwierdzić semantykę Amount i samodzielny kontrakt ServiceID. Zachowano wspólne pola, błędy oraz przykłady, bez publikowania osobnej usługi Marketplace. -->

> TODO MIG-037: Wspólne definicje zwrotów i outDetails zawierają pola lub błędy Punktów Rozliczeń. Opis Amount starszej usługi mówi o wypłacie całego salda, mimo opisu zwrotu transakcji. Potwierdzić semantykę Amount i samodzielny kontrakt ServiceID. Zachowano wspólne pola, błędy oraz przykłady, bez publikowania osobnej usługi Marketplace.

## Zwroty transakcji

### Opis

Dla serwisów posiadających saldo w Systemie, możliwe jest wykonanie
operacji zwrotu do Klienta całości bądź części kwoty wpłaconej na rzecz
wskazanej transakcji. Skuteczny zwrot całości transakcji można wykonać
jeden raz (w przypadku ponownej próby zlecenia zwrotu tej samej
transakcji, System zwraca odpowiednio opisany błąd). Zwroty części kwoty
transakcji można na niej wykonywać wiele razy, o ile ich suma nie
przekroczy kwoty wpłaty.

Aby wykonać zwrot transakcji, należy wywołać metodę
**transactionRefund** ([https://{host_bramki}/settlementapi/transactionRefund](https://{host_bramki}/settlementapi/transactionRefund))
z odpowiednimi parametrami. Wszystkie parametry przekazywane są metodą
POST (Content-Type: application/x-www-form-urlencoded). Protokół
rozróżnia wielkość liter zarówno w nazwach jak i wartościach parametrów.
Wartości przekazywanych parametrów powinny być kodowane w UTF-8.

### Lista dostępnych parametrów

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna i wskazywać konkretne zlecenie wypłaty w Serwisie Partnera. Weryfikacja unikalności po stronie Systemu pozwala na ponawianie **MessageID** w przypadku problemów z komunikacją (ponowienie tej wartości skutkować będzie potwierdzeniem zlecenia, bez ponownego wykonania w Systemie). |
| 3 | RemoteID | TAK | string{1,20} | Alfanumeryczny identyfikator zwracanej transakcji wejściowej nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. |
| 4 | Amount | NIE | amount | Kwota wypłaty z salda (nie może być większa niż aktualne saldo serwisu); niepodanie tego parametru skutkuje wypłatą całości środków zgromadzonych na saldzie; jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00. <BR> W modelu Marketplace, pole musi być puste (zwrot całkowity). W przeciwnym wypadku nie byłoby możliwości wskazania punktu/ów rozliczeń, do obciążenia za taką operację. Przy zwrocie całkowitym saldo jest potrącane zgodnie z sumą kwot produktów danego punktu rozliczeń. |
| 5 | Currency | NIE | string{1,3} | Waluta wypłaty. Domyślną walutą jest PLN (użycie innej waluty musi być uzgodnione w trakcie integracji). W ramach ServiceID obsługiwana jest jedna waluta. <BR> Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |


W odpowiedzi na żądanie zwracany jest (w tej samej sesji HTTP) tekst w formacie XML, zawierający potwierdzenie wykonania operacji lub opis błędu (opis poniżej).


Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<transactionRefund>
		<serviceID>ServiceID</serviceID>
		<messageID>MessageID</messageID>
		<hash>Hash</hash>
	</transactionRefund>
```

### Opis pól

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. |
| 2 | messageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |


Opcja wykonywania zwrotów do opłaconych transakcji możliwa jest do 12
miesięcy wstecz, licząc od daty rozpoczęcia transakcji. Wyjątek stanowią
płatności BLIK, które ze względu na ograniczenia czasowe po stronie
dostawcy Kanału płatności, można zwracać do 6 miesięcy wstecz.

Powyższe terminy dotyczą realizowania zwrotów poprzez panel
administracyjny oraz **transactionRefund**, np. za pośrednictwem
własnych narzędzi administracyjnych. Po ich przekroczeniu zostanie
zwrócony błąd (**TRANSACTION_TOO_OLD_TO_REFUND**).
Schemat działania jest analogiczny jak w przypadku wypłat z salda. To znaczy, że System przyjmuje zlecenie i asynchronicznie przetwarza je w ciągu maksymalnie 30 minut, a w przypadku niepowodzenia informacja o operacjach zakończonych błędem jest wysyłana w raporcie następnego dnia roboczego.


W przypadku problemów z połączeniem, przekroczenia maksymalnego czasu oczekiwania na odpowiedź, żądanie może zostać ponowione z tym samym MessageID bez obawy o zduplikowanie zlecenia.
Za błąd uznać można wszystkie odpowiedzi inne niż założona (tj. z niepoprawnymi
polami, w szczególności pustym lub niepoprawnym
**hash**). W przypadku, gdy System zautoryzuje nadawcę komunikatu
zwrotu, jednak nie uda się wykonać operacji, w odpowiedzi zwrócony
zostanie komunikat błędu.

### Zwrot transakcji V3

Aby wykonać zwrot transakcji z możliwością zwrotu częściowego, należy wywołać metodę **transactionRefund/v3** ([https://{host_bramki}/settlementapi/transactionRefund/v3](https://{host_bramki}/settlementapi/transactionRefund/v3))
z odpowiednimi parametrami. Wszystkie parametry przekazywane są metodą
POST (Content-Type: application/json). Protokół
rozróżnia wielkość liter zarówno w nazwach jak i wartościach parametrów.
Wartości przekazywanych parametrów powinny być kodowane w UTF-8.

### Lista dostępnych parametrów

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceId | TAK | number | Identyfikator Serwisu Partnera. |
| 2 | messageId | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna i wskazywać konkretne zlecenie zwrotu w Serwisie Partnera. Weryfikacja unikalności po stronie Systemu pozwala na ponawianie messageId w przypadku problemów z komunikacją (ponowienie tej wartości skutkować będzie potwierdzeniem zlecenia, bez ponownego wykonania w Systemie). |
| 3 | remoteId | TAK | string{1,20} | Alfanumeryczny identyfikator zwracanej transakcji wejściowej nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. |
| 4 | amount | NIE | amount | Kwota zwrotu (nie może być większa niż kwota transakcji wejściowej); niepodanie tego parametru skutkuje zwrotem całości środków transakcji; jako separator dziesiętny używana jest kropka - '.' Format: 0.00. <BR> W modelu z productDetails, pole amount musi być równe sumie kwot poszczególnych produktów. |
| 5 | currency | NIE | string{1,3} | Waluta zwrotu. Domyślną walutą jest PLN (użycie innej waluty musi być uzgodnione w trakcie integracji). W ramach serviceId obsługiwana jest jedna waluta. <BR> Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD. |
| 6 | productDetails | NIE | array | Do wyliczenia hash brany jest po uwagę tylko rozmiar listy (ilość elementów). Lista szczegółów produktów do zwrotu. Jeśli podana jest kwota (amount), lista productDetails jest wymagana, a suma kwot produktów musi być równa kwocie amount. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |

### Struktura obiektu productDetails

| nazwa | wymagany | typ | opis |
| --- | --- | --- | --- |
| productId | TAK | string | Unikalny identyfikator (ten sam parametr który został przekazany przy starcie transakcji w koszyku produktów o nazwie productID) |
| amount | TAK | amount | Kwota zwrotu dla danego produktu. Musi być większa od 0. Format: 0.00. |

Przykład żądania (JSON)
```json
	{
		"serviceId": 12345,
		"messageId": "39778007a89e428bae8ab855085f3967",
		"remoteId": "AB87363T5",
		"amount": 34.21,
		"currency": "PLN",
		"productDetails": [
			{
				"productId": "p1",
				"amount": 24.21
			},
			{
				"productId": "p2",
				"amount": 10.00
			}
		],
		"hash": "a1b2c3d4e5f6..."
	}
```

W odpowiedzi na żądanie zwracany jest (w tej samej sesji HTTP) tekst w formacie JSON, zawierający potwierdzenie wykonania operacji lub opis błędu (opis poniżej).


Struktura potwierdzenia (JSON) status HTTP 200
```json
	{
		"serviceId": 12345,
		"messageId": "39778007a89e428bae8ab855085f3967",
		"hash": "a1b2c3d4e5f6..."
	}
```

### Opis pól odpowiedzi

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceId | TAK | number | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. |
| 2 | messageId | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |

Struktura komunikatu błędu (JSON) status HTTP 200
```json
	{
		"errorStatus": "TRANSACTION_NOT_FOUND",
		"errorStatusCode": -10,
		"errorMessages": "Transaction not found"
	}
```

### Lista możliwych błędów

| errorStatus | errorStatusCode | opis |
| --- | --- | --- |
| PRODUCT_PARAM_NOT_FOUND | -21 | Nie znaleziono parametru produktu. |
| USER_NOT_FOUND | -22 | Nie znaleziono użytkownika. |
| SETTLEMENT_CONF_NOT_FOUND | -14 | Nie znaleziono konfiguracji rozliczenia. |
| TRANSACTION_NOT_FOUND | -10 | Nie znaleziono transakcji. |
| SERVICE_NOT_FOUND | -3 | Nie znaleziono serwisu. |
| BALANCE_POINT_NOT_FOUND | -1 | Nie znaleziono punktu rozliczeń. |
| INVALID_FORMAT | 2 | Nieprawidłowy format danych. |
| INVALID_HASH | 7 | Nieprawidłowa wartość Hash. |
| VALIDATION_FAILED | 49 | Błąd walidacji parametrów wejściowych. |
| TRANSACTION_NOT_IN_REFUNDABLE_STATUS | 54 | Transakcja nie jest w statusie umożliwiającym zwrot. |
| EXTERNAL_REFUND_ERROR | 55 | Błąd zewnętrznego systemu zwrotu. |
| ON_DEMAND_ERROR | 57 | Błąd przetwarzania na żądanie. |
| MESSAGE_ID_NOT_UNIQUE | 76 | MessageId nie jest unikalne. |
| BALANCE_DISABLED | 107 | Saldo wyłączone. |
| PARTNER_DISABLED | 108 | Partner wyłączony. |
| TRANSACTION_TOO_OLD_TO_REFUND | 156 | Transakcja zbyt stara do zwrotu. |
| TEMPORARY_DISABLED | 157 | Tymczasowo wyłączone. |
| SCA_FAILED | 158 | Błąd uwierzytelniania SCA. |
| OTHER_ERROR | 200 | Inny błąd. |
| INCORRECT_AMOUNT_OF_PRODUCT_DETAILS | 201 | Suma kwot produktów niezgodna z kwotą amount. |
| PRODUCT_ALREADY_REFUNDED | 202 | Produkt został już zwrócony. |
| BALANCE_GET_ERROR | 3 | Błąd pobierania salda. |
| INCORRECT_AMOUNT_OF_BALANCE_POINTS_DETAILS | 203 | Nieprawidłowa kwota punktów rozliczeń. |
| BALANCE_POINT_ERROR | 204 | Błąd punktu rozliczeń. |
| PARTIAL_TRANSACTION_REFUNDS_FORBIDDEN | 209 | Częściowe zwroty transakcji zabronione. |
| BALANCE_POINT_DETAILS_ERROR | 210 | Błąd szczegółów punktu rozliczeń. |

Opcja wykonywania zwrotów do opłaconych transakcji możliwa jest do 12
miesięcy wstecz, licząc od daty rozpoczęcia transakcji. Wyjątek stanowią
płatności BLIK, które ze względu na ograniczenia czasowe po stronie
dostawcy Kanału płatności, można zwracać do 6 miesięcy wstecz.

Powyższe terminy dotyczą realizowania zwrotów poprzez panel
administracyjny oraz **transactionRefund/v3**, np. za pośrednictwem
własnych narzędzi administracyjnych. Po ich przekroczeniu zostanie
zwrócony błąd (**TRANSACTION_TOO_OLD_TO_REFUND**).
Schemat działania jest analogiczny jak w przypadku wypłat z salda. To znaczy, że System przyjmuje zlecenie i asynchronicznie przetwarza je w ciągu maksymalnie 30 minut, a w przypadku niepowodzenia informacja o operacjach zakończonych błędem jest wysyłana w raporcie następnego dnia roboczego.


W przypadku problemów z połączeniem, przekroczenia maksymalnego czasu oczekiwania na odpowiedź, żądanie może zostać ponowione z tym samym MessageID bez obawy o zduplikowanie zlecenia.
Za błąd uznać można wszystkie odpowiedzi inne niż założona (tj. z niepoprawnymi
polami, w szczególności pustym lub niepoprawnym
**hash**). W przypadku, gdy System zautoryzuje nadawcę komunikatu
zwrotu, jednak nie uda się wykonać operacji, w odpowiedzi zwrócony
zostanie komunikat błędu.

## Zwroty produktu

### Opis

Dla serwisów posiadających saldo w Systemie oraz podających w koszyku
produktów parametr **productID**, możliwe jest wykonanie operacji zwrotu
do Klienta całości, bądź części kwoty wpłaconej na rzecz wskazanego
produktu. Skuteczny zwrot całości kwoty produktu można wykonać jeden raz
(w przypadku ponownej próby zlecenia zwrotu tego samego produktu, System
zwraca odpowiednio opisany błąd). Zwroty części kwoty produktu można na
nim wykonywać wiele razy, o ile ich suma nie przekroczy kwoty wpłaconej
na rzecz produktu.

Aby wykonać zwrot produktu, należy wywołać metodę **productRefund**
([https://{host_bramki}/settlementapi/productRefund](https://{host_bramki}/settlementapi/productRefund)) z odpowiednimi
parametrami. Wszystkie parametry przekazywane są metodą POST
(Content-Type: application/x-www-form-urlencoded). Protokół rozróżnia
wielkość liter zarówno w nazwach jak i wartościach parametrów. Wartości
przekazywanych parametrów powinny być kodowane w UTF-8.

### Lista parametrów

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna i wskazywać konkretne zlecenie wypłaty w Serwisie Partnera. Weryfikacja unikalności po stronie Systemu pozwala na ponawianie **MessageID** w przypadku problemów z komunikacją (ponowienie tej wartości skutkować będzie potwierdzeniem zlecenia, bez ponownego wykonania w Systemie). |
| 3 | RemoteID | TAK | string{1,20} | Alfanumeryczny identyfikator zwracanej transakcji wejściowej nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. |
| 4 | ProductID | TAK | string{1,36} | Identyfikator zwracanego produktu. |
| 5 | Amount | NIE | amount | Kwota zwrotu (nie może być większa niż kwota produktu oraz aktualne saldo serwisu + ew. kwota prowizji za zwrot). Niepodanie tego parametru skutkuje zwrotem do Klienta całości środków wpłaconych na rzecz zwracanego produktu; jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00. |
| 6 | Currency | NIE | string{1,3} | Waluta wypłaty. Domyślną walutą jest PLN (użycie innej waluty musi być uzgodnione w trakcie integracji). W ramach ServiceID obsługiwana jest jedna waluta. <BR> Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |


### Odpowiedź na żądanie

W odpowiedzi na żądanie zwracany jest (w tej samej sesji HTTP) tekst w
formacie XML, zawierający potwierdzenie wykonania operacji lub opis
błędu (opis poniżej).


Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<productRefund>
		<serviceID>ServiceID</serviceID>
		<messageID>MessageID</messageID>
		<hash>Hash</hash>
	</productRefund>
```

### Opis pól

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. |
| 2 | messageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |


Opcja wykonywania zwrotów do opłaconych transakcji możliwa jest do 12
miesięcy wstecz, licząc od daty rozpoczęcia transakcji. Wyjątek stanowią
płatności BLIK, które ze względu na ograniczenia czasowe po stronie
dostawcy Kanału płatności, można zwracać do 6 miesięcy wstecz.

Powyższe terminy dotyczą realizowania zwrotów poprzez panel
administracyjny oraz **productRefund**, np. za pośrednictwem własnych
narzędzi administracyjnych. Po ich przekroczeniu zostanie zwrócony błąd
(**TRANSACTION_TOO_OLD_TO_REFUND**).
Schemat działania jest analogiczny jak w przypadku wypłat z salda. To znaczy, że System przyjmuje zlecenie i asynchronicznie przetwarza je w ciągu maksymalnie 30 minut, a w przypadku niepowodzenia informacja o operacjach zakończonych błędem jest wysyłana w raporcie następnego dnia roboczego.

W przypadku problemów z połączeniem, przekroczenia maksymalnego czasu oczekiwania na odpowiedź, żądanie może zostać ponowione z tym samym MessageID bez obawy o zduplikowanie zlecenia.
Za błąd uznać można wszystkie odpowiedzi inne niż założona (tj. z niepoprawnymi
polami, w szczególności pustym lub niepoprawnym
**hash**). W przypadku, gdy System zautoryzuje nadawcę komunikatu
zwrotu, jednak nie uda się wykonać operacji, w odpowiedzi zwrócony
zostanie komunikat błędu (Zob. [Komunikaty błędu](../../additional-information/errors.md#komunikaty-błędu)).

## Odpytanie o status zwrotu lub wypłaty z salda

### Opis
Po wykonaniu zwrotu lub wypłaty z salda mamy możliwość weryfikacji w jakim statusie jest transakcja rozliczeniowa oraz
jaki został jej nadany identyfikator przez System płatności online. W tym celu należy wywołać metodę *outDetailsV2*
([https://{host_bramki}/settlementapi/outDetails/v2](https://{host_bramki}/settlementapi/outDetails/v2)) z odpowiednimi
parametrami.

Wszystkie parametry przekazywane są metodą POST (Content-Type:
application/json). Protokół rozróżnia wielkość liter
zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych
parametrów powinny być kodowane w UTF-8.


### Lista dostępnych parametrów dla wypłat z salda

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

**UWAGA:** Wymagane jedno z pól **serviceId** lub **balancePointId**. <BR> Podanie obu spowoduje zatrzymanie przetwarzania żądania oraz błąd http.

### Przykład żądania

Struktura żądania (JSON)
```javascript
{
  "serviceId": long,
  "balancePointId": long,
  "messageId": "string",
  "method": "string", // BALANCE_PAYOFF, TRANSACTION_REFUND, PRODUCT_REFUND
  "hash": "string"
}
```

Przykładowe żądanie (JSON)
```json
{
  "serviceId": 101773,
  "messageId": "817684a6294c43ab8b375bae217a5592",
  "method": "TRANSACTION_REFUND",
  "hash": "******"
}
```

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceId | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 1 | balancePointId | TAK | string{1,10} | Identyfikator Punktu Rozliczeń. |
| 2 | messageId | TAK | string{32} | Należy podać ten sam identyfikator komunikatu, który był wysłany poprzednio przy zleceniu zwrotu lub wypłaty z salda. |
| 3 | method | TAK | enum | Operacja dla której powstała transakcja rozliczeniowa: <br/>  [**BALANCE_PAYOFF** - wypłata z salda](payouts.md#wypłaty-z-salda) <br/>  [**TRANSACTION_REFUND** - zwrot transakcji](#zwroty-transakcji) <br/> [**PRODUCT_REFUND** - zwrot produktu](#zwroty-produktu) |
| nd. | hash | TAK | string{1,128} | Stosowany jest klucz współdzielony przypisany do zastosowanego identyfikatora konfiguracji (Serwisu lub Punktu Rozliczeń). Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |

### Odpowiedź na żądanie

Struktura potwierdzenia (JSON)
```javascript
{
  "serviceId": long,
  "balancePointId":long,
  "messageId": "string",
  "status": "NEW",
  "remoteOutId": "string",
  "hash": "string",
  "errorStatus": "string",
  "errorStatusCode": int,
  "errorMessages": "string"
}
```

Przykładowe potwierdzenie (JSON)
```json
{
  "serviceId": 101773,
  "balancePointId": null,
  "messageId": "817684a6294c43ab8b375bae217a5592",
  "status": "DONE",
  "remoteOutId": "ZWTR_6063048773",
  "hash": "*******",
  "errorStatus": null,
  "errorStatusCode": null,
  "errorMessages": null
}
```

## Opis pól

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceId | TAK | string{1,10} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. |
| 1 | balancePointId | TAK | string{1,10} | Identyfikator Punktu Rozliczeń. Pochodzi z żądania metody. _**UWAGA:** Zwrócone zostanie jedno z pól **ServiceID** lub **BalancePointID**._ |
| 2 | messageId | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| 3 | status | TAK | enum | Status przetwarzania: <br /> **NEW** - transakcja zapisana w systemie. <br /> **WAITING_FOR_TRANSFER_DATA** - transakcja czeka na dane do przelewu (tylko dla niektórych kanałów płatności).  <br /> **COMMISSIONS_CALCULATED** - transakcja w procesie wyliczania prowizji o ile są takie zdefiniowane, według umowy z klientem. Jeśli nie ma konfiguracji ten krok jest pomijany. <br />  **CANCELED_AMOUNT_EXCEEDS_TRANSACTION** - transakcja anulowana, gdy kwota zwrotów przekracza kwotę transakcji. Może się do wydarzyć w dwóch przypadkach: <br /> - dla zwrotów całości transakcji, gdy klient w jednym momencie wykona kilka zwrotów tej samej transakcji z różnymi messageId <br /> - dla zwrotów częściowych, gdy klient w jednym momencie wykona wiele zwrotów częściowych i ich suma przekracza kwotę zwracanej transakcji <br /> Ze względu na asynchroniczną walidację, ten błąd może wystąpić w wyniku błędnej integracji <br /> **FAILED_ATTEMPT_OF_BALANCE_CHANGE**  <br /> - brak środków na saldzie, klient próbuje ponowić ściągnięcie środków z salda przez 30 minut. <br /> **CANCELED_NO_FUNDS_ON_BALANCE** <br /> - gdy przez 30 minut nie udało się ściągnąć środków z salda transakcja zostaje anulowane ( klienta ma możliwość ponowić wykonanie zwrotu z nowym messageId lub serwisowo po kontakcie z supportem jest możliwość po naszej stronie ręcznego ponowienia takiej transakcji). Jeśli transakcja nie została anulowana, to przy próbuje ponowienie jej z nowym messageId, request zostanie odbity z komunikatem że transakcja została zwrócona. <br /> **CANCELED_MANUALLY_BY_SERVICES** <br /> - transakcja anulowane ręcznie przez obsługę - można anulować ręcznie transakcje tylko wtedy gdy jeszcze nie zostały pobrane środki z salda. <br /> **PROCESSING** <br /> - transakcja przekazane do wykonania przelewu <br /> **DONE** <br /> - potwierdzone dokonanie przelewu (środki fizycznie wyszły) |
| 4 | remoteOutId | NIE | string{1,20} | Alfanumeryczny identyfikator transakcji rozliczeniowej nadany przez System płatności online.   <br /> Uzupełniany tylko dla statusu **DONE** |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |
| nd. | errorStatus | NIE | string{1,128} | Status błędu, wypełniany w przypadku błędu. W przeciwnym wypadku null. |
| nd. | errorStatusCode | NIE | integer{1,3} | Kod statusu błędu, wypełniany w przypadku błędu. W przeciwnym wypadku null. |
| nd. | errorMessages | NIE | string{1,128} | Komunikat błędu, wypełniany w przypadku błędu. W przeciwnym wypadku null. |
