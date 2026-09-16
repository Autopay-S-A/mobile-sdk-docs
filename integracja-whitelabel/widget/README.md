# Jak działa Widget Autopay



## Widget Autopay (model WhiteLabel)

Partner który chciałby osadzić część startów transakcji bezpośrednio w na swoim serwisie / w swoim koszyku (w tzw. modelu WhiteLabel), może to zrobić integrując Widget Autopay.
Aktualnie Widget Autopay wspiera zbieranie danych kartowych (w ramach PaywayId `1500`/`1503`) oraz starty Visa Mobile (PaywayId `1523`)

## Autopay WidgetJS SDK

Do osadzenia i komunikowania się z widget'em Autopay należy użyć Autopay WidgetJS SDK.
W skrócie, sprowadzać się to będzie do osadzenia HTML IFRAME z widget'em oraz skonfigurowania JS SDK w celu obsługi komunikatów (event'ów) produkowanych podczas interakcji Cardholder'a z widget'em.
Komunikatem finalnym jest event ze statusem `FORM_SUCCESS` zawierający `paymentToken` niezbędny do backend'owego startu transakcji na API Systemu Płatności Online AP.


## Osadzenie SDK

Poniżej przykłady, jak w prosty sposób, przy użyciu Autopay WidgetJS SDK, osadzić i skomunikować widget, zarówno dla kanałów kartowych jak i dla kanału VisaMobile.

Autopay WidgetJS SDK jest dostępny pod adresem `widget-new/widget-communication.min.js` po umieszczeniu go w sekcji `<head>`

```html
<script src="https://testcards.autopay.eu/widget-new/widget-communication.min.js"></script>
```

uzyskujemy dostęp do obiektu `WidgetConnection`

```javascript
var widgetConfigObject = { ... };
var widget = new WidgetConnection(widgetConfigObject)
```

który po uzupełnieniu o konfigurację w formie obiektu JSON umożliwi pełną komunikację z API Autopay, a w rezultacie zapewni otrzymywanie event'u o statusie `FORM_SUCCESS` z `paymentToken'em`.


## Przykłady konfiguracji

**Przykład konfiguracji dla danych kartowych**

Konfiguracja:

```javascript
{ language: 'pl', amount: 1.23, currency: 'PLN', serviceId: 123456 }
```

Zwrócony PaymentToken:

```javascript
{status: 'FORM_SUCCESS', message: 'ey...9', id: 'OGFlZTYyYTMtN2U2OS00MTU1LTgyNDctNmMwMGI2NjE5ZDQy'}
```

**Przykład konfiguracji dla danych VisaMobile**

Konfiguracja:

```javascript
{ language: 'pl', amount: 1.23, currency: 'PLN', serviceId: 123456, merchantName: 'ShopName' }
```

Zwrócony PaymentToken:

```javascript
{status: 'FORM_SUCCESS', message: 'ey...9', prefix: '48', phoneNumber: '666666666', id: 'OGFlZTYyYTMtN2U2OS00MTU1LTgyNDctNmMwMGI2NjE5ZDQy'}
```


## Szczegółowe omówienie konfiguracji obiektu WidgetConnection

**Język**

Determinuje wersję językową widget'u w jakiej zostanie zaprezentowany.

Nazwa pola: `language`
Format `string`
Wartości: domyślnie `pl`, aktualnie wspierane są następujące języki: `cs`, `de`, `el`, `en`, `es`, `fr`, `hr`, `hu`, `it`, `pl`, `ro`, `se`, `sk`, `sl`, `uk`

**Kwota transakacji**

Kwota transakcji

Nazwa pola: `amount`
Format `float`
Wartości: kwota zapisana w formacie float, czyli np: "1,23 PLN" to `1.23`

amount: 1.23, currency: 'PLN', serviceId: 123456, merchantName: 'ShopName'

**Waluta transakacji**

Waluta transakcji

Nazwa pola: `currency`
Format `string`
Wartości: domyślnie `PLN`, inne waluty w formacie zgodnym z aktualną konfiguracją serwisu


**Numer serwisu**

Numer serwisu otrzymany od Autopay (zależny od środowiska developerskiego)

Nazwa pola: `serviceId`
Format `integer`
Wartości: zazwyczaj sześciocyfrowy


**Typ rekurencji (tylko dla kart)**

Wskazanie rodzaju inicjacji rekurencji
(Tylko dla kanału 1503 związanego z inicjacją rekurencji)

Nazwa pola: `recurringAction`
Format `string`
Wartości: 'INIT_WITH_REFUND', 'INIT_WITH_PAYMENT'

**Nazwa sklepu  (tylko dla VisaMobile)**

Nazwa wyświetlana użykownikowi w powiadomieniu VisaMobile w aplikacji mobilnej banku
(Tylko dla kanału 1523 VisaMobile)

Nazwa pola: `merchantName`
Format `string`
Wartości: Nazwa sklepu

[Wymagania integracyjne i bezpieczeństwo](../requirements.md) · [Widget kartowy](cards.md) · [Widget Visa Mobile](visa-mobile.md)
