# RPAN i RPDN – płatności automatyczne

<!-- TODO MIG-028: Źródło: README-2.md:2967–2976. Problem: Przykład RPDN zawiera otwarcie recurringData zamiast jego zamknięcia. Zachowano oryginalny XML. Wymagana decyzja/materiał: Potwierdzić poprawny XML przed użyciem przykładu. -->

> TODO MIG-028: Przykład RPDN zawiera otwarcie recurringData zamiast jego zamknięcia. Zachowano oryginalny XML. Potwierdzić poprawny XML przed użyciem przykładu.

### Komunikat RPAN

Komunikat RPAN, informujacy o uruchomieniu usługi płatności automatycznej wysyłany jest jest protokołem HTTPS (domyślnie port 443),
metodą POST, jako parametr HTTP o nazwie `recurring`. Parametr ten jest zapisany mechanizmem kodowania transportowego `Base64`.

Format dokumentu (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<recurringActivation>
		<serviceID>ServiceID</serviceID>
		<transaction>
			<orderID>OrderID</orderID>
			<remoteID>RemoteID</remoteID>
			<amount>999999.99</amount>
			<currency>PLN</currency>
			<gatewayID>GatewayID</gatewayID>
			<paymentDate>YYYYMMDDhhmmss</paymentDate>
			<paymentStatus>PaymentStatus</paymentStatus>                 
			<paymentStatusDetails>PaymentStatusDetails</paymentStatusDetails>
			<startAmount>999998.99</startAmount>
			<invoiceNumber>InvoiceNumber</invoiceNumber>
			<customerNumber>CustomerNumber</customerNumber>
			<customerEmail>CustomerEmail</customerEmail>
			<customerPhone>CustomerPhone</customerPhone>
		</transaction>
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
			<mask>Mask</mask>
		</cardData>
		<blikData>
			<appLabel>label</appLabel>
		</blikData>
		<hash>Hash</hash>
	</recurringActivation>
```

Wartości elementów: **orderID, serviceID, amount** dotyczące każdej z
aktywowanych płatności automatycznych są identyczne z wartościami
odpowiadających im pól podanymi przez Serwis przy rozpoczęciu danej
płatności inicjalizacyjnej.

### Opis zwracanych parametrów dla uruchomienia płatności automatycznej

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1. | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera, nadawany w trakcie rejestracji usługi, jednoznacznie identyfikuje Serwis Partnera w Systemie płatności online. |
| 2. | transaction -\> orderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| 3. | transaction -\> remoteID | TAK | string{1,20} | Alfanumeryczny identyfikator transakcji nadany przez System płatności online. |
| 5. | transaction -\> amount | TAK | amount | Kwota transakcji. Jako separator dziesiętny używana jest kropka - \'.\'<BR>Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce._**UWAGA:** Dopuszczalna wartość pojedynczej Transakcji w Systemie produkcyjnym wynosi odpowiednio:<BR>\- dla PBL - min. 0.01 PLN, max. 100000.00 PLN (lub do wysokości ustalonej przez Bank wydający instrument płatniczy)<BR>\- dla Kart płatniczych – min. 0.10 PLN, max. 100000.00 PLN (lub do wysokości indywidualnego limitu pojedynczej transakcji w Banku wydawcy Karty)<BR>\- dla Szybkich przelewów - min. 0.01 PLN, max. 100000.00 PLN (lub do wysokości indywidualnego limitu pojedynczej transakcji w Banku dla przelewu wewnątrzbankowego)<BR>\- dla BLIK - min. 0.01 PLN, max. 50000.00 PLN (lub do wysokości indywidualnego limitu pojedynczej transakcji w Banku dla przelewu wewnątrzbankowego)<BR>\- dla OTP – min. 100.00 PLN, max. 2000.00 PLN<BR>\- dla Alior Rat – min. 50.00 PLN, max. 7750.00 PLN_ |
| 6. | transaction -\> currency | TAK | string{1,3} | Waluta transakcji. |
| 7. | transaction -\> gatewayID | TAK | string{1,5} | Identyfikator Kanału Płatności, za pomocą, którego klient uregulował płatność. |
| 8. | transaction -\> paymentDate | TAK | string{14} | Moment zautoryzowania transakcji, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET) |
| 9. | transaction -\> paymentStatus | TAK | enum | Status autoryzacji transakcji. Przyjmuje wartości (przejścia statusów identyczne z analogicznym polem w ITN):<BR>**PENDING** – transakcja rozpoczęta<BR>**SUCCESS** – poprawna autoryzacja transakcji, Serwis otrzyma środki za transakcję<BR>**FAILURE** – transakcja nie została zakończona poprawnie |
| 10. | transaction -\> paymentStatusDetails | TAK | enum | Szczegółowy status transakcji, wartość może być ignorowana przez Serwis. |
| 11. | transaction -\> startAmount | NIE | amount | Kwota transakcji podana w Linku Płatności (nie uwzględnia ew. kwoty prowizji naliczonej Klientowi). Suma prowizji Klienta i startAmount znajduje się w polu amount, gdyż jest to wynikowa wartość transakcji). Jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00; maksymalna długość: 14 cyfr przed kropką i 2 po kropce. |
| 12. | transaction -\> invoiceNumber | NIE | string{1,100} | Numer dokumentu finansowego w serwisie. |
| 13. | transaction -\> customerNumber | NIE | string{1,35} | Numer klienta w serwisie. |
| 14. | transaction -\> customerEmail | NIE | string{1,60} | Adres email klienta. |
| 15. | transaction -\> customerPhone | NIE | string{9-15} | Numer telefonu użytkownika. |
| 16. | recurringData -\> recurringAction | NIE | string{1,100} | Akcja w procesie płatności automatycznej. |
| 17. | recurringData -\> clientHash | TAK | string{1,64} | Identyfikator płatności automatycznej. |
| 18. | recurringData -\> expirationDate | NIE | string{14} | Moment wygaśnięcia ważności płatności automatycznej, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET) |
| 19. | cardData -\> index | NIE | string{1, 64} | Index karty płatniczej używanej w płatności automatycznej (jeśli użyto karty). |
| 20. | cardData -\> validityYear | NIE | string{4} | Ważność karty w formacie YYYY (jeśli użyto karty). |
| 21. | cardData -\> validityMonth | NIE | string{2} | Ważność karty w formacie mm (jeśli użyto karty). |
| 22. | cardData -\> issuer | NIE | string{64} | Wystawca karty, możliwe wartości: <BR> - VISA<BR> - MASTERCARD<BR> - MAESTRO<BR> -  AMERICAN EXPRESS (obecnie nie wspierane)<BR> -  DISCOVER (obecnie nie wspierane)<BR> -  DINERS (obecnie nie wspierane)<BR> -  UNCATEGORIZED (nierozpoznany wystawca) |
| 23. | cardData -\> bin | NIE | string{6} | Pierwsze 6 cyfr numeru karty. |
| 24. | cardData -\> mask | NIE | string{4} | Ostatnie 4 cyfry numeru karty. |
| 25. | blikData -\> appLabel | NIE | string{1,20} | Etykieta konta mobilnego nadawana przez Bank. Można prezentować użytkownikowi celem weryfikacji aplikacji bankowej. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |

**WSKAZÓWKA:** Element **hash** (komunikatu) służy do autentykacji
dokumentu. Wartość tego elementu obliczana jest jako wartość funkcji
skrótu z łańcucha zawierającego sklejone wartości wszystkich pól
dokumentu oraz dołączonego klucza współdzielonego. Opis sposobu
obliczania skrótu znajduje się w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji).

*Odpowiedź na powiadomienie*

W odpowiedzi na powiadomienie oczekiwany jest status HTTP 200 (OK) oraz
tekst w formacie XML (nie kodowany Base64), zwracany przez Serwis
Partnera w tej samej sesji HTTP, zawierający potwierdzenie otrzymania
komunikatu.


Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<confirmationList>
		<serviceID>ServiceID</serviceID>
		<recurringConfirmations>
			<recurringConfirmed>
				<clientHash>ClientHash</clientHash>
				<confirmation>Confirmation</confirmation>
			</recurringConfirmed>
		</recurringConfirmations>
		<hash>Hash</hash>
	</confirmationList>
```

## Element confirmation płatności automatycznej

Element **confirmation** służy do przekazania stanu weryfikacji
autentyczności transakcji przez Serwis Partnera. Wartość elementu
wyznaczana jest przez sprawdzenie poprawności wartości parametru
**serviceID**, porównanie wartości pól **orderID** i **amount** w
komunikacie powiadomienia oraz w komunikacie rozpoczynającym transakcję,
a także weryfikację zgodności wyliczonego skrótu z parametrów komunikatu
z wartością przekazaną w polu hash komunikatu.

Przewidziano dwie wartości elementu **confirmation**:

a)  **CONFIRMED** – wartości parametrów w obu komunikatach oraz parametr hash są zgodne – transakcja autentyczna;

