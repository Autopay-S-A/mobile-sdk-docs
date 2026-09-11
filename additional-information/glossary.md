# Słownik pojęć





**Aplikacja** – Aplikacja Mobilna Partnera, komunikująca się z [SDK
Systemu Płatności Online Autopay](../mobile-sdk/README.md)
w celu rejestrowania Transakcji.

**AP** – Autopay Spółka Akcyjna z siedzibą w Sopocie przy ulicy
Powstańców Warszawy 6, wpisana do rejestru przedsiębiorców prowadzonego
przez Sąd Rejonowy Gdańsk-Północ w Gdańsku, VIII Wydział Gospodarczy
Krajowego Rejestru Sądowego pod numerem KRS 0000320590, NIP
585-13-51-185, Regon 191781561, o kapitale zakładowym w wysokości 2 205
500 PLN (w całości opłaconym), nadzorowana przez Komisję Nadzoru
Finansowego i wpisana do rejestru krajowych instytucji płatniczych pod
numerem IP17/2013, właściciel Systemu.

**ClientHash** - parametr w komunikatach; pozwala w sposób
zanonimizowany przypisać Instrument płatniczy (np. Kartę) do Klienta. Na
jego podstawie Partner może wywoływać kolejne obciążenia w modelu
płatności automatycznych.

**Dzień Roboczy** – dzień tygodnia od poniedziałku do piątku, z
wyłączeniem dni ustawowo wolnych od pracy.

**Instrument Płatniczy** **(Kanał Płatności)** – uzgodniony przez
Klienta i jego dostawcę zbiór procedur lub zindywidualizowane
urządzenie, wykorzystywane przez Klienta do złożenia zlecenia
płatniczego np. Karta, PBL.

**Narzędzie e-przelew** – uzgodniony przez Partnera i AP zbiór procedur
lub zindywidualizowane urządzenie, wykorzystywane przez Partnera do
złożenia zlecenia płatniczego umożliwiającego realizację wypłatę środków
z salda na rachunek bankowy Partnera lub Klienta oraz inny instrument
płatniczy należący do Partnera lub Klienta. Udostępnienie
funkcjonalności jest zależne od indywidualnych ustaleń między Partnerem
i AP.

**IPN** **(Instant Product Notification)** – natychmiastowe
powiadomienie wysyłane z Systemu płatności online do Serwisu Partnera
przekazujące zmianę statusu produktu. Struktura IPN jest podobna do ITN
(rozszerzona jedynie o węzeł *product*).

**ITN** **(Instant Transaction Notification)** – natychmiastowe
powiadomienie wysyłane z Systemu płatności online do Serwisu Partnera
przekazujące zmianę statusu transakcji.

**ISTN (Instant Settlement Transaction Notification)** – natychmiastowe
powiadomienia o zmianie statusu transakcji rozliczeniowej. System
niezwłocznie przekazuje powiadomienia o fakcie zlecenia transakcji
rozliczeniowej (ew. wypłatach/zwrotach) oraz zmianie jej statusu.

**Karta** - karta płatnicza wydana w ramach systemów VISA i Mastercard, dopuszczona
regulacjami tychże systemów do realizacji Transakcji bez fizycznej jej
obecności.

**Klient (Płatnik)** – osoba uiszczająca w Serwisie płatność za usługi
lub produkty Partnera przy wykorzystaniu Systemu.

**Koszyk produktów** – jest to informacja o składowych płatności,
przekazywana (w Linku płatności) do Systemu w celu późniejszego jej
przetwarzania. Każdy produkt koszyka opisują dwa obowiązkowe pola: kwota
składowa oraz pole pozwalające przekazać parametry charakterystyczne dla
produktu.

