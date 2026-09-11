# Saldo

<!-- TODO MIG-035: Źródło: README-2.md:4094–4156. Problem: Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i zależne reguły Hash. Zachowano je, pomijając odrębny przykład Punktu Rozliczeń. Wymagana decyzja/materiał: Potwierdzić samodzielny kontrakt dla ServiceID; wariant Punktów Rozliczeń pozostaje poza zakresem wdrożenia. -->

> TODO MIG-035: Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i zależne reguły Hash. Zachowano je, pomijając odrębny przykład Punktu Rozliczeń. Potwierdzić samodzielny kontrakt dla ServiceID; wariant Punktów Rozliczeń pozostaje poza zakresem wdrożenia.

<!-- TODO MIG-026: Źródło: README-2.md:5158–5180. Problem: Modele rozliczeń produktów mają niejasną granicę zakresu. Dla transactionSettlement źródło podaje wyłącznie nazwę operacji. Wymagana decyzja/materiał: Dostarczyć niezależny opis modeli produktów i kompletny kontrakt transactionSettlement. -->

> TODO MIG-026: Modele rozliczeń produktów mają niejasną granicę zakresu. Dla transactionSettlement źródło podaje wyłącznie nazwę operacji. Dostarczyć niezależny opis modeli produktów i kompletny kontrakt transactionSettlement.

## Odpytanie o stan salda

### Opis

Dla wszystkich serwisów możliwe jest odpytanie o aktualny stan salda. W
tym celu należy wywołać metodę **balanceGet**
[https://{host_bramki}/webapi/balanceGet](https://{host_bramki}/webapi/balanceGet) z odpowiednimi parametrami.
Wszystkie parametry przekazywane są metodą POST (Content-Type:
application/x-www-form-urlencoded). Protokół rozróżnia wielkość liter
zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych
parametrów powinny być kodowane w UTF-8.

### Lista dostępnych parametrów dla aktualnego stanu salda

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 1 | BalancePointID | TAK | string{1,10} | Identyfikator Punktu Rozliczeń. _**UWAGA:** Wymagane jedno z pól **ServiceID** lub **BalancePointID**. <BR> Podanie obu spowoduje zatrzymanie przetwarzania żądania oraz błąd http._ |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Wartość pola musi być unikalna dla Serwisu Partnera. |
| 3 | PlenipotentiaryID | NIE | string{8,8} | Identyfikator pełnomocnika. Jeżeli jest obecny to do obliczania Hash jest używany klucz współdzielony pełnomocnika, a nie główny klucz serwisu/punktu rozliczeń. Wpływa też na Hash w odpowiedzi na ten komunikat. _**WAŻNE!** Użycie tego pola wymaga specjalnych uzgodnień biznesowych._ |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). Stosowany jest klucz współdzielony przypisany do zastosowanego identyfikatora konfiguracji (Serwisu, bądź Punktu Rozliczeń). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


### Odpowiedź na żądanie aktualnego salda

W odpowiedzi na żądanie zwracany jest (w tej samej sesji HTTP) tekst w
formacie XML, zawierający potwierdzenie przyjęcia operacji do realizacji
lub opis błędu.

Struktura potwierdzenia (XML)

```xml
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<balanceGet>
		<serviceID>ServiceID</serviceID>
		<messageID>MessageID</messageID>
		<balance>Balance</balance>
		<currency>Currency</currency>
		<hash>Hash</hash>
	</balanceGet>
```

### Lista pól odpowiedzi

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. |
| 1 | balancePointID | TAK | string{1,10} | Identyfikator Punktu Rozliczeń. Pochodzi z żądania metody. _**UWAGA:** Zwrócone zostanie jedno z pól **ServiceID** lub **BalancePointID**._ |
| 2 | messageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| 3 | balance | TAK | amount | Wartość salda; jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00. |
| 4 | currency | TAK | string{1,3} | Waluta salda. <BR> Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). Stosowany jest klucz współdzielony przypisany do zastosowanego identyfikatora konfiguracji (Serwisu, bądź Punktu Rozliczeń). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |



## Zasilanie salda

### Opis

Dla serwisów posiadających saldo w Systemie, możliwe jest wykonanie
operacji zasilenia salda na poczet przyszłych zwrotów. W tym celu należy
wykonać start transakcji z parametrem **TransactionSettlementMode** o
wartości **NONE** oraz **Amount** wskazującym **kwotę zasilenia**.

Skorzystanie z **Przedtransakcji** pozwoli na proste dostarczenie
gotowej transakcji do płatnika (np. podając w CustomerEmail adres
księgowości Partnera).

**UWAGA:** Usługa musi zostać uzgodnienia z opiekunem biznesowym. 

## Modele rozliczeń
Schematy obsługi transakcji i rozliczeń

### Model rozliczeń zbiorczych transakcji (model domyślny)

Rozliczenia zbiorcze następują następnego dnia roboczego (D+1).

<!-- TODO MIG-069: Źródło: README-2.md: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png. Problem: Brak obrazu/diagramu: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-069: Brak obrazu/diagramu: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


#### Model rozliczeń transakcji po każdej wpłacie

Rozliczenia po każdej wpłacie wykonywane mogą być niezwłocznie po
otrzymaniu wpłaty od Klienta na wskazane w parametrach Linka płatności
dane transakcji (opcje: Konto odbiorcy, Tytuł przelewu rozliczeniowego,
Nazwa odbiorcy przelewu rozliczeniowego).

<!-- TODO MIG-070: Źródło: README-2.md: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png. Problem: Brak obrazu/diagramu: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-070: Brak obrazu/diagramu: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Model rozliczeń transakcji na żądanie

Rozliczenia mogą być zlecane przez Partnera poprzez wywołanie metody:
**transactionSettlement**.

<!-- TODO MIG-071: Źródło: README-2.md: images/model_rozliczeń_transakcji_na_żądanie.png. Problem: Brak obrazu/diagramu: images/model_rozliczeń_transakcji_na_żądanie.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-071: Brak obrazu/diagramu: images/model_rozliczeń_transakcji_na_żądanie.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.
