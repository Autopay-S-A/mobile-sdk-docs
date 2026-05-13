# Migracja z poprzednich wersji


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
