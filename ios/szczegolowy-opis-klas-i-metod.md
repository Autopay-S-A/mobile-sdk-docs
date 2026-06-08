---
coverY: 0
---

# Szczegółowy opis klas i metod

## Klasy

### Autopay / AutopayObjc

Główna klasa SDK. Obiekt pozwala na startowanie transakcji, pobieranie listy kanałów płatności, pobieranie regulaminów, sprawdzanie statusu transakcji oraz pobieraniu opłaty konsumenckiej. Posiada parametr APConfig wymagany przy inicjalizacji.

| Publiczny konstruktor    |
| ------------------------ |
| `init(config: APConfig)` |

**Publiczne metody**

| metoda                                                 | opis                                                                                                                             |
| ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `startTransaction(transactionData: APTransactionData)` | Rozpoczyna transakcję na podstawie zdefiniowanych danych kryjących się pod parametrem _transactionData_. Zwraca dane transakcji. |
| `getGatewayList()`                                     | Zwraca listę kanałów płatności zdefiniowanych dla skonfigurowanego serwisu                                                       |
| `getRegulations(gatewayId: Int)`                       | Zwraca listę regulaminów dla wybranego kanału płatności identyfikującego się parametrem _gatewayId_.                             |
| `getCustomerFee(gatewayId: Int, amount: Double)`       | Zwraca opłatę konsumencką dla wybranego kanału płatności i zdefiniowanej kwoty transakcji.                                       |
| `getTransactionStatus(orderId: String)`                | Odpytuje serwis o status transakcji kryjący się pod identyfikatorem _orderId_. Zwraca dane o statusie transakcji.                |
| `class getSdkVersion()`                                | Zwraca wersję SDK.                                                                                                               |

### APConfig

Główna klasa konfiguracyjna SDK. Bez tej klasy nie można wykonać żadnych operacji. Posiada cztery wymagane parametry:

* **token transakcyjny** - uzyskiwany z backendu aplikacji,
* **id serwisu** (`SID`) - przydzielany przez System Płatności Online BM,
* **id akceptanta** - przydzielany przez System Płatności Online BM,
* **typ środowiska** Systemu Płatności Online BM - do wyboru typ produkcyjny lub testowy.

Istnieją również opcjonalne parametry:

* `contextPath` - służy do ustawiania dedykowanego kontekstu kanału płatności pozwalającego na ostylowanie kanału pod klienta
* `applePayMerchantId` - identyfikator merchanta Apple Pay
* `currencies` - lista obsługiwanych walut (domyślnie tylko `PLN`); zaleca się podanie jednej waluty
* `countryCode` - kod kraju wykorzystywany do płatności **ApplePay**, domyślnie `PL`
* `defaultRegulationsCode` - domyślny kod kraju dla którego zostaną pobrane regulaminy w przypadku gdy dla obecnego języka urządzenia nie jest możliwe pobranie regulaminów, domyślnie `PL`
* `regulationsHidden` - steruje możliwością wyświetlania regulaminów na kanałach płatności, domyślnie na wszystkich kanałach regulaminy są widoczne

**Publiczny konstruktor**

```swift
init(
     token: String,
     serviceId: Int,
     acceptorId: Int,
     applePayMerchantId: String? = nil,
     environment: APEnvironmentEnum,
     contextPath: String? = nil,
     currencies: [String]? = nil,
     countryCode: String? = nil,
     defaultRegulationsCode: String? = nil,
     regulationsHidden: [APGatewayPaymentGroup]? = nil
    )
```

**Publiczne metody**

| metoda                                                  | opis                                                                                                                                                                                                |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `setCountryCode(countryCode: String)`                   | Aktualizuje countryCode wykorzystywane do płatności ApplePay.                                                                                                                                       |
| `setCurrencies(currencies: [String])`                   | Ustawia listę walut na podstawie której będą pobrane kanały płatności. Domyślnie lista kanałów płatności pobierana jest dla waluty "PLN". Dopuszczalne są jedynie wartości: PLN, EUR, GBP oraz USD. |
| `setRegulationsHidden(hidden: [APGatewayPaymentGroup])` | Steruje możliwością wyświetlania regulaminów na kanałach płatności. Podając typ kanału płatności wyłączamy widoczność regulaminów na wybranej grupie płatności.                                     |
| `setToken(token: String)`                               | Aktualizuje token                                                                                                                                                                                   |

📌 Ważne: W przypadku przekazania do `setRegulationsHidden` typu `.card` np `[.card]` sekcja regulaminów nie zostanie wyświetlona zarówno na kanale płatności kartą płatniczą jak i aktywacji karty płatniczej.

### APGatewayBaseViewModelData

Główna klasa konfiguracyjna dla widoków. Posiada dwa wymagane parametry:

* **config** - obiekt konfiguracyjny SDK [APConfig](szczegolowy-opis-klas-i-metod.md#apconfig),
* **amount** - kwota transakcji bez opłaty konsumenckiej.

Opcjonalne:

* `summary` — tekst w podsumowaniu płatności; jeśli null lub pusty, podsumowanie nie będzie widoczne, wykorzystywane również do podsumowania płatności w komponencie systemowym ApplePay
* `customerEmail` — adres e-mail
* `customerPhone` — numer telefonu klienta
* `blikContentHeaderTitle` — indywidualne tłumaczenie nagłówka przy płatności Blik. Domyślnie wykorzystywane jest tłumaczenie z SDK.
* `bankContentHeaderTitle` — indywidualne tłumaczenie nagłówka przy płatności Przelewu bankowego. Domyślnie wykorzytywane jest tłumaczenie z SDK.
* `paymentViewCallback` - callback zwracający status płatności lub błąd w przypadku gdy płatność realizowana jest przez SDK
* `payTappedCallback` - callback zwracający dane niezbędne do realizacji płatności przez aplikację, wywoływany jest w momencie kliknięcia przycisku płatności.
* `customerFeeDidUpdatedCallback` — callback wywoływany w momencie zaktualizowania opłaty konsumenckiej.
* `tokenExpiredCallback` - callback wywoływany gdy SDK wykryje wygaśnięty token, zwraca obiekt błędu `APError`.

📌 Ważne: W zależnośći od tego czy zostanie przekazany `payTappedCallback` czy `paymentViewCallback` SDK realizuje różne scenariusze, jeśli planujesz przetwarzać płatność po stronie aplikacji nie przekazuj `paymentViewCallback` do modelu danych oraz adekwatnie jeśli chcesz aby SDK przeprowadziło pełny proces transakcji nie przekazuj `payTappedCallback`.

**UWAGA:** Jeśli token wygaśnie, należy zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w obiekcie configuracyjnym `APConfig` metodą `setToken(token: String)`, odblokować interfejs i pozwolić użytkownikowi na kontynuowanie płatności.

**Publiczny konstruktor**

```swift
init(
     config: APConfig,
     amount: Double,
     summary: String? = nil,
     customerEmail: String? = nil,
     customerPhone: String? = nil,
     paymentViewCallback: APPPaymentViewCallback? = nil,
     payTappedCallback: APPayTappedCallback? = nil,
     customerFeeDidUpdatedCallback: APCustomerFeeDidUpdatedCallback? = nil,
     tokenExpiredCallback: APTokenExpiredCallback? = nil
    )
```

### APStyleManager

Klasa odpowiedzialna za sylizacje widoków, przekazywana jako `enviromentObject` w przypadku implementacji SwiftUI oraz atrybut `styleManager: APStyleManager` w inicjalizatorach w przypadku implementacji UIKit. Obiekt ten zawiera style domyślne oraz kolorystykę przedstawioną w aplikacji demonstracyjnej, tak by użytkownik mógł podmienić tylko to czego potrzebuje.

**Publiczny konstruktor**

```swift
init(
        typography: APTypography = .init(),
        primaryButtonStyle: APButtonStyle = .init(style: .primary),
        secondaryButtonStyle: APButtonStyle = .init(style: .secondary),
        tertiaryButtonStyle: APButtonStyle = .init(style: .tertiary),
        bankGridStyle: APBankGridStyle = .init(),
        checkboxStyle: APCheckboxStyle = .init(),
        dccPaymentFormStyle: APDCCPaymentFormStyle = .init(),
        dialogStyle: APDialogStyle = .init(),
        loaderStyle: APLoaderStyle = .init(),
        paymentMethodButtonStyle: APButtonStyle = .init(style: .paymentMethod),
        paymentMethodTitleStyle: APPaymentMethodTitleStyle = .init(),
        paymentSummaryStyle: APPaymentSummaryStyle = .init(),
        radioButtonStyle: APRadioButtonStyle = .init(),
        switchStyle: APSwitchStyle = .init(),
        textInputStyle: APTextInputStyle = .init(),
        errorColor: APColor? = nil,
        footerIconsColor: APColor? = nil,
    )
```

**Publiczne atrybuty**

| atrybut                                              | opis                                                                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| `typography: APTypography`                           | Zestaw czcionek oraz domyślnym kolorem tekstu                                                                |
| `primaryButtonStyle: APButtonStyle`                  | Zestaw parametrów stylizujacych przycisk główny (wypełniony) w SDK                                           |
| `secondaryButtonStyle: APButtonStyle`                | Zestaw parametrów stylizujacych przycisk dodatkowy (obramowany) w SDK                                        |
| `tertiaryButtonStyle: APButtonStyle`                 | Zestaw parametrów stylizujacych przycisk pomocniczy (obramowany) w SDK                                       |
| `bankGridStyle: APBankGridStyle`                     | Zestaw parametrów stylizujacych siatkę banków na grupie Przelewy bankowe                                     |
| `checkboxStyle: APCheckboxStyle`                     | Zestaw parametrów stylizujacych widoki typu checkbox                                                         |
| `dccPaymentFormStyle: APDCCPaymentFormStyle`         | Zestaw parametrów stylizujących okno z formularzem przewalutowania przy płatności kartą                      |
| `dialogStyle: APDialogStyle`                         | Zestaw parametrów stylizujących wyświetlane okna w SDK                                                       |
| `loaderStyle: APLoaderStyle`                         | Zestaw parametrów stylizujących widoki ładowania danych                                                      |
| `paymentMethodButtonStyle: APButtonStyle`            | Zestaw parametrów stylizujących przycisk kanału płatności na liście kanałów płatności                        |
| `paymentMethodTitleStyle: APPaymentMethodTitleStyle` | Zestaw parametrów stylizujących tytuł kanału płatności, po wybraniu danej formy i rozwinięciu jej szczegółów |
| `paymentSummaryStyle: APPaymentSummaryStyle`         | Zestaw parametrów stylizujących etykietę z podsumowaniem płatności                                           |
| `radioButtonStyle: APRadioButtonStyle`               | Zestaw parametrów stylizujacych widoki typu radio button                                                     |
| `switchStyle: APSwitchStyle`                         | Zestaw parametrów stylizujacych widoki typu switch                                                           |
| `textInputStyle: APTextInputStyle`                   | Zestaw parametrów stylizujących widoki wprowadzania danych tekstowych                                        |
| `errorColor: APColor`                                | Kolor błędów                                                                                                 |
| `footerIconsColor: APColor`                          | Kolor ikon partnerów wystepujący na dole listy kanałów płatności                                             |

### APColor

Reprezentuje wartość koloru w dwóch trybach - jasny ciemny.

| Publiczne konstruktory            | opis                                                                                     |
| --------------------------------- | ---------------------------------------------------------------------------------------- |
| `init(light: Color, dark: Color)` | Przyjmuje jako parametry jasny i ciemny kolor                                            |
| `init(light: Color)`              | Przyjmuje jako parametr tylko jasny kolor, w trybie ciemnym przyjmowany jest kolor jasny |

**Publiczne atrybuty**

| atrybut        | opis                   |
| -------------- | ---------------------- |
| `light: Color` | Kolor w trybie jasnym  |
| `dark: Color`  | Kolor w trybie ciemnym |

### APTextStyle

Reprezentuje styl tekstu, zawierający czcionkę oraz jej kolor.

| Publiczne konstruktory               | opis                                                                             |
| ------------------------------------ | -------------------------------------------------------------------------------- |
| `init(font: UIFont, color: APColor)` | Przyjmuje jako parametry czcionkę oraz parę kolorów dla trybu jasnego i ciemnego |
| `convenience init(font: UIFont)`     | Przyjmuje jako parametr tylko czcionkę, kolor tekstu pozostaje domyślny z SDK    |

**Publiczne atrybuty**

| atrybut          | opis                                      |
| ---------------- | ----------------------------------------- |
| `font: UIFont`   | Czcionka                                  |
| `color: APColor` | Para kolorów dla trybu jasnego i ciemnego |

### APTypography

Reprezentuje zestaw styli tekstów wraz z kolorem domyślnym dla każdego stylu. System zakłada użycie biblioteki w 4 rozmiarach - 12, 14, 16, 18, o wadze standardowej (400). Jedynie czcionka o rozmiarze 12 ma swój pogrubiony odpowiednik o wadze 500. Każdy styl tekstu przekazywany jest w postaci parametrów.

**Publiczne atrybuty**

| atrybut                      | opis                                                  |
| ---------------------------- | ----------------------------------------------------- |
| `labelSmallFont: UIFont`     | Czcionka w treściach o rozmiarze 12 (400)             |
| `labelMediumFont: UIFont`    | Czcionka w treściach o rozmiarze 14 (400)             |
| `labelLargeFont: UIFont`     | Czcionka w treściach o rozmiarze 16 (400)             |
| `labelXLargeFont: UIFont`    | Czcionka w treściach o rozmiarze 18 (400)             |
| `labelSmallBoldFont: UIFont` | Czcionka w treściach o rozmiarze 12 (500)             |
| `defaultTextColor: APColor`  | Domyślny kolor tekstu dla wyżej wymienionych czcionek |

### APButtonStyle

Reprezentuje styl przycisków

| Publiczne konstruktory                                                                                                                                                                                                                                                   | opis                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| `init(style: APButtonStyleType)`                                                                                                                                                                                                                                         | Przyjmuje za parametr typ stylu przycisku i przypisuje domyślne dla niego wartości |
| `init(containerColor: APColor, containerInactiveColor: APColor, borderColor: APColor, borderInactiveColor: APColor, textStyle: APTextStyle, textInactiveStyle: APTextStyle, iconColor: APColor? = nil, cornerRadius: CGFloat, borderWidth: CGFloat, minHeight: CGFloat)` | Przyjmuje za parametry wszystkie z możliwych atrybutów                             |

**Publiczne atrybuty**

| atrybut                           | opis                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `containerColor: APColor`         | Kolor tła przycisku                                          |
| `containerInactiveColor: APColor` | Kolor tła przycisku w stanie niekatywnym                     |
| `borderColor: APColor`            | Kolor obramowania przycisku                                  |
| `borderInactiveColor: APColor`    | Kolor obramowania przycisku w stanie nieaktywnym             |
| `textStyle: APTextStyle`          | Czcionka oraz kolor tekstu w przycisku                       |
| `textInactiveStyle: APTextStyle`  | Czcionka oraz kolor tekstu w przycisku w stanie nieaktywnym  |
| `iconColor: APColor?`             | Kolor ikony w przycisku (tylko na przycisku metod płatności) |
| `cornerRadius: CGFloat`           | Zaokrąglenie przycisku, domyślnie .infinity                  |
| `borderWidth: CGFloat`            | Grubość obramowania przycisku                                |
| `minHeight: CGFloat`              | Minimalna wysokość przycisku, domyślnie 48                   |

### APBankGridStyle

Reprezentuje zestaw parametrów stylizujacych siatkę banków na grupie Przelewy bankowe

**Publiczne atrybuty**

| atrybut                         | opis                                  |
| ------------------------------- | ------------------------------------- |
| `columns: Int`                  | Liczba kolumn, domyślnie 3            |
| `cellHeight: CGFloat`           | Wysokość komórki, domyślnie 80        |
| `radius: CGFloat`               | Zaokrąglenie komórki, domyślnie 12    |
| `backgroundColor: APColor`      | Kolor tła komórki                     |
| `checkedBorderColor: APColor`   | Kolor obramowania zaznaczonej komórki |
| `uncheckedBorderColor: APColor` | Kolor obramowania odznaczonej komórki |

### APCheckboxStyle

Reprezentuje zestaw parametrów stylizujacych widoki typu checkbox

**Publiczne atrybuty**

| atrybut                   | opis                                                                        |
| ------------------------- | --------------------------------------------------------------------------- |
| `checkedColor: APColor`   | Kolor wypełnienia zaznaczonego checkboxa                                    |
| `uncheckedColor: APColor` | Kolor obramowania w stanie domyślnym niezaznaczonym                         |
| `errorColor: APColor`     | Kolor obramowania w przypadku błędu spowodowanego niezaznaczeniem checkboxa |

### APDCCPaymentFormStyle

Reprezentuje zestaw parametrów stylizujących okno z formularzem przewalutowania przy płatności kartą

**Publiczne atrybuty**

| atrybut                          | opis                                           |
| -------------------------------- | ---------------------------------------------- |
| `selectedBorderColor: APColor`   | Kolor obramowania zaznaczonej komórki z walutą |
| `unselectedBorderColor: APColor` | Kolor obramowania odznaczonej komórki z walutą |
| `cellBackgroundColor: APColor`   | Kolor tła komórki z walutą                     |
| `cellRadius: CGFloat`            | Zaokrąglenie komórki z walutą, domyślnie 16    |

### APDialogStyle

Reprezentuje zestaw parametrów stylizujących wyświetlane okna w SDK

**Publiczne atrybuty**

| atrybut                          | opis                            |
| -------------------------------- | ------------------------------- |
| `dialogRadius: CGFloat`          | Zaokrąglenie okna, domyślnie 16 |
| `dialogBackgroundColor: APColor` | Kolor tła okna                  |

### APLoaderStyle

Reprezentuje zestaw parametrów stylizujących element sygnalizujący ładowanie danych.

**Publiczne atrybuty**

| atrybut          | opis                                     |
| ---------------- | ---------------------------------------- |
| `size: CGFloat`  | Rozmiar elementu ładowania, domyślnie 60 |
| `color: APColor` | Kolor elementu ładowania                 |

### APPaymentMethodTitleStyle

Reprezentuje zestaw parametrów stylizujących tytuł kanału płatności, po wybraniu danej formy i rozwinięciu jej szczegółów

**Publiczne atrybuty**

| atrybut                    | opis                                                                                                                                                         |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `backgroundColor: APColor` | Kolor tła                                                                                                                                                    |
| `iconColor: APColor`       | Kolor ikon na przycisku - tylko w przypadku Karty płatniczej oraz Przelewów bankowych, pozostałe przyciski mają ikony wielokolorowe odpowiadające ich markom |
| `textStyle: APTextStyle`   | Czcionka oraz kolor tekstu tytułu                                                                                                                            |
| `radius: CGFloat`          | Zaokrąglenie tła, domyślnie 16                                                                                                                               |

### APPaymentSummaryStyle

Reprezentuje zestaw parametrów stylizujących komponent z podsumowaniem płatności

**Publiczne atrybuty**

| atrybut                    | opis                                       |
| -------------------------- | ------------------------------------------ |
| `backgroundColor: APColor` | Kolor tła                                  |
| `borderColor: APColor`     | Kolor obramowania komponentu               |
| `dividerColor: APColor`    | Kolor lini rozdzielającej                  |
| `borderWidth: CGFloat`     | Grubość obramowania komonentu, domyślnie 0 |
| `dividerHeight: CGFloat`   | Wysokość lini rozdzielającej, domyślnie 1  |
| `radius: CGFloat`          | Zaokrąglenie tła, domyślnie 16             |

### APRadioButtonStyle

Reprezentuje zestaw parametrów stylizujacych widoki typu radio button

**Publiczne atrybuty**

| atrybut                   | opis                       |
| ------------------------- | -------------------------- |
| `checkedColor: APColor`   | Kolor w stanie zaznaczonym |
| `uncheckedColor: APColor` | Kolor w stanie odznaczonym |

### APSwitchStyle

Reprezentuje zestaw parametrów stylizujacych widoki typu radio button

**Publiczne atrybuty**

| atrybut                         | opis                                       |
| ------------------------------- | ------------------------------------------ |
| `checkedThumbColor: APColor`    | Kolor przełącznika w stanie zaznaczonym    |
| `uncheckedThumbColor: APColor`  | Kolor przełącznika w stanie niezaznaczonym |
| `checkedTrackColor: APColor`    | Kolor tła w stanie zaznaczonym             |
| `uncheckedTrackColor: APColor`  | Kolor tła w stanie niezaznaczonym          |
| `checkedBorderColor: APColor`   | Kolor obramowania w stanie zaznaczonym     |
| `uncheckedBorderColor: APColor` | Kolor obramowania w stanie niezaznaczonym  |

### APTextInputStyle

Reprezentuje zestaw parametrów stylizujacych widoki wprowadzania danych tekstowych

**Publiczne atrybuty**

| atrybut                        | opis                                                                 |
| ------------------------------ | -------------------------------------------------------------------- |
| `inputTextStyle: APTextStyle`  | Czcionka oraz kolor tekstu wprowadzanego                             |
| `labelTextStyle: APTextStyle`  | Czcionka oraz kolor tekstu etykiety nad widokiem                     |
| `errorTextStyle: APTextStyle`  | Czcionka oraz kolor tekstu błędu pod widokiem                        |
| `borderInactiveColor: APColor` | Kolor obramowania w stanie domyślnym                                 |
| `borderActiveColor: APColor`   | Kolor obramowania w stanie zaznaczonym                               |
| `borderErrorColor: APColor`    | Kolor obramowania w przypadku błędu w formularzu                     |
| `backgroundColor: APColor`     | Kolor tła widoku                                                     |
| `trailingIconsColor: APColor`  | Kolor ikon dodatkowych                                               |
| `spaceBetweenInputs: CGFloat`  | Odległość między polami w formularzu, domyślnie 12                   |
| `strokeWidth: CGFloat`         | Grubość obramowania pola wprowadzania danych tekstowych, domyślnie 1 |
| `radius: CGFloat`              | Zaokrąglenie tła, domyślnie .infinity                                |

### APTransactionData

Obiekt zawierający wszystkie dane potrzebne do realizacji transakcji.

| Publiczne konstruktory                              |
| --------------------------------------------------- |
| `init(amount: String)`                              |
| `convenience init(amount: String, orderId: String)` |

**Publiczne metody**

| metoda                                                     | opis                                                           |
| ---------------------------------------------------------- | -------------------------------------------------------------- |
| `getOrderId() -> String`                                   | Zwraca id transakcji.                                          |
| `getParams() -> [String : String]`                         | Zwraca listę ustawionych parametrów do danych transakcji.      |
| `setCustomerEmail(_ customerEmail: String)`                | Ustawia adres e-mail klienta.                                  |
| `setCustomerPhone(_ customerPhone: String)`                | Ustawia numer telefonu klienta.                                |
| `setApplePayPaymentToken(_ paymentToken: PKPaymentToken)`  | Ustawia payment token dla płatności Apple Pay.                 |
| `setGatewayId(_ gatewayId: Int)`                           | Ustawia id kanału płatności, jakim ma być wykonana transakcja. |
| `setRecurringAction(_ recurringAction: APRecurringAction)` | Ustawia typ włączania rekurencji.                              |
| `addParam(value: String, key: String)`                     | Dodaje parametr do danych transakcji.                          |
| `addParams(_ customParams: [String: String])`              | Dodaje listę parametrów do danych transakcji.                  |
| `setBlikCode(_ blikCode: String)`                          | Dodaje kod blik do płatności Blik.                             |
| `setRegulations(regulations: [RegulationModel])`           | Dodaje regulaminy do transakcji.                               |

### APProductList

Obiekt listy produktów w koszyku transakcji.

| Publiczne konstruktory           |
| -------------------------------- |
| `init(productList: [APProduct])` |
| `init(product: APProduct)`       |

**Publiczne metody**

| metoda                           | opis                     |
| -------------------------------- | ------------------------ |
| `addProduct(product: APProduct)` | Dodaje produkt do listy. |

### APProduct

Obiekt produktu w koszyku transakcji. Posiada parametry wymagane: `subAmount` (kwota, jaką trzeba zapłacić za dany produkt) oraz parametr `params` (lista dodatkowych parametrów produktu), lub `param` pojedynczy parametr produktu.

| Publiczne konstruktory                       |
| -------------------------------------------- |
| `init(subAmount: String, param: APParam)`    |
| `init(subAmount: String, params: [APParam])` |

**Publiczne metody**

| metoda                              | opis |
| ----------------------------------- | ---- |
| `APProduct addParam(APParam param)` |      |

### APParam

Obiekt parametrów produktu.

| Publiczne konstruktory                                                      |
| --------------------------------------------------------------------------- |
| `init(name: String, value: String)`                                         |
| `init(name: String, value: String, additionalAttributes: [String: String])` |

**Publiczne metody**

| metoda                                               | opis                                  |
| ---------------------------------------------------- | ------------------------------------- |
| `addAdditionalAttribute(key: String, value: String)` | Ustawia dodatkowy atrybut parametrowi |

### APTransaction

Obiekt zwracany podczas wykononywania płatności przy pomocy `APGatewayListView` lub aktywacji karty (`APCardActivationGatewayView`) zawarty w callbacku `APPPaymentViewCallback`.

| atrybut                           | opis                                                                                                                                                 |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `orderId: String?`                | Identyfikator transakcji.                                                                                                                            |
| `remoteId: String?`               | Unikalny identyfikator transakcji nadany w Systemie BM.                                                                                              |
| `transactionHash: String?`        | Hash zwrócony z backendu Autopay.                                                                                                                    |
| `serviceId: String?`              | Identyfikator serwisu obsługującego płatność.                                                                                                        |
| `messageId: String?`              | Identyfikator wiadomości zwróconej z backendu Autopay                                                                                                |
| `confirmation: APConfirmation?`   | Status potwierdzenia przyjęcia zlecenia.                                                                                                             |
| `reason: String?`                 | Powód niedokonania transakcji, jeśli istnieje. Jeśli puste, transakcja się rozpoczęła.                                                               |
| `paymentStatus: APPaymentStatus?` | Status transakcji.                                                                                                                                   |
| `status: APPaymentStatus?`        | Status transakcji.                                                                                                                                   |
| `redirectUrl: String?`            | Url strony do przekierowania w celu dokończenia transakcji. Może być pusty, wtedy transakcja jest w trakcie realizacji i można sprawdzić jej status. |

### APCustomerFee

Obiekt opłaty konsumenckiej

| Publiczny konstruktor                             |
| ------------------------------------------------- |
| `init(customerFee: Double, receiverName: String)` |

| atrybut                | opis                           |
| ---------------------- | ------------------------------ |
| `customerFee: Double`  | Kwota opłata konsumenckiej.    |
| `receiverName: String` | Odbiorca opłaty konsumenckiej. |

### APGateway

Obiekt opisujący kanał płatności.

**Publiczne metody / atrybuty**

| metoda / atrybut             | opis                                                                                                                                |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `gatewayId: Int`             | Zwraca id kanału płatności.                                                                                                         |
| `gatewayName: String?`       | Zwraca nazwę kanału płatności.                                                                                                      |
| `gatewayType: APGatewayType` | Zwraca typ kanału płatności.                                                                                                        |
| `bankName: String?`          | Zwraca nazwę banku.                                                                                                                 |
| `iconUrl: String`            | Zwraca link do ikony kanału płatności.                                                                                              |
| `statusDate: String`         | Zwraca datę pobrania danych kanału płatności z Systemu Płatności Online BM.                                                         |
| `currencyList: [APCurrency]` | Zwaraca listę obsługiwanych walut przez kanał płatności.                                                                            |
| `isInvalid() -> Bool`        | Zwraca informację, czy kanał płatności jest niepoprawny, tj. zawiera nieprawidłowe dane (niepoprawny - `true`, poprawny - `false`). |

### APResult

Obiekt zwracany w przypadku otrzymania rezultatu transakcji podczas wykorzystania `WebView` - callback `APPWebViewCallback`.

**Publiczne atrybuty**

| atrybut                   | opis                                               |
| ------------------------- | -------------------------------------------------- |
| `status: APPaymentStatus` | Rezultat transakcji (enum typu `APPaymentStatus`). |

### APError

Obiekt zwracany w przypadku wystąpienia błędu w bibliotece w metodach `throws` lub `async throws` oraz callbackach `APPPaymentViewCallback`, `APPWebViewCallback`.

**Publiczne atrybuty**

| atrybut               | opis                                                   |
| --------------------- | ------------------------------------------------------ |
| `status: APErrorEnum` | Zwraca błąd, który wystąpił (enum typu `APErrorEnum`). |
| `message: String?`    | Zwraca informację tekstową o błędzie.                  |

### APLog

Klasa służąca do logowania zdarzeń do konsoli.

**Publiczne metody / atrybuty**

| atrybut                 | opis                                                                   |
| ----------------------- | ---------------------------------------------------------------------- |
| `static SHOW_LOG: Bool` | Ustawia logowanie informacji (włączone - `true`, wyłączone - `false`). |

## Widoki

### APGatewayListView / APGatewayListContainerView

Widok listy kanałów płatności jest rozbudowanym widokiem obsługującym zarówno załadowanie listy kanałów płatności, ich wyświetlanie oraz rozwinięcie szczegółów wybranego kanału płatności wraz z załadowaniem regulaminów, opłaty konsumenckiej oraz dokonaniem płatności. Po dokonaniu płatności widok wraca do stanu załadowanej listy.

Publiczny konstruktor

SwiftUI

```swift
init(
     data: APGatewayBaseViewModelData,
     excludedGatewayPaymentGroups: [APGatewayPaymentGroup] = [],
     selectedPaymentGroupHandler: @escaping (APGatewayPaymentGroup?) -> Void
   )
```

UIKit

```swift
init(
     data: APGatewayBaseViewModelData,
     styleManager: APStyleManager = APStyleManager(),
     excludedGatewayPaymentGroups: [APGatewayPaymentGroup] = [],
     selectedPaymentGroupHandler: @escaping (APGatewayPaymentGroup) -> Void,
     deselectedPaymentGroupHandler: @escaping () -> Void
   )
```

Obj-c

```objective-c
init(
     data: APGatewayBaseViewModelData,
     excludedGatewayPaymentGroups: [Int] = [],
     selectedPaymentGroupHandler: @escaping (APGatewayPaymentGroup) -> Void,
     deselectedPaymentGroupHandler: @escaping () -> Void
   )
```

**Opis konstruktora**

| atrybut                                                                  | opis                                                                                                                                                                                                                           |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `data: APGatewayBaseViewModelData`                                       | Obiekt konfiguracyjny widoku [APGatewayBaseViewModelData](szczegolowy-opis-klas-i-metod.md#apgatewaybaseviewmodeldata)                                                                                                         |
| `styleManager: APStyleManager`                                           | W przypadku użycia **APGatewayListContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |
| `excludedGatewayPaymentGroups: [APGatewayPaymentGroup]`                  | Lista wyłączonych grup kanałów płatności. W przypadku implementacji Obj-c atrybut przyjmuje tablicę Int reprezentujących APGatewayPaymentGroup                                                                                 |
| `selectedPaymentGroupHandler: @escaping (APGatewayPaymentGroup) -> Void` | Callback zwracający informacje o wybranym kanale płatności, moze zostać wykorzystane do aktualizacji nagłówka w navigation bar                                                                                                 |
| `deselectedPaymentGroupHandler: @escaping (?) -> Void`                   | Callback wykorzystywany w implementacji UIKit zwracający informacje o powrocie do listy kanałów płatności                                                                                                                      |

### APApplePayGatewayView / APApplePayGatewayContainerView

Widok rozwiniętego kanału płatności typu _ApplePay_. Nie zawiera podsumowania płatności.

Publiczny konstruktor

SwiftUI

```swift
init(
     data: APGatewayBaseViewModelData
   )
```

UIKit

```swift
init(
     data: APGatewayBaseViewModelData,
     styleManager: APStyleManager = APStyleManager()
   )
```

**Opis konstruktora**

| atrybut                            | opis                                                                                                                                                                                                                               |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `data: APGatewayBaseViewModelData` | Obiekt konfiguracyjny widoku [APGatewayBaseViewModelData](szczegolowy-opis-klas-i-metod.md#apgatewaybaseviewmodeldata)                                                                                                             |
| `styleManager: APStyleManager`     | W przypadku użycia **APApplePayGatewayContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |

### APBankTransferGatewayView / APBankTransferGatewayContainerView

Widok rozwiniętego kanału płatności typu _Bank_. Nie zawiera podsumowania płatności.

SwiftUI

```swift
init(
     data: APGatewayBaseViewModelData
   )
```

UIKit

```swift
init(
     data: APGatewayBaseViewModelData,
     styleManager: APStyleManager = APStyleManager()
   )
```

**Opis konstruktora**

| atrybut                            | opis                                                                                                                                                                                                                                   |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `data: APGatewayBaseViewModelData` | Obiekt konfiguracyjny widoku [APGatewayBaseViewModelData](szczegolowy-opis-klas-i-metod.md#apgatewaybaseviewmodeldata)                                                                                                                 |
| `styleManager: APStyleManager`     | W przypadku użycia **APBankTransferGatewayContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |

### APBlikGatewayView / APBlikGatewayContainerView

Widok rozwiniętego kanału płatności typu _Blik_. Nie zawiera podsumowania płatności.

Publiczny konstruktor

SwiftUI

```swift
init(
     data: APGatewayBaseViewModelData
   )
```

UIKit

```swift
init(
     data: APGatewayBaseViewModelData,
     styleManager: APStyleManager = APStyleManager()
   )
```

**Opis konstruktora**

| atrybut                            | opis                                                                                                                                                                                                                           |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `data: APGatewayBaseViewModelData` | Obiekt konfiguracyjny widoku [APGatewayBaseViewModelData](szczegolowy-opis-klas-i-metod.md#apgatewaybaseviewmodeldata)                                                                                                         |
| `styleManager: APStyleManager`     | W przypadku użycia **APBlikGatewayContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |

### APCardGatewayView / APCardGatewayContainerView

Widok rozwiniętego kanału płatności typu _Card_. Nie zawiera podsumowania płatności.

Publiczny konstruktor

SwiftUI

```swift
init(
     data: APGatewayBaseViewModelData,
   )
```

UIKit

```swift
init(
     data: APGatewayBaseViewModelData,
     styleManager: APStyleManager = APStyleManager()
   )
```

**Opis konstruktora**

| atrybut                            | opis                                                                                                                                                                                                                           |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `data: APGatewayBaseViewModelData` | Obiekt konfiguracyjny widoku [APGatewayBaseViewModelData](szczegolowy-opis-klas-i-metod.md#apgatewaybaseviewmodeldata)                                                                                                         |
| `styleManager: APStyleManager`     | W przypadku użycia **APCardGatewayContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |

### APVisaGatewayView / APVisaGatewayContainerView

Widok rozwiniętego kanału płatności typu _Visa_. Nie zawiera podsumowania płatności.

Publiczny konstruktor

SwiftUI

```swift
init(
     data: APGatewayBaseViewModelData
   )
```

UIKit

```swift
init(
     data: APGatewayBaseViewModelData,
     styleManager: APStyleManager = APStyleManager()
   )
```

**Opis konstruktora**

| atrybut                            | opis                                                                                                                                                                                                                           |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `data: APGatewayBaseViewModelData` | Obiekt konfiguracyjny widoku [APGatewayBaseViewModelData](szczegolowy-opis-klas-i-metod.md#apgatewaybaseviewmodeldata)                                                                                                         |
| `styleManager: APStyleManager`     | W przypadku użycia **APVisaGatewayContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |

### APCardActivationGatewayView / APCardActivationGatewayContainerView

Widok przedstawiający formularz aktywacji karty za pomocą serwisu Autopay. Naliczana w nim jest opłata konsumencka, która będzie zwrócona klientowi.

Publiczny konstruktor

SwiftUI

```swift
init(
     apConfig: APConfig,
     payTappedCallback: APPayTappedCallback? = nil,
     paymentViewCallback: APPaymentViewCallback? = nil,
     tokenExpiredCallback: APTokenExpiredCallback? = nil
   )
```

UIKit:

```swift
init(
     apConfig: APConfig,
     styleManager: APStyleManager = APStyleManager(),
     payTappedCallback: APPayTappedCallback? = nil,
     paymentViewCallback: APPaymentViewCallback? = nil,
     tokenExpiredCallback: APTokenExpiredCallback? = nil
   )
```

**Opis konstruktora**

| atrybut                                         | opis                                                                                                                                                                                                                                     |
| ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `apConfig: APConfig`                            | Obiekt konfiguracyjny [APConfig](szczegolowy-opis-klas-i-metod.md#apconfig)                                                                                                                                                              |
| `styleManager: APStyleManager`                  | W przypadku użycia **APCardActivationGatewayContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())` |
| `payTappedCallback: APPWebViewCallback`         | Callback zwracający dane niezbędne do realizacji akcji przez aplikację, wywoływany jest w momencie kliknięcia przycisku płatności, wykorzystywany w wariancie (I)                                                                        |
| `paymentViewCallback: APPPaymentViewCallback?`  | Callback wywoływany w momencie zakończenia operacji aktywacji karty płatniczej, wykorzystywany w wariancie (II)                                                                                                                          |
| `tokenExpiredCallback: APTokenExpiredCallback?` | Callback wywoływany gdy SDK wykryje wygaśnięty token, zwraca obiekt błędu `APError`                                                                                                                                                      |

📌 Ważne: W zależnośći od tego czy zostanie przekazany `payTappedCallback` czy `paymentViewCallback` SDK realizuje różne scenariusze, jeśli planujesz przetwarzać transakcje po stronie aplikacji nie przekazuj `paymentViewCallback` do modelu danych oraz adekwatnie jeśli chcesz aby SDK przeprowadziło pełny proces transakcji nie przekazuj `payTappedCallback`.

**UWAGA:** Jeśli token wygaśnie, należy zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w obiekcie configuracyjnym `APConfig` metodą `setToken(token: String)`, odblokować interfejs i pozwolić użytkownikowi na kontynuowanie płatności.

### WebView / WebViewContainerView

Widok służący do obsługi strony przekierowania po dokonaniu płatności. Wyposażony jest w dodatkowe interfejsy JavaScript’owe i dodatkową metodę aktualizującą url pozwalając na reagowanie na zdarzenia wynikające z serwisu Autopay w trakcie dokończenia transakcji.

Publiczny konstruktor

```swift
init(
     url: URL,
     transactionCallback: @escaping APPWebViewCallback
   )
```

**Opis konstruktora**

| atrybut                                             | opis                                                                        |
| --------------------------------------------------- | --------------------------------------------------------------------------- |
| `url: URL`                                          | URL który ma zostać otwarty w webView np _redirectUrl_                      |
| `transactionCallback: @escaping APPWebViewCallback` | Callback wywoływany w momencie zakończenia płatności w komponencie WebView. |

## Callbacki

### APPPaymentViewCallback

Callback wywoływany w momencie zakończenia po stronie SDK procesu płatności w komponencie `APGatewayListView`. Możliwa konieczność kontynuowania w WebView w przypadku gdy płatność tego wymaga.

| wartość                  | opis                |
| ------------------------ | ------------------- |
| `result: APTransaction?` | Rezultat płatności. |
| `error: APError?`        | Błąd płatności.     |

### APPWebViewCallback

Callback wywoływany w momencie zakończenia płatności w komponencie WebView. Callback może zwrócić zarówno status jak i bład w postaci nil ponieważ jest to wstępna informacja o statusie transakcji. Dla potwierdzenia rezultatu należy skorzystać z metody `getTransactionStatus(orderId: String)` z klasy `Autopay`

| wartość             | opis              |
| ------------------- | ----------------- |
| `result: APResult?` | Status płatności. |
| `error: APError?`   | Błąd płatności.   |

### APPayTappedCallback

Callback wywoływany w momencie kliknięcia przycisku płatności podczas gdy korzystamy z indywidualnych kontrolek, w celu przekazania informacji jaką płatność powinna zeralizować aplikacja.

| wartość                               | opis                                          |
| ------------------------------------- | --------------------------------------------- |
| `paymentGroup: APGatewayPaymentGroup` | Określa jaka grupa płatności została wybrana. |
| `gateway: APGateway`                  | Obiekt kanału płatności.                      |
| `transactionData: APTransactionData`  | Obiekt wymagany do wykonania transakcji.      |

### APCustomerFeeDidUpdatedCallback

Callback wywoływany w momencie aktualizacji opłaty konsumenckiej.

| wartość                      | opis                         |
| ---------------------------- | ---------------------------- |
| `customerFee: APCustomerFee` | Obiekt opłaty konsumenckiej. |

### APTokenExpiredCallback

Callback wywoływany w momencie gdy SDK wykryje wygaśnięty token.

| wartość           | opis                             |
| ----------------- | -------------------------------- |
| `error: APError?` | Obiekt błędu wygaśniętego tokenu |

## Enumy

### APEnvironmentEnum

Rodzaj środowiska wymagany podczas inicjalizowania `APConfig`.

| wartość | opis                                                                                                                                    |
| ------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `prod`  | Adres środowiska produkcyjnego Systemu Płatności Online BM [https://pay.autopay.eu](https://pay.autopay.eu/).           |
| `dev`   | Adres środowiska deweloperskiego Systemu Płatności Online BM [https://testpay.autopay.eu](https://testpay.autopay.eu/). |

### APButtonStyleType

Enum reprezentujący styl przycisku wykorzystywany podczas inicjalizowania `APButtonStyle`. Zawiera domyślne wartości dla danego stylu przycisku.

| Wartość       | Opis                                                               |
| ------------- | ------------------------------------------------------------------ |
| primary       | Reprezentuje przycisk główny (wypełniony) w SDK                    |
| secondary     | Reprezentuje przycisk dodatkowy (obramowany) w SDK                 |
| tertiary      | Reprezentuje przycisk pomocniczy (obramowany) w SDK                |
| paymentMethod | Reprezentuje przycisk kanału płatności na liście kanałów płatności |

### APErrorEnum

Błąd zwracany przez SDK. Może dotyczyć transakcji, komunikacji z Systemem Płatności Online BM bądź wewnętrznych błędów SDK.

| wartość                           | wartość z API                        | opis                                                                           |
| --------------------------------- | ------------------------------------ | ------------------------------------------------------------------------------ |
| `insufficientStartAmount`         | `INSUFFICIENT_START_AMOUNT`          | Niedozwolona kwota transakcji                                                  |
| `bankDisabled`                    | `BANK_DISABLED`                      | Bank z którego próbujesz dokonać transakcji jest obecnie niedostępny.          |
| `blockMultipleTransactions`       | `BLOCK_MULTIPLE_TRANSACTIONS`        | Zablokowano próbę wykonania wielu transakcji z tym samym numerem zamówienia    |
| `blockPaidTransactions`           | `BLOCK_PAID_TRANSACTIONS`            | Transakcja o podanym numerze (orderId) została już opłacona.                   |
| `outdatedError`                   | `OUTDATED_ERROR`                     | Transakcja przeterminowana (czas płatności upłynął).                           |
| `internalServerError`             | `INTERNAL_SERVER_ERROR`              | Wewnętrzny błąd serwera                                                        |
| `unexpectedError`                 | `UNEXPECTED_ERROR`                   | Niespodziewany błąd                                                            |
| `unexpectedFormatError`           | `UNEXPECTED_FORMAT_ERROR`            | Niespodziewany format                                                          |
| `errFieldNotFound`                | `ERR_FIELD_NOT_FOUND`                | Brak wymaganego parametru.                                                     |
| `errBadClientSource`              | `ERR_BAD_CLIENT_SOURCE`              | Błąd ogólny.                                                                   |
| `nrParametersError`               | `NR_PARAMETERS_ERROR`                | Błędna liczba parametrów                                                       |
| `transactionOutdated`             | `TRANSACTION_OUTDATED`               | Transakcja nieaktualna                                                         |
| `linkValidityTimeOutdated`        | `LINK_VALIDITY_TIME_OUTDATED`        | Odnośnik do transakcji przekroczył swój czas ważności                          |
| `transactionValidityTimeOutdated` | `TRANSACTION_VALIDITY_TIME_OUTDATED` | Przekazany czas ważności transakcji jest czasem przeszłym                      |
| `multiplyTransaction`             | `MULTIPLY_TRANSACTION`               | Wystąpiła więcej niż jedna transakcja o tym samym identyfikatorze              |
| `transactionCanceled`             | `TRANSACTION_CANCELED`               | Transakcja anulowana                                                           |
| `multiplyPaidTransaction`         | `MULTIPLY_PAID_TRANSACTION`          | Wystąpiła więcej niż jedna opłacona transakcja o tym samym identyfikatorze     |
| `bankTemporaryMaintenance`        | `BANK_TEMPORARY_MAINTENANCE`         | Bank jest tymczasowo niedostępny. Prawdopodobnie z powodu prac konserwacyjnych |
| `startAmountOutOfRange`           | `START_AMOUNT_OUT_OF_RANGE`          | Początkowa kwota transakcji jest poza dozwolonym zakresem                      |
| `nonAccountedLimitExceeded`       | `NON_ACCOUNTED_LIMIT_EXCEEDED`       | Przekroczono limit rozpoczętych transakcji                                     |
| `parsingError`                    | `PARSING_ERROR`                      | Błąd parsowania                                                                |
| `emptyTransactionError`           | `EMPTY_TRANSACTION_ERROR`            | Pusta transakcja                                                               |
| `notConfirmedError`               | `NOT_CONFIRMED_ERROR`                | Operacja nie powiodła się.                                                     |
| `connectionError`                 | `CONNECTION_ERROR`                   | Błąd połączenia internetowego.                                                 |
| `generalError`                    | `GENERAL_ERROR`                      | Błąd ogólny                                                                    |
| `ticketUsed`                      | `TICKET_USED`                        | Podany kod został już wykorzystany.                                            |
| `wrongTicket`                     | `WRONG_TICKET`                       | Podano nieprawidłowy kod.                                                      |
| `ticketExpired`                   | `TICKET_EXPIRED`                     | Podany kod wygasł.                                                             |
| `emptyGatewaysError`              | Błąd wewnętrzny                      | Brak kanałów płatności                                                         |
| `invalidUrlError`                 | Błąd wewnętrzny                      | Niepoprwanie skonfigurowany url                                                |
| `paywayNotFound`                  | `PAYWAY_NOT_FOUND`                   | Wybrany kanał płatności jest nieaktywny.                                       |

### APRecurringActionEnum

Pole wymagane dla płatności automatycznych, określające możliwe akcje na płatności automatycznej.

| wartość           | opis                                                             |
| ----------------- | ---------------------------------------------------------------- |
| `initWithPayment` | Aktywacja płatności automatycznej wraz z opłatą za towar/usługę. |
| `initWithRefund`  | Aktywacja płatności automatycznej, a następnie zwrot wpłaty.     |
| `auto`            | Płatność cykliczna (obciążenie bez udziału Klienta).             |
| `deactivate`      | Dezaktywacja płatności automatycznej.                            |
| `unknown`         | Nieznana wartość.                                                |

### APGatewayType

Typ kanału płatności. Dostępne typy kanałów płatności są zależne od konfiguracji dla danego serwisu (`serviceId`).

| wartość           | opis                                                                                |
| ----------------- | ----------------------------------------------------------------------------------- |
| `blik`            | Kanał płatności typu BLIK.                                                          |
| `autoPaymentBlik` | Kanał płatności typu BLIK (z opcją włączenia płatności automatycznej).              |
| `pbl`             | Kanał płatności typu PBL (przelew bankowy).                                         |
| `bankTransfer`    | Kanał płatności typu przelew bankowy (typ agregujący typy `PBL` i `FAST_TRANSFER`). |
| `fastTransaction` | Kanał płatności typu szybki przelew.                                                |
| `card`            | Kanał płatności typu karta płatnicza.                                               |
| `autoPaymentCard` | Kanał płatności typu karta płatnicza (z opcją włączenia płatności automatycznej).   |
| `installments`    | Kanał płatności typu raty.                                                          |
| `pis`             | Kanał płatności typu PIS.                                                           |
| `ais`             | Kanał płatności typu AIS.                                                           |
| `otp`             | Kanał płatności typu odroczony termin płatności.                                    |
| `masterPass`      | Kanał płatności typu Master Pass.                                                   |
| `googlePay`       | Kanał płatności typu Google Pay.                                                    |
| `visaCheckout`    | Kanał płatności typu Visa Checkout.                                                 |
| `visaMobile`      | Kanał płatności typu Visa Mobile.                                                   |
| `applePay`        | Kanał płatności typu Apple Pay.                                                     |
| `autoPaymentDcb`  | Kanał płatności automatycznej DCB.                                                  |
| `undefined`       | Nieznany typ kanału płatności.                                                      |

### APPaymentStatus

Enum reprezentujący rezultat transakcji.

| Wartość     | Opis                              |
| ----------- | --------------------------------- |
| success     | Poprawna autoryzacja transakcji.  |
| successMany | Wielokrotnie opłacona transakcja. |
| pending     | Transakcja oczekuje na opłacenie. |
| failure     | Błąd transakcji.                  |

### APRequestDataParamKey

Enum typu String reprezentujący klucze parametrów do stworzenia transakcji `APTransactionData`

| Wartość                  |
| ------------------------ |
| versionSDK               |
| messageId                |
| currencies               |
| serviceId                |
| currency                 |
| amount                   |
| gatewayId                |
| language                 |
| orderId                  |
| customerEmail            |
| customerPhone            |
| products                 |
| authorizationCode        |
| recurringAcceptanceState |
| recurringAcceptanceId    |
| recurringAcceptanceTime  |
| recurringAction          |
| paymentToken             |
| walletType               |
| acceptanceIdSuffix       |
| acceptanceStateSuffix    |
| acceptanceTimeSuffix     |

### APGatewayPaymentGroup

Enum wykorzystywany to wykluczenia grup płatności z widoku `APGatewayListView` oraz w callbacku `APPayTappedCallback`

| Wartość      | Opis                                             |
| ------------ | ------------------------------------------------ |
| blik         | Reprezentuje grupę płatności Blik.               |
| card         | Reprezentuje grupę płatności kartą.              |
| bankTransfer | Reprezentuje grupę płatności przelwów bankowych. |
| visa         | Reprezentuje grupę płatności Visa.               |
| applePay     | Reprezentuje płatność ApplePay.                  |

## Wyjątki

### APConfigurationError

Wyjątek informujący o błędnej konfiguracji klasy `Autopay`. Może zostać rzucony w przypadku **niepoprawnych** bądź **pustych argumentów** przesyłach w inicjalizatorach oraz metodach.

| wartość                        | opis                            |
| ------------------------------ | ------------------------------- |
| `unparsableTokenError`         | Nieprawidłowy format tokena.    |
| `authorizationEncryptionError` | Problem z autoryzacją requestu. |
| `invalidUrlError`              | Niepoprawny url.                |

##
