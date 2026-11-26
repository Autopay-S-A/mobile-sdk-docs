# Raport migracji dokumentacji

Migracja struktury na podstawie DOCS_MIGRATION.md i README-2.md. Nie publikowano dokumentacji. Strony z TODO kontraktu wymagają potwierdzenia przed użyciem jako gotowa specyfikacja.

> Aktualizacja 2026-11-03: ścieżki stron zaktualizowano do struktury po eksporcie GitBook (np. `bramka-platnosci-online/`, `sdk-mobilne/`, `wtyczki-e-commerce/`). Tabela TODO ma kolumnę Status: 42 punkty otwarte, 30 rozwiązanych lub nieaktualnych. Liczby w sekcji „Kontrole i ograniczenia” opisują stan z dnia migracji.

## Decyzje redakcyjne

Decyzje podjęte podczas przeglądu komentarzy analityka i otwartych punktów TODO. Każda decyzja ma odpowiadający wpis w tabeli TODO.

| Data | Punkt | Decyzja |
| --- | --- | --- |
| 2026-11-20 | MIG-040–042, 044–050, 059, 064–067, 069–071 | Brakujące schematy utrzymujemy jako diagramy Mermaid (lub tabele Markdown), odtworzone z obrazków starej dokumentacji, zamiast plików graficznych. |
| 2026-11-20 | MIG-032 | Unikalność `OrderID` jest zalecana, nie wymagana; System domyślnie jej nie weryfikuje. ~~`CustomerEmail` dokumentujemy jako parametr wymagany~~ — zmienione 2026-11-26 na spotkaniu: `CustomerEmail` jest opcjonalny, ale zdecydowanie zalecany. |
| 2026-11-20 | MIG-025 | Rozliczenia **MASS_TRANSFER** na poziomie produktu (`customerNRB`, `title`, `receiverName` itd.) oraz `idBalancePoint` w koszyku nie wchodzą do nowej dokumentacji. |
| 2026-11-26 | Komentarz: indeks „Metody płatności” | Treść strony „Dostępne metody płatności” stanowi treść strony nadrzędnej „Metody płatności”; osobna podstrona usunięta. Adresy URL (angielskie nazwy plików) na razie bez zmian — temat odłożony. |
| 2026-11-26 | Komentarz: „Karty płatnicze” | Strona opisuje trzy warianty płatności kartą: Paywall, WhiteLabel bez widgetu (`GatewayID` 1500/1503) i WhiteLabel z Widgetem Kartowym. Autopay nie oferuje integracji, w której Partner sam zbiera dane karty. |
| 2026-11-26 | Komentarz: „Powrót klienta do serwisu” | Strona nosi tytuł „Powrót z płatności”. Po płatności System sam wyświetla podsumowanie i automatycznie przekierowuje do Serwisu Partnera; aktywacji wymaga tylko wyświetlanie podsumowania na żądanie (metoda confirmation). |
| 2026-11-26 | Komentarz: „Modele rozliczeń” na stronie „Saldo” | Modele rozliczeń przeniesione z „Pozostałe operacje API → Saldo” na nową stronę „Pierwsze kroki → Modele rozliczeń”. |
| 2026-11-26 | MIG-035, MIG-036, MIG-026 | `balanceGet` i wypłaty dokumentujemy tylko z `ServiceID` (bez `BalancePointID`). Modele rozliczeń produktów pozostają poza dokumentacją. |
| 2026-11-26 | Komentarz: „Zwroty”, MIG-037 | Dokumentujemy tylko `transactionRefund/v3` (zwrot całości, części lub produktów) i `outDetails/v2`. Starszy `transactionRefund` i `productRefund` usunięte — wtyczki i klienci z nich nie korzystają. |
| 2026-11-26 | Komentarz: formatowanie „UWAGA” | Uwagi stojące jako osobny akapit zapisujemy jako podpowiedzi GitBooka: `UWAGA`/`WAŻNE` → `{% hint style="warning" %}`, `WSKAZÓWKA`/`Podpowiedź` → `{% hint style="info" %}`. Uwagi w komórkach tabel bez zmian. |
| 2026-11-26 | MIG-027 | Po potwierdzeniu w modelu z potwierdzeniem merchanta końcowy `paymentStatus` to `SUCCESS` (nie `CONFIRMED`). `ON_HOLD` jest dokumentowaną wartością `paymentStatus` w ITN. |
| 2026-11-26 | MIG-022 | Nie przenosimy historii zmian ze starej dokumentacji. Nowy portal prowadzi historię od zera: pierwszy wpis „Uruchomienie nowej dokumentacji” z listą zmian API od sierpnia 2026, dalej tylko zmiany API i zachowania bramki (bez poprawek redakcyjnych). |
| 2026-11-26 | Komentarz: nawigacja („wstecz”, pozycja przewinięcia) | Bez zmian – zachowanie platformy GitBook, nie treści. |
| 2026-11-26 | Komentarz: „zwykły BLIK” (spotkanie) | Strona BLIK opisuje BLIK w modelu Paywall. Przy `GatewayID=509` w starcie transakcji Klient trafia bezpośrednio na stronę eBLIK, z pominięciem paywalla. |
| 2026-11-26 | Komentarz M. Gotowskiego o `GatewayID` (spotkanie) | `GatewayID` w starcie transakcji jest opcjonalny — komentarz był pomyłką; strona bez zmian. |
| 2026-11-26 | MIG-024 (spotkanie) | Strona „IPN – status produktu” usunięta z głównej dokumentacji (IPN opisany tylko dla Punktów Rozliczeń). |
| 2026-11-26 | Punkty Rozliczeń i Marketplace (spotkanie) | Nie wchodzą do głównego trzonu dokumentacji, ale **nie mogą zostać pominięte** — zostaną opisane osobno. Do tej osobnej dokumentacji trafią m.in.: `BalancePointID`/`idBalancePoint`, MASS_TRANSFER, modele rozliczeń produktów, IPN, `productRefund` i usługi Marketplace. Treść źródłowa: `~/documentation/README.md` oraz historia gita tego repo. |
| 2026-11-26 | MIG-026 (spotkanie) | Metoda `transactionSettlement` nie istnieje — model „rozliczeń transakcji na żądanie” usunięty ze strony „Modele rozliczeń”. |
| 2026-11-26 | MIG-031 (spotkanie) | Odpowiedź `gatewayList/v3`: poprawne nazwy pól to `gatewayID` i `iconUrl`; odpowiedź nie zawiera Hash. |
| 2026-11-26 | MIG-033 (spotkanie) | `transactionClear`: odpowiedź `<transaction>` z `serviceID`, `messageID`, `confirmation`, `hash` (poprzedni przykład `<balancePayoff>` był błędny). `Products` – opcjonalny, wymagany tylko, gdy preautoryzacja obejmowała wiele produktów. |
| 2026-11-26 | MIG-022 (spotkanie) | Analitycy akceptują konwencję historii zmian (tylko zmiany API i zachowania bramki) i dbają o kolejne wpisy. Historię zaczynamy prowadzić po oficjalnej publikacji; do tego czasu strona bez wpisów. |
| 2026-11-26 | Stara dokumentacja i portal developers.autopay.pl | Brak decyzji o wyłączeniu. Przez pewien czas stara i nowa dokumentacja są utrzymywane równolegle; linki do starego portalu na razie zostają. Zmiany w starej dokumentacji trzeba przenosić do GitBooka. |
| 2026-11-26 | Kolejność Hash parametrów startu | Numeracja „kolejność Hash” w tabelach parametrów podstawowych i dodatkowych odtworzona z listy `hashorder` z kodu bramki (od analityków): 78 udokumentowanych parametrów, numery 1–78 bez duplikatów. `ValidityTime` = 25, `LinkValidityTime` = 40, `IPKSeFTransaction` = 77, `IPKSeFLinkValidityTime` = 78. `CustomerEmail` liczony na pozycji 700 (parametry podstawowe), `Title` (rozliczenie) na 1500. |
| 2026-11-26 | `Hold`, `MerchantConfirmationRequired`, `HoldType` | Dopisane do tabeli dodatkowych parametrów na pozycjach Hash 55–57 (wg `hashorder` 5900/5910/5920); kolejne pozycje przesunięte o 3 (razem 81). Typy przyjęte z opisów scenariuszy (`true`/`false`; `FINAL_AUTHORIZATION`/`PREAUTHORIZATION`) — do potwierdzenia. |
| 2026-11-26 | MIG-022 — zakres historii zmian | Historię zmian prowadzimy od stycznia 2027. Zmian z sierpnia–października 2026 (i wcześniejszych) nie wpisujemy — nie będzie wpisu „Uruchomienie nowej dokumentacji” z ich listą. |

