# Pay by Link i szybkie przelewy



## Zamówienie danych do przelewu w transakcji typu Szybki Przelew

### Opis zamawiania danych do przelewu w transakcji typu Szybki Przelew

Szybki Przelew to forma płatności, która wymaga od Klienta samodzielnego
przepisania danych do przelewu dostarczanych przez System. Jakiego typu
jest dany Kanał płatności, mówi parametr gatewayType w odpowiedzi na
wywołanie usługi „Odpytywanie o listę aktualnie dostępnych Kanałów
Płatności". Dane do przelewu mogą być Klientowi wyświetlone:

-   na stronie AP (realizacja transakcji w oparciu o standardowy model
    startu transakcji opisany w części [Rozpoczęcie transakcji](../payment-flow/start-transaction.md#rozpoczęcie-transakcji))

-   w serwisie Partnera (realizację transakcji bez przekierowania
    Klienta na stronę AP opisano poniżej)

### Wywołanie

Dla poprawnego nadania komunikatu należy przesłać backendowo (np.
cURLem) standardowy komunikat startu transakcji, z nagłówkiem
\'BmHeader\' o wartości: *\'pay-bm\'* (w całości nagłówek powinien
prezentować się następująco *\'BmHeader: pay-bm\'*). W przypadku
błędnego zdefiniowania nagłówka lub jego braku, komunikat zostanie
błędnie odczytany. Dodatkowo zalecane jest przekazywanie parametru
CustomerIP zgodnie z opisem w punkcie IP użytkownika (potrzebne do
procesów reklamacyjnych, sprawozdawczych) oraz **wymagane** jest
przekazanie niezerowego parametru **GatewayID** (o **gatewayType „Szybki
Przelew"**).

Implementacja startu transakcji w tle (PHP)
```php
	$data = array(
	 'ServiceID' => '100047',
	 'OrderID' => '20150723144517',
	 'Amount' => '1.00',
	 'Description' => 'test bramki',
	 'GatewayID' => '71',
	 'Currency' => 'PLN',
	 'CustomerEmail' => 'test@bramka.pl',
	 'CustomerIP' => '127.0.0.0',
	 'Title' => 'Test title',
	 'ValidityTime' => '2016-12-19 09:40:32',
	 'LinkValidityTime' => '2016-07-20 10:43:50',
	 'Hash' => 'e627d0b17a14d2faee669cad64e3ef11a6da77332cb022bb4b8e4a376076daaa'
	);

	$fields = (is_array($data)) ? http_build_query($data) : $data;

	$curl = curl_init('https://{host_bramki}/test_ecommerce');
	curl_setopt($curl, CURLOPT_HTTPHEADER, array('BmHeader: pay-bm'));
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

### Odpowiedź – dane do przelewu

W przypadku płatności tego typu, System generuje komplet danych
potrzebnych do wykonania wewnątrzbankowego (a więc szybkiego) przelewu
na rachunek bankowy AP. Dane te umieszczane są w odpowiedzi na start
transakcji, w dokumencie xml.

Odpowiedź systemu płatności na start transakcji (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<transaction>
		<receiverNRB>47 1050 1764 1000 0023 2741 0516</receiverNRB>
		<receiverName>Autopay</receiverName>
		<receiverAddress>81-718 Sopot, ul. Powstańców Warszawy 6</receiverAddress>
		<orderID>9IMYEH2AV3</orderID>
		<amount>1.00</amount>
		<currency>PLN</currency>
		<title>9IMYEH2AV3  - weryfikacja rachunku</title>
		<remoteID>9IMYEH2AV3</remoteID>
		<bankHref>https://ssl.bsk.com.pl/bskonl/login.html</bankHref>
		<hash> fe685d5e1ce904d059eb9b7532f9e06a64c34c1ea9fcf29b62afefdb7aad7b75 </hash>
	</transaction>
```


### Lista zwracanych parametrów dla odpowiedzi

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | receiverNRB | TAK | string{32} | Numer rachunku odbiorcy przelewu (AP). |
| 2 | receiverName | TAK | string{1,100} | Nazwa odbiorcy przelewu (AP). |
| 3 | receiverAddress | TAK | string{1,100} | Dane adresowe odbiorcy przelewu (AP). |
| 5 | orderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| 6 | amount | TAK | amount | Kwota transakcji. Jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00; maksymalna długość: 14 cyfr przed przecinkiem i 2 po przecinku. _**UWAGA:** Dopuszczalna wartość pojedynczej Transakcji w Systemie produkcyjnym wynosi min. 0.01 PLN, max. 100000.00 PLN (lub do wysokości indywidualnego limitu pojedynczej transakcji w Banku dla przelewu wewnątrzbankowego)._ |
| 7 | currency | TAK | string{1,3} | Waluta transakcji. |
| 8 | title | TAK | string{1,140} | Pełny tytuł przelewu (ID wraz z doklejonym polem Description ze startu transakcji). |
| 9 | remoteID | TAK | string{1,20} | Unikalny identyfikator przelewu nadany w Systemie AP. |
| 10 | bankHref | TAK | string{1,100} | Adres logowania w systemie bankowości internetowej, który można wykorzystać do stworzenia przycisku „Przejdź do banku". |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


**UWAGA:** Powyższe informacje należy wykorzystać do wyświetlenia danych
przelewowych oraz przekierowania użytkownika do strony logowania banku.

## Przelewy do Urzędu Skarbowego

### Opis

System umożliwia realizację przelewów do Urzędu Skarbowego.

## Walidacja tytułów przelewów do Urzędu Skarbowego

nazwa walidatora:  US\_TITLE\_VALIDATOR

Title=&quot;/TI/\_\_\_\_\_\_\_\_\_\_\_\_\_\_/OKR/\_\_\_\_\_\_/SFP/\_\_\_\_\_\_\_/TXT/\_\_\_\_\_\_\_\_\_{idtransremote\_out}&quot;, gdzie:

a) TI: oznacza identyfikator podatnika (P dla PESEL lub N dla NIP lub R dla REGON).  Wpływa na:

- Y w NRB (0/1)
- X w NRB, który jest PESELem, lub NIPem.

b) OKR: Rok, typ okresu i numer okresu, za który dokonywana jest płatność podatku

- R – rok w formacie dwucyfrowym
- P - półrocze
- K - kwartał
- M - miesiąc
- D - dekada
- J - dzień
- 0 (zero) - dla należności niezwiązanych z okresem rozliczeniowym

Numer okresu:

- dla R znaki 4-7 nie powinny być wypełnione
- dla P znaki 4-5 = 01 lub 02, znaki 6-7 nie wypełnione
- dla K znaki 4-5 = 01,02,03 lub 04, znaki 6-7 nie wypełnione
- dla M znaki 4-5 = 01 do 12, znaki 6-7 nie wypełnione
- dla D znaki 4-5 = 01,02 lub 03, znaki 6-7 = 01 do 12
- dla J znaki 4-5 = 01 do 31, znaki 6-7 = 01 do 12

c) SFP: symbol formularza płatności:

