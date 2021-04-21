---
title: Fordítási metódus megszakítása
titleSuffix: Azure Cognitive Services
description: A fordítás megszakítása metódus megszakítja a jelenleg feldolgozási vagy várólistán lévő műveleteket.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 3de052f50676065a6656f77a0ea68cf8c9ab46a8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836202"
---
# <a name="cancel-translation"></a>Fordítás megszakítása

Megszakíthatja a jelenleg feldolgozási vagy várólistán lévő műveleteket. Egy művelet nem lesz megszakítva, ha már befejeződött, meghiúsult vagy megszakította. A visszaadott kérés hibás lesz. A fordítást lezáró összes dokumentum nem lesz visszavonva, és díjat számítunk fel. Ha lehetséges, minden függőben lévő dokumentum törlődik.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `DELETE` következőre:

```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Ismerje meg, hogyan találhatja meg [az egyéni tartománynevét.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **A Document Translation szolgáltatásnak minden API-kérelemhez egyéni tartományvégpontra van szükség.**
> * Nem használhatja az erőforráskulcsok és -végpontok oldalán található végpontot Azure Portal, sem a globális fordítóvégpontot– a  `api.cognitive.microsofttranslator.com` dokumentumfordításra vonatkozó HTTP-kérések igénylésére.

## <a name="request-parameters"></a>Kérelemparaméterek

A lekérdezési sztringen átadott kérelemparaméterek a következőek:

|Lekérdezési paraméter|Kötelező|Leírás|
|-----|-----|-----|
|id|Igaz|A művelet azonosítója.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|-----|-----|
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérés által visszaadott lehetséges HTTP-állapotkódok a következők.

| Állapotkód| Description|
|-----|-----|
|200|OK gombra. A kérelem visszavonása el lett elküldve|
|401|Jogosulatlan. Ellenőrizze a hitelesítő adatait.|
|404|Nem található. Az erőforrás nem található. 
|500|Belső kiszolgálóhiba.
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="cancel-translation-response"></a>Fordítás megszakítása válasz

### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz az alábbi adatokat tartalmazza.

|Név|Típus|Description|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|Művelet létrehozási dátumának időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma.|
|status|Sztring|Feladat vagy dokumentum lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Érvénytelenítés</li><li>Sikertelen</li><li>NotStarted (Nem új)</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed (Érvényesítési hiba)</li></ul>|
|összegzés|StatusSummary (Állapot állapota)|Az alább felsorolt részleteket tartalmazó összegzés.|
|summary.total|egész szám|Az összes dokumentum száma.|
|summary.failed|egész szám|A sikertelen dokumentumok száma.|
|summary.success|egész szám|A sikeresen lefordított dokumentumok száma.|
|summary.inProgress (Összegzés.inProgress)|egész szám|A folyamatban lévő dokumentumok száma.|
|summary.notYetStarted|egész szám|A feldolgozással még nem elindított dokumentumok száma.|
|summary.cancelled|egész szám|A lemondottak száma.|
|summary.totalCharacterCharged|egész szám|Az API által felszámított karakterek teljes száma.|

### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|Cél|sztring|Lekérte a hiba forrását. Ez lehet például "documents" (dokumentumok) vagy "document id" (dokumentumazonosító) érvénytelen dokumentum esetén.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a szükséges ErrorCode, üzenet és opcionális tulajdonságok célértékét, a részleteket (kulcs-érték pár), a belső hibát (beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|Belső. Eroor.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa a sikeres válaszra

Az alábbi JSON-objektum egy példa a sikeres válaszra.

Állapotkód: 200

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

Az alábbi JSON-objektum egy példa egy hibaválaszra. A többi hibakód sémája megegyezik.

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
