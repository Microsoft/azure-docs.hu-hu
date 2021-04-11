---
title: Dokumentum fordításának megszakítása művelet metódusa
titleSuffix: Azure Cognitive Services
description: A megszakítási műveletek metódusa megszakítja az aktuális feldolgozási vagy várólistán lévő műveletet.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 39730f118dd93a972f238f85ef890f4dc54ca91a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613066"
---
# <a name="document-translation-cancel-operations"></a>Dokumentum fordítása: művelet megszakítása

Egy jelenleg feldolgozott vagy várólistán lévő művelet megszakítása. A művelet nem lesz megszakítva, ha már befejeződött vagy meghiúsult, vagy megszakítja a műveletet. A rendszer rossz kérést ad vissza. A teljes fordítással rendelkező dokumentumok nem lesznek megszakítva, és a rendszer díjat számít fel. Ha lehetséges, az összes függőben lévő dokumentum le lesz szakítva.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `DELETE` a következőnek:
```DELETE HTTP
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}
```

Megtudhatja, hogyan keresheti meg az [Egyéni tartománynevet](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **A dokumentum-fordítási szolgáltatáshoz tartozó összes API-kérelemhez egyéni tartományi végpont szükséges**.
> * Nem használhatja a végpontot a Azure Portal erőforrás- _kulcsok és a végpontok_ oldalán, sem a globális Translator végponton –, `api.cognitive.microsofttranslator.com` hogy http-kéréseket lehessen felvenni a dokumentumok fordítására.

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

|Lekérdezési paraméter|Kötelező|Leírás|
|-----|-----|-----|
|id|Igaz|A művelet azonosítója.|

## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Leírás|
|-----|-----|
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

| Állapotkód| Leírás|
|-----|-----|
|200|OK gombra. A visszavonási kérelem elküldve|
|401|Jogosulatlan. A hitelesítő adatok ellenőrzéséhez.|
|404|Nem található. Az erőforrás nem található. 
|500|Belső kiszolgálóhiba.
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|

## <a name="cancel-operations-response"></a>Művelet-visszavonási válasz

### <a name="successful-response"></a>Sikeres válasz

A sikeres válasz a következő információkat adja vissza.

|Név|Típus|Leírás|
|--- |--- |--- |
|id|sztring|A művelet azonosítója.|
|createdDateTimeUtc|sztring|A művelet létrehozásának dátuma és időpontja.|
|lastActionDateTimeUtc|sztring|A művelet állapotának frissítésének dátuma és időpontja.|
|status|Sztring|A feladatok vagy dokumentumok lehetséges állapotának listája: <ul><li>Megszakítva</li><li>Megszakítása</li><li>Sikertelen</li><li>NotStarted</li><li>Futó</li><li>Sikeres</li><li>ValidationFailed</li></ul>|
|összegzés|StatusSummary|Az alább felsorolt részleteket tartalmazó összefoglalás.|
|összefoglalás. Total|egész szám|A dokumentumok száma összesen.|
|összefoglalás. sikertelen|egész szám|A dokumentumok száma nem sikerült.|
|összefoglalás. sikeres|egész szám|A dokumentumok száma sikeresen lefordítva.|
|összefoglalás. inelőrehaladás|egész szám|A folyamatban lévő dokumentumok száma.|
|összefoglalás. notYetStarted|egész szám|A még nem megkezdett dokumentumok száma.|
|összefoglalás. megszakítva|egész szám|A megszakított szám.|
|összefoglalás. totalCharacterCharged|egész szám|Az API által felszámított összes karakter.|

### <a name="error-response"></a>Hiba válasza

|Név|Típus|Leírás|
|--- |--- |--- |
|code|sztring|A magas szintű hibakódokat tartalmazó enumerálások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|cél|sztring|Lekéri a hiba forrását. Egy érvénytelen dokumentum esetében például a "Documents" vagy a "Document id" lenne.|
|innerError|InnerErrorV2|Új belső hiba formátuma, amely megfelel a Cognitive Services API-irányelveknek. Tartalmazza a szükséges tulajdonságokat ErrorCode, üzenetet és opcionális tulajdonságok célját, részleteit (Key Value Pair), belső hibát (beágyazható).|
|innerError. code|sztring|Hibakód-karakterlánc beolvasása.|
|belső. Hibakódot. Message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

A következő JSON-objektum egy példa a sikeres válaszra.

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

### <a name="example-error-response"></a>Példa a hiba válaszára

A következő JSON-objektum például egy hibaüzenetre adott válasz. A többi hibakód sémája ugyanaz.

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
