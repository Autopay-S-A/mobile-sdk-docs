# Płatności automatyczne



## Płatność automatyczna

### Opis płatności automatycznej

Płatność automatyczna to wygodny i bezpieczny sposób dokonywania powtarzalnych transakcji. Polega ona na automatycznym
pobieraniu należności od Klienta w jej terminach płatności, po wcześniejszej aktywacji usługi.
W przypadku kart odbywa się to poprzez przekierowanie klienta do formatki aktywacyjnej usługi. W przypadku BLIK - poprzez akceptację płatności automatycznej w Aplikacji Mobilnej Banku.

**UWAGA:** Przed uruchomieniem płatności automatycznych, Partner zobowiązany jest do zapoznania się ze standardami Autopay oraz wymogami organizacji płatniczych VISA i Mastercard. Wymogi te mają na celu ograniczenie ryzyka wystąpienia chargebacków oraz zapewnienie zgodności z regulacjami branżowymi.
Szczegółowe informacje dostępne są w artykule: <a href="https://developers.autopay.pl/online/wymogi-dla-platnosci-automatycznych" target="_blank">Wymogi dla transakcji automatycznych</a>

Po skutecznym zautoryzowaniu transakcji aktywacyjnej, Autopay przekazuje do Partnera standardowy komunikat o zmianie statusu
transakcji (ITN) oraz komunikat o uruchomieniu usługi płatności automatycznej (RPAN). Komunikat RPAN zawiera pole **clientHash**,
którym Partner będzie identyfikować konkretną płatność automatyczną podczas późniejszych obciążeń oraz dezaktywacji usługi.

Płatności automatyczne dla metody BLIK możemy uruchomić w dwóch wariantach:
1. Model M - z potwierdzeniem każdej płatności przez Klienta w aplikacji mobilnej banku (kanał 522),
2. Model O - bez potwierdzenia płatności w banku (kanał 524). W tym modelu niezbędna jest certyfikacja
   Serwisu Merchanta zgodnie z procedurą opisaną pod adresem:
   <a href="https://blik.com/lp/reccuring-payments/BLIK-Recurring-Payments---Certification-Checklist.141951091.html" target="_blank">https://blik.com/lp/reccuring-payments/BLIK-Recurring-Payments---Certification-Checklist.141951091.html</a>

### Aktywacja płatności automatycznej

Aktywacja płatności automatycznej składa się z autoryzacji transakcji aktywacyjnej, komunikacji ITN oraz RPAN.
Po otrzymaniu RPAN, Partner jest gotowy do wykonywania obciążeń cyklicznych (lub jednym kliknięciem).

Możliwe sposoby aktywacji usługi (**RecurringAction**):

a)  wartość **INIT_WITH_PAYMENT** - odpowiada aktywacji usługi płatności
automatycznej podczas płatności za usługę/towar (karta lub rachunek
obciążane są kwotą należności, a środki z płatności przekazywane
są do Partnera); na liście dostępnych kanałów płatności System
prezentuje tylko płatności automatyczne (o ile nie wybrano kanału
płatności w Serwisie),

b)  wartość **INIT_WITH_REFUND** – odpowiada aktywacji usługi płatności
automatycznej poza procesem płatności za usługę/towar (karta lub
rachunek obciążane są kwotą 1 PLN, po czym następuje automatyczny
zwrot środków na rachunek Klienta); na liście dostępnych kanałów
płatności System prezentuje tylko płatności automatyczne (o ile nie
wybrano kanału płatności w Serwisie),

c)  wartość **INIT_WITHOUT_PAYMENT** – odpowiada aktywacji usługi płatności
automatycznej poza procesem płatności za usługę/towar; Wartość obsługiwana tylko
w modelu WhiteLabel, dla metody BLIK. Kwota startowa dla tej wartości to 0.00.

d)  brak parametru (lub pusty) – o ile nie wybrano kanału płatności w
Serwisie, System wyświetli wszystkie dostępne dla Serwisu kanały
płatności (wraz z automatycznymi) oraz pozostawi Klientowi decyzję:
płatność jednorazowa, czy uruchomienie płatności automatycznej.
Jeśli Klient wybierze płatność automatyczną, to transakcja zostanie
w standardowy sposób rozliczona na rzecz Partnera (a w RPAN wróci
parametr RecurringAction=INIT_WITH_PAYMENT).

<!-- TODO MIG-065: Źródło: README-2.md: images/aktywacja_płatności_automatycznej.png. Problem: Brak obrazu/diagramu: images/aktywacja_płatności_automatycznej.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-065: Brak obrazu/diagramu: images/aktywacja_płatności_automatycznej.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


### Start transakcji automatycznej - wymagane parametry

Wszystkie transakcje w ramach cyklu życia płatności automatycznej (aktywacja i obciążenia) są realizowane w ramach dedykowanych Kanałów
Płatności (BLIK model M – **GatewayID=522**, BLIK model O – **GatewayID=524**, Karty – **GatewayID = 1503**).

