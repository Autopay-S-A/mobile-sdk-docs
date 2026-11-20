# BLIK OneClick



## Płatność BLIK 0 OneClick

### Opis płatności BLIK 0 OneClick

Jest to rozwiązanie dedykowane dla płatności BLIK, pozwalające
zrealizować płatność bez podawania kodu BLIK (oraz bez konieczności
opuszczania Serwisu). Jej skuteczne zainicjowanie w Systemie powoduje
automatyczne uruchomienie/wybudzenie aplikacji mobilnej banku i
zaprezentowanie transakcji do potwierdzenia Użytkownikowi.

Potencjalne korzyści:

-   udostępnienie pierwszej wygodnej i bezpiecznej metody płatności w
    mCommerce niewymagającej podania numeru karty otwiera ten segment na
    nowych klientów,

-   lepsze doświadczenie zakupowe Klienta - płaci szybciej i wygodniej,

-   częstotliwość zakupów i wartość klienta w czasie – Klienci chętniej
    i częściej kupują w tych sklepach, w których proces zakupowy jest
    wygodniejszy,

-   współczynnik konwersji – Serwis ma większą kontrolę nad procesem
    zakupu i płatności (Klient go nie opuszcza), eliminowane jest ryzyko
    utraty koszyka,

-   szybka decyzja transakcyjna - w krótkim czasie transakcja podlega
    autoryzacji, odmowie lub unieważnieniu,

-   Serwis ma możliwość objęcia analizą samego etapu dokonywania
    płatności.

Warunkiem udostępnienia Klientowi BLIK 0 OneClick jest zautoryzowanie w
Serwisie (posiadanie konta oraz wcześniejsze do niego zalogowanie).
Jeśli podczas wcześniej wykonywanej płatności BLIK, wraz z innymi
informacjami o płatności, Serwis przesłał dedykowany Alias UID (opis
parametrów **BlikUIDKey** oraz **BlikUIDLabel** w innej części
dokumentu), a Klient potwierdzając płatność w aplikacji mobilnej
zaznaczył, że chce zapamiętać sklep, to skutkiem było trwałe powiązanie
(typowo na okres 2 lat) Klienta Serwisu z jego aplikacją, czyli
zarejestrowanie Aliasu UID. Kolejne jego użycie będzie skutkować
autoryzacją transakcji bez podania kodu.

### Wywołanie płatności BLIK 0 OneClick

Zaleca się, aby przy wyborze Kanału Płatności BLIK nie wymuszać podania
kodu BLIK. Warto natomiast wyświetlić hiperłącze „Chcę wprowadzić kod
BLIK" pod przyciskiem „Kupuję i płacę", aby umożliwić wpisanie kodu w
pierwszej próbie (na wypadek, gdyby Klient chciał dokonać płatności BLIK
z innej aplikacji mobilnej niż ta, w której wcześniej zapamiętał dany
Serwis).

Serwis powinien wykonać Przedtransakcję, ze zwróceniem szczególnej uwagi
na:

-   podanie parametru **GatewayID** = 509 – wskazanie kanału płatności
    BLIK,

-   podanie parametrów **BlikUIDKey** oraz **BlikUIDLabel** – wskazanie
    wymaganego w usłudze BLIK 0 OneClick Alias UID (identyfikatora
    użytkownika)

-   podanie parametru **AuthorizationCode** – jeśli Klient podał kod
    BLIK,

-   podanie parametru **BlikAMKey** – jeśli Klient wskazał etykietę
    aplikacji mobilnej banku spośród zaprezentowanej w Serwisie listy,

-   obsługę możliwych odpowiedzi na przedtransakcję, w tym obsłużyć
    „Odpowiedź – brak kontynuacji" oraz błędy specyficzne dla BLIK 0
    OneClick:

a)  błąd wielu aplikacji mobilnych banku (**confirmation=NOTCONFIRMED** oraz **reason=ALIAS_NONUNIQUE**) – wyświetlenie listy etykiet zwróconej w przedtransakcji listy aliasów (pary klucz + etykieta zawarte w strukturze **BlikAMList**), w celu pobrania wybranego klucza i podania go w parametrze **BlikAMKey** kolejnej próby przedtransakcji

b)  błędy autoryzacji (**confirmation=NOTCONFIRMED** oraz **reason** o jednej z wartości: **ALIAS_DECLINED, ALIAS_NOT_FOUND, WRONG_TICKET, TICKET_EXPIRED, TICKET_USED**) – wyświetlenie pola Kod Blik, w celu pobrania go i podania w parametrze **AuthorizationCode** kolejnej próby przedtransakcji
