# Szczegółowy opis klas i metod

## Klasy

### Autopay

W zasadzie nie klasa, a obiekt zawierający metodę inicjalizacyjną projektu, konfigurację stylistyki oraz wszystkie metody potrzebne do samodzielnego komunikowania się z serwerem **Autopay**. Nie posiada konstruktora.

**init**

`public fun init(config: AutopayConfig)`

Metoda inicjalizująca działanie **SDK**. Jej parametr config zawiera wszystkie informacje niezbędne do funkcjonowania biblioteki pozwalające na prawidłową komunikację z serwisem **Autopay**. Jej wywołanie jest **NIEZBĘDNE** do korzystania z **SDK**. Zaleca się wywołanie jej w klasie dziedziczącej po `Application`, ale można w dowolnym innym momencie przed użyciem widoków lub metod udostępnionych przez bibliotekę. Każde ponowne wywołanie tej metody inicjalizuje **SDK** na nowo, powodując wyczyszczenie danym cache’owanych na potrzeby skrócenia czasu odpowiedzi na zapytania.

**updateToken**

`public fun updateToken(newToken: String)`

Służy do aktualizacji tokenu autoryzacyjnego.

**getGatewaysList**

`public suspend fun getGatewaysList(): List<APGateway>?`

Zwraca listę kanałów płatności zdefiniowanych dla skonfigurowanego serwisu. Należy pamiętać o wywołaniu jej w osobnym wątku ze względu na komunikację HTTP kryjącą się pod nią. Zwraca nulla gdy SDK nie zostało zainicjalizowane oraz SDK wypisuje w logach wyjątek.

`public fun getGatewaysListBlocking(): List<APGateway>?` - odpowiednik metody `getGatewaysList` dla JAVY.

**getRegulationsForGateway**

`public suspend fun getRegulationsForGateway(gatewayId: Long): List<APRegulation>?`

Zwraca listę regulaminów dla wybranego kanału płatności identyfikującego się parametrem `gatewayId`. Należy pamiętać o wywołaniu jej w osobnym wątku ze względu na komunikację HTTP kryjącą się pod nią. Zwraca nulla gdy SDK nie zostało zainicjalizowane oraz SDK wypisuje w logach wyjątek.

`public fun getRegulationsForGatewayBlocking(gatewayId: Long): List<APRegulation>?` - odpowiednik metody `getRegulationsForGateway` dla JAVY.

**getCustomerFeeForGateway**

`public suspend fun getCustomerFeeForGateway(gatewayId: Long, amount: BigDecimal): APCustomerFee?`

Zwraca opłatę konsumencką dla wybranego kanału płatności i zdefiniowanej kwoty transakcji. Należy pamiętać o wywołaniu jej w osobnym wątku ze względu na komunikację HTTP kryjącą się pod nią. Zwraca nulla gdy SDK nie zostało zainicjalizowane oraz SDK wypisuje w logach wyjątek.

`public fun getCustomerFeeForGatewayBlocking(gatewayId: Long, amount: BigDecimal): APCustomerFee?` - odpowiednik metody `getCustomerFeeForGateway` dla JAVY.

**makeTransaction**

`public suspend fun makeTransaction(transactionData: APTransactionData): APPreTransaction?`

Rozpoczyna transakcję na podstawie zdefiniowanych danych kryjących się pod parametrem `transactionData`. Zwraca dane transakcji. Należy pamiętać o wywołaniu jej w osobnym wątku ze względu na komunikację HTTP kryjącą się pod nią. Zwraca nulla gdy SDK nie zostało zainicjalizowane oraz SDK wypisuje w logach wyjątek.

`public fun makeTransactionBlocking(transactionData: APTransactionData): APPreTransaction?` - odpowiednik metody `makeTransaction` dla JAVY.

**checkTransactionStatus**

`public suspend fun checkTransactionStatus(orderId: String): APTransactionStatus`

Odpytuje serwis o status transakcji kryjący się pod identyfikatorem `orderId`. Zwraca dane o statusie transakcji. Należy pamiętać o wywołaniu jej w osobnym wątku ze względu na komunikację HTTP kryjącą się pod nią. Zwraca nulla gdy SDK nie zostało zainicjalizowane oraz SDK wypisuje w logach wyjątek.

`public fun checkTransactionStatusBlocking(orderId: String): APTransactionStatus?` - odpowiednik metody `checkTransactionStatus` dla JAVY.

**setUiStyle** 

`public fun setUiStyle(style: AutopayUIStyle)`

Ustawia styl widoków wewnątrz SDK

**getCurrentUiStyle** 

`public fun getCurrentUiStyle(): AutopayUiStyle`

Zwraca obecnie ustawiony styl widoków wewnątrz SDK

**updateFont** 

`public fun updateFont(@FontRes typefaceResId: Int, @FontRes typefaceBoldResId: Int)`

