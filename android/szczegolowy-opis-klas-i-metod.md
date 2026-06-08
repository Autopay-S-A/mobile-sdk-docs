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

```kotlin
public class AutopayConfig
private constructor(
    public val environmentType: APEnvironmentType,
    public val token: String,
    public val serviceId: String,
    public val acceptorId: String,
    public val contextPath: String,
    public val currencies: List<String>,
    public val regulationsFallbackLanguageCode: String,
    public val merchantCountryCode: String,
    public val googlePayMerchantId: String? = null,
    public val enableLogging: Boolean,
) {

    /** Builder class to make easier creation [AutopayConfig] object. */
    public class Builder(
        private val environmentType: APEnvironmentType,
        private val token: String,
        private val serviceId: String,
        private val acceptorId: String,
    ) {
        private var contextPath: String = DEFAULT_CONTEXT_PATH
        private var currencies: MutableList<String> = mutableListOf(DEFAULT_CURRENCY)
        private var regulationsFallbackLanguageCode: String =
            DEFAULT_FALLBACK_REGULATIONS_LANGUAGE_CODE
        private var merchantCountryCode: String = DEFAULT_COUNTRY_CODE
        private var googlePayMerchantId: String? = null
        private var enableLogging: Boolean = false

        init {
            require(token.isNotEmpty())
            require(serviceId.isNotEmpty())
            require(acceptorId.isNotEmpty())
        }

        public fun contextPath(value: String): Builder = apply {
            if (!value.isEmpty()) {
                contextPath = value
            }
        }

        public fun currencies(value: List<String>): Builder = apply {
            if (!value.isEmpty()) {
                currencies = value.toMutableList()
            }
        }

        public fun regulationsFallbackLanguageCode(value: String): Builder = apply {
            if (!value.isEmpty()) {
                regulationsFallbackLanguageCode = value
            }
        }

        public fun merchantCountryCode(value: String): Builder = apply {
            if (!value.isEmpty()) {
                merchantCountryCode = value
            }
        }

        public fun googlePayMerchantId(value: String?): Builder = apply {
            googlePayMerchantId = value
        }

        public fun enableLogging(value: Boolean): Builder = apply { enableLogging = value }

        public fun build(): AutopayConfig {
            return AutopayConfig(
                environmentType = environmentType,
                token = token,
                serviceId = serviceId,
                acceptorId = acceptorId,
                contextPath = contextPath,
                currencies = currencies.toList(),
                regulationsFallbackLanguageCode = regulationsFallbackLanguageCode,
                merchantCountryCode = merchantCountryCode,
                googlePayMerchantId = googlePayMerchantId,
                enableLogging = enableLogging,
            )
        }
    }

    public companion object {
        private const val DEFAULT_CONTEXT_PATH: String = "/payment"
        private const val DEFAULT_CURRENCY: String = "PLN"
        private const val DEFAULT_COUNTRY_CODE: String = "PL"
        private const val DEFAULT_FALLBACK_REGULATIONS_LANGUAGE_CODE: String = "PL"
    }
}
```

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

```java
Autopay.init(new AutopayConfig.Builder(
    APEnvironmentType.DEV.INSTANCE,
    "token",
    "serviceId",
    "acceptorId"
)
.contextPath("/payment")
.enableLogging(true)
.googlePayMerchantId("merchantId")
.build());
```

## Modele danych

### APCardData

```kotlin
public data class APCardData(
   val cardNumber: String,
   val expiryMonth: Int,
   val expiryYear: Int,
   val cvv: String,
   val firstName: String? = null,
   val lastName: String? = null
)
```

- `cardNumber` Numer karty. 16-znakowy ciąg cyfr bez spacji.
- `expiryMonth` Numer miesiąca, zaczynając od 1 - styczeń.
- `expiryYear` Rok ważności karty.
- `cvv` Kod CVV, trzycyfrowy ciąg cyfr.
- `firstName` Imię posiadacza karty, opcjonalne.
- `lastName` Nazwisko posiadacza karty, opcjonalne.

### AutopayUIStyle

```kotlin
public data class AutopayUIStyle(
    val typography: APTypography = APTypography(),
    val primaryButtonStyle: APButtonStyle = APButtonStyle.APPrimaryButtonStyle,
    val secondaryButtonStyle: APButtonStyle = APButtonStyle.APSecondaryButtonStyle,
    val tertiaryButtonStyle: APButtonStyle = APButtonStyle.APTertiaryButtonStyle,
    val inputStyle: APTextInputStyle = APTextInputStyle(),
    val gatewayButtonStyle: APGatewayButtonStyle = APGatewayButtonStyle(),
    val gatewayTitleStyle: APGatewayTitleStyle = APGatewayTitleStyle(),
    val checkboxStyle: APCheckboxStyle = APCheckboxStyle(),
    val switchStyle: APSwitchStyle = APSwitchStyle(),
    val radioButtonStyle: APRadioButtonStyle = APRadioButtonStyle(),
    val dialogStyle: APDialogStyle = APDialogStyle(),
    val loaderStyle: APLoaderStyle = APLoaderStyle(),
    val bankGridStyle: APBankGridStyle = APBankGridStyle(),
    val paymentSummaryStyle: APPaymentSummaryStyle = APPaymentSummaryStyle(),
    val dccPaymentFormStyle: APDCCPaymentFormStyle = APDCCPaymentFormStyle(),
    val errorColor: APThemeColor = APThemeColor(APColors.errorLight, APColors.errorDark),
    val footerIconsColor: APThemeColor = APThemeColor(APColors.greyDarkLight, APColors.greyDarkDark),
) {
    /** Builder class to make easier creation [AutopayUIStyle] object in Java projects. */
    public class Builder(private var styleInstance: AutopayUIStyle = AutopayUIStyle()) {

        public fun build(): AutopayUIStyle = styleInstance

        public fun typography(typography: APTypography): Builder {
            styleInstance = styleInstance.copy(typography = typography)
            return this
        }

        public fun primaryButtonStyle(primaryButtonStyle: APButtonStyle): Builder {
            styleInstance = styleInstance.copy(primaryButtonStyle = primaryButtonStyle)
            return this
        }

        public fun secondaryButtonStyle(secondaryButtonStyle: APButtonStyle): Builder {
            styleInstance = styleInstance.copy(secondaryButtonStyle = secondaryButtonStyle)
            return this
        }

        public fun tertiaryButtonStyle(tertiaryButtonStyle: APButtonStyle): Builder {
            styleInstance = styleInstance.copy(tertiaryButtonStyle = tertiaryButtonStyle)
            return this
        }

        public fun inputStyle(inputStyle: APTextInputStyle): Builder {
            styleInstance = styleInstance.copy(inputStyle = inputStyle)
            return this
        }

        public fun gatewayButtonStyle(gatewayButtonStyle: APGatewayButtonStyle): Builder {
            styleInstance = styleInstance.copy(gatewayButtonStyle = gatewayButtonStyle)
            return this
        }

        public fun gatewayTitleStyle(gatewayTitleStyle: APGatewayTitleStyle): Builder {
            styleInstance = styleInstance.copy(gatewayTitleStyle = gatewayTitleStyle)
            return this
        }

        public fun checkboxStyle(checkboxStyle: APCheckboxStyle): Builder {
            styleInstance = styleInstance.copy(checkboxStyle = checkboxStyle)
            return this
        }

        public fun switchStyle(switchStyle: APSwitchStyle): Builder {
            styleInstance = styleInstance.copy(switchStyle = switchStyle)
            return this
        }

        public fun radioButtonStyle(radioButtonStyle: APRadioButtonStyle): Builder {
            styleInstance = styleInstance.copy(radioButtonStyle = radioButtonStyle)
            return this
        }

        public fun dialogStyle(dialogStyle: APDialogStyle): Builder {
            styleInstance = styleInstance.copy(dialogStyle = dialogStyle)
            return this
        }

        public fun loaderStyle(loaderStyle: APLoaderStyle): Builder {
            styleInstance = styleInstance.copy(loaderStyle = loaderStyle)
            return this
        }

        public fun bankGridStyle(bankGridStyle: APBankGridStyle): Builder {
            styleInstance = styleInstance.copy(bankGridStyle = bankGridStyle)
            return this
        }

        public fun paymentSummaryStyle(paymentSummaryStyle: APPaymentSummaryStyle): Builder {
            styleInstance = styleInstance.copy(paymentSummaryStyle = paymentSummaryStyle)
            return this
        }

        public fun dccPaymentFormStyle(dccPaymentFormStyle: APDCCPaymentFormStyle): Builder {
            styleInstance = styleInstance.copy(dccPaymentFormStyle = dccPaymentFormStyle)
            return this
        }

        public fun errorColor(errorColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(errorColor = errorColor)
            return this
        }

        public fun footerIconsColor(footerIconsColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(footerIconsColor = footerIconsColor)
            return this
        }
    }
}
```

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

