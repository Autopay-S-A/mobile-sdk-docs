# Statusy transakcji



## Szczegółowy opis zachowania i zmiany statusów płatności (paymentStatus)

Wybór przez Klienta metody płatności każdorazowo spowoduje wysłanie
statusu **PENDING**. W kolejnym komunikacie ITN system dostarczy status
**SUCCESS** lub **FAILURE**.

**UWAGA** Status **PENDING** nie zostanie wysłany, jeśli:
- klient zrezygnuje lub powróci z ekranu listy metod płatności bez wybrania konkretnej metody. W tym wypadku od razu zostanie wysłany status **FAILURE**. Nie pojawi się status PENDING, ponieważ klient nie rozpoczął procesu płatności.
- status finalny (**SUCCESS** lub **FAILURE**) zostanie dostarczony przed wysłaniem ITN ze statusem **PENDING**.


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

<!-- TODO MIG-041: Źródło: README-2.md: images/obraz2.png. Problem: Brak obrazu/diagramu: images/obraz2.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-041: Brak obrazu/diagramu: images/obraz2.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


## Szczegółowy opis zmiany statusu weryfikacji – dla transakcji zakończonej poprawnie (wynik pozytywny lub negatywny)

| Status Płatności (paymentStatus) | Status Weryfikacji (verificationStatus) | Szczegóły Weryfikacji (verificationStatusReasons) | Szczegóły |
| --- | --- | --- | --- |
| PENDING | PENDING | Puste | Klient wybrał metodę płatności. |
| SUCCESS | PENDING | Puste | Transakcja została opłacona, System oczekuje na pozyskanie danych wpłacającego z rachunku. |
| SUCCESS | PENDING | NEED_FEEDBACK | Autopay oczekuje na spełnienie warunków formalnych przez Partnera. |
| SUCCESS | POSITIVE | Puste | Weryfikacja przebiegła pozytywnie. |
| SUCCESS | NEGATIVE | Lista powodów dla NEGATIVE | Weryfikacja negatywna. |


## Szczegółowy opis zmiany statusu weryfikacji – dla transakcji nie zakończonej poprawnie

| Status Płatności (paymentStatus) | Status Weryfikacji (verificationStatus) | Szczegóły Weryfikacji (verificationStatusReasons) | Szczegóły |
| --- | --- | --- | --- |
| PENDING | PENDING | Puste | Klient wybrał metodę płatności. |
| FAILURE | PENDING | Puste | Transakcja nie została zakończona poprawnie. Status weryfikacji nie zostanie dostarczony. |


## Szczegółowe statusy transakcji

Komunikat ITN dla transakcji wejściowej zawiera oprócz statusu płatności
(pole **paymentStatus**) szczegółowy opis tego statusu (pole
**paymentStatusDetails**). Opis ten należy traktować informacyjnie,
lista jego dozwolonych wartości jest ciągle powiększana i pojawienie się
nowych wartości nie może pociągać za sobą brak akceptacji komunikatu
ITN.

## Wartości statusów transakcji – Statusy ogólne (niezależne od kanału płatności)

| Wartość pola | Znaczenie pola |
| --- | --- |
| **AUTHORIZED** | transakcja zautoryzowana przez Kanał Płatności |
| **ACCEPTED** | transakcja zatwierdzona przez Call Center (np. w wyniku pozytywnie rozpatrzonej reklamacji) |
| **REJECTED** | transakcja przerwana przez Kanał Płatności (bank/agenta rozliczeniowego) |
| **REJECTED_BY_USER** | transakcja przerwana przez Klienta |
| **INCORRECT_AMOUNT** | wpłacono kwotę różną od kwoty podanej przy starcie transakcji |
| **EXPIRED** | transakcja przeterminowana |
| **CANCELLED** | transakcja anulowana przez Serwis Partnera lub Call Center (np. na prośbę Klienta). Nie jest możliwe wystartowanie nowej ani kontynuowanie wcześniej wystartowanej transakcji o tym samym **OrderID** |
| **RECURSION_INACTIVE** | błąd aktywności płatności cyklicznej |
| **ANOTHER_ERROR** | wystąpił inny błąd przy przetwarzaniu transakcji |

## Wartości statusów transakcji – Statusy kartowe

| Wartość pola | Znaczenie pola | Opcjonalny kod błędu organizacji kartowej |
| --- | --- | --- |
| **CONNECTION_ERROR** | błąd z połączeniem do banku wystawcy karty płatniczej | ✔ |
| **CARD_LIMIT_EXCEEDED** | błąd limitów na karcie płatniczej | ✔ |
| **SECURITY_ERROR** | błąd bezpieczeństwa (np. nieprawidłowy cvv) | ✔ |
| **DO_NOT_HONOR** | odmowa autoryzacji w banku; sugerowany kontakt klienta z wystawcą karty | ✔ |
| **THREEDS_NEGATIVE** | transakcja nieudana w systemie 3DS | ✔ |
| **CARD_EXPIRED** | karta nieważna | ✔ |
| **INCORRECT_CARD_NUMBER** | nieprawidłowy numer karty | ✔ |
| **FRAUD_SUSPECT** | podejrzenie fraudu (np. zagubiona karta itp.) | ✔ |
| **STOP_RECURRING** | rekurencja niemożliwa z powodu anulowania dyspozycji klienta | ✔ |
| **VOID** | transakcja porzucona lub błąd komunikacyjny | ✖ |
| **UNCLASSIFIED** | pozostałe błędy | ✔ |

## Wartości statusów transakcji – Statusy specyficzne dla transakcji BLIK

| Wartość pola | Znaczenie pola |
| --- | --- |
| **INSUFFICIENT_FUNDS** | Brak środków. Zalecane wyświetlenie Klientowi komunikatu o treści: <br> *Płatność nieudana – odmowa banku. Sprawdź powód odmowy w aplikacji bankowej.* <br> *Jeśli powodem jest przekroczenie limitu, możesz go podwyższyć kontaktując się z bankiem.* |
| **LIMIT_EXCEEDED** | Błąd limitów (np. kwotowych). Zalecane wyświetlenie Klientowi komunikatu wyświetlenie informacji o treści: <br> *Płatność nieudana – odmowa banku. Sprawdź powód odmowy w aplikacji bankowej.* <br> *Jeśli powodem jest przekroczenie limitu, możesz go podwyższyć kontaktując się z bankiem.* |
| **BAD_PIN** | podano nieprawidłowy PIN podczas potwierdzania transakcji |
| **ISSUER_DECLINED, USER_DECLINED, SEC_DECLINED** | transakcja przerwana przez Klienta |
| **TIMEOUT** i **AM_TIMEOUT** | timeout w komunikacji z aplikacją mobilną banku |
| **USER_TIMEOUT** | timeout oczekiwania na potwierdzenie transakcji przez Klienta |