Uaktualnia czcionkę dla wszystkich rodzajów tekstów na podstawie parametru typefaceResId oraz typefaceBoldResId będących czcionkami z plików źródłowych, pozostawiając dotychczasowe parametry stylu SDK. typefaceResId odpowiada za czcionkę lekką o wadze 400, a typefaceBoldResId cięższą o wadze 500. Zmienia czcionkę definicji zawartych  w obiekcie APTypography jak i pozostałych stylach typu: przyciski, formularze danych itd.

**setRegulationsHidden**

`public fun setRegulationsHidden(hiddenList: Map<APGatewayPaymentGroup, Boolean>)`

Steruje możliwością wyświetlania regulaminów na kanałach płatności. Podając parę Grupa płatności - true - wyłączamy widoczność regulaminów na wybranej grupie płatności.
📌 Ważne: `APGatewayPaymentGroup.CARD` ma wpływ na wyświetlanie regulaminów zarówno na kanale płatności kartą płatniczą jak i aktywacji karty płatniczej.

**setBLIKContentHeader**

`public fun setBLIKContentHeader(@StringRes value: Int)`

Ustawia własne tłumaczenie nagłówka przy płatności typu BLIK.

**setBankContentHeader**

`public fun setBankContentHeader(@StringRes value: Int)`

Ustawia własne tłumaczenie nagłówka przy płatności typu Przelew bankowy.

### AutopayConfig

Klasa reprezentująca pełną konfigurację **SDK**. Jej dane są wymagana podczas korzystania z widoków udostępnionych przez **SDK**, a także do prawidłowego korzystania z metod pozwalających na samodzielną komunikację z serwisem **Autopay**. Klasa posiada wewnątrz Buildera pozwalającego na łatwiejsze utworzenie obiektu konfiguracyjnego.

@[AutopayConfig](codes/android/00_config_constructor.md)

- `environmentType` Definiuje typ środowiska z jakiego chcemy korzystać.
- `token` Token autoryzacyjny do komunikacji HTTP.
- `serviceId` Identyfikator serwisu klienta.
- `acceptorId` Identyfikator akceptanta.
- `contextPath` Służy do ustawiania dedykowanego kontekstu kanału płatności pozwalającego na ostylowanie kanału pod klienta. Wartość domyślna **/payment**.
- `currencies` Lista walut, na podstawie której będą pobierane kanały płatności. Domyślnie lista kanałów płatności pobierana jest dla waluty **PLN**. Dopuszczalne są jedynie wartości: PLN, EUR, GBP oraz USD. Waluty muszą być zgodne z konfiguracją kanału na backendzie Autopay. Płatność odbędzie się w **pierwszej** walucie znajdującej się na tej liście.
- `regulationsFallbackLanguageCode` - kod języka regulaminów, w przypadku gdy w domyślnym języku urządzenia nie są dostępne. Kod w formacie ISO-3166-1 alfa-2 (domyślnie `PL`)
- `merchantCountryCode` Kod kraju merchanta używany przez Google Pay. Domyślnie **PL**. Dwuznakowy kod języka w ISO 639-1.
- `googlePayMerchantId` Identyfikator merchanta używany przez Google Pay. Domyślnie **null**.
- `enableLogging` Definiuje, czy dane mają być logowane na konsoli - informacje dla programisty. Domyślnie *false*. Dodatkowo dane takie jak numer karty, kod CVV czy token karty pozostają anonimizowane. Ze względów bezpieczeństwa w buildach produkcyjnych **ZALECA SIĘ** pozostawienie flagi z wartością false.

Przykładowe użycie buildera w projektach pisanych w Javie:

@[AutopayConfig - Java](codes/android/00_java_config.md)

## Modele danych

### APCardData

@[APCardData](codes/android/00_card_data_constructor.md)

- `cardNumber` Numer karty. 16-znakowy ciąg cyfr bez spacji.
- `expiryMonth` Numer miesiąca, zaczynając od 1 - styczeń.
- `expiryYear` Rok ważności karty.
- `cvv` Kod CVV, trzycyfrowy ciąg cyfr.
- `firstName` Imię posiadacza karty, opcjonalne.
- `lastName` Nazwisko posiadacza karty, opcjonalne.

### AutopayUIStyle

@[AutopayUIStyle](codes/android/00_style_autopay_ui_style.md)

