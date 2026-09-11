# Google Pay



## Google Pay

### Opis

Google Pay to błyskawiczny i intuicyjny system płatności od Google.
Pozwala on użytkownikowi na przeprowadzenie procesu płatności bez
wypełniania formularza kartowego, ponieważ dane karty są przechowywane
bezpiecznie na serwerach firmy.

Google Pay to produkt umożliwiający uzyskanie zaszyfrowanych danych
karty płatniczej klienta pozwalających na jej obciążenie.

W celu dokonania płatności przez Google Pay należy zapisać kartę
płatniczą na swoim koncie Google, używając jakiejkolwiek platformy
Google (np. kupując aplikacje w Google Play) lub bezpośrednio na stronie
[Google Pay](https://pay.google.com/payments/home#paymentMethods).

**UWAGA:** Usługa wymaga wcześniejszego podpisania umowy z operatorem
kartowym. Po szczegółowe informacje należy zwrócić się do
Działu Biznesu Autopay.


### Różne sposoby integracji

Istnieją dwie możliwości integracji przycisku gPay na stronie merchanta:


### Integracja bez konieczności rejestrowania się w GooglePay jako Merchant

*Nie ma konieczności* rejestrowania się indywidualnie jako Merchant w GooglePay.
Opisana szczegółowo w następnych akapitach konfiguracja pozwala w całości skorzystać ze wsparcia infrastruktury Autopay.
Obowiązkowe wtedy jest użycie w konfiguracji pól `merchantId`, `merchantOrigin` oraz `authJwt` otrzymanych od Autopay.


### Integracja Merchanta indywidualnie zarejestrowanego w GooglePay

Jeśli jednak merchant decyduje się samodzielnie zarejestrować w Konsoli GooglePay i zweryfikować domenę sklepu (pod którą osadzony będzie przycisk gPay) może wówczas w konfiguracji gPay na www, w sekcji `merchantInfo` użyć własnego `merchantId` oraz domeny w polu `merchantOrigin` *pomijając* wartość `authJwt`.


**UWAGA:** Niezależnie od sposobu integracji, we wszystkich przypadkach procesorem płatności pozostaje Autopay, dlatego w sekcji `tokenizationSpecification` zawsze typem integracji powinno być `PAYMENT_GATEWAY` ze wskazaniem Autopay (dawna nazwa BlueMedia)  `'gateway': 'bluemedia'`


**UWAGA:** W przypadku integracji gPay w aplikacji mobilnej konieczne jest zarejstrowanie sie jako Merchant w Konsoli GooglePay, więcej o tym w ostatniej sekcji.


### Integracja na www - schemat komunikacji


Po kliknięciu „Zapłać przez Google Pay" na stronie sklepu pojawia się
formularz Google Pay. Klient potwierdza w nim swoje konto Google i
kartę, którą zamierza zapłacić (na tym etapie może też zmienić kartę na
inną lub dodać nową). Skrypt przekazuje zakodowane dane karty w tle
poprzez funkcję **postMessage**, następnie sklep musi je przyjąć i
zakodować przez funkcję base64 i w końcu wysłać w parametrze
**PaymentToken** wraz z pozostałymi parametrami (danymi transakcji).

Na swojej stronie sklep musi wywołać skrypt udostępniony przez Google z
podmienionymi danymi Procesora płatności.

**WSKAZÓWKA:** Szczegóły w [dokumentacji deweloperskiej Google](https://developers.google.com/pay/api/web/guides/tutorial).

<!-- TODO MIG-050: Źródło: README-2.md: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png. Problem: Brak obrazu/diagramu: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-050: Brak obrazu/diagramu: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.

*Szczegółowy schemat komunikacji i wymiany danych*

### Rejestracja Transakcji Google Pay

1) Sklep na swojej stronie musi wysłać zapytanie do Systemu Płatności Online AP,
   aby pobrać dane potrzebne do realizacji płatności Google Pay
   (**paybmApiResponse**).

**WSKAZÓWKA:** Przykład wysłania zapytania dostępny jest na [GitHubie Autopay](https://github.com/bluepayment-plugin/google-pay-integration-sample/blob/master/sample_pre_transaction.php#L73).

2) Następnie, sklep musi wywołać skrypt udostępniony w [części Samouczek dokumentacji deweloperskiej Google](https://developers.google.com/pay/api/web/guides/tutorial), zawierający:

a) Podmienione dane Procesora płatności:

```javascript
const tokenizationSpecification = {
	type: 'PAYMENT_GATEWAY',
	parameters: {
		'gateway': 'bluemedia',
		'gatewayMerchantId': paybmApiResponse.acceptorId
	}
};
```

b) Dane zwrócone przez System Płatności Online AP przekazane w obiekcie **PaymentDataRequest.merchantInfo**:

