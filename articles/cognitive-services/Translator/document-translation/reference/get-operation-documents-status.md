---
title: Dokumentum fordításának lekérési művelete – dokumentumok állapota
titleSuffix: Azure Cognitive Services
description: A műveleti dokumentumok beolvasása állapot módszere egy batch-dokumentum fordítási kérelmében szereplő összes dokumentum állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c6cf33f32abb473badbb11bf58bd52ffba53d75c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613033"
---
# <a name="document-translation-get-operation-documents-status"></a>Dokumentum fordítása: működési dokumentumok állapotának lekérése

A műveleti dokumentumok beolvasása állapot módszere egy batch-dokumentum fordítási kérelmében szereplő összes dokumentum állapotát adja vissza.

A válaszban szereplő dokumentumokat csökkenő sorrendben, a dokumentum azonosítója szerint rendezi a rendszer. Ha a válaszban szereplő dokumentumok száma meghaladja a lapozási korlátot, a rendszer kiszolgálóoldali lapozást használ. A többoldalas válaszok részleges eredményt jeleznek, és a válaszban egy folytatási tokent tartalmaznak. A folytatási token hiánya azt jelenti, hogy nincsenek elérhető további lapok.

$top és $skip lekérdezési paraméterekkel megadható a visszaadott eredmények száma és a gyűjtemény eltolása. A kiszolgáló tiszteletben tartja az ügyfél által megadott értékeket. Az ügyfeleknek azonban elő kell készülniük a különböző oldalméret vagy folytatási tokent tartalmazó válaszok kezelésére.

Ha $top és $skip is szerepel, a kiszolgálónak először $skip kell alkalmaznia, majd $top a gyűjteményen.

> [!NOTE]
> Ha a kiszolgáló nem tudja megbecsülni $top és/vagy $skip, a kiszolgálónak hibát kell visszaadnia az ügyfélnek, és nem csupán figyelmen kívül hagyja a lekérdezés beállításait. Ez csökkenti annak kockázatát, hogy az ügyfél feltételezi a visszaadott adatmennyiséget.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `GET` a következőnek:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|$skip|Hamis|Hagyja ki a gyűjtemény $skip bejegyzéseit. Ha a $top és a $skip is meg van adva, a rendszer először a $skip alkalmazza.|
|$top|Hamis|Végezze el a gyűjtemény $top bejegyzéseit. Ha a $top és a $skip is meg van adva, a rendszer először a $skip alkalmazza.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Leírás|
|--- |--- |
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Leírás|
|--- |--- |
|200|OK gombra. A sikeres kérelem és a dokumentumok állapotát adja vissza. HeadersRetry-After: integerETag: string|
|400|Érvénytelen kérés. Adja meg a bemeneti paramétereket.|
|401|Jogosulatlan. A hitelesítő adatok ellenőrzéséhez.|
|404|Az erőforrás nem található.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|


## <a name="get-operation-documents-status-response"></a>Műveleti dokumentumok állapotának lekérése – válasz

### <a name="successful-get-operation-documents-status-response"></a>Sikeres lekérési művelet dokumentumainak állapota válasz

A sikeres válasz a következő információkat adja vissza.

|Név|Típus|Description|
|--- |--- |--- |
|@nextLink|sztring|A következő oldal URL-címe. NULL, ha nincs több oldal elérhető.|
|érték|DocumentStatusDetail []|Az alább felsorolt egyes dokumentumok részletes állapota.|
|Value. Path|sztring|A dokumentum vagy mappa helye.|
|Value. createdDateTimeUtc|sztring|A művelet létrehozásának dátuma és időpontja.|
|Value. lastActionDateTimeUt|sztring|A művelet állapotának frissítésének dátuma és időpontja.|
|Value. status|status|A feladatok vagy dokumentumok lehetséges állapotának listája.<ul><li>Megszakítva</li><li>Megszakítása</li><li>Sikertelen</li><li>NotStarted</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed</li></ul>|
|value.to|sztring|Nyelvre.|
|érték. Progress|sztring|A fordítás előrehaladása, ha elérhető.|
|value.id|sztring|Dokumentum azonosítója.|
|Value. characterCharged|egész szám|Az API által felszámított karakterek.|

### <a name="error-response"></a>Hiba válasza

|Név|Típus|Leírás|
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
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
