# ISTN – status rozliczenia



## Natychmiastowe powiadomienia o zmianie statusu transakcji rozliczeniowej (ISTN)

Istnieje możliwość dostarczania komunikatów o wszystkich wypłatach
(rozliczeniach, wypłatach z salda i zwrotach) wykonywanych przez System
w ramach usługi płatności. Ponieważ usługa nie jest domyślnie
uruchomiona, zapotrzebowanie na nią, wraz z adresem do wysyłki ISTN,
musi być przez Partnera zgłoszone w trakcie ustalania wymagań.

W przypadku skutecznego uruchomienia komunikacji ISTN, System
niezwłocznie przekazuje powiadomienia o fakcie zlecenia transakcji
rozliczeniowej (ew. wypłatach/zwrotach) oraz zmianie jej statusu.
Potwierdzenia przesyłane są, na ustalony w trakcie dodawania
konfiguracji Serwisu Partnera, adres na serwerze Serwisu Partnera:

```text
	https://sklep_nazwa/odbior_informacji_o_rozliczeniu
```

Powiadomienie to polega na wysłaniu przez System dokumentu XML
zawierającego nowe statusy transakcji. Dokument wysyłany jest protokołem
HTTPS (domyślnie port 443). Dokument przesyłany jest
metodą POST, jako parametr HTTP o nazwie transactions. Parametr ten
zapisany jest mechanizmem kodowania transportowego Base64.


