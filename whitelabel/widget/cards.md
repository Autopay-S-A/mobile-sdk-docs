# Widget kartowy

<!-- TODO MIG-039: Źródło: README-2.md:2259–2260. Problem: Nie dostarczono odrębnego załącznika z kartami i scenariuszami testowymi. Wymagana decyzja/materiał: Dostarczyć załącznik testowy widgetu kartowego. -->

> TODO MIG-039: Nie dostarczono odrębnego załącznika z kartami i scenariuszami testowymi. Dostarczyć załącznik testowy widgetu kartowego.

## Widget Kartowy - Przykład implementacji na stronie partnera

**WAŻNE!** [Poniższy przykład kodu HTML](#omówienie-przykładowego-kodu-html-js-widget-kartowy-i-visamobile) powstał gównie w celach poglądowych.
Aby go faktycznie uruchomić na swoim lokalnym komputerze, poniższy plik HTML musi zostać umieszczony pod jakąś lokalną domeną (dowolną, może być `test.local`).
Ten HTML nie może być odpalany w przeglądarce w formie lokalnego pliku ponieważ eventyJS wymieniane pomiędzy IFRAME a stroną są weryfikowane pod kątem zgodności domeny (a wieć jakaś domena musi być obecna).


Poniższa strona ma za zadanie imitować Front Merchanta, pokazując jakie elementy należy zaimplementować aby dokonać integracji z Widget'em Autopay.

W przeglądarce poniższa, przykładowa strona składa się z trzech sekcji:
- sekcja górna zawiera możliwość wyboru konkretnych kanałów płatności
- sekcja środkowa zawiera miejsce, w którym osadzony będzie HTML IFRAME, do którego, w razie potrzeby, umieszczany będzie adres widget'u (visamobile lub standardowego kartowego wedle potrzeby)
- sekcja dolna zawiera (domyślnie nieaktywny) przycisk `PayButon` spięty z SDK JS sterujący uruchomieniem procesu w widget'cie  (w tym przykładzie przycisk uaktywnia się dopiero, gdy otrzymuje komunikat o poprawnej walidacji i uzyskaniu kompletu danych potrzebnych do wystartowania procesu)

<!-- TODO MIG-051: Źródło: README-2.md: images/widget-empty-example-page-start.png. Problem: Brak obrazu/diagramu: images/widget-empty-example-page-start.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-051: Brak obrazu/diagramu: images/widget-empty-example-page-start.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


Kiedy zostanie wybrany kanał kartowy (payway: 1500 lub 1503), wczyta się dedykowany widok formatki kartowej (oparty o HTML IFRAME).
W momencie wprowadzenia w widgecie pełnych, prawidłowych danych kartowych,  (dzięki eventom walidacyjnym) nastąpi uaktywnienie się przycisku "Zapłać" na Froncie Merchanta.

<!-- TODO MIG-052: Źródło: README-2.md: images/widget-card-example-page-loaded.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-loaded.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-052: Brak obrazu/diagramu: images/widget-card-example-page-loaded.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.



**Podpowiedź:** Jak widać w przykładzie w modelu WhiteLabel jest również możliwa obsługa kanału VisaMobile. Implementacja/osadzenie są analogiczne do widgeta kartowego dlatego poniższy kod zawiera już oba przypadki.


**Walidacja i skompletowanie danych**

W momencie wpisywania danych karty Autopay Widget JS SDK otrzymuje od widget'u event'y `VALIDITY_STATUS` z wartoscią `valid: false`

<!-- TODO MIG-053: Źródło: README-2.md: images/widget-card-example-page-invalid.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-invalid.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-053: Brak obrazu/diagramu: images/widget-card-example-page-invalid.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.



Gdy uzyskamy pełne dane karty, ostatnim event'em będzie `VALIDITY_STATUS` z wartością `valid: true`

```javascript
{status: 'VALIDITY_STATUS', message: null, valid: true, id: 'M2Zl...mU2'}
```

O ten event można oprzeć uaktywnianie przycisku `PayButton`

<!-- TODO MIG-054: Źródło: README-2.md: images/widget-card-example-page-ready.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-ready.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-054: Brak obrazu/diagramu: images/widget-card-example-page-ready.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.



**Podpowiedź:** Na środowisku testowym płatności kartowe są oparte o mock 3ds i mock autoryzacyjny. Poszczególnym scenariuszom odpowiadają dedykowane numery kart testowych. Pełna lista przypadków testowych znajduje się w oddzielnym załączniku.


**Ekran DCC**

W przypadku gdy dany scenariusz i karta spełnia warunki uzyskania oferty DCC pojawi dodatkowy ekran z propozycją przewalutowania dla Cardholdera

<!-- TODO MIG-055: Źródło: README-2.md: images/widget-card-example-page-dcc.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-dcc.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-055: Brak obrazu/diagramu: images/widget-card-example-page-dcc.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


Cardholder może zdecydować się na skorzystanie z obciążenia karty w natywnej dla niej walucie lub pozostawić oryginalną walute.
Na tym ekranie również występuje walidacja.

<!-- TODO MIG-056: Źródło: README-2.md: images/widget-card-example-page-dcc-invalid.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-dcc-invalid.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-056: Brak obrazu/diagramu: images/widget-card-example-page-dcc-invalid.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


Wybór waluty Cardholdera nie będzie miał wpływu na Merchanta i oryginalna kwotę samej transakcji, ale będzie miał wpływ na kwote jaką zostanie obciążna karta.
Jeśli Cardholder nie chce skorzystać z oferty przewalutowania DCC, zaznacza oryginaną walute (czyli w tym przypadku PLN).

<!-- TODO MIG-057: Źródło: README-2.md: images/widget-card-example-page-dcc-rejected.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-dcc-rejected.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-057: Brak obrazu/diagramu: images/widget-card-example-page-dcc-rejected.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


**Uzyskanie tokenu**

Przycisk należy powiązać z Autopay Widget JS SDK tak, aby jego kliknięcie triggerowało wywołanie metody `widget.sendForm();` w obiekcie `WidgetConnection`
Co finalnie, zaowocuje uzyskaniem event'u `FORM_SUCCESS`, czyli uzyskaniem wartości paymentToken'u (w polu `message`).


```javascript
{status: 'FORM_SUCCESS', message: 'eyJ...n19', id: 'M2Z...ZmU2'}
```

<!-- TODO MIG-058: Źródło: README-2.md: images/widget-card-example-page-clicked.png. Problem: Brak obrazu/diagramu: images/widget-card-example-page-clicked.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-058: Brak obrazu/diagramu: images/widget-card-example-page-clicked.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.



## Widget Kartowy - Szczegółowy schemat komunikacji i wymiany danych

Poniżej przedstawiono szczegółowy schemat komunikacji miedzy Merchantem, Cardholderem a systemami płatniczymi Autopay w przypadku tzw. integracji WhiteLabel (czyli z użyciem widget'a kartowego)

<!-- TODO MIG-059: Źródło: README-2.md: images/widget-card-diagram-flow.svg. Problem: Brak obrazu/diagramu: images/widget-card-diagram-flow.svg. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-059: Brak obrazu/diagramu: images/widget-card-diagram-flow.svg. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.



Bezpieczne przekazania danych karty do systemu Autopay oraz pełny flow transakcji:

* (0) Przekazanie do frontu danych konfiguracyjnych inicjujących osadzenie widget'a
* (1) Wyświetlenie formatki kartowej Widgeta osadzonej na froncie Merchanta (dane karty nie są podawane na frontendzie merchanta tylko na frontendzie widget'a Autopay)
* (2) Start poprzez wpisanie przez Cardholdera danych karty
* (3) Przesłanie danych kartowych z użyciem połączenia TLS zabezpieczonego certyfikatem typu Extended Validation do backendu CardsAutopay
  ** Dotyczy tylko przypadku gdy jest możliwe zaproponowanie DCC
  ** (3a) Zwrócenie szczegółów propozycji przewalutowania DCC
  ** (3b) Cardholder podejmuje decyzję, czy chce skorzystać z DCC
  ** (3c) Następuje przekazanie danych karty wprowadzonych wcześniej przez Cardholdera oraz decyzji DCC
* (4) WidgetJS otrzymuje z CardsAutopay i przesyła do Frontu Merchanta (za pomocą JS) wartość `paymentToken'a`
* (5) Front Merchanta poprzez Event JavaScript otrzymuje z Widget'a token platniczy
* (6) Front Merchanta przekazuje do Backendu Merchanta token płatniczy
* (7) Następuje backendowy start [Przedtransakcji z paymentToken'em](../../online-payments/advanced-flows/pretransaction.md#przedtransakcja) otrzymanym wcześniej z frontu
* (8) Autopay API zwraca URL [kontynuacji transakcji](../../online-payments/advanced-flows/pretransaction.md#odpowiedź-na-przedtransakcję--link-do-kontynuacji-transakcji) który posłuży przekierowania do startu transakcji z użytkownikiem
* (9) Backend Merchanta przekazuje do FrontuMerchanta URL przekierowujący
* (10) Przekierowanie Cardholdera ze strony Merchanta na PayAutopay w celu autentykacji 3DS
* Następuje weryfikacja 3DS (w zależności od decyzji banku może to być werfikacja pełna lub uproszczona)
* (11) Po "powrocie" Cardholdera z 3DS następuje dokończenie/zebranie wyniku autentykacji i autoryzacja
* (12) Autopay otrzymuje wynik obciążenia
* (13) Status transakcji jest przekazywany do Systemu Płatności Online (w tle)
* (14) Następuje przekierowanie Cardholdera ze strony PayAutopay (po autentykacji 3DS) z powrotem [do strony Merchanta](../../online-payments/payment-flow/customer-redirect.md#przekierowanie-do-serwisu-partnera)
* (15) Asynchronicznie do Backendu Merchanta przychodzi komunikat [ITN](../../online-payments/notifications/itn.md#powiadomienia-natychmiastowe-itn) ze statusem transakcji
  ** ( w przypadku transakcji inicjującej rekurencje, Backend Merchanta dostanie też dodatkowy komunikat [RPAN](../../online-payments/notifications/rpan-rpdn.md#komunikat-rpan) )

## Omówienie przykładowego kodu HTML JS (Widget Kartowy i VisaMobile)

Poniższy kod został wykorzystany do wygenerowania przykładowych integracji, o których była mowa wyżej w sekcjach z przykładami implementacji Widget'a Kartowego jak i Widget'a Visa Mobile

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Autopay Widget Integration Example</title>
    <script src="https://testcards.autopay.eu/widget-new/widget-communication.min.js"></script>
    <style>/* pominięte w przykladzie */</style>
</head>
<body>
<div>
  <form onsubmit="submitForm(event)" novalidate>
   <div class="form-group"><p>Transaction amount:</p><span>1,23 PLN</span></div>

    <!-- przykładowa implementacja mechanizmu wyboru kanału płatności po stronie merchanta -->
    <p>Choose payment method:</p>
    <ul>
      <li onclick="setPayway(event, 1500)">One time payment with card</li>
      <li onclick="setPayway(event, 1503)">Remember your card</li>
      <li onclick="setPayway(event, 1523)">Pay with VisaMobile</li>
    </ul>

    <!-- miejsce, w które wstrzyknięty zostanie HTML IFRAME z widget'em -->
    <div class="form-group" id="iframe-wrapper">
      <iframe id="iframe"></iframe>
    </div>

    <!-- przycisk wywołujący akcję w widget'cie -->
    <button type="submit" id="button" disabled="disabled">PayButton</button>
  </form>
</div>
<script type="text/javascript">
window.addEventListener('load', () => {
    // pomocnicze zmienne (tylko na potrzeby przykładu)
    var currentPayway = null;
    var widget = null;

    // przykładowe konfiguracje zależne od środowiska devloperskiego (tylko na potrzeby przykładu)
	var AUTOPAY_CARDS_DOMAIN_ENV_PROD = 'https://cards.autopay.eu';
	var AUTOPAY_CARDS_DOMAIN_ENV_TEST = 'https://testcards.autopay.eu';

	var MERCHANTS_SERVICE_ID_ENV_PROD = 903555;
	var MERCHANTS_SERVICE_ID_ENV_TEST = 903555;

    // pomocnicza metoda obsługująca wybór kanału płatności i osadzenie widget'u
    function setPayway (event, paywayId) {
        if (currentPayway === paywayId) {
            return;
        }
        currentPayway = paywayId
        removeWidget();
        disableSubmitButton();
        markActiveIcon(event);

        if (paywayId === 1500) {
            startWidget('/widget-new/partner'   , { language: 'en', amount: 1.23, currency: 'PLN', serviceId: MERCHANTS_SERVICE_ID_ENV_TEST });
            return
        }
        if (paywayId === 1503) {
            startWidget('/widget-new/partner'   , { language: 'en', amount: 1.23, currency: 'PLN', serviceId: MERCHANTS_SERVICE_ID_ENV_TEST, recurringAction: 'INIT_WITH_REFUND' });
            return
        }
        if (paywayId === 1523) {
            startWidget('/widget-new/visamobile', { language: 'en', amount: 1.23, currency: 'PLN', serviceId: MERCHANTS_SERVICE_ID_ENV_TEST,  merchantName: 'ShopName' });
			return
        }
    }

    // pomocnicza metoda (tylko na potrzeby przykładu) ustawiająca obramowanie na wybranym kanale płatności
    function markActiveIcon (event) {
        var currentActive = document.querySelector('ul li.active');
        if (currentActive) {
            currentActive.classList.remove('active');
        }
        var newActive = event.target;
        if (newActive.nodeName.toLowerCase() === 'img') {
            newActive = newActive.parentNode;
        }
        newActive.classList.add('active');
    }

    // główna metoda odpowiadająca za osadzenie IFRAME z widget'em i zestawienia komunikacji między nim a jego obiektowym reprezentantem WidgetConnection
    function startWidget (widgetVariantUrl, widgetConfig) {
        if (!widgetEvents || !WidgetConnection) {
            return;
        }
        var iframeEl = document.getElementById('iframe');
        iframeEl.src = AUTOPAY_CARDS_DOMAIN_ENV_TEST + widgetVariantUrl;
        widget = new WidgetConnection(widgetConfig)

        widget.startConnection(iframeEl).then(() => {

            // obsłużenie głównego, finalnego event'u zawierającego wartość PaymentToken'a
            widget.on(widgetEvents.formSuccess, function (message, eventData) {
                console.log('payment token event =>', eventData);
                console.log('payment token value:', message);
                // w tym miejscu powinno być wywołanie API merchanta, aby przekazać paymentToken (message) do backend'u merchanta;    <<<<<<<<<<<<<<<<<
            })

            // obsłużenie event'ów związanych z walidacją podczas wprowadzania danych w widget'cie przez użytkownika/cardholder'a
            widget.on(widgetEvents.validityStatus, function (message, eventData) {
                console.log('form validation status =>', eventData);
                if (eventData.valid) {
                    enableSubmitButton();
                } else {
                    disableSubmitButton();
                }
            })

            // obsłużenie event'u związanego z walidacją w momencie wprowadzania danych w widget'cie przez użytkownika/cardholder'a
            widget.on(widgetEvents.validationResult, function (message, eventData) {
                console.log('form validation result =>', eventData);
                if (eventData.valid) {
                    enableSubmitButton();
                } else {
                    disableSubmitButton();
                }
            })

            // obsłużenie event'u showModal
            widget.on(widgetEvents.showModal, function () {
                console.log('show modal');
            })
        })
    }

    // pomocnicza metoda (tylko na potrzeby przykładu) ustawiająca usuwanie widget'u dla innych, niż kartowe, kanałów płatnosći (w przykladzie jest kanał 106 PBL)
    function removeWidget () {
        if (!widget) {
            return;
        }
        widget.stopConnection();
    }

    // pomocnicza metoda (tylko na potrzeby przykładu)
    function enableSubmitButton () {
        document.getElementById('button').removeAttribute('disabled');
    }

    // pomocnicza metoda (tylko na potrzeby przykładu)
    function disableSubmitButton () {
        document.getElementById('button').setAttribute('disabled', 'disabled');
    }

    // pomocnicza metoda (tylko na potrzeby przykładu) powiązująca naćiśnięcie aktywnego przycisku zapłać z wywołaniem sendForm() w obiekcie widget'u
    function submitForm (event) {
        event.preventDefault();
        if (!widget || widget.invalid) {
            return;
        }
        disableSubmitButton();
        widget.sendForm();
    }

    window.setPayway = setPayway;
    window.submitForm = submitForm
});
</script>
</body>
</html>
```
