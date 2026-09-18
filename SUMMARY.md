# Table of contents

* [Dokumentacja Autopay](README.md)

## Zacznij tutaj

* [O Płatnościach Online Autopay](zacznij-tutaj/getting-started.md)
* [Wybierz sposób integracji](zacznij-tutaj/choose-integration.md)
* [Środowisko testowe i produkcyjne](zacznij-tutaj/environments.md)

## Bramka Płatności Online

* [Pierwsze kroki](bramka-platnosci-online/pierwsze-kroki/README.md)
  * [Jak działa płatność](bramka-platnosci-online/pierwsze-kroki/payment-overview.md)
  * [Dane potrzebne do integracji](bramka-platnosci-online/pierwsze-kroki/integration-credentials.md)
  * [Uruchomienie i testy](bramka-platnosci-online/pierwsze-kroki/testing-and-go-live.md)
* [Podstawowy proces płatności](bramka-platnosci-online/podstawowy-proces-platnosci/README.md)
  * [Rozpoczęcie transakcji](bramka-platnosci-online/podstawowy-proces-platnosci/start-transaction.md)
  * [Powrót klienta do serwisu](bramka-platnosci-online/podstawowy-proces-platnosci/customer-redirect.md)
  * [Status transakcji i ITN](bramka-platnosci-online/podstawowy-proces-platnosci/transaction-status-and-itn.md)
* [Zaawansowany proces płatności](integracja-whitelabel/whitelabel.md)
  * [Schemat procesu](integracja-whitelabel/payment-method-selection.md)
  * [Jak działa Widget Autopay](integracja-whitelabel/widget/README.md)
    * [Widget kartowy](integracja-whitelabel/widget/cards.md)
    * [Widget Visa Mobile](integracja-whitelabel/widget/visa-mobile.md)
  * [Wymagania integracyjne i bezpieczeństwo](integracja-whitelabel/requirements.md)
* [Metody płatności](bramka-platnosci-online/metody-platnosci/README.md)
  * [Dostępne metody płatności](bramka-platnosci-online/metody-platnosci/payment-methods.md)
  * [BLIK](bramka-platnosci-online/metody-platnosci/blik.md)
  * [Karty płatnicze](bramka-platnosci-online/metody-platnosci/cards.md)
  * [Pay by Link i szybkie przelewy](bramka-platnosci-online/metody-platnosci/pay-by-link.md)
  * [Google Pay](bramka-platnosci-online/metody-platnosci/google-pay.md)
  * [Apple Pay](bramka-platnosci-online/metody-platnosci/apple-pay.md)
  * [Visa Mobile](bramka-platnosci-online/metody-platnosci/visa-mobile.md)
* [Zaawansowane scenariusze płatności](bramka-platnosci-online/zaawansowane-scenariusze-platnosci/README.md)
  * [Przedtransakcja](bramka-platnosci-online/zaawansowane-scenariusze-platnosci/pretransaction.md)
  * [Płatności automatyczne](bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md)
  * [Preautoryzacja kartowa](bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md)
  * [Płatność z potwierdzeniem merchanta](bramka-platnosci-online/zaawansowane-scenariusze-platnosci/merchant-confirmation.md)
  * [BLIK OneClick](bramka-platnosci-online/zaawansowane-scenariusze-platnosci/blik-oneclick.md)
* [Dane transakcji](bramka-platnosci-online/dane-transakcji/README.md)
  * [Parametry transakcji](bramka-platnosci-online/dane-transakcji/parameters.md)
  * [Koszyk produktów](bramka-platnosci-online/dane-transakcji/product-basket.md)
  * [Dodatkowe parametry](bramka-platnosci-online/dane-transakcji/additional-parameters.md)
  * [Statusy transakcji](bramka-platnosci-online/dane-transakcji/statuses.md)
* [Powiadomienia](bramka-platnosci-online/powiadomienia/README.md)
  * [ITN – status transakcji](bramka-platnosci-online/powiadomienia/itn.md)
  * [IPN – status produktu](bramka-platnosci-online/powiadomienia/ipn.md)
  * [ISTN – status rozliczenia](bramka-platnosci-online/powiadomienia/istn.md)
  * [RPAN i RPDN – płatności automatyczne](bramka-platnosci-online/powiadomienia/rpan-rpdn.md)
  * [Ponawianie powiadomień](bramka-platnosci-online/powiadomienia/retry-policy.md)
* [Bezpieczeństwo](bramka-platnosci-online/bezpieczenstwo/README.md)
  * [Hash i uwierzytelnianie komunikatów](bramka-platnosci-online/bezpieczenstwo/hashing.md)
* [Pozostałe operacje API](bramka-platnosci-online/pozostale-operacje-api/README.md)
  * [Lista metod płatności – gatewayList](bramka-platnosci-online/pozostale-operacje-api/gateway-list.md)
  * [Regulaminy i zgody](bramka-platnosci-online/pozostale-operacje-api/legal-consents.md)
  * [Status transakcji](bramka-platnosci-online/pozostale-operacje-api/transaction-status.md)
  * [Anulowanie transakcji](bramka-platnosci-online/pozostale-operacje-api/cancel-transaction.md)
  * [Saldo](bramka-platnosci-online/pozostale-operacje-api/balance.md)
  * [Wypłaty](bramka-platnosci-online/pozostale-operacje-api/payouts.md)
  * [Zwroty](bramka-platnosci-online/pozostale-operacje-api/refunds.md)

## SDK mobilne

* [O SDK mobilnym](sdk-mobilne/mobile-sdk.md)
* [Android](sdk-mobilne/android/README.md)
  * [Szczegółowy opis klas i metod](sdk-mobilne/android/szczegolowy-opis-klas-i-metod.md)
  * [Migracja z poprzednich wersji](sdk-mobilne/android/migracja-z-poprzednich-wersji.md)
  * [Demo App (Android)](https://github.com/Autopay-S-A/demo-autopay-sdk-android)
* [IOS](sdk-mobilne/ios/README.md)
  * [Szczegółowy opis klas i metod](sdk-mobilne/ios/szczegolowy-opis-klas-i-metod.md)
  * [Migracja z poprzednich wersji](sdk-mobilne/ios/migracja.md)
  * [Demo App (IOS)](https://github.com/Autopay-S-A/autopay-sdk-pay-ios)
* [Skąd wziąć token?](sdk-mobilne/token.md)

## Wtyczki e-commerce

* [O wtyczkach Autopay](wtyczki-e-commerce/plugins.md)
* [PrestaShop](wtyczki-e-commerce/prestashop.md)
* [WooCommerce](wtyczki-e-commerce/woocommerce.md)
* [Magento](wtyczki-e-commerce/magento.md)

## Informacje dodatkowe

* [Słownik pojęć](informacje-dodatkowe/glossary.md)
* [Kody i komunikaty błędów](informacje-dodatkowe/errors.md)
* [Historia zmian](informacje-dodatkowe/changelog.md)
