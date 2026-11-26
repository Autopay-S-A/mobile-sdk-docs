# BLIK

## BLIK w modelu Paywall

Klient wybiera BLIK na liście kanałów na stronie Autopay (paywall) i wpisuje kod BLIK. Nie wymaga to dodatkowej integracji poza [rozpoczęciem transakcji](../podstawowy-proces-platnosci/start-transaction.md).

Jeśli w starcie transakcji przekażesz `GatewayID=509`, Klient nie zobaczy paywalla z listą kanałów — zostanie przekierowany bezpośrednio na stronę eBLIK, na której wpisuje kod BLIK.

## Pozostałe warianty BLIK

BLIK 0 można uruchomić przez [przedtransakcję](../zaawansowane-scenariusze-platnosci/pretransaction.md) z `GatewayID=509` i kodem w `AuthorizationCode`.

[BLIK OneClick](../zaawansowane-scenariusze-platnosci/blik-oneclick.md) opisuje płatność z użyciem zapamiętanego aliasu, wymagania logowania i obsługę błędów.

[Płatności automatyczne](../zaawansowane-scenariusze-platnosci/recurring-payments.md) opisują BLIK w modelach M i O oraz ich aktywację i obciążenia. Parametry są zdefiniowane w [dodatkowych parametrach transakcji](../dane-transakcji/additional-parameters.md).
