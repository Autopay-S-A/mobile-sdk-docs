# Raport migracji dokumentacji

Migracja struktury na podstawie DOCS_MIGRATION.md i README-2.md. Nie publikowano dokumentacji. Strony z TODO kontraktu wymagają potwierdzenia przed użyciem jako gotowa specyfikacja.

## Źródła i konfiguracja

- README-2.md: 8912 linii, SHA-256 `4b1828a705cc12454ca672219409c699cfd1c57ec5b6990f0f872f058a153ae8`. Załączniki w Downloads pozostawiono bez zmian.
- Root treści: katalog repozytorium. Brak .gitbook.yaml, book.json i lokalnego narzędzia podglądu; zachowano SUMMARY.md i .gitbook/assets.
- Pierwotny README SDK zachowano jako wprowadzenie mobile-sdk/README.md, poprawiając zewnętrzne ścieżki.
- Chronione android/ i ios/ pozostają w dotychczasowych katalogach. Nazwy i hierarchia SDK pozostają wyjątkami od angielskich nazw nowych stron.
- token.md pozostawiono przy korzeniu, ponieważ ios/README.md linkuje do ../token.md.
- schemat-transakcji-whitelabel.md zachowano bez zmian poza nową nawigacją jako wcześniejszy materiał. Nie scalono jego specyfikacji z README-2.md. Nowy właściwy opis: whitelabel/payment-method-selection.md.
- Istniejąca lokalna zmiana .DS_Store pozostawiona bez zmian.

## Mapa treści

Zakresy odnoszą się do dostarczonego README-2.md; tabele wspólne z polami spoza zakresu pozostawiono jako nierozstrzygnięte, z TODO, bez zmiany kontraktu.