- `errorColor` - kolor błędów
- `footerIconsColor` - kolor ikon partnerów wystepujący na dole listy kanałów płatności
- `typography` - zestaw styli tekstów wraz ze wspólnym kolorem domyślnym tekstów
- `primaryButtonStyle` - zestaw parametrów stylizujących przycisk główny (wypełniony) w SDK - używany na większości ekranów (wyjątki opisane niżej, w `secondaryButtonStyle`, `tertiaryButtonStyle`, `gatewayButtonStyle`)
- `secondaryButtonStyle` - zestaw parametrów stylizujących przycisk dodatkowy (obramowany) w SDK, użwany w dialogu z odnośnikami do Regulaminów, dialogu przewalutowania, dialogu skanowania NFC
- `tertiaryButtonStyle` - zestaw parametrów stylizujących przycisk dodatkowy (obramowany) w SDK o pomniejszonej czcionce, używany w sekcji regulaminów
- `inputStyle` - zestaw parametrów stylizujących widoki wprowadzania danych tekstowych
- `gatewayButtonStyle` - zestaw parametrów stylizujących przycisk kanału płatności na liście kanałów płatności
- `gatewayTitleStyle` - zestaw parametrów stylizujących tytuł kanału płatności po wybraniu danej formy i rozwinięciu jej szczegółów
- `checkboxStyle` - zestaw parametrów stylizujących widoki typu checkbox
- `switchStyle` - zestaw parametrów stylizujących widoki typu switch
- `radioButtonStyle` - zestaw parametrów stylizujących widoki typu radio button
- `dialogStyle` - zestaw parametrów stylizujących wyświetlane okna w SDK
- `loaderStyle` - zestaw parametrów stylizujących widoki ładowania danych
- `bankGridStyle` - zestaw parametrów stylizujących siatkę banków na grupie "Przelewy bankowe" 
- `paymentSummaryStyle` - zestaw parametrów stylizujących etykietę z podsumowaniem płatności
- `dccPaymentFormStyle` - zestaw parametrów stylizujących okno z formularzem przewalutowania przy płatności kartą

Przykładowe użycie buildera w projektach pisanych w Javie:

@[AutopayConfig - Java](codes/android/00_java_style.md)

### APThemeColor

@[APThemeColor](codes/android/00_style_theme_color.md)

Reprezentuje wartość koloru w dwóch trybach - jasny ciemny

- `lightColor` - wartośc koloru dla trybu jasnego
- `darkColor` - wartośc koloru dla trybu ciemnego, opcjonalna, gdy jej brak => brana jest wartość `lightColor`

### APTextStyleWrapper

@[APTextStyleWrapper](codes/android/00_style_text_style_wrapper.md)

Zestaw parametrów potrzebnych do utworzenia obiektu **androidx.compose.ui.text.TextStyle** w projektach pisanych w Javie.

### APTypography

@[APTypography](codes/android/00_style_typography.md)

Zestaw styli tekstów wraz ze wspólnym kolorem domyślnym tekstów. System zakłada użycie biblioteki w 4 rozmiarach - 12, 14, 16, 18, o wadze 400. Jedynie czcionka o rozmiarze 12 ma swój pogrubiony odpowiednik o wadze 500. Każdy styl tekstu przekazywany jest w postaci parametrów **androidx.compose.ui.text.TextStyle**

- `defaultTextColor` - kolor tekstu dla wszystkich styli tekstów w tym obiekcie, nie dotyczy koloru tekstów na przyciskach
- `labelSmall` - mały styl tekstu, domyślnie wielkości 12.sp
- `labelSmallBold` - mały styl tekstu pogrubiony, domyślnie waga 500, wielkość 12.sp
- `labelMedium` - średni styl tekstu, domyślnie wielkości 14.sp
- `labelLarge`- wielki styl tekstu, domyślnie wielkości 16.sp
- `labelXLarge` - największy styl tekstu, domyślnie wielkości 18.sp

### APButtonStyle

@[APButtonStyle](codes/android/00_style_button.md)

Zestaw parametrów stylizujących przyciski w SDK

- `containerColor` - kolor tła przycisku
- `inactiveContainerColor` - kolor tła przycisku w stanie zablokowanym
- `contentColor` - kolor treści przycisku
- `inactiveContentColor` - kolor treści przycisku w stanie zablokowanym
- `borderColor` - kolor obramowania przycisku
- `inactiveBorderColor` - kolor obramowania przycisku w stanie zablokowanym
- `radius` - promień zaokrąglenia przycisku
- `borderWidth` - grubość obramowania
- `textStyle` - styl tekstu na przycisku
- `minHeight` - minimalna wysokość przycisku

Zawiera domyślne wartości dla **primaryButtonStyle**, **secodnaryButtonStyle** oraz **tertiaryButtonStyle**

### APTextInputStyle

@[APTextInputStyle](codes/android/00_style_text_input.md)

Zestaw parametrów stylizujących widoki wprowadzania danych tekstowych

- `inputTextStyle` - styl tekstu wprowadzanego
- `inputTextColor` - kolor tekstu wprowadzanego
- `placeholderTextColor` - kolor tekstu placeholdera
- `labelTextStyle` - styl tekstu etykiety nad widokiem
- `labelTextColor` - kolor tekstu etykiety nad widokiem
- `errorTextStyle` - styl tekstu błędu pod widokiem
- `errorTextColor` - kolor tekstu błędu pod widokiem
- `borderInactiveColor` - kolor obramowania w stanie domyślnym
- `borderActiveColor` - kolor obramowania w stanie zaznaczonym
- `borderErrorColor` - kolor obramowania w przypadku błędu w formularzu
- `backgroundColor` - kolor tła wewnątrz obramowania
- `trailingIconsColor` - kolor ikon dodatkowych (OCR i NFC w formularzu karty płatniczej w okienku numeru karty płatniczej)
- `radius` - promień załamania obramowania
- `strokeWidth` - grubość obramowania
- `spaceBetweenInputs` - odległość między polami w formularzu

