# Przedtransakcja

<!-- TODO MIG-029: Źródło: README-2.md:1313–1323. Problem: Przykład PHP zawiera typograficzne cudzysłowy OrderID i Hash bez cudzysłowów. Wymagana decyzja/materiał: Potwierdzić poprawny kod; nie uruchamiać przykładu bez korekty uzgodnionej z właścicielem API. -->

> TODO MIG-029: Przykład PHP zawiera typograficzne cudzysłowy OrderID i Hash bez cudzysłowów. Potwierdzić poprawny kod; nie uruchamiać przykładu bez korekty uzgodnionej z właścicielem API.

## Przedtransakcja

### Opis Przedtransakcji

Przedtransakcja rozszerza standardowy model rozpoczęcia transakcji o
obsługę określonych potrzeb:

-   zamówienia linku do płatności na podstawie przesłanych parametrów

-   obciążenia Klienta (jeśli nie jest wymagana dodatkowa autoryzacja
    dokonana przez Klienta)

-   zweryfikowania poprawności linku płatności, zanim Klient zostanie
    przekierowany do Systemu – wywołanie powoduje walidację parametrów
    i konfiguracji Systemu

-   skrócenia linka płatności – zamiast kilku/kilkunastu parametrów,
    link zostaje skrócony do dwóch identyfikatorów

-   ukrycia danych wrażliwych parametrów linku transakcji –
    przedtransakcja odbywa się backendowo, a link do kontynuacji
    transakcji nie zawiera danych wrażliwych, a jedynie identyfikatory
    kontynuacji

-   użycia SDK mobilnego w wariancie mieszanym – start transakcji
    wykonuje backend aplikacji mobilnej, a nie samo SDK z użyciem tokena
    transakcyjnego

**WSKAZÓWKA:** Szczegóły na temat wariantów SDK w [Dokumentacji SDK](../../mobile-sdk/README.md).

Szczególne przypadki użycia Przedtransakcji, to obciążenia:

-   BLIK 0\
    Aby użyć tej usługi należy podać **GatewayID=509** oraz przekazać
    kod autoryzacji transakcji w parametrze **AuthorizationCode**.

-   BLIK 0 OneClick

-   Obciążenia „Płatności automatycznej"\
    Aby użyć tej usługi należy podać jeden z **GatewayID** o
    **gatewayType="Płatność automatyczna"** oraz niezbędne parametry.

-   Autoryzacje poprzez portfele Visa\
    Aby użyć tej usługi należy podać **GatewayID=1511** oraz przekazać
    zakodowany token w parametrze **PaymentToken**. W przypadku braku
    tokena, autoryzacja odbędzie się na stronie Systemu.

-   Autoryzacje poprzez portfele Google Pay

**UWAGA:** Usługa umożliwia obciążenie karty zapisanej w portfelu Klienta bez przekierowania do Systemu. Często następuje wymuszenie dodatkowej autoryzacji w postaci 3DS (domyślne zachowanie środowiska testowego, które można przekonfigurować).

W modelu **Whitelabel** należy zintegrować się zgodnie z opisem, a
następnie podać **GatewayID=1512** oraz zakodowany token w parametrze
**PaymentToken**. W przypadku braku tokena (lub model inny, niż
**Whitelabel**) wystarczy podać **GatewayID=1512** - autoryzacja
odbędzie się na stronie Systemu.

-   Autoryzacje poprzez portfele Apple Pay\
    Aby użyć tej usługi należy podać **GatewayID=1513**. Autoryzacja
    odbędzie się na stronie Systemu.

-   Autoryzacja poprzez natywną formatkę SDK mobilnego

**UWAGA:** Usługa umożliwia obciążenie karty, której szczegóły podano na
bezpiecznej formatce kartowej SDK, a sam start transakcji wykonuje
backend aplikacji mobilnej.

