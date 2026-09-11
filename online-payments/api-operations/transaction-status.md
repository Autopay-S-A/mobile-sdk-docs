# Status transakcji



## Odpytanie o status transakcji

### Opis

Dla wszystkich serwisów możliwe jest odpytanie o status transakcji. W
tym celu należy wywołać metodę **transactionStatus**
([https://{host_bramki}/webapi/transactionStatus](https://{host_bramki}/webapi/transactionStatus)) z odpowiednimi parametrami.

Wszystkie parametry przekazywane są metodą POST (Content-Type:
application/x-www-form-urlencoded). Protokół rozróżnia wielkość liter
zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych
parametrów powinny być kodowane w UTF-8.



### Lista parametrów dostępnych dla statusu transakcji

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | OrderID | TAK | string{32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |


### Nagłówek HTTP dla zapytania o status transakcji

Do poprawnego odpytania należy, wraz z przekazywanymi parametrami,
przesłać zdefiniowany nagłówek HTTP o odpowiedniej treści. Dołączony
nagłówek powinien nosić nazwę *\'BmHeader\'* i posiadać następującą
wartość *\'pay-bm\'*, w całości powinien prezentować się następująco
*\'BmHeader: pay-bm\'*. W przypadku poprawnego komunikatu zwracany jest
(w tej samej sesji HTTP) tekst w formacie XML, zawierający wszystkie
transakcje o wskazanym OrderID wraz z podstawowymi o nich informacjami.

**WSKAZÓWKA:** Sytuacja taka może mieć miejsce np. w przypadku, gdy
Klient zmieni Kanał Płatności, wywoła ponownie ten sam start transakcji
z historii przeglądarki itp. System umożliwia blokowanie takich
przypadków, jednak opcja nie jest zalecana (nie byłoby możliwe opłacenie
porzuconej transakcji).

**WAŻNE!** W przypadku gdy odpytanie dotyczy orderID, które występuje w ponad 50 transakcjach danego serwisu, zwracana jest odpowiedź (XML) z
kodem błędu 403.

Struktura odpowiedzi w przypadku osiągniętego limitu transakcji z tym samym orderId:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<transaction>
    <reason>LIMIT_REQUESTED_TRANSACTIONS_WITH_THE_SAME_ORDER_ID_AND_SERVICE_ID_EXCEEDED</reason>
    <description>Transaction limit 50 with the same order id {{ORDER_ID}} and service id {{SERVICE_ID}} exceeded. Requested count {{TRANSACTION_AMOUNT}}
    </description>
</transaction>
```
### Lista pól dla zapytania o status transakcji

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera, nadawany w trakcie rejestracji usługi, jednoznacznie identyfikuje Serwis Partnera w Systemie płatności online. |
| 2 | orderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| 3 | remoteID | TAK | string{1,20} | Alfanumeryczny identyfikator transakcji nadany przez System płatności online. |
| 5 | amount | TAK | amount | Kwota transakcji jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. |
| 6 | currency | TAK | string{1,3} | Waluta transakcji. |
| 7 | gatewayID | NIE | string{1,5} | Identyfikator Kanału Płatności, za pomocą, którego Klient uregulował płatność. |
| 8 | paymentDate | TAK | string{14} | Moment zautoryzowania transakcji, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET) |
| 9 | paymentStatus | TAK | enum | Status autoryzacji transakcji, przyjmuje wartości (opis zmian statusów dalej): <BR> **PENDING** – transakcja rozpoczęta. <BR> **SUCCESS** – poprawna autoryzacja transakcji, Serwis Partnera otrzyma środki za transakcje – można wydać towar/usługę. <BR> **FAILURE** – transakcja nie została zakończona poprawnie. |
| 10 | paymentStatusDetails | NIE | string{64} | Szczegółowy status transakcji, wartość może być ignorowana przez Serwis Partnera. _**WSKAZÓWKA:** Szczegółowy opis w części [Szczegółowe statusy transakcji](../transaction-data/statuses.md#szczegółowe-statusy-transakcji)._ |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


**UWAGA:** Ponieważ metoda może zwrócić wiele transakcji, do Hash
pobierane są kolejne transakcje (zgodnie z kolejnością występowania
transakcji w odpowiedzi). W ramach danej transakcji zastosowanie ma
kolejność zgodna z numerem obok pola (z wyłączeniem parametru ServiceID,
poziom wyżej).

Przykład łańcucha funkcji skrótu
```text
		Hash = funkcja(<serviceID> + „|” +
	<orderID1> + „|” + <remoteID1> + „|” + <amount1> + „|” + <currency1> + „|” + <gatewayID1> + „|” + <paymentDate1> + „|” + <paymentStatus1> + „|” + <paymentStatusDetails1> + „|” +
	<orderID2> + „|” + <remoteID2> + „|” + <amount2> + „|” + <currency2> + „|” + <gatewayID2> + „|” + <paymentDate2> + „|” + <paymentStatus2> + „|” + <paymentStatusDetails2> + …
```

### Obsługa odpowiedzi zapytania o status transakcji - propozycja obsługi wielu transakcji w odpowiedzi


| Warunki | Znaczenie | Proponowany komunikat dla Klienta |
| --- | --- | --- |
| Dokładnie jedna transakcja o statusie paymentstatus=SUCCESS | Poprawnie opłacona transakcja. | System poprawnie zarejestrował płatność. |
| Więcej niż jedna transakcja o statusie paymentstatus=SUCCESS | Wielokrotnie opłacona transakcja. | System zarejestrował więcej niż jedną płatność. |
| Istnieje RemoteID o statusie paymentstatus=PENDING i nie istnieje o statusie paymentstatus=SUCCESS | Transakcja oczekuje na opłacenie. | System oczekuje na płatność. |
| Istnieje przynajmniej jedna transakcja, ale nie ma innych statusów niż paymentstatus=FAILURE | Transakcja anulowana. | System zarejestrował rezygnację z płatności lub brak autoryzacji płatności. |
| Brak transakcji lub inny błąd | Nieudana próba znalezienia transakcji. | Transakcja nie została odnaleziona. |