| Linie źródła | Cel | Postępowanie |
| --- | --- | --- |
| 1–9 | — | Wstęp opracowano w README.md, getting-started/README.md i online-payments/README.md; pominięto zapowiedzi wyłączonych działów |
| 10–236 | — | Słownik → additional-information/glossary.md; pominięto definicje wyłącznie Integratora/Marketplace i onboardingu; wspólne pojęcia zawężono do Partnera |
| 237–249 | getting-started/environments.md | Przeniesiono |
| 250–297 | online-payments/getting-started/payment-overview.md | Przeniesiono |
| 298–362 | online-payments/getting-started/integration-credentials.md | Przeniesiono |
| 363–383 | online-payments/getting-started/testing-and-go-live.md | Przeniesiono |
| 384–400 | online-payments/payment-flow/start-transaction.md | Przeniesiono |
| 401–419 | online-payments/transaction-data/parameters.md | Przeniesiono |
| 420–467 | online-payments/payment-flow/start-transaction.md | Przeniesiono |
| 468–497 | online-payments/payment-flow/customer-redirect.md | Przeniesiono |
| 498–623 | online-payments/notifications/itn.md | Przeniesiono |
| 624–680 | online-payments/transaction-data/statuses.md | Przeniesiono |
| 681–969 | online-payments/security/hashing.md | Przeniesiono |
| 970–973 | — | Nagłówki organizacyjne zastąpione nawigacją |
| 974–1055 | online-payments/advanced-flows/merchant-confirmation.md | Przeniesiono |
| 1056–1159 | online-payments/advanced-flows/card-preauthorization.md | Przeniesiono |
| 1160–1174 | — | Wyłączono samodzielny przykład BalancePoint |
| 1175–1220 | online-payments/advanced-flows/card-preauthorization.md | Przeniesiono |
| 1221–1550 | online-payments/advanced-flows/pretransaction.md | Przeniesiono |
| 1551–1663 | online-payments/payment-methods/pay-by-link.md | Przeniesiono |
| 1664–1739 | online-payments/advanced-flows/blik-oneclick.md | Przeniesiono |
| 1740–1833 | online-payments/payment-methods/pay-by-link.md | Przeniesiono |
| 1834–2006 | online-payments/payment-methods/google-pay.md | Przeniesiono |
| 2007–2069 | online-payments/payment-methods/apple-pay.md | Przeniesiono |
| 2070–2074 | whitelabel/widget/README.md | Przeniesiono |
| 2075–2093 | whitelabel/requirements.md | Przeniesiono |
| 2094–2208 | whitelabel/widget/README.md | Przeniesiono |
| 2209–2318 | whitelabel/widget/cards.md | Przeniesiono |
| 2319–2405 | whitelabel/widget/visa-mobile.md | Przeniesiono |
| 2406–2571 | whitelabel/widget/cards.md | Przeniesiono |
| 2572–2576 | online-payments/payment-methods/cards.md | Przeniesiono |
| 2577–2671 | online-payments/advanced-flows/recurring-payments.md | Przeniesiono |
| 2672–2812 | online-payments/notifications/rpan-rpdn.md | Przeniesiono |
| 2813–2831 | online-payments/notifications/retry-policy.md | Przeniesiono |
| 2832–2954 | online-payments/advanced-flows/recurring-payments.md | Przeniesiono |
| 2955–3064 | online-payments/notifications/rpan-rpdn.md | Przeniesiono |
| 3065–3181 | online-payments/transaction-data/additional-parameters.md | Przeniesiono |
| 3182–3260 | online-payments/transaction-data/product-basket.md | Przeniesiono |
| 3261–3327 | — | Wyłączono model Punktów Rozliczeń; granica MASS_TRANSFER wymaga decyzji |
| 3328–3347 | online-payments/transaction-data/product-basket.md | Przeniesiono |
| 3348–3349 | — | Nagłówek organizacyjny zastąpiony nawigacją |
| 3350–3469 | online-payments/notifications/itn.md | Przeniesiono |
| 3470–3538 | online-payments/transaction-data/statuses.md | Przeniesiono |
| 3539–3542 | — | Opis konfiguracji IPN oparty o Punkty Rozliczeń; relacja ITN/IPN zachowana na stronie IPN z TODO |
| 3543–3691 | online-payments/notifications/istn.md | Przeniesiono |
| 3692–3693 | — | Nagłówek organizacyjny zastąpiony nawigacją |
| 3694–3901 | online-payments/api-operations/gateway-list.md | Przeniesiono |
| 3902–4081 | online-payments/api-operations/legal-consents.md | Przeniesiono |
| 4082–4125 | online-payments/api-operations/balance.md | Przeniesiono |
| 4126–4139 | — | Wyłączono samodzielny przykład BalancePoint |
| 4140–4174 | online-payments/api-operations/balance.md | Przeniesiono |
| 4175–4233 | online-payments/api-operations/payouts.md | Przeniesiono |
| 4234–4245 | — | Wyłączono samodzielny przykład BalancePoint |
| 4246–4268 | online-payments/api-operations/payouts.md | Przeniesiono |
| 4269–4481 | online-payments/api-operations/refunds.md | Przeniesiono |
| 4482–4643 | — | Wyłączono Zwrot transakcji Marketplace, vendorsContribution i błędy wariantu |
| 4644–4832 | online-payments/api-operations/refunds.md | Przeniesiono |
| 4833–4865 | online-payments/payment-flow/customer-redirect.md | Przeniesiono |
| 4866–4968 | online-payments/api-operations/transaction-status.md | Przeniesiono |
| 4969–5057 | online-payments/api-operations/cancel-transaction.md | Przeniesiono |
| 5058–5080 | additional-information/errors.md | Przeniesiono |
| 5081–5094 | online-payments/getting-started/payment-overview.md | Przeniesiono |
| 5095–5100 | whitelabel/payment-method-selection.md | Przeniesiono |
| 5101–5139 | — | Wyłączono rozszerzoną strukturę Punktów Rozliczeń |
| 5140–5157 | online-payments/api-operations/balance.md | Przeniesiono |
| 5158–5172 | — | Nierozstrzygnięty zakres modeli rozliczeń produktów; TODO na stronie Saldo |
| 5173–5180 | online-payments/api-operations/balance.md | Przeniesiono |
| 5181–8912 | — | Integrator i Marketplace poza zakresem |

Dodatkowo pominięto wyłącznie zdanie o zasilaniu Punktu Rozliczeń w opisie zasilania salda (4171–4173). Wstępy i strony przekrojowe odsyłają do kanonicznych tabel. Przykład HTML widgetu pozostaje kompletny w widget/cards.md i jest linkowany z Visa Mobile.

## Inwentaryzacja chronionych plików

