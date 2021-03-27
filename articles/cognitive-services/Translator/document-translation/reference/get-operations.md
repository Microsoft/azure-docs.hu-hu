---
title: Dokumentumok fordítása – lekérési műveletek
titleSuffix: Azure Cognitive Services
description: A Get Operations metódus az elküldött batch-kérelmek listáját és az egyes kérelmek állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c42f3081a831c267c7bc605267b99e2a916ea3d8
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613025"
---
# <a name="document-translation-get-operations"></a>Dokumentum fordítása: lekérési műveletek

A Get Operations metódus az elküldött batch-kérelmek listáját és az egyes kérelmek állapotát adja vissza. Ez a lista csak a felhasználó által küldött batch-kérelmeket tartalmazza (az előfizetés alapján). Az egyes kérelmek állapota az azonosító szerint van rendezve.

Ha a kérések száma meghaladja a lapozási korlátot, a rendszer kiszolgálóoldali lapozást használ. A többoldalas válaszok részleges eredményt jeleznek, és a válaszban egy folytatási tokent tartalmaznak. A folytatási token hiánya azt jelenti, hogy nincsenek elérhető további lapok.

$top és $skip lekérdezési paraméterekkel megadható a visszaadott eredmények száma és a gyűjtemény eltolása.

A kiszolgáló tiszteletben tartja az ügyfél által megadott értékeket. Az ügyfeleknek azonban elő kell készülniük a különböző oldalméret vagy folytatási tokent tartalmazó válaszok kezelésére.

Ha $top és $skip is szerepel, a kiszolgálónak először $skip kell alkalmaznia, majd $top a gyűjteményen. 

> [!NOTE]
> Ha a kiszolgáló nem tudja megbecsülni $top és/vagy $skip, a kiszolgálónak hibát kell visszaadnia az ügyfélnek, és nem csupán figyelmen kívül hagyja a lekérdezés beállításait. Ez csökkenti annak kockázatát, hogy az ügyfél feltételezi a visszaadott adatmennyiséget.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `GET` a következőnek:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|$skip|Hamis|Hagyja ki a gyűjtemény $skip bejegyzéseit. Ha a $top és a $skip is meg van adva, a rendszer először a $skip alkalmazza.|
|$top|Hamis|Végezze el a gyűjtemény $top bejegyzéseit. Ha a $top és a $skip is meg van adva, a rendszer először a $skip alkalmazza.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. Sikeres kérés, és visszaadja az összes művelet állapotát. HeadersRetry-After: integerETag: string|
|400|Hibás kérelem. Érvénytelen kérés. Adja meg a bemeneti paramétereket.|
|401|Jogosulatlan. A hitelesítő adatok ellenőrzéséhez.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|

## <a name="get-operations-response"></a>Műveletek beolvasása – válasz

### <a name="successful-get-operations-response"></a>Sikeres lekérési műveletekre adott válasz

A sikeres válasz a következő információkat adja vissza.

|Név|Típus|Description|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|A művelet létrehozásának dátuma és időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma és időpontja.|
|status|Sztring|A feladatok vagy dokumentumok lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Megszakítása</li><li>Sikertelen</li><li>NotStarted</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed</li></ul>|
|összegzés|StatusSummary[]|Az alább felsorolt részleteket tartalmazó összefoglalás.|
|összefoglalás. Total|egész szám|A dokumentumok száma összesen.|
|összefoglalás. sikertelen|egész szám|A dokumentumok száma nem sikerült.|
|összefoglalás. sikeres|egész szám|A dokumentumok száma sikeresen lefordítva.|
|összefoglalás. inelőrehaladás|egész szám|A folyamatban lévő dokumentumok száma.|
|összefoglalás. notYetStarted|egész szám|A még nem megkezdett dokumentumok száma.|
|összefoglalás. megszakítva|egész szám|A dokumentumok száma megszakítva.|
|összefoglalás. totalCharacterCharged|egész szám|A felszámított karakterek teljes száma.|

###<a name="error-response"></a>Hiba válasza

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|A magas szintű hibakódokat tartalmazó enumerálások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|cél|sztring|Lekéri a hiba forrását. Egy érvénytelen dokumentum esetében például a "Documents" vagy a "Document id" lenne.|
|innerError|InnerErrorV2|Új belső hiba formátuma, amely megfelel a Cognitive Services API-irányelveknek. Tartalmazza a szükséges tulajdonságokat: ErrorCode, üzenet és opcionális tulajdonságok célpontja, részletek (kulcs érték párok), belső hiba (ez lehet beágyazott).|
|innerError. code|sztring|Hibakód-karakterlánc beolvasása.|
|innerError. Message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

A következő példa egy sikeres választ mutat be.

```JSON
{
  "value": [
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
  ]
}
```

### <a name="example-error-response"></a>Példa a hiba válaszára

A következő példa egy hibaüzenetet választ. A többi hibakód sémája ugyanaz.

Állapotkód: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "target": "Operation",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezéssel kapcsolatos további információkért tekintse meg a dokumentumok fordításának és az ügyféloldali kódtár használatának lépéseit.

> [!div class="nextstepaction"]
> [Ismerkedés a dokumentumok fordításával](../get-started-with-document-translation.md)
