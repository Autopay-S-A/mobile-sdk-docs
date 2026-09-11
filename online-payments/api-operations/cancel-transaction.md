# Anulowanie transakcji



## Anulowanie nieopłaconej transakcji

### Opis anulowania nieopłaconej transakcji

Dla wszystkich serwisów możliwe jest anulowanie rozpoczętej, ale
nieopłaconej transakcji poprzez wywołanie metody *transactionCancel*
([https://{host_bramki}/webapi/transactionCancel](https://{host_bramki}/webapi/transactionCancel)) z odpowiednimi
parametrami. Wszystkie parametry przekazywane są metodą POST
(Content-Type: application/x-www-form-urlencoded).

Protokół rozróżnia wielkość liter zarówno w nazwach jak i wartościach
parametrów. Wartości przekazywanych parametrów powinny być kodowane w
UTF-8.

### Lista parametrów dostępnych dla anulowania nieopłaconej transakcji

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Wartość pola musi być unikalna dla Serwisu Partnera. |
| 3 | RemoteID | NIE | string{1,20} | Alfanumeryczny identyfikator transakcji nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. Jego podanie spowoduje anulowanie tylko jednej transakcji o wskazanym **RemoteID**, jeśli oczekuje na wpłatę (status **PENDING**). |
| 4 | OrderID | NIE | string{32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. Jego podanie (brak **RemoteID**) spowoduje anulowanie wszystkich transakcji oczekujących na wpłatę (status **PENDING**) o wskazanym **OrderID** (oraz **ServiceID**).  _**UWAGA:** Wymagane jedno z pól **OrderID** lub **RemoteID**. Podanie obu spowoduje zatrzymanie przetwarzania żądania oraz błąd http._ |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


#### Nagłówek dla anulowania nieopłaconej transakcji

Do poprawnego odpytania należy, wraz z przekazywanymi parametrami,
przesłać zdefiniowany nagłówek HTTP o odpowiedniej treści. Dołączony
nagłówek powinien nosić nazwę *\'BmHeader\'* i posiadać następującą
wartość *\'pay-bm\'*. W całości powinien prezentować się następująco:

```text
	'BmHeader: pay-bm'
```

W przypadku poprawnego komunikatu zwracany jest (w tej samej sesji HTTP)
tekst w formacie XML, zawierający potwierdzenie wykonania operacji lub
opis błędu.

Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<transaction>
		<serviceID>ServiceID</serviceID>
		<messageID>MessageID</messageID>
		<confirmation>ConfStatus</confirmation>
		<reason>Reason</reason>
		<hash>Hash</hash>
	</transaction>
```

#### Lista parametrów dla anulowania nieopłaconej transakcji

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | NIE | string{1,32} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. <BR> Wymagany dla confirmation=CONFIRMED |
| 2 | messageID | NIE | string{1,20} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. <BR> Wymagany dla confirmation=CONFIRMED |
| 3 | confirmation | TAK | string{1,100} | Status potwierdzenia przyjęcia zlecenia. <BR> Może przyjmować dwie wartości: <BR> - CONFIRMED – operacja powiodła się <BR> - NOTCONFIRMED – operacja nie powiodła się |
| 4 | reason | NIE | string{1,1000} | Wyjaśnienie szczegółów przetwarzania żądania. |
| nd. | hash | NIE | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. <BR> Wymagany dla confirmation=CONFIRMED |


#### Odpowiedzi na żądania anulowania transakcji

Jeśli komunikat jest poprawny składniowo, System zwróci jedną z
poniższych par opisujących wynik przetwarzania. Poza jej interpretacją,
zaleca się kontrolnie odpytać o status transakcji (metoda
**transactionStatus**).

Należy pamiętać, że po skutecznym anulowaniu przynajmniej jednej
transakcji, nie jest możliwe wystartowanie nowej, ani kontynuowanie
wcześniej wystartowanej transakcji o tym samym **OrderID**.


| cofnfirmation | reason | szczegóły |
| --- | --- | --- |
| CONFIRMED | CANCELED_FULLY | Dla wskazanego OrderID: wszystkie transakcje oczekujące na wpłaty zostały anulowane. <BR> Dla wskazanego RemoteID: transakcja została anulowana. |
| CONFIRMED | CANCELED_PARTIALLY | Dla wskazanego OrderID: anulowano przynajmniej jedną transakcję, jednak wystąpiły transakcje, których nie można było anulować (np. były już opłacone). <BR> Dla wskazanego RemoteID: taka odpowiedź nie występuje. |
| NOTCONFIRMED | INCORRECT_PAYMENT_STATUS | Znaleziono przynajmniej jedną wskazaną transakcję, jednak nie udało się żadnej anulować (np. nie było transakcji oczekującej na wpłatę). |
| NOTCONFIRMED | TRANSACTION_NOT_FOUND | Nie znaleziono wskazanej transakcji. |
| NOTCONFIRMED | OTHER_ERROR | Wystąpił inny błąd przy przetwarzaniu żądania. |

<a id="anulowanie-nieoplaconej-transakcji"></a>
