# Synchronizacja dokumentacji WooCommerce

Workflow `sync-woocommerce-docs.yml` pobiera codziennie o 04:17 UTC plik
`README.md` z gałęzi `main` repozytorium
https://github.com/bluepayment-plugin/autopay-payments i zapisuje go jako
`wtyczki-e-commerce/woocommerce.md`, pod istniejącym wpisem WooCommerce w SUMMARY.md.
Harmonogram GitHub może uruchomić zadanie z opóźnieniem.

Po umieszczeniu workflow i skryptu na domyślnej gałęzi repozytorium harmonogram
będzie aktywny. Można też użyć Actions → Sync WooCommerce documentation → Run workflow.
Workflow zawsze zapisuje zmiany na domyślnej gałęzi. GitBook powinien synchronizować tę samą gałąź.

Workflow korzysta z wbudowanego GITHUB_TOKEN z uprawnieniem contents: write.
Polityka organizacji i ochrona gałęzi muszą dopuszczać taki zapis. Jeśli wymagane są
pull requesty, należy zmienić sposób dostarczania aktualizacji; workflow nie omija ochrony gałęzi.

Pobranie nieudane, pusty plik lub brak nagłówka H1 przerywa import przed nadpisaniem strony.
Commit powstaje tylko po zmianie treści. Zapis obejmuje wyłącznie stronę WooCommerce.
Nie jest wykonywany żaden kod z repozytorium wtyczki.

Dokumentację WooCommerce należy poprawiać w repozytorium wtyczki. Lokalne zmiany
tej strony zostaną zastąpione podczas następnej synchronizacji. Zachowywana jest
struktura README źródła. Względne linki Markdown, definicje referencyjne oraz
cytowane atrybuty HTML src/href są przekształcane na adresy repozytorium źródłowego.
Obrazy są udostępniane z raw.githubusercontent.com; nie są kopiowane do repozytorium dokumentacji.
Bloki kodu i kod inline nie są zmieniane. Niestandardowe konstrukcje, np. srcset,
nie są obecnie przekształcane.

Ręczny import pobranego pliku:

```sh
python3 scripts/sync_woocommerce_readme.py /tmp/woocommerce-README.md
```