Proces aktywacji usługi inicjowany jest z Serwisu Partnera, poprzez rozpoczęcie transakcji z parametrami:
-   **GatewayID** - wskazanie kanału płatności (524 - Płatność Powtarzalna BLIK model O lub 522 - Płatność Powtarzalna BLIK model M, 1503 - Płatność Powtarzalna Kartą),
-   **RecurringAcceptanceState** - fakt zaakceptowania regulaminu usługi płatności automatycznej (RecurringAcceptanceState=ACCEPTED),
-   **RecurringAction** - wybór inicjalizacji płatności automatycznej wraz z możliwym obciążeniem rachunku Klienta (możliwe opcje INIT_WITH_PAYMENT, INIT_WITH_REFUND, INIT_WITHOUT_PAYMENT (BLIK w modelu O));
-   **AuthorizationCode** - W przypadku BLIK - 6 cyfrowy kod BLIK (wygenerowany w aplikacji bankowej),

Parametry opcjonalne:
-   **BlikPPLabel** - Etykieta płatności powtarzalnej BLIK, wyświetlana w aplikacji mobilnej podczas jej akceptowania. W przypadku braku parametru, zostanie użyta jej wartość domyślna (ustalana w trakcie integracji).
-   **RecurringValidityTime** - Data ważności aktywowanych płatności automatycznych BLIK. Jeżeli jest pusta - ustawiana jest zgodnie z konfiguracją serwisu (może być bezterminowa).
-   **RecurringAcceptanceID** - ID regulaminu płatności (jeśli wynika to z odpowiedzi metody legalData (TODO: odwołanie wymaga potwierdzenia)).

W odpowiedzi na przedtransakcję, System Autopay zwróci link do przekierowania Klienta (dla płatności kartowych) albo informację statusie rejestracji
transakcji w systemie Autopay i BLIK (dla płatności BLIK).
Dla płatności BLIK, należy przygotować się na następujące komunikaty błędów (**confirmation=NOTCONFIRMED** oraz **reason** o jednej z wartości poniżej):

| Wartość pola | Znaczenie pola |
| --- | --- |
| **RECURRENCY_NOT_SUPPORTED** | Płatność cykliczna nie jest obsługiwana przez Bank Klienta |
| **WRONG_TICKET** | Podano nieprawidłowy kod BLIK |
| **TICKET_EXPIRED** | Podany kod BLIK wygasł |
| **TICKET_USED** | Podany kod BLIK został już wykorzystany |

**UWAGA:** Niedozwolone jest rozpoczynanie transakcji aktywacyjnych z
wybranym kanałem płatności automatycznej, ale bez wybranego RecurringAction.

### Proces aktywacji usługi płatności automatycznych

Po zautoryzowaniu transakcji, System Autopay przekazuje do Serwisu Partnera komunikat o zmianie statusu transakcji (ITN)
oraz komunikat o uruchomieniu usługi płatności automatycznej (RPAN). Komunikat RPAN jest dedykowany dla zdarzeń
aktywacji płatności automatycznej i zawiera jej identyfikator (ClientHash), którym Partner będzie się posługiwać podczas
późniejszych obciążeń oraz dezaktywacji usługi.

## Obciążenie dla płatności automatycznej

Poprawne odebranie identyfikatora usługi (**ClientHash**), sprawia, że
Partner jest gotowy do automatycznego obciążania Klienta za
towary/usługi zakupione w Serwisie. Proces składa się z transakcji oraz
komunikacji ITN.

Poniżej proces automatycznego obciążenia Klienta za usługę/towar (a więc
**RecurringAction=MANUAL/AUTO** i rozliczenie transakcji do Partnera).

<!-- TODO MIG-066: Źródło: README-2.md: images/obciążenie_dla_płatności_automatycznej.png. Problem: Brak obrazu/diagramu: images/obciążenie_dla_płatności_automatycznej.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-066: Brak obrazu/diagramu: images/obciążenie_dla_płatności_automatycznej.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


Komunikat ITN wysyłany po płatności automatycznej jest podobny do tych,
otrzymywanych po płatnościach jednorazowych. Rozszerzony jest jedynie o
węzeł RecurringData oraz (dla płatności kartowej) CardData.

## Komunikat startu transakcji płatności automatycznej

Aby wykonać automatyczne obciążenie, Serwis Partnera powinien wykonać
Przedtransakcję z parametrem **ClientHash**, zgodnym z aktywowaną
wcześniej usługą płatności automatycznej (pochodzące z RPAN), z
parametrem **RecurringAcceptanceState** o wartości **NOT_APPLICABLE**
oraz odpowiednią wartość parametru **RecurringAction**:

a)  **AUTO** - płatność cykliczna (obciążenie bez udziału Klienta),

b)  **MANUAL** - płatność jednym kliknięciem (obciążenie zlecane przez
Klienta, zwane OneClick).

