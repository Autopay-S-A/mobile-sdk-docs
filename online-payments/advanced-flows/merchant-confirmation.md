# Płatność z potwierdzeniem merchanta

<!-- TODO MIG-027: Źródło: README-2.md:1003,1013,1041. Problem: Opis wskazuje końcowy paymentStatus=CONFIRMED, tabela odpowiedzi wskazuje SUCCESS. Zachowano oba opisy. Wymagana decyzja/materiał: Potwierdzić końcowy status ITN i pełną listę statusów dla tego modelu. -->

> TODO MIG-027: Opis wskazuje końcowy paymentStatus=CONFIRMED, tabela odpowiedzi wskazuje SUCCESS. Zachowano oba opisy. Potwierdzić końcowy status ITN i pełną listę statusów dla tego modelu.

## Płatność z potwierdzeniem merchanta

**WAŻNE!** Jeżeli interesuje Cię usługa - zgłoś się do swojego opiekuna biznesowego.

### Opis ogólny działania usługi płatności z potwierdzeniem merchanta

Proces płatności z potwierdzeniem merchanta to model płatniczy, w którym środki klienta są wstępnie przyjmowane, a następnie – dopiero po decyzji merchanta – przekazywane Merchantowi do rozliczenia (/transactionConfirm (TODO: odwołanie wymaga potwierdzenia)) lub zwracana na rachunek Klienta ([/transactionCancel](../api-operations/cancel-transaction.md#anulowanie-nieoplaconej-transakcji)) na żądanie Merchanta albo na skutek upływu czasu.
Umożliwia to weryfikację zamówienia, sprawdzenie dostępności towaru lub wykonanie dodatkowych procesów decyzyjnych, zanim środki faktycznie zostaną przekazane na rachunek bankowy Merchanta.
Poniżej przedstawiono przebieg procesu zgodnie z diagramem sekwencji.

<!-- TODO MIG-042: Źródło: README-2.md: images/paybm-sequences-2pv.png. Problem: Brak obrazu/diagramu: images/paybm-sequences-2pv.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-042: Brak obrazu/diagramu: images/paybm-sequences-2pv.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


### Inicjalizacja procesu

Klient inicjuje płatność na stronie lub w aplikacji Merchanta, wybierając metodę płatności wspierającą model płatności z potwierdzeniem Merchanta (BLIK, PBL, PIS, płatności kartowe).
Merchant wysyła do systemu płatniczego żądanie rozpoczęcia transakcji z włączonym parametrem: **MerchantConfirmationRequired=true**

Parametr ten informuje PSP, że transakcja wymaga późniejszej decyzji merchanta.

### Rejestracja transakcji i wstępne przyjęcie środków
Po zautoryzowaniu transakcji przez Klienta i otrzymaniu informacji o pobraniu środków, Autopay potwierdza ten fakt poprzez wysłanie komunikatu ITN ze statusem **paymentStatus = ON_HOLD** - oznaczający wstępne przyjęcie środków i oczekiwanie **na pozytywną lub negatywną decyzję** merchanta albo upływ czasu.

### Decyzja merchanta

W późniejszym momencie (asynchronicznie), po wykonaniu swoich procesów biznesowych, merchant podejmuje decyzję co do dalszego losu transakcji.

### Decyzja pozytywna – pobranie środków (/transactionConfirm (TODO: odwołanie wymaga potwierdzenia))

Jeżeli transakcja jest poprawna i zamówienie może zostać zrealizowane Merchant powinien wywołać endpoint /transactionConfirm (TODO: odwołanie wymaga potwierdzenia) co oznacza chęć przekazania środków do ostatecznego rozliczenia.. Na potwierdzenie transakcji ma maksymalnie 120 minut (liczone od momentu otrzymania przez Autopay potwierdzenia autoryzacji). Potwierdzeniem ostatecznej akceptacji transakcji jest asynchroniczny komunikat ITN ze statusem **paymentStatus = CONFIRMED**.

### Decyzja negatywna – anulowanie transakcji ([/transactionCancel](../api-operations/cancel-transaction.md#anulowanie-nieoplaconej-transakcji)) lub timeout

Jeśli transakcja nie może zostać zrealizowana (np. brak towaru, błąd zamówienia, negatywna weryfikacja), Merchant powinien wywołać metodę [/transactionCancel](../api-operations/cancel-transaction.md#anulowanie-nieoplaconej-transakcji). Ostatecznie, jeśli merchant nie podejmie decyzji w odpowiednim czasie, może wystąpić **timeout**, co jest równoznaczne z automatycznym żądaniem anulowania transakcji przez Merchanta. Potwierdzenie anulowania transakcji jest wysyłka komunikatu ITN ze statusem **paymentStatus = FAILURE** - oznaczający zwrot środków do Klienta.

<a id="platnosc-z-potwierdzeniem-merchanta"></a>
### Opis metody potwierdzającej transakcję

Metoda **https://{host_bramki}/webapi/transactionConfirm** służy do **finalizacji transakcji** w modelu płatności z potwierdzeniem merchanta poprzez pobranie wcześniej zablokowanych środków (zakomunikowanych statusem ITN **paymentStatus = ON_HOLD**). Wywołanie tej operacji jest wykonywane przez Merchanta, po zakończeniu weryfikacji zamówienia (np. potwierdzeniu dostępności, poprawności danych, decyzji antyfraudowej).
Po poprawnym potwierdzeniu transakcji Autopay przekaże wynik w komunikacie ITN ze statusem **paymentStatus = CONFIRMED**.

### Lista parametrów żądania metody potwierdzającej transakcję

Wszystkie parametry przekazywane są metodą POST
(Content-Type: application/x-www-form-urlencoded). Protokół rozróżnia
wielkość liter zarówno w nazwach jak i wartościach parametrów.

Dodatkowo podczas wywołania należy przesłać nagłówek HTTP BmHeader o wartości pay-bm (**BmHeader: pay-bm**).

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | integer | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna i wskazywać konkretne zlecenie wypłaty w Serwisie Partnera. |
| 3 | RemoteID | TAK | string{1,20} | Alfanumeryczny identyfikator transakcji nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. Jego podanie spowoduje obciążenie karty zautoryzowanej w transakcji o wskazanym **RemoteID**, jeśli jest w stanie blokady (**status ON_HOLD**). |
| nd. | Hash | TAK | string{1, 128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#przykładowe-obliczenia-wartości-funkcji-skrótu-w-odpytaniu-o-listę-kanałów-płatności) |

### Lista parametrów odpowiedzi metody potwierdzającej transakcję


| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,32} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. <BR> **Wymagany dla confirmation=CONFIRMED.** |
| 2 | messageID | TAK | string{1,20} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. <BR> **Wymagany dla confirmation=CONFIRMED.** |
| 3 | confirmation | TAK | string{1,100} | Status potwierdzenia przyjęcia zlecenia. <BR> Może przyjmować dwie wartości: <BR> - CONFIRMED – operacja powiodła się. _**UWAGA:** Nie oznacza to wykonania obciążenia! System asynchronicznie dostarczy ITN z **paymentStatus=SUCCESS**._ - NOTCONFIRMED – operacja nie powiodła się. |
| 4 | reason | NIE | string{1,1000} | Wyjaśnienie szczegółów przetwarzania żądania. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. <BR> **Wymagany dla confirmation=CONFIRMED.** |

Przykładowa odpowiedź (XML):

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<transaction>
    <serviceID>100</serviceID>
    <messageID>6781ba8534a1e0df655384d8e8a62acb</messageID>
    <confirmation>CONFIRMED</confirmation>
    <hash>eb5d9f9f7dd69e01d3a416d7140bd727838d67d8d465b494d793f0b8c137hdgc</hash>
</transaction>
```

<!-- TODO MIG-043: Źródło: README-2.md: odwołania #platnosc-z-potwierdzeniem-merchanta, #platnosc-z-potwierdzeniem-merchanta, #platnosc-z-potwierdzeniem-merchanta. Problem: Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. Wymagana decyzja/materiał: Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą. -->

> TODO MIG-043: Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą.
