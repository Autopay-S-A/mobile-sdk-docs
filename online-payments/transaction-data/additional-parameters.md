# Dodatkowe parametry



### Weryfikacja tożsamości płatnika

#### Opis weryfikacji tożsamości płatnika

Dokonanie weryfikacji tożsamości płatnika może dobywać się na zasadzie
porównania danych przekazanych do weryfikacji w parametrach startu
transakcji oraz danych uzyskanych z wpłaty zarejestrowanej w Systemie.
Jeśli w trakcie integracji zostanie uzgodniona opcja weryfikacji danych
oraz w starcie transakcji zostaną podane zadeklarowane przez klienta
dane do weryfikacji, System dokona sprawdzenia prawdziwości tych danych
i jego wynik umieści w komunikacie ITN obok danych nadawcy przelewu. Dla
większości transakcji typu PBL, pierwszy komunikat ITN nie będzie
zawierać danych adresowych (nazw, adres) klienta. Dane te są uzupełniane
w Systemie po zeskanowaniu historii rachunku AP danego kanału płatności.
Uzupełnienie tych danych skutkuje wysłaniem kolejnego komunikatu ITN do
Partnera, zawierającego już te dane.

**TIP:** W przypadku wykrycia współwłasności rachunku, czyli obecności danych dwóch osób, System w polach `fName` i `lName` umieszcza dane pierwszej osoby w kolejności występowania. Pełne, nieprzetworzone dane nadawcy, zawierające wszystkie wykryte imiona i nazwiska, są dostępne w polu `senderData`.

