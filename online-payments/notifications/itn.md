# ITN – status transakcji

<!-- TODO MIG-034: Źródło: README-2.md:3361–3469. Problem: Wspólny przykład ITN/IPN zawiera idBalancePoint. Tabela powtarza verificationStatus dla powodu weryfikacji, przykład używa verificationStatusReason; validityMonth ma długość 4 mimo formatu mm. Wymagana decyzja/materiał: Potwierdzić wariant wspólny i nazwy/typy pól. Definicji ani przykładu nie okrojono; nie traktować tej części jako zweryfikowanej specyfikacji. -->

> TODO MIG-034: Wspólny przykład ITN/IPN zawiera idBalancePoint. Tabela powtarza verificationStatus dla powodu weryfikacji, przykład używa verificationStatusReason; validityMonth ma długość 4 mimo formatu mm. Potwierdzić wariant wspólny i nazwy/typy pól. Definicji ani przykładu nie okrojono; nie traktować tej części jako zweryfikowanej specyfikacji.

## Powiadomienia natychmiastowe (ITN)

### Opis powiadomień natychmiastowych

System przekazuje powiadomienia o zmianie statusu transakcji
niezwłocznie po otrzymaniu takiej informacji z Kanału Płatności, a
komunikat zawsze dotyczy pojedynczej transakcji. Potwierdzenia
przesyłane są przez System płatności online na adres na serwerze Serwisu
Partnera, ustalony w trakcie dodawania konfiguracji Serwisu Partnera.

**UWAGA:** Domena musi być publiczna i dostępna przez System.
Domena musi być zabezpieczona ważnym certyfikatem, wystawionym przez publiczny urząd certyfikacji (Certificate authority)
Serwer musi się przedstawiać pełnym łańcucha certyfikatów (Chain of Trust)
Komunikacja musi się odbywać w oparciu o prokół TLS w wersji 1.2 lub 1.3
*Inne formy zazabezpieczenia połączenia np. VPN, mTLS muszą być uzgadniane indywidualnie z osobą odpowiedzialną za wdrożenie.

Przykład:

```text
	https://sklep_nazwa/odbior_statusu
```

Powiadomienie o zmianie statusu transakcji wejściowej polega na wysłaniu
przez System dokumentu XML zawierającego nowe statusy transakcji.

Dokument wysyłany jest protokołem HTTPS (domyślnie port 443) – metodą POST, jako parametr HTTP o nazwie transactions. Parametr
ten zapisany jest mechanizmem kodowania transportowego Base64.

