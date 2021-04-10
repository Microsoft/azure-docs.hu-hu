---
title: Dokumentum fordításának beolvasása dokumentum állapotának módszere
titleSuffix: Azure Cognitive Services
description: A dokumentum beolvasása állapot metódus egy adott dokumentum állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 79bc3d076c1a7e164cab9c3231b29be84370e04a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613060"
---
# <a name="document-translation-get-document-status"></a>Dokumentum fordítása: dokumentumok állapotának lekérése

A dokumentum beolvasása állapot metódus egy adott dokumentum állapotát adja vissza. A metódus egy adott dokumentum fordítási állapotát adja vissza a kérelem azonosítója és a dokumentum azonosítója alapján.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `GET` a következőnek:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents/{documentId}
```

Megtudhatja, hogyan keresheti meg az [Egyéni tartománynevet](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **A dokumentum-fordítási szolgáltatáshoz tartozó összes API-kérelemhez egyéni tartományi végpont szükséges**.
> * Nem használhatja a végpontot a Azure Portal erőforrás- _kulcsok és a végpontok_ oldalán, sem a globális Translator végponton –, `api.cognitive.microsofttranslator.com` hogy http-kéréseket lehessen felvenni a dokumentumok fordítására.

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

|Lekérdezési paraméter|Kötelező|Leírás|
|--- |--- |--- |
|documentId|Igaz|A dokumentum azonosítója.|
|id|Igaz|A Batch-azonosító.|
## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Leírás|
|--- |--- |
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Leírás|
|--- |--- |
|200|OK gombra. Sikeres kérés, és a szolgáltatás fogadja el. A rendszer visszaadja a művelet részleteit. HeadersRetry-After: integerETag: string|
|401|Jogosulatlan. A hitelesítő adatok ellenőrzéséhez.|
|404|Nem található. Az erőforrás nem található.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|

## <a name="get-document-status-response"></a>Dokumentum állapotának lekérése válasz

### <a name="successful-get-document-status-response"></a>Sikerült lekérni a dokumentum állapotának válaszát

|Név|Típus|Leírás|
|--- |--- |--- |
|path|sztring|A dokumentum vagy mappa helye.|
|createdDateTimeUtc|sztring|A művelet létrehozásának dátuma és időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma és időpontja.|
|status|Sztring|A feladatok vagy dokumentumok lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Megszakítása</li><li>Sikertelen</li><li>NotStarted</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed</li></ul>|
|felhasználóként a(z)|sztring|Két betűs nyelvi kód a nyelvhez. Tekintse meg a nyelvek listáját.|
|progress|szám|A fordítás előrehaladása, ha elérhető|
|id|sztring|Dokumentum azonosítója.|
|characterCharged|egész szám|Az API által felszámított karakterek.|

### <a name="error-response"></a>Hiba válasza

|Név|Típus|Leírás|
|--- |--- |--- |
|code|sztring|A magas szintű hibakódokat tartalmazó enumerálások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hiba formátuma, amely megfelel a Cognitive Services API-irányelveknek. Tartalmazza a szükséges tulajdonságokat ErrorCode, üzenetet és opcionális tulajdonságok célját, részleteit (Key Value Pair), belső hibát (beágyazható).|
|innerError. code|sztring|Hibakód-karakterlánc beolvasása.|
|innerError. Message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra
A következő JSON-objektum egy példa a sikeres válaszra.

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

### <a name="example-error-response"></a>Példa a hiba válaszára

A következő JSON-objektum például egy hibaüzenetre adott válasz. A többi hibakód sémája ugyanaz.

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

A gyors üzembe helyezéssel kapcsolatos további információkért tekintse meg a dokumentumok fordításának és az ügyféloldali kódtár használatának lépéseit.

> [!div class="nextstepaction"]
> [Ismerkedés a dokumentumok fordításával](../get-started-with-document-translation.md)
