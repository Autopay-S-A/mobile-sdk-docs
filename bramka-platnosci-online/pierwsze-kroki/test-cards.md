# Karty testowe

Na środowisku testowym płatności kartowe są obsługiwane przez mock 3DS i mock autoryzacyjny. Poszczególnym scenariuszom odpowiadają dedykowane numery kart testowych: wynik uwierzytelnienia 3DS i autoryzacji zależy od użytej karty, a nie od rzeczywistego wydawcy.

Karty testowe działają we wszystkich wariantach płatności kartą: w [Paywall](../metody-platnosci/cards.md#paywall), w WhiteLabel bez widgetu oraz w [Widgecie Kartowym](../whitelabel/widget/cards.md). Na środowisku produkcyjnym nie działają.

{% hint style="info" %}
Lista kart testowych i scenariuszy jest w przygotowaniu. Do czasu jej publikacji karty testowe przekaże Ci opiekun integracji.
{% endhint %}

<!-- TODO MIG-039: Uzupełnić tabelę listą kart testowych i scenariuszy po potwierdzeniu przez zespół kart (dev kart). Po uzupełnieniu usunąć hint powyżej.

## Lista kart testowych

Dla każdej karty możesz podać dowolne imię i nazwisko oraz dowolną datę ważności z przyszłości, o ile w tabeli nie wskazano inaczej.

| Numer karty | Data ważności | CVV | 3DS | Wynik autoryzacji | Scenariusz |
| --- | --- | --- | --- | --- | --- |
| | | | | | |

-->
