# Statusy transakcji

Opis zmian statusu płatności w kolejnych komunikatach ITN i zalecana obsługa tych zmian po stronie Serwisu Partnera znajdują się w części [Obsługa statusów z ITN](../powiadomienia/itn-status-handling.md).

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