### APGatewayButtonStyle

@[APGatewayButtonStyle](codes/android/00_style_gateway_button.md)

Zestaw parametrów stylizujących przycisk kanału płatności na liście kanałów płatności

- `backgroundColor` - kolor wypełniania wewnątrz obramowania
- `borderColor` - kolor obramowania przycisku
- `iconColor` - kolor ikon na przycisku - tylko w przypadku Karty płatniczej oraz Przelewów bankowych, pozostałe przyciski mają ikony wielokolorowe odpowiadające ich markom
- `textColor` - kolor tekstu na przycisku
- `textStyle` - styl tekstu na przycisku
- `borderWidth` - grubość obramowania
- `radius` - promień załamania obramowania
- `minHeight` - minimalna wysokość przycisku

### APGatewayTitleStyle

@[APGatewayTitleStyle](codes/android/00_style_gateway_title.md)

Zestaw parametrów stylizujących tytuł kanału płatności po wybraniu danej formy i rozwinięciu jej szczegółów

- `backgroundColor` - kolor tła
- `iconColor` - kolor ikon na przycisku - tylko w przypadku Karty płatniczej oraz Przelewów bankowych, pozostałe przyciski mają ikony wielokolorowe odpowiadające ich markom
- `textColor` - kolor tekstu na przycisku
- `textStyle` - styl tekstu na przycisku
- `radius` - promień załamania tła

### APCheckboxStyle

@[APCheckboxStyle](codes/android/00_style_checkbox.md)

Zestaw parametrów stylizujących widoki typu checkbox

- `checkedColor` - kolor wypełnienia zaznaczonego checkboxa
- `uncheckedColor` - kolor obramowania w stanie domyślnym niezaznaczonym
- `errorColor` - kolor obramowania w przypadku błędu spowodowanego niezaznaczeniem checkboxa

### APSwitchStyle

@[APSwitchStyle](codes/android/00_style_switch_style.md)

Zestaw parametrów stylizujących widoki typu switch

- `checkedThumbColor` - kolor przełącznika w stanie zaznaczonym
- `uncheckedThumbColor` - kolor przełącznika w stanie niezaznaczonym
- `checkedTrackColor` - kolor tła w stanie zaznaczonym
- `uncheckedTrackColor` - kolor tła w stanie niezaznaczonym
- `checkedBorderColor` - kolor obramowania w stanie zaznaczonym
- `uncheckedBorderColor` - kolor obramowania w stanie niezaznaczonym

### APRadioButtonStyle

@[APRadioButtonStyle](codes/android/00_style_radio_button.md)

Zestaw parametrów stylizujących widoki typu radio button

- `checkedColor` - kolor w stanie zaznaczonym
- `uncheckedColor` - kolor w stanie odznaczonym

### APDialogStyle

@[APDialogStyle](codes/android/00_style_dialog.md)

Zestaw parametrów stylizujących wyświetlane okna w SDK

- `dialogRadius` - zaokrąglenie okna
- `dialogBackgroundColor` - kolor tła okna

### APLoaderStyle

@[APLoaderStyle](codes/android/00_style_loader.md)

Zestaw parametrów stylizujących widoki ładowania danych

- `color` - kolor loadera
- `size` - rozmiar loadera

### APBankGridStyle

@[APBankGridStyle](codes/android/00_style_bank_grid.md)

Zestaw parametrów stylizujących siatkę banków na grupie "Przelewy bankowe"

- `columns` - liczba kolumn w siatce banków
- `cellHeight` - wysokość komórki z ikoną banku
- `radius` - promień załamania obramowania komórki
- `backgroundColor` - kolor wypełnienia komórki wewnątrz obramowania
- `checkedBorderColor` - kolor obramowania zaznaczonego banku
- `uncheckedBorderColor` - kolor obramowania banku gdy nie jest zaznaczony

### APPaymentSummaryStyle

@[APPaymentSummaryStyle](codes/android/00_style_payment_summary.md)

Zestaw parametrów stylizujących etykietę z podsumowaniem płatności

- `backgroundColor` - kolor tła etykiety
- `borderColor` - kolor obramowania etykiety
- `borderWidth` - grubość obramowania etykiety
- `dividerColor` - kolor separatora w etykiecie
- `dividerHeight` - grubość separatora w etykiecie
- `radius` - promień załamania obramowania etykiety

### APDCCPaymentFormStyle

@[APDCCPaymentFormStyle](codes/android/00_style_dcc_payment_form.md)

Zestaw parametrów stylizujących okno z formularzem przewalutowania przy płatności kartą

- `selectedBorderColor` - kolor obramowania etykiety zaznaczonej waluty
- `unselectedBorderColor` - kolor obramowania etykiety niezaznaczonej waluty
- `cellRadius` - promień załamania obramowania etykiety z walutą
- `cellBackgroundColor` - kolor wypełnienia etykiety z walutą wewnątrz obramowania