b)  **NOTCONFIRMED** – wartości w obu komunikatach są różne lub niezgodność hash – transakcja nieautentyczna;

**WSKAZÓWKA:** Element **hash** (w odpowiedzi na komunikat) służy do autentykacji odpowiedzi i liczony jest z wartości parametrów odpowiedzi.
Opis sposobu obliczania skrótu znajduje się w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji).

W wypadku braku poprawnej odpowiedzi na wysłane powiadomienia, System
płatności online podejmie kolejne próby jego przekazania po upływie
określonego czasu. Serwis Partnera powinien wykonywać własną logikę
biznesową (np. uruchomienie usługi płatności automatycznej, mailingu
itp.) jedynie po pierwszym komunikacie o danym **ClientHash**.

## Powiadomienie o dezaktywacji płatności automatycznej (RPDN)

Po wyłączeniu płatności automatycznej dla danego **ClientHash**,
wysyłany jest dedykowany komunikat w postaci dokumentu XML, jest
protokołem HTTPS (domyślnie port 443), metodą POST, z
parametrem o nazwie **recurring**. Parametr ten zapisany jest
mechanizmem kodowania transportowego Base64.

Format dokumentu (XML)

```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<recurringDeactivation>
		<serviceID>ServiceID</serviceID>
		<recurringData>
			<recurringAction>RecurringAction</recurringAction>
			<clientHash>ClientHash</clientHash>
			<deactivationSource>DeactivationSource</deactivationSource>
			<deactivationDate>DeactivationDate</deactivationDate>
		<recurringData>
		<hash>Hash</hash>
	</recurringDeactivation>
```

