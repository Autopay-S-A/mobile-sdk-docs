# Nawigacja GitBooka

* [Dokumentacja Autopay](README.md)

## Zacznij tutaj

* [O Płatnościach Online Autopay](getting-started/README.md)
* [Wybierz sposób integracji](getting-started/choose-integration.md)
* [Środowisko testowe i produkcyjne](getting-started/environments.md)

## Bramka Płatności Online

* [Bramka Płatności Online](online-payments/README.md)
* Pierwsze kroki
  * [Jak działa płatność](online-payments/getting-started/payment-overview.md)
  * [Dane potrzebne do integracji](online-payments/getting-started/integration-credentials.md)
  * [Uruchomienie i testy](online-payments/getting-started/testing-and-go-live.md)
* Podstawowy proces płatności
  * [Rozpoczęcie transakcji](online-payments/payment-flow/start-transaction.md)
  * [Powrót klienta do serwisu](online-payments/payment-flow/customer-redirect.md)
  * [Status transakcji i ITN](online-payments/payment-flow/transaction-status-and-itn.md)
* Metody płatności
  * [Dostępne metody płatności](online-payments/payment-methods/README.md)
  * [BLIK](online-payments/payment-methods/blik.md)
  * [Karty płatnicze](online-payments/payment-methods/cards.md)
  * [Pay by Link i szybkie przelewy](online-payments/payment-methods/pay-by-link.md)
  * [Google Pay](online-payments/payment-methods/google-pay.md)
  * [Apple Pay](online-payments/payment-methods/apple-pay.md)
  * [Visa Mobile](online-payments/payment-methods/visa-mobile.md)
* Zaawansowane scenariusze płatności
  * [Przedtransakcja](online-payments/advanced-flows/pretransaction.md)
  * [Płatności automatyczne](online-payments/advanced-flows/recurring-payments.md)
  * [Preautoryzacja kartowa](online-payments/advanced-flows/card-preauthorization.md)
  * [Płatność z potwierdzeniem merchanta](online-payments/advanced-flows/merchant-confirmation.md)
  * [BLIK OneClick](online-payments/advanced-flows/blik-oneclick.md)
* Dane transakcji
  * [Parametry transakcji](online-payments/transaction-data/parameters.md)
  * [Koszyk produktów](online-payments/transaction-data/product-basket.md)
  * [Dodatkowe parametry](online-payments/transaction-data/additional-parameters.md)
  * [Statusy transakcji](online-payments/transaction-data/statuses.md)
* Powiadomienia
  * [ITN – status transakcji](online-payments/notifications/itn.md)
  * [IPN – status produktu](online-payments/notifications/ipn.md)
  * [ISTN – status rozliczenia](online-payments/notifications/istn.md)
  * [RPAN i RPDN – płatności automatyczne](online-payments/notifications/rpan-rpdn.md)
  * [Ponawianie powiadomień](online-payments/notifications/retry-policy.md)
* Bezpieczeństwo
  * [Hash i uwierzytelnianie komunikatów](online-payments/security/hashing.md)
* Pozostałe operacje API
  * [Lista metod płatności – gatewayList](online-payments/api-operations/gateway-list.md)
  * [Regulaminy i zgody](online-payments/api-operations/legal-consents.md)
  * [Status transakcji](online-payments/api-operations/transaction-status.md)
  * [Anulowanie transakcji](online-payments/api-operations/cancel-transaction.md)
  * [Saldo](online-payments/api-operations/balance.md)
  * [Wypłaty](online-payments/api-operations/payouts.md)
  * [Zwroty](online-payments/api-operations/refunds.md)

## Integracja WhiteLabel

* [O integracji WhiteLabel](whitelabel/README.md)
* [Wybór metody płatności po stronie merchanta](whitelabel/payment-method-selection.md)
* [Jak działa Widget Autopay](whitelabel/widget/README.md)
  * [Widget kartowy](whitelabel/widget/cards.md)
  * [Widget Visa Mobile](whitelabel/widget/visa-mobile.md)
* [Wymagania integracyjne i bezpieczeństwo](whitelabel/requirements.md)

## SDK mobilne

* [O SDK mobilnym](mobile-sdk/README.md)
* [Android](android/README.md)
  * [Szczegółowy opis klas i metod](android/szczegolowy-opis-klas-i-metod.md)
  * [Migracja z poprzednich wersji](android/migracja-z-poprzednich-wersji.md)
  * [Demo App (Android)](https://github.com/Autopay-S-A/demo-autopay-sdk-android)
* [IOS](ios/README.md)
  * [Szczegółowy opis klas i metod](ios/szczegolowy-opis-klas-i-metod.md)
  * [Migracja z poprzednich wersji](ios/migracja.md)
  * [Demo App (IOS)](https://github.com/Autopay-S-A/autopay-sdk-pay-ios)
* [Skąd wziąć token?](token.md)

## Wtyczki e-commerce

* [O wtyczkach Autopay](plugins/README.md)
* [PrestaShop](plugins/prestashop.md)
* [WooCommerce](plugins/woocommerce.md)
* [Magento](plugins/magento.md)

## Informacje dodatkowe

* [Słownik pojęć](additional-information/glossary.md)
* [Kody i komunikaty błędów](additional-information/errors.md)
* [Historia zmian](additional-information/changelog.md)
