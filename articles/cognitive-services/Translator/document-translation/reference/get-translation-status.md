---
title: Fordítási állapot lekért állapota
titleSuffix: Azure Cognitive Services
description: A get translation status metódus egy dokumentumfordítási kérelem állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 8b129974396e420948737c9bdf47a5707decab6b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836213"
---
# <a name="get-translation-status"></a>Fordítási állapot lekért állapota

A Get translation status metódus egy dokumentumfordítási kérelem állapotát adja vissza. Az állapot tartalmazza a kérelem általános állapotát és a kérés részeként lefordított dokumentumok állapotát.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Megtudhatja, hogyan találhatja meg [az egyéni tartománynevet.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **A Document Translation szolgáltatásnak minden API-kéréséhez egyéni tartományvégpontra van szükség.**
> * Nem használhatja az erőforráskulcsok és Azure Portal  oldalán található végpontot, sem a globális fordítóvégpontot – a Dokumentumfordítás szolgáltatásnak `api.cognitive.microsofttranslator.com` való HTTP-kérések igénylésére.


## <a name="request-parameters"></a>Kérelemparaméterek

A lekérdezési sztringen átadott kérelemparaméterek a következőek:

|Lekérdezési paraméter|Kötelező|Leírás|
|--- |--- |--- |
|id|Igaz|A művelet azonosítója.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérések által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. Sikeres kérés, és a kötegelt fordítási művelet állapotát adja vissza. HeadersRetry-After: integerETag: sztring|
|401|Jogosulatlan. Ellenőrizze a hitelesítő adatait.|
|404|Az erőforrás nem található.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="get-translation-status-response"></a>Fordítási állapot lekért válasza

### <a name="successful-get-translation-status-response"></a>Sikeres fordítási állapot lekért válasza

A sikeres válasz az alábbi adatokat tartalmazza.

|Név|Típus|Description|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|Művelet létrehozási dátumának időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma.|
|status|Sztring|Feladat vagy dokumentum lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Érvénytelenítés</li><li>Sikertelen</li><li>NotStarted (Nincs indítás)</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed (Érvényesítési hiba)</li></ul>|
|összegzés|StatusSummary (Állapot állapota)|Az alább felsorolt részleteket tartalmazó összegzés.|
|summary.total|egész szám|Összesített szám.|
|summary.failed|egész szám|Sikertelenek száma.|
|summary.success|egész szám|A sikeresek száma.|
|summary.inProgress (összegzés.inProgress)|egész szám|A folyamatban lévők száma.|
|summary.notYetStarted|egész szám|A még el nem indítottak száma.|
|summary.cancelled|egész szám|A lemondottak száma.|
|summary.totalCharacterCharged|egész szám|Az API által felszámított karakterek teljes száma.|

###<a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|Cél|sztring|Lekérte a hiba forrását. Ez lehet például "documents" (dokumentumok) vagy "document id" (dokumentumazonosító) érvénytelen dokumentum esetén.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza az ErrorCode, az üzenet és az opcionális céltulajdonságok kötelező tulajdonságait, a részleteket (kulcs-érték párt), a belső hibát (beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

Az alábbi JSON-objektum egy példa a sikeres válaszra.

```JSON
{
  "id": "727bf148-f327-47a0-9481-abae6362f11e",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Succeeded",
  "summary": {
    "total": 10,
    "failed": 1,
    "success": 9,
    "inProgress": 0,
    "notYetStarted": 0,
    "cancelled": 0,
    "totalCharacterCharged": 0
  }
}
```

### <a name="example-error-response"></a>Példa hibaválaszra

Az alábbi JSON-objektum egy példa egy hibaválaszra. A többi hibakód sémája ugyanaz.

Állapotkód: 401

```JSON
{
  "error": {
    "code": "Unauthorized",
    "message": "User is not authorized",
    "target": "Document",
    "innerError": {
      "code": "Unauthorized",
      "message": "Operation is not authorized"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

A dokumentumfordítás és az ügyféloldali kódtár használatával kapcsolatos további információkért kövesse a rövid útmutatót.

> [!div class="nextstepaction"]
> [A dokumentumfordítás első lépések](../get-started-with-document-translation.md)