### APCustomerFee

@[APCustomerFee](codes/android/00_customer_fee_constructor.md)

- `customerFee` Kwota opłata konsumenckiej.
- `receiverName` Odbiorca opłaty konsumenckiej.

### APEnvironmentType

@[APEnvironmentType](codes/android/00_environment_type.md)

Klasa definiująca środowisko, z którym chcemy się komunikować.

### APError

@[APError](codes/android/00_error_constructor.md)

Klasa reprezentująca błędy przychodzące z **SDK**, sama będąca błędem *Throwable*.

- `type` Typ błędu.
- `message` Wiadomość błędu, przydatne głównie dla programistów.
- `orderId` Numer zamówienia, występuje gdy błąd przychodzi w trakcie rozpoczynania transakcji.

### APErrorType

Enum reprezentujący typ błędu
| Wartość | Opis |
|---|---|
| `INSUFFICIENT_START_AMOUNT` | Niedozwolona kwota transakcji. |
| `BANK_DISABLED` | Bank z którego próbujesz dokonać transakcji jest obecnie niedostępny. |
| `BLOCK_MULTIPLE_TRANSACTIONS` | Zablokowano próbę wykonania wielu transakcji z tym samym numerem zamówienia. |
| `BLOCK_PAID_TRANSACTIONS` | Transakcja o podanym numerze (orderId) została już opłacona. |
| `OUTDATED_ERROR` | Transakcja przeterminowana (czas płatności upłynął). |
| `INTERNAL_SERVER_ERROR` | Wewnętrzny błąd serwera. |
| `UNEXPECTED_ERROR` | Niespodziewany błąd. |
| `UNEXPECTED_FORMAT_ERROR` | Niespodziewany format. |
| `ERR_FIELD_NOT_FOUND` | Brak wymaganego parametru. |
| `ERR_BAD_CLIENT_SOURCE` | Błąd ogólny. |
| `NR_PARAMETERS_ERROR` | Błędna liczba parametrów. |
| `TRANSACTION_OUTDATED` | Transakcja nieaktualna. |
| `LINK_VALIDITY_TIME_OUTDATED` | Odnośnik do transakcji przekroczył swój czas ważności. |
| `TRANSACTION_VALIDITY_TIME_OUTDATED` | Przekazany czas ważności transakcji jest czasem przeszłym. |
| `MULTIPLY_TRANSACTION` | Wystąpiła więcej niż jedna transakcja o tym samym identyfikatorze. |
| `TRANSACTION_CANCELED` | Transakcja anulowana. |
| `MULTIPLY_PAID_TRANSACTION` | Wystąpiła więcej niż jedna opłacona transakcja o tym samym identyfikatorze. |
| `BANK_TEMPORARY_MAINTENANCE` | Bank jest tymczasowo niedostępny. Prawdopodobnie z powodu prac konserwacyjnych. |
| `START_AMOUNT_OUT_OF_RANGE` | Początkowa kwota transakcji jest poza dozwolonym zakresem. |
| `NON_ACCOUNTED_LIMIT_EXCEEDED` | Przekroczono limit rozpoczętych transakcji. |
| `PARSING_ERROR` | Błąd parsowania. |
| `EMPTY_TRANSACTION_ERROR` | Pusta transakcja. |
| `NOT_CONFIRMED_ERROR` | Operacja nie powiodła się. |
| `CONNECTION_ERROR` | Błąd połączenia internetowego. |
| `GENERAL_ERROR` | Błąd ogólny. |
| `TICKET_USED` | Podany kod został już wykorzystany. |
| `WRONG_TICKET` | Podano nieprawidłowy kod. |
| `PAYWAY_NOT_FOUND` | Wybrany kanał płatności jest nieaktywny. |
| `TICKET_EXPIRED` | Podany kod wygasł. |
| `TOKEN_EXPIRED` | Podatny token wygasł, należy go zaktualizować metodą `Autopay.updateToken()` |

### APEvent

Zdarzenia przychodzące w callbacku *APWebView*. 

| Wartość | Opis |
|---|---|
| `PAGE_LOADED` | Załadowano stronę.|
| `CONTENT_LOADED` | Załadowano dane na stronie (po zdarzeniu `PAGE_LODADED`.)|
| `LATER_CLICK` | Rezygnacja z transakcji.|

### APGateway

@[APGateway](codes/android/00_gateway_constructor.md)

Klasa opisująca kanał płatności.

- `gatewayId` Identyfikator kanału płatności.
- `gatewayName` Nazwa kanału płatności.
- `gatewayType` Typ kanału płatności.
- `bankName` Nazwa banku, jeśli kanał dotyczy banku, inaczej puste.
- `iconURL` Link do ikony kanału płatności.
- `currenciesList` Lista obsługiwanych walut przez kanał płatności.
- `group` Grupa kanału płatności.