Wartości elementów: serviceID, clientHash dotyczące każdej z
deaktywowanych płatności cyklicznych, są identyczne z wartościami
odpowiadających im pól, podanymi w komunikacie RPAN przy rozpoczęciu
danej płatności inicjalizacyjnej.

## Opis zwracanych parametrów

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera, nadawany w trakcie rejestracji usługi, jednoznacznie identyfikuje Serwis Partnera w Systemie płatności online. |
| 2 | recurringData -\> recurringAction | TAK | string{1,100} | Akcja w procesie płatności automatycznych (w tym wypadku wartość DEACTIVATE). |
| 3 | recurringData -\> clientHash | TAK | string{1,64} | Identyfikator płatności automatycznej. |
| 4 | recurringData -\> deactivationSource | TAK | string{1,64} | Przyczyna dezaktywacji płatności automatycznej. Opis ten należy traktować informacyjnie, lista jego dozwolonych wartości jest ciągle powiększana i pojawienie się nowych wartości nie może pociągać za sobą braku akceptacji komunikatu RPDN.<BR><BR>Poniżej aktualne wartości:<BR>- **SERVICE**: zlecone przez Partnerów<BR>- **ACQUIRER**: zlecone przez AP (np. po otrzymaniu informacji o fraudzie)<BR>- **BM_PL**: zlecone przez Klienta na stronie bills.autopay.eu<BR>- **PAYBM**: wynikająca z wygaśnięcia ważności karty.<BR> |
| 5 | recurringData -\> deactivationDate | TAK | string{14} | Moment wyłączenia płatności automatycznej, przekazywany w formacie YYYYMMDDhhmmss. (Czas CET) |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


**UWAGA:** Element hash (komunikatu) służy do autentykacji dokumentu.
Wartość tego elementu obliczana jest jako wartość funkcji skrótu z
łańcucha zawierającego sklejone wartości wszystkich pól dokumentu oraz
dołączonego klucza współdzielonego.

## Potwierdzenie otrzymania komunikatu

W odpowiedzi na powiadomienie oczekiwany jest status HTTP 200 (OKq) oraz
tekst w formacie XML (nie kodowany Base64), zwracany przez Serwis w tej
samej sesji HTTP, zawierający potwierdzenie otrzymania komunikatu.



Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<confirmationList>
		<serviceID>ServiceID</serviceID>
		<recurringConfirmations>
			<recurringConfirmed>
				<clientHash>ClientHash</clientHash>
				<confirmation>Confirmation</confirmation>
			</recurringConfirmed>
		</recurringConfirmations>
		<hash>Hash</hash>
	</confirmationList>
```

## Element Confirmation

Element **confirmation** służy do przekazania stanu weryfikacji
autentyczności operacji przez Serwis. Wartość elementu wyznaczana jest
przez sprawdzenie poprawności wartości parametrów **serviceID** oraz
**clientHash** z podanymi w komunikacie RPAN przy rozpoczęciu danej
płatności inicjalizacyjnej oraz weryfikację zgodności wyliczonego skrótu
z parametrów komunikatu z wartością przekazaną w polu hash.

Przewidziano dwie wartości elementu **confirmation**:

a)  **CONFIRMED** – wartości parametrów są poprawne oraz parametr hash
są zgodne – operacja autentyczna

b)  **NOTCONFIRMED** – wartości w obu komunikatach są niepoprawne lub
niezgodność hash – operacja nieautentyczna

**UWAGA:** Element hash (w odpowiedzi na komunikat) służy do
autentykacji odpowiedzi i liczony jest z wartości parametrów odpowiedzi.
Wartość tego elementu obliczana jest jako wartość funkcji skrótu z
łańcucha zawierającego sklejone wartości wszystkich pól dokumentu (bez
znaczników) oraz dołączonego klucza współdzielonego. Opis sposobu
obliczania skrótu znajduje się w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji).

W wypadku braku poprawnej odpowiedzi na wysłane powiadomienia, System
podejmie kolejne próby jego przekazania po upływie określonego czasu.
Serwis powinien wykonywać własną logikę biznesową (np. zatrzymanie
płatności automatycznej, mailing itp.), jedynie po pierwszym komunikacie
RPDN o danym ClientHash.

**WSKAZÓWKA:** Zalecamy zapoznanie się również z częściami *Monitoring
komunikacji ITN/ISTN/IPN/RPAN/RPDN* i *Schemat ponawiania komunikatów*
*ITN/ISTN/IPN/RPAN/RPDN*.

[Schemat ponawiania powiadomień](retry-policy.md).
