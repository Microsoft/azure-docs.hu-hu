---
title: Dokumentum fordításának lekérési műveletének állapota
titleSuffix: Azure Cognitive Services
description: A lekérési művelet állapota metódus egy dokumentum-fordítási kérelem állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: e8aa9b93dc089cc05dd5157a7ac84cceb5f6fcd5
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613020"
---
# <a name="document-translation-get-operation-status"></a>Dokumentum fordítása: művelet állapotának beolvasása

A lekérési művelet dokumentumai status metódus visszaadja egy dokumentum-fordítási kérelem állapotát. Az állapot tartalmazza a kérelem teljes állapotát és a lefordított dokumentumok állapotát a kérelem részeként.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `GET` a következőnek:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
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
|id|Igaz|A művelet azonosítója.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. Sikeres kérelem, és visszaadja a Batch fordítási műveletének állapotát. HeadersRetry-After: integerETag: string|
|401|Jogosulatlan. A hitelesítő adatok ellenőrzéséhez.|
|404|Az erőforrás nem található.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|

## <a name="get-operation-status-response"></a>Művelet állapotának lekérése válasz

### <a name="successful-get-operation-status-response"></a>Sikeres lekérési művelet állapotának válasza

A sikeres válasz a következő információkat adja vissza.

|Név|Típus|Description|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|A művelet létrehozásának dátuma és időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma és időpontja.|
|status|Sztring|A feladatok vagy dokumentumok lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Megszakítása</li><li>Sikertelen</li><li>NotStarted</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed</li></ul>|
|összegzés|StatusSummary|Az alább felsorolt részleteket tartalmazó összefoglalás.|
|összefoglalás. Total|egész szám|Összesített szám.|
|összefoglalás. sikertelen|egész szám|Sikertelen darabszám.|
|összefoglalás. sikeres|egész szám|Sikeresek száma.|
|összefoglalás. inelőrehaladás|egész szám|A folyamatban lévők száma.|
|összefoglalás. notYetStarted|egész szám|A még nem elindított darabszámok száma.|
|összefoglalás. megszakítva|egész szám|A megszakított szám.|
|összefoglalás. totalCharacterCharged|egész szám|Az API által felszámított összes karakter.|

###<a name="error-response"></a>Hiba válasza

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|A magas szintű hibakódokat tartalmazó enumerálások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|cél|sztring|Lekéri a hiba forrását. Egy érvénytelen dokumentum esetében például a "Documents" vagy a "Document id" lenne.|
|innerError|InnerErrorV2|Új belső hiba formátuma, amely megfelel a Cognitive Services API-irányelveknek. Tartalmazza a szükséges tulajdonságokat ErrorCode, üzenetet és opcionális tulajdonságok célját, részleteit (Key Value Pair), belső hibát (beágyazható).|
|innerError. code|sztring|Hibakód-karakterlánc beolvasása.|
|innerError. Message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

A következő JSON-objektum egy példa a sikeres válaszra.

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