### APGatewayPaymentGroup

Grupa kanałów płatności według której są one pogrupowane w widoku `APGatewayListCompose`/`APGatewayListView`.

| Wartość | Opis |
|---|---|
| `BLIK` | Grupa kanałów płatności BLIK |
| `CARD` | Grupa kanałów płatności płatności kartą  |
| `BANK_TRANSFER` | Grupa kanałów płatności przelewm bankowym  |
| `VISA` | Grupa kanałów płatności Visa Mobile  |
| `GOOGLE_PAY` | Grupa kanałów płatności Google Pay |

### APGatewayType

Enum reprezentujący typ kanału płatności.

| Wartość | Opis |
|---|---|
| `BLIK` | Kanał płatności typu BLIK. |
| `AUTO_PAYMENT_BLIK` | Kanał płatności typu BLIK (z opcją włączenia płatności automatycznej). |
| `PBL` | Kanał płatności typu PBL (przelew bankowy). |
| `FAST_TRANSFER` | Kanał płatności typu szybki przelew. |
| `CARD` | Kanał płatności typu karta płatnicza. |
| `AUTO_PAYMENT_CARD` | Kanał płatności typu karta płatnicza (z opcją włączenia płatności automatycznej). |
| `INSTALLMENTS` | Kanał płatności typu raty. |
| `PIS` | Kanał płatności typu PIS. |
| `AIS` | Kanał płatności typu AIS. |
| `OTP` | Kanał płatności typu odroczony termin płatności. |
| `MASTER_PASS` | Kanał płatności typu Master Pass. |
| `GOOGLE_PAY` | Kanał płatności typu Google Pay. |
| `VISA_MOBILE` | Kanał płatności typu Visa MObile. |
| `VISA_CHECKOUT` | Kanał płatności typu Visa Checkout. |
| `APPLE_PAY` | Kanał płatności typu Apple Pay. |
| `UNDEFINED` | Nieznany typ kanału płatności. |

### APPreTransaction

@[APPreTransaction](codes/android/00_pre_transaction_constructor.md)

Klasa reprezentująca dane rozpoczętej transakcji.

- `orderId` Identyfikator transakcji.
- `serviceId` Identyfikator serwisu obsługującego transakcję.
- `status` Status transakcji.
- `redirectUrl` Url strony do przekierowania w celu dokończenia transakcji. Może być pusty, wtedy transakcja jest w trakcie realizacji i można sprawdzić jej status.
- `reason` Powód nie dokonania transakcji, jeśli istnieje. Jeśli puste, transakcja się rozpoczęła. Najlepszym sposobem jest próba zmapowania wartości reason na enum `APErrorType`.
- `confirmation` Status potwierdzenia przyjęcia zlecenia.

### APConfirmation

@[APConfirmation](codes/android/00_confirmation_enum.md)

Status potwierdzenia przyjęcia zlecenia.
- `CONFIRMED` Operacja powiodła się. **Uwaga!** Nie oznacza to wykonania obciążenia!
- `NOTCONFIRMED` Operacja nie powiodła się.

### APProduct

@[APProduct](codes/android/00_product_constructor.md)

Informacje o produktach które możemy dodać jako parametry transakcji.
- `subAmount` Kwota produktu.
- `params` Dodatkowe parametry w postaci klucz-wartość.

### APRegulation

@[APRegulation](codes/android/00_regulations_constructor.md)

Klasa reprezentująca dane na temat regulaminów przypisanych do kanałów płatności. Klasa `Label` przedstawia treści tych regulaminów.
- `regulationId` Identyfikator regulaminu.
- `type` Typ regulaminu.
- `url` Link do pełnej treści regulaminu.
- `labelList` Lista treści regulaminu.
    - `labelId` Identyfikator treści regulaminu
    - `inputLabel` Treść regulaminu - HTML.
    - `placement` Sugerowane umiejscowienie treści.
    - `showCheckbox` Informacja czy powinien być pokazany checkbox obok treści.
    - `checkboxRequired` Informacja, czy treść musi zostać zaakceptowana checkboxem.

*Dodatkowe parametry transakcji*

Regulaminy mogą posiadać dodatkowe parametry potrzebne do stworzenia zapytania o rozpoczęcie transakcji. W przypadku własnej implementacji obsługi transakcji należy dodać je na podstawie wcześniej pobranych regulaminów. Metoda tworząca taką mapę parametrów to `getPaymentParamsIfNeeded`.

`fun getPaymentParamsIfNeeded(): Map<String, String>`

### APResult

Enum reprezentujący rezultat transakcji. 

| Wartość | Opis |
|---|---|
| `SUCCESS` | Poprawna autoryzacja transakcji. |
| `SUCCESS_MANY` | Wielokrotnie opłacona transakcja. |
| `PENDING` | Transakcja oczekuje na opłacenie. |
| `FAILURE` | Błąd transakcji. |
| `TRANSACTION_CANCELED` | Transakcja anulowana. |

### APSdkState

