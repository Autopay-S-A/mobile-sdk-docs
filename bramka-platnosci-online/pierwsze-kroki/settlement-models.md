# Modele rozliczeń

Model rozliczeń określa, kiedy i w jaki sposób Autopay przekazuje Partnerowi środki z opłaconych transakcji.

## Model rozliczeń zbiorczych transakcji (model domyślny)

Rozliczenia zbiorcze następują następnego dnia roboczego (D+1).

```mermaid
sequenceDiagram
    autonumber
    participant K as Klient
    participant P as Serwis Partnera
    participant S as System Płatności Online
    participant KP as Kanał Płatności

    K->>P: Płatność w Serwisie Partnera
    P->>S: Rozpoczęcie transakcji
    S->>KP: Wybór Kanału Płatności
    KP-->>KP: Autoryzacja płatności
    KP->>S: Powrót z Kanału Płatności
    S->>P: Powrót (przekierowanie) na stronę Serwisu Partnera
    KP-)S: Status płatności
    S-)P: ITN – powiadomienie o zmianie statusu transakcji wejściowej
    S-)K: Mail z potwierdzeniem płatności
    P->>K: Realizacja usługi (dla pozytywnego statusu ITN)
    Note over K,KP: Rozliczenie i raport zbiorczy w D+1
    S-)P: Przelew rozliczeniowy wszystkich transakcji z dnia D
    Note over K,KP: Miesięczna faktura
    S-)P: Faktura oraz raport miesięczny
```

## Model rozliczeń transakcji po każdej wpłacie

Rozliczenia po każdej wpłacie wykonywane mogą być niezwłocznie po otrzymaniu wpłaty od Klienta na wskazane w parametrach Linka płatności dane transakcji (opcje: Konto odbiorcy, Tytuł przelewu rozliczeniowego, Nazwa odbiorcy przelewu rozliczeniowego).

```mermaid
sequenceDiagram
    autonumber
    participant K as Klient
    participant P as Serwis Partnera
    participant S as System Płatności Online
    participant KP as Kanał Płatności

    K->>P: Płatność w Serwisie Partnera
    P->>S: Rozpoczęcie transakcji
    S->>KP: Wybór Kanału Płatności
    KP-->>KP: Autoryzacja płatności
    KP->>S: Powrót z Kanału Płatności
    S->>P: Powrót (przekierowanie) na stronę Serwisu Partnera
    KP-)S: Status płatności
    S-)P: ITN – powiadomienie o zmianie statusu transakcji wejściowej
    S-)K: Mail z potwierdzeniem płatności
    P->>K: Realizacja usługi (dla pozytywnego statusu ITN)
    S-)P: Przelew rozliczeniowy transakcji
    Note over K,KP: Raport zbiorczy w D+1
    S-)P: Raport zbiorczy
    Note over K,KP: Miesięczna faktura
    S-)P: Faktura oraz raport miesięczny
```

## Model rozliczeń transakcji na żądanie

Rozliczenia mogą być zlecane przez Partnera poprzez wywołanie metody: **transactionSettlement**.

<!-- TODO MIG-026: Brak kontraktu metody transactionSettlement (parametry, odpowiedź, Hash). Do ustalenia na spotkaniu z analitykami. -->

{% hint style="warning" %}
Szczegóły wywołania metody **transactionSettlement** ustala się z opiekunem biznesowym w trakcie integracji.
{% endhint %}

```mermaid
sequenceDiagram
    autonumber
    participant K as Klient
    participant P as Serwis Partnera
    participant S as System Płatności Online
    participant KP as Kanał Płatności

    K->>P: Płatność w Serwisie Partnera
    P->>S: Rozpoczęcie transakcji
    S->>KP: Wybór Kanału Płatności
    KP-->>KP: Autoryzacja płatności
    KP->>S: Powrót z Kanału Płatności
    S->>P: Powrót (przekierowanie) na stronę Serwisu Partnera
    KP-)S: Status płatności
    S-)P: ITN – powiadomienie o zmianie statusu transakcji wejściowej
    S-)K: Mail z potwierdzeniem płatności
    P->>K: Realizacja usługi (dla pozytywnego statusu ITN)
    Note over K,KP: Rozliczenie w ustalonym czasie od uzyskania statusu płatności (np. nie później niż 3 dni)
    P->>S: Zlecenie rozliczenia (transactionSettlement)
    S-->>P: Potwierdzenie przyjęcia zlecenia
    S-)P: Przelew rozliczeniowy transakcji
    Note over K,KP: Raport zbiorczy w D+1
    S-)P: Raport zbiorczy
    Note over K,KP: Miesięczna faktura
    S-)P: Faktura oraz raport miesięczny
```