```java
AutopayUIStyle currentStyle = Autopay.getCurrentUiStyle();
    Autopay.setUiStyle(
        new AutopayUIStyle.Builder(currentStyle)
            .inputStyle(
                new APTextInputStyle.Builder()
                    .labelTextStyle(
                        new APTextStyleWrapper.Builder()
                            .font(Typeface.DEFAULT_BOLD)
                            .size(20f)
                            .weight(800)
                            .getCompose()
                        )
                        .errorTextStyle(
                            new APTextStyleWrapper.Builder()
                                .size(18f)
                                .weight(300)
                                .font(Typeface.SANS_SERIF)
                                .letterSpacing(1f)
                                .lineHeight(1f)
                                .getCompose()
                        )
                        .labelTextColor(new APThemeColor(Color.BLUE, Color.GREEN))
                        .radius(4f)
                        .build()
            ).build()
    );
```

### APThemeColor

```kotlin
public data class APThemeColor(val lightColor: Color, val darkColor: Color? = null) {
    public constructor(
        lightColor: android.graphics.Color,
        darkColor: android.graphics.Color? = null
    ) : this(Color(lightColor.toArgb()), darkColor?.toArgb()?.let { Color(it) })

    public constructor(
        @ColorInt lightColor: Int,
        @ColorInt darkColor: Int? = null
    ) : this(Color(lightColor), darkColor?.let { Color(it) })
}
```

Reprezentuje wartość koloru w dwóch trybach - jasny ciemny

- `lightColor` - wartośc koloru dla trybu jasnego
- `darkColor` - wartośc koloru dla trybu ciemnego, opcjonalna, gdy jej brak => brana jest wartość `lightColor`

### APTextStyleWrapper

```kotlin
public class APTextStyleWrapper() {
    private val textStyle: TextStyle = TextStyle()

    /** @return TextStyle from given parameters in [APTextStyleWrapper]. */
    public fun getCompose(): TextStyle = textStyle

    /** Builder class to make easier creation [TextStyle] object in Java projects. */
    public class Builder(private var styleInstance: TextStyle = TextStyle()) {

        public fun getCompose(): TextStyle = styleInstance

        public fun size(size: Float): Builder {
            styleInstance = styleInstance.copy(fontSize = size.sp)
            return this
        }

        public fun weight(weight: Int): Builder {
            styleInstance = styleInstance.copy(fontWeight = FontWeight(weight))
            return this
        }

        public fun font(font: Typeface): Builder {
            styleInstance = styleInstance.copy(fontFamily = FontFamily(typeface = font))
            return this
        }

        public fun letterSpacing(letterSpacing: Float): Builder {
            styleInstance = styleInstance.copy(letterSpacing = letterSpacing.sp)
            return this
        }

        public fun lineHeight(lineHeight: Float): Builder {
            styleInstance = styleInstance.copy(lineHeight = lineHeight.sp)
            return this
        }
    }
}
```

Zestaw parametrów potrzebnych do utworzenia obiektu **androidx.compose.ui.text.TextStyle** w projektach pisanych w Javie.

### APTypography

```kotlin
public data class APTypography(
    val defaultTextColor: APThemeColor = APThemeColor(APColors.textLight, APColors.textDark),
    val labelSmall: TextStyle =
        TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.W400, fontSize = 12.sp),
    val labelSmallBold: TextStyle =
        TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.W500, fontSize = 12.sp),
    val labelMedium: TextStyle =
        TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.W400, fontSize = 14.sp),
    val labelLarge: TextStyle =
        TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.W400, fontSize = 16.sp),
    val labelXLarge: TextStyle =
        TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.W400, fontSize = 18.sp),
) {

    /** Builder class to make easier creation [APTypography] object in Java projects. */
    public class Builder(private var styleInstance: APTypography = APTypography()) {

        public fun build(): APTypography = styleInstance

        public fun defaultTextColor(defaultTextColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(defaultTextColor = defaultTextColor)
            return this
        }

        public fun labelSmall(labelSmall: TextStyle): Builder {
            styleInstance = styleInstance.copy(labelSmall = labelSmall)
            return this
        }

        public fun labelSmallBold(labelSmallBold: TextStyle): Builder {
            styleInstance = styleInstance.copy(labelSmallBold = labelSmallBold)
            return this
        }

        public fun labelMedium(labelMedium: TextStyle): Builder {
            styleInstance = styleInstance.copy(labelMedium = labelMedium)
            return this
        }

        public fun labelLarge(labelLarge: TextStyle): Builder {
            styleInstance = styleInstance.copy(labelLarge = labelLarge)
            return this
        }

        public fun labelXLarge(labelXLarge: TextStyle): Builder {
            styleInstance = styleInstance.copy(labelXLarge = labelXLarge)
            return this
        }
    }

    @Composable
    public fun labelSmall(): TextStyle = labelSmall.copy(color = defaultTextColor.color())

    @Composable
    public fun labelSmallBold(): TextStyle = labelSmallBold.copy(color = defaultTextColor.color())

    @Composable
    public fun labelMedium(): TextStyle = labelMedium.copy(color = defaultTextColor.color())

    @Composable
    public fun labelLarge(): TextStyle = labelLarge.copy(color = defaultTextColor.color())

    @Composable
    public fun labelXLarge(): TextStyle = labelXLarge.copy(color = defaultTextColor.color())
}
```

