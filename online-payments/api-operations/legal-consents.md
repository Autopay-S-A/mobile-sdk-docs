# Regulaminy i zgody

<!-- TODO MIG-030: Źródło: README-2.md:3977,4013. Problem: Opis regulationID używa isCheckboxRequired, tabela i przykład checkboxRequired; w przykładzie JSON brakuje przecinka po inputLabel. Wymagana decyzja/materiał: Potwierdzić nazwę pola i poprawny JSON. -->

> TODO MIG-030: Opis regulationID używa isCheckboxRequired, tabela i przykład checkboxRequired; w przykładzie JSON brakuje przecinka po inputLabel. Potwierdzić nazwę pola i poprawny JSON.

## Odpytywanie o listę aktualnie dostępnych zgód formalnych

### Opis

Opis integracji umożliwiający używanie listy płatności osadzonej w
serwisie (lub aplikacji mobilnej), bez kroków przejściowych. W
niektórych przypadkach zamiast kroku przejściowego, standardowe
zachowanie systemu przewiduje blokadę startu transakcji.

Należy wyświetlić odpowiednie treści formalne (a więc klauzule
informacyjne oraz ew. regulaminy) już w momencie wyboru formy płatności,
a następnie przekazać do Systemu Płatności Online potwierdzenie ich
wyświetlenia oraz ew. akceptacji (w postaci identyfikatorów).