| **symbol formularza płatności** | **mikrorachunek** | **Budowa NRB, na który należy przekazać kwotę** | **Okres, za który dokonywana jest płatność podatku** |
| --- | --- | --- | --- |
| CIT | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| CIT-10Z | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| CIT-11R | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| CIT-6R | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| CIT-6AR | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| CIT-8 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| CIT-8A | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| CIT-8B | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| CIT-9R | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| CIT-CFC | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| KP | NIE | XX XXXX XXXX XXXX XXX0 0007 0000 |  |
| SD | NIE | XX XXXX XXXX XXXX XXX0 0007 0000 | Należności niezwiązane z okresem rozliczeniowym. |
| SD-2 | NIE | XX XXXX XXXX XXXX XXX0 0007 0000 | Należności niezwiązane z okresem rozliczeniowym. |
| PCC | NIE | XX XXXX XXXX XXXX XXX0 0007 0000 | Należności niezwiązane z okresem rozliczeniowym. |
| PCC-2 | NIE | XX XXXX XXXX XXXX XXX0 0007 0000 | Należności niezwiązane z okresem rozliczeniowym. |
| PCC-3 | NIE | XX XXXX XXXX XXXX XXX0 0007 0000 | Należności niezwiązane z okresem rozliczeniowym. |
| PIT | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| PIT-28 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-36 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-36L | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-37 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-38 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-39 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-4 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | miesięczny |
| PIT-4R | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PPL | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| PIT-7 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| PIT-8A | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | miesięczny |
| PIT-8AR | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | roczny |
| PIT-CFC | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| PU1 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| PPD | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| PPE | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| PPW | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| VAT-7 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | miesięczny |
| VAT-7K | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | kwartalny. Od 1.10 te symbole będą zastąpione przez JPK_V7K oraz JPK_V7M. |
| VAT-7D | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | kwartalny |
| VAT-8 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | miesięczny |
| VAT-9M | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | miesięczny |
| VAT-10 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| VAT-12 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| VAT-14 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| VAP-1 | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| VAI | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| VAT-IM | TAK | LK 1010 0071 222Y XXXX XXXX XXXX |  |
| VAT-Z | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |
| VAT-In | TAK | LK 1010 0071 222Y XXXX XXXX XXXX | Należności niezwiązane z okresem rozliczeniowym. |

d) TXT: dodatkowy tekst (max 29 znaków)

e) {idtransremote\_out} →  stała tytułu, która musi znajdować się na końcu ciągu. W to miejsce nie należy wklejać żadnych dodatkowych wartości.