Zestaw styli tekstów wraz ze wspólnym kolorem domyślnym tekstów. System zakłada użycie biblioteki w 4 rozmiarach - 12, 14, 16, 18, o wadze 400. Jedynie czcionka o rozmiarze 12 ma swój pogrubiony odpowiednik o wadze 500. Każdy styl tekstu przekazywany jest w postaci parametrów **androidx.compose.ui.text.TextStyle**

- `defaultTextColor` - kolor tekstu dla wszystkich styli tekstów w tym obiekcie, nie dotyczy koloru tekstów na przyciskach
- `labelSmall` - mały styl tekstu, domyślnie wielkości 12.sp
- `labelSmallBold` - mały styl tekstu pogrubiony, domyślnie waga 500, wielkość 12.sp
- `labelMedium` - średni styl tekstu, domyślnie wielkości 14.sp
- `labelLarge`- wielki styl tekstu, domyślnie wielkości 16.sp
- `labelXLarge` - największy styl tekstu, domyślnie wielkości 18.sp

### APButtonStyle

```kotlin
public data class APButtonStyle(
    val containerColor: APThemeColor = APThemeColor(APColors.transparent, APColors.transparent),
    val inactiveContainerColor: APThemeColor = APThemeColor(
        APColors.transparent,
        APColors.transparent
    ),
    val contentColor: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val inactiveContentColor: APThemeColor = APThemeColor(
        APColors.primaryAlpha66Light,
        APColors.primaryAlpha66Dark
    ),
    val borderColor: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val inactiveBorderColor: APThemeColor = APThemeColor(
        APColors.primaryAlpha66Light,
        APColors.primaryAlpha66Dark
    ),
    val radius: Dp = 28.dp,
    val borderWidth: Dp = 1.dp,
    val textStyle: TextStyle = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.W400,
        fontSize = 18.sp
    ),
    val minHeight: Dp = 48.dp
) {

    /** Builder class to make easier creation [APButtonStyle] object in Java projects. */
    public class Builder(private var styleInstance: APButtonStyle = APButtonStyle()) {

        public fun build(): APButtonStyle = styleInstance

        public fun containerColor(containerColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(containerColor = containerColor)
            return this
        }

        public fun inactiveContainerColor(inactiveContainerColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(inactiveContainerColor = inactiveContainerColor)
            return this
        }

        public fun contentColor(contentColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(contentColor = contentColor)
            return this
        }

        public fun inactiveContentColor(inactiveContentColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(inactiveContentColor = inactiveContentColor)
            return this
        }

        public fun borderColor(borderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(borderColor = borderColor)
            return this
        }

        public fun inactiveBorderColor(inactiveBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(inactiveBorderColor = inactiveBorderColor)
            return this
        }

        public fun radius(radius: Dp): Builder {
            styleInstance = styleInstance.copy(radius = radius)
            return this
        }

        public fun borderWidth(borderWidth: Dp): Builder {
            styleInstance = styleInstance.copy(borderWidth = borderWidth)
            return this
        }

        public fun textStyle(textStyle: TextStyle): Builder {
            styleInstance = styleInstance.copy(textStyle = textStyle)
            return this
        }

        public fun minHeight(minHeight: Dp): Builder {
            styleInstance = styleInstance.copy(minHeight = minHeight)
            return this
        }

        public fun radius(radius: Float): Builder {
            styleInstance = styleInstance.copy(radius = radius.dp)
            return this
        }

        public fun borderWidth(borderWidth: Float): Builder {
            styleInstance = styleInstance.copy(borderWidth = borderWidth.dp)
            return this
        }

        public fun textStyle(textStyle: APTextStyleWrapper): Builder {
            styleInstance = styleInstance.copy(textStyle = textStyle.getCompose())
            return this
        }

        public fun minHeight(minHeight: Float): Builder {
            styleInstance = styleInstance.copy(minHeight = minHeight.dp)
            return this
        }
    }

        public companion object {
        public val APPrimaryButtonStyle: APButtonStyle =
            APButtonStyle(
                containerColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
                inactiveContainerColor =
                    APThemeColor(APColors.primaryAlpha66Light, APColors.primaryAlpha66Dark),
                contentColor = APThemeColor(APColors.onPrimaryLight, APColors.onPrimaryDark),
                inactiveContentColor =
                    APThemeColor(APColors.onPrimaryLight, APColors.onPrimaryDark),
                borderWidth = Dp.Unspecified,
            )
        public val APSecondaryButtonStyle: APButtonStyle = APButtonStyle()
        public val APTertiaryButtonStyle: APButtonStyle =
            APButtonStyle(textStyle = TextStyle(fontSize = 12.sp))
    }
}
```

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

```kotlin
public data class APTextInputStyle(
    val inputTextStyle: TextStyle = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.W400,
        fontSize = 16.sp
    ),
    val inputTextColor: APThemeColor = APThemeColor(APColors.textLight, APColors.textDark),
    val placeholderTextColor: APThemeColor = APThemeColor(
        APColors.greyDarkLight,
        APColors.greyDarkDark
    ),
    val labelTextStyle: TextStyle = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.W400,
        fontSize = 14.sp
    ),
    val labelTextColor: APThemeColor = APThemeColor(APColors.textLight, APColors.textDark),
    val errorTextStyle: TextStyle = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.W400,
        fontSize = 14.sp
    ),
    val errorTextColor: APThemeColor = APThemeColor(APColors.errorLight, APColors.errorDark),
    val borderInactiveColor: APThemeColor = APThemeColor(
        APColors.greyDarkAlpha66Light,
        APColors.greyDarkAlpha66Dark
    ),
    val borderActiveColor: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val borderErrorColor: APThemeColor = APThemeColor(APColors.errorLight, APColors.errorDark),
    val backgroundColor: APThemeColor = APThemeColor(
        APColors.backgroundLight,
        APColors.backgroundDark
    ),
    val trailingIconsColor: APThemeColor = APThemeColor(APColors.textLight, APColors.textDark),
    val radius: Dp = 28.dp,
    val strokeWidth: Dp = 1.dp,
    val spaceBetweenInputs: Dp = 16.dp
) {

    /** Builder class to make easier creation [APTextInputStyle] object in Java projects. */
    public class Builder(private var styleInstance: APTextInputStyle = APTextInputStyle()) {

        public fun build(): APTextInputStyle = styleInstance

        public fun inputTextStyle(inputTextStyle: TextStyle): Builder {
            styleInstance = styleInstance.copy(inputTextStyle = inputTextStyle)
            return this
        }

        public fun inputTextStyle(inputTextStyle: APTextStyleWrapper): Builder {
            styleInstance = styleInstance.copy(inputTextStyle = inputTextStyle.toCompose())
            return this
        }

        public fun inputTextColor(inputTextColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(inputTextColor = inputTextColor)
            return this
        }

        public fun placeholderTextColor(placeholderTextColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(placeholderTextColor = placeholderTextColor)
            return this
        }

        public fun labelTextStyle(labelTextStyle: TextStyle): Builder {
            styleInstance = styleInstance.copy(labelTextStyle = labelTextStyle)
            return this
        }

        public fun labelTextStyle(labelTextStyle: APTextStyleWrapper): Builder {
            styleInstance = styleInstance.copy(labelTextStyle = labelTextStyle.toCompose())
            return this
        }

        public fun labelTextColor(labelTextColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(labelTextColor = labelTextColor)
            return this
        }

        public fun errorTextStyle(errorTextStyle: TextStyle): Builder {
            styleInstance = styleInstance.copy(errorTextStyle = errorTextStyle)
            return this
        }

        public fun errorTextStyle(errorTextStyle: APTextStyleWrapper): Builder {
            styleInstance = styleInstance.copy(errorTextStyle = errorTextStyle.toCompose())
            return this
        }

        public fun errorTextColor(errorTextColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(errorTextColor = errorTextColor)
            return this
        }

        public fun borderInactiveColor(borderInactiveColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(borderInactiveColor = borderInactiveColor)
            return this
        }

        public fun borderActiveColor(borderActiveColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(borderActiveColor = borderActiveColor)
            return this
        }

        public fun borderErrorColor(borderErrorColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(borderErrorColor = borderErrorColor)
            return this
        }

        public fun backgroundColor(backgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(backgroundColor = backgroundColor)
            return this
        }

        public fun trailingIconsColor(trailingIconsColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(trailingIconsColor = trailingIconsColor)
            return this
        }

        public fun radius(radius: Dp): Builder {
            styleInstance = styleInstance.copy(radius = radius)
            return this
        }

        public fun strokeWidth(strokeWidth: Dp): Builder {
            styleInstance = styleInstance.copy(strokeWidth = strokeWidth)
            return this
        }

        public fun spaceBetweenInputs(spaceBetweenInputs: Dp): Builder {
            styleInstance = styleInstance.copy(spaceBetweenInputs = spaceBetweenInputs)
            return this
        }

        public fun radius(radius: Float): Builder {
            styleInstance = styleInstance.copy(radius = radius.dp)
            return this
        }

        public fun strokeWidth(strokeWidth: Float): Builder {
            styleInstance = styleInstance.copy(strokeWidth = strokeWidth.dp)
            return this
        }

        public fun spaceBetweenInputs(spaceBetweenInputs: Float): Builder {
            styleInstance = styleInstance.copy(spaceBetweenInputs = spaceBetweenInputs.dp)
            return this
        }
    }
}
```

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

