# Status transakcji i ITN

Powrót z płatności i synchroniczne potwierdzenie przyjęcia zlecenia są odrębne od powiadomienia o wyniku płatności. O wyniku płatności Serwis Partnera dowiaduje się z komunikatu ITN.

{% stepper %}
{% step %}
### Odbierz i zweryfikuj ITN

Odbierz i zweryfikuj [komunikat ITN](../powiadomienia/itn.md), w tym dane transakcji i Hash.
{% endstep %}

{% step %}
### Rozróżnij próby płatności

Rozróżniaj próby płatności o wspólnym `OrderID` na podstawie `remoteID` zgodnie z [obsługą statusów z ITN](../powiadomienia/itn-status-handling.md).
{% endstep %}

{% step %}
### Zrealizuj usługę tylko raz

Usługę (np. wysyłkę towaru) realizuj tylko raz — przy pierwszym ITN ze statusem **SUCCESS** dla danej transakcji. Kolejne komunikaty (ponowienia, zmiany statusu szczegółowego) tylko potwierdzaj.
{% endstep %}

{% step %}
### Odeślij potwierdzenie

Odeślij potwierdzenie zgodnie z [opisem odpowiedzi na ITN](../powiadomienia/itn.md#odpowiedź-na-powiadomienie-natychmiastowe). Brak poprawnej odpowiedzi powoduje [ponawianie](../powiadomienia/retry-policy.md).
{% endstep %}
{% endstepper %}

{% hint style="info" %}
Jeśli ITN nie dotrze — np. gdy obciążenie przez [przedtransakcję](../zaawansowane-scenariusze-platnosci/pretransaction.md) (BLIK 0, BLIK OneClick) zakończyło się timeoutem — po upływie ważności transakcji odpytaj o jej status usługą [transactionStatus](../pozostale-operacje-api/transaction-status.md).
{% endhint %}
