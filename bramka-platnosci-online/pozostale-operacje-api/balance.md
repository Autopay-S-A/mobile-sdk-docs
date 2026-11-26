# Saldo

## Odpytanie o stan salda

### Opis

Dla wszystkich serwisów możliwe jest odpytanie o aktualny stan salda. W tym celu należy wywołać metodę **balanceGet** [https://{host\_bramki}/webapi/balanceGet](https://{host_bramki}/webapi/balanceGet) z odpowiednimi parametrami. Wszystkie parametry przekazywane są metodą POST (Content-Type: application/x-www-form-urlencoded). Protokół rozróżnia wielkość liter zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych parametrów powinny być kodowane w UTF-8.

### Lista dostępnych parametrów dla aktualnego stanu salda

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa             | wymagany | typ           | opis                                                                                                                                                                                                                                                                                                                                                               |
| -------------- | ----------------- | -------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1              | ServiceID         | TAK      | string{1,10}  | Identyfikator Serwisu Partnera.                                                                                                                                                                                                                                                                                                                                    |
| 2              | MessageID         | TAK      | string{32}    | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Wartość pola musi być unikalna dla Serwisu Partnera.                                                                                                                                                                                          |
| 3              | PlenipotentiaryID | NIE      | string{8,8}   | Identyfikator pełnomocnika. Jeżeli jest obecny to do obliczania Hash jest używany klucz współdzielony pełnomocnika, a nie główny klucz serwisu. Wpływa też na Hash w odpowiedzi na ten komunikat. _**WAŻNE!** Użycie tego pola wymaga specjalnych uzgodnień biznesowych._                                                                         |
| nd.            | Hash              | TAK      | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../bezpieczenstwo/hashing.md#bezpieczeństwo-transakcji). Stosowany jest klucz współdzielony przypisany do Serwisu Partnera. **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |

### Odpowiedź na żądanie aktualnego salda

W odpowiedzi na żądanie zwracany jest (w tej samej sesji HTTP) tekst w formacie XML, zawierający potwierdzenie przyjęcia operacji do realizacji lub opis błędu.

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

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa          | wymagany | typ           | opis                                                                                                                                                                                                                                                                                                                                                                        |
| -------------- | -------------- | -------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1              | serviceID      | TAK      | string{1,10}  | Identyfikator Serwisu Partnera. Pochodzi z żądania metody.                                                                                                                                                                                                                                                                                                                  |
| 2              | messageID      | TAK      | string{32}    | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody.                                                                                                                                                                                                                             |
| 3              | balance        | TAK      | amount        | Wartość salda; jako separator dziesiętny używana jest kropka - '.' Format: 0.00.                                                                                                                                                                                                                                                                                            |
| 4              | currency       | TAK      | string{1,3}   | <p>Waluta salda.<br>Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD.</p>                                                                                                                                                                                                                                                                                              |
| nd.            | hash           | TAK      | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../bezpieczenstwo/hashing.md#bezpieczeństwo-transakcji). Stosowany jest klucz współdzielony przypisany do Serwisu Partnera. **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |

## Zasilanie salda

### Opis

Dla serwisów posiadających saldo w Systemie, możliwe jest wykonanie operacji zasilenia salda na poczet przyszłych zwrotów. W tym celu należy wykonać start transakcji z parametrem **TransactionSettlementMode** o wartości **NONE** oraz **Amount** wskazującym **kwotę zasilenia**.

Skorzystanie z **Przedtransakcji** pozwoli na proste dostarczenie gotowej transakcji do płatnika (np. podając w CustomerEmail adres księgowości Partnera).

**UWAGA:** Usługa musi zostać uzgodnienia z opiekunem biznesowym.