System umożliwia zdalne odpytanie o aktualną listę obowiązków i
powiązanych treści formalnych. W tym celu należy wywołać metodę
**legalData** (*https://{host_bramki}/legalData*) z odpowiednimi
parametrami (w formacie JSON).

**WSKAZÓWKA:** Wszystkie parametry przekazywane są za pomocą technologii
REST. Protokół rozróżnia wielkość liter zarówno w nazwach jak i
wartościach parametrów. Wartości przekazywanych parametrów powinny być
kodowane w UTF-8.

### Lista dostępnych parametrów

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | integer | Identyfikator Serwisu Partnera. |
| 2 | MessageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Wartość pola musi być unikalna dla Serwisu Partnera. |
| 3 | GatewayID | TAK | integer{1,5} | Identyfikator Kanału Płatności, za pomocą, którego Klient zamierza uregulować płatność. |
| 4 | Language | TAK | string{2} | Język, w jakim są prezentowane treści w Serwisie. <BR> Dopuszczalne wartości PL, EN, DE, CS, ES, FR, IT, SK, RO, HU, UK. <BR> Użycie wartości innych niż PL powinno być potwierdzone w trakcie integracji i zależeć od faktycznego wyboru (przez Klienta) języka w Serwisie. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |



Przykładowy komunikat
```json
	{
		"ServiceID": 102422,
		"MessageID": "11111111111111111111111111111111",
		"GatewayID": 1500,
		"Language": "PL",
		"Hash":"61789013d932e2bc728d6206f7e9222b93e3176f7f07f6aa8cce1ccd65afaf0d"
	}
```

### Lista zwracanych parametrów

W odpowiedzi na żądanie zwracana jest (w tej samej sesji HTTP) lista,
zawierająca kolejne treści formalne w postaci: ID, typ i brzmienie
treści, ich umiejscowienie w Serwisie, adres do regulaminu oraz inne
informacje dodatkowe.

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | result | TAK | string{1,5} | Status odpowiedzi. <BR> Dopuszczalne wartości: <BR> - **OK** <BR> - **ERROR** |
| 2 | errorStatus | TAK | string{1,100} | Status błędu, wypełniany w przypadku błędu. W przeciwnym wypadku null. |
| 3 | description | TAK | string{1,500} | Opis błędu, wypełniany w przypadku błędu. W przeciwnym wypadku null. |
| 4 | serviceID | TAK | string{1,10} | Identyfikator Serwisu Partnera; pochodzi z żądania metody. |
| 5 | messageID | TAK | string{32} | Pseudolosowy identyfikator komunikatu o długości 32 znaków alfanumerycznych alfabetu łacińskiego (np. na bazie UID). Pochodzi z żądania metody. |
| 6 | gatewayID | TAK | integer{1,5} | Identyfikator Kanału Płatności, za pomocą którego Klient może uregulować płatność. |
| 7 | language | TAK | string{2} | Język, w jakim System zwraca treści (klauzule i regulaminy). |
| 8 | serviceModel | TAK | string{1,20} | Pole oznaczające model, w którym pracuje serwis, na potrzeby ew. przyszłych wytycznych w oparciu o te wartości (aktualnie o wartościach: MERCHANT, PAYER). W tym momencie powinno być ignorowane. |
| nd. | regulationList | TAK | list | Lista zawierająca treści formalne dostępne dla kanału płatności. |
| 9 | regulationID | TAK | integer{1,10} | Identyfikator treści formalnej, który (w przypadku jego akceptacji przez Klienta) powinien być przekazany w parametrze startowym **DefaultRegulationAcceptanceID**, lub **RecurringAcceptanceID** (odpowiednio dla typu **DEFAULT** i **RECURRING**). <BR> Sposób akceptacji określają pola **showCheckbox** i **isCheckboxRequired**. _**UWAGA:** Ta wartość może się powtarzać dla wywołań o różnych **GatewayID**, gdyż regulaminy są przyporządkowane raczej grupie kanałów płatności, a nie pojedynczym kanałom._ |
| 10 | type | TAK | string{1,64} | Typ obowiązku formalnego. <BR> Przewidziane wartości: <BR> - **DEFAULT** – klauzula (lub klauzule) oraz regulamin płatności w modelu usługi świadczonej przez AP na rzecz Klienta <BR> - **RECURRING** – klauzule (lub klauzule) oraz regulamin płatności automatycznej. Wartość dostępna tylko, jeśli skonfigurowana jest usługa płatności automatycznej <BR> - **PSD2** – klauzula dedykowana kanałom typu PSD2 (w tej chwili wartość nie jest używana) <BR> - **RODO** – klauzula informacyjna dotycząca przetwarzania danych osobowych <BR> - **PRIVACY** – klauzula informacyjna dotycząca polityki prywatności |
| 11 | url | NIE | string{1,500} | Adres do pliku z regulaminem (do samodzielnego osadzenia w Serwisie). Standardowo, jeśli tak stanowi obowiązek formalny, powinien być częścią jednej z jego klauzul, tj. pola **inputLabel**. _**WSKAZÓWKA:** Pojawia się w przypadku wystąpienia dokumentu powiązanego ze zgodą._ |
| nd. | labelList | TAK | list | Lista zawierająca klauzule dostępne dla danego obowiązku formalnego. Obowiązek ten może wymagać wyświetlenia jednej lub więcej treści. |
| 12 | labelID | TAK | integer{1,10} | Identyfikator klauzuli, przekazywane na potrzeby diagnostyczne (może być przez Partnera ignorowany). |
| 13 | inputLabel | TAK | string{1,500} | Treść klauzuli do wyświetlenia w Serwisie w powiązaniu z odpowiednim **regulationID**. W niektórych przypadkach może zawierać link do regulaminu. |
| 14 | placement | NIE | string{1,64} | Informacja, stanowiąca sugestię, gdzie umieścić klauzule. <BR> Aktualne wartości: <BR> - TOP_OF_PAGE – na górze Serwisu (np. w okolicach logo/bannera górnego) <BR> - NEAR_PAYWALL – w okolicach listy kanałów płatności (bezpośrednio nad, pod lub obok) <BR> - ABOVE_BUTTON – nad przyciskiem „Rozpocznij płatność" <BR> - BOTTOM_OF_PAGE – na samym dole strony (zazwyczaj dotyczy klauzul informacyjnych RODO, PRIVACY) |
| 15 | showCheckbox | TAK | boolean | Informacja czy klauzula powinna być wyświetlana obok checkboxa do akceptacji przez użytkownika. |
| 16 | checkboxRequired | TAK | boolean | Informacja, czy wyświetlany Checkbox musi zostać zaznaczony przez użytkownika, aby móc przejść do płatności. _**UWAGA:** W przypadku wartości true, należy zablokować przycisk „Rozpocznij płatność", do czasu zaznaczenia checkboxa._ |
| nd. | hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis Partnera**. |


Przykładowa odpowiedź
```json
	{
		"serviceID": "102422",
		"messageID": "11111111111111111111111111111111",
		"gatewayID": "1500",
		"language": "PL",
		"serviceModel": "PAYER",
		"regulationList": [
			{
				"regulationID": 6288,
				"type": "RECURRING",
				"url": "https://host/path?params",
				"labelList": [
					{
						"labelID": 1,
						"inputLabel": "<ul><li>\r\nZapoznałem się i akceptuję <a id=\"regulations_pdf\" target=\"_blank\" href=https://{host_bramki}/path?params>Regulamin świadczenia usług płatniczych</a> oraz <a class=\"privacy-policy\" href=\"https://{host_bramki}/polityka-prywatnosci.pdf\" target=\"_blank\">Politykę prywatności</a></li><li>\r\nChcę aby usługa została zrealizowana niezwłocznie, a w przypadku odstąpienia od umowy, wiem, że nie otrzymam zwrotu poniesionych kosztów za usługi zrealizowane na moje żądanie do chwili odstąpienia od umowy\r\n</li></ul>",
						"placement": "ABOVE_BUTTON",
						"showCheckbox": true,
						"checkboxRequired": true
					}
				]
			},
			{
				"regulationID": 1,
				"type": "PRIVACY",
				"labelList": [
					{
						"labelID": 1,
						"inputLabel": "Autopay korzysta z plików cookie. Pozostając na tej stronie, wyrażasz zgodę na korzystanie z plików cookie zgodnie z <a class=\"privacy-policy\" href=\"https://{host_bramki}/polityka-prywatnosci.pdf\" target=\"_blank\">Polityką prywatności Autopay S.A. </a> Możesz samodzielnie zarządzać cookies zmieniając odpowiednio ustawienia swojej przeglądarki lub oprogramowania urządzenia."
						"placement": "BOTTOM_OF_PAGE",
						"showCheckbox": false,
						"checkboxRequired": false
					}
				]
			}
		],
		"hash": "61789013d932e2bc728d6206f7e9222b93e3176f7f07f6aa8cce1ccd65afaf0d",
		"result": "OK",
		"errorStatus": null,
		"description": null
	}
```

### Opis obsługi odpowiedzi

Ponieważ wymogi formalne dotyczące treści klauzul, ich rozmieszczenie
oraz sposób akceptacji zależą od stosowanego kanału płatniczego, metoda
ta powinna być wywoływana każdorazowo po jego wyborze (stąd obowiązkowy
parametr **GatewayID**).

Odpowiednie treści i zachowanie powinny być dynamicznie dostosowywane do
odpowiedzi z Systemu (np. powinien pojawić się wymagany checkbox z
klauzulą informacyjną oraz linkiem do regulaminu). Oczywiście, aby
aplikacja działała szybko, mile widziane jest używanie cache do
zapamiętywania odpowiedzi niedawno wykonanych wywołań (np. na 1 minutę).

Wyświetlona (i ew. zatwierdzona) w momencie przejścia do płatności
zgoda, powinna być potwierdzona w Systemie poprzez dołączenie do
komunikatu startu transakcji w parametrze startowym jej identyfikatora
(a więc odpowiednią wartość **regulationID**).

W zależności od wartości pola **type** regulaminu:

\- dla wyświetlanej/akceptowanej klauzuli o **type=DEFAULT**:

> a\. do parametru **DefaultRegulationAcceptanceID** powinna trafiać jej
> wartość **regulationID**;
>
> b\. do parametru **DefaultRegulationAcceptanceState** powinna trafić
> wartość **ACCEPTED** oraz
>
> c\. do parametru **DefaultRegulationAcceptanceTime** powinna trafić
> wartość odpowiadająca chwili akceptacji zgody poprzez zaznaczenie
> checkboxa oraz kliknięcie przycisku „Rozpocznij płatność"

\- dla wyświetlanej/akceptowanej klauzuli o **type=RECURRING**:

> a\. do parametru **RecurringAcceptanceID** powinna trafiać jej wartość
> **regulationID**;
>
> b\. do parametru **RecurringAcceptanceState** powinna trafić wartość
> **ACCEPTED** oraz
>
> c\. do parametru **RecurringAcceptanceTime** powinna trafić wartość
> odpowiadająca chwili akceptacji zgody poprzez zaznaczenie checkboxa
> oraz kliknięcie przycisku „Rozpocznij płatność"

**UWAGA:** Pola (np. **serviceModel, url, labelID**) i wartości pól (np.
**PSD2, RODO, PRIVACY**) metody **legalData** nie są wymagane do
obsługi, ale należy przewidzieć możliwość ich występowania w odpowiedzi
na żądanie.
