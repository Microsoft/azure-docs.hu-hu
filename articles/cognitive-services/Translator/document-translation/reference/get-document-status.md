---
title: Dokumentumállapot-metódus lekérte
titleSuffix: Azure Cognitive Services
description: A get document status metódus egy adott dokumentum állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 36cd10a0b04be21e9f332832b4381662eeedd01d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833669"
---
# <a name="get-document-status"></a>Dokumentum állapotának lekért állapota

A Get Document Status metódus egy adott dokumentum állapotát adja vissza. A metódus egy adott dokumentum fordítási állapotát adja vissza a kérés azonosítója és a dokumentum azonosítója alapján.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
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
|documentId (dokumentumazonosító)|Igaz|A dokumentum azonosítója.|
|id|Igaz|A köteg azonosítója.|
## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérések által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. Sikeres kérés, amelyet a szolgáltatás elfogad. A rendszer visszaadja a művelet részleteit. HeadersRetry-After: integerETag: sztring|
|401|Jogosulatlan. Ellenőrizze hitelesítő adatait.|
|404|Nem található. Az erőforrás nem található.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="get-document-status-response"></a>Dokumentumállapot lekért válasza

### <a name="successful-get-document-status-response"></a>Sikeres dokumentumállapot-lekért válasz

|Név|Típus|Description|
|--- |--- |--- |
|path|sztring|A dokumentum vagy mappa helye.|
|createdDateTimeUtc|sztring|Művelet létrehozási dátumának időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma.|
|status|Sztring|Feladat vagy dokumentum lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Érvénytelenítés</li><li>Sikertelen</li><li>NotStarted (Nincs indítás)</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed (Érvényesítési hiba)</li></ul>|
|felhasználóként a(z)|sztring|A To Language kétbetűs nyelvi kódja. Tekintse meg a nyelvek listáját.|
|progress|szám|A fordítás előrehaladása, ha elérhető|
|id|sztring|Dokumentumazonosító.|
|characterCharged (felöltött)|egész szám|Az API által felszámított karakterek.|

### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a szükséges ErrorCode, üzenet és opcionális tulajdonságok célértékét, a részleteket (kulcs-érték pár), a belső hibát (beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra
Az alábbi JSON-objektum egy példa a sikeres válaszra.

```JSON
{
  "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
  "createdDateTimeUtc": "2020-03-26T00:00:00Z",
  "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
  "status": "Running",
  "to": "fr",
  "progress": 0.1,
  "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
  "characterCharged": 0
}
```

### <a name="example-error-response"></a>Példa hibaválaszra

Az alábbi JSON-objektum egy példa egy hibaválaszra. A többi hibakód sémája megegyezik.

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
