# Rozpoczęcie transakcji



## Rozpoczęcie transakcji

### Opis rozpoczęcia transakcji

Serwis Partnera inicjując transakcję przekazuje do Systemu płatności
online parametry niezbędne do jej zrealizowania oraz późniejszego
przekazania statusu płatności.

Wszystkie parametry przekazywane są metodą POST (*Content-Type:
application/x-www-form-urlencoded*).

Protokół rozróżnia wielkość liter zarówno w nazwach jak i wartościach
parametrów. Wartości przekazywanych parametrów powinny być kodowane w
UTF-8 (oraz protokołem transportowym – zakodować przed wysłaniem, o ile
narzędzie wykorzystane do wysyłki komunikatu nie robi tego samodzielnie,
przykład kodowania: URLEncode).

## Sposób rozpoczęcia transakcji

Rozpoczęcie transakcji następuje przez przesłanie wywołaniem HTTPS
kombinacji powyższych parametrów na ustalony w trakcie rejestracji
usługi adres Systemu płatności online.

**UWAGA:** Liczba transakcji wystartowanych przez Partnera w ciągu
jednej minuty może wynieść maksymalnie 100, chyba że Partner i AP ustalą
wyższy limit w ramach zawartej umowy.

Przykład rozpoczęcia transakcji:

Adres:
- [https://{host_bramki}/sciezka](https://{host_bramki}/sciezka)

Parametry:
- ServiceID=2
- OrderID=100
- Amount=1.50

```text
  	Hash=2ab52e6918c6ad3b69a8228a2ab815f11ad58533eeed963dd990df8d8c3709d1
```

Przesłanie komunikatu bez wszystkich **wymaganych** parametrów
(**ServiceID, OrderID, Amount i Hash**) lub zawierającego błędne ich
wartości, spowoduje zatrzymanie procesu płatności wraz z podaniem kodu
błędu transakcji i krótką informacją o błędzie (brak powrotu na stronę
Serwisu Partnera).

**WAŻNE!**  "Para parametrów **ServiceID** i **OrderID** jednoznacznie
identyfikuje transakcję. Niedopuszczalne jest powtórzenie się wartości
parametru **OrderID** przez cały okres świadczenia usług przez System na
rzecz jednego Serwisu Partnera (**ServiceID**)."


Opcjonalny parametr **GatewayID** służy do określenia Kanału Płatności,
za pomocą którego ma zostać zrealizowana płatność. Pozwala to przenieść
ekran wyboru Kanałów Płatności do Serwisu. Aktualna lista
identyfikatorów Kanałów Płatności, wraz z logotypami, dostępna jest
poprzez metodę **gatewayList**.

Komunikat rozpoczęcia transakcji może być nadany w tle, tzn. bez
przekierowania użytkownika do Systemu płatności online. W tym modelu,
samego wyboru Kanału Płatności, Klient także dokonuje w Serwisie
Partnera.

[Pełna tabela parametrów startowych](../transaction-data/parameters.md) · [Dodatkowe parametry](../transaction-data/additional-parameters.md) · [Hash](../security/hashing.md)
