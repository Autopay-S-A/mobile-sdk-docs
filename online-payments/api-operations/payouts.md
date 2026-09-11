# Wypłaty

<!-- TODO MIG-036: Źródło: README-2.md:4191–4258. Problem: Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i reguły klucza pełnomocnika. Zachowano kontrakt bez usuwania pól. Wymagana decyzja/materiał: Potwierdzić samodzielny wariant dla ServiceID przed uznaniem tej części za gotową specyfikację. -->

> TODO MIG-036: Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i reguły klucza pełnomocnika. Zachowano kontrakt bez usuwania pól. Potwierdzić samodzielny wariant dla ServiceID przed uznaniem tej części za gotową specyfikację.

## Wypłaty z salda

### Opis

Dla serwisów posiadających saldo w Systemie, możliwe jest wykonanie
operacji wypłaty całości bądź części salda na zdefiniowany rachunek do
rozliczeń. W tym celu należy wywołać metodę *balancePayoff*
([https://{host_bramki}/settlementapi/balancePayoff](https://{host_bramki}/settlementapi/balancePayoff)) z odpowiednimi
parametrami.

Wszystkie parametry przekazywane są metodą POST (Content-Type:
application/x-www-form-urlencoded). Protokół rozróżnia wielkość liter
zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych
parametrów powinny być kodowane w UTF-8.


### Lista dostępnych parametrów dla wypłat z salda

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

**UWAGA:** Wymagane jedno z pól **ServiceID** lub **BalancePointID**. <BR> Podanie obu spowoduje zatrzymanie przetwarzania żądania oraz błąd http.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 1 | BalancePointID | TAK | string{1,10} | Identyfikator Punktu Rozliczeń. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID), wartość pola musi być unikalna i wskazywać konkretne zlecenie wypłaty w Serwisie Partnera. Weryfikacja unikalności po stronie Systemu pozwala na ponawianie MessageID w przypadku problemów z komunikacją (ponowienie tej wartości skutkować będzie potwierdzeniem zlecenia, bez ponownego wykonania w Systemie). |
| 3 | Amount | NIE | amount | Kwota wypłaty z salda (nie może być większa niż aktualne saldo serwisu); niepodanie tego parametru skutkuje wypłatą całości środków zgromadzonych na saldzie; jako separator dziesiętny używana jest kropka - \'.\' Format: 0.00. |
| 4 | Currency | NIE | string{1,3} | Waluta wypłaty. Domyślną walutą jest PLN (użycie innej waluty musi być uzgodnione w trakcie integracji). W ramach ServiceID obsługiwana jest jedna waluta. <BR> Dopuszczalne jedynie wartości: PLN, EUR, GBP oraz USD. |
| 5 | CustomerNRB | NIE | string{26} | Numer rachunku, na który ma być wykonana wypłata z salda. Domyślnie konfiguracja wypłat nie zezwala na definiowanie tej wartości w żądaniu metody **balancePayoff**. Należy takie zapotrzebowanie zgłosić w trakcie integracji. _**UWAGA:** W niektórych modelach użycie tego pola może następować jedynie w przypadku żądań z listy zaufanych IP oraz zastosowaniu jednego z 3 dodatkowych elementów: <BR> - zabezpieczenie komunikacji certyfikatem klienckim lub <BR> - zabezpieczenie komunikacji tunelem IPSec lub <BR> - użycie wartości parametru **CustomerNRB** z listy zaufanych rachunków <BR> Niespełnienie ich kończy się błędem CUSTOMER_NRB_NOT_AUTHORIZED._ <BR> Administratorzy panelu Systemu (https://portal.autopay.eu/admin) mogą samodzielnie aktualizować listy zaufanych IP i NRB w zakładce **Kontrola dostępu** konfiguracji serwisu. Dopuszczalne tylko cyfry. Jeśli w trakcie integracji ustalono wykorzystanie rachunków spoza Polski, wtedy pole przenosi IBAN i oczekiwany zakres danych pola zmienia się na: alfanumeryczne znaki alfabetu łacińskiego (min. 15, max. 32 znaki). |
| 6 | SwiftCode | NIE | string{8,11} | Kod swift odpowiadający podanemu numerowi rachunku. <BR> Dopuszczalne tylko cyfry. Parametr podawany, jeśli w trakcie integracji ustalono wykorzystanie rachunków spoza Polski. |
| 7 | ForeignTransferMode | NIE | string{4,5} | System jakim ma zostać wykonany zagraniczny przelew rozliczeniowy: <BR> - SEPA (Single Euro Payments Area) - możliwy do wykonania przelewu w walucie Euro w obrębie państw członkowskich Unii Europejskiej, jak i innych państwach na terenie Starego Kontynentu, np. Islandii, Liechtensteinu, Norwegii, Szwajcarii, Monako czy Andory, <BR> - SWIFT – przelewy zagraniczne niemożliwe do wykonania za pomocą SEPA (np. inna waluta niż Euro. Opcja ta wiąże się z wyższymi kosztami wykonania przelewu niż w przypadku SEPA. <BR> Dopuszczalne wartości: **SEPA** i **SWIFT**. <BR> Parametr podawany, jeśli w trakcie integracji ustalono wykorzystanie rachunków spoza Polski. |
| 8 | ReceiverName | NIE | string{35} | Nazwa odbiorcy wypłaty z salda. Domyślnie konfiguracja wypłat nie zezwala na definiowanie tej wartości w żądaniu metody **balancePayoff**. <BR> Należy takie zapotrzebowanie zgłosić w trakcie integracji. <BR> Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: <BR> *ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!()=\[\]{};:?* |
| 9 | Title | NIE | string{32} | Tytuł wypłaty z salda. Domyślnie konfiguracja wypłat nie zezwala na definiowanie tej wartości w żądaniu metody **balancePayoff**. Należy takie zapotrzebowanie zgłosić w trakcie integracji. <BR> W niektórych przypadkach, niezależnych od AP, ten tytuł może zostać samodzielnie zmodyfikowany przez Bank. <BR> Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego oraz znaki z zakresu: <BR> *ĘęÓóĄąŚśŁłŻżŹźĆćŃń\\s.-/,!()\"*, gdzie znak "/" będzie podmieniany na "-" dla transakcji wychodzących. |
| 10 | RemoteRefID | NIE | string{1,20} | Alfanumeryczny identyfikator transakcji wejściowej nadany przez System oraz przekazywany do Partnera w komunikacie ITN transakcji wejściowej. Wartość w tym komunikacie służy wskazaniu instrumentu płatniczego (karty, rachunku etc.), który ma zostać użyty do wykonania wypłaty. |
| 11 | InvoiceNumber | NIE | string{1,100} | Numer dokumentu finansowego w Serwisie. W tym komunikacie wartość służy wskazaniu faktury korygującej powiązanej z wypłatą. |
| 12 | PlenipotentiaryID | NIE | string{8,8} | Identyfikator pełnomocnika. Jeżeli jest obecny to do obliczania Hash jest używany klucz współdzielony pełnomocnika, a nie główny klucz serwisu/punktu rozliczeń. Wpływa też na Hash w odpowiedzi na ten komunikat. _**WAŻNE!** Użycie tego pola wymaga specjalnych uzgodnień biznesowych._ |
| nd. | Hash | TAK | string{1,128} | Stosowany jest klucz współdzielony przypisany do zastosowanego identyfikatora konfiguracji (Serwisu lub Punktu Rozliczeń). Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


### Odpowiedź na żądanie

Po otrzymaniu żądania wypłaty z salda System wykonuje wstępną weryfikację pod kątem przesłanych w komunikacie pól i ich wartości oraz zapisuje zlecenie do wykonania. W odpowiedzi na żądanie zwracany jest (w tej samej sesji HTTP) tekst w formacie XML, zawierający potwierdzenie zapisania zlecenia w kolejce do wykonania lub opis błędu(struktura komunikatu błędu opisana w części [Komunikaty błędu](../../additional-information/errors.md#komunikaty-błędu).

**UWAGA:** Potwierdzenie przyjęcia zlecania nie jest równoznaczne z faktycznym jego wykonaniem. Wypłata z salda może być realizowana do 30 minut od momentu wysyłki żądania wypłaty i nie zawsze może się zakończyć sukcesem. W przypadku problemów napotkanych podczas procesowania wypłaty z salda (np. niewystarczające środki na saldzie) następnego dnia roboczego wysyłany jest raport zawierający informację o wypłatach z salda, które nie doszły do skutku.


Struktura potwierdzenia (XML)
```xml
	<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
	<balancePayoff>
		<serviceID>ServiceID</serviceID>
		<messageID>MessageID</messageID>
		<hash>Hash</hash>
	</balancePayoff>
```

## Opis pól

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. Pochodzi z żądania metody. |
| 1 | balancePointID | TAK | string{1,10} | Identyfikator Punktu Rozliczeń. Pochodzi z żądania metody. _**UWAGA:** Zwrócone zostanie jedno z pól **ServiceID** lub **BalancePointID**._ |
| 2 | messageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |

**UWAGA:**
Za błąd uznać można wszystkie odpowiedzi inne niż założona (tj. z niepoprawnymi polami, w szczególności pustym lub niepoprawnym **hash**). W przypadku, gdy System zautoryzuje nadawcę komunikatu zwrotu, jednak nie uda się wykonać operacji, odpowiedź będzie zgodna ze schematem opisanym w części [Komunikaty błędu](../../additional-information/errors.md#komunikaty-błędu). W przypadku błędu komunikacji lub braku wystarczającego salda (np. ON_DEMAND_ERROR) można ponowić zlecenie. W przypadku blokady salda (BALANCE_DISABLED) lub nieaktywnej konfiguracji (PARTNER_DISABLED) nie należy ponawiać zlecenia.

**WAŻNE!** W przypadku błędu zwróconego w odpowiedzi na żądanie wypłaty z salda można ponowić zlecenie, należy jednak pamiętać o tym, że każde zlecenie wypłaty nie kończące się błędem może zostać wykonane. W przypadku problemów z połączeniem, przekroczenia maksymalnego czasu oczekiwania na odpowiedź, żądanie może zostać ponowione z tym samym MessageID bez obawy o zduplikowanie zlecenia. <br>
W przypadku blokady salda (BALANCE_DISABLED) lub nieaktywnej konfiguracji (PARTNER_DISABLED) nie należy ponawiać zlecenia. 3 błędy pod rząd (bez względu na przyczynę) powodują blokadę usługi wypłat na żądanie dla określonego IP nadawcy komunikatu na 10 minut – wywołania w tym czasie dla tego IP będą kończyć się błędem TEMPORARY_DISABLED.

[Status zwrotu lub wypłaty z salda – outDetails/v2](refunds.md#odpytanie-o-status-zwrotu-lub-wypłaty-z-salda)
