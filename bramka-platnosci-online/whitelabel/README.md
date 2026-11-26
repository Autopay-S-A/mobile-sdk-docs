# Zaawansowany proces płatności

Zaawansowana integracja (model WhiteLabel) rozszerza [podstawowy proces płatności](../podstawowy-proces-platnosci/): wybór metody płatności i akceptacja regulaminów odbywają się w serwisie Partnera. Dodatkowo Partner może osadzić [Widget Autopay](widget/) (kartowy lub Visa Mobile), aby Klient podawał dane karty lub numer telefonu bez opuszczania serwisu.

W modelu WhiteLabel klient wybiera kanał płatności oraz akceptuje wymagane regulaminy w serwisie Partnera. Start transakcji zawiera wybrany `GatewayID` oraz wymagane w danym wariancie identyfikatory zgód.

* [Schemat procesu WhiteLabel](payment-method-selection.md).
* [Widget Autopay i WidgetJS SDK](widget/).
* [Widget kartowy](widget/cards.md) i [Widget Visa Mobile](widget/visa-mobile.md).
* [Wymagania integracyjne i bezpieczeństwo](requirements.md).
* Backendowy proces: [przedtransakcja](../zaawansowane-scenariusze-platnosci/pretransaction.md).

## Wspólne elementy integracji

W obu modelach korzystasz z tej samej dokumentacji bramki: [metod płatności](../metody-platnosci/), [danych transakcji](../dane-transakcji/), [powiadomień](../powiadomienia/), [bezpieczeństwa](../bezpieczenstwo/) i [operacji API](../pozostale-operacje-api/). Szczegółowe wymagania zależą od wybranego scenariusza.
