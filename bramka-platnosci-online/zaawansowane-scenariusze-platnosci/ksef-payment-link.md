# Link do płatności z faktury KSeF (IPKSeF)

## Link do płatności z faktury KSeF

{% hint style="danger" %}
{% hint style="warning" %}
Jeżeli interesuje Cię usługa, zgłoś się do swojego opiekuna biznesowego. Konieczna będzie rozbudowa struktury raportów rozliczeniowych o parametr IPKSeF, który pojawi się w ostatniej kolumnie każdej transakcji opłaconej za pomocą linku IPKSeF (umieszczonego na fakturze KSeF).
{% endhint %}
{% endhint %}

### Opis działania usługi

Alternatywą do samodzielnego przekazywania płatnikowi linku do bramki płatniczej może być dystrybucja linków do płatności poprzez fakturę KSeF. Standard KSeF przewiduje możliwość podania ustrukturyzowanego linku w dedykowanym do tego polu, który zostanie dostarczony odbiorcy na fakturze w sposób przejrzysty i łatwy do użycia. Płatnik po jego kliknięciu zostanie przekierowany na bramkę płatniczą w celu wyboru dogodnego kanału płatności i uregulowania należności. Sam proces płatności nie różni się dla płatnika od standardu Systemu płatności online, poza tym, że na każdym jego etapie płatnik jest informowany o identyfikatorze swojej płatności w KSeF.

### Wywołanie

Zamówienie linku do płatności za fakturę KSeF jest podobne do standardowej [Przedtransakcji](pretransaction.md). Różnice polegają na:

* zmianie (w żądaniu) hosta z _host\_bramki_ na _host\_ipksef_ (patrz [Środowisko testowe i produkcyjne](../../zacznij-tutaj/environments.md)), przy zachowaniu tej samej ścieżki, nagłówków itp.,
* oczekiwaniu (w odpowiedzi) linku IPKSeF zamiast linku do kontynuacji.

Przy zamawianiu linku IPKSeF obowiązują dodatkowe wymagania i ograniczenia parametrów. Należy pamiętać o:

* przekazaniu jawnej deklaracji zamówienia linku IPKSeF w dedykowanym parametrze **IPKSeFTransaction** (opis w części [Dodatkowe parametry](../dane-transakcji/additional-parameters.md#ipkseftransaction-param)),
* przekazaniu daty ważności linku do płatności w dedykowanym parametrze **IPKSeFLinkValidityTime** (opis w części [Dodatkowe parametry](../dane-transakcji/additional-parameters.md#ipkseflinkvaliditytime-param)),
* przekazaniu numeru opłacanego dokumentu finansowego w Serwisie (odpowiadającego fakturze przed jej wysłaniem do KSeF) w parametrze **InvoiceNumber**,
* niepodawaniu **GatewayID** (kanał płatności wybierze płatnik na bramce płatniczej),
* niepodawaniu **RecurringAction** o wartości innej niż INIT\_WITH\_PAYMENT,
* niepodawaniu **ValidityTime** i **LinkValidityTime** (ważność linku sterowana jest wyłącznie parametrem **IPKSeFLinkValidityTime**),
* niepodawaniu innych parametrów nieobsługiwanych w linkach IPKSeF (obciążenia są niedozwolone): **PaymentToken**, **AuthorizationCode**, **ClientHash**, **BlikUIDKey**, **BlikUIDLabel**, **BlikAMKey**, **TransactionSettlementMode**.

{% hint style="danger" %}
{% hint style="warning" %}
Dla danej faktury (**InvoiceNumber**) może powstać tylko jeden link do płatności KSeF, dlatego należy zadbać o unikalność pary parametrów **OrderID** i **InvoiceNumber** (w kontekście danego **ServiceID**). Próba ponownego użycia któregoś z tych parametrów do wygenerowania linku IPKSeF może zostać zablokowana (z błędem o strukturze podobnej do błędów zwracanych w [Przedtransakcji](pretransaction.md)).
{% endhint %}
{% endhint %}

### Odpowiedź – dane do faktury KSeF

W odpowiedzi na poprawne zamówienie linku IPKSeF System zwróci parę wartości: **IPKSeF** oraz **IPKSeFLink**, w poniższym formacie.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<IPKSeFDocument>
	<IPKSeF>{IPKSeF}</IPKSeF>
	<IPKSeFLink>https://{host_ipksef}/{id}?IPKSeF={IPKSeF}</IPKSeFLink>
</IPKSeFDocument>
```

Wartość **IPKSeFLink** należy umieścić w fakturze KSeF (przy bezpośredniej integracji jest to pole `linkDoPlatnosci`). Wartość **IPKSeF** należy umieścić w polu `identyfikatorPlatnosciKSeF` (ta wartość będzie się również automatycznie pojawiać w procesie płatności).
