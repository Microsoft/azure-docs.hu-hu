---
title: Ügyfelek hitelesítése az eseményeket Event Grid egyéni témakörök vagy tartományok számára
description: Ez a cikk különböző módszereket ismertet az ügyfelek közzétételi eseményeinek Event Grid egyéni témakörökbe való hitelesítéséhez.
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c415b7e1bb6bd7a2116da82c7d8f1de205009d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94886260"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>Közzétételi ügyfelek hitelesítése (Azure Event Grid)
Ez a cikk a **hozzáférési kulcs** vagy a **közös hozzáférésű aláírás (SAS)** token használatával Azure Event Grid témaköröket vagy tartományokat közzétevő ügyfelek hitelesítésével kapcsolatos információkat tartalmaz. A SAS-token használatát javasoljuk, de a kulcsos hitelesítés egyszerű programozást biztosít, és számos meglévő webhook-közzétevővel kompatibilis.  

## <a name="authenticate-using-an-access-key"></a>Hitelesítés Hívóbetű használatával
A hozzáférési kulcs hitelesítése a hitelesítés legegyszerűbb formája. A hozzáférési kulcsot HTTP-fejlécként vagy URL-lekérdezési paraméterként adhatja át. 

### <a name="access-key-in-a-http-header"></a>Hozzáférési kulcs egy HTTP-fejlécben
Adja át a hozzáférési kulcsot a HTTP-fejléc értékeként: `aeg-sas-key` .

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>Hozzáférési kulcs lekérdezési paraméterként
`aeg-sas-key`Lekérdezési paraméterként is megadható. 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

A témakörök vagy tartományok hozzáférési kulcsainak lekérésével kapcsolatos útmutatásért lásd: [hozzáférési kulcsok](get-access-keys.md)beolvasása.

## <a name="authenticate-using-a-sas-token"></a>Hitelesítés SAS-token használatával
Event Grid erőforrás SAS-jogkivonatai közé tartozik az erőforrás, a lejárati idő és az aláírás. Az SAS-token formátuma: `r={resource}&e={expiration}&s={signature}` .

Az erőforrás az Event Grid-témakör elérési útja, amelyhez eseményeket küld. Egy érvényes erőforrás elérési útja például a következő: `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` . Az összes támogatott API-verzió megtekintéséhez lásd: [Microsoft. EventGrid erőforrástípusok](/azure/templates/microsoft.eventgrid/allversions). 

Először programozott módon állítson elő SAS-tokent, majd használja a `aeg-sas-token` fejlécet vagy a `Authorization SharedAccessSignature` fejlécet a Event Grid való hitelesítéshez. 

### <a name="generate-sas-token-programmatically"></a>SAS-jogkivonat programozott módon történő előállítása
Az alábbi példa egy SAS-tokent hoz létre a Event Gridhoz való használatra:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

```python
def generate_sas_token(uri, key, expiry=3600):
    ttl = datetime.datetime.utcnow() + datetime.timedelta(seconds=expiry)
    encoded_resource = urllib.parse.quote_plus(uri)
    encoded_expiration_utc = urllib.parse.quote_plus(ttl.isoformat())

    unsigned_sas = f'r={encoded_resource}&e={encoded_expiration_utc}'
    signature = b64encode(HMAC(b64decode(key), unsigned_sas.encode('utf-8'), sha256).digest())
    encoded_signature = urllib.parse.quote_plus(signature)
    
    token = f'r={encoded_resource}&e={encoded_expiration_utc}&s={encoded_signature}'

    return token
```

### <a name="using-aeg-sas-token-header"></a>Az AEG-sas-token fejléc használata
Íme egy példa arra, hogy az SAS-tokent a fejléc értékeként adja át `aeg-sas-token` . 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Az engedélyezési fejléc használata
Íme egy példa arra, hogy az SAS-tokent a fejléc értékeként adja át `Authorization` . 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevents&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>Következő lépések
Az események kézbesítéséhez az eseménykezelővel való hitelesítés megismeréséhez tekintse meg az [esemény kézbesítésének hitelesítését](security-authentication.md) ismertető témakört. 
