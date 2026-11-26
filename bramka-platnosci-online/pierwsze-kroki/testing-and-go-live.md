# Uruchomienie i testy

## Implementacja interfejsów i procesów po stronie Partnera

W minimalnej wersji integracji należy zaimplementować [rozpoczęcie transakcji](../podstawowy-proces-platnosci/start-transaction.md), [powrót z płatności](../podstawowy-proces-platnosci/customer-redirect.md) oraz [odbiór komunikatów ITN](../podstawowy-proces-platnosci/transaction-status-and-itn.md).

{% hint style="info" %}
Zaleca się zapoznanie ze [schematem działania](payment-overview.md). W razie potrzeby warto zapoznać się także z [dodatkowymi parametrami](../dane-transakcji/additional-parameters.md) lub [pozostałymi operacjami API](../pozostale-operacje-api/README.md).
{% endhint %}

## Testy integracyjne i migracja na środowisko produkcyjne

Podczas wykonywania testów należy uzupełniać białe pola arkusza oraz podesłać go zwrotnie do AP, w celu potwierdzenia poprawności integracji przed migracją na środowisko produkcyjne.

{% hint style="info" %}
Przed wdrożeniem produkcyjnym zaleca się wykonać testy zgodnie ze [scenariuszami testowymi](https://developers.autopay.pl/scenariuszetestowe) w wersji podstawowej, a dla bardziej zaawansowanych integracji również zgodnie ze scenariuszami dodatkowymi.
{% endhint %}

[Dane integracyjne](integration-credentials.md) · [Karty testowe](test-cards.md) · [Środowiska](../../zacznij-tutaj/environments.md)