**UWAGA:** Udział klienta w opcji MANUAL, zazwyczaj ogranicza się do
wywołania komunikatu (wybranie w Serwisie opcji zapłaty zapamiętaną
kartą). W zdecydowanej większości przypadków wymagana jest dodatkowa
autoryzacja w banku (w postaci 3DS lub kodu CVC). Wtedy zamiast
obciążenia (i statusu zlecenia w odpowiedzi na przedtranksację), System
zwróci link do kontynuacji – takie jest domyślne zachowanie systemu na
środowisku testowym. Aby przetestować scenariusz obciążenia bez potrzeby
dodatkowej autoryzacji należy zgłosić potrzebę zmiany konfiguracji
Systemu na czas testu.

**UWAGA:** Opcja niedostępna dla płatności automatycznych BLIK (BLIK
OneClick).

## Dezaktywacja usługi

Partner może dezaktywować usługę płatności automatycznych w dowolnym
momencie. Proces może składać się z komunikatu zlecającego dezaktywację
oraz komunikatu RPDN (dedykowanego dla zdarzeń rezygnacji z usługi
płatności automatycznej).

<!-- TODO MIG-067: Źródło: README-2.md: images/dezaktywacja_usługi.png. Problem: Brak obrazu/diagramu: images/dezaktywacja_usługi.png. Wymagana decyzja/materiał: Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. -->

> TODO MIG-067: Brak obrazu/diagramu: images/dezaktywacja_usługi.png. Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem.


Może się również zdarzyć, że rezygnacja z usługi zostanie zainicjowana
ze strony AP (np. na wniosek Klienta, banku lub organizacji kartowej). W
takiej sytuacji System również dostarczy komunikat RPDN.

## Komunikat dezaktywacji płatności automatycznej

Serwis może wyłączyć usługę poprzez dedykowany komunikat. Wszystkie
parametry przekazywane są metodą POST (na adres
[https://{host_bramki}/deactivate_recurring](https://{host_bramki}/deactivate_recurring)). Protokół rozróżnia
wielkość liter zarówno w nazwach jak i wartościach parametrów. Wartości
przekazywanych parametrów powinny być kodowane w UTF-8.

## Lista parametrów dezaktywacji płatności automatycznej

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera, nadawany w trakcie rejestracji usługi, jednoznacznie identyfikuje Serwis Partnera w Systemie płatności online. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna dla Serwisu Partnera. |
| 3 | ClientHash | TAK | string{1,64} | Identyfikator płatności automatycznej. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |

**UWAGA:** Element Hash (komunikatu) służy do autentykacji dokumentu. Wartość tego elementu obliczana jest jako wartość funkcji skrótu z łańcucha zawierającego sklejone wartości wszystkich pól dokumentu oraz dołączonego klucza współdzielonego.

## Odpowiedź

W odpowiedzi na powiadomienie zwracany jest tekst w formacie XML w tej
samej sesji HTTP, zawierający potwierdzenie.

Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<confirmationList>
		<serviceID>ServiceID</serviceID>
		<messageID>MessageID</messageID>
		<recurringConfirmations>
			<recurringConfirmed>
				<clientHash>ClientHash</clientHash>
				<confirmation>Confirmation</confirmation>
				<reason>Reason</reason>
			</recurringConfirmed>
		</recurringConfirmations>
		<hash>Hash</hash>
	</confirmationList>
```

## Element confirmation

Element **confirmation** służy do przekazania stanu weryfikacji
autentyczności operacji przez Serwis. Wartość elementu wyznaczana jest
przez sprawdzenie poprawności wartości parametrów **serviceID** oraz
**clientHash** z podanymi w komunikacie RPAN przy rozpoczęciu danej
płatności aktywacyjnej, a także weryfikację zgodności wyliczonego skrótu
z parametrów komunikatu z wartością przekazaną w polu Hash.

Przewidziano dwie wartości elementu **confirmation**:

a)  **CONFIRMED** – wartości parametrów są poprawne oraz parametr Hash
są zgodne – operacja autentyczna;

b)  **NOTCONFIRMED** – wartości w obu komunikatach są niepoprawne lub
niezgodność Hash – operacja nieautentyczna;

**UWAGA:** Element hash (w odpowiedzi na komunikat) służy do autentykacji odpowiedzi i liczony jest z wartości parametrów odpowiedzi. Wartość tego elementu obliczana jest jako wartość funkcji skrótu z łańcucha zawierającego sklejone wartości wszystkich pól dokumentu (bez znaczników) oraz dołączonego klucza współdzielonego. Opis w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji).

[Specyfikacja RPAN i RPDN](../notifications/rpan-rpdn.md)

<!-- TODO MIG-068: Źródło: README-2.md: odwołania #pobranie-informacji-o-aktualnej-liście-dostępnych-regulaminów---getlegaldata. Problem: Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. Wymagana decyzja/materiał: Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą. -->

> TODO MIG-068: Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą.
