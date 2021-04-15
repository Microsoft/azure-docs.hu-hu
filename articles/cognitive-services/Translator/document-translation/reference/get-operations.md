---
title: Dokumentumfordítási get műveletek
titleSuffix: Azure Cognitive Services
description: A get operations metódus visszaadja az elküldött kötegelt kérelmek listáját és az egyes kérések állapotát.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: fd7cee564aa3a00e21d1e707d08a18115d519925
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484676"
---
# <a name="document-translation-get-operations"></a>Dokumentumfordítás: get műveletek

A Get Operations metódus visszaadja az elküldött kötegelt kérelmek listáját és az egyes kérések állapotát. Ez a lista csak a felhasználó által elküldött kötegelt kérelmeket tartalmazza (az előfizetés alapján). Az egyes kérések állapota id szerint van rendezve.

Ha a kérelmek száma meghaladja a lapozási korlátot, a rendszer kiszolgálóoldali lapozást használ. A lapszámozott válaszok részleges eredményt jeleznek, és folytatási tokent is tartalmaznak a válaszban. A folytatási token hiánya azt jelenti, hogy nem áll rendelkezésre további oldal.

$top és $skip lekérdezési paraméterekkel megadható a visszaadni kívánt eredmények száma és a gyűjtemény eltolása.

A kiszolgáló az ügyfél által megadott értékeket is beveszi. Az ügyfeleket azonban fel kell készíteni az olyan válaszok kezelésére, amelyek más oldalméretet vagy folytatási tokent tartalmaznak.

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
> * Nem használhatja az erőforráskulcsok és -végpontok oldalán található végpontot Azure Portal a globális fordítóvégpontot sem a dokumentumfordításra vonatkozó  `api.cognitive.microsofttranslator.com` HTTP-kérések igénylésére.

## <a name="request-parameters"></a>Kérelemparaméterek

A lekérdezési sztringen átadott kérelemparaméterek a következőek:

|Lekérdezési paraméter|Kötelező|Leírás|
|--- |--- |--- |
|$skip|Hamis|Hagyja ki $skip gyűjtemény összes bejegyzését. Ha a $top és a $skip is meg van adva, a $skip lesz alkalmazva.|
|$top|Hamis|Vegye $top gyűjtemény összes bejegyzését. Ha a $top és a $skip is meg van adva, a $skip lesz alkalmazva.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Leírás|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérések által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Leírás|
|--- |--- |
|200|OK gombra. Sikeres kérés, és az összes művelet állapotát adja vissza. HeadersRetry-After: integerETag: sztring|
|400|Hibás kérés. Érvénytelen kérés. Ellenőrizze a bemeneti paramétereket.|
|401|Jogosulatlan. Ellenőrizze a hitelesítő adatait.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="get-operations-response"></a>Műveletek lekért válasza

### <a name="successful-get-operations-response"></a>Sikeres get operations válasz

A sikeres válasz az alábbi adatokat tartalmazza.

|Név|Típus|Leírás|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|Művelet létrehozási dátumának időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma.|
|status|Sztring|Feladat vagy dokumentum lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Érvénytelenítés</li><li>Sikertelen</li><li>NotStarted (Nincs indítás)</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed (Érvényesítési hiba)</li></ul>|
|összegzés|StatusSummary[]|Az alább felsorolt részleteket tartalmazó összegzés.|
|summary.total|egész szám|Az összes dokumentum száma.|
|summary.failed|egész szám|A sikertelen dokumentumok száma.|
|summary.success|egész szám|A sikeresen lefordított dokumentumok száma.|
|summary.inProgress (összegzés.inProgress)|egész szám|A folyamatban lévő dokumentumok száma.|
|summary.notYetStarted|egész szám|A feldolgozásra még nem elindított dokumentumok száma.|
|summary.cancelled|egész szám|A lemondott dokumentumok száma.|
|summary.totalCharacterCharged|egész szám|A felszámért karakterek teljes száma.|

### <a name="error-response"></a>Hibaválasz

|Név|Típus|Leírás|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|Cél|sztring|Lekérte a hiba forrását. Ez lehet például "documents" (dokumentumok) vagy "document id" (dokumentumazonosító) érvénytelen dokumentum esetén.|
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

Az alábbiakban egy példa egy hibaválaszra. A többi hibakód sémája ugyanaz.

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
