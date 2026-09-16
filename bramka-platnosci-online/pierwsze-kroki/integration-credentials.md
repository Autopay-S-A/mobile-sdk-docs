# Dane potrzebne do integracji



## Kroki integracji obsługi transakcji i rozliczeń

### Dane wymagane podczas integracji obsługi transakcji i rozliczeń

Wymagane dane wymieniane podczas integracji różnią się dla środowiska
testowego i produkcyjnego. Poniżej lista parametrów przekazywanych przez
AP do Partnera oraz w kierunku odwrotnym.

Przekazywane są też informacje ogólne tj. aktywne kanały płatności wraz
z grafikami (w wyniku odpytywania o listę dostępnych kanałów płatności).

Opcjonalnie, mogą pojawić się dodatkowe dane przekazywane przez Partnera
do AP, na przykład: informacje o wymaganej zawartości koszyka i sposobie
przetwarzania go (w raportach, rozliczeniach, panelu administracyjnym),
dodatkowe wymagania (dla zasilenia salda przedpłaconego). Dla płatności
automatycznych BLIK również domyślna długość życia aktywowanych
płatności automatycznych oraz domyślna etykieta aktywowanych płatności
automatycznych.

### Dane wymieniane w środowisku testowym

#### Dane przekazywane przez Partnera do AP:

- Adres dla komunikatów ITN
- Adres dla komunikatów RPAN (może być ten sam, co dla komunikatów ITN) [dla płatności automatycznych\]
- Adres dla komunikatów RPDN (może być ten sam, co dla komunikatów ITN) [dla płatności automatycznych\]
- Adres powrotu z płatności (bez parametrów)

#### Dane przekazywane przez AP do Partnera:

- Adres Systemu płatności online
- ServiceID
- AcceptorID \[dla portfeli w modelu WhiteLabel\]
- Klucz współdzielony
- Mechanizm funkcji skrótu
- Adres testowego formularza
- Adres IP, z którego wysyłane są ITNy
- Adres do panelu administracyjnego
- Login
- Hasło

### Dane przekazywane w środowisku produkcyjnym

#### Przez Partnera do AP:

- Adres dla komunikatów ITN
- Adres dla komunikatów RPAN (może być ten sam, co dla komunikatów ITN) [dla płatności automatycznych\]
- Adres dla komunikatów RPDN (może być ten sam, co dla komunikatów ITN) [dla płatności automatycznych\]
- Adres powrotu z płatności (bez parametrów)
- Adresy email dla raportów transakcyjnych
- Adresy email dla faktur i raportów rozliczeniowych
- Adresy email dla reklamacji (wysyłany w wiadomościach do płatników)

#### Przez AP do Partnera:

- Adres Systemu płatności online
- ServiceID
- AcceptorID \[dla portfeli w modelu WhiteLabel\]
- Klucz współdzielony
- Mechanizm funkcji skrótu
- Adres IP, z którego wysyłane są ITNy
- Adres do panelu administracyjnego
- Login
- Hasło