## Źródła i konfiguracja

- README-2.md: 8912 linii, SHA-256 `4b1828a705cc12454ca672219409c699cfd1c57ec5b6990f0f872f058a153ae8`. Załączniki w Downloads pozostawiono bez zmian.
- Root treści: katalog repozytorium. Brak .gitbook.yaml, book.json i lokalnego narzędzia podglądu; zachowano SUMMARY.md i .gitbook/assets.
- Pierwotny README SDK zachowano jako wprowadzenie sdk-mobilne/mobile-sdk.md, poprawiając zewnętrzne ścieżki.
- Chronione android/ i ios/ pozostawiono w dotychczasowych katalogach. Eksport GitBook przeniósł je później do sdk-mobilne/android/ i sdk-mobilne/ios/ razem z token.md (sdk-mobilne/token.md). Nazwy i hierarchia SDK pozostają wyjątkami od angielskich nazw nowych stron.
- schemat-transakcji-whitelabel.md był wcześniejszym materiałem spoza nawigacji; usunięto go razem z innymi osieroconymi kopiami (PR #4). Właściwy opis: bramka-platnosci-online/whitelabel/payment-method-selection.md.
- Istniejąca lokalna zmiana .DS_Store pozostawiona bez zmian.

## Mapa treści

Zakresy odnoszą się do dostarczonego README-2.md; tabele wspólne z polami spoza zakresu pozostawiono jako nierozstrzygnięte, z TODO, bez zmiany kontraktu.

| Linie źródła | Cel | Postępowanie |
| --- | --- | --- |
| 1–9 | — | Wstęp opracowano w README.md, zacznij-tutaj/getting-started.md i bramka-platnosci-online/pierwsze-kroki/README.md; pominięto zapowiedzi wyłączonych działów |
| 10–236 | — | Słownik → informacje-dodatkowe/glossary.md; pominięto definicje wyłącznie Integratora/Marketplace i onboardingu; wspólne pojęcia zawężono do Partnera |
| 237–249 | zacznij-tutaj/environments.md | Przeniesiono |
| 250–297 | bramka-platnosci-online/pierwsze-kroki/payment-overview.md | Przeniesiono |
| 298–362 | bramka-platnosci-online/pierwsze-kroki/integration-credentials.md | Przeniesiono |
| 363–383 | bramka-platnosci-online/pierwsze-kroki/testing-and-go-live.md | Przeniesiono |
| 384–400 | bramka-platnosci-online/podstawowy-proces-platnosci/start-transaction.md | Przeniesiono |
| 401–419 | bramka-platnosci-online/dane-transakcji/parameters.md | Przeniesiono |
| 420–467 | bramka-platnosci-online/podstawowy-proces-platnosci/start-transaction.md | Przeniesiono |
| 468–497 | bramka-platnosci-online/podstawowy-proces-platnosci/customer-redirect.md | Przeniesiono |
| 498–623 | bramka-platnosci-online/powiadomienia/itn.md | Przeniesiono |
| 624–680 | bramka-platnosci-online/dane-transakcji/statuses.md | Przeniesiono |
| 681–969 | bramka-platnosci-online/bezpieczenstwo/hashing.md | Przeniesiono |
| 970–973 | — | Nagłówki organizacyjne zastąpione nawigacją |
| 974–1055 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/merchant-confirmation.md | Przeniesiono |
| 1056–1159 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | Przeniesiono |
| 1160–1174 | — | Wyłączono samodzielny przykład BalancePoint |
| 1175–1220 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | Przeniesiono |
| 1221–1550 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/pretransaction.md | Przeniesiono |
| 1551–1663 | bramka-platnosci-online/metody-platnosci/pay-by-link.md | Przeniesiono |
| 1664–1739 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/blik-oneclick.md | Przeniesiono |
| 1740–1833 | bramka-platnosci-online/metody-platnosci/pay-by-link.md | Przeniesiono |
| 1834–2006 | bramka-platnosci-online/metody-platnosci/google-pay.md | Przeniesiono |
| 2007–2069 | bramka-platnosci-online/metody-platnosci/apple-pay.md | Przeniesiono |
| 2070–2074 | bramka-platnosci-online/whitelabel/widget/README.md | Przeniesiono |
| 2075–2093 | bramka-platnosci-online/whitelabel/requirements.md | Przeniesiono |
| 2094–2208 | bramka-platnosci-online/whitelabel/widget/README.md | Przeniesiono |
| 2209–2318 | bramka-platnosci-online/whitelabel/widget/cards.md | Przeniesiono |
| 2319–2405 | bramka-platnosci-online/whitelabel/widget/visa-mobile.md | Przeniesiono |
| 2406–2571 | bramka-platnosci-online/whitelabel/widget/cards.md | Przeniesiono |
| 2572–2576 | bramka-platnosci-online/metody-platnosci/cards.md | Przeniesiono |
| 2577–2671 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | Przeniesiono |
| 2672–2812 | bramka-platnosci-online/powiadomienia/rpan-rpdn.md | Przeniesiono |
| 2813–2831 | bramka-platnosci-online/powiadomienia/retry-policy.md | Przeniesiono |
| 2832–2954 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | Przeniesiono |
| 2955–3064 | bramka-platnosci-online/powiadomienia/rpan-rpdn.md | Przeniesiono |
| 3065–3181 | bramka-platnosci-online/dane-transakcji/additional-parameters.md | Przeniesiono |
| 3182–3260 | bramka-platnosci-online/dane-transakcji/product-basket.md | Przeniesiono |
| 3261–3327 | — | Wyłączono model Punktów Rozliczeń; granica MASS_TRANSFER wymaga decyzji |
| 3328–3347 | bramka-platnosci-online/dane-transakcji/product-basket.md | Przeniesiono |
| 3348–3349 | — | Nagłówek organizacyjny zastąpiony nawigacją |
| 3350–3469 | bramka-platnosci-online/powiadomienia/itn.md | Przeniesiono |
| 3470–3538 | bramka-platnosci-online/dane-transakcji/statuses.md | Przeniesiono |
| 3539–3542 | — | Opis konfiguracji IPN oparty o Punkty Rozliczeń; relacja ITN/IPN zachowana na stronie IPN z TODO |
| 3543–3691 | bramka-platnosci-online/powiadomienia/istn.md | Przeniesiono |
| 3692–3693 | — | Nagłówek organizacyjny zastąpiony nawigacją |
| 3694–3901 | bramka-platnosci-online/pozostale-operacje-api/gateway-list.md | Przeniesiono |
| 3902–4081 | bramka-platnosci-online/pozostale-operacje-api/legal-consents.md | Przeniesiono |
| 4082–4125 | bramka-platnosci-online/pozostale-operacje-api/balance.md | Przeniesiono |
| 4126–4139 | — | Wyłączono samodzielny przykład BalancePoint |
| 4140–4174 | bramka-platnosci-online/pozostale-operacje-api/balance.md | Przeniesiono |
| 4175–4233 | bramka-platnosci-online/pozostale-operacje-api/payouts.md | Przeniesiono |
| 4234–4245 | — | Wyłączono samodzielny przykład BalancePoint |
| 4246–4268 | bramka-platnosci-online/pozostale-operacje-api/payouts.md | Przeniesiono |
| 4269–4481 | bramka-platnosci-online/pozostale-operacje-api/refunds.md | Przeniesiono |
| 4482–4643 | — | Wyłączono Zwrot transakcji Marketplace, vendorsContribution i błędy wariantu |
| 4644–4832 | bramka-platnosci-online/pozostale-operacje-api/refunds.md | Przeniesiono |
| 4833–4865 | bramka-platnosci-online/podstawowy-proces-platnosci/customer-redirect.md | Przeniesiono |
| 4866–4968 | bramka-platnosci-online/pozostale-operacje-api/transaction-status.md | Przeniesiono |
| 4969–5057 | bramka-platnosci-online/pozostale-operacje-api/cancel-transaction.md | Przeniesiono |
| 5058–5080 | informacje-dodatkowe/errors.md | Przeniesiono |
| 5081–5094 | bramka-platnosci-online/pierwsze-kroki/payment-overview.md | Przeniesiono |
| 5095–5100 | bramka-platnosci-online/whitelabel/payment-method-selection.md | Przeniesiono |
| 5101–5139 | — | Wyłączono rozszerzoną strukturę Punktów Rozliczeń |
| 5140–5157 | bramka-platnosci-online/pozostale-operacje-api/balance.md | Przeniesiono |
| 5158–5172 | — | Nierozstrzygnięty zakres modeli rozliczeń produktów; TODO na stronie Saldo |
| 5173–5180 | bramka-platnosci-online/pozostale-operacje-api/balance.md | Przeniesiono |
| 5181–8912 | — | Integrator i Marketplace poza zakresem |

Dodatkowo pominięto wyłącznie zdanie o zasilaniu Punktu Rozliczeń w opisie zasilania salda (4171–4173). Wstępy i strony przekrojowe odsyłają do kanonicznych tabel. Przykład HTML widgetu pozostaje kompletny w widget/cards.md i jest linkowany z Visa Mobile.

## Inwentaryzacja chronionych plików

| Plik | SHA-256 przed i po |
| --- | --- |
| sdk-mobilne/android/README.md | `60185db6ed63c37d1f4fef37bcf2e5fb4c09af9029ce4cff6e11fadba961c2f2` |
| sdk-mobilne/android/szczegolowy-opis-klas-i-metod.md | `7fcd5f29a0527c7b3286c7cb7601b0961172d4b0c913ed349c5612251023cc5d` |
| sdk-mobilne/android/migracja-z-poprzednich-wersji.md | `471de3cc510b14ed899ecc29e6efaa70006e9defaf38c1cb26ea718c4917942c` |
| sdk-mobilne/ios/README.md | `490a6dfa8bf1cd0678aa93a04ad1b0cd70377534f49af7bf5889a8ed02de5388` |
| sdk-mobilne/ios/szczegolowy-opis-klas-i-metod.md | `4173f41c0c0b7eba04c22dc34139f5b7a3fcb5127e6c3063c048c53eb96d3ad9` |
| sdk-mobilne/ios/migracja.md | `7d37af76ab0ebacd3138cad58b9e3dc38c27472bbba3cee5d9abac2e352da160` |
| .gitbook/assets/diagram_variant_II_en.png | `3f2aac3e84ab3fd164eb06d4a6559fb7b62e47e0eaf33ef0ee8a1f62db99871c` |
| .gitbook/assets/diagram_numbers_variant_II_en.png | `ac4f3535b62862b7eabab9b0b4ba5f279ef63282999532d76865508999962c45` |
| .gitbook/assets/diagram_numbers_variant_III.png | `561323376b5aeddd6e7d8832f2fb2b593277bf6a10e24e33460d964f71ee5268` |
| .gitbook/assets/autopay_sdk.png | `26074b7c1c553c1031af06156cc17580b1634fe940de8fde99ff68c2a9ac2332` |
| .gitbook/assets/diagram_variant_III.png | `1ae0d7416cf6743be9009ba430d39c1c836cb1492ffc0abcee2e561db0f2165f` |
| .gitbook/assets/diagram_variant_I_en.png | `281409689c89586dd7623af6f645b52ab6b85f240227b2f539b870a3d73cae9f` |
| .gitbook/assets/diagram_numbers_variant_I_en.png | `ea0509ebaa70d673f455f2cc7212cf6ead2340e9a0a1f673ad4748d2a4e463d4` |
| .gitbook/assets/diagram_variant_I.png | `b639d9be6f246eaa79e91b1b22ba3a5a86042e330f1b853dd26ae29295110bd9` |
| .gitbook/assets/android.png | `60309b175baf79d3c9176f9202ce1fc3879c8a7dc5e9ed4f77a7bcc2569d96dd` |
| .gitbook/assets/diagram_variant_II.png | `986ddf8b044f22954bddab23551d39eeaa06198257f3cc106f5de355c7717e28` |
| .gitbook/assets/diagram_numbers_variant_II.png | `61fb7570a20321590826abda6807243a274c62f42373146b69a20ec035312574` |
| .gitbook/assets/diagram_numbers_variant_III_en.png | `27930be1ec5ee2ec700486c08d6c0ead9d82bfc3f812e975f5ac53548b7d56e9` |
| .gitbook/assets/ios.png | `616e0455d497ae92073fafd31a106a0e624c612877dfa2ed84aad9e4983801ba` |
| .gitbook/assets/autopay_sdk_header_logo.png | `028da783041c12b68d344f80cfadd57092a359442dd16af0a32d5e08b79cdad7` |
| .gitbook/assets/diagram_variant_III_en.png | `82cd02244e7af18acc2e3a04dac5e4d978bade371c2f7681feb6344faeab5779` |
| .gitbook/assets/diagram_numbers_variant_I.png | `b604f08b39e2ddf03d186baa3e70e5485fc6a2aae2d42a836fdedd9aa86a0d93` |

## Zasoby

Katalogu images/ źródła nie dostarczono. Żadnego brakującego diagramu nie zastąpiono grafiką. Nie kopiowano zasobów SDK. Katalog assets/images/ należy wypełnić po dostarczeniu oryginałów, używając angielskich nazw. Poniżej pełna lista użyć brakujących zasobów objętych migracją.

| Strona | Ścieżka źródła | Wynik |
| --- | --- | --- |
| bramka-platnosci-online/pierwsze-kroki/payment-overview.md | `images/paybm-sequences-trx-paywall.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/dane-transakcji/statuses.md | `images/obraz2.png` | Zastąpiono tabelą Markdown |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/merchant-confirmation.md | `images/paybm-sequences-2pv.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | `images/SchematA.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | `images/SchematB.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | `images/SchematC.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | `images/SchematD.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | `images/SchematE.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | `images/SchematF.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/metody-platnosci/google-pay.md | `images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-empty-example-page-start.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-loaded.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-invalid.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-ready.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-dcc.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-dcc-invalid.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-dcc-rejected.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-example-page-clicked.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/cards.md | `images/widget-card-diagram-flow.svg` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/whitelabel/widget/visa-mobile.md | `images/widget-empty-example-page-start.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-example-page-loaded.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-example-page-ready.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-example-page-clicked.png` | Dodano do .gitbook/assets |
| bramka-platnosci-online/whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-diagram-flow.svg` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | `images/aktywacja_płatności_automatycznej.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | `images/obciążenie_dla_płatności_automatycznej.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | `images/dezaktywacja_usługi.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/pozostale-operacje-api/balance.md | `images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/pozostale-operacje-api/balance.md | `images/model_rozliczeń_transakcji_po_każdej_wpłacie.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/pozostale-operacje-api/balance.md | `images/model_rozliczeń_transakcji_na_żądanie.png` | Zastąpiono diagramem Mermaid |
| bramka-platnosci-online/whitelabel/payment-method-selection.md | `images/paybm-sequences-trx-whiteLabel.png` | Brak pliku; TODO |

## TODO

| ID | Strona | Źródło | Problem | Wymagana decyzja/materiał | Status |
| --- | --- | --- | --- | --- | --- |
| MIG-001 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „O wtyczce”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-002 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Wymagania”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-003 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Instalacja”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-004 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Konfiguracja”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-005 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Dostępne metody płatności”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-006 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Aktualizacja”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-007 | wtyczki-e-commerce/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Rozwiązywanie problemów”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. | Otwarte |
| MIG-008 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „O wtyczce”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-009 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Wymagania”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-010 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Instalacja”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-011 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Konfiguracja”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-012 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Dostępne metody płatności”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-013 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Aktualizacja”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-014 | wtyczki-e-commerce/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Rozwiązywanie problemów”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki WooCommerce (scripts/sync_woocommerce_readme.py) |
| MIG-015 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „O wtyczce”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-016 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Wymagania”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-017 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Instalacja”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-018 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Konfiguracja”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-019 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Dostępne metody płatności”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-020 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Aktualizacja”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-021 | wtyczki-e-commerce/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Rozwiązywanie problemów”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. | Rozwiązane: strona synchronizowana automatycznie z README wtyczki Magento (scripts/sync_magento_readme.py) |
| MIG-022 | informacje-dodatkowe/changelog.md | Dostarczone materiały | Nie dostarczono potwierdzonej historii zmian bramki i WhiteLabel. | Dostarczyć historię wersji; nie przenosić historii SDK do historii bramki. | Rozwiązane 2026-11-26: historia zmian prowadzona od stycznia 2027, bez wcześniejszych zmian |
| MIG-023 | sdk-mobilne/mobile-sdk.md | sdk-mobilne/android/README.md, sdk-mobilne/ios/README.md | Gałęzie pozostają w android/ i ios/: przeniesienie zerwałoby odwołania ../.gitbook/assets/ i ../token.md. | Ewentualne przeniesienie wymaga osobnej decyzji o zmianie chronionych odwołań. | Nieaktualne: eksport GitBook przeniósł SDK do sdk-mobilne/ razem z odwołaniami; adnotację usunięto ze strony |
| MIG-024 | bramka-platnosci-online/powiadomienia/ipn.md | README-2.md:3539–3542 | Źródło opisuje konfigurację IPN wyłącznie na poziomie Punktów Rozliczeń. Nie ustalono samodzielnego wariantu dla zwykłego serwisu. | Potwierdzić dostępność i konfigurację IPN poza wyłączonym modelem. | Rozwiązane 2026-11-26 (spotkanie): strona IPN usunięta z dokumentacji głównej; temat wraca w osobnej dokumentacji Punktów Rozliczeń i Marketplace |
| MIG-025 | bramka-platnosci-online/dane-transakcji/product-basket.md | README-2.md:3272–3312 | Opis MASS_TRANSFER wiąże rozliczenia produktów z Punktami Rozliczeń; nie przeniesiono niejednoznacznego wariantu. | Potwierdzić niezależny od Punktów Rozliczeń zakres parametrów rozliczania produktów. | Rozwiązane 2026-11-20: MASS_TRANSFER poza zakresem (patrz Decyzje redakcyjne) |
| MIG-026 | bramka-platnosci-online/pierwsze-kroki/settlement-models.md | README-2.md:5158–5180 | Modele rozliczeń produktów mają niejasną granicę zakresu. Dla transactionSettlement źródło podaje wyłącznie nazwę operacji. | Dostarczyć niezależny opis modeli produktów i kompletny kontrakt transactionSettlement. | Rozwiązane 2026-11-26 (spotkanie): endpoint transactionSettlement nie istnieje — usunięto model rozliczeń „na żądanie”; modele rozliczeń produktów poza zakresem |
| MIG-027 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/merchant-confirmation.md | README-2.md:1003,1013,1041 | Opis wskazuje końcowy paymentStatus=CONFIRMED, tabela odpowiedzi wskazuje SUCCESS. Zachowano oba opisy. | Potwierdzić końcowy status ITN i pełną listę statusów dla tego modelu. | Rozwiązane 2026-11-26: końcowy status SUCCESS (tabela, diagram, specyfikacja ITN); ON_HOLD dopisany do wartości paymentStatus w ITN |
| MIG-028 | bramka-platnosci-online/powiadomienia/rpan-rpdn.md | README-2.md:2967–2976 | Przykład RPDN zawiera otwarcie recurringData zamiast jego zamknięcia. Zachowano oryginalny XML. | Potwierdzić poprawny XML przed użyciem przykładu. | Rozwiązane 2026-11-26: poprawiono zamknięcie </recurringData> w przykładzie RPDN |
| MIG-029 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/pretransaction.md | README-2.md:1313–1323 | Przykład PHP zawiera typograficzne cudzysłowy OrderID i Hash bez cudzysłowów. | Potwierdzić poprawny kod; nie uruchamiać przykładu bez korekty uzgodnionej z właścicielem API. | Rozwiązane 2026-11-26: poprawiono cudzysłowy OrderID i ujęto Hash w cudzysłowy |
| MIG-030 | bramka-platnosci-online/pozostale-operacje-api/legal-consents.md | README-2.md:3977,4013 | Opis regulationID używa isCheckboxRequired, tabela i przykład checkboxRequired; w przykładzie JSON brakuje przecinka po inputLabel. | Potwierdzić nazwę pola i poprawny JSON. | Rozwiązane 2026-11-26: ujednolicono na checkboxRequired (tabela i przykład), poprawiono JSON |
| MIG-031 | bramka-platnosci-online/pozostale-operacje-api/gateway-list.md | README-2.md:3740–3890 | Tabela używa gatewayID i iconUrl; przykład ma także id i iconURL. Opisy currencies wspominają Hash odpowiedzi, choć przykład go nie zawiera. | Potwierdzić nazwy pól i zakres podpisywania odpowiedzi v3. | Rozwiązane 2026-11-26 (spotkanie): pola gatewayID i iconUrl; odpowiedź v3 bez Hash |
| MIG-032 | bramka-platnosci-online/dane-transakcji/parameters.md | README-2.md:415,444–450,1546 | CustomerEmail jest wymagany w tabeli, lecz pominięty w liście i przykładzie startu. Opisy unikalności OrderID różnią się między startem i przedtransakcją. | Potwierdzić wymagalność CustomerEmail i reguły ponawiania OrderID; zachowano oba warianty. | Rozwiązane: unikalność OrderID zalecana; CustomerEmail opcjonalny, zdecydowanie zalecany (spotkanie 2026-11-26) |
| MIG-033 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md:1135,1150–1184 | Products zawiera idBalancePoint we wspólnej definicji. Przykład odpowiedzi balancePayoff nie odpowiada tabeli confirmation/reason. | Potwierdzić kontrakt transactionClear i zakres koszyka; zachowano wspólną definicję bez usuwania pól. | Rozwiązane 2026-11-26 (spotkanie): odpowiedź transactionClear to <transaction> (serviceID, messageID, confirmation, hash); Products wymagany tylko przy wielu produktach; idBalancePoint usunięty |
| MIG-034 | bramka-platnosci-online/powiadomienia/itn.md | README-2.md:3361–3469 | Wspólny przykład ITN/IPN zawiera idBalancePoint. Tabela powtarza verificationStatus dla powodu weryfikacji, przykład używa verificationStatusReason; validityMonth ma długość 4 mimo formatu mm. | Potwierdzić wariant wspólny i nazwy/typy pól. Definicji ani przykładu nie okrojono; nie traktować tej części jako zweryfikowanej specyfikacji. | Rozwiązane 2026-11-26: usunięto idBalancePoint z przykładu, wiersz 33 → verificationStatusReason, validityMonth string{2} |
| MIG-035 | bramka-platnosci-online/pozostale-operacje-api/balance.md | README-2.md:4094–4156 | Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i zależne reguły Hash. Zachowano je, pomijając odrębny przykład Punktu Rozliczeń. | Potwierdzić samodzielny kontrakt dla ServiceID; wariant Punktów Rozliczeń pozostaje poza zakresem wdrożenia. | Rozwiązane 2026-11-26: usunięto BalancePointID z balanceGet (Punkty Rozliczeń poza zakresem) |
| MIG-036 | bramka-platnosci-online/pozostale-operacje-api/payouts.md | README-2.md:4191–4258 | Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i reguły klucza pełnomocnika. Zachowano kontrakt bez usuwania pól. | Potwierdzić samodzielny wariant dla ServiceID przed uznaniem tej części za gotową specyfikację. | Rozwiązane 2026-11-26: usunięto BalancePointID (Punkty Rozliczeń poza zakresem) |
| MIG-037 | bramka-platnosci-online/pozostale-operacje-api/refunds.md | README-2.md:4298,4432–4461,4728–4832 | Wspólne definicje zwrotów i outDetails zawierają pola lub błędy Punktów Rozliczeń. Opis Amount starszej usługi mówi o wypłacie całego salda, mimo opisu zwrotu transakcji. | Potwierdzić semantykę Amount i samodzielny kontrakt ServiceID. Zachowano wspólne pola, błędy oraz przykłady, bez publikowania osobnej usługi Marketplace. | Rozwiązane 2026-11-26: strona ograniczona do transactionRefund/v3 i outDetails/v2; usunięto starszy transactionRefund, productRefund i pola Punktów Rozliczeń |
| MIG-038 | informacje-dodatkowe/errors.md | README-2.md:5058–5064 oraz Zwrot transakcji V3 | Ogólny opis mówi o wszystkich błędach w XML, podczas gdy V3 opisuje błędy JSON. | Potwierdzić zakres ogólnego opisu; stosować specyfikację właściwej operacji. | Rozwiązane 2026-11-26: opis rozróżnia błędy XML (form-urlencoded) i JSON (metody JSON) |
| MIG-039 | bramka-platnosci-online/pierwsze-kroki/test-cards.md | README-2.md:2259–2260 | Nie dostarczono odrębnego załącznika z kartami i scenariuszami testowymi. | Dostarczyć załącznik testowy widgetu kartowego. | Otwarte – przygotowano stronę „Karty testowe”; lista czeka na potwierdzenie zespołu kart |
| MIG-040 | bramka-platnosci-online/pierwsze-kroki/payment-overview.md | README-2.md: images/paybm-sequences-trx-paywall.png | Brak obrazu/diagramu: images/paybm-sequences-trx-paywall.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-041 | bramka-platnosci-online/dane-transakcji/statuses.md | README-2.md: images/obraz2.png | Brak obrazu/diagramu: images/obraz2.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako tabelę Markdown na podstawie ~/documentation/images |
| MIG-042 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/merchant-confirmation.md | README-2.md: images/paybm-sequences-2pv.png | Brak obrazu/diagramu: images/paybm-sequences-2pv.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-043 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/merchant-confirmation.md | README-2.md: odwołania #platnosc-z-potwierdzeniem-merchanta, #platnosc-z-potwierdzeniem-merchanta, #platnosc-z-potwierdzeniem-merchanta | Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. | Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą. | Rozwiązane 2026-11-26: odwołania wskazują sekcję „Opis metody potwierdzającej transakcję” na tej samej stronie |
| MIG-044 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md: images/SchematA.png | Brak obrazu/diagramu: images/SchematA.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-045 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md: images/SchematB.png | Brak obrazu/diagramu: images/SchematB.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-046 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md: images/SchematC.png | Brak obrazu/diagramu: images/SchematC.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-047 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md: images/SchematD.png | Brak obrazu/diagramu: images/SchematD.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-048 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md: images/SchematE.png | Brak obrazu/diagramu: images/SchematE.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-049 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/card-preauthorization.md | README-2.md: images/SchematF.png | Brak obrazu/diagramu: images/SchematF.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-050 | bramka-platnosci-online/metody-platnosci/google-pay.md | README-2.md: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png | Brak obrazu/diagramu: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-051 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-empty-example-page-start.png | Brak obrazu/diagramu: images/widget-empty-example-page-start.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-052 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-loaded.png | Brak obrazu/diagramu: images/widget-card-example-page-loaded.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-053 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-invalid.png | Brak obrazu/diagramu: images/widget-card-example-page-invalid.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-054 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-ready.png | Brak obrazu/diagramu: images/widget-card-example-page-ready.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-055 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-dcc.png | Brak obrazu/diagramu: images/widget-card-example-page-dcc.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-056 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-dcc-invalid.png | Brak obrazu/diagramu: images/widget-card-example-page-dcc-invalid.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-057 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-dcc-rejected.png | Brak obrazu/diagramu: images/widget-card-example-page-dcc-rejected.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-058 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-clicked.png | Brak obrazu/diagramu: images/widget-card-example-page-clicked.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-059 | bramka-platnosci-online/whitelabel/widget/cards.md | README-2.md: images/widget-card-diagram-flow.svg | Brak obrazu/diagramu: images/widget-card-diagram-flow.svg. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-060 | bramka-platnosci-online/whitelabel/widget/visa-mobile.md | README-2.md: images/widget-empty-example-page-start.png | Brak obrazu/diagramu: images/widget-empty-example-page-start.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-061 | bramka-platnosci-online/whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-example-page-loaded.png | Brak obrazu/diagramu: images/widget-visa-mobile-example-page-loaded.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-062 | bramka-platnosci-online/whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-example-page-ready.png | Brak obrazu/diagramu: images/widget-visa-mobile-example-page-ready.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-063 | bramka-platnosci-online/whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-example-page-clicked.png | Brak obrazu/diagramu: images/widget-visa-mobile-example-page-clicked.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: dodano oryginalny zrzut ekranu z ~/documentation/images do .gitbook/assets |
| MIG-064 | bramka-platnosci-online/whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-diagram-flow.svg | Brak obrazu/diagramu: images/widget-visa-mobile-diagram-flow.svg. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-065 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | README-2.md: images/aktywacja_płatności_automatycznej.png | Brak obrazu/diagramu: images/aktywacja_płatności_automatycznej.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-066 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | README-2.md: images/obciążenie_dla_płatności_automatycznej.png | Brak obrazu/diagramu: images/obciążenie_dla_płatności_automatycznej.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-067 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | README-2.md: images/dezaktywacja_usługi.png | Brak obrazu/diagramu: images/dezaktywacja_usługi.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-20: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-068 | bramka-platnosci-online/zaawansowane-scenariusze-platnosci/recurring-payments.md | README-2.md: odwołania #pobranie-informacji-o-aktualnej-liście-dostępnych-regulaminów---getlegaldata | Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. | Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą. | Rozwiązane 2026-11-26: odwołanie do legalData wskazuje stronę „Regulaminy i zgody” |
| MIG-069 | bramka-platnosci-online/pozostale-operacje-api/balance.md | README-2.md: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png | Brak obrazu/diagramu: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-070 | bramka-platnosci-online/pozostale-operacje-api/balance.md | README-2.md: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png | Brak obrazu/diagramu: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-071 | bramka-platnosci-online/pozostale-operacje-api/balance.md | README-2.md: images/model_rozliczeń_transakcji_na_żądanie.png | Brak obrazu/diagramu: images/model_rozliczeń_transakcji_na_żądanie.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane 2026-11-26: odtworzono jako diagram Mermaid na podstawie ~/documentation/images |
| MIG-072 | bramka-platnosci-online/whitelabel/payment-method-selection.md | README-2.md: images/paybm-sequences-trx-whiteLabel.png | Brak obrazu/diagramu: images/paybm-sequences-trx-whiteLabel.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. | Rozwiązane w GitBook: oryginalny obraz zastąpiono diagramem Mermaid; do potwierdzenia przez analityków |

## Kontrole i ograniczenia

Kontrole statyczne zakończone: 54 strony w SUMMARY.md, brak osieroconych stron migracji, wszystkie lokalne cele Markdown i HTML istnieją, wszystkie sprawdzane kotwice prowadzą do nagłówków lub jawnych identyfikatorów. Każda nowa strona ma jeden H1, 74 bloki kodu są zamknięte. Porównano 62 oryginalnych bloków kodu: wszystkie zachowują dokładną treść. Porównano 544 źródłowych wierszy tabel (po normalizacji formatowania i odsyłaczy). Zachowano 6 plików Android/iOS i 16 wspólnych zasobów z identycznymi sumami SHA-256; kolejność obu poddrzew nawigacji jest identyczna. Strony wtyczek mają wymagane siedem sekcji. Kontrola wyłączeń: brak osobnych stron Integratora/Marketplace, widgetu onboardingowego i vendorsContribution. Wspólne nierozstrzygnięte pola i błędy pozostawiono wyłącznie z opisanymi TODO. Materiały wejściowe nie były modyfikowane. Nie wykonano podglądu GitBooka ani testów integracyjnych z API. Nie sprawdzano aktualności zewnętrznych URL-i ani wymagań u dostawców: migracja zachowuje dostarczone źródło. Nie skonfigurowano przekierowań publicznych URL-i, ponieważ nie dostarczono potwierdzonej mapy; mapa treści powyżej służy do jej przygotowania.

## Dodatkowe transformacje formatowania

Tabelę HTML wyniku przedtransakcji przepisano na Markdown, zachowując zagnieżdżony przykład blikAMList jako XML pod tabelą. Przykłady wcięte otrzymały bloki kodu; treści przykładów zachowano. Uzupełniono puste końcowe komórki tabeli podatkowej i końcowe separatory tabel; nie zmieniano wartości pól.

## Proponowana mapa nazw brakujących zasobów

To propozycja ścieżek po dostarczeniu plików; nie utworzono fikcyjnych obrazów.

| Źródło | Proponowany cel |
| --- | --- |
| `images/SchematA.png` | `assets/images/preauthorization-scheme-a.png` |
| `images/SchematB.png` | `assets/images/preauthorization-scheme-b.png` |
| `images/SchematC.png` | `assets/images/preauthorization-scheme-c.png` |
| `images/SchematD.png` | `assets/images/preauthorization-scheme-d.png` |
| `images/SchematE.png` | `assets/images/preauthorization-scheme-e.png` |
| `images/SchematF.png` | `assets/images/preauthorization-scheme-f.png` |
| `images/aktywacja_płatności_automatycznej.png` | `assets/images/recurring-payment-activation.png` |
| `images/dezaktywacja_usługi.png` | `assets/images/recurring-payment-deactivation.png` |
| `images/model_rozliczeń_transakcji_na_żądanie.png` | `assets/images/on-demand-transaction-settlement.png` |
| `images/model_rozliczeń_transakcji_po_każdej_wpłacie.png` | `assets/images/per-payment-transaction-settlement.png` |
| `images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png` | `assets/images/batch-transaction-settlement.png` |
| `images/obciążenie_dla_płatności_automatycznej.png` | `assets/images/recurring-payment-charge.png` |
| `images/obraz2.png` | `assets/images/transaction-status-flow.png` |
| `images/paybm-sequences-2pv.png` | `assets/images/paybm-sequences-2pv.png` |
| `images/paybm-sequences-trx-paywall.png` | `assets/images/paybm-sequences-trx-paywall.png` |
| `images/paybm-sequences-trx-whiteLabel.png` | `assets/images/paybm-sequences-trx-white-label.png` |
| `images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png` | `assets/images/google-pay-communication-flow.png` |
| `images/widget-card-diagram-flow.svg` | `assets/images/widget-card-diagram-flow.svg` |
| `images/widget-card-example-page-clicked.png` | `assets/images/widget-card-example-page-clicked.png` |
| `images/widget-card-example-page-dcc-invalid.png` | `assets/images/widget-card-example-page-dcc-invalid.png` |
| `images/widget-card-example-page-dcc-rejected.png` | `assets/images/widget-card-example-page-dcc-rejected.png` |
| `images/widget-card-example-page-dcc.png` | `assets/images/widget-card-example-page-dcc.png` |
| `images/widget-card-example-page-invalid.png` | `assets/images/widget-card-example-page-invalid.png` |
| `images/widget-card-example-page-loaded.png` | `assets/images/widget-card-example-page-loaded.png` |
| `images/widget-card-example-page-ready.png` | `assets/images/widget-card-example-page-ready.png` |
| `images/widget-empty-example-page-start.png` | `assets/images/widget-empty-example-page-start.png` |
| `images/widget-visa-mobile-diagram-flow.svg` | `assets/images/widget-visa-mobile-diagram-flow.svg` |
| `images/widget-visa-mobile-example-page-clicked.png` | `assets/images/widget-visa-mobile-example-page-clicked.png` |
| `images/widget-visa-mobile-example-page-loaded.png` | `assets/images/widget-visa-mobile-example-page-loaded.png` |
| `images/widget-visa-mobile-example-page-ready.png` | `assets/images/widget-visa-mobile-example-page-ready.png` |
