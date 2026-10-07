# Migracja z poprzednich wersji

W pierwszym kroku usuwamy plik `.aar` z starą wersją **SDK**. Dodawanie biblioteki do projektu odbywa się przy pomocy zależności pochodzącej z repozytorium Maven. Konfiguracja i dodawanie zależności opisana jest w pierwszym rozdziale dokumentacji.

Najważniejsze aspekty migracji:

- Klasę *APConfig* zastąpiono klasą `AutopayConfig`.
- Inicjalizacja **SDK** odbywa się w obiekcie `Autopay` w metodzie `init`,  gdzie przekazujemy konfigurację podobnie jak w starym **SDK**. Najistotniejsze parametry to `serviceId`, `acceptorId` oraz `token`.
- Widok *APGatewayCategories* został zastąpiony widokami `APGatewayListCompose` oraz `APGatewayListView`, można używać w zależności od tego czy aplikacja pisana jest z użyciem technologii Compose, czy przy użyciu XML. Widoki te prezentują całościowy kanał płatności **Autopay** z wszystkimi kanałami płatności, wyświetlaniem regulaminów i dokonywaniem płatności. Istotne jest by obsłużyć w nich callback `onPreTransactionDone`, aby móc otrzymać informacje o transakcji oraz `onPreTransactionError` aby obsłużyć niepowodzenie oraz odświeżanie tokenu.
- Widok APTransactionView został zastąpiony widokiem `APWebView`.
- Widoki tj.
    - *APGooglePayView* zastąpiono `APGooglePayGatewayCompose` oraz `APGooglePayGatewayView`
    - *APVisaMobileView* zastąpiono `APVisaGatewayCompose` oraz `APVisaGatewayView`
    - *APBlikView* zastąpiono `APBlikGatewayCompose` oraz `APBlikGatewayView`
    - *APBankTransferGridView* zastąpiono `APBankGatewayCompose` oraz `APBankGatewayView`
    - *APPaymentCardView* zastąpiono `APCardGatewayCompose` oraz `APCardGatewayView`
    - *APPaymentCardActivationView* zastąpiono `APCardActivationCompose` oraz `APCardActivationView`
	- *APGatewayCategoriesView* został usunięty, można go zastąpić konkretnym widokiem kanału płatności.
- Każdy z tych widoków ma swoją implementację do wykorzystania w aplikacjach pisanych z pomocą Jetpack Compose oraz swój odpowiednik dla standardowej implementacji przy użyciu widoków i XML'a. Każdy z widoków samodzielnie pobiera listę kanałów płatności.
- Usunięto metodę do samodzielnego dokonywania transakcji z kartą płatniczą. Taki rodzaj płatności można dokonać jedynie korzystając z dostarczonych widoków.
- Dla osób, które preferowały korzystanie z **SDK** do samodzielnej komunikacji z serwisem **Autopay**, obiekt `Autopay` posiada wszystkie metody pozwalające na rozpoczynanie transakcji i pobieranie wszystkich niezbędnych danych w celu własnej wizualizacji kanału płatności. Jedyną różnicą względem starego **SDK** jest to, że metody nie zawierają callbacków tylko są oznaczone jako suspend, więc muszą zostać wywołane na osobnym wątku.
- SDK wspiera wersje systemu Android >= 8.0 (API 26).
- Wymagana Java min 11.
- Wymagany Android Gradle Plugin min. 8.12.
- Wymagana Gradle min 9.0.