```kotlin
public data class APGatewayButtonStyle(
    val backgroundColor: APThemeColor =
        APThemeColor(APColors.backgroundLight, APColors.backgroundDark),
    val borderColor: APThemeColor =
        APThemeColor(APColors.greyDarkAlpha66Light, APColors.greyDarkAlpha66Dark),
    val iconColor: APThemeColor = APThemeColor(APColors.iconLight, APColors.iconDark),
    val textColor: APThemeColor = APThemeColor(APColors.textLight, APColors.textDark),
    val textStyle: TextStyle =
        TextStyle(fontFamily = FontFamily.Default, fontWeight = FontWeight.W400, fontSize = 16.sp),
    val borderWidth: Dp = 1.dp,
    val radius: Dp = 122.dp,
    val minHeight: Dp = 48.dp,
) {

    /** Builder class to make easier creation [APGatewayButtonStyle] object in Java projects. */
    public class Builder(private var styleInstance: APGatewayButtonStyle = APGatewayButtonStyle()) {

        public fun build(): APGatewayButtonStyle = styleInstance

        public fun backgroundColor(backgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(backgroundColor = backgroundColor)
            return this
        }

        public fun borderColor(borderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(borderColor = borderColor)
            return this
        }

        public fun iconColor(iconColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(iconColor = iconColor)
            return this
        }

        public fun textColor(textColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(textColor = textColor)
            return this
        }

        public fun textStyle(textStyle: TextStyle): Builder {
            styleInstance = styleInstance.copy(textStyle = textStyle)
            return this
        }

        public fun borderWidth(borderWidth: Dp): Builder {
            styleInstance = styleInstance.copy(borderWidth = borderWidth)
            return this
        }

        public fun radius(radius: Dp): Builder {
            styleInstance = styleInstance.copy(radius = radius)
            return this
        }

        public fun borderWidth(borderWidth: Float): Builder {
            styleInstance = styleInstance.copy(borderWidth = borderWidth.dp)
            return this
        }

        public fun radius(radius: Float): Builder {
            styleInstance = styleInstance.copy(radius = radius.dp)
            return this
        }

        public fun minHeight(minHeight: Dp): Builder {
            styleInstance = styleInstance.copy(minHeight = minHeight)
            return this
        }

        public fun minHeight(minHeight: Float): Builder {
            styleInstance = styleInstance.copy(minHeight = minHeight.dp)
            return this
        }
    }
}
```

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

