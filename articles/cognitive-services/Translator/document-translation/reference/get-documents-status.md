---
title: Dokumentumok állapotának lekért állapota
titleSuffix: Azure Cognitive Services
description: A dokumentumok lekérése állapot metódus egy kötegelt dokumentumfordítási kérelemben az összes dokumentum állapotát adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1e52d4ff5ccb5047ed82cca2764e98a8bd212305
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836220"
---
# <a name="get-documents-status"></a>Dokumentumok állapotának lekért állapota

A Dokumentumok állapotának lekérése metódus egy kötegelt dokumentumfordítási kérelemben az összes dokumentum állapotát adja vissza.

A válaszban szereplő dokumentumok dokumentumazonosító szerint csökkenő sorrendben vannak rendezve. Ha a válaszban található dokumentumok száma meghaladja a lapozási korlátot, a rendszer kiszolgálóoldali lapozást használ. A lapszámozott válaszok részleges eredményt jeleznek, és folytatási tokent is tartalmaznak a válaszban. A folytatási token hiánya azt jelenti, hogy nem áll rendelkezésre további oldal.

$top és $skip lekérdezési paraméterekkel megadható a visszaadni kívánt eredmények száma és a gyűjtemény eltolása. A kiszolgáló az ügyfél által megadott értékeket tiszteletben azzal, hogy be van adva. Az ügyfeleket azonban fel kell készíteni olyan válaszok kezelésére, amelyek más oldalméretet vagy folytatási tokent tartalmaznak.

Ha a $top és $skip is tartalmazza, a kiszolgálónak először alkalmaznia kell $skip, majd $top a gyűjteményre.

> [!NOTE]
> Ha a kiszolgáló nem tudja figyelembe $top és/vagy $skip-t, akkor a lekérdezési beállítások figyelmen kívül hagyása helyett hibát kell visszaadni az ügyfélnek, amely erről tájékoztatja. Ez csökkenti annak kockázatát, hogy az ügyfél feltételezéseket tevéssen a visszaadott adatokkal kapcsolatban.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
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
|$skip|Hamis|Hagyja ki $skip gyűjtemény összes bejegyzését. Ha a $top és a $skip is meg van adva, a $skip lesz alkalmazva.|
|$top|Hamis|Vegye $top gyűjtemény összes bejegyzését. Ha a $top és a $skip is meg van adva, a $skip lesz alkalmazva.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérés által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. Sikeres kérés, és a dokumentumok állapotát adja vissza. HeadersRetry-After: integerETag: sztring|
|400|Érvénytelen kérés. Ellenőrizze a bemeneti paramétereket.|
|401|Jogosulatlan. Ellenőrizze hitelesítő adatait.|
|404|Az erőforrás nem található.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|


## <a name="get-documents-status-response"></a>Dokumentumok állapotának lekért válasza

### <a name="successful-get-documents-status-response"></a>A dokumentumok állapotának sikeres lekért válasza

A sikeres válasz az alábbi adatokat tartalmazza.

|Név|Típus|Description|
|--- |--- |--- |
|@nextLink|sztring|A következő oldal URL-címe. Null értékű, ha nincs több elérhető oldal.|
|érték|DocumentStatusDetail []|Az alábbiakban felsorolt egyes dokumentumok részletes állapota.|
|value.path|sztring|A dokumentum vagy mappa helye.|
|value.createdDateTimeUtc|sztring|Művelet létrehozási dátumának időpontja.|
|value.lastActionDateTimeUt|sztring|A művelet állapotának frissítésének dátuma.|
|value.status|status|A feladat vagy dokumentum lehetséges állapotának listája.<ul><li>Megszakítva</li><li>Érvénytelenítés</li><li>Sikertelen</li><li>NotStarted (Nem új)</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed (Érvényesítési hiba)</li></ul>|
|value.to|sztring|Nyelvre.|
|value.progress|sztring|A fordítás előrehaladása, ha elérhető.|
|value.id|sztring|Dokumentumazonosító.|
|value.characterCharged|egész szám|Az API által felszámított karakterek.|

### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|Cél|sztring|Lekérte a hiba forrását. Érvénytelen dokumentum esetén például "documents" (dokumentumok) vagy "document id" (dokumentumazonosító).|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a következő kötelező tulajdonságokat: ErrorCode, üzenet és opcionális céltulajdonság, részletek (kulcs-érték pár), belső hiba (ez beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa a sikeres válaszra

Az alábbi példa egy sikeres választ mutat be.

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

### <a name="example-error-response"></a>Példa hibaválaszra

Az alábbiakban egy példa egy hibaválaszra. A többi hibakód sémája megegyezik.

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

A dokumentumfordítás és az ügyféloldali kódtár használatával kapcsolatos további információkért kövesse a rövid útmutatót.

> [!div class="nextstepaction"]
> [A dokumentumfordítás első lépések](../get-started-with-document-translation.md)