@[APSdkState](codes/android/00_sdk_state.md)

Klasa reprezentująca obecny stan widoku *APGatewayListCompose*/*APGatewayListView*. 

- *APGatewaysLoading* Definiuje stan ładowania listy kanałów płatności.
- *APGatewaysList* Definiuje stan wyświetlania kanałów płatności.
- *APGatewayDetails* Definiuje stan wyświetlania szczegółów grupy kanałów płatności. Grupa zdefiniowana jest parametrem *gatewayGroup*.
- *APPreTransactionInProgress* Definiuje stan rozpoczęcia transakcji i oczekiwania na jej wykonanie w obrębie wskazanej grupy kanałów płatności parametrem *gatewayGroup*.

### APTransactionData

@[APTransactionData](codes/android/00_transaction_data_constructor.md)

Klasa reprezentuje dane przekazywane do serwisu **Autopay** w celu rozpoczęcia transakcji. Jej jedynym wymaganym parametrem jest kwota transakcji.

- `amount` Kwota transakcji do opłacenia.
- `orderId` Identyfikator transakcji. Opcjonalny parametr. Automatycznie generowany przez UUID, ale SDK pozwala na własne oznaczenie transakcji swoim identyfikatorem. Musi posiadać 32 znaki składające się tylko z cyfr i liter oraz **BYĆ UNIKATOWY** w systemie partnera.
- `gatewayId` Identyfikator kanału płatności. Opcjonalny parametr. Brak podania odpowiedniej wartości poskutkuje zwróconym adresem przekierowania z wyborem kanału płatności i pełną obsługą transakcji w przeglądarce.
- `language` Kod języka obsługiwanej transakcji. Opcjonalny parametr. Domyślnie brany z języka aplikacji. **KONIECZNIE** kod musi być z dużych liter.
- `authorizationCode` Kod autoryzacyjny. Opcjonalny parametr. Wymagany przy transakcji typu BLIK.
- `email` Email klienta. Opcjonalny parametr.
- `phone` Numer telefonu klienta. Opcjonalny parametr.
- `googlePaymentToken` Token transakcji Google Pay. Opcjonalny parametr. Wymagany przy transakcji z użyciem Google Pay.
- `products` Dodatkowe parametry produktów w koszyku. Opcjonalny parametr.
- `params` Dodatkowe parametry transakcji. Opcjonalny parametr.

### APTransactionStatus

@[APTransactionStatus](codes/android/00_transaction_status_constructor.md)

Klasa reprezentuje status transakcji dla danego `orderId`. 

- `orderId` Numer zamówienia.
- `serviceId` Identyfikator serwisu obsługującego płatność.
- `hash` Hash zwrócony z backendu Autopay.
- `remoteId` Identyfikator zwrócony z backendu Autopay.
- `messageId` Identyfikator wiadomości zwróconej z backendu Autopay.
- `transactions` List transakcji wchodzących w jedno zamówienie.
    - `orderId` Identyfikator pojedynczej transakcji.
    - `remoteId` Identyfikator z backendu.
    - `amount` Kwota transakcji.
    - `currency` Waluta transakcji.
    - `gatewayId` Identyfikator kanału płatności użytego do obsługi transakcji.
    - `paymentDate` Data dokonania płatności.
    - `paymentStatus` Status płatności.
    - `paymentStatusDetails` Dodatkowe informacje na temat statusu. Np. wyjaśnienie  w przypadku odrzucenia lub błędu płatności.

## UI

Każdy widok poza `APWebView` występuje w dwóch wersjach - Compose i View, w zależności od potrzeb projektu integrującego się z **SDK Autopay**. Widoki nie zawierają paddingów ani marginesów zewnętrznych, należy dodać je we własnym zakresie. Dodatkowo w celu uniknięcia konfliktów z skrolowalnymi wrapperami nie użyto w nich wbudowanych narzędzi do scrollowania, więc widoki mogą zajmować więcej przestrzeni niż jest dostępne na ekranach urządzeń. Dlatego **ZALECA** się umieszczenie widoków wewnątrz scrollowalnych widoków nadrzędnych. Wyjątkiem jest `APWebView`, który jest nadpisaniem klasycznego widoku *WebView*.

### APGatewayListCompose / APGatewayListView

@[APGatewayListCompose](codes/android/00_gateway_list_compose_constructor.md)

@[APGatewayListView constructor](codes/android/00_gateway_list_view_constructor.md)

@[APGatewayListView XML](codes/android/00_gateway_list_xml.md)

Widok listy kanałów płatności jest rozbudowanym widokiem obsługującym zarówno załadowanie listy kanałów płatności, ich wyświetlanie oraz rozwinięcie szczegółów wybranego kanału płatności wraz z załadowaniem regulaminów, opłaty konsumenckiej oraz dokonaniem płatności. Z racji tego, że ten widok posiada kilka stanów, zawiera on callback pozwalający reagować na zmieniający się stan. Po rozwinięciu szczegółów kanału płatności nadpisany jest systemowy callback na przycisk wstecz, by móc wrócić do listy kanałów płatności. Po dokonaniu płatności widok wraca do stanu załadowanej listy.

- `amount` Kwota obsługiwanej płatności.
- `paymentSummary` Tytuł podsumowania płatności. Pozostawienie pustego lub null-owego zakryje sekcje z podsumowaniem płatności. Nie będzie wtedy widoczna opłata konsumencka.
- `visibleGateways` Lista widocznych grup kanałów płatności. Domyślnie widoczne są wszystkie, jednak zależne jest to od konfiguracji serwisu Autopay. Kanał płatności Google Pay jest dodatkowo zależny od jego dostępności na urządzeniu.
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPaymentStateChange` Callback informujący o zmianach stanu widoku.
- `onPreTransactionDone` Callback informujący o rezultacie dokonanej płatności.
- `onPreTransactionError` Callback informujący o błędzie w trakcie dokonywania płatności oraz o wygaśnięciu tokenu. Błędy występujące w trakcie ładowania listy kanałów płatności, regulaminów i opłaty konsumenckiej obsługiwane są bezpośrednio przez widok. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APWebView

`class APWebView : WebView`

Klasa służąca do obsługi strony przekierowania po dokonaniu płatności. Wyposażona jest w dodatkowe interfejsy JavaScript’owe i dodatkową metodę `loadUrl` pozwalając na reagowanie na zdarzenia wynikające z serwisu Autopay w trakcie dokończenia transakcji.

**loadUrl**

@[APWebView load](codes/android/00_webview_load.md)

Metoda do wczytywania strony przekierowania transakcji

- `url` Adres strony www do załadowania.
- `transactionCallback` Callback informujący o statusie transakcji.
- `eventCallback` Callback informujący o zmianach na wyświetlanej stronie.
- `errorCallback` Callback informujący o występujących błędach w trakcie obsługi transakcji.

### APCardActivationCompose / APCardActivationView

@[APCardActivationCompose](codes/android/00_gateway_card_activation_compose.md)

@[APCardActivationView constructor](codes/android/00_gateway_card_activation_view_constructor.md)

@[APCardActivationView XML](codes/android/00_gateway_card_activation_xml.md)

Widok przedstawiający formularz aktywacji karty za pomocą serwisu Autopay. Naliczana w nim jest opłata konsumencka, która będzie zwrócona klientowi.

- `onActivationDone` Callback informujący o statusie aktywacji karty.
- `onActivationError` Callback informujący o występujących błędach w trakcie aktywacji karty oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onActivationDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `activationTextColor` Parametr zmiany koloru tekstu z informacją o opłacie za aktywacje karty.
- `activationTextSize` Parametr zmiany wielkości tekstu z informacją o opłacie za aktywacje karty.

### APBankGatewayCompose / APBankGatewayView

@[APBankGatewayCompose](codes/android/00_gateway_bank_compose_constructor.md)

@[APBankGatewayView constructor](codes/android/00_gateway_bank_view_constructor.md)

@[APBankGatewayView XML](codes/android/00_gateway_bank_xml.md)

Widok rozwiniętej grupy kanałów płatności typu *BANK*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APBlikGatewayCompose / APBlikGatewayView

@[APBlikGatewayCompose](codes/android/00_gateway_blik_compose_constructor.md)

@[APBlikGatewayView constructor](codes/android/00_gateway_blik_view_constructor.md)

@[APBlikGatewayView XML](codes/android/00_gateway_blik_xml.md)

Widok rozwiniętego kanału płatności typu *BLIK*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `contentHeader` opcjonalne pole do podania tłumaczenia do nagłówka kanału płatności.
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APCardGatewayCompose / APCardGatewayView

@[APCardGatewayCompose](codes/android/00_gateway_card_compose_constructor.md)

@[APCardGatewayView constructor](codes/android/00_gateway_card_view_constructor.md)

@[APCardGatewayView XML](codes/android/00_gateway_card_xml.md)

Widok rozwiniętego kanału płatności typu *CARD*. Nie zawiera podsumowania płatności. Obsługuje zarówno płatność kartą, płatność kartą automatyczną oraz oba kanały.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APGooglePayGatewayCompose / APGooglePayGatewayView

@[APGooglePayGatewayCompose](codes/android/00_gateway_google_compose_constructor.md)

@[APGooglePayGatewayView constructor](codes/android/00_gateway_google_view_constructor.md)

@[APGooglePayGatewayView XML](codes/android/00_gateway_google_xml.md)

Widok rozwiniętego kanału płatności typu *GOOGLE_PAY*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APVisaGatewayCompose / APVisaGatewayView

@[APVisaGatewayCompose](codes/android/00_gateway_visa_compose_constructor.md)

@[APVisaGatewayView constructor](codes/android/00_gateway_visa_view_constructor.md)

@[APVisaGatewayView XML](codes/android/00_gateway_visa_xml.md)

Widok rozwiniętego kanału płatności typu *VISA*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)
