# Apple Pay



## Apple Pay

Implementacja Apple Pay na stronie sklepu.

Prośba o kontakt z produktem infrastruktury płatniczej - potrzebne wsparcie IT.

1. Utworzenie konta przez Partnera i uzyskanie certyfikatu przetwarzania płatności zgodnie z [dokumentem **Configure Apple Pay (iOS, watchOS)**](https://help.apple.com/developer-account/#/devb2e62b839?sub=devf31990e3f)

` `- **certyfikat komunikacyjny** – do tzw. przedstawienia się – *merchant identifier*

` `- **certyfikat obciążający** – *payment processing certificate*

2. Implementacja Web zgodnie z [dokumentem **Apple Pay on the Web**](https://developer.apple.com/documentation/apple_pay_on_the_web/)

3. Przygotowanie 2 endpointów po stronie Partnera, w domenie zgłoszonej w Apple (z wykorzystaniem 2 certyfikatów od Apple):

` `- do rozpoczęcia sesji

` `- do obciążenia Klienta na podstawie tokena od Apple

WSKAZÓWKA: safari (przeglądarka Klienta) komunikuje się z endpointem zwracającym sesję (o którym mowa powyżej), po czym Apple odpytuje się o sesję Autopay.

4. Przetwarzanie płatności Apple Pay

- W ramach rejestracji usługi w Apple wygenerować swój certyfikat *merchant identity.*
- Wygenerować certyfikat *payment processing* na bazie certyfikatu dostarczonego przez AP CSR

UWAGA: Certyfikaty AP CSR dla acceptu i dla produkcji różnią się).

- Po wykorzystaniu go w procesie rejestracji Apple dostarczyć AP certyfikat podpisany przez Apple i wysłać poprzez [formularz Autopay](https://developers.autopay.eu/kontakt)

  WSKAZÓWKA: Klient powinien podać kraj, miasto, domenę strony www, email osoby kontaktowej.

- W ramach realizacji płatności na stronie Partnera, rozpocząć sesję API Apple.
- Następnie zwrócić Autopay token w parametrze startowym PaymentToken.

UWAGA: Odszyfrowanie tokena to obowiązek AP.

Format payment tokena: wycinek obiektu w formacie json, który zwraca api ApplePay:
```text
  EncryptedPaymentData {
	  String version;
	  String data;
	  String signature;
	  Header header;
  }
  Header {
	  String ephemeralPublicKey;
	  String publicKeyHash;
	  String transactionId;
	  String applicationData;
  }
  
```

UWAGA: Przy wysyłce ApplePayPaymentRequest, trzeba uzupełnić pole applicationData o wartość orderId zakodowaną Base64, zgodnie z opisem w [dokumencie applicationData](https://developer.apple.com/documentation/apple_pay_on_the_web/applepaypaymentrequest/2577137-applicationdata).