```javascript
PaymentDataRequest.merchantInfo = {
	merchantId: paybmApiResponse.merchantId,
	merchantOrigin: paybmApiResponse.merchantOrigin,
	merchantName: paybmApiResponse.merchantName,
	authJwt: paybmApiResponse.authJwt,
};
```

3) Po kliknięciu „Zapłać przez Google Pay" na stronie sklepu pojawia
   się formularz Google Pay. Klient potwierdza w nim swoje konto Google i
   kartę, którą ma zamiar zapłacić (na tym etapie może też zmienić kartę na
   inną lub dodać nową). Skrypt w tle przekazuje zakodowane dane karty,
   które sklep musi przyjąć, a następnie zakodować funkcją Base64 i wysłać
   w parametrze **PaymentToken** wraz z pozostałymi parametrami startowymi
   transakcji (tj. danymi transakcji Systemu Płatności Online AP - szczegóły opisane są w
   części [Rozpoczęcie transakcji z dodatkowymi parametrami](../transaction-data/additional-parameters.md#rozpoczęcie-transakcji-z-dodatkowymi-parametrami)).

**WSKAZÓWKA:** Kompletny przykład integracji z Google Pay dostępny jest
na [GitHubie Autopay](https://github.com/bluepayment-plugin/google-pay-integration-sample/blob/master/sample_pre_transaction.php#L118).

### Informację dodatkowe

Aby zachować integralność estetyczną stylistyki stosowanej na stronie
www oraz w aplikacji mobilnej należy skorzystać ze wskazówek, które
znajdują się w [części Brand Guidelines dokumentacji deweloperskiej
Google](https://developers.google.com/pay/api/web/guides/brand-guidelines)
w przypadku opisów styli oraz przycisków dla stron www, oraz w [części
Tutorial dokumentacji deweloperskiej
Google](https://developers.google.com/pay/api/android/guides/tutorial),
gdzie znajdują się informacje potrzebne przy tworzeniu aplikacji
mobilnej.

### Integracja mobilna (android)

W przypadku implementacji GooglePay w aplikacji mobilnej (przy pomocy Autopay SDK Mobile) Merchant *jest zobowiązany zarajestrować się w Konsoli GooglePay*. Należy to zrobić z tego samego adresu mailowego, za pomocą którego publikuje się aplikację mobilną w Google Play.

Uzyskanego w ten sposób MerchantID *nie trzeba* podawać w konfiguracji gPay (powiązanie aplikacji mobilnej z merchantem gPay odbywa się po adresie mailowym), pole `merchantId` jest wtedy pomijane w konfiguracji.

W takim przypadku w sekcji `merchantInfo` należy podać tylko `merchantName`. Sekcja `GatewayTokenizationSpecification` jest wymagana i musi zostać uzupełniona w podobny sposób jak opisana wyżej konfiguracja pod www.


```java
  private static JSONObject getGatewayTokenizationSpecification() throws JSONException {
    return new JSONObject() {{
      put("type", "PAYMENT_GATEWAY");
      put("parameters", new JSONObject() {{
        put("gateway", "bluemedia");
        put("gatewayMerchantId", "Autopay AcceptorId");
      }});
    }};
  }

  private static JSONObject getMerchantInfo() throws JSONException {
    return new JSONObject().put("merchantName", "Example Merchant");
  } 
```

### FAQ

Na podstawie doświadczeń przygotowaliśmy kilka sugestii pozwalających usprawnić proces integracji i uniknąć po stronie frontendu aplikacji błędów o kodzie `OR_BIBED_06` wyskakujących po kliknięciu w przycisk gPay.


W polu `merchantOrigin` należy wpisać nazwę domeny nie poprzedzoną protokołem

```javascript
"merchantOrigin":"https://online.example.pl/pay-off"   #Niepoprawny zapis
"merchantOrigin":"online.example.pl"                   #Poprawny zapis
```


Dodatkowo domena sklepu podana w konfiguracji ServiceId w Autopay (czyli domena zwracana z endpointu `googlePayMerchantInfo` *musi* być dokładnie tą na której osadzany sie przycisk gPay (google to weryfikuje).


Powyższa domena (również jej odpowiednik na środowisku testowym) powinna być publicznie dostępna oraz mieć ważny/poprawny certyfikat SSL.
