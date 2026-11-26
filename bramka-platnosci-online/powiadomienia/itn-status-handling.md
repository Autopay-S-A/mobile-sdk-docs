# Obsługa statusów z ITN

Jak zmienia się status płatności (`paymentStatus`) w kolejnych komunikatach [ITN](itn.md) i jak obsłużyć te zmiany po stronie Serwisu Partnera. Słowniki wartości statusów znajdują się w części [Statusy transakcji](../dane-transakcji/statuses.md).

## Szczegółowy opis zachowania i zmiany statusów płatności (paymentStatus)

Wybór przez Klienta metody płatności każdorazowo spowoduje wysłanie
statusu **PENDING**. W kolejnym komunikacie ITN system dostarczy status
**SUCCESS** lub **FAILURE**.

{% hint style="warning" %}
Status **PENDING** nie zostanie wysłany, jeśli:
- klient zrezygnuje lub powróci z ekranu listy metod płatności bez wybrania konkretnej metody. W tym wypadku od razu zostanie wysłany status **FAILURE**. Nie pojawi się status PENDING, ponieważ klient nie rozpoczął procesu płatności.
- status finalny (**SUCCESS** lub **FAILURE**) zostanie dostarczony przed wysłaniem ITN ze statusem **PENDING**.
{% endhint %}


Dla pojedynczej transakcji (o unikatowych parametrach **OrderID** oraz
**RemoteID**) nie może nastąpić zmiana statusu **SUCCESS** na
**PENDING** lub **SUCCESS** na **FAILURE**.

W każdym przypadku może nastąpić zmiana statusu szczegółowego –
**paymentStatusDetails** (kolejne komunikaty o zmianie statusu
szczegółowego są jedynie informacyjne i nie powinny prowadzić do
ponownego wykonania opłacanej usługi/wysyłki produktu itp.).

W szczególnych przypadkach użycia może nastąpić zmiana statusu:

a)  **FAILURE** na **SUCCESS** (np. po zatwierdzeniu przez konsultanta AP transakcji wpłaconej z błędną kwotą. Takie zachowania wymaga specjalnych uzgodnień biznesowych i nie jest włączone domyślnie),

b)  **SUCCESS** na **FAILURE** (np. po wywołaniu wielu transakcji z tym samym **OrderID**, ale różnym **RemoteID**). Taki przypadek występuje w sytuacji rozpoczęcia przez Klienta wielu płatności z tym samym **OrderID** (np. Klient zmienia decyzję, jakim Kanałem płatności chce opłacić transakcję). Każda z rozpoczętych przez niego płatności generuje ITNy i poszczególne transakcje Partner powinien rozróżnić na podstawie parametru **RemoteID**. Ponieważ czas otrzymania statusu **FAILURE** może być bardzo różny, może się zdarzyć otrzymanie takiego statusu po odebraniu **SUCCESS** (oczywiście z innym **RemoteID**). W takim wypadku, komunikat ITN powinien być potwierdzany, ale nie powinien pociągać za sobą anulowania statusu transakcji w systemie Partnera.

### Obsługa statusów transakcji z ITN – model uproszczony

W modelu, w którym nie jest potrzebne powiadamianie mailem/smsem Klienta
o statusach innych niż SUCCESS, można ograniczyć ilość informacji
zapisywanych do bazy Serwisu oraz śledzenie zmian RemoteID.

Wystarczy:

- dla statusów innych niż **SUCCESS**, za każdym razem potwierdzać ITN
  poprawną strukturą odpowiedzi, statusem **CONFIRMED** oraz poprawnie
  policzoną wartością pola Hash,

- w przypadku otrzymania **pierwszego** statusu **SUCCESS**, dodać
  również aktualizację statusu, jego czasu i RemoteID w bazie Serwisu oraz
  realizację procesów biznesowych (powiadomienia do Klienta o zmianie
  statusu, wykonania opłacanej usługi/wysyłki produktu itp.),

- w przypadku otrzymania kolejnego statusu **SUCCESS**, za każdym razem
  potwierdzać ITN poprawną strukturą odpowiedzi, statusem **CONFIRMED**
  oraz poprawnie policzoną wartością pola Hash, bez aktualizacji bazy
  Serwisu oraz bez procesów biznesowych.

### Obsługa statusów transakcji z ITN – model pełny

W modelu, w którym potrzebna jest cała historia zmian statusów
transakcji i/lub powiadamianie Klienta o ważniejszych zmianach statusów
transakcji należy zastosować logikę przybliżoną do poniższego opisu.

| Dotychczasowy ogólny status płatności | PaymentStatus w ITN | Różne RemoteID | Proces biznesowy (powiadomienie Klienta o zmianie statusu) | Proces biznesowy (wykonanie opłaconej usługi, wysyłka produktu itp.) | Zawartość pola confirmation odpowiedzi | Aktualizacja ogólnego statusu transakcji, jej czasu i wartości RemoteID | Uwagi |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Brak | PENDING | n/d | Tak | Nie | CONFIRMED | Tak | |
| Brak | FAILURE | n/d | Tak | Nie | CONFIRMED | Tak | |
| Brak | SUCCESS | n/d | Tak | Tak | CONFIRMED | Tak | |
| PENDING | PENDING | Nie | Nie | Nie | CONFIRMED | Nie | |
| PENDING | FAILURE | Nie | Tak | Nie | CONFIRMED | Tak | |
| PENDING | SUCCESS | Nie | Tak | Tak | CONFIRMED | Tak | |
| FAILURE | PENDING | Nie | Nie | Nie | CONFIRMED | Nie | |
| FAILURE | FAILURE | Nie | Nie | Nie | CONFIRMED | Nie | |
| FAILURE | SUCCESS | Nie | Tak | Tak | CONFIRMED | Tak | |
| SUCCESS | PENDING | Nie | Nie | Nie | CONFIRMED | Nie | Niemożliwe |
| SUCCESS | FAILURE | Nie | Nie | Nie | CONFIRMED | Nie | Niemożliwe |
| SUCCESS | SUCCESS | Nie | Nie | Nie | CONFIRMED | Nie | |
| PENDING | PENDING | Tak | Nie | Nie | CONFIRMED | Tak | |
| PENDING | FAILURE | Tak | Nie | Nie | CONFIRMED | Tak | |
| PENDING | SUCCESS | Tak | Tak | Tak | CONFIRMED | Tak | |
| FAILURE | PENDING | Tak | Nie | Nie | CONFIRMED | Tak | |
| FAILURE | FAILURE | Tak | Nie | Nie | CONFIRMED | Tak | |
| FAILURE | SUCCESS | Tak | Tak | Tak | CONFIRMED | Tak | |
| SUCCESS | PENDING | Tak | Nie | Nie | CONFIRMED | Nie | Niemożliwe |
| SUCCESS | FAILURE | Tak | Nie | Nie | CONFIRMED | Nie | Niemożliwe |
| SUCCESS | SUCCESS | Tak | Nie | Nie | NOTCONFIRMED | Nie | Niemożliwe |
