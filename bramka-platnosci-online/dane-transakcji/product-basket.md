# Koszyk produktów

<!-- TODO MIG-025: Źródło: README-2.md:3272–3312. Problem: Opis MASS_TRANSFER wiąże rozliczenia produktów z Punktami Rozliczeń; nie przeniesiono niejednoznacznego wariantu. Wymagana decyzja/materiał: Potwierdzić niezależny od Punktów Rozliczeń zakres parametrów rozliczania produktów. -->

> TODO MIG-025: Opis MASS_TRANSFER wiąże rozliczenia produktów z Punktami Rozliczeń; nie przeniesiono niejednoznacznego wariantu. Potwierdzić niezależny od Punktów Rozliczeń zakres parametrów rozliczania produktów.

## Koszyk produktów

### Opis koszyka produktów

Koszyk produktów przesyłany jest jako parametr (metody POST) o nazwie
Products. Jego wartość jest zakodowana protokołem transportowym Base64.


Format przed zakodowaniem (XML)
```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<productList>
		<product>
			<subAmount>SubAmount1</subAmount>
			<params>Params1</params>
		</product>
		<product>
			<subAmount>SubAmount2</subAmount>
			<params>Params2</params>
		</product>
		…
		<product>
			<subAmount>SubAmountN</subAmount>
			<params>ParamsN</params>
		</product>
	</productList>
```

Węzeł **productList** musi zawierać przynajmniej 1 element **product**,
każdy węzeł **product** musi zawierać po jednym elemencie **subAmount**
i **params**.

Element **subAmount** musi zawierać dodatnią kwotę produktu (separatorem
dziesiętnym jest kropka, a po niej występują dwie cyfry groszy). Suma
kwot kolejnych produktów musi być równa kwocie podanej w parametrze
**Amount** (kwocie transakcji).

W przypadku niespełnienia powyższych warunków System zwróci błąd.


### Element Params

Element **params** może służyć do przekazywania informacji
charakterystycznych dla danego produktu. Nazwy parametrów oraz ich
znaczenie podlega każdorazowo uzgodnieniom w formie roboczej podczas
integracji.

Przykładowe parametry produktu i ich znaczenie poniżej.

-   W tym wypadku węzeł zawiera nazwę produktu:

```xml
		<params>
			<param name="productName" value="Nazwa produktu 1" />
		</params>
```

-   W tym wypadku węzeł zawiera dwie wartości przypisane do danego produktu, mogące oznaczać przykładowo typ produktu oraz jego nazwę:

```xml
		<params>
			<param name="productType" value="ABCD" />
			<param name="productName" value="Nazwa produktu 1" />
		</params>
```

-   W przypadku, gdy Serwis posiada saldo w Systemie, oraz planuje
    wykonywać zwroty do Klienta całości, bądź części kwoty wpłaconej na
    rzecz wskazanego produktu, zobowiązany jest przekazywać w produkcie
    jego **unikalny** identyfikator (parametr o nazwie productID o typie
    string{1,36} (Dopuszczalne alfanumeryczne znaki alfabetu łacińskiego
    oraz znaki: \_ i -)):

```xml
		<params>
			<param name="productID" value="12456" />
		</params>
```

## Wyświetlanie koszyka produktów na ekranie wyboru Kanału Płatności

Jeżeli w trakcie rozmów dotyczących koszyka produktów ustalono, że jego
podsumowanie ma zostać wyświetlone na stronie Systemu (ekran wyboru
Kanału Płatności), można określić etykiety każdego użytego w koszyku
parametru. System może użyć domyślnej etykiety parametru lub może
przyjąć ją w starcie transakcji.

Wartość atrybutu **title** zostanie wyświetlona przed wartością
parametru produktu.

Przykład atrybutu title

```xml
	<params>
		<param name="productName" value="Nazwa produktu 1" title="Nazwa"/>
		<param name="productType" value="ABCD" title="Typ"/>
	</params>
```
