---
title: Támogatott tárolóforrások lekért metódusa
titleSuffix: Azure Cognitive Services
description: A támogatott tárolóforrások lekért metódusa a támogatott tárolóforrások listáját adja vissza.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 97212a0da47a25330def26a5bff2b39ff840a23d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836190"
---
# <a name="get-supported-storage-sources"></a>Támogatott tárolóforrások lekért száma

A Get supported storage sources metódus a Document Translation szolgáltatás által támogatott tárolóforrások/lehetőségek listáját adja vissza.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Megtudhatja, hogyan találhatja meg [az egyéni tartománynevet.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **A Document Translation szolgáltatásnak minden API-kéréséhez egyéni tartományvégpontra van szükség.**
> * Nem használhatja az erőforráskulcsok és Azure Portal  oldalán található végpontot, sem a globális fordítóvégpontot – a Dokumentumfordítás szolgáltatásnak `api.cognitive.microsofttranslator.com` való HTTP-kérések igénylésére.

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérések által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. Sikeres kérés, és visszaadja a tárolóforrások listáját.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Támogatott tárforrások lekért válasza

### <a name="successful-get-supported-storage-sources-response"></a>A támogatott tárolóforrások sikeres lekért válasza
A lista alaptípusa a Get supported storage sources API (Támogatott tárolóforrások le szolgáltatása) API-ban található.

|Név|Típus|Description|
|--- |--- |--- |
|érték|sztring []|Objektumok listája.|


### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a szükséges ErrorCode tulajdonságokat, az üzenetet és az opcionális céltulajdonságokat, a részleteket (kulcs-érték párt), a belső hibát (ez beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

Az alábbi példa egy sikeres választ mutat be.

```JSON
{
  "value": [
    "AzureBlob"
  ]
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