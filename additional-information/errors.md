# Kody i komunikaty błędów

<!-- TODO MIG-038: Źródło: README-2.md:5058–5064 oraz Zwrot transakcji V3. Problem: Ogólny opis mówi o wszystkich błędach w XML, podczas gdy V3 opisuje błędy JSON. Wymagana decyzja/materiał: Potwierdzić zakres ogólnego opisu; stosować specyfikację właściwej operacji. -->

> TODO MIG-038: Ogólny opis mówi o wszystkich błędach w XML, podczas gdy V3 opisuje błędy JSON. Potwierdzić zakres ogólnego opisu; stosować specyfikację właściwej operacji.

## Komunikaty błędu

### Opis komunikatów błędu

Wszystkie komunikaty błędów będą zwracane w postaci dokumentu XML,
zawierającego kod błędu, jego nazwę oraz opis. Ze względu na dużą
zmienność możliwych błędów, nie jest utrzymywana pełna dokumentacja
błędów.

**WSKAZÓWKA:** Pole **description**, dokładnie opisuje każdy z błędów
(pola **statusCode** i **name** mogą być ignorowane).

Przykładowy błąd (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<error>
		<statusCode>55</statusCode>
		<name>BALANCE_ERROR</name>
		<description>Wrong services balance! Should be 100 but is 40</description>
	</error>
```

[Błędy przedtransakcji](../online-payments/advanced-flows/pretransaction.md) · [Błędy zwrotów](../online-payments/api-operations/refunds.md) · [Anulowanie transakcji](../online-payments/api-operations/cancel-transaction.md) · [Statusy szczegółowe](../online-payments/transaction-data/statuses.md)