```kotlin
public data class APGatewayTitleStyle(
    val backgroundColor: APThemeColor = APThemeColor(
        APColors.backgroundLight,
        APColors.backgroundDark
    ),
    val textStyle: TextStyle = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.W400,
        fontSize = 16.sp
    ),
    val textColor: APThemeColor = APThemeColor(APColors.textLight, APColors.textDark),
    val iconColor: APThemeColor = APThemeColor(APColors.iconLight, APColors.iconDark),
    val radius: Dp = 16.dp
) {

    /** Builder class to make easier creation [APGatewayTitleStyle] object in Java projects. */
    public class Builder(private var styleInstance: APGatewayTitleStyle = APGatewayTitleStyle()) {

        public fun build(): APGatewayTitleStyle = styleInstance

        public fun backgroundColor(backgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(backgroundColor = backgroundColor)
            return this
        }

        public fun textStyle(textStyle: TextStyle): Builder {
            styleInstance = styleInstance.copy(textStyle = textStyle)
            return this
        }

        public fun textStyle(textStyle: APTextStyleWrapper): Builder {
            styleInstance = styleInstance.copy(textStyle = textStyle.toCompose())
            return this
        }

        public fun textColor(textColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(textColor = textColor)
            return this
        }

        public fun iconColor(iconColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(iconColor = iconColor)
            return this
        }

        public fun radius(radius: Dp): Builder {
            styleInstance = styleInstance.copy(radius = radius)
            return this
        }

        public fun radius(radius: Float): Builder {
            styleInstance = styleInstance.copy(radius = radius.dp)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących tytuł kanału płatności po wybraniu danej formy i rozwinięciu jej szczegółów

- `backgroundColor` - kolor tła
- `iconColor` - kolor ikon na przycisku - tylko w przypadku Karty płatniczej oraz Przelewów bankowych, pozostałe przyciski mają ikony wielokolorowe odpowiadające ich markom
- `textColor` - kolor tekstu na przycisku
- `textStyle` - styl tekstu na przycisku
- `radius` - promień załamania tła

### APCheckboxStyle

```kotlin
public data class APCheckboxStyle(
    val checkedColor: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val uncheckedColor: APThemeColor = APThemeColor(APColors.greyDarkLight, APColors.greyDarkDark),
    val errorColor: APThemeColor = APThemeColor(APColors.errorLight, APColors.errorDark),
) {
    /** Builder class to make easier creation [APCheckboxStyle] object in Java projects. */
    public class Builder(private var styleInstance: APCheckboxStyle = APCheckboxStyle()) {

        public fun build(): APCheckboxStyle = styleInstance

        public fun checkedColor(checkedColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(checkedColor = checkedColor)
            return this
        }

        public fun uncheckedColor(uncheckedColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(uncheckedColor = uncheckedColor)
            return this
        }

        public fun errorColor(errorColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(errorColor = errorColor)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących widoki typu checkbox

- `checkedColor` - kolor wypełnienia zaznaczonego checkboxa
- `uncheckedColor` - kolor obramowania w stanie domyślnym niezaznaczonym
- `errorColor` - kolor obramowania w przypadku błędu spowodowanego niezaznaczeniem checkboxa

### APSwitchStyle

```kotlin
public data class APSwitchStyle(
    val checkedThumbColor: APThemeColor = APThemeColor(
        APColors.onPrimaryLight,
        APColors.onPrimaryDark
    ),
    val uncheckedThumbColor: APThemeColor = APThemeColor(
        APColors.greyDarkLight,
        APColors.greyDarkDark
    ),
    val checkedTrackColor: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val uncheckedTrackColor: APThemeColor = APThemeColor(
        APColors.backgroundLight,
        APColors.backgroundDark
    ),
    val checkedBorderColor: APThemeColor = APThemeColor(
        APColors.primaryLight,
        APColors.primaryDark
    ),
    val uncheckedBorderColor: APThemeColor = APThemeColor(
        APColors.greyDarkLight,
        APColors.greyDarkDark
    ),
) {
    /** Builder class to make easier creation [APSwitchStyle] object in Java projects. */
    public class Builder(private var styleInstance: APSwitchStyle = APSwitchStyle()) {

        public fun build(): APSwitchStyle = styleInstance

        public fun checkedThumbColor(checkedThumbColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(checkedThumbColor = checkedThumbColor)
            return this
        }

        public fun uncheckedThumbColor(uncheckedThumbColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(uncheckedThumbColor = uncheckedThumbColor)
            return this
        }

        public fun checkedTrackColor(checkedTrackColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(checkedTrackColor = checkedTrackColor)
            return this
        }

        public fun uncheckedTrackColor(uncheckedTrackColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(uncheckedTrackColor = uncheckedTrackColor)
            return this
        }

        public fun checkedBorderColor(checkedBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(checkedBorderColor = checkedBorderColor)
            return this
        }

        public fun uncheckedBorderColor(uncheckedBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(uncheckedBorderColor = uncheckedBorderColor)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących widoki typu switch

- `checkedThumbColor` - kolor przełącznika w stanie zaznaczonym
- `uncheckedThumbColor` - kolor przełącznika w stanie niezaznaczonym
- `checkedTrackColor` - kolor tła w stanie zaznaczonym
- `uncheckedTrackColor` - kolor tła w stanie niezaznaczonym
- `checkedBorderColor` - kolor obramowania w stanie zaznaczonym
- `uncheckedBorderColor` - kolor obramowania w stanie niezaznaczonym

### APRadioButtonStyle

```kotlin
public data class APRadioButtonStyle(
    val checkedColor: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val uncheckedColor: APThemeColor = APThemeColor(APColors.greyDarkLight, APColors.greyDarkDark),
) {
    /** Builder class to make easier creation [APRadioButtonStyle] object in Java projects. */
    public class Builder(private var styleInstance: APRadioButtonStyle = APRadioButtonStyle()) {

        public fun build(): APRadioButtonStyle = styleInstance

        public fun checkedColor(checkedColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(checkedColor = checkedColor)
            return this
        }

        public fun uncheckedColor(uncheckedColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(uncheckedColor = uncheckedColor)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących widoki typu radio button

- `checkedColor` - kolor w stanie zaznaczonym
- `uncheckedColor` - kolor w stanie odznaczonym

### APDialogStyle

```kotlin
public data class APDialogStyle(
    val dialogRadius: Dp = 16.dp,
    val dialogBackgroundColor: APThemeColor = APThemeColor(
        APColors.greyLightLight,
        APColors.greyLightDark
    )
) {

    /** Builder class to make easier creation [APDialogStyle] object in Java projects. */
    public class Builder(private var styleInstance: APDialogStyle = APDialogStyle()) {

        public fun build(): APDialogStyle = styleInstance

        public fun dialogRadius(dialogRadius: Dp): Builder {
            styleInstance = styleInstance.copy(dialogRadius = dialogRadius)
            return this
        }

        public fun dialogRadius(dialogRadius: Float): Builder {
            styleInstance = styleInstance.copy(dialogRadius = dialogRadius.dp)
            return this
        }

        public fun dialogBackgroundColor(dialogBackgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(dialogBackgroundColor = dialogBackgroundColor)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących wyświetlane okna w SDK

- `dialogRadius` - zaokrąglenie okna
- `dialogBackgroundColor` - kolor tła okna

### APLoaderStyle

```kotlin
public data class APLoaderStyle(
    val color: APThemeColor = APThemeColor(APColors.primaryLight, APColors.primaryDark),
    val size: Dp = 32.dp
) {

    /** Builder class to make easier creation [APLoaderStyle] object in Java projects. */
    public class Builder(private var styleInstance: APLoaderStyle = APLoaderStyle()) {

        public fun build(): APLoaderStyle = styleInstance

        public fun color(color: APThemeColor): Builder {
            styleInstance = styleInstance.copy(color = color)
            return this
        }

        public fun size(size: Dp): Builder {
            styleInstance = styleInstance.copy(size = size)
            return this
        }

        public fun size(size: Float): Builder {
            styleInstance = styleInstance.copy(size = size.dp)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących widoki ładowania danych

- `color` - kolor loadera
- `size` - rozmiar loadera

### APBankGridStyle

```kotlin
public data class APBankGridStyle(
    val columns: Int = 3,
    val cellHeight: Dp = 80.dp,
    val radius: Dp = 12.dp,
    val backgroundColor: APThemeColor = APThemeColor(
        APColors.backgroundLight,
        APColors.backgroundDark
    ),
    val checkedBorderColor: APThemeColor = APThemeColor(
        APColors.primaryLight,
        APColors.primaryDark
    ),
    val uncheckedBorderColor: APThemeColor = APThemeColor(
        APColors.greyDarkAlpha66Light,
        APColors.greyDarkAlpha66Dark
    ),
) {

    /** Builder class to make easier creation [APBankGridStyle] object in Java projects. */
    public class Builder(private var styleInstance: APBankGridStyle = APBankGridStyle()) {

        public fun build(): APBankGridStyle = styleInstance

        public fun columns(columns: Int): Builder {
            styleInstance = styleInstance.copy(columns = columns)
            return this
        }

        public fun cellHeight(cellHeight: Dp): Builder {
            styleInstance = styleInstance.copy(cellHeight = cellHeight)
            return this
        }

        public fun radius(radius: Dp): Builder {
            styleInstance = styleInstance.copy(radius = radius)
            return this
        }

        public fun cellHeight(cellHeight: Float): Builder {
            styleInstance = styleInstance.copy(cellHeight = cellHeight.dp)
            return this
        }

        public fun radius(radius: Float): Builder {
            styleInstance = styleInstance.copy(radius = radius.dp)
            return this
        }

        public fun backgroundColor(backgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(backgroundColor = backgroundColor)
            return this
        }

        public fun checkedBorderColor(checkedBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(checkedBorderColor = checkedBorderColor)
            return this
        }

        public fun uncheckedBorderColor(uncheckedBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(uncheckedBorderColor = uncheckedBorderColor)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących siatkę banków na grupie "Przelewy bankowe"

- `columns` - liczba kolumn w siatce banków
- `cellHeight` - wysokość komórki z ikoną banku
- `radius` - promień załamania obramowania komórki
- `backgroundColor` - kolor wypełnienia komórki wewnątrz obramowania
- `checkedBorderColor` - kolor obramowania zaznaczonego banku
- `uncheckedBorderColor` - kolor obramowania banku gdy nie jest zaznaczony

### APPaymentSummaryStyle

```kotlin
public data class APPaymentSummaryStyle(
    val backgroundColor: APThemeColor = APThemeColor(
        APColors.backgroundLight,
        APColors.backgroundDark
    ),
    val borderColor: APThemeColor = APThemeColor(
        APColors.greyDarkAlpha66Light,
        APColors.greyDarkAlpha66Dark
    ),
    val borderWidth: Dp = 1.dp,
    val dividerColor: APThemeColor = APThemeColor(
        APColors.greyDarkAlpha66Light,
        APColors.greyDarkAlpha66Dark
    ),
    val dividerHeight: Dp = 1.dp,
    val radius: Dp = 16.dp
) {

    /** Builder class to make easier creation [APPaymentSummaryStyle] object in Java projects. */
    public class Builder(private var styleInstance: APPaymentSummaryStyle = APPaymentSummaryStyle()) {

        public fun build(): APPaymentSummaryStyle = styleInstance

        public fun backgroundColor(backgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(backgroundColor = backgroundColor)
            return this
        }

        public fun borderColor(borderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(borderColor = borderColor)
            return this
        }

        public fun borderWidth(borderWidth: Dp): Builder {
            styleInstance = styleInstance.copy(borderWidth = borderWidth)
            return this
        }

        public fun borderWidth(borderWidth: Float): Builder {
            styleInstance = styleInstance.copy(borderWidth = borderWidth.dp)
            return this
        }

        public fun dividerColor(dividerColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(dividerColor = dividerColor)
            return this
        }

        public fun dividerHeight(dividerHeight: Dp): Builder {
            styleInstance = styleInstance.copy(dividerHeight = dividerHeight)
            return this
        }

        public fun radius(radius: Dp): Builder {
            styleInstance = styleInstance.copy(radius = radius)
            return this
        }

        public fun dividerHeight(dividerHeight: Float): Builder {
            styleInstance = styleInstance.copy(dividerHeight = dividerHeight.dp)
            return this
        }

        public fun radius(radius: Float): Builder {
            styleInstance = styleInstance.copy(radius = radius.dp)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących etykietę z podsumowaniem płatności

- `backgroundColor` - kolor tła etykiety
- `borderColor` - kolor obramowania etykiety
- `borderWidth` - grubość obramowania etykiety
- `dividerColor` - kolor separatora w etykiecie
- `dividerHeight` - grubość separatora w etykiecie
- `radius` - promień załamania obramowania etykiety

### APDCCPaymentFormStyle

```kotlin
public data class APDCCPaymentFormStyle(
    val selectedBorderColor: APThemeColor = APThemeColor(
        APColors.primaryLight,
        APColors.primaryDark
    ),
    val unselectedBorderColor: APThemeColor = APThemeColor(
        APColors.greyLightLight,
        APColors.greyLightDark
    ),
    val cellRadius: Dp = 16.dp,
    val cellBackgroundColor: APThemeColor = APThemeColor(
        APColors.backgroundLight,
        APColors.backgroundDark
    )
) {

    /** Builder class to make easier creation [APDCCPaymentFormStyle] object in Java projects. */
    public class Builder(private var styleInstance: APDCCPaymentFormStyle = APDCCPaymentFormStyle()) {

        public fun build(): APDCCPaymentFormStyle = styleInstance

        public fun selectedBorderColor(selectedBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(selectedBorderColor = selectedBorderColor)
            return this
        }

        public fun unselectedBorderColor(unselectedBorderColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(unselectedBorderColor = unselectedBorderColor)
            return this
        }

        public fun cellRadius(cellRadius: Dp): Builder {
            styleInstance = styleInstance.copy(cellRadius = cellRadius)
            return this
        }

        public fun cellRadius(cellRadius: Float): Builder {
            styleInstance = styleInstance.copy(cellRadius = cellRadius.dp)
            return this
        }

        public fun cellBackgroundColor(cellBackgroundColor: APThemeColor): Builder {
            styleInstance = styleInstance.copy(cellBackgroundColor = cellBackgroundColor)
            return this
        }
    }
}
```

Zestaw parametrów stylizujących okno z formularzem przewalutowania przy płatności kartą

- `selectedBorderColor` - kolor obramowania etykiety zaznaczonej waluty
- `unselectedBorderColor` - kolor obramowania etykiety niezaznaczonej waluty
- `cellRadius` - promień załamania obramowania etykiety z walutą
- `cellBackgroundColor` - kolor wypełnienia etykiety z walutą wewnątrz obramowania

### APCustomerFee

```kotlin
public data class APCustomerFee(val customerFee: BigDecimal, val receiverName: String)
```

- `customerFee` Kwota opłata konsumenckiej.
- `receiverName` Odbiorca opłaty konsumenckiej.

### APEnvironmentType

```kotlin
public sealed class APEnvironmentType {
   public data object DEV : APEnvironmentType()

   public data object PROD : APEnvironmentType()
}
```

Klasa definiująca środowisko, z którym chcemy się komunikować.

### APError

```kotlin
public class APError(public val type: APErrorType, message: String, public val orderId: String? = null) : Throwable(message)
```

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

```kotlin
data class APGateway(
   val gatewayId: Long,
   val gatewayName: String,
   val gatewayType: APGatewayType,
   val bankName: String,
   val iconURL: String,
   val currencyList: List<String>,
) {
   val group: APGatewayPaymentGroup? =
       when (gatewayType) {
           APGatewayType.BLIK -> APGatewayPaymentGroup.BLIK
           APGatewayType.PBL -> APGatewayPaymentGroup.BANK_TRANSFER
           APGatewayType.FAST_TRANSFER -> APGatewayPaymentGroup.BANK_TRANSFER
           APGatewayType.CARD -> APGatewayPaymentGroup.CARD
           APGatewayType.AUTO_PAYMENT_CARD -> APGatewayPaymentGroup.CARD
           APGatewayType.GOOGLE_PAY -> APGatewayPaymentGroup.GOOGLE_PAY
           APGatewayType.VISA_MOBILE -> APGatewayPaymentGroup.VISA
           else -> null
       }
}
```

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

```kotlin
public data class APPreTransaction(
   val orderId: String,
   val remoteId: String,
   val hash: String,
   val serviceId: String,
   val messageId: String,
   val status: APResult,
   val redirectUrl: String?,
   val reason: String,
   val confirmation: APConfirmation?,
)
```

Klasa reprezentująca dane rozpoczętej transakcji.

- `orderId` Identyfikator transakcji.
- `serviceId` Identyfikator serwisu obsługującego transakcję.
- `status` Status transakcji.
- `redirectUrl` Url strony do przekierowania w celu dokończenia transakcji. Może być pusty, wtedy transakcja jest w trakcie realizacji i można sprawdzić jej status.
- `reason` Powód nie dokonania transakcji, jeśli istnieje. Jeśli puste, transakcja się rozpoczęła. Najlepszym sposobem jest próba zmapowania wartości reason na enum `APErrorType`.
- `confirmation` Status potwierdzenia przyjęcia zlecenia.

### APConfirmation

```kotlin
public enum class APConfirmation {
    CONFIRMED,
    NOTCONFIRMED,
}
```

Status potwierdzenia przyjęcia zlecenia.
- `CONFIRMED` Operacja powiodła się. **Uwaga!** Nie oznacza to wykonania obciążenia!
- `NOTCONFIRMED` Operacja nie powiodła się.

### APProduct

```kotlin
public data class APProduct(val subAmount: BigDecimal, val params: Map<String, String>)
```

Informacje o produktach które możemy dodać jako parametry transakcji.
- `subAmount` Kwota produktu.
- `params` Dodatkowe parametry w postaci klucz-wartość.

### APRegulation

```kotlin
public data class APRegulation(
   val regulationId: Int,
   val type: String,
   val url: String,
   val labelList: List<Label>,
) {


   public data class Label(
       val labelId: Int,
       val inputLabel: String,
       val placement: Placement,
       val showCheckbox: Boolean,
       val checkboxRequired: Boolean,
   )


   public enum class Placement {
       TOP,
       MIDDLE,
       BOTTOM,
   }
}
```

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

```kotlin
public sealed class (public val groups: List<APGatewayPaymentGroup>) {
   public data object APGatewaysLoading : APSdkState(emptyList())

    public data class APGatewaysList(val data: List<APGateway>) :
        APSdkState(data.mapNotNull { it.group }.distinct())

    public data class APGatewayDetails(val gatewayGroup: APGatewayPaymentGroup) :
        APSdkState(listOf(gatewayGroup))

    public data class APPreTransactionInProgress(val gatewayGroup: APGatewayPaymentGroup) :
        APSdkState(listOf(gatewayGroup))
}
```

Klasa reprezentująca obecny stan widoku *APGatewayListCompose*/*APGatewayListView*. 

- *APGatewaysLoading* Definiuje stan ładowania listy kanałów płatności.
- *APGatewaysList* Definiuje stan wyświetlania kanałów płatności.
- *APGatewayDetails* Definiuje stan wyświetlania szczegółów grupy kanałów płatności. Grupa zdefiniowana jest parametrem *gatewayGroup*.
- *APPreTransactionInProgress* Definiuje stan rozpoczęcia transakcji i oczekiwania na jej wykonanie w obrębie wskazanej grupy kanałów płatności parametrem *gatewayGroup*.

### APTransactionData

```kotlin
public data class APTransactionData(
   val amount: BigDecimal,
   val orderId: String = NonceGenerator.nonce,
   val gatewayId: Long = 0L,
   val language: String = Locale.getDefault().language.uppercase(),
   val authorizationCode: String? = null,
   val email: String? = null,
   val phone: String? = null,
   val googlePaymentToken: String? = null,
   val products: List<APProduct> = listOf(),
   val params: Map<String, String> = mapOf(),
)

/** Builder class to make easier creation [APTransactionData] object. */
public class Builder internal constructor(private val amount: BigDecimal) {
    private var orderId: String = NonceGenerator.nonce
    private var gatewayId: Long = 0L
    private var language: String = Locale.getDefault().language.uppercase()
    private var authorizationCode: String? = null
    private var email: String? = null
    private var phone: String? = null
    private var googlePaymentToken: String? = null
    private var products: List<APProduct> = listOf()
    private var params: Map<String, String> = mapOf()

    public fun orderId(orderId: String): Builder = apply { this.orderId = orderId }

    public fun gatewayId(gatewayId: Long): Builder = apply { this.gatewayId = gatewayId }

    public fun language(language: String): Builder = apply { this.language = language }

    public fun authorizationCode(authorizationCode: String?): Builder = apply {
        this.authorizationCode = authorizationCode
    }

    public fun email(email: String?): Builder = apply { this.email = email }

    public fun phone(phone: String?): Builder = apply { this.phone = phone }

    public fun googlePaymentToken(token: String?): Builder = apply {
        this.googlePaymentToken = token
    }

    public fun products(products: List<APProduct>): Builder = apply { this.products = products }

    public fun params(params: Map<String, String>): Builder = apply { this.params = params }

    public fun build(): APTransactionData =
        APTransactionData(
            amount = amount,
            orderId = orderId,
            gatewayId = gatewayId,
            language = language,
            authorizationCode = authorizationCode,
            email = email,
            phone = phone,
            googlePaymentToken = googlePaymentToken,
            products = products,
            params = params,
        )
    }
```

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

```kotlin
public data class APTransactionStatus(
   val orderId: String,
   val remoteId: String,
   val hash: String,
   val serviceId: String,
   val messageId: String,
   val transactions: List<Transaction>,
) {

   public data class Transaction(
       val orderId: String,
       val remoteId: String,
       val amount: String,
       val currency: String,
       val gatewayId: String,
       val paymentDate: String,
       val paymentStatus: APResult?,
       val paymentStatusDetails: String,
   )
}
```

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

```kotlin
@Composable
public fun APGatewayListCompose(
   amount: BigDecimal,
   paymentSummary: String? = null,
   visibleGateways: List<APGatewayPaymentGroup> = APGatewayPaymentGroup.entries.toList(),
   customerEmail: String? = null,
   customerPhone: String? = null,
   orderId: String? = null,
   onPaymentStateChange: (APSdkState) -> Unit = {},
   onPreTransactionDone: (APPreTransaction) -> Unit = {},
   onPreTransactionError: (Throwable) -> Unit = {},
   finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
)
```

```kotlin
public class APGatewayListView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var amount: BigDecimal
    public var visibleGateways: List<APGatewayPaymentGroup>
    public var paymentSummary: String?
    public var customerEmail: String? = null
    public var customerPhone: String? = null
    public var orderId: String? = null
    public var onPaymentStateChange: (APSdkState) -> Unit = {}
    public var onPreTransactionDone: (APPreTransaction) -> Unit = {}
    public var onPreTransactionError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
}
```

```xml
<eu.autopay.pay.sdk.ui.list.APGatewayListView
   android:id="@+id/gatewayList"
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

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

```kotlin
public fun loadUrl(
   url: String,
   transactionCallback: (APResult?) -> Unit,
   eventCallback: (APEvent?) -> Unit,
   errorCallback: (APError?) -> Unit,
)
```

Metoda do wczytywania strony przekierowania transakcji

- `url` Adres strony www do załadowania.
- `transactionCallback` Callback informujący o statusie transakcji.
- `eventCallback` Callback informujący o zmianach na wyświetlanej stronie.
- `errorCallback` Callback informujący o występujących błędach w trakcie obsługi transakcji.

### APCardActivationCompose / APCardActivationView

```kotlin
public APCardActivationCompose(
    onActivationDone: (APPreTransaction) -> Unit,
    onActivationError: (Throwable) -> Unit = {},
    finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null,
    orderId: String? = null,
    activationTextColor: APThemeColor = APThemeColor(Color(0xFF282828), Color(0xFFFAFAFA)),
    activationTextSize: TextUnit = 12.sp
)
```

```java
@Composable
public class APCardGatewayView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var onActivationDone: (APPreTransaction) -> Unit = {}
    public var onActivationError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
    public var orderId: String? = null
    public var activationTextColor: APThemeColor = APThemeColor(Color.parseColor("282828"), Color.parseColor("FAFAFA"))
    public var activationTextSize: Float = 12f
}
```

```xml
<eu.autopay.pay.sdk.ui.card.APCardActivationView
   android:id="@+id/cardPaywall"
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

Widok przedstawiający formularz aktywacji karty za pomocą serwisu Autopay. Naliczana w nim jest opłata konsumencka, która będzie zwrócona klientowi.

- `onActivationDone` Callback informujący o statusie aktywacji karty.
- `onActivationError` Callback informujący o występujących błędach w trakcie aktywacji karty oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onActivationDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `activationTextColor` Parametr zmiany koloru tekstu z informacją o opłacie za aktywacje karty.
- `activationTextSize` Parametr zmiany wielkości tekstu z informacją o opłacie za aktywacje karty.

### APBankGatewayCompose / APBankGatewayView

```kotlin
@Composable
public fun APBankGatewayCompose(
   amount: BigDecimal,
   customerEmail: String? = null,
   customerPhone: String? = null,
   orderId: String? = null,
   @StringRes contentHeader: Int? = null,
   onPreTransactionDone: (APPreTransaction) -> Unit = {},
   onPreTransactionError: (Throwable) -> Unit = {},
   finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null,
)
```

```kotlin
public class APBankGatewayView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var amount: BigDecimal = BigDecimal(0)
    public var customerEmail: String? = null
    public var customerPhone: String? = null
    public var orderId: String? = null
    public var contentHeader: Int? = null
    public var onPreTransactionDone: (APPreTransaction) -> Unit = {}
    public var onPreTransactionError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
}
```

```xml
<eu.autopay.pay.sdk.ui.views.bank.APBankGatewayView
   android:id="@+id/bankGateway"
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

Widok rozwiniętej grupy kanałów płatności typu *BANK*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APBlikGatewayCompose / APBlikGatewayView

```kotlin
@Composable
public fun APBlikGatewayCompose(
   amount: BigDecimal,
   customerEmail: String? = null,
   customerPhone: String? = null,
   orderId: String? = null,
   @StringRes contentHeader: Int? = null,
   onPreTransactionDone: (APPreTransaction) -> Unit = {},
   onPreTransactionError: (Throwable) -> Unit = {},
   finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null,
)
```

```kotlin
public class APBlikGatewayView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var amount: BigDecimal = BigDecimal(0)
    public var customerEmail: String? = null
    public var customerPhone: String? = null
    public var orderId: String? = null
    public var contentHeader: Int? = null
    public var onPreTransactionDone: (APPreTransaction) -> Unit = {}
    public var onPreTransactionError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
}
```

```xml
<eu.autopay.pay.sdk.ui.views.bank.APBankGatewayView
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

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

```kotlin
@Composable
public fun APCardGatewayCompose(
   amount: BigDecimal,
   customerEmail: String? = null,
   customerPhone: String? = null,
   orderId: String? = null,
   onPreTransactionDone: (APPreTransaction) -> Unit = {},
   onPreTransactionError: (Throwable) -> Unit = {},
   finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null,
)
```

```kotlin
public class APCardGatewayView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var amount: BigDecimal
    public var customerEmail: String? = null
    public var customerPhone: String? = null
    public var orderId: String? = null
    public var onPreTransactionDone: (APPreTransaction) -> Unit = {}
    public var onPreTransactionError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
}
```

```xml
<eu.autopay.pay.sdk.ui.views.card.APCardGatewayView
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

Widok rozwiniętego kanału płatności typu *CARD*. Nie zawiera podsumowania płatności. Obsługuje zarówno płatność kartą, płatność kartą automatyczną oraz oba kanały.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APGooglePayGatewayCompose / APGooglePayGatewayView

```kotlin
@Composable
fun APGooglePayGatewayCompose(
   amount: BigDecimal,
   customerEmail: String? = null,
   customerPhone: String? = null,
   orderId: String? = null,
   onPreTransactionDone: (APPreTransaction) -> Unit = {},
   onPreTransactionError: (Throwable) -> Unit = {},
   finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null,
)
```

```kotlin
public class APGooglePayGatewayView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var amount: BigDecimal
    public var customerEmail: String? = null
    public var customerPhone: String? = null
    public var orderId: String? = null
    public var onPreTransactionDone: (APPreTransaction) -> Unit = {}
    public var onPreTransactionError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
}
```

```xml
<eu.autopay.pay.sdk.ui.views.google.APGooglePayGatewayView
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

Widok rozwiniętego kanału płatności typu *GOOGLE_PAY*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)

### APVisaGatewayCompose / APVisaGatewayView

```kotlin
@Composable
public fun APVisaGatewayCompose(
   amount: BigDecimal,
   customerEmail: String? = null,
   customerPhone: String? = null,
   orderId: String? = null,
   onPreTransactionDone: (APPreTransaction) -> Unit = {},
   onPreTransactionError: (Throwable) -> Unit = {},
   finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null,
)
```

```kotlin
public class APVisaGatewayView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0) {
    public var amount: BigDecimal
    public var customerEmail: String? = null
    public var customerPhone: String? = null
    public var orderId: String? = null;
    public var onPreTransactionDone: (APPreTransaction) -> Unit = {}
    public var onPreTransactionError: (Throwable) -> Unit = {}
    public var finishBeforePreTransaction: ((APTransactionData) -> Unit)? = null
}
```

```xml
<eu.autopay.pay.sdk.ui.views.visa.APVisaGatewayView
   android:layout_width="match_parent"
   android:layout_height="wrap_content" />
```

Widok rozwiniętego kanału płatności typu *VISA*. Nie zawiera podsumowania płatności.

- `amount` Kwota transakcji
- `customerEmail` opcjonalne pole do podania e-maila użytkownika, przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `customerPhone` opcjonalne pole do podania numeru telefonu użytkownika przekazywanego dalej do transakcji. Zaleca się podanie jeśli posiadamy jego wartość.
- `orderId` - opcjonalny identyfikator transakcji, musi mieć 32 znaki z zakresu "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz", jeśli nie spełni tego kryterium, będzie wygenerowany automatycznie
- `onPreTransactionDone` Callback informujący o statusie transakcji.
- `onPreTransactionError` Callback informujący o występujących błędach w obsłudze transakcji oraz o wygaśnięciu tokenu. Wygaśnięcie tokenu należy obsłużyć w aplikacji implementującej SDK - zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w SDK metodą `Autopay.updateToken()` i odblokować interfejs.
- `finishBeforePreTransaction` - callback opcjonalny, jeśli nie jest nullem, wtedy `onPreTransactionDone` jest ignorowane, zwraca zestaw danych potrzebnych do samodzielnego rozpoczęcia transakcji z wykorzystaniem swojego backendu (Wariant I)