Format dokumentu (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<transactionList>
		<serviceID>ServiceID</serviceID>
		<transactions>
			<transaction>
				<orderID>OrderID</orderID>
				<remoteID>RemoteID</remoteID>
				<amount>999999.99</amount>
				<currency>PLN</currency>
				<gatewayID>GatewayID</gatewayID>
				<paymentDate>YYYYMMDDhhmmss</paymentDate>
				<paymentStatus>PaymentStatus</paymentStatus>
				<paymentStatusDetails>PaymentStatusDetails</paymentStatusDetails>
			</transaction>
		</transactions>
		<hash>Hash</hash>
	</transactionList>
```

**UWAGA:** Węzeł **transactions** może zawierać jedynie jeden węzeł
**transaction** (a więc powiadomienie dotyczy zawsze jednej transakcji).
Wartości elementów **orderID** i **amount** dotyczących każdej z
transakcji są identyczne z wartościami odpowiadających im pól, podanymi
przez Serwis Partnera przy rozpoczęciu danej transakcji.
Wyjątkiem są tutaj modele, w których prowizja doliczana jest do kwoty transakcji. Wówczas wartość amount w ITN jest powiększona o tą prowizję. Walidację kwot można wtedy przeprowadzić na podstawie opcjonalnego pola ITN startAmount. Należy jednak zgłosić zapotrzebowanie na to pole podczas integracji.

### Lista zwracanych parametrów dla powiadomień natychmiastowych

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera, nadawany w trakcie rejestracji usługi,jednoznacznie identyfikuje Serwis Partnera w Systemie płatności online. |
| 2 | orderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| 3 | remoteID | TAK | string{1,20} | Alfanumeryczny identyfikator transakcji nadany przez System płatności online. Warto go zapisać przy zamówieniu na potrzeby dalszej obsługi (dla wielu transakcji z tym samym **OrderID**, dla zwrotów itp.). <br> Sytuacja taka może mieć miejsce np. w przypadku, gdy Klient zmieni Kanał Płatności, wywoła ponownie ten sam start transakcji z historii przeglądarki itp. System umożliwia blokowanie takich przypadków, jednak opcja nie jest zalecana (nie byłoby możliwe opłacenie porzuconej transakcji). |
| 5 | amount | TAK | amount | Kwota transakcji. Jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. |
| 6 | currency | TAK | string{1,3} | Waluta transakcji. |
| 7 | gatewayID | NIE | string{1,5} | Identyfikator Kanału Płatności, za pomocą, którego Klient uregulował płatność. |
| 8 | paymentDate | TAK | string{14} | Moment zautoryzowania transakcji, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET) |
| 9 | paymentStatus | TAK | enum | Status autoryzacji transakcji, przyjmuje wartości (opis zmian statusów dalej): <ul> <li>**PENDING** – transakcja rozpoczęta.</li> <li> **SUCCESS** – poprawna autoryzacja transakcji, Serwis Partnera otrzyma środki za transakcje – można wydać towar/usługę.</li> <li> **FAILURE** – transakcja nie została zakończona poprawnie.</li></ul> |
| 10 | paymentStatusDetails | NIE | string{1,64} | Szczegółowy status transakcji, wartość może być ignorowana przez Serwis Partnera. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#przykładowe-obliczenia-wartości-funkcji-skrótu-w-komunikacie-itn). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |

**WSKAZÓWKA:** Element **hash** (komunikatu) służy do autentykacji
dokumentu. Opis sposobu obliczania skrótu znajduje się w części [Bezpieczeństwo transakcji](../security/hashing.md#przykładowe-obliczenia-wartości-funkcji-skrótu-w-komunikacie-itn).

### Odpowiedź na powiadomienie natychmiastowe

W odpowiedzi na powiadomienie oczekiwany jest status HTTP 200 (OK) oraz
tekst w formacie XML (niekodowany Base64), zwracany przez Serwis
Partnera w tej samej sesji HTTP, zawierający potwierdzenie otrzymania
statusu transakcji.

Struktura potwierdzenia (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<confirmationList>
		<serviceID>ServiceID</serviceID>
		<transactionsConfirmations>
			<transactionConfirmed>
				<orderID>OrderID</orderID>
				<confirmation>Confirmation</confirmation>
			</transactionConfirmed>
		</transactionsConfirmations>
		<hash>Hash</hash>
	</confirmationList>
```

### Opis pól potwierdzenia dla powiadomień natychmiastowych

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera pochodzący z komunikatu. |
| 2 | orderID | TAK | string{32} | Identyfikator transakcji, nadany w Serwisie Partnera i przekazany w starcie transakcji, pochodzący z komunikatu. |
| 3 | confirmation | TAK | string{1,25} | Element służy do przekazania stanu weryfikacji autentyczności transakcji przez Serwis Partnera. Wartość elementu wyznaczana jest przez sprawdzenie poprawności wartości parametru **serviceID** oraz **currency**, porównanie wartości pól **orderID** i **amount** w komunikacie powiadomienia oraz w komunikacie rozpoczynającym transakcję, a także weryfikację zgodności wyliczonego skrótu z parametrów komunikatu z wartością przekazaną w polu hash komunikatu. Wyjątkiem są modele, w których prowizja doliczana jest do kwoty transakcji. Wówczas wartość amount w ITN jest powiększona o tą prowizję. Walidację kwot można wtedy przeprowadzić na podstawie opcjonalnego pola ITN startAmount. Należy jednak zgłosić zapotrzebowanie na to pole podczas integracji. <BR> Przewidziano dwie wartości elementu **confirmation**:<ul> <li> **CONFIRMED** – wartości parametrów w obu komunikatach oraz parametr hash są zgodne – transakcja autentyczna;</li> <li> **NOTCONFIRMED** – wartości w obu komunikatach są różne lub niezgodność hash – transakcja nieautentyczna;</li></ul> |
| nd. | hash | TAK | string{1,128} | Element hash (w odpowiedzi na komunikat) służy do autentykacji odpowiedzi i liczony jest z wartości parametrów odpowiedzi. Opis sposobu obliczania skrótu znajduje się w części [Bezpieczeństwo transakcji](../security/hashing.md#przykładowe-obliczenia-wartości-funkcji-skrótu-w-komunikacie-itn). |

W wypadku braku poprawnej odpowiedzi na wysłane powiadomienia, System
podejmie kolejne próby przekazania najnowszego statusu transakcji po
upływie określonego czasu. Serwis Partnera powinien wykonywać własną
logikę biznesową (np. mail z potwierdzeniem), jedynie po pierwszym
komunikacie o danym statusie płatności.

**WSKAZÓWKA:** Warto zapoznać się ze *Schematem ponawiania komunikatów
ITN/ISTN/IPN/RPAN/RPDN*.

## Dodatkowe pola w komunikacie ITN/IPN transakcji wejściowej

### Opis

Natychmiastowe powiadomienia o zmianie statusu transakcji mogą zawierać
dodatkowe pola (patrz [Schematy dla Preautoryzacji](../advanced-flows/card-preauthorization.md#schematy-dla-preautoryzacji)). Ich występowanie jest kwestią
konfiguracyjną, ustalaną w trakcie integracji (domyślnie wysyłane jest
tylko węzeł **customerData**).

O tym, czy jest to komunikat ITN, czy IPN decyduje jedynie występowanie węzła **product**.

## Pełna lista dodatkowych pól w komunikacie ITN/IPN

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | typ | opis |
| --- | --- | --- | --- |
| 11 | addressIP | string{1,15} | Adres IP Klienta, zarejestrowany przez front Systemu, ew. adres przekazany do Systemu w parametrze CustomerIP, bądź IP z którego nastąpił start transakcji w Systemie. |
| 13 | customerNumber | string{1,35} | Numer Klienta w Serwisie. |
| 21 | title | string{1,140} | Tytuł wpłaty. W niektórych przypadkach, niezależnych od AP tytuł przelewu może zostać samodzielnie zmodyfikowany przez Bank, w którym nastąpiła wpłata dokonana przez klienta. |
| 22 | customerData-\> fName | string{1,128} | Imię płatnika. |
| 23 | customerData-\> lName | string{1,128} | Nazwisko płatnika. |
| 24 | customerData-\> streetName | string{1,128} | Nazwa ulicy płatnika. |
| 25 | customerData-\> streetHouseNo | string{1,10} | Numer domu płatnika. |
| 26 | customerData-\> streetStaircaseNo | string{1,10} | Numer klatki płatnika. |
| 27 | customerData-\> streetPremiseNo | string{1,10} | Numer lokalu płatnika. |
| 28 | customerData-\> postalCode | string{1,6} | Kod pocztowy adresu płatnika. |
| 29 | customerData-\> city | string{1,128} | Miasto płatnika. |
| 30 | customerData-\> nrb | string{1,26} | Rachunek bankowy płatnika. |
| 31 | customerData-\> senderData | string{1,600} | Dane płatnika w postaci niepodzielonej. |
| 32 | verificationStatus | enum | Element zawierający status weryfikacji płatnika. To enum dopuszczający wartości: PENDING, POSITIVE oraz NEGATIVE. |
| nd. | verificationStatusReasons | list | Lista zawierająca powody negatywnej lub oczekującej weryfikacji. Powodów może być wiele. |
| 33 | verificationStatus | enum | Szczegółowy powód w przypadku negatywnej lub oczekującej weryfikacji. <BR> Dozwolone wartości dla negatywnej weryfikacji: <BR> - NAME – nie zgadza się imię lub nazwisko <BR> - NRB – nie zgadza się numer rachunku <BR> - TITLE – nie zgadza się tytuł <BR> - STREET – nie zgadza się nazwa ulicy <BR> - HOUSE_NUMBER – nie zgadza się numer domu <BR> - STAIRCASE – nie zgadza się numer klatki schodowej <BR> - PREMISE_NUMBER – nie zgadza się numer lokalu <BR> - POSTAL_CODE – nie zgadza się kod pocztowy <BR> - CITY - nie zgadza się miasto <BR> - BLACKLISTED – rachunek, z którego została wykonana wpłata znajduje się na czarnej liście <BR> - SHOP_FORMAL_REQUIREMENTS – weryfikowany serwis nie spełnił warunków formalnych <BR> - JOINT_OWNERSHIP - na rachunku bankowym wykryto współwłasność, które - według konfiguracji serwisu - nie są tolerowane <BR> <BR> Dozwolone wartości dla oczekującej weryfikacji: <BR> - NEED_FEEDBACK – trwa oczekiwanie na spełnienie przez serwis warunków formalnych. _**UWAGA:** Do liczenia wartości Hash pobierane są wartości kolejnych węzłów: **verificationStatusReasons**, **verificationStatusReason**._ |
| 60 | startAmount | amount | Kwota transakcji podana w Linku Płatności (nie uwzględnia ew. kwoty prowizji naliczonej Klientowi). Suma prowizji Klienta i **startAmount** znajduje się w polu **amount**, ponieważ jest to wynikowa wartość transakcji). Jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. |
| 70 | recurringData-\> recurringAction | string{1,100} | Akcja w procesie płatności automatycznej (znaczenie i dozwolone wartości opisano w części [Definicje](../../additional-information/glossary.md)). |
| 71 | recurringData-\> clientHash | string{1,64} | Identyfikator płatności automatycznej generowany przez AP i przekazywany do Partnera po skutecznej aktywacji płatności automatycznej. |
| 72 | recurringData-\> expirationDate | string{14} | Moment wygaśnięcia ważności płatności automatycznej, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET) |
| 73 | cardData-\> index | string{1,64} | Index karty (jeśli użyto karty). Index identyfikuje kartę o danej dacie ważności (zmiana daty lub numeru karty powoduje zmianę wartości tego parametru). |
| 74 | cardData-\> validityYear | string{4} | Ważność karty w formacie YYYY (jeśli użyto karty). |
| 75 | cardData-\> validityMonth | string{4} | Ważność karty w formacie mm (jeśli użyto karty). |
| 76 | cardData-\> issuer | string{1,64} | Typ karty (jeśli użyto karty). <BR> Możliwe wartości: <BR> - VISA <BR> - MASTERCARD <BR> - MAESTRO <BR> - AMERICAN EXPRESS (obecnie nie wspierane) <BR> - DISCOVER (obecnie nie wspierane) <BR> - DINERS (obecnie nie wspierane) <BR> - UNCATEGORIZED (nierozpoznany wystawca) |
| 77 | cardData-\> bin | string{6} | Pierwsze 6 cyfr numeru karty (jeśli użyto karty). Przekazywane, jeśli nie przekazywany jest parametr cardData-\> mask. |
| 78 | cardData-\> mask | string{4} | Ostatnie 4 cyfry numeru karty (jeśli użyto karty). Przekazywane, jeśli nie przekazywany jest parametr cardData-\>bin. |
| 90 | product-\> subAmount | amount | Kwota produktu jako separator dziesiętny używana jest kropka - \'.\' <BR> Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. <BR> Węzeł dostępny tylko w komunikatach IPN. |
| 91 | product-\> params | list | Kolejne parametry produktu zgodne z formatem koszyka w starcie transakcji. Węzeł dostępny tylko w komunikatach IPN. |
| 100 | gatewayCustomerID | string{32} | BLIK USER ID - identyfikator nadawany w Systemie PSP Kontu Mobilnemu zarejestrowanemu przez Partnera, które w systemie Partnera powiązane jest z Aplikacją wydaną przez Partnera danemu Klientowi. Parametr może być przekazywany jedynie instytucjom obowiązanym lub podmiotom zobowiązanym regulacyjnie do wykonywania obowązków z zakresu przeciwdziałania praniu pieniędzy oraz finansowaniu terroryzmu. |
| 101 | schemeTransactionID | string{1,32} | Unikalny identyfikator techniczny nadawany przez organizacje płatnicze (payment schemes). Identyfikator łączy transakcję pierwotną z transakcjami kolejnymi. Może być również wykorzystywany jako pojedyncza, unikalna referencja transakcji. Parametr przesyłany dla transakcji kartowych, jego występowanie jest kwestią konfiguracyjną, ustalaną w trakcie integracji. |

**UWAGA:** Do liczenia wartości Hash pobierane są atrybuty **value**
kolejnych węzłów **product.params**.

Przykład komunikatu ITN/IPN z dodatkowymi parametrami (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<transactionList>
	  <serviceID>ServiceID</serviceID>
	  <transactions>
	  <transaction>
		<orderID>OrderID</orderID>
		<remoteID>RemoteID</remoteID>
		<amount>999999.99</amount>
		<currency>PLN</currency>
		<gatewayID>GatewayID</gatewayID>
		<paymentDate>YYYYMMDDhhmmss</paymentDate>
		<paymentStatus>PaymentStatus</paymentStatus>                 
		<paymentStatusDetails>PaymentStatusDetails</paymentStatusDetails>
		<addressIP>127.0.0.1</addressIP>
		<customerNumber>1111111</customerNumber>
		<title>title</title>
		<customerData>
		  <fName>fName</fName>
		  <lName>lName</lName>
		  <streetName>streetName</streetName>
		  <streetHouseNo>streetHouseNo</streetHouseNo>
		  <streetStaircaseNo>streetStaircaseNo</streetStaircaseNo>
		  <streetPremiseNo>streetPremiseNo</streetPremiseNo>
		  <postalCode>postalCode</postalCode>
		  <city>city</city>
		  <nrb>nrb</nrb>
		  <senderData>senderData</senderData>    
		</customerData>
		<verificationStatus>verificationStatus</verificationStatus>
		<verificationStatusReasons>
		   <verificationStatusReason>reason1</verificationStatusReason>
		   <verificationStatusReason>reason2</verificationStatusReason>
		   <verificationStatusReason>reason3</verificationStatusReason>
		</verificationStatusReasons>
		<startAmount>999998.99</startAmount>    
		<recurringData>
			<recurringAction>RecurringAction</recurringAction>
			<clientHash>ClientHash</clientHash>
			<expirationDate>YYYYMMDDhhmmss</expirationDate>
		</recurringData>
		<cardData>
			<index>Index</index>
			<validityYear>ValidityYear</validityYear>
			<validityMonth>ValidityMonth</validityMonth>
			<issuer>Issuer</issuer>
			<bin>BIN</bin>
		</cardData>
		<product>
			<subAmount>SubAmount</subAmount>
			<params>
				<param name="idBalancePoint" value="idBalancePoint"/>
				<param name="invoiceNumber" value="invoiceNumber"/>
				<param name="customerNumber" value="customerNumber"/>
				<param name="subAmount" value="SubAmount"/>
			</params>
		</product>
		<gatewayCustomerID>GatewayCustomerID</gatewayCustomerID>
		<schemeTransactionID>SchemeTransactionID</schemeTransactionID>
	  </transaction>
	  </transactions>
	  <hash>Hash</hash>
	</transactionList>
```

[Schemat ponawiania powiadomień](retry-policy.md).
