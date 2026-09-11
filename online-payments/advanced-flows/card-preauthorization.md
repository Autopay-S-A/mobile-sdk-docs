# Preautoryzacja kartowa

<!-- TODO MIG-033: Źródło: README-2.md:1135,1150–1184. Problem: Products zawiera idBalancePoint we wspólnej definicji. Przykład odpowiedzi balancePayoff nie odpowiada tabeli confirmation/reason. Wymagana decyzja/materiał: Potwierdzić kontrakt transactionClear i zakres koszyka; zachowano wspólną definicję bez usuwania pól. -->

> TODO MIG-033: Products zawiera idBalancePoint we wspólnej definicji. Przykład odpowiedzi balancePayoff nie odpowiada tabeli confirmation/reason. Potwierdzić kontrakt transactionClear i zakres koszyka; zachowano wspólną definicję bez usuwania pól.

## Preautoryzacja kartowa

### Opis ogólny działania usługi preautoryzacji kartowej

Preautoryzacja kartowa polega na blokowaniu środków na karcie Klienta na pewien (np. z góry ustalony w trakcie zakładania blokady) czas, a następnie dokonaniu obciążenia na kwotę preautoryzacji lub kwotę niższą.
Możliwa jest też sytuacja, w której blokada jest zdejmowana bez potrącania jakiejkolwiek kwoty (np. usługa na rzecz klienta nie została wykonana ).

Operacje realizowane przez API Autopay:
- preautoryzacja (blokada środków)
- obciążenie (transactionClear)
- zwolnienie blokady (transactionCancel)

Po wykonaniu preautoryzacji (założeniu blokady) możliwe są 3 scenariusze:
- obciążenie na pełną kwotę preautoryzacji
- obciążenie na kwotę mniejszą niż kwota preautoryzacji
- całkowite zwolnienie blokady (brak obciążenia)


### Etapy transakcji preautoryzacji kartowej

### Założenie blokady na wniosek Partnera

Można wyróżnić 3 podstawowe sposoby zakładania blokady na karcie:

