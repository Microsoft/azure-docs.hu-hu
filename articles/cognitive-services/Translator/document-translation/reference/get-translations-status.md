---
title: Fordítások állapotának lekérte
titleSuffix: Azure Cognitive Services
description: A get translations status metódus visszaadja az elküldött kötegelt kérelmek listáját és az egyes kérések állapotát.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: c3301283f0a7334a7c207ff7c80b4f71a13de465
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864828"
---
# <a name="get-translations-status"></a>Fordítások állapotának lekérte

A Fordítások állapotának lekérése metódus visszaadja az elküldött kötegelt kérelmek listáját és az egyes kérések állapotát. Ez a lista csak a felhasználó által elküldött kötegelt kérelmeket tartalmazza (az előfizetés alapján). Az egyes kérések állapota id szerint van rendezve.

Ha a kérelmek száma meghaladja a lapozási korlátot, a rendszer kiszolgálóoldali lapozást használ. A lapszámozott válaszok részleges eredményt jeleznek, és folytatási tokent is tartalmaznak a válaszban. A folytatási token hiánya azt jelenti, hogy nem áll rendelkezésre további oldal.

$top és $skip lekérdezési paraméterekkel megadható a visszaadni kívánt eredmények száma és a gyűjtemény eltolása.

A kiszolgáló az ügyfél által megadott értékeket tiszteletben adva van. Az ügyfeleket azonban fel kell készíteni az olyan válaszok kezelésére, amelyek más oldalméretet vagy folytatási tokent tartalmaznak.

Ha a $top és $skip is tartalmazza, a kiszolgálónak először alkalmaznia kell $skip, majd $top a gyűjteményre. 

> [!NOTE]
> Ha a kiszolgáló nem tudja figyelembe $top és/vagy $skip-t, a kiszolgálónak hibát kell visszaadni az ügyfélnek, amely erről tájékoztatja, és nem kell figyelmen kívül hagynia a lekérdezési beállításokat. Ez csökkenti annak kockázatát, hogy az ügyfél feltételezéseket hoz a visszaadott adatokkal kapcsolatban.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Ismerje meg, hogyan találhatja meg [az egyéni tartománynevét.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **A Document Translation szolgáltatásnak minden API-kérelemhez egyéni tartományvégpontra van szükség.**
> * Nem használhatja az erőforráskulcsok és -végpontok oldalán található végpontot Azure Portal, sem a globális fordítóvégpontot– a  `api.cognitive.microsofttranslator.com` dokumentumfordításra vonatkozó HTTP-kérések igénylésére.

## <a name="request-parameters"></a>Kérelemparaméterek

A lekérdezési sztringen átadott kérelemparaméterek a következőek:

|Lekérdezési paraméter|Kötelező|Leírás|
|--- |--- |--- |
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
|200|OK gombra. Sikeres kérés, és az összes művelet állapotát adja vissza. HeadersRetry-After: integerETag: sztring|
|400|Hibás kérés. Érvénytelen kérés. Ellenőrizze a bemeneti paramétereket.|
|401|Jogosulatlan. Ellenőrizze hitelesítő adatait.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="get-translations-status-response"></a>Fordítási állapot lekért válasza

### <a name="successful-get-translations-status-response"></a>Sikeres fordítási állapot lekért állapota válasz

A sikeres válasz az alábbi adatokat tartalmazza.

|Név|Típus|Description|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|Művelet létrehozási dátumának időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma.|
|status|Sztring|Feladat vagy dokumentum lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Érvénytelenítés</li><li>Sikertelen</li><li>NotStarted (Nem új)</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed (Érvényesítési hiba)</li></ul>|
|összegzés|StatusSummary[]|Az alább felsorolt részleteket tartalmazó összegzés.|
|summary.total|egész szám|Az összes dokumentum száma.|
|summary.failed|egész szám|A sikertelen dokumentumok száma.|
|summary.success|egész szám|A sikeresen lefordított dokumentumok száma.|
|summary.inProgress (Összegzés.inProgress)|egész szám|A folyamatban lévő dokumentumok száma.|
|summary.notYetStarted|egész szám|A feldolgozással még nem elindított dokumentumok száma.|
|summary.cancelled|egész szám|A lemondott dokumentumok száma.|
|summary.totalCharacterCharged|egész szám|A számított karakterek teljes száma.|

### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|Cél|sztring|Lekérte a hiba forrását. Érvénytelen dokumentum esetén például "documents" (dokumentumok) vagy "document id" (dokumentumazonosító).|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a szükséges ErrorCode tulajdonságokat, az üzenetet és az opcionális céltulajdonságokat, a részleteket (kulcs-érték párt), a belső hibát (ez beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

Az alábbi példa egy sikeres választ mutat be.

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

### <a name="example-error-response"></a>Példa hibaválaszra

Az alábbi példa egy hibaválaszra mutat példát. A többi hibakód sémája megegyezik.

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
