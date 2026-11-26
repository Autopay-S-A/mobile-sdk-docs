# Transakcje leasingowe B2B (VAS Payer Paywall)

## Rozpoczęcie transakcji leasingowych B2B w modelu koszykowym (model VAS Payer Paywall)

### Opis modelu

Model transakcji B2B (Business To Business) w modelu koszykowym (VAS Payer Paywall) rozszerza standardowe [Rozpoczęcie transakcji](../podstawowy-proces-platnosci/start-transaction.md) o dodatkową płatną usługę dla płatnika (usługa VAS Payer Paywall). Transakcję rozpoczyna się analogicznie do standardowego startu transakcji – jedyną różnicą jest inny adres usługi: `/vas-paywall-service/initiate-payment`. Transakcja składa się z koszyka dwóch odrębnych opłat, a jej dalszy przebieg jest standardowy.

{% hint style="warning" %}
**UWAGA:** Usługa musi zostać uzgodniona z opiekunem biznesowym oraz włączona dla Serwisu Partnera. Wywołanie dla Serwisu bez aktywnej konfiguracji zakończy się błędem. Usługa jest dostępna tylko dla modelu biznesowego B2B, w którym Partner (Przedsiębiorca) świadczy usługę innemu Przedsiębiorcy, i jest ograniczona do dedykowanych obszarów biznesowych (leasing).
{% endhint %}

Adres:

* [https://{host\_bramki}/vas-paywall-service/initiate-payment](https://{host_bramki}/vas-paywall-service/initiate-payment)

Zasady kodowania wartości oraz rozróżniania wielkości liter są takie same jak w standardowym rozpoczęciu transakcji.

### Lista parametrów

Obowiązują parametry [Rozpoczęcia transakcji](../dane-transakcji/parameters.md) oraz parametry opisane w części [Rozpoczęcie transakcji z dodatkowymi parametrami](../dane-transakcji/additional-parameters.md#rozpoczęcie-transakcji-z-dodatkowymi-parametrami). Poniżej zestawiono wyłącznie te parametry, których obsługa w tym modelu różni się od standardowej.

| nazwa | wymagany | opis |
| ----- | -------- | ---- |
| Amount | TAK | Kwota transakcji **bez** opłaty za usługę dodatkową (VAS Payer Paywall). Opłatę dolicza System, dlatego Klientowi prezentowana jest kwota wyższa od wartości przekazanej w tym parametrze. |
| CustomerEmail | TAK | <p>Adres email Klienta, wykorzystywany m.in. do wystawienia faktury za usługę dodatkową (VAS Payer Paywall).<br>Jeśli parametr nie zostanie przekazany, Klient poda adres na stronie AP – bez poprawnego adresu przejście do płatności nie jest możliwe.</p> |
| GatewayID | TAK | W tym modelu parametr nie pomija strony wyboru Kanałów Płatności. Klient zawsze zobaczy stronę AP z podsumowaniem transakcji i wysokością opłaty. |
| PayerNip, PayerFirmName, PayerStreet, PayerHouse, PayerFlat, PayerCity, PayerPostCode, PayerCountryCode | NIE | <p>Dane płatnika, na podstawie których wystawiana jest faktura za usługę VAS Payer Paywall – opis w części <a href="../dane-transakcji/additional-parameters.md#rozpoczęcie-transakcji-z-dodatkowymi-parametrami">Rozpoczęcie transakcji z dodatkowymi parametrami</a>.<br>Jeśli parametry nie zostaną przekazane, Klient poda te dane na stronie AP.</p> |

### Przykład rozpoczęcia transakcji

W przykładzie przyjęto, że dla Serwisu Partnera opłata za usługę VAS Payer Paywall wynosi 5.00 PLN (przykładowa wartość).

Adres:

* [https://{host\_bramki}/vas-paywall-service/initiate-payment](https://{host_bramki}/vas-paywall-service/initiate-payment)

Parametry:

* ServiceID=100047
* OrderID=20260806143213
* Amount=5000.00
* Currency=PLN
* CustomerEmail=test@bramka.pl

```
	Hash=0c5ca136e8833e40efbf42a4da7c148c50bf99f8af26f5c9400681702bd72056
```

Transakcja jest transakcją koszykową, która składa się z dwóch odrębnych opłat. Do kwoty transakcji Partnera doliczana jest opłata za usługę dodatkową (VAS Payer Paywall), wobec czego Klientowi prezentowana jest kwota 5005.00 PLN. Z tą samą kwotą dostarczony zostanie komunikat ITN – w polu **amount** znajdzie się wartość 5005.00, a kwota z rozpoczęcia transakcji dostępna będzie w opcjonalnym polu **startAmount** (patrz [Pełna lista dodatkowych pól w komunikacie ITN/IPN](../powiadomienia/itn.md#pełna-lista-dodatkowych-pól-w-komunikacie-itnipn)).

### Odpowiedź na rozpoczęcie transakcji

Domyślnie zwracane jest przekierowanie HTTP 303 (_See Other_) z nagłówkiem **Location**, wskazującym stronę AP, na której Klient wybiera Kanał Płatności i widzi podsumowanie transakcji wraz z wysokością opłaty.

Przykład odpowiedzi dla standardowego rozpoczęcia transakcji:

```http
HTTP/1.1 303 See Other
Location: https://{host_bramki}/web/{context}/start/9f2c1ab7d4e84c0fa1b3
```

Usługa obsługuje również model [Przedtransakcji](pretransaction.md) – w tym celu należy przesłać nagłówek HTTP `BmHeader` o wartości `pay-bm-continue-transaction-url` (w całości: `BmHeader: pay-bm-continue-transaction-url`). W takim przypadku zwracany jest dokument XML o strukturze opisanej w części [Odpowiedź na Przedtransakcję – link do kontynuacji transakcji](pretransaction.md#odpowiedź-na-przedtransakcję--link-do-kontynuacji-transakcji).

Przykładowa odpowiedź dla Przedtransakcji (XML):

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<transaction>
	<status>PENDING</status>
	<redirecturl>https://{host_bramki}/web/payment/continue/A74X8MDBZV/I1D8H9SW</redirecturl>
	<orderID>20260806143213</orderID>
	<remoteID>A74X8MDBZV</remoteID>
	<hash>7a4c2999de208d2339ae7e81facace756444bb76144d08dafbfad98743f5b347</hash>
</transaction>
```

### Komunikaty błędu

Błędy specyficzne dla tego modelu zwracane są w postaci dokumentu XML, z kodem odpowiedzi HTTP 200. Informację o błędzie należy więc odczytać z treści dokumentu, a nie z kodu odpowiedzi.

| status | przyczyna |
| ------ | --------- |
| INVALID\_HASH | Niepoprawna wartość funkcji skrótu przekazana w parametrze **Hash**. |
| INVALID\_PARAMS\_VALUE | Niepoprawna lub brakująca wartość parametru; niezgodność sumy wartości **subAmount** z parametrem **Amount**; użycie zarezerwowanej nazwy parametru produktu. |
| OTHER\_ERROR | Usługa nie jest włączona dla Serwisu Partnera lub brak aktywnej konfiguracji opłaty za usługę VAS Payer Paywall. |

Status błędu przekazywany jest w elemencie **reason**, a element **confirmation** przyjmuje wartość _NOTCONFIRMED_.

Przykładowa odpowiedź z błędem (XML):

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<transaction>
	<orderID>20260806143213</orderID>
	<confirmation>NOTCONFIRMED</confirmation>
	<reason>INVALID_PARAMS_VALUE</reason>
</transaction>
```

{% hint style="warning" %}
**UWAGA:** W przypadku błędu **INVALID\_HASH** odpowiedź zawiera dodatkowo element **hash**, umożliwiający weryfikację autentyczności komunikatu. Dla pozostałych błędów z powyższej tabeli element **hash** nie jest zwracany.
{% endhint %}

Pozostałe przypadki, w tym walidację parametrów rozpoczęcia transakcji, System obsługuje standardowo – patrz [Komunikaty błędu](../../informacje-dodatkowe/errors.md#komunikaty-błędu).
