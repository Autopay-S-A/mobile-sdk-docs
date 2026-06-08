---
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
  tags:
    visible: true
---

# IOS

## Przygotowanie projektu - wymagana konfiguracja

SDK dystrybuowane jest w formie xcframework za pomocą SPM lub cocoa pods. Wspiera projekty napisane w Swift oraz Objective-C, dla systemu iOS 15.0 lub wyższego. SDK jest kompatybilne z projektami budowanymi w Xcode wersji 16 lub wyższej.

### Zanim zaczniesz

Instrukcja dodania SDK do aplikacji iOS przez **Swift Package Manager** oraz **CocoaPods**, z uwzględnieniem projektów **Objective‑C** i **dwóch wariantów** biblioteki:

* `AutopaySdk` – wariant pełny (z OCR)
* `AutopaySdkWithoutOCR` – wariant bez OCR

**Repozytorium:** `https://github.com/Autopay-S-A/autopay-sdk-pay-ios`

{% tabs %}
{% tab title="Swift Package Manager (SPM)" %}
#### 1.1 Instalacja przez Swift Package Manager (SPM)

**1.1.1 Dodanie przez UI Xcode**

1. Otwórz projekt → **File** → **Add Package Dependencies…**
2. Wklej URL repozytorium: `https://github.com/Autopay-S-A/autopay-sdk-pay-ios`
3. Wybierz regułę wersji (np. **Exact** `4.0.3` lub **Up to Next…** od tej wersji).
4. Z listy produktów zaznacz **jeden**:
   * `AutopaySdk`
   * `AutopaySdkWithoutOCR`
5. Zatwierdź.

**1.1.2 Dodanie przez Packages.swift**

W pliku **Package.swift** dodaj nowe dependencies

```swift
dependencies: [
    .package(url: "https://github.com/Autopay-S-A/autopay-sdk-pay-ios", .upToNextMajor(from: "4.0.3"))
]
```
{% endtab %}

{% tab title="CocoaPods" %}
#### 1.2 Instalacja przez CocoaPods

Wybierz **jeden** wariant SDK (lub użyj różnych w odrębnych targetach):

```ruby
platform :ios, '15.0'
use_frameworks!

target 'YourAppTarget' do
  # Wariant pełny (z OCR):
  pod 'AutopaySdk', :git => 'https://github.com/Autopay-S-A/autopay-sdk-pay-ios

  # — ALBO — wariant bez OCR:
  # pod 'AutopaySdkWithoutOCR', :git => 'https://github.com/Autopay-S-A/autopay-sdk-pay-ios'
end
```

Instalacja:

```ruby
pod deintegrate
rm -rf Pods Podfile.lock
pod install
```
{% endtab %}
{% endtabs %}

### 1.3 Import w kodzie

{% tabs %}
{% tab title="Swift" %}
```swift
import AutopaySdk               // lub import AutopaySdkWithoutOCR
```
{% endtab %}

{% tab title="Objective-C" %}
Pamiętaj, aby sprawdzić czy w projekcie istnieje plik `[nazwa projektu]-Bridging-Header.h`. Dodaj do niego wpis:

```objc
@import AutopaySdk;            // albo: @import AutopaySdkWithoutOCR;
```

Jeżeli chcesz użyć któregoś komponentu frameworka, w wykorzystującym go pliku dodaj u góry deklarację import

```objc
#import <AutopaySdk/AutopaySdk-Swift.h>    // lub: #import <AutopaySdkWithoutOCR/AutopaySdkWithoutOCR-Swift.h>
```
{% endtab %}
{% endtabs %}

***

### 1.4 Wybór wariantu SDK

* **`AutopaySdk`** – pełny (z OCR, korzysta z Vision/VisionKit).
* **`AutopaySdkWithoutOCR`** – lżejszy, bez OCR.
* W jednym **targetcie** aplikacji używaj **jednego** wariantu.

### 1.5 Uprawnienia, capabilities i manifest prywatności

* **Apple Pay / PassKit** – włącz Capability **Apple Pay** oraz skonfiguruj **Merchant ID** w `*.entitlements`.
* **Kamera** – wymagane jest dodanie do `Info.plist` klucza:\
  `NSCameraUsageDescription` z opisem powodu użycia kamery. SDK wymaga dostępu do kamery w celu realizacji niektórych metod płatności oraz do odczytywania danych z karty (OCR)
* **Privacy manifest (`PrivacyInfo.xcprivacy`)** – plik jest dołączony w `.framework` SDK i zostanie wbudowany automatycznie (SPM i CocoaPods).\
  Jeśli aplikacja używa dodatkowych „Required Reason APIs”, dołącz **własny** manifest.

📌 Ważne: Klucz `NSCameraUsageDescription` jest wymagany niezależnie od wariantu integracji SDK. Jego brak może zakłócić działanie zarówno SDK jak i aplikacji mobilnej.

### 1.6 Aktualizacja wersji

* **SPM:** `File → Packages → Update to Latest Package Versions` lub ręcznie zmień wersję w panelu pakietów.
* **CocoaPods:** `pod update AutopaySdk` (lub `AutopaySdkWithoutOCR`).

***

## 2. Tutorial - przykładowa implementacja

