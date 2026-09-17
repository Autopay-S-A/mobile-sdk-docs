# Zaawansowana integracja

Zaawansowana integracja rozszerza [podstawowy proces płatności](../bramka-platnosci-online/podstawowy-proces-platnosci/README.md) o wybór metody płatności i osadzenie widgetów w serwisie Partnera, aż do pełnego modelu WhiteLabel.

W modelu WhiteLabel klient wybiera kanał płatności oraz akceptuje wymagane regulaminy w serwisie Partnera. Start transakcji zawiera wybrany `GatewayID` oraz wymagane w danym wariancie identyfikatory zgód.

* [Wybór metody płatności](payment-method-selection.md).
* [Widget Autopay i WidgetJS SDK](widget/).
* [Widget kartowy](widget/cards.md) i [Widget Visa Mobile](widget/visa-mobile.md).
* [Wymagania integracyjne i bezpieczeństwo](requirements.md).
* Backendowy proces: [przedtransakcja](../bramka-platnosci-online/zaawansowane-scenariusze-platnosci/pretransaction.md).

## Wspólne elementy integracji

W obu modelach korzystasz z tej samej dokumentacji bramki: [metod płatności](../bramka-platnosci-online/metody-platnosci/README.md), [danych transakcji](../bramka-platnosci-online/dane-transakcji/README.md), [powiadomień](../bramka-platnosci-online/powiadomienia/README.md), [bezpieczeństwa](../bramka-platnosci-online/bezpieczenstwo/README.md) i [operacji API](../bramka-platnosci-online/pozostale-operacje-api/README.md). Szczegółowe wymagania zależą od wybranego scenariusza.