Pola w komunikacie startu transakcji powiązane z usługą (opis w części
[Rozpoczęcie transakcji z dodatkowymi parametrami](#rozpoczęcie-transakcji-z-dodatkowymi-parametrami)):

-   **VerificationFName,**

-   **VerificationLName,**

-   **VerificationStreet,**

-   **VerificationStreetHouseNo,**

-   **VerificationStreetStaircaseNo,**

-   **VerificationStreetPremiseNo,**

-   **VerificationPostalCode,**

-   **VerificationCity,**

-   **VerificationNRB**

Pola w komunikacie ITN powiązane z usługą (opisane w części [Dodatkowe pola w komunikacie ITN/IPN transakcji wejściowej](../notifications/itn.md#dodatkowe-pola-w-komunikacie-itnipn-transakcji-wejściowej)):

-   węzeł **customerData**

-   **verificationStatus**

-   węzeł **verificationStatusReasons**

**WSKAZÓWKA:** Szczegółowe instrukcje, jak interpretować statusy
płatności i weryfikacji w tym procesie zawiera część [Dodatkowe pola w komunikacie ITN/IPN transakcji wejściowej](../notifications/itn.md#dodatkowe-pola-w-komunikacie-itnipn-transakcji-wejściowej).


## Rozpoczęcie transakcji z dodatkowymi parametrami

Starty transakcji mogą zostać przeprowadzone z dodatkowymi parametrami rozpisanymi w poniższych punktach.

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | typ | opis |
| --- | --- | --- | --- |
| 8 | Language | string{1,2} | Wybór języka, w jakim będą prezentowane treści w Sytemie. <BR> Dopuszczalne wartości to: PL, EN, DE, CS, ES, FR, IT. <BR> Użycie innych wartości niż PL powinno być potwierdzone w trakcie integracji i powinno zależeć od faktycznego wyboru języka w Serwisie przez Klienta. |
| 9 | CustomerNRB | string{26} | Numer rachunku Klienta, parametr przeznaczony wyłącznie dla Serwisów Partnera generujących dedykowane numery rachunków dla zamówienia lub Klienta (patrz [Model rozliczeń transakcji po każdej wpłacie](../api-operations/balance.md#model-rozliczeń-transakcji-po-każdej-wpłacie)). <BR> Dopuszczalne tylko cyfry. Jeśli w trakcie integracji ustalono wykorzystanie rachunków spoza Polski, wtedy pole przenosi IBAN i oczekiwany zakres danych pola zmienia się na: alfanumeryczne znaki alfabetu łacińskiego (min. 15, maks. 32 znaki). |
| 10 | SwiftCode | string{8,11} | Kod swift odpowiadający podanemu numerowi rachunku. <BR> Dopuszczalne tylko cyfry. Parametr podawany, jeśli w trakcie integracji ustalono wykorzystanie rachunków spoza Polski. |
| 11 | ForeignTransferMode | string{4,5} | System jakim ma zostać wykonany zagraniczny przelew rozliczeniowy: <BR> SEPA (Single Euro Payments Area) - możliwy do wykonania przelewu w walucie Euro w obrębie państw członkowskich Unii Europejskiej, jak i innych państwach na terenie Starego Kontynentu, np. Islandii, Liechtensteinu, Norwegii, Szwajcarii, Monako czy Andory, <BR> SWIFT - przelewy zagraniczne niemożliwe do wykonania za pomocą SEPA (np. inna waluta niż Euro), wiąże się z wyższymi kosztami wykonania przelewu, niż w przypadku SEPA. <BR> <BR> _Dopuszczalne wartości: SEPA i SWIFT. <BR> Parametr podawany, jeśli w trakcie integracji ustalono wykorzystanie rachunków spoza Polski._ |
| 12 | TaxCountry | string{1,64} | Kraj zamieszkania płatnika. |
| 13 | CustomerIP | string{1,39} | Adres IP użytkownika, parametr przeznaczony wyłącznie dla Serwisów Partnera uruchamiających System w tle (patrz [Przedtransakcja](../advanced-flows/pretransaction.md#przedtransakcja) oraz [Zamówienie danych do przelewu w transakcji typu Szybki Przelew](../payment-methods/pay-by-link.md#zamówienie-danych-do-przelewu-w-transakcji-typu-szybki-przelew)). |
| 14 | Title | string{1,95} | Tytuł przelewu rozliczającego transakcję, parametr przeznaczony wyłącznie dla Serwisów Partnera rozliczanych przelewem po każdej wpłacie (patrz [Model rozliczeń transakcji po każdej wpłacie](../api-operations/balance.md#model-rozliczeń-transakcji-po-każdej-wpłacie)). W niektórych przypadkach, niezależnych od AP tytuł przelewu rozliczeniowego może zostać samodzielnie zmodyfikowany przez Bank, z którego nastąpiło rozliczenie. <BR> _Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: `ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!()\"`, gdzie znak "/" będzie podmieniany na "-" dla transakcji wychodzących._ |
| 15 | ReceiverName | string{1,35} | Nazwa odbiorcy przelewu rozliczającego transakcję, parametr przeznaczony wyłącznie dla Serwisów Partnera rozliczanych przelewem po każdej wpłacie (patrz [Model rozliczeń transakcji po każdej wpłacie](../api-operations/balance.md#model-rozliczeń-transakcji-po-każdej-wpłacie)). <BR> _Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!()=\[\]{};:?_ |
| 16 | Products | string{1,10000} | Informacje o produktach wchodzących w skład transakcji, przekazywany w postaci zakodowanego protokołem transportowym Base64 XMLa. <BR> Opis struktury w części [Koszyk produktów](product-basket.md#koszyk-produktów). |
| 17 | CustomerPhone | string{9-15} | Numer telefonu użytkownika. <BR> _Dopuszczalne tylko cyfry._ |
| 18 | CustomerPesel | string{11} | Numer PESEL użytkownika. <BR> _Dopuszczalne tylko cyfry._ |
| 20 | CustomerNumber | string{1,35} | Numer Klienta w Serwisie. |
| 21 | InvoiceNumber | string{1,100} | Numer dokumentu finansowego w Serwisie. |
| 22 | CompanyName | string{1,150} | Nazwa firmy do automatycznej weryfikacji wpłacającego, np. Firma Fajna. |
| 23 | Nip | string{1,10} | Numer identyfikacyjny NIP weryfikowanej firmy, np. 5851351185. <BR> _Dopuszczalne tylko cyfry._ |
| 24 | Regon | string{9,14} | Numer identyfikacyjny REGON weryfikowanej firmy, np. 191781561. <BR> _Dopuszczalne tylko cyfry._ |
| 25 | VerificationFName | string{1,32} | Imię podane w Serwisie do automatycznej weryfikacji wpłacającego, np. Jan. <BR> Dopuszczalne tylko litery alfabetu polskiego. |
| 26 | VerificationLName | string{1,64} | Nazwisko podane w Serwisie do automatycznej weryfikacji wpłacającego, np. Kowalski. <BR> Dopuszczalne tylko litery alfabetu polskiego. |
| 27 | VerificationStreet | string{1,64} | Ulica podana w Serwisie do automatycznej weryfikacji, np. Długa. <BR> Dopuszczalne tylko litery alfabetu polskiego oraz cyfry. |
| 28 | VerificationStreetHouseNo | string{1,64} | Numer domu podany w Serwisie do automatycznej weryfikacji wpłacającego. <BR> Dopuszczalne tylko litery alfabetu polskiego oraz cyfry. |
| 29 | VerificationStreetStaircaseNo | string{1,64} | Numer klatki podany w Serwisie do automatycznej weryfikacji wpłacającego. <BR> Dopuszczalne tylko litery alfabetu polskiego oraz cyfry. |
| 30 | VerificationStreetPremiseNo | string{1,64} | Numer lokalu podany w Serwisie do automatycznej weryfikacji wpłacającego. <BR> Dopuszczalne tylko litery alfabetu polskiego oraz cyfry. |
| 31 | VerificationPostalCode | string{1,64} | Kod pocztowy podany w Serwisie do automatycznej weryfikacji wpłacającego, format XX-XXX, np. 80-180. <BR> Dopuszczalne tylko cyfry oraz znak -. |
| 32 | VerificationCity | string{1,64} | Miasto podane w Serwisie do automatycznej weryfikacji wpłacającego, np. Warszawa. <BR> Dopuszczalne tylko litery alfabetu polskiego oraz cyfry. |
| 33 | VerificationNRB | string{1,26} | Numer rachunku bankowego podany w Serwisie do automatycznej weryfikacji wpłacającego, np. 88154010982001554242710005. <BR> Dopuszczalne tylko cyfry. |
| 35 | RecurringAcceptanceState | string{1,100} | Informacja o akceptacji regulaminu płatności automatycznej określająca, czy Klient zaakceptował regulamin płatności automatycznej, czy należy wymusić jego akceptację po stronie Systemu. Pole wymagane dla płatności automatycznych w modelu WhiteLabel usługi płatniczej świadczonej przez AP na rzecz Klienta (Klient płaci prowizję). Dostępność regulaminu można sprawdzić wywołując metodę legalData. <BR> Dozwolone wartości: <BR> **NOT_APPLICABLE** - akceptacja regulaminu niewymagana (płatność jednorazowa lub akcja obciążenia, tj. recurringAction o wartości AUTO lub MANUAL) <BR> **ACCEPTED** – deklaracja akceptacji regulaminu w serwisie kontrahenta (należy podać wraz z **RecurringAcceptanceID**) <BR> **PROMPT** – na formularzu kartowym jest proponowana zgoda na zapisanie karty, jej zaznaczenie rozpoczyna płatność automatyczną <BR> **FORCE** – w formularzu kartowym jest wymagana zgoda na zapisanie karty, inaczej płatność jest niemożliwa. _**UWAGA:** Dostępność opcji **ACCEPTED/PROMPT/FORCE** zależy od uzgodnień biznesowych (w szczególności ustalenia miejsca wyświetlania zgody/regulaminu usługi płatności automatycznej)._ |
| 36 | RecurringAction | string{1,100} | Pole wymagane dla płatności automatycznych, określające możliwe akcje na płatności automatycznej. <BR> Dozwolone wartości: <BR> INIT_WITH_PAYMENT - aktywacja płatności automatycznej wraz z opłatą za towar/usługę <BR> INIT_WITH_REFUND - aktywacja płatności automatycznej, a następnie zwrot wpłaty <BR> INIT_WITHOUT_PAYMENT - aktywacja płatności automatycznej bez opłaty za towar/usługę. Kwota startowa płatności to 0.00. <BR> AUTO - płatność cykliczna (obciążenie bez udziału Klienta) <BR> MANUAL - płatność jednym kliknięciem (obciążenie zlecane przez Klienta) _**UWAGA:** Opcja niedostępna dla płatności automatycznych BLIK (BLIK OneClick)._ <BR> DEACTIVATE – dezaktywacja płatności automatycznej |
| 37 | ClientHash | string{1,64} | Identyfikator płatności automatycznej. Parametr pozwala w sposób zanonimizowany przypisać Instrument płatniczy (np. Kartę, BLIK) do Klienta. Na jego podstawie Partner może wywoływać kolejne obciążenia w modelu płatności automatycznych. |
| 38 | OperatorName | string{1,35} | Nazwa operatora podanego numeru telefonu. <BR> _Dopuszczalne wartości: Plus, Play, Orange, T-Mobile._ |
| 39 | ICCID | string{12,19} | Numer karty SIM podanego numeru telefonu. <BR> Dozwolone wartości (dopuszczalne tylko cyfry): <BR> Dla Plus: 12 lub 13 cyfr <BR> Dla Play, Orange, T-Mobile: 19 cyfr |
| 40 | AuthorizationCode | string{6} | Kod autoryzacji płatności wprowadzany po stronie Serwisu/Systemu (obecnie obsługiwany w BLIK). Jego zastosowanie powoduje, że nie ma potrzeby przekierowania Klienta na stronę Kanału Płatności. Należy zatem podawać go jedynie poprzez Przedtransakcję. <BR> Format zależny od Kanału Płatności. Dla BLIK realizowanego w tle (BLIK 0, ew. BLIK OneClick): 6 cyfr. |
| 41 | ScreenType | string{4,6} | Rodzaj widoku formatki autoryzującej płatność. Dopuszczalne wartości: <BR> IFRAME - niewspierany <BR> FULL. |
| 42 | BlikUIDKey | string{1,64} | Klucz Aliasu UID (używane w BLIK). Jest to unikalny identyfikator użytkownika w Serwisie. <BR> _Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki: \_._ |
| 43 | BlikUIDLabel | string{1,20} | Etykieta Aliasu UID (używane w BLIK), która będzie prezentowana Klientowi w aplikacji bankowej w celu rozróżniania kont u Partnera. Zaleca się stosowanie loginu, nicka lub adresu mailowego przypisanego do zautoryzowanego konta Klienta. W przypadku możliwości wystąpienia w polu danych osobowych (np. adres mailowy jan.kowalski\@poczta.pl) należy wykonać utajnienie danych (poprzez zastąpienie 3 kropkami niektórych znaków, np. ja\...ki\@po\...pl). <BR> _Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: . : @ - , spacja._ |
| 44 | BlikAMKey | string{1,64} | Klucz Aliasu aplikacji mobilnej banku (używane w BLIK). Jest to unikalny identyfikator konta w BLIK. <BR> _Dopuszczalne cyfry._ |
| 45 | ReturnURL | string{1,1000} | Dynamiczny adres powrotu z płatności zaczynający się od http/https. <BR> _Dopuszczalne poprawne URL. Może zawierać IP, port, subdomenę, polskie znaki, a także (po domenie) parametry i znaki specjalne: ,\'\\+&;%\$#\_!=._ |
| 46 | TransactionSettlementMode | string{2,10} | Możliwość zmiany sposobu rozliczania transakcji. Brak parametru (kompatybilność wstecz) traktowana, jak przesłanie wartości COMMON. <BR> Parametr NONE powoduje potraktowanie transakcji jako zasilenia salda przedpłaconego i brak rozliczenia. <BR> _Dopuszczalne wartości: <BR> COMMON <BR> NONE_ |
| 47 | PaymentToken | string{1,100000} | Token używany w portfelach Visa oraz Google Pay umieszczanych bezpośrednio na stronie Partnera (autoryzacja bez przekierowania do Systemu). W tym wypadku Serwis integruje się bezpośrednio z API Visy i/lub Google w celu pobrania uchwytu do karty. Uzyskany token jest przekazywany do Systemu Płatności Online w postaci zakodowanej protokołem transportowym Base64. _**UWAGA:** Parametr jest zbędny, jeśli wybór Kanałów Płatności (oraz logowanie do portfela) odbywa się bezpośrednio na stronie Systemu Płatności Online._ |
| 48 | DocNumber | string{1,150} | Numer dokumentu finansowego. |
| 49 | RecurringAcceptanceID | string{1,10} | Identyfikator wyświetlanego w Serwisie i akceptowanego przez Klienta regulaminu usługi płatności automatycznej. Pole wymagane dla płatności automatycznych w modelu **WhiteLabel** usługi płatniczej świadczonej przez AP na rzecz Klienta (Klient płaci prowizję). ID regulaminu odpowiedniego dla wybranego języka (i kanału płatności) należy pobrać za pomocą metody **legalData**. |
| 50 | RecurringAcceptanceTime | string{1,19} | Pole opcjonalne. Moment akceptacji regulaminu przez Klienta, ta wartość będzie weryfikowana przez System z czasem obowiązywania regulaminu o podanym **RecurringAcceptanceID**. <BR> Przykładowa wartość: 2014-10-30 07:54:50. (Czas w CET) |
| 51 | DefaultRegulationAcceptanceState | string{1,100} | Informacja o akceptacji regulaminu usługi płatniczej. Pole wymagane w modelu **WhiteLabel** usługi płatniczej świadczonej przez AP na rzecz Klienta (Klient płaci prowizję). Jego niepodanie może wiązać się z błędem lub wyświetleniem strony przejściowej Systemu z wymaganiem akceptacji regulaminów. Dostępność regulaminu można sprawdzić wywołując metodę **legalData**. <BR> Dozwolone wartości: <BR> **ACCEPTED** - akceptacja regulaminu wykonana w serwisie kontrahenta (należy podać wraz z **DefaultRegulationAcceptanceID**). |
| 52 | DefaultRegulationAcceptanceID | string{1,10} | Identyfikator wyświetlanego w Serwisie i akceptowanego przez Klienta regulaminu usługi płatniczej świadczonej przez AP na rzecz Klienta. Pole wymagane w modelu WhiteLabel usługi płatniczej świadczonej przez AP na rzecz Klienta (Klient płaci prowizję). ID regulaminu odpowiedniego dla wybranego języka (i kanału płatności) należy pobrać za pomocą metody legalData. |
| 53 | DefaultRegulationAcceptanceTime | string{1,19} | Pole opcjonalne. Moment akceptacji regulaminu przez Klienta, ta wartość będzie weryfikowana przez System z czasem obowiązywania regulaminu o podanym DefaultRegulationAcceptanceID; przykładowa wartość: 2014-10-30 07:54:50. (Czas w CET) |
| 54 | WalletType | string{1,32} | Typ portfela płatniczego, określa źródło parametru PaymentToken (jeżeli został przesłany). <BR> _Dostępne wartości to: <BR> SDK_NATIVE – natywna formatka kartowa (SDK mobilne) <BR> WIDGET – widget kartowy (formatka kartowa przed startem transakcji) _ |
| 55 | RecurringValidityTime | string{10} | Data ważności aktywowanej płatności automatycznej BLIK, format YYYY-MM-DD (przykładowa wartość: 2024-01-30). W przypadku braku parametru, zostanie zaproponowany termin domyślny konfiguracji ustalany w trakcie integracji (zwyczajowo bezterminowy). |
| 56 | ServiceURL | string{1,1000} | Parametr określający adres www sklepu, z którego została wystartowana płatność, zaczynający się od http/https. Dopuszczalne poprawne URL. |
| 57 | MCC | string{4,4} | Merchant Category Code - czterocyfrowy kod klasyfikujący branżę sprzedawcy. Powinien zostać przekazany przez integratorów płatności w przypadku kiedy transakcja jest realizowana na rzecz innego akceptanta. |
| 58 | BlikPPLabel | string{1,35} | Etykieta Płatności Powtarzalnej BLIK, wyświetlana w aplikacji mobilnej podczas jej akceptowania. W przypadku braku parametru, zostanie użyta jej wartość domyślna (ustalana w trakcie integracji). |
| 59 | BlikPPRefuseNotSupported | string{4,5} | Parametr Płatności Powtarzalnej BLIK, ustawiany w przypadku, gdy biznesowym celem transakcji, poza samą płatnością za towar czy usługę, jest uruchomienie płatności automatycznej. Używany wspólnie z `RecurringAction`=`INIT_WITH_PAYMENT`. Jeżeli ustawimy `true` a bank nie obsługuje płatności rekurencyjnych - transakcja zostanie przerwana. |
| 60 | ReceiverNameForFront | string{1,35} | Nazwa odbiorcy płatności, wyświetlana m.in. na paywallu czy w aplikacji mobilnej podczas jej akceptowania. W przypadku braku parametru, zostanie użyta jej wartość domyślna (zwyczajowo adres URL Serwisu). _**UWAGA:** Usługa musi zostać uzgodnienia z opiekunem biznesowym. Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego, znaki z zakresu: ĘęÓóĄąŚśŁłŻżŹźĆćŃń-/,!()=[]{};:.? oraz spacja._ |
| 61 | AccountHolderName | string{1,100} | Nazwa właściciela środka płatniczego. |
| 62 | UserAgent | string{1,150} | User Agent przeglądarki internetowej, z której korzysta Użytkownik. Parametr przeznaczony wyłącznie dla Serwisów Partnera uruchamiających System w tle. |

[Statusy weryfikacji](statuses.md)