a) Założenie blokady podczas autoryzacji płatności jednorazowej (Patrz [Schemat A dla Preautoryzacji](#schemat-a-dla-preautoryzacji-zakładanie-blokady-podczas-autoryzacji-płatności-jednorazowej)).
Podstawowe parametry startowe:
- kartowy kanał płatności (**GatewayID=1500 / 1512 / 1513 / 1523**) oraz
- chęć zabezpieczenia środków, zamiast obciążenia (**Hold=true**)

b)  Założenie blokady podczas inicjowania płatności automatycznej (zapisywania karty w Serwisie lub Aplikacji Mobilnej) (Patrz [Schemat B dla Preautoryzacji](#schemat-b-dla-preautoryzacji-zakładanie-blokady-podczas-inicjowania-płatności-automatycznej-zapisywania-karty)).

> **UWAGA:** Dla preautoryzacji wysyłka komunikatu RPAN wraz z ClientHash  jest uruchamiana dopiero wraz z wywołaniem usługi transactionClear. Jeżeli dla transakcji inicjującej (Hold=true) nie zostanie wywołane obciążenie (usługa transactionClear) lub preautoryzacja zostanie anulowana (usługa transactionCancel) - karta nie zostanie zapamiętana, gdyż transakcja nie doszła do skutku (nie przyjdzie komunikat RPAN z polem clientHash).

Podstawowe parametry startowe:

- kanał płatności (**GatewayID=1503**),
- fakt zaakceptowania regulaminu usługi płatności automatycznej dostarczonego przez AP (**RecurringAcceptanceState=ACCEPTED**, lub po ustaleniach biznesowych wartości **PROMPT/FORCE**)
- wybór inicjalizacji płatności automatycznej wraz z potencjalnym obciążeniem karty (**RecurringAction=INIT_WITH_PAYMENT**)
- chęć zabezpieczenia środków, zamiast obciążenia (**Hold=true**)

c)  Załozenie blokady z wykorzystaniem wcześniej zapisanej karty (Patrz [Schemat C dla Preautoryzacji](#schemat-c-dla-preautoryzacji-zakładanie-blokady-z-wykorzystaniem-wcześniej-zapisanej-karty)).

Podstawowe parametry startowe:

- kanał płatności (**GatewayID=1503**)
- wskazanie wcześniej dodanej karty (**ClientHash** pochodzący z RPAN)
- wybór metody płatności automatycznej (**RecurringAction=MANUAL/AUTO**)
- chęć zabezpieczenia środków, zamiast obciążenia (**Hold=true**)

Każda z tych metod zakładania blokady skutkuje komunikatem ITN, którego status wskazuje wynik autoryzacji transakcji. Poza standardowymi statusami, w wypadku blokowania środków, System dostarcza w ITN status **paymentStatus=ON_HOLD**, który stanowi potwierdzenie założenia blokady środków na karcie Klienta. Ponadto ITN będzie standardowo zawierać globalny identyfikator transakcji (**remoteID**), który będzie niezbędny do późniejszego obciążenia założonej blokady.
Komunikat ITN jest jedyną wiążącą informacją o zmianie statusu transakcji oraz (stosowany wraz z usługą transactionStatus; patrz część [Odpytanie o status transakcji](../api-operations/transaction-status.md#odpytanie-o-status-transakcji)), pomaga obsłużyć transakcję nie zrywając sesji z użytkownikiem (nawet w przypadku różnych problemów sieciowych). Synchroniczne potwierdzenia operacji (węzeł confirmation, służą jedynie do prezentowania wstępnej informacji o zleceniu).

### Czas ważności preautoryzacji
Obciążenie lub zwolnienie blokady musi nastąpić w terminach zdefiniowanych przez Międzynarodowe Organizacje Płatnicze:
- Karta VISA (transakcja jednorazowa) nie dłużej niż 10 dni
- Karta VISA, transakcje cykliczne typu Merchant Initiated Transactions (kanał 1503 oraz recurringAction: AUTO) nie dłużej niż 5 dni
- Karta Mastercard (wszystkie typy transakcji) nie dłużej niż 30 dni

> **UWAGA:** Autopay automatycznie wykonuje zdjęcie blokady (transactionCancel), jeżeli Partner nie wywoła obciążenia (transactionClear) w wyżej zdefiniowanych terminach (Patrz [Zwolnienie blokady po przeterminowaniu transakcji](#zwolnienie-blokady-po-przeterminowaniu-transakcji)).

### Obciążenie karty na wniosek Partnera

### Opis obciążenia karty na wniosek Partnera

W celu obciążenia karty po zrealizowanej preautoryzacji należy wywołać dedykowaną usługę transactionClear (https://{host_bramki}/webapi/transactionClear) z odpowiednimi parametrami (Patrz Schemat D dla Preautoryzacji). Wszystkie parametry przekazywane są metodą POST (Content-Type: application/x-www-form-urlencoded). Protokół rozróżnia wielkość liter zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych parametrów powinny być kodowane w UTF-8.

### Opis dostępnych parametrów dla obciążenia karty na wniosek Partnera


| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna i wskazywać konkretne zlecenie wypłaty w Serwisie Partnera. |
| 3 | RemoteID | TAK | string{1,20} | Alfanumeryczny identyfikator transakcji nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. Jego podanie spowoduje obciążenie karty zautoryzowanej w transakcji o wskazanym **RemoteID**, jeśli jest w stanie blokady (**status ON_HOLD**). |
| 4 | Amount | TAK | amount | Kwota obciążenia (nie może być większa niż kwota blokady); jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00. |
| 5 | Products | TAK | string{1,10000} | Informacje o produktach wchodzących w skład transakcji, przekazywany w postaci zakodowanego protokołem transportowym Base64 XMLa. <BR> Struktura musi zawierać wszystkie podane w preautoryzacji produkty, jednak może być uproszczona (w celu identyfikacji produktu, którego kwota ma być zaktualizowana, brane będą pod uwagę jedynie productID oraz idBalancePoint, a nową kwotę należy podać w subAmount). <BR> /**Wymagane dla wielu produktów podanych w preautoryzacji.** |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |



### Potwierdzenie wykonania operacji dla obciążenia karty na wniosek Partnera

Do poprawnego odpytania należy, wraz z przekazywanymi parametrami,
przesłać zdefiniowany nagłówek HTTP o odpowiedniej treści. Dołączony
nagłówek powinien nosić nazwę \'BmHeader\' i posiadać następującą
wartość \'pay-bm\', w całości powinien prezentować się następująco
\'BmHeader: pay-bm\'. W przypadku poprawnego komunikatu zwracany jest (w
tej samej sesji HTTP) tekst w formacie XML, zawierający potwierdzenie
wykonania operacji lub opis błędu.

Struktura potwierdzenia (XML)

```xml
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<balancePayoff>
	<serviceID>ServiceID</serviceID>
	<messageID>MessageID</messageID>
	<remoteOutID>RemoteOutID</remoteOutID>
	<hash>Hash</hash>
	</balancePayoff>
```

## Opis zwracanych parametrów dla obciążenia karty na wniosek Partnera

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,32} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. <BR> **Wymagany dla confirmation=CONFIRMED.** |
| 2 | messageID | TAK | string{1,20} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. <BR> **Wymagany dla confirmation=CONFIRMED.** |
| 3 | confirmation | TAK | string{1,100} | Status potwierdzenia przyjęcia zlecenia. <BR> Może przyjmować dwie wartości: <BR> - CONFIRMED – operacja powiodła się. _**UWAGA:** Nie oznacza to wykonania obciążenia! System asynchronicznie dostarczy ITN z **paymentStatus=SUCCESS**._ - NOTCONFIRMED – operacja nie powiodła się. |
| 4 | reason | NIE | string{1,1000} | Wyjaśnienie szczegółów przetwarzania żądania. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. <BR> **Wymagany dla confirmation=CONFIRMED.** |


## Zwolnienie blokady na wniosek Partnera

Zwolnienie blokady realizowane jest za pomocą usługi **transactionCancel** (Patrz [Anulowanie nieopłaconej transakcji](../api-operations/cancel-transaction.md#anulowanie-nieoplaconej-transakcji)). Po skutecznym zainicjowaniu zwolnienia blokady (poprawnej odpowiedzi na anulowanie transakcji), System asynchronicznie dostarczy ITN z **paymentStatus=FAILURE** oraz **paymentStatusDetails=CANCELLED**. (Patrz [Schemat E dla Preautoryzacji](#schemat-e-dla-preautoryzacji-zlecenie-przez-partnera-zwolnienia-blokady-bez-potrącania-środków))

## Zwolnienie blokady po przeterminowaniu transakcji

W przypadku braku wywołania przez Partnera obciążenia w terminach określonych dla ważności preautoryzacji (Patrz [Czas ważności preautoryzacji](#czas-ważności-preautoryzacji)), następuje jej automatyczne zwolnienie przez System  (bez potrącania jakichkolwiek środków) (Patrz [Schemat F dla Preautoryzacji](#schemat-f-dla-preautoryzacji-zwolnienie-blokady-przez-system-bez-potrącania-środków)). System dokona anulowania transakcji, zdjęcia blokady oraz dostarczy ITN z **paymentStatus=FAILURE** oraz **paymentStatusDetails=CANCELLED**.

## Schematy dla Preautoryzacji

## Schemat A dla Preautoryzacji: Zakładanie blokady podczas autoryzacji płatności jednorazowej

<!-- TODO MIG-044: Źródło: README-2.md: images/SchematA.png. Problem: Brak obrazu/diagramu: images/SchematA.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-044: Brak obrazu/diagramu: images/SchematA.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Schemat B dla Preautoryzacji: Zakładanie blokady podczas inicjowania płatności automatycznej (zapisywania karty)

<!-- TODO MIG-045: Źródło: README-2.md: images/SchematB.png. Problem: Brak obrazu/diagramu: images/SchematB.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-045: Brak obrazu/diagramu: images/SchematB.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Schemat C dla Preautoryzacji: Zakładanie blokady z wykorzystaniem wcześniej zapisanej karty

<!-- TODO MIG-046: Źródło: README-2.md: images/SchematC.png. Problem: Brak obrazu/diagramu: images/SchematC.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-046: Brak obrazu/diagramu: images/SchematC.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Schemat D dla Preautoryzacji: Zlecenie przez Partnera obciążenia wcześniej zautoryzowanej karty

<!-- TODO MIG-047: Źródło: README-2.md: images/SchematD.png. Problem: Brak obrazu/diagramu: images/SchematD.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-047: Brak obrazu/diagramu: images/SchematD.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Schemat E dla Preautoryzacji: Zlecenie przez Partnera zwolnienia blokady (bez potrącania środków)

<!-- TODO MIG-048: Źródło: README-2.md: images/SchematE.png. Problem: Brak obrazu/diagramu: images/SchematE.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-048: Brak obrazu/diagramu: images/SchematE.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Schemat F dla Preautoryzacji: Zwolnienie blokady przez System (bez potrącania środków)

<!-- TODO MIG-049: Źródło: README-2.md: images/SchematF.png. Problem: Brak obrazu/diagramu: images/SchematF.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-049: Brak obrazu/diagramu: images/SchematF.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.
