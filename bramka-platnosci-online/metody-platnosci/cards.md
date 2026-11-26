# Karty płatnicze

Płatność kartą można przyjmować na trzy sposoby. W każdym z nich Klient podaje dane karty w formularzu Autopay, więc dane karty nie trafiają do Serwisu Partnera. Autopay nie udostępnia integracji, w której Partner samodzielnie zbiera dane karty.

## Paywall

Klient wybiera płatność kartą na stronie Autopay z listą Kanałów Płatności i tam podaje dane karty. Nie wymaga to dodatkowej integracji poza [rozpoczęciem transakcji](../podstawowy-proces-platnosci/start-transaction.md).

## WhiteLabel bez widgetu

Partner prezentuje wybór metody płatności w swoim serwisie i w starcie transakcji lub [przedtransakcji](../zaawansowane-scenariusze-platnosci/pretransaction.md) przekazuje kartowy `GatewayID`:

* `1500` – płatność jednorazowa,
* `1503` – płatność z aktywacją płatności automatycznej.

Klient pomija ekran wyboru Kanału Płatności i trafia bezpośrednio do formularza kartowego Autopay.

## WhiteLabel z Widgetem Kartowym

Formularz kartowy Autopay jest osadzony bezpośrednio w serwisie Partnera, więc Klient nie opuszcza strony zamówienia przy podawaniu danych karty. Widget zwraca token płatniczy, którym Partner rozpoczyna transakcję. Szczegóły w części [Widget Kartowy](../whitelabel/widget/cards.md).

Ten wariant wymaga spełnienia [wymagań integracyjnych i bezpieczeństwa](../whitelabel/requirements.md), w tym przekazania kwestionariusza SAQ-A PCI.

## Powiązane scenariusze

* [Karty testowe](../pierwsze-kroki/test-cards.md) – numery kart i scenariusze dla środowiska testowego.
* [Preautoryzacja kartowa](../zaawansowane-scenariusze-platnosci/card-preauthorization.md) – blokada środków na karcie i późniejsze obciążenie.
* [Płatności automatyczne](../zaawansowane-scenariusze-platnosci/recurring-payments.md) – zapisanie karty i kolejne obciążenia bez udziału Klienta.

{% hint style="info" %}
iFrame kartowy (PayBmCheckout) nie jest już wspierany. Do płatności kartą osadzonej w serwisie Partnera używaj [Widgetu Kartowego](../whitelabel/widget/cards.md).
{% endhint %}
