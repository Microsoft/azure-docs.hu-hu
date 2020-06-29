---
title: Ügyfelek hitelesítése az eseményeket Event Grid egyéni témakörök vagy tartományok számára
description: Ez a cikk különböző módszereket ismertet az ügyfelek közzétételi eseményeinek Event Grid egyéni témakörökbe való hitelesítéséhez.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 7805c9b9096cb52fcef3dad8eb0bdd9509cbce47
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414878"
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
https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01&&aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

A témakörök vagy tartományok hozzáférési kulcsainak lekérésével kapcsolatos útmutatásért lásd: [hozzáférési kulcsok](get-access-keys.md)beolvasása.

## <a name="authenticate-using-a-sas-token"></a>Hitelesítés SAS-token használatával
Event Grid erőforrás SAS-jogkivonatai közé tartozik az erőforrás, a lejárati idő és az aláírás. Az SAS-token formátuma: `r={resource}&e={expiration}&s={signature}` .

Az erőforrás az Event Grid-témakör elérési útja, amelyhez eseményeket küld. Egy érvényes erőforrás elérési útja például a következő: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events?api-version=2019-06-01` . Az összes támogatott API-verzió megtekintéséhez lásd: [Microsoft. EventGrid erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions). 

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

### <a name="using-aeg-sas-token-header"></a>Az AEG-sas-token fejléc használata
Íme egy példa arra, hogy az SAS-tokent a fejléc értékeként adja át `aeg-sas-toke` . 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Az engedélyezési fejléc használata
Íme egy példa arra, hogy az SAS-tokent a fejléc értékeként adja át `Authorization` . 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>További lépések
Az események kézbesítéséhez az eseménykezelővel való hitelesítés megismeréséhez tekintse meg az [esemény kézbesítésének hitelesítését](security-authentication.md) ismertető témakört. 