---
title: Dokumentum fordítása dokumentum-tárolási forrás metódusának beolvasása
titleSuffix: Azure Cognitive Services
description: A Dokumentumvédelem forrásának beolvasása metódus a támogatott tárolási források listáját adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: d6d8b1d08bbef1c37cbf0583022428037808580d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613059"
---
# <a name="document-translation-get-document-storage-source"></a>Dokumentum fordítása: a dokumentum tárolási forrásának beolvasása

A dokumentum-tárolási forrás beolvasása módszer a dokumentum-fordítási szolgáltatás által támogatott tárolási források/beállítások listáját adja vissza.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `GET` a következőnek:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Megtudhatja, hogyan keresheti meg az [Egyéni tartománynevet](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **A dokumentum-fordítási szolgáltatáshoz tartozó összes API-kérelemhez egyéni tartományi végpont szükséges**.
> * Nem használhatja a végpontot a Azure Portal erőforrás- _kulcsok és a végpontok_ oldalán, sem a globális Translator végponton –, `api.cognitive.microsofttranslator.com` hogy http-kéréseket lehessen felvenni a dokumentumok fordítására.

## <a name="request-headers"></a>Kérésfejlécek

A kérelem fejlécei:

|Fejlécek|Leírás|
|--- |--- |
|Ocp-Apim-Subscription-Key|Kötelező kérelem fejléce|

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők:

|Állapotkód|Leírás|
|--- |--- |
|200|OK gombra. Sikeres kérelem, amely a tárolási források listáját adja vissza.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló átmenetileg nem érhető el</li></ul>|

## <a name="get-document-storage-source-response"></a>Dokumentum-tárolási forrás válaszának beolvasása

### <a name="successful-get-document-storage-source-response"></a>A dokumentum-tárolási forrás válaszának beolvasása sikeres
A lista alaptípusa a dokumentum-tárolási forrás API beolvasása szolgáltatásban.

|Név|Típus|Leírás|
|--- |--- |--- |
|érték|karakterlánc []|Objektumok listája.|


### <a name="error-response"></a>Hiba válasza

|Név|Típus|Leírás|
|--- |--- |--- |
|code|sztring|A magas szintű hibakódokat tartalmazó enumerálások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hiba formátuma, amely megfelel a Cognitive Services API-irányelveknek. Tartalmazza a szükséges tulajdonságokat: ErrorCode, üzenet és opcionális tulajdonságok célpontja, részletek (kulcs érték párok), belső hiba (ez lehet beágyazott).|
|innerError. code|sztring|Hibakód-karakterlánc beolvasása.|
|innerError. Message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

A következő példa egy sikeres választ mutat be.

```JSON
{
  "value": [
    "AzureBlob"
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