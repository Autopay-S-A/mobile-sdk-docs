# Kody i komunikaty błędów

## Komunikaty błędu

### Opis komunikatów błędu

Metody przyjmujące dane w formacie `application/x-www-form-urlencoded` zwracają błędy w postaci dokumentu XML, zawierającego kod błędu, jego nazwę oraz opis (przykład poniżej). Metody JSON (np. `transactionRefund/v3`, `outDetails/v2`) zwracają błędy w formacie JSON — ich strukturę opisano przy danej metodzie. Ze względu na dużą zmienność możliwych błędów, nie jest utrzymywana pełna dokumentacja błędów.

**WSKAZÓWKA:** Pole **description**, dokładnie opisuje każdy z błędów (pola **statusCode** i **name** mogą być ignorowane).

Przykładowy błąd (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<error>
		<statusCode>55</statusCode>
		<name>BALANCE_ERROR</name>
		<description>Wrong services balance! Should be 100 but is 40</description>
	</error>
```

[Błędy przedtransakcji](../bramka-platnosci-online/zaawansowane-scenariusze-platnosci/pretransaction.md) · [Błędy zwrotów](../bramka-platnosci-online/pozostale-operacje-api/refunds.md) · [Anulowanie transakcji](../bramka-platnosci-online/pozostale-operacje-api/cancel-transaction.md) · [Statusy szczegółowe](../bramka-platnosci-online/dane-transakcji/statuses.md)
