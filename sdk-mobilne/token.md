---
description: Usługa pobrania tymczasowego Tokena (metoda Systemu)
---

# Skład wziąć token?

Interfejs potrzebny w wariancie rozszerzonym. Aby uchronić się przed kompromitacją\
klucza współdzielonego, można zamiast niego w Aplikacji stosować tymczasowy Token.\
Powinien on być dostarczany z backendu Aplikacji na żądanie. Standardowa ważność\
Tokena to 1h (wymaga to więc ponownej inicjalizacji obiektu config), nie zaleca się jego\
generowania częściej niż co pół godziny. W tym modelu backend jest jedynym miejscem\
posiadającym klucz współdzielony i używa go do generowania tymczasowych Tokenów,\
poprzez wywołanie (metodą GET) usługi getMacAccessToken. Protokół rozróżnia wielkość\
liter zarówno w nazwach jak i wartościach parametrów. Wartości przekazywanych\
parametrów powinny być kodowane w UTF-8. W przypadku poprawnej weryfikacji\
parametrów, zostanie zwrócony (w tej samej sesji HTTP) ciąg znaków (max 500 znaków),\
który można następnie użyć do autoryzacji żądań z Aplikacji. Poniżej lista oczekiwanych\
parametrów:

<table><thead><tr><th width="114.21875">Kolejność do HASH</th><th width="133.93359375">Nazwa</th><th width="115.26171875">Typ</th><th>Opis</th></tr></thead><tbody><tr><td>1</td><td>ServiceID</td><td>string{1,10}</td><td>Identyfikator Serwisu Partnera</td></tr><tr><td>2</td><td>MessageID</td><td>string{32}</td><td>Pseudolosowy identyfikator komunikatu o długości<br>32 znaków alfanumerycznych alfabetu łacińskiego<br>(np. na bazie UID), wartość pola musi być unikalna<br>dla Serwisu Partnera</td></tr><tr><td>99</td><td>Hash</td><td>string{1,128}</td><td>Wartość funkcji skrótu dla komunikatu obliczona<br>zgodnie z opisem w rozdziale <a href="https://developers.autopay.pl/online/dokumentacja#bezpiecze%C5%84stwo-transakcji">Bezpieczeństwo</a>.<br>Weryfikacja zgodności wyliczonego skrótu<br>przez Serwis Partnera jest obowiązkowa.</td></tr></tbody></table>

Przykładowa struktura wywołania metody:\
`https://domena_bramki/webapi/getMacAccessToken?ServiceID=&MessageID=&Hash=<Hash>`