Oprócz odpowiedniego GatewayID - 1500 dla płatności jednorazowej lub
1503 dla aktywacji płatności automatycznej (oraz innych parametrów) –
należy podać uzyskany z SDK PaymentToken oraz parametr
WalletType=SDK_NATIVE (opis w części [Rozpoczęcie transakcji z dodatkowymi parametrami](../transaction-data/additional-parameters.md#rozpoczęcie-transakcji-z-dodatkowymi-parametrami))

### Wywołanie Przedtransakcji

Obowiązkowym elementem w przypadku przedtransakcji jest przesłanie
backendowo (używając np. cURL) standardowego komunikatu startu
transakcji (patrz [Rozpoczęcie transakcji](../payment-flow/start-transaction.md#rozpoczęcie-transakcji)), z nagłówkiem \'BmHeader\' o
wartości: \'pay-bm-continue-transaction-url\':

Przykład nagłówka

***\'BmHeader: pay-bm-continue-transaction-url\'*)**

Dodatkowo zalecane jest przekazywanie parametru **CustomerIP** (do celów
reklamacyjnych, sprawozdawczych).

Przykład startu Przedtransakcji (PHP)
```php
	$data = array(
	 'ServiceID' => '100047',
	 'OrderID' => ‘20161017143213’,
	 'Amount' => '1.00',
	 'Description' => 'test bramki',
	 'GatewayID' => '0',
	 'Currency' => 'PLN',
	 'CustomerEmail' => 'test@bramka.pl',
	 'CustomerIP' => '127.0.0.0',
	 'Title' => 'Test title',
	'Hash' => 0c5ca136e8833e40efbf42a4da7c148c50bf99f8af26f5c9400681702bd72056
	);

	$fields = (is_array($data)) ? http_build_query($data) : $data;

	$curl = curl_init('https://{host_bramki}/test_ecommerce');
	curl_setopt($curl, CURLOPT_HTTPHEADER, array('BmHeader: pay-bm-continue-transaction-url'));
	curl_setopt($curl, CURLOPT_POSTFIELDS, $fields);
	curl_setopt($curl, CURLOPT_POST, 1);
	curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, true);
	$curlResponse = curl_exec($curl);
	$code = curl_getinfo($curl, CURLINFO_HTTP_CODE);
	$response = curl_getinfo($curl);
	curl_close($curl);

	echo htmlspecialchars_decode($curlResponse);
```

## Odpowiedź na Przedtransakcję – link do kontynuacji transakcji

W przypadku poprawnej walidacji parametrów (i konfiguracji) oraz
potrzeby wykonania przez Klienta dodatkowej akcji (wybrania kanału
płatności - jeśli podano **GatewayID=0**, wykonania/zatwierdzenia
przelewu, podania kodu CVC/CVV, wykonania 3DS) – zostanie zwrócony XML
z linkiem kontynuacji transakcji.

Przykład pliku z linkiem kontynuacji transakcji (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
		<transaction>
		<status>PENDING</status>
		<redirecturl>
		https://{host_bramki}/payment/continue/96VSD39Z6E/L6CGP5BH
		</redirecturl>
		<orderID>20180824105435</orderID>
		<remoteID>96VSD39Z6E</remoteID>
		<hash>
		1c6eae2127f0c3f81fbed3b6372f128040729a4d4e562fb696c22e0db68dbbe1
		</hash>
	</transaction>
```

### Obiekt transaction dla Przedtransakcji

Obiekt **transaction** reprezentuje wpływ lub wypłatę środków z konta
AP, np. zrealizowany zakup lub zwrot.

### Atrybuty obiektu transaction dla Przedtransakcji

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | status | TAK | string{1,32} | Status transakcji. W tym wypadku stała PENDING. |
| 2 | redirecturl | TAK | string{1,100} | Adres do kontynuacji transakcji rozpoczętej przez komunikat przedtransakcji. |
| 3 | orderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| 4 | remoteID | TAK | string{1,20} | Unikalny identyfikator transakcji nadany w Systemie AP. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez herwis**. |

### Odpowiedź na Przedtransakcję – brak kontynuacji transakcji

W przypadku niepoprawnej walidacji lub nieskutecznego obciążenia nie
jest generowany link kontynuacji. Zwracany jest (w tej samej sesji HTTP)
tekst w formacie XML, informujący o statusie przetwarzania żądania.


Przykład statusu przetwarzania żądania (XML)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<transaction>
	<orderID>OrderID</orderID>
	<remoteID>RemoteID</remoteID>
	<confirmation>ConfStatus</confirmation>
	<reason>Reason</reason>
	<blikAMList>
		<blikAM>
			<blikAMKey>Klucz1</blikAMKey>
			<blikAMLabel>Etykieta1</blikAMLabel>
		</blikAM>
		<blikAM>
			<blikAMKey>Klucz2</blikAMKey>
			<blikAMLabel>Etykieta2</blikAMLabel>
		</blikAM>
	</blikAMList>
	<paymentStatus>PaymentStatus</paymentStatus>
	<hash>Hash</hash>
</transaction>
```


### Wynik Przedtransakcji

Parametry zwracane dla wyniku Przedtransakcji.

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.


| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | orderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. <BR> <strong>Wymagany dla confirmation=CONFIRMED.</strong> |
| 2 | remoteID | TAK | string{1,20} | Unikalny identyfikator transakcji nadany w Systemie AP. <BR> <strong>Wymagany dla confirmation=CONFIRMED.</strong> |
| 3 | confirmation | TAK | string{1,100} | Status potwierdzenia przyjęcia zlecenia. <BR> Może przyjmować dwie wartości: <BR> - CONFIRMED – operacja powiodła się. <BR> <strong>UWAGA: Nie oznacza obciążenia.</strong> <BR> - NOTCONFIRMED – operacja nie powiodła się. |
| 4 | reason | NIE | string{1,1000} | Wyjaśnienie przyczyny odrzucenia zlecenia (dla confirmation=NOTCONFIRMED), jeśli jest ona dostępna. |
| 5 | blikAMList | NIE | string{1,10000} | Lista dostępnych aplikacji mobilnych banków w opcji BLIK 0 OneClick (dla confirmation=NOTCONFIRMED oraz reason=ALIAS_NONUNIQUE). |
| 6 | paymentStatus | NIE | enum | Status autoryzacji transakcji, przyjmuje wartości: <BR> - PENDING – transakcja rozpoczęta <BR> - SUCCESS – poprawna autoryzacja transakcji <BR> - FAILURE – transakcja nie została zakończona poprawnie |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części <a href="../security/hashing.md#bezpieczeństwo-transakcji">Bezpieczeństwo transakcji</a>. <strong>Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis. <BR> Wymagany dla confirmation=CONFIRMED.</strong> |

Format dla blikAMList:

```xml
<blikAM>
		<blikAMKey>Klucz1</blikAMKey>
		<blikAMLabel>Etykieta1</blikAMLabel>
		</blikAM>
		…
		<blikAM>
		<blikAMKey>KluczN</blikAMKey>
		<blikAMLabel>EtykietaN</blikAMLabel>
		</blikAM>
```



### Poprawna walidacja parametrów

W przypadku poprawnej walidacji parametrów (i konfiguracji) oraz braku
potrzeby wykonania przez Klienta dodatkowej akcji – zwracane jest
potwierdzenie zlecenia obciążenia.

Ma to miejsce w przypadkach, gdzie dane są wystarczające do wykonania
obciążenia dla danego kanału płatności, na przykład: BLIK 0 bez
wymaganego kodu BLIK (ani wskazania aliasu aplikacji mobilnej banku),
płatność cykliczna, płatność Kartą OneClick bez wymaganego CVC/CVV/3DS.

Result

confirmation=CONFIRMED

**Niepoprawna walidacja parametrów**

W przypadku niepoprawnej walidacji parametrów (i konfiguracji) –
zwracany jest błąd.

Result

confirmation=NOTCONFIRMED

Błąd może być również zwrócony w przypadku synchronicznej odpowiedzi z Kanału Płatności (np. błąd specyficzny dla próby inicjalizacji płatności automatycznej BLIK, tj. reason= RECURRENCY_NOT_SUPPORTED).

**UWAGA:** Błąd może być również zwrócony w przypadku synchronicznej odpowiedzi z Kanału Płatności (np. błąd specyficzny dla próby inicjalizacji płatności automatycznej BLIK, tj. *reason=RECURRENCY_NOT_SUPPORTED*). Innym znanym przypadkiem jest też błąd walidacji adresu podanego w parametrze startowym CustomerEmail (INVALID_EMAIL).


### Obsługa odpowiedzi dla Przetransakcji

| Status potwierdzenia przyjęcia zlecenia (confirmation) | Status Płatności (paymentStatus) | Opis zachowania Partnera |
| --- | --- | --- |
| CONFIRMED | SUCCESS | Przyjęto transakcję do przetwarzania, status poprawny. <BR> Nie należy ponawiać próby obciążenia. <BR> Można wyświetlić potwierdzenie płatności, ale procesy biznesowe powinny być wstrzymane do potwierdzenia w ITN (zostanie ono wysłane po otrzymaniu przez AP poprawnego statusu transakcji z Kanału Płatności). |
| CONFIRMED | FAILURE | Przyjęto transakcję do przetwarzania, status niepoprawny. <BR> Można ponowić próbę obciążenia z tym samym **OrderID**. Po otrzymaniu przez AP statusu transakcji z Kanału Płatności, wysłany zostanie komunikat ITN. _**UWAGA:** Nie można ponawiać próby obciążenia z tym samym **OrderID**, jeśli w trakcie integracji uzgodniony zostanie model blokowania przez System startów transakcji z tym samym **OrderID**. Domyślnie zachowanie przez Partnera unikalności **OrderID** jest tylko zaleceniem i nie podlega weryfikacji w starcie transakcji._ |
| CONFIRMED | PENDING | Przyjęto transakcję do przetwarzania, ale nieznany jest jeszcze jej status. Nie należy ponawiać próby obciążenia. Dalsza obsługa, jak w przypadku Timeout. |
| NOTCONFIRMED | - | Nie zlecono transakcji (przyczyna wskazana w węźle reason). Można ponowić próbę obciążenia z tym samym OrderID. <BR> Komunikat ITN nie powinien nigdy być wysłany. |
| Timeout (lub inna odpowiedź, jak niepoprawna struktura, brak wymaganych pól, inny status potwierdzenia) | - | Należy poczekać na ITN do terminu ważności transakcji (w tym celu zaleca się stosowanie krótkiego czasu ważności, np. 15 min), informując Klienta o wyniku w ramach odrębnego procesu (mail/sms). Po tym czasie zaleca się odpytać o status transakcji (**transactionStatus**). Jeśli metoda zwróci brak zarejestrowanej transakcji (lub same statusy płatności **FAILURE**), można ponowić zlecenie obciążenia z takim samym **OrderID**. <BR> <BR> Alternatywnie można spróbować unieważnić transakcję, przyśpieszając tym samym proces uzyskiwania ostatecznego statusu transakcji i ew. proces ponowienia komunikatu startu transakcji. Należy w tym celu użyć usługi anulowania transakcji (**transactionCancel**) oraz potwierdzić jej działanie poprzez odpytanie o status transakcji (jak opisano wyżej). |