Poniższy tutorial opisuje sposób integracji biblioteki w wariancie z wykorzystaniem tokenu transakcyjnego uzyskanego z backendu aplikacji (Wariant 2). Zalecany jest wariant mieszany – z użyciem `WebView` i tworzeniem transakcji po stronie backendu. Aplikacja otrzymuje jedynie link do kontynuacji, który następnie jest ładowany w komponencie [WebView](tutorial-przykladowa-implementacja#webview).

Wykonaj poniższe czynności, aby zintegrować Twoją aplikację na Androida z **Autopay SDK**:

<figure><img src="../.gitbook/assets/diagram_numbers_variant_II.png" alt=""><figcaption></figcaption></figure>

{% stepper %}
{% step %}
Aplikacja odpytuje swój backend o token transakcyjny (akcja dzieje się bez udziału SDK).
{% endstep %}

{% step %}
Backend aplikacji odpytuje backend Autopay o token transakcyjny (opis w dokumencie [**Specyfikacja integracji Serwisu Partnera z Systemem Płatności Online Autopay w zakresie obsługi transakcji – Usługa pobrania tymczasowego Tokena**](download/System_platnosci_online_obsluga_transakcji_Dodatek_oAuth_1.0.0.pdf)).
{% endstep %}

{% step %}
Backend aplikacji otrzymuje token transakcyjny ważny 1h.
{% endstep %}

{% step %}
Backend aplikacji przekazuje token transakcyjny do aplikacji mobilnej.
{% endstep %}

{% step %}
Aplikacja wykorzystuje token do dokonania transakcji.
{% endstep %}

{% step %}
Status transakcji zostaje przesłany do backendu partnera jako ITN (punkt [**5. Natychmiastowe powiadomienia o zmianie statusu transakcji wejściowej**](https://developers.autopay.pl/online/dokumentacja#powiadomienia-natychmiastowe-\(itn\))**{.external-link}** w dokumencie **Specyfikacja integracji Serwisu Partnera z Systemem Płatności Online Autopay w zakresie obsługi transakcji i rozliczeń**).
{% endstep %}
{% endstepper %}

### Przygotowanie obiektu APConfig

Do poprawnego działania **SDK** w większości klas niezbędne jest przekazanie obiektu `APConfig` zainicjalizowanego specjalnym **tokenem transakcyjnym** do bezpośredniej komunikacji SDK z Systemem Płatności Online BM, **numerem serwisu**, **numerem akceptanta** przydzielonymi przez System Płatności Online BM, **typem środowiska** Systemu Płatności Online BM oraz opcjonalnie **identyfikatorem Merchant Apple Pay**.

Wymagane pola:

* `token`
* `serviceId`
* `acceptorId`
* `environmentType` - `APEnvironmentEnum.dev` lub `APEnvironmentEnum.prod`, w zalezności od środowiska (sandbox lub produkcjne)

Opcjonalne pola:

* `contextPath` - ścieżka endpointu inicjującego transakcję, domyślnie `/payment`
* `applePayMerchantId` — identyfikator merchanta Apple Pay
* `currencies` — lista obsługiwanych walut (domyślnie tylko `PLN`); zaleca się podanie jednej waluty
* `countryCode` - kod kraju wykorzystywany do płatności **ApplePay**, domyślnie `PL`
* `defaultRegulationsCode` - domyślny kod kraju dla którego zostaną pobrane regulaminy w przypadku gdy dla obecnego języka urządzenia nie jest możliwe pobranie regulaminów, domyślnie `PL`
* `regulationsHidden` - steruje możliwością wyświetlania regulaminów na kanałach płatności, domyślnie na wszystkich kanałach regulaminy są widoczne

Dodatkowo klasa `APConfig` posiada następujące metody aktualizujące:

* `setCurrencies(currencies: [String])` przyjmująca listę obsługujących walut
* `setCountryCode(countryCode: String)` przyjmująca kod kraju
* `setRegulationsHidden(hidden: [APGatewayPaymentGroup])` - przyjmuje listę grup płatności na których sekcja ragulaminów zostanie ukryta
* `setToken(token: String)` - aktualizuje token

📌 Ważne: W przypadku przekazania do `setRegulationsHidden` typu `.card` np `[.card]` sekcja regulaminów nie zostanie wyświetlona zarówno na kanale płatności kartą płatniczą jak i aktywacji karty płatniczej.

**UWAGA**: Token transakcyjny ma ograniczony czas ważności, tym samym przed każdym użyciem metody z klasy `Autopay` zalecane jest pobranie z backendu aplikacji nowego tokenu transakcyjnego.

```Swift
    APConfig(
            token: String,
            serviceId: Int,
            acceptorId: Int,
            applePayMerchantId: String?,
            environment: APEnvironmentEnum,
            contextPath: String?,
            currencies: [String]?,
            countryCode: String?,
            defaultRegulationsCode: String? = nil,
            regulationsHidden: [APGatewayPaymentGroup]? = nil
    )
```

```Objective-C
APConfig * config = [[APConfig alloc]
    initWithToken:(NSString *)token
    serviceId:(NSInteger)serviceId
    acceptorId:(NSInteger)acceptorId
    applePayMerchantId:(nullable NSString *)applePayMerchantId
    environment:(APEnvironmentEnum)environment
    contextPath:(nullable NSString *)contextPath
    currencies:(nullable NSArray<NSString *> *)currencies
    countryCode:(nullable NSString *)countryCode;
```

### Przygotowanie obiektu APGatewayBaseViewModelData

Obiekt niezbędny przy wykorzystywaniu widoku takiego jak `APGatewayListView` lub indywidualnych widoków płatności.

Wymagane:

* `config` — obiekt konfiguracyjny - [Przygotowanie obiektu APConfig.](./#przygotowanie-obiektu-apconfig-)
* `amount` — kwota transakcji bez opłaty konsumenckiej

Opcjonalne:

* `summary` — tekst w podsumowaniu płatności; jeśli null lub pusty, podsumowanie nie będzie widoczne, wykorzystywane również do podsumowania płatności ApplePay
* `customerEmail` — adres e-mail
* `customerPhone` — numer telefonu klienta
* `blikContentHeaderTitle` — indywidualne tłumaczenie nagłówka przy płatności Blik. Domyślnie wykorzystywane jest tłumaczenie z SDK.
* `bankContentHeaderTitle` — indywidualne tłumaczenie nagłówka przy płatności Przelewu bankowego. Domyślnie wykorzystywane jest tłumaczenie z SDK.
* `paymentViewCallback` - callback zwracający status płatności lub błąd w przypadku gdy płatność realizowana jest przez SDK
* `payTappedCallback` - callback zwracający dane niezbędne do realizacji płatności przez aplikację, wywoływany jest w momencie kliknięcia przycisku płatności.
* `customerFeeDidUpdatedCallback` — callback wywoływany w momencie zaktualizowania opłaty konsumenckiej.
* `tokenExpiredCallback` - callback wywoływany gdy SDK wykryje wygaśnięty token, zwraca obiekt błędu `APError`.

```Swift
    APGatewayBaseViewModelData(
            config: APConfig,
            amount: Double,
            summary: String?,
            customerEmail: String?,
            customerPhone: String?,
            paymentViewCallback: APPPaymentViewCallback?,
            payTappedCallback: APPayTappedCallback?,
            customerFeeDidUpdatedCallback: APCustomerFeeDidUpdatedCallback?,
            tokenExpiredCallback: APTokenExpiredCallback?
        )
```

```Objective-C
    APGatewayBaseViewModelData(
            config: APConfig,
            amount: Double,
            summary: String?,
            customerEmail: String?,
            customerPhone: String?,
            paymentViewCallback: APPPaymentViewCallback?,
            payTappedCallback: APPayTappedCallback?,
            customerFeeDidUpdatedCallback: APCustomerFeeDidUpdatedCallback?,
            tokenExpiredCallback: APTokenExpiredCallback?
        )
```

**UWAGA:** Jeśli token wygaśnie, należy zablokować interfejs użytkownika, pobrać nowy token, zaktualizować go w obiekcie configuracyjnym `APConfig` metodą `setToken(token: String)`, odblokować interfejs i pozwolić użytkownikowi na kontynuowanie płatności.

📌 Ważne: W zależnośći od tego czy zostanie przekazany `payTappedCallback` czy `paymentViewCallback` SDK realizuje różne scenariusze, jeśli planujesz przetwarzać płatność po stronie aplikacji nie przekazuj `paymentViewCallback` do modelu danych oraz adekwatnie jeśli chcesz aby SDK przeprowadziło pełny proces transakcji nie przekazuj `payTappedCallback`.

### Informacje ogólne

Poniżej przedstawiono podstawowe użycie **SDK Autopay** z wykorzystaniem udostępnionych widoków. Przykładową implementację można znaleźć w aplikacji demonstracyjnej [https://github.com/Autopay-S-A/autopay-sdk-pay-ios](https://github.com/Autopay-S-A/autopay-sdk-pay-ios/tree/main/DemoAutopaySdk){.external-link}.

Widoki:

* nie zawierają tła — należy je nadać nadrzędnemu widokowi,
* nie posiadają marginesów ani paddingów — należy dodać je samodzielnie.
* nie posiadają navigation bara

📌 **Zalecenie:** opakować widok w scrollowalny komponent, aby uniknąć zasłaniania widoku przez klawiaturę lub brak miejsca na ekranie urządzenia.

📌 **Ważne:**\
W przypadku osadzenia dowolnego wiodku pochodzącego z SDK przy użyciu SwiftUI należy przekazać obiekt styli `APStyleManager` jako `enviromentObject`.

### Lista kanałów płatności

Za wyświetlanie rozbudowanego widoku listy kanałów płatności odpowiadają klasy `APGatewayListView` oraz `APGatewayListContainerView` w zależności, czy korzystasz w swojej aplikacji z `SwiftUI` czy `UIKit`.\
Widoki te obsługują pobieranie i wyświetlanie dostępnych kanałów płatności. Po rozwinięciu grupy kanałów, SDK wykonuje zapytanie o kwotę opłaty konsumenckiej oraz odpowiednie regulaminy. Wysokość opłaty konsumenckiej jest zależna od modelu biznesowego jaki został ustalony dla merchanta.

```SwiftUI
    APGatewayListView(
        data: APGatewayBaseViewModelData,
        excludedGatewayPaymentGroups: [APGatewayPaymentGroup] = [],
        selectedPaymentGroupHandler: @escaping (APGatewayPaymentGroup?) -> Void
    ).environmentObject(APStyleManager)
```

```UIKit
    APGatewayListContainerView(
        data: APGatewayBaseViewModelData,
        styleManager: APStyleManager = .init(),
        excludedGatewayPaymentGroups: [APGatewayPaymentGroup],
        selectedPaymentGroupHandler: @escaping (APGatewayPaymentGroup) -> Void,
        deselectedPaymentGroupHandler: @escaping () -> Void
    )
```

Parametry widoku:

Wymagane:

* `data` — obiekt konfiguracyjny widoku - [Przygotowanie obiektu APGatewayBaseViewModelData.](./#przygotowanie-obiektu-apgatewaybaseviewmodeldata-)
* `selectedPaymentGroupHandler` — callback zwracający informacje o wybranym kanale płatności, moze zostać wykorzystane do aktualizacji nagłówka w navigation bar

Opcjonalne:

* `styleManager` — W przypadku użycia **APGatewayListContainerView** manager styli domyślnie przyjmuje style zadeklarowane w SDK, w przypadku użycia przy pomocy SwiftUI konieczne jest przekazanie go jako `.enviromentObject(APStyleManager())`
* `excludedGatewayPaymentGroups` — lista wyłączonych grup kanałów płatności (domyślnie wszystkie są włączone)

📌 **Zalecenie:** opakować widok w scrollowalny komponent, aby uniknąć zasłaniania widoku przez klawiaturę lub brak miejsca na ekranie urządzenia.

### Podsumowanie płatności

Jeśli `summary` zawiera wartość, widok wyświetli nagłówek z kwotą. Po rozwinięciu grupy kanałów, jeśli pobrane regulaminy dla kanału płatności posiadają parametr `serviceModel` o wartości `PAYER`, pobierana i dodawana jest kwota opłaty konsumenckiej, aktualizująca sumę.

**Wyjątek:**\
Dla **Przelewu bankowego** opłata konsumencka i regulaminy ładowane są dopiero po wyborze banku.

### Regulaminy

* SDK automatycznie pobiera i prezentuje użytkownikowi zestaw dokumentów regulaminowych odpowiednich dla wybranego kanału płatności.
* Wymagane regulaminy posiadają checkboxy - brak zgody blokuje transakcję
* Długie zgody można rozwinąć przyciskiem „Zobacz więcej”
* Możliwość wyświetlenia pełnych treści regulaminów (przycisk „Zapoznaj się z treścią”)

W celu ukrycia sekcji regulaminów przekazujemy odpowiednią wartość w polu `regulationsHidden` przy inicjalizacji obiektu konfiguracyjnego `APConfig` lub wywołujemy `setRegulationsHidden` na tym obiekcie. Podając typ kanału płatności wyłączamy widoczność regulaminów na wybranym kanale płatności. Umożliwia to merchantowi wyświetlenie regulaminów w innym miejscu aplikacji.

📌 **Ważne:** W przypadku przekazania w polu `regulationsHidden` lub funkcji `setRegulationsHidden` typu `.card` np `[.card]` sekcja regulaminów nie zostanie wyświetlona zarówno na kanale płatności kartą płatniczą jak i aktywacji karty płatniczej.

### Grupy kanłów płatności

Wyświetlane są na podstawie danych z REST API, ale można je ograniczyć parametrem `excludedGatewayPaymentGroups`.

**Uwaga**: SDK narzuca kolejność ich wyświetlania.

Każda grupa zawiera przycisk „Zmień formę płatności”, prowadzący z powrotem do listy wszystkich grup. Widoki zawierają także regulaminy i przycisk rozpoczęcia transakcji (aktywny tylko po spełnieniu wszystkich warunków).

#### Płatności Blika

* Wymagane jest wpisanie 6-cyfrowego kodu
* Przycisk aktywuje się po wpisaniu ostatniej cyfry oraz po zaakcpetowaniu wymaganych regulaminów jeśli takie istnieją

#### Karta płatnicza / Płatność automatyczna

* Nazwa zależy od konfiguracji: „Karta płatnicza” lub „Karta płatnicza - płatność automatyczna”
* Możliwa aktywacja płatności automatycznej przy pomocy przełącznika
* Wszystkie pola formularza są wymagane
* Wariant z OCR'em umożliwia skanowanie karty (kamera)
* Poprawne wypełnienie aktywuje przycisk płatności
* Formatka kartowa spełnia wymagania PCI DSS i gwarantuje, że żadne wrażliwe dane kartowe nie trafiają do backendu ani aplikacji Merchanta.

Po poprawnym wypełnieniu formularza przycisk rozpoczynania płatności zmieni stan na aktywny.

#### Przelew bankowy

* Opłata konsumencka i regulaminy ładowane dopiero po wyborze banku
* Po załadowaniu dane umożliwiają rozpoczęcie transakcji

#### Płatności Visa Mobile

#### Apple Pay

* Wymagane `merchantId` w `APConfig` oraz włączone capability opisane w [1.5 Uprawnienia, capabilities i manifest prywatności](./#uprawnienia-capabilities-i-manifest-prywatnosci)
* SDK samodzielnie wykrywa dostępność usługi
* Przycisk otwiera interfejs płatności Apple Pay

### WebView / WebViewContainerView

SDK zawiera własną implementację `WKWebView` w postaci klasy `WebView` oraz `WebViewContainerView` dla implementacji UIKIt

```SwiftUI
    WebView(
        url: URL,
        transactionCallback: (_ result: APResult?, _ error: APError?) -> Void
    )
```

```UIKit
    WebViewContainerView(
        url: URL,
        transactionCallback: (_ result: APResult?, _ error: APError?) -> Void
    )
```

* `url` — URL który ma zostać otwarty w webView
* `transactionCallback` — callback zwracający status transakcji lub błąd. Callback może zwrócić zarówno status jak i bład w postaci nil ponieważ jest to wstępna informacja o statusie transakcji. Dla potwierdzenia rezultatu należy skorzystać z metody `getTransactionStatus(orderId: String)` z klasy `Autopay`

**Zalecane użycie**: do obsługi `redirectUrl`

### Kompletny przykład poprawnie zintegrowanej biblioteki

{% stepper %}
{% step %}
Inicjalizacja niezbędnych danych do wyświetlania widoku w viewModelu:

```SwiftUI
import AutopaySdk
import Combine
import SwiftUI

@MainActor
class PaymentContentViewModel: ObservableObject {
    @Published var redirectUrl: URL? = nil
    @Published var shouldShowPaymentStatus: Bool = false
    @Published var shouldShowWebView: Bool = false
    @Published var navigationTitle: LocalizedStringKey = "demo_list_title"
    @Published var styleManager: APStyleManager = APStyleManager()

    private var orderId: String?
    private var error: APError?
    private var subscriptions = Set<AnyCancellable>()

    init() {
        setupBinding()
    }

    var viewData: APGatewayBaseViewModelData {
        return APGatewayBaseViewModelData(
            config: SdkConfigManager.shared.config,
            amount: Double(SdkConfigManager.shared.param(for: .price).replacingOccurrences(of: ",", with: ".")) ?? 29.00,
            summary: SdkConfigManager.shared.param(for: .paymentSummary),
            customerEmail: SdkConfigManager.shared.param(for: .email),
            paymentViewCallback: { [weak self] result, error in
                self?.transactionCompleted(result: result, error: error)
            },
            tokenExpiredCallback: { error in
                // Display progress, refresh token, update it by using:
                // config.setToken(token: "new_token_here")
                // And let user use retry button or pay button
            }
        )
    }

    var paymentStatusViewModel: PaymentStatusViewModel? {
        if let error {
            return .init(orderId: nil, titleKey: error.message, imageName: APPaymentStatus.failure.imageName)
        }
        guard let orderId else {
            return nil
        }
        return .init(orderId: orderId)
    }

    func transactionCompleted(result: AutopaySdk.APTransaction?, error: APError?) {
        clearState()
        guard let result = result else {
            if let error {
                self.error = error
                shouldShowPaymentStatus = true
            }
            return
        }
        if let redirectUrl = result.redirectUrl, let url = URL(string: redirectUrl) {
            self.redirectUrl = url
        }

        orderId = result.orderId

        if redirectUrl == nil {
            shouldShowPaymentStatus = true
        }
    }

    func webViewCompleted(result _: APResult?, error: APError?) {
        redirectUrl = nil
        if let error {
            self.error = error
        }
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.3) {
            self.shouldShowPaymentStatus = true
        }
    }

    func setSelectedPaymentGroup(_ paymentGroup: APGatewayPaymentGroup?) {
        switch paymentGroup {
        case .applePay: navigationTitle = "demo_apple_pay_title"
        case .bankTransfer: navigationTitle = "demo_bank_title"
        case .blik: navigationTitle = "demo_blik_title"
        case .card: navigationTitle = "demo_card_title"
        case .visa: navigationTitle = "demo_visa_title"
        default: navigationTitle = "demo_list_title"
        }
    }

    private func setupBinding() {
        $redirectUrl.sink { [weak self] url in
            self?.shouldShowWebView = url != nil
        }.store(in: &subscriptions)
    }

    func clearState() {
        orderId = nil
        redirectUrl = nil
        error = nil
    }
}
```

```UIKit
import Foundation
import Combine
import AutopaySdk

@MainActor
final class PaymentContentViewModelUIKit {

    @Published var redirectUrl: URL? = nil
    @Published var shouldShowWebView: Bool = false
    @Published var shouldShowPaymentStatus: Bool = false
    @Published var navigationTitle: String = "demo_list_title"

    var orderId: String?
    var error: APError?

    var viewData: APGatewayBaseViewModelData {
        APGatewayBaseViewModelData(
            config: SdkConfigManager.shared.config,
            amount: Double(
                SdkConfigManager.shared.param(for: .price)
                    .replacingOccurrences(of: ",", with: ".")
            ) ?? 29.00,
            summary: SdkConfigManager.shared.param(for: .paymentSummary),
            customerEmail: SdkConfigManager.shared.param(for: .email),
            paymentViewCallback: { [weak self] result, error in
                self?.transactionCompleted(result: result, error: error)
            },
            tokenExpiredCallback: { error in
                // Display progress, refresh token, update it by using:
                // config.setToken(token: "new_token_here")
                // And let user use retry button or pay button
            }
        )
    }

    func webViewCompleted(result: APTransaction?, error: APError?) {
        if let error {
            self.error = error
        }
        self.shouldShowWebView = false
        self.redirectUrl = nil
        self.shouldShowPaymentStatus = true
    }

    func transactionCompleted(result: APTransaction?, error: APError?) {
        if let error {
            self.error = error
            self.shouldShowPaymentStatus = true
            self.shouldShowWebView = false
            self.redirectUrl = nil
            return
        }
        if let redirect = result?.redirectUrl, let url = URL(string: redirect) {
            self.redirectUrl = url
            self.shouldShowWebView = true
        } else {
            self.shouldShowPaymentStatus = true
            self.shouldShowWebView = false
            self.redirectUrl = nil
        }
    }

    func setSelectedPaymentGroup(_ group: APGatewayPaymentGroup?) {
        switch group {
        case .applePay:     navigationTitle = "demo_apple_pay_title"
        case .bankTransfer: navigationTitle = "demo_bank_title"
        case .blik:         navigationTitle = "demo_blik_title"
        case .card:         navigationTitle = "demo_card_title"
        case .visa:         navigationTitle = "demo_visa_title"
        default:            navigationTitle = "demo_list_title"
        }
    }
}
```
{% endstep %}

{% step %}
Konfiguracja widoku wraz z obsługą **redirectUrl** w **WebView**:

```SwiftUI
import AutopaySdk
import SwiftUI

struct PaymentContentView: View {
    @EnvironmentObject private var colorManager: ColorManager
    @StateObject private var viewModel: PaymentContentViewModel = .init()

    var body: some View {
        VStack {
            content
            webViewNavigationLink
        }
        .background(colorManager.neutralLightColor)
        .fullScreenCover(
            isPresented: $viewModel.shouldShowPaymentStatus,
            content: {
                if let viewModel = viewModel.paymentStatusViewModel {
                    PaymentStatusView(viewModel: viewModel, isPresented: $viewModel.shouldShowPaymentStatus) {
                        self.viewModel.redirectUrl = nil
                    }
                }
            }
        )
        .navigationTitle(viewModel.navigationTitle)
    }

    @ViewBuilder
    var content: some View {
        if #available(iOS 17.0, *) {
            ScrollView {
                paymentView
            }
            .contentMargins(.bottom, 48)
        } else {
            ScrollView {
                paymentView
            }
            .safeAreaInset(edge: .bottom) {
                Spacer()
                    .frame(height: 48)
            }
        }
    }

    var paymentView: some View {
        APGatewayListView(
            data: viewModel.viewData) { viewModel.setSelectedPaymentGroup($0) }
            .background(.white)
            .environmentObject(viewModel.styleManager)
    }

    var webViewNavigationLink: some View {
        NavigationLink(
            destination: webView,
            isActive: $viewModel.shouldShowWebView
        ) {}
            .accessibilityHidden(true)
    }

    var webView: some View {
        RedirectWebView(url: viewModel.redirectUrl) { result, error in
            viewModel.webViewCompleted(result: result, error: error)
        }
    }
}

struct RedirectWebView: View {
    @State var url: URL?
    var transactionCallback: (APResult?, APError?) -> Void

    var body: some View {
        Group {
            if let url {
                WebView(
                    url: url,
                    transactionCallback: transactionCallback
                )
                .navigationBarTitleDisplayMode(.inline)
            }
        }
    }
}

```

```UIKit
import UIKit
import Combine
import AutopaySdk

final class PaymentContentViewController: UIViewController {

    let viewModel = PaymentContentViewModelUIKit()
    private var gatewayContainerView: APGatewayListContainerView!
    private var bag = Set<AnyCancellable>()

    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemBackground

        gatewayContainerView = APGatewayListContainerView(
            data: viewModel.viewData,
            selectedPaymentGroupHandler: { [weak self] group in
                self?.viewModel.setSelectedPaymentGroup(group)
            },
            deselectedPaymentGroupHandler: { [weak self] group in
                self?.viewModel.setSelectedPaymentGroup(nil)
            }
        )

        view.addSubview(gatewayContainerView)
        gatewayContainerView.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            gatewayContainerView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            gatewayContainerView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            gatewayContainerView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor),
            gatewayContainerView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor)
        ])

        bindViewModel()
    }

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        gatewayContainerView.attach(to: self)
    }

    private func bindViewModel() {
        viewModel.$navigationTitle
            .receive(on: RunLoop.main)
            .sink { [weak self] key in
                self?.title = NSLocalizedString(key, comment: "")
            }
            .store(in: &bag)

        viewModel.$redirectUrl
            .combineLatest(viewModel.$shouldShowWebView)
            .receive(on: RunLoop.main)
            .sink { [weak self] url, shouldShow in
                guard let self, shouldShow, let url else { return }
                self.openRedirect(url)
            }
            .store(in: &bag)

        viewModel.$shouldShowPaymentStatus
            .removeDuplicates()
            .receive(on: RunLoop.main)
            .sink { [weak self] show in
                guard let self, show else { return }
                self.presentPaymentStatus()
            }
            .store(in: &bag)
    }

    private func openRedirect(_ url: URL) {
        let redirectVC = RedirectWebViewController(
            url: url,
            transactionCallback: { [weak self] result, error in
                self?.viewModel.webViewCompleted(result: result as? APTransaction, error: error)
            }
        )
        navigationController?.pushViewController(redirectVC, animated: true)
    }

    private func presentPaymentStatus() {
        let alert = UIAlertController(
            title: "Status płatności",
            message: viewModel.error?.localizedDescription ?? "Zakończono",
            preferredStyle: .alert
        )
        alert.addAction(UIAlertAction(title: "OK", style: .default) { [weak self] _ in
            self?.viewModel.clearState()
        })
        present(alert, animated: true)
    }
}

public final class RedirectWebViewController: UIViewController {

    private let url: URL
    private let transactionCallback: APPWebViewCallback
    private var webContainer: WebViewContainerView!

    public init(url: URL, transactionCallback: @escaping APPWebViewCallback) {
        self.url = url
        self.transactionCallback = transactionCallback
        super.init(nibName: nil, bundle: nil)
    }

    required init?(coder: NSCoder) { nil }

    public override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .systemBackground

        webContainer = WebViewContainerView(
            url: url,
            transactionCallback: transactionCallback
        )

        view.addSubview(webContainer)
        webContainer.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            webContainer.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            webContainer.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
            webContainer.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor),
            webContainer.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor)
        ])
    }

    public override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        webContainer.attach(to: self)
    }
}
```
{% endstep %}
{% endstepper %}

## 3. Funkcjonalności zaawansowane

### Kontynuacja transakcji z linku

Oprócz startu transakcji bezpośrednio z aplikacji mobilnej, istnieje również możliwość kontynuacji transakcji na podstawie adresu **URL** otrzymanego z backendu aplikacji. W takim przypadku wykorzystywany jest widok `WebView` lub `WebViewContainerView` w przypadku UIKit, który posiada specjalnie przygotowane `WKWebView`.

Wykonaj poniższe czynności, aby zintegrować Twoją aplikację w trybie kontynuacji transakcji.

#### Wariant I (mieszany)

<figure><img src="../.gitbook/assets/diagram_numbers_variant_I.png" alt=""><figcaption></figcaption></figure>

{% stepper %}
{% step %}
Aplikacja odpytuje swój backend o token transakcyjny (akcja dzieje się bez udziału SDK).
{% endstep %}

{% step %}
Backend aplikacji odpytuje backend Autopay o token transakcyjny (opis w dokumencie [token.md](../token.md "mention"))
{% endstep %}

{% step %}
Backend aplikacji otrzymuje token transakcyjny ważny 1h.
{% endstep %}

{% step %}
Backend aplikacji przekazuje token transakcyjny do aplikacji mobilnej.
{% endstep %}

{% step %}
Aplikacja za pośrednictwem SDK wyświetla listę z kanałami płatności, lub indywidualny kanał.
{% endstep %}

{% step %}
SDK odbiera listę kanałów płatności i ładuje ją do natywnych widoków.
{% endstep %}

{% step %}
Aplikacja mobilna przesyła do backendu partnera dane transakcyjne (możliwe parametry: PaymentToken, AuthorizationCode, GatewayId, RegulationParams, WalletType), zwrócone przez natywny widok SDK po kliknięciu przez użytkownika przycisku "Zapłać".
{% endstep %}

{% step %}
Backend partnera wykonuje odpytanie o przedtransakcję do backendu Autopay, dla otrzymanych od aplikacji mobilnej parametrów transakcji.
{% endstep %}

{% step %}
W rezultacie żądania rozpoczęcia transakcji, backend partnera dostaje w zależności od kanału płatności:

* Link do kontynuacji transakcji (PBL, Fast Transfer, Google Pay, Aktywacja i płatność kartą)
* Wstępne informacje o statusie transakcji (Blik, Apple Pay)
{% endstep %}

{% step %}
Backend aplikacji przekazuje link do kontynuacji transakcji do aplikacji mobilnej jako odpowiedź na informację o potrzebie rozpoczęcia transakcji.
{% endstep %}

{% step %}
Aplikacja uruchamia link do kontynuacji transakcji poprzez załadowanie go do SDK.
{% endstep %}

{% step %}
Status transakcji zostaje przesłany do backendu partnera jako ITN (punkt [**5. Natychmiastowe powiadomienia o zmianie statusu transakcji wejściowej**](https://developers.autopay.pl/online/dokumentacja#powiadomienia-natychmiastowe-\(itn\))**{.external-link}** w dokumencie **Specyfikacja integracji Serwisu Partnera z Systemem Płatności Online Autopay w zakresie obsługi transakcji i rozliczeń**).
{% endstep %}
{% endstepper %}

```Swift
import AutopaySdk
import SwiftUI

struct TransactionView: View {
    ...
    var paymentView: some View {
        APGatewayListView(
            data: APGatewayBaseViewModelData(
                config: SdkConfigManager.shared.config,
                amount: Double(SdkConfigManager.shared.param(for: .price).replacingOccurrences(of: ",", with: ".")) ?? 29.00,
                summary: SdkConfigManager.shared.param(for: .paymentSummary),
                customerEmail: SdkConfigManager.shared.param(for: .email),
                payTappedCallback: { paymentGroup, gateway, transactionData in
                    viewModel.processTransacton(paymentGroup: paymentGroup, gateway: gateway, transactionData: transactionData)
                },
                tokenExpiredCallback: { error in
                    // Display progress, refresh token, update it by using:
                    // config.setToken(token: "new_token_here")
                    // And let user use retry button or pay button
               }
            )) { viewModel.setSelectedPaymentGroup($0) }
            .background(.white)
            .environmentObject(viewModel.styleManager)
    }
    ...
}


class TransactionViewModel: ObservableObject {
    ...
    func processTransaction(paymentGroup: APGatewayPaymentGroup, gateway: APGateway, transactionData: APTransactionData) {
        Task {
            do {
                let data = try await Autopay(config: SdkConfigManager.shared.config).startTransaction(transactionData: transactionData)
                transactionCompleted(result: data, error: nil)
            } catch {
                transactionCompleted(result: nil, error: error as? APError)
            }
        }
    }
    ...
}
```

#### Wariant III

<figure><img src="../.gitbook/assets/diagram_numbers_variant_III.png" alt=""><figcaption></figcaption></figure>

{% stepper %}
{% step %}
Aplikacja informuje swój backend o potrzebie wystartowania transakcji na skutek np. kliknięcia przycisku **Zapłać** (akcja dzieje się bez udziału SDK).
{% endstep %}

{% step %}
Backend aplikacji odpytuje backend Autopay o przedtransakcję (punkt **1.1 Przedtransakcja** w dokumencie **Specyfikacja integracji Serwisu Partnera z Systemem Płatności Online Autopay w zakresie obsługi transakcji i rozliczeń – Dodatek**).
{% endstep %}

{% step %}
Backend aplikacji otrzymuje link do kontynuacji transakcji.
{% endstep %}

{% step %}
Backend aplikacji przekazuje link do kontynuacji transakcji do aplikacji mobilnej jako odpowiedź na informację o potrzebie rozpoczęcia transakcji.
{% endstep %}

{% step %}
Aplikacja uruchamia link do kontynuacji transakcji poprzez załadowanie go do SDK.
{% endstep %}

{% step %}
Status transakcji zostaje przesłany do backendu partnera jako ITN (punkt [**Natychmiastowe powiadomienia o zmianie statusu transakcji wejściowej**](https://developers.autopay.pl/online/dokumentacja#powiadomienia-natychmiastowe-\(itn\)) w dokumencie **Specyfikacja integracji Serwisu Partnera z Systemem Płatności Online Autopay w zakresie obsługi transakcji i rozliczeń**).
{% endstep %}
{% endstepper %}

Aplikacja ma również możliwość samodzielnie odpytać o status transakcji poprzez wykorzystanie metody `getTransactionStatus()` z klasy `Autopay` (Otrzymany status jest równoznaczny z ITN’ami). Do tego celu potrzebuje `orderId` procesowanej transakcji (może go otrzymać wraz z linkiem do kontynuacji transakcji od swojego backendu).

```Swift
import AutopaySdk
import SwiftUI

struct TransactionView: View {
    @State var url: URL?
    @StateObject private var viewModel: TransactionViewModel = TransactionViewModel()

    var body: some View {
        Group {
            if let url = viewModel.url {
                WebView(url: url) { result, error in
                    if let result {
                        viewModel.checkStatus(result: result)
                    } else {
                        // handle error
                    }
                }
            }
        }
    }
}


class TransactionViewModel: ObservableObject {
    @Published var url: URL?
    private var orderId: String = "TestID"

    func checkStatus(result: APResult?) {
        Task {
            do {
                let status = try await Autopay(config: SdkConfigManager.shared.config).getTransactionStatus(orderId: orderId)
            } catch {
                // handle error
            }
        }
    }
}
```

### Aktywacja karty płatniczej

```SwiftUI
    APCardActivationGatewayView(
        apConfig: APConfig,
        payTappedCallback: APPayTappedCallback? = nil,
        paymentViewCallback: APPPaymentViewCallback? = nil,
        tokenExpiredCallback: APTokenExpiredCallback? = nil
    ).environmentObject(APStyleManager)
```

```UIKit
    APCardActivationGatewayContainerView(
        apConfig: APConfig,
        styleManager: APStyleManager = .init(),
        payTappedCallback: APPayTappedCallback? = nil,
        paymentViewCallback: APPPaymentViewCallback? = nil,
        tokenExpiredCallback: APTokenExpiredCallback? = nil
    )
```

**SDK** udostępnia widok pozwalający na dokonanie aktywacji karty płatniczej. Występuje tutaj zarówno wersja dla SwiftUI **APCardActivationGatewayView**, jak i implementacja dla aplikacji wykorzystujących widoki UIKit **APCardActivationGatewayContainerView**.

📌 **Zalecenie:** opakować widok w scrollowalny komponent, aby uniknąć zasłaniania widoku przez klawiaturę lub brak miejsca na ekranie urządzenia.

* Wszystkie pola formularza są obowiązkowe.
* Niepoprawne uzupełnienie skutkuje pokazaniem błędu przy danym polu.
* Jeśli używany wariant SDK zawiera OCR – pojawi się ikona aparatu umożliwiająca odczyt danych karty.
* Po poprawnym wypełnieniu formularza, przycisk aktywacji staje się aktywny.

📌 Ważne: W zależnośći od tego czy zostanie przekazany `payTappedCallback` czy `paymentViewCallback` SDK realizuje różne scenariusze, jeśli planujesz przetwarzać transakcje po stronie aplikacji nie przekazuj `paymentViewCallback` do modelu danych oraz adekwatnie jeśli chcesz aby SDK przeprowadziło pełny proces transakcji nie przekazuj `payTappedCallback`.

### Samodzielna komunikacja z serwisem Autopay

**SDK** udostępnia szereg metod pozwalających na samodzielną obsługę płatności z serwisem **Autopay**. Tak samo jak w przypadku korzystania z dedykowanych widoków, na początku trzeba przygotować obiekt konfiguracyjny `APConfig` a następnie przekazać go podczas inicjalizacji klasy `Autopay` lub w przypadku Obj-C `AutopayObjC`.

```Swift
    Autopay(
        config: APConfig
    )
```

Wszystkie metody dostępne są z obiektu _Autopay_.

#### Pobieranie listy kanałów płatności

Swift

`func getGatewayList() async throws -> [APGateway]`

Obj-c

`func getGatewayList(completion: @escaping ([APGateway]?, NSError?) -> Void)`

Wywołanie tej metody zwraca w rezultacie listę dostępnych kanałów płatności w podanej konfiguracji. Najistotniejszym elementem w dalszej komunikacji z serwisem **Autopay** będzie parametr **APGateway**._gatewayId_, który posłuży do rozpoczynania transakcji, a także pozwala na pobranie regulaminów i opłaty konsumenckiej dla danego kanału płatności.

#### Pobieranie regulaminów

Swift

`func getRegulations(gatewayId: Int) async throws -> APRegulations`

Obj-c

`func getRegulations(gatewayId: Int, completion: @escaping (APRegulationsObjC?, NSError?) -> Void)`

Wywołanie tej metody zwraca listę regulaminów dla wybranego kanału płatności na podstawie jego identyfikatora. Pobieranie regulaminów nie jest wymagane, ale zalecane, ze względu na możliwą wymagalność ich akceptacji, która będzie musiała być przekazana jako parametry w obiekcie przekazywanym do metody rozpoczynającej transakcję w serwisie **Autopay**.

#### Pobieranie opłaty konsumenckiej

Swift

`func getCustomerFee(gatewayId: Int, amount: Double) async throws -> APCustomerFee`

Obj-c

`func getCustomerFee(gatewayId: Int, amount: Double, completion: @escaping (APCustomerFee?, NSError?) -> Void)`

Opcjonalna metoda zwracająca informację o wymaganej opłacie konsumenckiej i jej odbiorcy. Opłata konsumencka zależna jest od identyfikatora wybranego kanału płatności oraz kwoty transakcji. Nie jest to wymagane w celu rozpoczęcia transakcji, jednak informacja o tej opłacie pojawi się na stronie z adresu przekierowania z obiektu transakcji.

#### Rozpoczynanie transakcji

Swift

`func startTransaction(transactionData: APTransactionData) async throws -> APTransaction`

Obj-c

`func startTransaction(transactionData: APTransactionData, completion: @escaping (APTransaction?, NSError?) -> Void)`

Kluczowa metoda rozpoczynająca transakcję w serwisie **Autopay**. Na podstawie obiektu _transactionData_ tworzony jest request pozwalający na dokonanie transakcji.\
Jedynym wymaganym parametrem tej klasy jest kwota _amount_, a brak uzupełnienia parametru takiego jak _gatewayId_, będzie skutkować adresem przekierowania, na którym będzie możliwość wyboru kanału płatności.\
Istnieje kilka kluczowych metod, które aktualizują parametry obiektu, niektóre z nich są wymagane dla poszczególnych kanałów płatności:

**Funkcje ogólne**

* `func setCustomerEmail(_ customerEmail: String)` - adres email klienta
* `func setCustomerPhone(_ customerPhone: String)` - numer telefonu klienta
* `func setGatewayId(_ gatewayId: String)` - id kanału płatności (domyślnie 0)
* `func setProductList(_ products: APProductList)` - lista produktów
* `func setRegulations(regulations: [APRegulations])` - przekazywanie listy regulaminów ze stanami akceptacji \[TODO: zaktualizowąć pod obj-c]
* `func addParam(value: String, key: String)` - dodwanie parametrów reqestu na podstawie dostępnych w `APRequestDataParamKey` lub customowych

**Apple Pay**

* `func setApplePayPaymentToken(_ paymentToken: PKPaymentToken)` - token transakcji Apple Pay otrzymany z PassKit

**Blik**

* `func setBlikCode(_ blikCode: String)` - kod BLIK

**Płatność kartą**

* `func setRecurringAction(_ recurringAction: APRecurringActionEnum)`

Dla płatności kartą z płatnością automatyczną należy dodać **recurringAction** - `initWithPayment`

Dla aktywacji karty płatniczej należy dodać **recurringAction** - `initWithRefund`

W rezutacie do wykonanej transakcji otrzymamy obiekt **APTransaction** - można go obsłużyć otwierając url _redirectUrl_ w dedykowanym widoku _WebView_. Brak takiego zwracanego parametru lub jego pusta wartość, oznacza, że w dalszym kroku można sprawdzić status transakcji na podstawie parametru _orderId_.

***

#### Sprawdzanie statusu transakcji

Swift

`func getTransactionStatus(orderId: String) async throws -> APTransactionStatus`

Obj-c

`func getTransactionStatus(orderId: String, completion: @escaping (APTransactionStatus?, NSError?) -> Void)`

Metoda pozwala na sprawdzenie statusu transakcji na podstawie jej identyfikatora - orderId, otrzymanego przy rozpoczynaniu transakcji jako parametr **APTransaction**._orderId_. Zwracany obiekt `APTransactionStatus` określa status transakcji dla danego _orderId_.

### Indywidualne grupy płatności

**Autopay SDK** umożliwia użycie dedykowanych kanałów płatności, zamiast pełnej listy kanałów. Dla każdej grupy dostępne są oddzielne komponenty.

| **Grupa płatności** | **SwiftUI**               | **UIKit**                          |
| ------------------- | ------------------------- | ---------------------------------- |
| Banki               | APBankTransferGatewayView | APBankTransferGatewayContainerView |
| BLIK                | APBlikGatewayView         | APBlikPaymentContainerView         |
| Karty               | APCardGatewayView         | APCardGatewayContainerView         |
| Apple Pay           | APApplePayGatewayView     | APApplePayGatewayContainerView     |
| Visa Mobile         | APVisaGatewayView         | APVisaGatewayContainerView         |

Wszystkie komponenty należy zainicjalizować przy pomocy obiektu `APGatewayBaseViewModelData`:

📌 **Ważne:**\
W przypadku osadzenia dowolnego wiodku pochodzącego z SDK przy użyciu SwiftUI należy przekazać obiekt styli `APStyleManager` jako `enviromentObject`.

Przykładowa implementacja dla banków jako grupy kanałów płatności:

```SwiftUI
        APBankTransferGatewayView(
            data: APGatewayBaseViewModelData(
                config: APConfig,
                amount: Double,
                summary: String?,
                customerEmail: String?,
                customerPhone: String?,
                paymentViewCallback: APPPaymentViewCallback?,
                payTappedCallback: APPayTappedCallback?,
                customerFeeDidUpdatedCallback: APCustomerFeeDidUpdatedCallback?,
                tokenExpiredCallback: APTokenExpiredCallback?
            )
        ).environmentObject(APStyleManager)
```

```UIKit
        APBankTransferGatewayContainerView(
            data: APGatewayBaseViewModelData(
                config: APConfig,
                amount: Double,
                summary: String?,
                customerEmail: String?,
                customerPhone: String?,
                paymentViewCallback: APPPaymentViewCallback?,
                payTappedCallback: APPayTappedCallback?,
                customerFeeDidUpdatedCallback: APCustomerFeeDidUpdatedCallback?,
                tokenExpiredCallback: APTokenExpiredCallback?
            ),
            styleManager: APStyleManager = .init()
        )
```

## 4. Personalizacja widoków

### Manager styli

**SDK** umożliwa szereg globalnych personalizacji widoków, które udostępnia. Służy do tego metoda **APStyleManager** przekazywany jako `enviromentObject` w przypadku implementacji SwiftUI oraz atrybut `styleManager: APStyleManager` w inicjalizatorach w przypadku implementacji UIKit. Obiekt ten zawiera style domyślne oraz kolorystykę przedstawioną w aplikacji demonstracyjnej, tak by użytkownik mógł podmienić tylko to czego potrzebuje.

```SwiftUI
import AutopaySdk
import SwiftUI

class PaymentViewModel: ObservableObject {

    lazy var styleManager: APStyleManager = {
        let styleManager = APStyleManager()
        styleManager.typography.labelLargeFont = .systemFont(ofSize: 12)
        styleManager.primaryButtonStyle.containerColor = APColor(light: .blue, dark: .yellow)
        return styleManager
    }()

    lazy var config: APGatewayBaseViewModelData = {
        return APGatewayBaseViewModelData(
            config: SdkConfigManager.shared.config,
            amount: 20.00,
            summary: "Test",
            customerEmail: "test@email.com",
            customerPhone: "+48555555555") { result, error in
                // process result
            } customerFeeDidUpdatedCallback: { customerFee in
                // process update customerFee
            }

    }()
}

struct PaymentView: View {

    @StateObject private var viewModel: PaymentViewModel = .init()

    var body: some View {
        APGatewayListView(
            data: viewModel.config,
            excludedGatewayPaymentGroups: [.applePay]) { group in
                // did select group
            }
            .background(.white)
            .environmentObject(viewModel.styleManager)
    }
}
```

```UIKit
import AutopaySdk
import UIKit

class PaymentViewModel {

    lazy var styleManager: APStyleManager = {
        let styleManager = APStyleManager()
        styleManager.typography.labelLargeFont = .systemFont(ofSize: 12)
        styleManager.primaryButtonStyle.containerColor = APColor(light: .blue, dark: .yellow)
        return styleManager
    }()

    lazy var config: APGatewayBaseViewModelData = {
        return APGatewayBaseViewModelData(
            config: SdkConfigManager.shared.config,
            amount: 20.00,
            summary: "Test",
            customerEmail: "test@email.com",
            customerPhone: "+48555555555") { result, error in
                // process result
            } customerFeeDidUpdatedCallback: { customerFee in
                // process update customerFee
            }

    }()
}

class PaymentViewController: UIViewController {

    private var viewModel: PaymentViewModel = .init()

    override func viewDidLoad() {
        super.viewDidLoad()
        setupPaymentView()
    }

    private func setupPaymentView() {
        let gatewayListView = APGatewayListContainerView(
            data: viewModel.config,
            styleManager: viewModel.styleManager,
            excludedGatewayPaymentGroups: [.applePay]) { group in
                // did select group
            } deselectedPaymentGroupHandler: {
                // did deselect group
            }
        view.addSubview(gatewayListView)
        NSLayoutConstraint.activate([
            gatewayListView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 16),
            gatewayListView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -16),
            gatewayListView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 16)
        ])

        gatewayListView.attach(to: self)

    }
}
```

Klasa ta zawiera zestaw klas grupujących personalizację odpowiednich widoków oraz kilka ogólnych parametrów. W przypadku ustawiania kolorów dla danych elementów korzystamy z klasy **APColor** przyjmującą 2 parametry: `light` - wymagany kolor dla trybu jasnego, a także `dark` będący kolorem używanym w trakcie korzystania z ciemnego trybu w systemie. Kolor dla trybu ciemnego jest opcjonalny, jeśli nie zostanie podany, brana jest wartość koloru dla trybu jasnego. W przypadku styli tekstów korzystamy z klasy **APTextStyle** przyjmującej 2 parametry `font` - czcionka oraz `color` - klasa **APColor** określająca kolor tekstu.

W klasie **APStyleManager** możemy dostarczyć personalizację poszczególnych elementów:

* typography - zestaw czcionek oraz domyślnym kolorem tekstu.
  * labelSmallFont - rozmiar 12, standardowa (400)
  * labelMediumFont - rozmiar 14, standardowa (400)
  * labelLargeFont - rozmiar 16, standardowa (400)
  * labelXLargeFont - rozmiar 18, standardowa (400)
  * labelSmallBoldFont - rozmiar 12, pogrubiona (500)
  * defaultTextColor - domyślny kolor tekstu
* primaryButtonStyle - zestaw parametrów stylizujacych przycisk główny (wypełniony) w SDK, używany w większości ekranów np. jako przycisk płatności lub innej głównej akcji.
  * containerColor - kolor wypełnienia przycisku
  * inactiveContainerColor - kolor wypełnienia przycisku w stanie zablokowanym
  * textStyle - czcionka i kolor treści przycisku (czcionka w rozmiarze 18, waga standardowa (400))
  * textInactiveStyle - czcionka i kolor treści przycisku w stanie zablokowanym (czcionka w rozmiarze 18, waga standardowa (400))
  * borderColor - kolor obramowania przycisku
  * inactiveBorderColor - kolor obramowania przycisku w stanie zablokowanym
  * borderWidth - grubość obramowania
  * radius - promień zaokąglenia przycisku
  * minHeight - minimalna wysokość przycisku
* secondaryButtonStyle - zestaw parametrów stylizujacych przycisk dodatkowy (obramowany) w SDK, użwany w dialogu z odnośnikami do Regulaminów, dialogu przewalutowania
  * containerColor - kolor wypełnienia przycisku
  * inactiveContainerColor - kolor wypełnienia przycisku w stanie zablokowanym
  * textStyle - czcionka i kolor treści przycisku (czcionka w rozmiarze 18, waga standardowa (400))
  * textInactiveStyle - czcionka i kolor treści przycisku w stanie zablokowanym (czcionka w rozmiarze 18, waga standardowa (400))
  * borderColor - kolor obramowania przycisku
  * inactiveBorderColor - kolor obramowania przycisku w stanie zablokowanym
  * borderWidth - grubość obramowania
  * radius - promień zaokąglenia przycisku
  * minHeight - minimalna wysokość przycisku
* tertiaryButtonStyle - zestaw parametrów stylizujacych przycisk pomocniczny (obramowany) w SDK, używany w sekcji regulaminów
  * containerColor - kolor wypełnienia przycisku
  * inactiveContainerColor - kolor wypełnienia przycisku w stanie zablokowanym
  * textStyle - czcionka i kolor treści przycisku (czcionka w rozmiarze 12, waga standardowa (400))
  * textInactiveStyle - czcionka i kolor treści przycisku w stanie zablokowanym (czcionka w rozmiarze 12, waga standardowa (400))
  * borderColor - kolor obramowania przycisku
  * inactiveBorderColor - kolor obramowania przycisku w stanie zablokowanym
  * borderWidth - grubość obramowania
  * radius - promień zaokąglenia przycisku
  * minHeight - minimalna wysokość przycisku
* bankGridStyle - zestaw parametrów stylizujacych siatkę banków na grupie Przelewy bankowe
  * columns - liczba kolumn w siatce banków
  * cellHeight - wysokość komórki z ikoną banku
  * radius - promień załamania obramowania komórki
  * backgroundColor - kolor wypełnienia komórki wewnątrz obramowania
  * checkedBorderColor - kolor obramowania zaznaczonego banku
  * uncheckedBorderColor - kolor obramowania banku gdy nie jest zaznaczony
* checkboxStyle - zestaw parametrów stylizujacych widoki typu checkbox
  * checkedColor - kolor wypełnienia zaznaczonego checkboxa
  * uncheckedColor - kolor obramowania w stanie domyślnym niezaznaczonym
  * errorColor - kolor obramowania w przpadku błędu spowodowanego niezaznaczeniem checkboxa
* dccPaymentFormStyle - zestaw parametrów stylizujących okno z formularzem przewalutowania przy płatności kartą
  * selectedBorderColor - kolor obramowania etykiety zaznaczonej waluty
  * unselectedBorderColor - kolor obramowania etykiety niezaznaczonej waluty
  * cellRadius - promień załamania obrammowania etykiety z walutą
  * cellBackgroundColor - kolor wypełnienia etykiety z walutą wewnątrz obramowania
* dialogStyle - zestaw parametrów stylizujących wyświetlane okna w SDK
  * dialogRadius - zaokrąglenie okna
  * dialogBackgroundColor - kolor okna
* loaderStyle - zestaw parametrów stylizujących widoki ładowania danych
  * color - kolor loadera
  * size - rozmiar loadera
* paymentMethodButtonStyle - zestaw parametrów stylizujących przycisk kanału płatności na liście kanałów płatności
  * containerColor - kolor wypełnienia przycisku
  * inactiveContainerColor - kolor wypełnienia przycisku w stanie zablokowanym
  * textStyle - czcionka i kolor treści przycisku (czcionka w rozmiarze 16, waga standardowa (400))
  * textInactiveStyle - czcionka i kolor treści przycisku w stanie zablokowanym (czcionka w rozmiarze 16, waga standardowa (400))
  * borderColor - kolor obramowania przycisku
  * inactiveBorderColor - kolor obramowania przycisku w stanie zablokowanym
  * borderWidth - grubość obramowania
  * iconColor - kolor ikon na przycisku - tylko w przypadku Karty płatniczej oraz Przelewów bankowych, pozostałe przyciski mają ikony wielokolorowe odpowiadające ich markom
  * radius - promień zaokąglenia przycisku
  * minHeight - minimalna wysokość przycisku
* paymentMethodTitleStyle - zestaw parametrów stylizujących tytuł kanału płatności, po wybraniu danej formy i rozwinięciu jej szczegółów
  * backgroundColor - kolor tła
  * iconColor - kolor ikon na przycisku - tylko w przypadku Karty płatniczej oraz Przelewów bankowych, pozostałe przyciski mają ikony wielokolorowe odpowiadające ich markom
  * textStyle - kolor i styl tekstu elementu (czcionka w rozmiarze 16, waga standardowa (400))
  * radius - promień załamania tła
* radioButtonStyle - zestaw parametrów stylizujacych widoki typu radio button
  * checkedColor - kolor w stanie zaznaczonym
  * uncheckedColor - kolor w stanie odznaczonym
* switchStyle - zestaw parametrów stylizujących widoki typu switch
  * checkedThumbColor - kolor przełącznika w stanie zaznaczonym
  * uncheckedThumbColor - kolor przełącznika w stanie niezaznaczonym
  * checkedTrackColor - kolor tła w stanie zaznaczonym
  * uncheckedTrackColor - kolor tła w stanie niezaznaczonym
  * checkedBorderColor - kolor obramowania w stanie zaznaczonym
  * uncheckedBorderColor - kolor obramowania w stanie niezaznaczonym
* paymentSummaryStyle - zestaw parametrów stylizujących etykietę z podsumowaniem płatności
  * backgroundColor - kolor tła etykiety
  * borderColor - kolor obramowania etykiety
  * borderWidth - grubość obramowania etykiety
  * dividerColor - kolor separatora w etykiecie
  * dividerHeight - grubość separatora w etykiecie
  * radius - promień załamania obramowania etykiety
* textInputStyle - zestaw parametrów stylizujacych widoki wprowadzania danych tekstowych
  * inputTextStyle - kolor i czcionka tekstu wprowadzanego (czcionka w rozmiarze 16, waga standardowa (400))
  * labelTextStyle - kolor i styl tekstu etykiety nad widokiem (czcionka w rozmiarze 14, waga standardowa (400))
  * errorTextStyle - kolor i styl tekstu błędu pod widokiem (czcionka w rozmiarze 14, waga standardowa (400))
  * borderInactiveColor - kolor obramowania w stanie domyślnym
  * borderActiveColor - kolor obramowania w stanie zaznaczonym
  * borderErrorColor - kolor obramowania w przypadku błędu w formularzu
  * backgroundColor - kolor tła wewnatrz obramowania
  * trailingIconsColor - kolor ikon dodatkowych
  * radius - promień załamania obramowania
  * strokeWidth - grubość obramowania
  * spaceBetweenInputs - odległość między polami w formularzu
* errorColor - kolor błędów
* footerIconsColor - kolor ikon partnerów wystepujący na dole listy kanałów płatności

Dodatkowo jako developer możesz zmienić wartość nagłówka na płatności typu BLIK oraz Przelewy bakowego przekazując odpowiednie tłumaczenie do obiektu _APGatewayBaseViewModelData_: **blikContentHeaderTitle** oraz **bankContentHeaderTitle**.

### Przykładowe personalizacje

**Typografia**

Przykład zmiany czcionek na inną wagę oraz rozmiar, wraz ze zmianą koloru czcionki

```Swift
    let styleManager = APStyleManager()
    styleManager.typography.labelSmallFont = .systemFont(ofSize: 10, weight: .light)
    styleManager.typography.labelMediumFont = .systemFont(ofSize: 13, weight: .light)
    styleManager.typography.labelLargeFont = .systemFont(ofSize: 15, weight: .light)
    styleManager.typography.labelXLargeFont = .systemFont(ofSize: 20, weight: .light)
    styleManager.typography.labelSmallBoldFont = .systemFont(ofSize: 10, weight: .heavy)
    styleManager.typography.defaultTextColor = APColor(light: .brown, dark: .yellow)
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.typography.labelSmallFont = .systemFont(ofSize: 10, weight: .light)
styleManager.typography.labelMediumFont = .systemFont(ofSize: 13, weight: .light)
styleManager.typography.labelLargeFont = .systemFont(ofSize: 15, weight: .light)
styleManager.typography.labelXLargeFont = .systemFont(ofSize: 20, weight: .light)
styleManager.typography.labelSmallBoldFont = .systemFont(ofSize: 10, weight: .heavy)
styleManager.typography.defaultTextColor = [[APColor alloc] initWithLight:UIColor.brownColor dark:UIColor.yellowColor];
```

**Przycisk główny**

Przykład zmiany parametrów głównego przycisku.

```Swift
    let styleManager = APStyleManager()
    styleManager.primaryButtonStyle.containerColor = APColor(light: .orange)
    styleManager.primaryButtonStyle.containerInactiveColor = APColor(light: .orange.opacity(0.4))
    styleManager.primaryButtonStyle.borderColor = APColor(light: .blue)
    styleManager.primaryButtonStyle.borderInactiveColor = APColor(light: .blue.opacity(0.4))
    styleManager.primaryButtonStyle.borderWidth = 1
    styleManager.primaryButtonStyle.cornerRadius = 15
    styleManager.primaryButtonStyle.textStyle = APTextStyle(
        font: .systemFont(ofSize: 20, weight: .heavy),
        color: APColor(light: .gray)
    )
    styleManager.primaryButtonStyle.textInactiveStyle = APTextStyle(
        font: .systemFont(ofSize: 20, weight: .heavy),
        color: APColor(light: .gray.opacity(0.4))
    )
```

```Objective-C
APStyleManager *styleManager = [APStyleManager new];
styleManager.primaryButtonStyle.containerColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.primaryButtonStyle.containerInactiveColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
styleManager.primaryButtonStyle.borderColor = [[APColor alloc] initWithLight:UIColor.blueColor];
styleManager.primaryButtonStyle.borderInactiveColor = [[APColor alloc] initWithLight:[UIColor.blueColor colorWithAlphaComponent:0.4]];
styleManager.primaryButtonStyle.borderWidth = 1;
styleManager.primaryButtonStyle.cornerRadius = 15;
styleManager.primaryButtonStyle.textStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.grayColor]];
styleManager.primaryButtonStyle.textInactiveStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:[UIColor.grayColor colorWithAlphaComponent:0.4]]];
```

**Przycisk dodatkowy**

Przykład zmiany parametrów dodatkowego przycisku.

```Swift
    let styleManager = APStyleManager()
    styleManager.secondaryButtonStyle.containerColor = APColor(light: .gray)
    styleManager.secondaryButtonStyle.containerInactiveColor = APColor(light: .gray.opacity(0.4))
    styleManager.secondaryButtonStyle.borderColor = APColor(light: .orange)
    styleManager.secondaryButtonStyle.borderInactiveColor = APColor(light: .orange.opacity(0.4))
    styleManager.secondaryButtonStyle.cornerRadius = 15
    styleManager.secondaryButtonStyle.textStyle = APTextStyle(
        font: .systemFont(ofSize: 20, weight: .heavy),
        color: APColor(light: .orange)
    )
    styleManager.secondaryButtonStyle.textInactiveStyle = APTextStyle(
        font: .systemFont(ofSize: 20, weight: .heavy),
        color: APColor(light: .orange.opacity(0.4))
    )
```

```Objective-C
APStyleManager *styleManager = [APStyleManager new];
styleManager.secondaryButtonStyle.containerColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.secondaryButtonStyle.containerInactiveColor = [[APColor alloc] initWithLight:[UIColor.grayColor colorWithAlphaComponent:0.4]];
styleManager.secondaryButtonStyle.borderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.secondaryButtonStyle.borderInactiveColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
styleManager.secondaryButtonStyle.cornerRadius = 15;
styleManager.secondaryButtonStyle.textStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.orangeColor]];
styleManager.secondaryButtonStyle.textInactiveStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]]];
```

**Przycisk pomocniczy**

Przykład zmiany parametrów pomocniczego przycisku.

```Swift
    let styleManager = APStyleManager()
    styleManager.tertiaryButtonStyle.containerColor = APColor(light: .gray)
    styleManager.tertiaryButtonStyle.containerInactiveColor = APColor(light: .gray.opacity(0.4))
    styleManager.tertiaryButtonStyle.borderColor = APColor(light: .orange)
    styleManager.tertiaryButtonStyle.borderInactiveColor = APColor(light: .orange.opacity(0.4))
    styleManager.tertiaryButtonStyle.cornerRadius = 15
    styleManager.tertiaryButtonStyle.textStyle = APTextStyle(
        font: .systemFont(ofSize: 15, weight: .light),
        color: APColor(light: .orange)
    )
    styleManager.tertiaryButtonStyle.textInactiveStyle = APTextStyle(
        font: .systemFont(ofSize: 15, weight: .light),
        color: APColor(light: .orange.opacity(0.4))
    )
```

```Objective-C
APStyleManager *styleManager = [APStyleManager new];
styleManager.tertiaryButtonStyle.containerColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.tertiaryButtonStyle.containerInactiveColor = [[APColor alloc] initWithLight:[UIColor.grayColor colorWithAlphaComponent:0.4]];
styleManager.tertiaryButtonStyle.borderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.tertiaryButtonStyle.borderInactiveColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
styleManager.tertiaryButtonStyle.cornerRadius = 15;
styleManager.tertiaryButtonStyle.textStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:15 weight:UIFontWeightLight] color:[[APColor alloc] initWithLight:UIColor.orangeColor]];
styleManager.tertiaryButtonStyle.textInactiveStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:15 weight:UIFontWeightLight] color:[[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]]];
```

**Siatka banków**

Przykład zmiany parametrów siatki banków na grupie Przelewy bankowe

```Swift
    let styleManager = APStyleManager()
    styleManager.bankGridStyle.columns = 1
    styleManager.bankGridStyle.cellHeight = 120
    styleManager.bankGridStyle.radius = 5
    styleManager.bankGridStyle.checkedBorderColor = APColor(light: .orange)
    styleManager.bankGridStyle.uncheckedBorderColor = APColor(light: .orange.opacity(0.4))
    styleManager.bankGridStyle.backgroundColor = APColor(light: .gray)
```

```Objective-C
APStyleManager *styleManager = [APStyleManager new];
styleManager.bankGridStyle.columns = 1;
styleManager.bankGridStyle.cellHeight = 120;
styleManager.bankGridStyle.radius = 5;
styleManager.bankGridStyle.checkedBorderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.bankGridStyle.uncheckedBorderColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
styleManager.bankGridStyle.backgroundColor = [[APColor alloc] initWithLight:UIColor.grayColor];
```

**Checkbox**

Przykład zmiany parametrów siatki banków na grupie Przelewy bankowe

```Swift
    let styleManager = APStyleManager()
    styleManager.checkboxStyle.checkedColor = APColor(light: .orange)
    styleManager.checkboxStyle.uncheckedColor = APColor(light: .gray)
    styleManager.checkboxStyle.errorColor = APColor(light: .red)
```

```Objective-C
APStyleManager *styleManager = [APStyleManager new];
styleManager.checkboxStyle.checkedColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.checkboxStyle.uncheckedColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.checkboxStyle.errorColor = [[APColor alloc] initWithLight:UIColor.redColor];
```

**Formularzem przewalutowania**

Przykład zmiany parametrów okna z formularzem przewalutowania przy płatności kartą

```Swift
    let styleManager = APStyleManager()
    styleManager.dccPaymentFormStyle.cellBackgroundColor = APColor(light: .gray.opacity(0.3))
    styleManager.dccPaymentFormStyle.selectedBorderColor = APColor(light: .orange)
    styleManager.dccPaymentFormStyle.unselectedBorderColor = APColor(light: .orange.opacity(0.4))
    styleManager.dccPaymentFormStyle.cellRadius = 5
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.dccPaymentFormStyle.cellBackgroundColor = [[APColor alloc] initWithLight:[UIColor.grayColor colorWithAlphaComponent:0.3]];
styleManager.dccPaymentFormStyle.selectedBorderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.dccPaymentFormStyle.unselectedBorderColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
styleManager.dccPaymentFormStyle.cellRadius = 5;
```

**Okna dialogu**

Przykład zmiany parametrów okna dialogu

```Swift
    let styleManager = APStyleManager()
    styleManager.dialogStyle.dialogBackgroundColor = APColor(light: .white)
    styleManager.dialogStyle.dialogRadius = 5
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.dialogStyle.dialogBackgroundColor = [[APColor alloc] initWithLight:UIColor.whiteColor];
styleManager.dialogStyle.dialogRadius = 5;
```

**Loader**

Przykład zmiany parametrów komponentu ładowania danych

```Swift
    let styleManager = APStyleManager()
    styleManager.loaderStyle.size = 30
    styleManager.loaderStyle.color = APColor(light: .orange)
```

```Sbjective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.loaderStyle.size = 30;
styleManager.loaderStyle.color = [[APColor alloc] initWithLight:UIColor.orangeColor];
```

**Przycisków kanału płatności**

Przykład zmiany parametrów przycisków kanału płatności na liście kanałów płatności

```Swift
    let styleManager = APStyleManager()
    styleManager.paymentMethodButtonStyle.containerColor = APColor(light: .gray)
    styleManager.paymentMethodButtonStyle.borderColor = APColor(light: .orange)
    styleManager.paymentMethodButtonStyle.cornerRadius = 15
    styleManager.paymentMethodButtonStyle.iconColor = APColor(light: .orange)
    styleManager.paymentMethodButtonStyle.textStyle = APTextStyle(
        font: .systemFont(ofSize: 20, weight: .heavy),
        color: APColor(light: .orange)
    )
```

```Objective-C
APStyleManager *styleManager = [APStyleManager new];
styleManager.paymentMethodButtonStyle.containerColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.paymentMethodButtonStyle.borderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.paymentMethodButtonStyle.cornerRadius = 15;
styleManager.paymentMethodButtonStyle.iconColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.paymentMethodButtonStyle.textStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.orangeColor]];
```

**Tytułu kanału płatności**

Przykład zmiany parametrów tytułu kanału płatności po wybraniu danej formy i rozwinięciu jej szczegółów

```Swift
    let styleManager = APStyleManager()
    styleManager.paymentMethodTitleStyle.backgroundColor = APColor(light: .gray)
    styleManager.paymentMethodTitleStyle.iconColor = APColor(light: .orange)
    styleManager.paymentMethodTitleStyle.radius = 15
    styleManager.paymentMethodTitleStyle.textStyle = APTextStyle(
        font: .systemFont(ofSize: 20, weight: .heavy),
        color: APColor(light: .orange)
    )
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.paymentMethodTitleStyle.backgroundColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.paymentMethodTitleStyle.iconColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.paymentMethodTitleStyle.radius = 15;
styleManager.paymentMethodTitleStyle.textStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.orangeColor]];
```

**Podsumowanie płatności**

Przykład zmiany parametrów komponentu z podsumowaniem płatności

```Swift
    let styleManager = APStyleManager()
    styleManager.paymentSummaryStyle.backgroundColor = APColor(light: .white)
    styleManager.paymentSummaryStyle.borderColor = APColor(light: .orange)
    styleManager.paymentSummaryStyle.borderWidth = 2
    styleManager.paymentSummaryStyle.radius = 5
    styleManager.paymentSummaryStyle.dividerColor = APColor(light: .orange)
    styleManager.paymentSummaryStyle.dividerHeight = 2
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.paymentSummaryStyle.backgroundColor = [[APColor alloc] initWithLight:UIColor.whiteColor];
styleManager.paymentSummaryStyle.borderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.paymentSummaryStyle.borderWidth = 2;
styleManager.paymentSummaryStyle.radius = 5;
styleManager.paymentSummaryStyle.dividerColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.paymentSummaryStyle.dividerHeight = 2;
```

**RadioButton**

Przykład zmiany parametrów komponentu typu radio button

```Swift
    let styleManager = APStyleManager()
    styleManager.radioButtonStyle.checkedColor = APColor(light: .orange)
    styleManager.radioButtonStyle.uncheckedColor = APColor(light: .gray)
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.radioButtonStyle.checkedColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.radioButtonStyle.uncheckedColor = [[APColor alloc] initWithLight:UIColor.grayColor];
```

**Switch / Toggle**

Przykład zmiany parametrów komponentu typu switch / toggle

```Swift
    let styleManager = APStyleManager()
    styleManager.switchStyle.checkedThumbColor = APColor(light: .orange)
    styleManager.switchStyle.uncheckedThumbColor = APColor(light: .gray)
    styleManager.switchStyle.checkedTrackColor = APColor(light: .black)
    styleManager.switchStyle.uncheckedTrackColor = APColor(light: .gray)
    styleManager.switchStyle.checkedBorderColor = APColor(light: .orange)
    styleManager.switchStyle.uncheckedBorderColor = APColor(light: .orange.opacity(0.4))
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.switchStyle.checkedThumbColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.switchStyle.uncheckedThumbColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.switchStyle.checkedTrackColor = [[APColor alloc] initWithLight:UIColor.blackColor];
styleManager.switchStyle.uncheckedTrackColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.switchStyle.checkedBorderColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.switchStyle.uncheckedBorderColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
```

**Pole tekstowe**

Przykład zmiany parametrów pola tekstowego do wprowadzania danych

```Swift
    let styleManager = APStyleManager()
    styleManager.textInputStyle.inputTextStyle = APTextStyle(
            font: .systemFont(ofSize: 20, weight: .heavy),
            color: APColor(light: .blue)
        )
    styleManager.textInputStyle.labelTextStyle = APTextStyle(
            font: .systemFont(ofSize: 15, weight: .heavy),
            color: APColor(light: .blue)
        )
    styleManager.textInputStyle.errorTextStyle = APTextStyle(
            font: .systemFont(ofSize: 12, weight: .heavy),
            color: APColor(light: .red)
        )
    styleManager.textInputStyle.backgroundColor = APColor(light: .gray)
    styleManager.textInputStyle.borderActiveColor = APColor(light: .orange)
    styleManager.textInputStyle.borderInactiveColor = APColor(light: .orange.opacity(0.4))
    styleManager.textInputStyle.borderErrorColor = APColor(light: .red)
    styleManager.textInputStyle.trailingIconsColor = APColor(light: .orange)
    styleManager.textInputStyle.radius = 5
    styleManager.textInputStyle.spaceBetweenInputs = 25
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.textInputStyle.inputTextStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:20 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.blueColor]];
styleManager.textInputStyle.labelTextStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:15 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.blueColor]];
styleManager.textInputStyle.errorTextStyle = [[APTextStyle alloc] initWithFont:[UIFont systemFontOfSize:12 weight:UIFontWeightHeavy] color:[[APColor alloc] initWithLight:UIColor.redColor]];
styleManager.textInputStyle.backgroundColor = [[APColor alloc] initWithLight:UIColor.grayColor];
styleManager.textInputStyle.borderActiveColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.textInputStyle.borderInactiveColor = [[APColor alloc] initWithLight:[UIColor.orangeColor colorWithAlphaComponent:0.4]];
styleManager.textInputStyle.borderErrorColor = [[APColor alloc] initWithLight:UIColor.redColor];
styleManager.textInputStyle.trailingIconsColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
styleManager.textInputStyle.radius = 5;
styleManager.textInputStyle.spaceBetweenInputs = 25;
```

**Stopka logo partnerów**

Przykład zmiany koloru ikon stopki z logo partnerów

```Swift
    let styleManager = APStyleManager()
    styleManager.footerIconsColor = APColor(light: .orange)
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.footerIconsColor = [[APColor alloc] initWithLight:UIColor.orangeColor];
```

**Kolor błędu**

Przykład zmiany koloru ikon błędu

```Swift
    let styleManager = APStyleManager()
    styleManager.errorColor = APColor(light: .black)
```

```Objective-c
APStyleManager *styleManager = [APStyleManager new];
styleManager.errorColor = [[APColor alloc] initWithLight:UIColor.blackColor];
```