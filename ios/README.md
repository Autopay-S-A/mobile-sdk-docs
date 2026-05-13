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

Poniższy tutorial opisuje sposób integracji biblioteki w wariancie z wykorzystaniem tokenu transakcyjnego uzyskanego z backendu aplikacji (Wariant 2). Zalecany jest wariant mieszany – z użyciem `WebView` i tworzeniem transakcji po stronie backendu. Aplikacja otrzymuje jedynie link do kontynuacji, który następnie jest ładowany w komponencie [WebView](/broken/pages/0e6c5bee74ee25fe1b071518585dec24a8499706#webview).

Wykonaj poniższe czynności, aby zintegrować Twoją aplikację na Androida z **Autopay SDK**:

<figure><img src="../.gitbook/assets/diagram_numbers_variant_II.png" alt=""><figcaption></figcaption></figure>

{% stepper %}
{% step %}
Aplikacja odpytuje swój backend o token transakcyjny (akcja dzieje się bez udziału SDK).
{% endstep %}

{% step %}
Backend aplikacji odpytuje backend Autopay o token transakcyjny (opis w dokumencie [**Specyfikacja integracji Serwisu Partnera z Systemem Płatności Online Autopay w zakresie obsługi transakcji – Usługa pobrania tymczasowego Tokena**](/broken/pages/048f1e0a97b8095e790d176a3d86e8027b4c8374)).
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

@[Powołanie konfiguracji](/broken/pages/cb2e15337d7200598385a5be37deb93a41b031bd)

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

@[Powołanie konfiguracji widoku](/broken/pages/aa1cb090d99450eabc877d681628cf5864ae2357)

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

@[APGatewayListView / APGatewayListContainerView](/broken/pages/8772a06a2831fc4a1dabcb0b0cc5b10c530326c7)

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

@[WebView / WebContainerView](/broken/pages/72596e4da138c3b72021f9e1656c423d169798ee)

* `url` — URL który ma zostać otwarty w webView
* `transactionCallback` — callback zwracający status transakcji lub błąd. Callback może zwrócić zarówno status jak i bład w postaci nil ponieważ jest to wstępna informacja o statusie transakcji. Dla potwierdzenia rezultatu należy skorzystać z metody `getTransactionStatus(orderId: String)` z klasy `Autopay`

**Zalecane użycie**: do obsługi `redirectUrl`

### Kompletny przykład poprawnie zintegrowanej biblioteki

{% stepper %}
{% step %}
Inicjalizacja niezbędnych danych do wyświetlania widoku w viewModelu:

@[Konfiguracja danych](/broken/pages/d23877bd62390273763b6c5768d0cc8550d73734)
{% endstep %}

{% step %}
Konfiguracja widoku wraz z obsługą **redirectUrl** w **WebView**:

@[Konfiguracja widoku](/broken/pages/2145aea8aad6a2bf24578c0b7b9f1c46eb800523)
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

@[Transakcja wariant I](/broken/pages/52ba8eaeebfe3178f8861a89dd22d73c878003b7)

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

@[Transakcja wariant III](/broken/pages/21a474ffd8312d18114cd2749b7a76f4b79b1afa)

### Aktywacja karty płatniczej

@[APCardActivationGatewayView / APCardActivationGatewayContainerView](/broken/pages/141f3abab2df4f508e23598169a93e8d8c6fe3c5)

**SDK** udostępnia widok pozwalający na dokonanie aktywacji karty płatniczej. Występuje tutaj zarówno wersja dla SwiftUI **APCardActivationGatewayView**, jak i implementacja dla aplikacji wykorzystujących widoki UIKit **APCardActivationGatewayContainerView**.

📌 **Zalecenie:** opakować widok w scrollowalny komponent, aby uniknąć zasłaniania widoku przez klawiaturę lub brak miejsca na ekranie urządzenia.

* Wszystkie pola formularza są obowiązkowe.
* Niepoprawne uzupełnienie skutkuje pokazaniem błędu przy danym polu.
* Jeśli używany wariant SDK zawiera OCR – pojawi się ikona aparatu umożliwiająca odczyt danych karty.
* Po poprawnym wypełnieniu formularza, przycisk aktywacji staje się aktywny.

📌 Ważne: W zależnośći od tego czy zostanie przekazany `payTappedCallback` czy `paymentViewCallback` SDK realizuje różne scenariusze, jeśli planujesz przetwarzać transakcje po stronie aplikacji nie przekazuj `paymentViewCallback` do modelu danych oraz adekwatnie jeśli chcesz aby SDK przeprowadziło pełny proces transakcji nie przekazuj `payTappedCallback`.

### Samodzielna komunikacja z serwisem Autopay

**SDK** udostępnia szereg metod pozwalających na samodzielną obsługę płatności z serwisem **Autopay**. Tak samo jak w przypadku korzystania z dedykowanych widoków, na początku trzeba przygotować obiekt konfiguracyjny `APConfig` a następnie przekazać go podczas inicjalizacji klasy `Autopay` lub w przypadku Obj-C `AutopayObjC`.

@[Autopay / AutopayObjc](/broken/pages/96e574f1b29be2099a07423a8193022806035bea)

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

@[APBankTransferGatewayView / APBankTransferGatewayContainerView](/broken/pages/bb1492cf85f9383e10dd95be756a71a5fcb68c75)

## 4. Personalizacja widoków

### Manager styli

**SDK** umożliwa szereg globalnych personalizacji widoków, które udostępnia. Służy do tego metoda **APStyleManager** przekazywany jako `enviromentObject` w przypadku implementacji SwiftUI oraz atrybut `styleManager: APStyleManager` w inicjalizatorach w przypadku implementacji UIKit. Obiekt ten zawiera style domyślne oraz kolorystykę przedstawioną w aplikacji demonstracyjnej, tak by użytkownik mógł podmienić tylko to czego potrzebuje.

@[APStyleManager](/broken/pages/afa2d19038ccfc4302d21a868637be97c107a3b1)

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

@[Zmiana stylu typografii](/broken/pages/24dda175cfbc48b80e11d8634e22890ec2731819)

**Przycisk główny**

Przykład zmiany parametrów głównego przycisku.

@[Zmiana stylu przycisku głównego](/broken/pages/7982ea721f3ed0425165431fec46f50d88b520b4)

**Przycisk dodatkowy**

Przykład zmiany parametrów dodatkowego przycisku.

@[Zmiana stylu przycisku dodatkowego](/broken/pages/163eec5ef906430a51984dd700eb21a7bdad7bc7)

**Przycisk pomocniczy**

Przykład zmiany parametrów pomocniczego przycisku.

@[Zmiana stylu przycisku pomocniczego](/broken/pages/899ae72af97d241204027b9a5045da12d979d0d9)

**Siatka banków**

Przykład zmiany parametrów siatki banków na grupie Przelewy bankowe

@[Zmiana stylu siatki banków](/broken/pages/1c13cdf8425eb5b2e9c4c2a369689cf1203cddc9)

**Checkbox**

Przykład zmiany parametrów siatki banków na grupie Przelewy bankowe

@[Zmiana stylu checkbox](/broken/pages/51d339bf544c07a8cf7ff468e453291871cac334)

**Formularzem przewalutowania**

Przykład zmiany parametrów okna z formularzem przewalutowania przy płatności kartą

@[Zmiana stylu DCC](/broken/pages/d64283b3d4dc4d7da44ba244a5c5b913b057949e)

**Okna dialogu**

Przykład zmiany parametrów okna dialogu

@[Zmiana stylu okna dialogu](/broken/pages/906192f6342a34692966aaa016eabfbfb8dcdad5)

**Loader**

Przykład zmiany parametrów komponentu ładowania danych

@[Zmiana stylu loadera](/broken/pages/875ccd6d1b61605fff4fde02173a1ee685297044)

**Przycisków kanału płatności**

Przykład zmiany parametrów przycisków kanału płatności na liście kanałów płatności

@[Zmiana stylu przycisku kanału płatności](/broken/pages/4470ac9ce62ab51d15a98747bfa88a6499316d34)

**Tytułu kanału płatności**

Przykład zmiany parametrów tytułu kanału płatności po wybraniu danej formy i rozwinięciu jej szczegółów

@[Zmiana stylu tytułu kanału](/broken/pages/48c4fabdf49d13536acadd18e785f3011cfbe725)

**Podsumowanie płatności**

Przykład zmiany parametrów komponentu z podsumowaniem płatności

@[Zmiana stylu podsumowania](/broken/pages/c6787871afd5b6ffe3eb9dd0f76042afd8e378ce)

**RadioButton**

Przykład zmiany parametrów komponentu typu radio button

@[Zmiana stylu RadioButton](/broken/pages/58ae53e1ba50e247c586dae0d6d3489c5d86c302)

**Switch / Toggle**

Przykład zmiany parametrów komponentu typu switch / toggle

@[Zmiana stylu Switcha](/broken/pages/1a1ba2e5247d3fa85ebabc29057cc2adbd762eb2)

**Pole tekstowe**

Przykład zmiany parametrów pola tekstowego do wprowadzania danych

@[Zmiana stylu pola tekstowego](/broken/pages/59d3b997e48bec3b39ea6b0bc47329874bb3716a)

**Stopka logo partnerów**

Przykład zmiany koloru ikon stopki z logo partnerów

@[Zmiana stylu stopki](/broken/pages/44b28a304a7e6fcd85b35d6c2f89c78ae5cbb5e9)

**Kolor błędu**

Przykład zmiany koloru ikon błędu

@[Zmiana stylu błędu](/broken/pages/4b116add101fc7a0727fdbe725e821cc0001e7a0)

## 5. Migracje

W pierwszym kroku usuwamy stary framework z sekcji **Framework, Libraries and Embedded Content**. Dodanbie nowej wersji biblioteki jest możliwe za pomocą SPM lub Cocoapods opisane szczegółowo w sekcji:\
[1.1 Instalacja przez Swift Package Manager (SPM)](/broken/pages/b3972cfa58f93d3d018234bcf403b6c0378c3fdc) lub\
[1.2 Instalacja przez CocoaPods](/broken/pages/34ceea016fb37b5fc38c10352437f95f6b78c875)

Najważniejsze aspekty migracji:

* Aby użyć SDK należy zaimportować SDK za pomocą `import AutopaySdk`
* Główny obiekt Autopay inicjalizuje się podobnie jak w poprzednim SDK za pomocą APConfig. APConfig przy inicjalizacji dodatkowo wymaga parametru applePayMerchantId, który również powinien być ustawiony w konfiguracji projektu w sekcji capability. Dodatkowe parametry, które pojawiły się w obiekcie APConfig to: `contextPath`,`currencies` ,`countryCode`,`defaultRegulationsCode`,`regulationsHidden`. Szczegółowy opis konfiguracji obiektu znajduje się w sekcji [Przygotowanie obiektu APConfig.](./#przygotowanie-obiektu-apconfig-)
* Widok _APGatewayCategories_ został zastąpiony widokami `APGatewayListView` oraz `APGatewayListContainerView`, można używać w zależności od tego czy aplikacja pisana jest z użyciem technologii SwiftUI, czy przy użyciu UIKit. Widoki te prezentują pełną liste kanałów z wszystkimi kanałami płatności, wyświetlaniem regulaminów i dokonywaniem płatności. Wszelkie widoki są napisane w technologii SwiftUI, _APGatewayListContainerView_ oraz jemu podobne są jedynie opakowaniem wiodku SwiftUI do użytku bezpośrednio w UIKit. Opakowanie można również wykonać samodzielnie np korzystając ze sposobu opisanego w artykule: [https://sarunw.com/posts/swiftui-view-as-uiview/](https://sarunw.com/posts/swiftui-view-as-uiview/){.external-link}
* Widok APTransactionView został zastąpiony widokiem `WebView`.
* Widoki tj.
  * _APApplePayView_ zastąpiono `APApplePayGatewayView` oraz `APApplePayGatewayContainerView`
  * _APBlikView_ zastąpiono `APBlikGatewayView` oraz `APBlikGatewayContainerView`
  * _APBankTransferGridView_ zastąpiono `APBankTransferGatewayView` oraz `APBankTransferGatewayContainerView`
  * _APPaymentCardView_ zastąpiono `APCardGatewayCompose` oraz `APCardGatewayView`
  * _APPaymentCardActivationView_ zastąpiono `APCardActivationGatewayView` oraz `APCardActivationGatewayContainerView`
  * _APGatewayCategoriesView_ został usunięty, można go zastąpić konkretnym widokiem kanału płatności.
* Metody obiektu Autopay do bezpośredniej komunikacji z API są typu async i nie mają już callbacków
* Stylizacja widoków odbywa się za pomocą przekazania obiektu `APStyleManager` jako `environmentObject` w przypadku SwiftUI oraz podczas inicjalizacji widoku w przypadku UIKit
* Dla implementacji Obj-c klasa `Autopay` została zastąpiona klasą `AutopayObjC`
* Proponowany scenariusz użycia SDK:

{% stepper %}
{% step %}
Skonfigurowanie `APConfig`
{% endstep %}

{% step %}
Skonfigurowanie `APGatewayBaseViewModelData`
{% endstep %}

{% step %}
Osadzenie `APGatewayListView`
{% endstep %}

{% step %}
W przypadku gdy chcemy samodzielnie wykonać zapytanie transakcji przekazujemy do modelu `APGatewayBaseViewModelData` _payTappedCallback_ następnie musimy rozpocząć transakcje wywołując `Autopay.startTransaction` przy użyciu transactionData otrzymanego w _payTappedCallback_
{% endstep %}

{% step %}
Można też pominąć punkt numer 3. przekazując do modelu `APGatewayBaseViewModelData` _paymentViewCallback_, otrzymamy wtedy automatycznie obiekt `APTransaction` nie wywołując `Autopay.startTransaction`. W przypadku gdy chcemy zlecić wykonanie tego zapytania SDK, _payTappedCallback_ musi być nil
{% endstep %}

{% step %}
W odpowiedzi na `Autopay.startTransaction` lub w _paymentViewCallback_ (w zależności od wybranego sposobu) otrzymamy obiekt `APTransaction` w którym jest redirectUrl
{% endstep %}

{% step %}
Wyświetlenie WebView z redirectUrl (jako parametr przyjmuje również transactionCallback)
{% endstep %}

{% step %}
Po zakończeniu transakcji w _transactionCallback_ zostanie zwrócony obiekt `APResult` lub obiekt `APError`. Następnie można rozpoczać sprawdzanie statusu płatności za pomocą `Autopay.getTransactionStatus`
{% endstep %}
{% endstepper %}
