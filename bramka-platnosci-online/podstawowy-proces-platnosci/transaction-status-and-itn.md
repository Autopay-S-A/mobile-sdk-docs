# Status transakcji i ITN

Powrót klienta do serwisu i synchroniczne potwierdzenie przyjęcia zlecenia są odrębne od powiadomienia o wyniku płatności.

1. Odbierz i zweryfikuj [komunikat ITN](../powiadomienia/itn.md), w tym dane transakcji i Hash.
2. Rozróżniaj próby płatności o wspólnym `OrderID` na podstawie `remoteID` zgodnie z [opisem statusów i modeli obsługi](../dane-transakcji/statuses.md).
3. Wykonuj logikę biznesową tylko przy pierwszym właściwym powiadomieniu; ponowne komunikaty potwierdzaj bez ponownej realizacji usługi.
4. Odeślij potwierdzenie według [specyfikacji ITN](../powiadomienia/itn.md). Brak poprawnej odpowiedzi powoduje [ponawianie](../powiadomienia/retry-policy.md).

W przypadkach opisanych w [przedtransakcji](../zaawansowane-scenariusze-platnosci/pretransaction.md) skorzystaj z odrębnej usługi [transactionStatus](../pozostale-operacje-api/transaction-status.md).