Format dokumentu (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<transactionList>
		<serviceID>ServiceID</serviceID>
		<transactions>
			<transaction>
				<isRefund>true/false</isRefund>
				<productID>ProductID</productID>
				<orderID>OrderID</orderID>
				<orderOutID>OrderOutID</orderOutID>
				<remoteID>RemoteID</remoteID>
				<remoteOutID>RemoteOutID</remoteOutID>
				<amount>999999.99</amount>
				<currency>PLN</currency>
				<transferDate>YYYYMMDDhhmmss</transferDate>
				<transferStatus>TransferStatus</transferStatus>                 
				<transferStatusDetails>TranasferStatusDetails</transferStatusDetails>
				<title>Title</title>
				<receiverBank>ReceiverBank</receiverBank>      
				<receiverNRB>ReceiverNRB</receiverNRB>
				<receiverName>ReceiverName</receiverName>
				<receiverAddress>ReceiverAddress</receiverAddress>
				<senderBank>SenderBank</senderBank>
				<senderNRB>SenderNRB</senderNRB>
			</transaction>
		</transactions>
		<hash>Hash</hash>
	</transactionList>
```

### Zwracane parametry

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | NIE | string{1,10} | Identyfikator Serwisu Partnera, nadawany w trakcie rejestracji usługi, jednoznacznie identyfikuje Serwis Partnera w Systemie płatności online. |
| 2 | isRefund | NIE | Boolean | Informacja, czy ISTN dotyczy zwrotu transakcji (true), czy normalnego rozliczenia (false). |
| 3 | productID | NIE | string{1,36} | Identyfikator rozliczanego produktu z koszyka produktów transakcji wejściowej, wartość pola musi być unikalna dla Serwisu Partnera. |
| 4 | orderID | NIE | string{1,32} | Identyfikator transakcji wejściowej o długości do 32 znaków alfanumerycznych alfabetu łacińskiego, wartość pola musi być unikalna dla Serwisu Partnera. |
| 5 | orderOutID | NIE | string{1,32} | Identyfikator transakcji wyjściowej o długości do 32 znaków alfanumerycznych alfabetu łacińskiego. Pole może być nadawane przez Serwis (w przypadku zlecenia rozliczenia) lub przez System płatności online. |
| 6 | remoteID | NIE | string{1,20} | Alfanumeryczny identyfikator transakcji wejściowej nadany przez System płatności online (podany, jeśli do rozliczenia dowiązana jest jedna wpłata). |
| 7 | remoteOutID | NIE | string{1,20} | Alfanumeryczny identyfikator transakcji rozliczeniowej nadany przez System płatności online. |
| 8 | amount | TAK | amount | Kwota transakcji. Jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. |
| 9 | currency | TAK | string{1,3} | Waluta transakcji. |
| 40 | transferDate | NIE | string{14} | Moment zautoryzowania transakcji, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET). <br> Występuje jedynie dla transferStatus=SUCCESS. |
| 41 | transferStatus | TAK | enum | Status autoryzacji transakcji rozliczeniowej. <BR> Przyjmuje następujące wartości: <BR> - PENDING – przelew oczekuje na wykonanie <BR> - SUCCESS – przelew zlecono do banku <BR> - FAILURE – nie można wykonać przelewu, np. błędny numer rachunku |
| 42 | transferStatusDetails | NIE | enum | Szczegółowy status transakcji, wartość może być ignorowana przez Serwis Partnera. <BR> Przyjmuje poniższe wartości (lista może zostać rozszerzona): <BR> - AUTHORIZED – transakcja przekazana do realizacji w banku <BR> - CONFIRMED – transakcja potwierdzona w banku (fizycznie wysłane pieniądze) <BR> - CANCELLED – transakcja anulowana przez Serwis Partnera lub Call Center (np. na prośbę Serwisu) <BR> - ANOTHER_ERROR – wystąpił inny błąd przy przetwarzaniu transakcji |
| 43 | title | NIE | string{1,140} | Tytuł przelewu rozliczającego transakcję. W niektórych przypadkach, niezależnych od AP tytuł przelewu rozliczeniowego może zostać samodzielnie zmodyfikowany przez Bank, z którego nastąpiło rozliczenie. <BR> Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: `ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!@#%\^\*()\_=+\[\]{};:?`, gdzie znak "/" będzie podmieniany na "-" dla transakcji wychodzących. |
| 44 | receiverBank | NIE | string{1,64} | Nazwa banku, do którego System wykonał przelew. |
| 45 | receiverNRB | NIE | string{26} | Numer rachunku bankowego odbiorcy przelewu. |
| 46 | receiverName | NIE | string{1,140} | Nazwa odbiorcy przelewu. <BR> Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!@#%\^\*()\_=+\[\]{};:? |
| 47 | receiverAddress | NIE | string{1,140} | Adres odbiorcy przelewu. <BR> Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!@#%\^\*()\_=+\[\]{};:? |
| 48 | senderBank | NIE | string{1,64} | Nazwa banku, za pomocą którego System wykonał przelew. |
| 49 | senderNRB | NIE | string{26} | Numer rachunku bankowego nadawcy przelewu. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


### Odpowiedź na powiadomienie

W odpowiedzi na powiadomienie oczekiwany jest tekst w formacie XML (nie
kodowany Base64), zwracany przez Serwis Partnera w tej samej sesji HTTP,
zawierający potwierdzenie otrzymania statusu transakcji.


Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<confirmationList>
		<serviceID>ServiceID</serviceID>
		<transactionsConfirmations>
			<transactionConfirmed>
				<remoteOutID>RemoteOutID</remoteOutID>
				<confirmation>Confirmation</confirmation>
			</transactionConfirmed>
		</transactionsConfirmations>
		<hash>Hash</hash>
	</confirmationList>
```

Element **confirmation** służy do przekazania stanu weryfikacji
autentyczności transakcji przez Serwis Partnera. Wartość elementu
wyznaczana jest przez sprawdzenie poprawności wartości parametru
serviceID, a także weryfikację zgodności wyliczonego skrótu z wartością
przekazaną w polu hash.

Przewidziano dwie wartości tego elementu:

a)  **CONFIRMED** – parametr hash jest zgodny – transakcja
autentyczna;

b)  **NOTCONFIRMED** – parametr hash jest niezgodny– transakcja
nieautentyczna;

W wypadku braku poprawnej odpowiedzi na wysłane powiadomienia, System
podejmie kolejne próby przekazania nowego statusu po upływie określonego
czasu. Serwis Partnera powinien wykonywać własną logikę biznesową,
jedynie po pierwszym komunikacie o danym statusie płatności.

**WSKAZÓWKA:** Warto zapoznać się ze [Schematem ponawiania komunikatów ITN/ISTN/IPN/RPAN/RPDN](retry-policy.md#schemat-ponawiania-komunikatów-itnistnipnrpanrpdn).

### Szczegółowy opis zachowania i zmiany statusów rozliczenia (transferStatus)

W podstawowym modelu, System dostarczy jedynie status **SUCCESS**, możliwe
jest jednak dokładniejsze powiadamianie. Opcja pełna powinna być
zgłoszona podczas integracji i wiąże się z poniższym schematem przejść
statusów.

Zlecenie transakcji rozliczeniowej powoduje wysłanie statusu
**PENDING**. Później system dostarczy **SUCCESS** lub **FAILURE**. Dla
transakcji, dla której wystąpił status **SUCCESS**, nie powinna już
nastąpić zmiana statusu na **FAILURE**. Może jednakże nastąpić zmiana
statusu szczegółowego (kolejne komunikaty o zmianie statusu
szczegółowego są jedynie informacyjne i nie powinny pociągać za sobą
ponownego wykonywania żadnej logiki biznesowej).

W szczególnych przypadkach (np. błąd w banku) transakcja pierwotnie
potwierdzona, może zostać przekazana do ponownego wykonania, a więc
zmienić swój status na **PENDING** i ponownie na **SUCCESS.**

Innym szczególnym przypadkiem może być status **FAILURE** (np. po
błędzie wewnętrznym Systemu), następnie zastąpiony statusem **SUCCESS**.

[Schemat ponawiania powiadomień](retry-policy.md).
