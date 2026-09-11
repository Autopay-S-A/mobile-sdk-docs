# Powrót klienta do serwisu



## Przekierowanie do Serwisu Partnera

### Opis przekierowania do Serwisu Partnera

Niezwłocznie po zakończeniu autoryzacji transakcji przez Klienta jest on
przekierowywany z witryny Kanału Płatności na witrynę Systemu płatności
online, gdzie następuje automatyczne przekierowanie Klienta do Serwisu
Partnera.

Przekierowanie realizowane jest poprzez wysłanie żądania HTTPS (metodą
GET) pod ustalony wcześniej adres powrotu w Serwisie Partnera. Protokół
rozróżnia wielkość liter zarówno w nazwach jak i wartościach parametrów.

### Lista parametrów przekierowania do Serwisu Partnera

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich
numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | OrderID | TAK | string{1,32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


Przykład komunikatu przekierowującego Klienta z Systemu płatności online do Serwisu Partnera

```text
	https://sklep_nazwa/strona_powrotu?ServiceID=123458&OrderID=123402816&Hash=5432d69a66d721b2f5f785432bf5a1fc1b913bdb3bba465856a5c228fe95c1f8
```

## Strona podsumowania transakcji

### Opis

System umożliwia wyświetlanie Klientowi podsumowania transakcji. W tym
celu Partner może budować linki uruchamiające z odpowiednimi parametrami
metodę **confirmation** ([https://{host_bramki}/web/confirmation/payment](https://{host_bramki}/web/confirmation/payment)).
Wszystkie parametry przekazywane są metodą GET. Protokół rozróżnia
wielkość liter zarówno w nazwach jak i wartościach parametrów. Wartości
przekazywanych parametrów powinny być kodowane w UTF-8.

### Lista parametrów dla metody podsumowania transakcji

Przekierowanie Klienta z poprawnymi parametrami spowoduje wyświetlenie
podsumowania transakcji (o treści zależnej od jej stanu) lub informacji
o jej braku (jeśli System nie jej nie odnajdzie).

**WAŻNE!** Kolejność atrybutów do wyliczenia Hash musi być zgodna z ich numeracją.

| kolejność Hash | nazwa | wymagany | typ | opis |
| --- | --- | --- | --- | --- |
| 1 | ServiceID | TAK | string{1,10} | Identyfikator Serwisu Partnera. |
| 2 | OrderID | TAK | string{32} | Identyfikator transakcji nadany w Serwisie Partnera i przekazany w starcie transakcji. |
| nd. | Hash | TAK | string{1,128} | Wartość funkcji skrótu dla komunikatu obliczona zgodnie z opisem w części [Bezpieczeństwo transakcji](../security/hashing.md#bezpieczeństwo-transakcji). **Obowiązkowa weryfikacja zgodności wyliczonego skrótu przez Serwis**. |


**UWAGA:** Usługa musi zostać aktywowana po uzgodnieniu z opiekunem
biznesowym. Istnieje możliwość zmiany treści komunikatów lub
dostosowanie szaty graficznej (podlegają one każdorazowo ustaleniom w
formie roboczej podczas integracji).