**Link płatności** – żądanie umożliwiające start Transakcji wejściowej
(opisanej w części [Rozpoczęcie transakcji](../online-payments/payment-flow/start-transaction.md#rozpoczęcie-transakcji)). Można go stosować
bezpośrednio na stronach www (metoda POST), natomiast w mailach do
Klientów należy posłużyć się *Przedtransakcją* w celu uzyskania
krótkiego linka do płatności (metoda GET).

**Model Płatnika** – model, w którym prowizję za przeprowadzoną
transakcję opłaca klient na rzecz AP (koszt doliczany do kwoty
transakcji). W tym przypadku klient podczas płatności akceptuje również
regulamin AP.

**Model Merchanta** - model, w którym prowizja jest rozliczana między
Autopay a partnerem i nie jest doliczana do kwoty transakcji
opłacanej przez klienta.

**Partner** - podmiot będący odbiorcą środków z tytułu sprzedaży
produktów lub usług dystrybuowanych przez Partnera w Serwisie.

**Pay By Link (PBL)** – narzędzie umożliwiające realizację płatności za
pośrednictwem przelewu wewnątrzbankowego z rachunku Klienta na rachunek
AP. Po zalogowaniu do bankowości internetowej - dane potrzebne do
realizacji przelewu (dane informacyjne odbiorcy, numeru jego rachunku
bankowego, kwota i data realizacji przelewu) są wypełnione automatycznie
dzięki systemowi wymiany danych pomiędzy bankiem a AP.

**Pełnomocnik techniczny** – podmiot posiadający prawo dostępu do Rachunku Płatniczego Partnera,
który autoryzuje ten dostęp (zgoda lub umowa). W systemie pełnomocnictwo jest reprezentowane przez
konfigurację **PlenipotentiaryID**: jeden podmiot może mieć wiele pełnomocnictw dla różnych Partnerów.

**Płatność automatyczna** – płatność dokonywana bez potrzeby
każdorazowego wprowadzania danych Karty lub danych do autoryzacji
przelewu.

**Płatność jednym kliknięciem** – jest to Płatność automatyczna zlecana
przez Klienta.

**Płatność cykliczna** - jest to Płatność automatyczna zlecana bez
udziału Klienta (przez Serwis Partnera).

**Przedtransakcja** - specyficzny (wykonywany w tle) sposób zamawiania
linku do płatności.

**Rachunek Płatniczy (Saldo)** – rachunek płatniczy prowadzony przez AP
dla Partnera, na którym gromadzone są środki wpłacone od Klientów.
Udostępnienie funkcjonalności jest zależne od indywidualnych ustaleń
między Partnerem i AP.

**RPAN** **(Recurring Payment Activation Notification)** – komunikat o
aktywowaniu usługi płatności automatycznych.

**RPDN (Recurring Payment Deactivation Notification)** – komunikat o
dezaktywacji usługi płatności automatycznych.

**Serwis** – strona lub strony internetowe Partnera zintegrowane z Systemem, na których Klient może nabyć od Partnera produkty lub usługi.

**Specyfikacja** – dokumentacja opisująca komunikację pomiędzy Serwisem
a Systemem.

**System Płatności Online AP (System)** – rozwiązanie
informatyczno-funkcjonalne, w ramach którego AP udostępnia Partnerowi
aplikację umożliwiającą procesowanie płatności Klientów dokonanych przy
użyciu Instrumentów Płatniczych, a także weryfikację statusu płatności
oraz odbiór płatności.

**Szybki Przelew** – realizacja płatności za pośrednictwem przelewu
wewnątrzbankowego z rachunku Klienta na rachunek AP. Od płatności
dokonywanych za pośrednictwem PBL płatność różni się koniecznością
samodzielnego wypełnienia wszystkich danych potrzebnych do dokonania
przelewu przez Klienta.

**Transakcja** - oznacza transakcję płatniczą w rozumieniu Ustawy z dnia
19 sierpnia 2011 r. o usługach płatniczych.

**Transakcja wejściowa** – część procesu obsługi płatności, dotycząca
wpłaty dokonywanej przez Klienta do AP.

**Transakcja rozliczeniowa** – część procesu obsługi płatności,
dotycząca przelewu wykonywanego przez AP na rachunek Partnera. Aby
powstała Transakcja rozliczeniowa, Transakcja wejściowa musi zostać
przez Klienta opłacona. Transakcja rozliczeniowa może dotyczyć
pojedynczej transakcji wejściowej (wpłaty), bądź agregować ich wiele.

**Ustawa** – ustawa z dn. 19 sierpnia 2011 r. o usługach płatniczych.

**Ważność linku** – parametr określający moment, po przekroczeniu
którego Link płatności przestaje być aktywny. Powinna być ustawiana
przez parametr LinkValidityTime w Linku płatności.

**Ważność transakcji** – parametr określający moment, po przekroczeniu
którego System płatności online blokuje i automatycznie zwraca wpłaty
Klienta. Wartość domyślna obliczana jest poprzez dodanie 6 dni do dnia
wybrania przez Klienta Kanału Płatności. Może ona być również ustawiana
przez parametr ValidityTime w Linku płatności. W takim przypadku, po
upłynięciu wskazanego czasu link przestaje być aktywny, a wpłaty są
zwracane do Klienta. Maksymalna ważność transakcji to 31 dni.

**Widget Autopay** – mechanizm umożliwiający realizacje płatności Kartą za produkty/usługi
oferowane przez Partnera, w której dane Karty wprowadzane są przez
Klienta do mechanizmu osadzonego bezpośrednio w Serwisie Partnera.
Wywołanie formatki kartowej widgetu wymaga implementacji kodu
JavaScript wykorzystującego dedykowaną bibliotekę AP.

**WhiteLabel** – model integracji, w którym Klient już w Serwisie
dokonuje wyboru kanału płatności oraz akceptuje regulaminy (o ile
konieczność ich akceptacji wynika z indywidualnych ustaleń między
Partnerem i AP), a start transakcji zawiera wypełnione pole GatewayID
(oraz w określonych przypadkach DefaultRegulationAcceptanceID lub
RecurringAcceptanceID).

**Rozpoczęcie zlecenia płatniczego** – moment, w którym użytkownik bramki płatniczej dokonuje wyboru kanału
płatności i następuje przekierowanie do strony zgodnej z wybranym kanałem płatności albo (dla płatności automatycznych,
portfeli elektronicznych czy BLIK) następuje próba obciążenia karty lub rachunku u dostawcy kanału płatności.
