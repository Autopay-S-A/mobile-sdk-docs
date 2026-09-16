# Wybór metody płatności po stronie merchanta

## Model WhiteLabel

Charakteryzuje się prezentacją kanałów płatności po stronie Merchanta. W tym celu należy pozyskać listę kanałów wraz z opisami z usługi `gatewayList/v3` oraz pozyskać listę niezbędnych regulaminów dla poszczególnych kanałów płatności (`/legalData`). Pozostała część procesu jest taka sama jak w modelu Paywall.

```mermaid
sequenceDiagram
    participant C as Klient
    participant M as Merchant
    participant A as Autopay
    participant P as Kanał Płatności

    C->>M: Inicjacja płatności (SID)
    M->>A: /gatewayList/v3
    A-->>M: lista kanałów płatności (json)
    C->>M: wybór kanału
    M->>A: /legalData
    A-->>M: wymagane regulaminy i zgody (json)
    C->>M: akceptacja wymaganych treści
    M->>A: /payment
    A->>P: start transakcji
    P-->>A: link do kontynuacji <br> (jeśli konieczne)
    A-->>M: link do kontynuacji <br> (jeśli konieczne)
    M-->>C: link do kontynuacji <br> (jeśli konieczne)
    C->>P: autoryzacja płatności
    P-->>A: powrót
    A-->>M: powrót
    M-->>C: powrót
    P->>A: status płatności
    A->>M: ITN ze statusem transakcji
    M-->>A: potwierdzenie ITN
```

[Specyfikacja gatewayList](../bramka-platnosci-online/pozostale-operacje-api/gateway-list.md) · [Regulaminy i zgody](../bramka-platnosci-online/pozostale-operacje-api/legal-consents.md) · [Przedtransakcja](../bramka-platnosci-online/zaawansowane-scenariusze-platnosci/pretransaction.md)