| Plik | SHA-256 przed i po |
| --- | --- |
| android/README.md | `60185db6ed63c37d1f4fef37bcf2e5fb4c09af9029ce4cff6e11fadba961c2f2` |
| android/szczegolowy-opis-klas-i-metod.md | `7fcd5f29a0527c7b3286c7cb7601b0961172d4b0c913ed349c5612251023cc5d` |
| android/migracja-z-poprzednich-wersji.md | `471de3cc510b14ed899ecc29e6efaa70006e9defaf38c1cb26ea718c4917942c` |
| ios/README.md | `490a6dfa8bf1cd0678aa93a04ad1b0cd70377534f49af7bf5889a8ed02de5388` |
| ios/szczegolowy-opis-klas-i-metod.md | `4173f41c0c0b7eba04c22dc34139f5b7a3fcb5127e6c3063c048c53eb96d3ad9` |
| ios/migracja.md | `7d37af76ab0ebacd3138cad58b9e3dc38c27472bbba3cee5d9abac2e352da160` |
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
| online-payments/getting-started/payment-overview.md | `images/paybm-sequences-trx-paywall.png` | Brak pliku; TODO |
| online-payments/transaction-data/statuses.md | `images/obraz2.png` | Brak pliku; TODO |
| online-payments/advanced-flows/merchant-confirmation.md | `images/paybm-sequences-2pv.png` | Brak pliku; TODO |
| online-payments/advanced-flows/card-preauthorization.md | `images/SchematA.png` | Brak pliku; TODO |
| online-payments/advanced-flows/card-preauthorization.md | `images/SchematB.png` | Brak pliku; TODO |
| online-payments/advanced-flows/card-preauthorization.md | `images/SchematC.png` | Brak pliku; TODO |
| online-payments/advanced-flows/card-preauthorization.md | `images/SchematD.png` | Brak pliku; TODO |
| online-payments/advanced-flows/card-preauthorization.md | `images/SchematE.png` | Brak pliku; TODO |
| online-payments/advanced-flows/card-preauthorization.md | `images/SchematF.png` | Brak pliku; TODO |
| online-payments/payment-methods/google-pay.md | `images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-empty-example-page-start.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-loaded.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-invalid.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-ready.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-dcc.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-dcc-invalid.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-dcc-rejected.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-example-page-clicked.png` | Brak pliku; TODO |
| whitelabel/widget/cards.md | `images/widget-card-diagram-flow.svg` | Brak pliku; TODO |
| whitelabel/widget/visa-mobile.md | `images/widget-empty-example-page-start.png` | Brak pliku; TODO |
| whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-example-page-loaded.png` | Brak pliku; TODO |
| whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-example-page-ready.png` | Brak pliku; TODO |
| whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-example-page-clicked.png` | Brak pliku; TODO |
| whitelabel/widget/visa-mobile.md | `images/widget-visa-mobile-diagram-flow.svg` | Brak pliku; TODO |
| online-payments/advanced-flows/recurring-payments.md | `images/aktywacja_płatności_automatycznej.png` | Brak pliku; TODO |
| online-payments/advanced-flows/recurring-payments.md | `images/obciążenie_dla_płatności_automatycznej.png` | Brak pliku; TODO |
| online-payments/advanced-flows/recurring-payments.md | `images/dezaktywacja_usługi.png` | Brak pliku; TODO |
| online-payments/api-operations/balance.md | `images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png` | Brak pliku; TODO |
| online-payments/api-operations/balance.md | `images/model_rozliczeń_transakcji_po_każdej_wpłacie.png` | Brak pliku; TODO |
| online-payments/api-operations/balance.md | `images/model_rozliczeń_transakcji_na_żądanie.png` | Brak pliku; TODO |
| whitelabel/payment-method-selection.md | `images/paybm-sequences-trx-whiteLabel.png` | Brak pliku; TODO |

## TODO

| ID | Strona | Źródło | Problem | Wymagana decyzja/materiał |
| --- | --- | --- | --- | --- |
| MIG-001 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „O wtyczce”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-002 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Wymagania”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-003 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Instalacja”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-004 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Konfiguracja”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-005 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Dostępne metody płatności”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-006 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Aktualizacja”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-007 | plugins/prestashop.md | Brak załączonej dokumentacji PrestaShop | Brak materiału źródłowego dla sekcji „Rozwiązywanie problemów”. | Dostarczyć instrukcję wtyczki PrestaShop dla tej sekcji. |
| MIG-008 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „O wtyczce”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-009 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Wymagania”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-010 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Instalacja”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-011 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Konfiguracja”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-012 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Dostępne metody płatności”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-013 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Aktualizacja”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-014 | plugins/woocommerce.md | Brak załączonej dokumentacji WooCommerce | Brak materiału źródłowego dla sekcji „Rozwiązywanie problemów”. | Dostarczyć instrukcję wtyczki WooCommerce dla tej sekcji. |
| MIG-015 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „O wtyczce”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-016 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Wymagania”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-017 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Instalacja”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-018 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Konfiguracja”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-019 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Dostępne metody płatności”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-020 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Aktualizacja”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-021 | plugins/magento.md | Brak załączonej dokumentacji Magento | Brak materiału źródłowego dla sekcji „Rozwiązywanie problemów”. | Dostarczyć instrukcję wtyczki Magento dla tej sekcji. |
| MIG-022 | additional-information/changelog.md | Dostarczone materiały | Nie dostarczono potwierdzonej historii zmian bramki i WhiteLabel. | Dostarczyć historię wersji; nie przenosić historii SDK do historii bramki. |
| MIG-023 | mobile-sdk/README.md | android/README.md, ios/README.md | Gałęzie pozostają w android/ i ios/: przeniesienie zerwałoby odwołania ../.gitbook/assets/ i ../token.md. | Ewentualne przeniesienie wymaga osobnej decyzji o zmianie chronionych odwołań. |
| MIG-024 | online-payments/notifications/ipn.md | README-2.md:3539–3542 | Źródło opisuje konfigurację IPN wyłącznie na poziomie Punktów Rozliczeń. Nie ustalono samodzielnego wariantu dla zwykłego serwisu. | Potwierdzić dostępność i konfigurację IPN poza wyłączonym modelem. |
| MIG-025 | online-payments/transaction-data/product-basket.md | README-2.md:3272–3312 | Opis MASS_TRANSFER wiąże rozliczenia produktów z Punktami Rozliczeń; nie przeniesiono niejednoznacznego wariantu. | Potwierdzić niezależny od Punktów Rozliczeń zakres parametrów rozliczania produktów. |
| MIG-026 | online-payments/api-operations/balance.md | README-2.md:5158–5180 | Modele rozliczeń produktów mają niejasną granicę zakresu. Dla transactionSettlement źródło podaje wyłącznie nazwę operacji. | Dostarczyć niezależny opis modeli produktów i kompletny kontrakt transactionSettlement. |
| MIG-027 | online-payments/advanced-flows/merchant-confirmation.md | README-2.md:1003,1013,1041 | Opis wskazuje końcowy paymentStatus=CONFIRMED, tabela odpowiedzi wskazuje SUCCESS. Zachowano oba opisy. | Potwierdzić końcowy status ITN i pełną listę statusów dla tego modelu. |
| MIG-028 | online-payments/notifications/rpan-rpdn.md | README-2.md:2967–2976 | Przykład RPDN zawiera otwarcie recurringData zamiast jego zamknięcia. Zachowano oryginalny XML. | Potwierdzić poprawny XML przed użyciem przykładu. |
| MIG-029 | online-payments/advanced-flows/pretransaction.md | README-2.md:1313–1323 | Przykład PHP zawiera typograficzne cudzysłowy OrderID i Hash bez cudzysłowów. | Potwierdzić poprawny kod; nie uruchamiać przykładu bez korekty uzgodnionej z właścicielem API. |
| MIG-030 | online-payments/api-operations/legal-consents.md | README-2.md:3977,4013 | Opis regulationID używa isCheckboxRequired, tabela i przykład checkboxRequired; w przykładzie JSON brakuje przecinka po inputLabel. | Potwierdzić nazwę pola i poprawny JSON. |
| MIG-031 | online-payments/api-operations/gateway-list.md | README-2.md:3740–3890 | Tabela używa gatewayID i iconUrl; przykład ma także id i iconURL. Opisy currencies wspominają Hash odpowiedzi, choć przykład go nie zawiera. | Potwierdzić nazwy pól i zakres podpisywania odpowiedzi v3. |
| MIG-032 | online-payments/transaction-data/parameters.md | README-2.md:415,444–450,1546 | CustomerEmail jest wymagany w tabeli, lecz pominięty w liście i przykładzie startu. Opisy unikalności OrderID różnią się między startem i przedtransakcją. | Potwierdzić wymagalność CustomerEmail i reguły ponawiania OrderID; zachowano oba warianty. |
| MIG-033 | online-payments/advanced-flows/card-preauthorization.md | README-2.md:1135,1150–1184 | Products zawiera idBalancePoint we wspólnej definicji. Przykład odpowiedzi balancePayoff nie odpowiada tabeli confirmation/reason. | Potwierdzić kontrakt transactionClear i zakres koszyka; zachowano wspólną definicję bez usuwania pól. |
| MIG-034 | online-payments/notifications/itn.md | README-2.md:3361–3469 | Wspólny przykład ITN/IPN zawiera idBalancePoint. Tabela powtarza verificationStatus dla powodu weryfikacji, przykład używa verificationStatusReason; validityMonth ma długość 4 mimo formatu mm. | Potwierdzić wariant wspólny i nazwy/typy pól. Definicji ani przykładu nie okrojono; nie traktować tej części jako zweryfikowanej specyfikacji. |
| MIG-035 | online-payments/api-operations/balance.md | README-2.md:4094–4156 | Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i zależne reguły Hash. Zachowano je, pomijając odrębny przykład Punktu Rozliczeń. | Potwierdzić samodzielny kontrakt dla ServiceID; wariant Punktów Rozliczeń pozostaje poza zakresem wdrożenia. |
| MIG-036 | online-payments/api-operations/payouts.md | README-2.md:4191–4258 | Wspólne tabele zawierają alternatywę ServiceID/BalancePointID i reguły klucza pełnomocnika. Zachowano kontrakt bez usuwania pól. | Potwierdzić samodzielny wariant dla ServiceID przed uznaniem tej części za gotową specyfikację. |
| MIG-037 | online-payments/api-operations/refunds.md | README-2.md:4298,4432–4461,4728–4832 | Wspólne definicje zwrotów i outDetails zawierają pola lub błędy Punktów Rozliczeń. Opis Amount starszej usługi mówi o wypłacie całego salda, mimo opisu zwrotu transakcji. | Potwierdzić semantykę Amount i samodzielny kontrakt ServiceID. Zachowano wspólne pola, błędy oraz przykłady, bez publikowania osobnej usługi Marketplace. |
| MIG-038 | additional-information/errors.md | README-2.md:5058–5064 oraz Zwrot transakcji V3 | Ogólny opis mówi o wszystkich błędach w XML, podczas gdy V3 opisuje błędy JSON. | Potwierdzić zakres ogólnego opisu; stosować specyfikację właściwej operacji. |
| MIG-039 | whitelabel/widget/cards.md | README-2.md:2259–2260 | Nie dostarczono odrębnego załącznika z kartami i scenariuszami testowymi. | Dostarczyć załącznik testowy widgetu kartowego. |
| MIG-040 | online-payments/getting-started/payment-overview.md | README-2.md: images/paybm-sequences-trx-paywall.png | Brak obrazu/diagramu: images/paybm-sequences-trx-paywall.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-041 | online-payments/transaction-data/statuses.md | README-2.md: images/obraz2.png | Brak obrazu/diagramu: images/obraz2.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-042 | online-payments/advanced-flows/merchant-confirmation.md | README-2.md: images/paybm-sequences-2pv.png | Brak obrazu/diagramu: images/paybm-sequences-2pv.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-043 | online-payments/advanced-flows/merchant-confirmation.md | README-2.md: odwołania #platnosc-z-potwierdzeniem-merchanta, #platnosc-z-potwierdzeniem-merchanta, #platnosc-z-potwierdzeniem-merchanta | Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. | Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą. |
| MIG-044 | online-payments/advanced-flows/card-preauthorization.md | README-2.md: images/SchematA.png | Brak obrazu/diagramu: images/SchematA.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-045 | online-payments/advanced-flows/card-preauthorization.md | README-2.md: images/SchematB.png | Brak obrazu/diagramu: images/SchematB.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-046 | online-payments/advanced-flows/card-preauthorization.md | README-2.md: images/SchematC.png | Brak obrazu/diagramu: images/SchematC.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-047 | online-payments/advanced-flows/card-preauthorization.md | README-2.md: images/SchematD.png | Brak obrazu/diagramu: images/SchematD.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-048 | online-payments/advanced-flows/card-preauthorization.md | README-2.md: images/SchematE.png | Brak obrazu/diagramu: images/SchematE.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-049 | online-payments/advanced-flows/card-preauthorization.md | README-2.md: images/SchematF.png | Brak obrazu/diagramu: images/SchematF.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-050 | online-payments/payment-methods/google-pay.md | README-2.md: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png | Brak obrazu/diagramu: images/szczegółowy_schemat_komunikacji_i_wymiany_danych.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-051 | whitelabel/widget/cards.md | README-2.md: images/widget-empty-example-page-start.png | Brak obrazu/diagramu: images/widget-empty-example-page-start.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-052 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-loaded.png | Brak obrazu/diagramu: images/widget-card-example-page-loaded.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-053 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-invalid.png | Brak obrazu/diagramu: images/widget-card-example-page-invalid.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-054 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-ready.png | Brak obrazu/diagramu: images/widget-card-example-page-ready.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-055 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-dcc.png | Brak obrazu/diagramu: images/widget-card-example-page-dcc.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-056 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-dcc-invalid.png | Brak obrazu/diagramu: images/widget-card-example-page-dcc-invalid.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-057 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-dcc-rejected.png | Brak obrazu/diagramu: images/widget-card-example-page-dcc-rejected.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-058 | whitelabel/widget/cards.md | README-2.md: images/widget-card-example-page-clicked.png | Brak obrazu/diagramu: images/widget-card-example-page-clicked.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-059 | whitelabel/widget/cards.md | README-2.md: images/widget-card-diagram-flow.svg | Brak obrazu/diagramu: images/widget-card-diagram-flow.svg. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-060 | whitelabel/widget/visa-mobile.md | README-2.md: images/widget-empty-example-page-start.png | Brak obrazu/diagramu: images/widget-empty-example-page-start.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-061 | whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-example-page-loaded.png | Brak obrazu/diagramu: images/widget-visa-mobile-example-page-loaded.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-062 | whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-example-page-ready.png | Brak obrazu/diagramu: images/widget-visa-mobile-example-page-ready.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-063 | whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-example-page-clicked.png | Brak obrazu/diagramu: images/widget-visa-mobile-example-page-clicked.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-064 | whitelabel/widget/visa-mobile.md | README-2.md: images/widget-visa-mobile-diagram-flow.svg | Brak obrazu/diagramu: images/widget-visa-mobile-diagram-flow.svg. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-065 | online-payments/advanced-flows/recurring-payments.md | README-2.md: images/aktywacja_płatności_automatycznej.png | Brak obrazu/diagramu: images/aktywacja_płatności_automatycznej.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-066 | online-payments/advanced-flows/recurring-payments.md | README-2.md: images/obciążenie_dla_płatności_automatycznej.png | Brak obrazu/diagramu: images/obciążenie_dla_płatności_automatycznej.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-067 | online-payments/advanced-flows/recurring-payments.md | README-2.md: images/dezaktywacja_usługi.png | Brak obrazu/diagramu: images/dezaktywacja_usługi.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-068 | online-payments/advanced-flows/recurring-payments.md | README-2.md: odwołania #pobranie-informacji-o-aktualnej-liście-dostępnych-regulaminów---getlegaldata | Odwołania źródłowe nie mają jednoznacznego celu w zakresie migracji. | Potwierdzić zależność techniczną i właściwy cel; nie zastąpiono jej domyślnie inną usługą. |
| MIG-069 | online-payments/api-operations/balance.md | README-2.md: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png | Brak obrazu/diagramu: images/model_rozliczeń_zbiorczych_transakcji__model_domyślny_.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-070 | online-payments/api-operations/balance.md | README-2.md: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png | Brak obrazu/diagramu: images/model_rozliczeń_transakcji_po_każdej_wpłacie.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-071 | online-payments/api-operations/balance.md | README-2.md: images/model_rozliczeń_transakcji_na_żądanie.png | Brak obrazu/diagramu: images/model_rozliczeń_transakcji_na_żądanie.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |
| MIG-072 | whitelabel/payment-method-selection.md | README-2.md: images/paybm-sequences-trx-whiteLabel.png | Brak obrazu/diagramu: images/paybm-sequences-trx-whiteLabel.png. | Dostarczyć oryginalny plik; nie zastępować wygenerowanym diagramem. |

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
