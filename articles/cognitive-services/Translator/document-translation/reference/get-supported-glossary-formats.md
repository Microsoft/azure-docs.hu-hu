---
title: Támogatott szószedetformátumok metódusának lekért száma
titleSuffix: Azure Cognitive Services
description: A get supported glossary formats metódus visszaadja a támogatott szószedetformátumok listáját.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: ea22e6a3afe8ee90cb7b59d1aca0a37fc4fa03d6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864918"
---
# <a name="get-supported-glossary-formats"></a>Támogatott szószedetformátumok lekért száma

A Get supported glossary formats metódus a Document Translation szolgáltatás által támogatott szószedetformátumok listáját adja vissza. A lista tartalmazza a gyakran használt fájlkiterjesztést.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/glossaries/formats
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
|200|OK gombra. A támogatott szószedetfájlformátumok listáját adja vissza.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|


## <a name="get-supported-glossary-formats-response"></a>Válasz a támogatott szószedetformátumokra

A lista alaptípusa a Get supported glossary formats API (Támogatott szószedetformátumok le szolgáltatása) API-ban érhető el.

### <a name="successful-get-supported-glossary-formats-response"></a>A támogatott szószedetformátumok sikeres lekért válasza

A lista alaptípusa a Get supported glossary formats API (Támogatott szószedetformátumok le szolgáltatása) API-ban van visszaadva.

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. A támogatott szószedetfájl-formátumok listáját adja vissza.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|Túl sok kérelemKiszolgáló ideiglenesen nem érhető el|

### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a szükséges ErrorCode tulajdonságokat, az üzenetet és az opcionális céltulajdonságokat, a részleteket (kulcs-érték párt), a belső hibát (ez beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa a sikeres válaszra

Az alábbi példa egy sikeres választ mutat be.

```JSON
{
    "value": [
        {
            "format": "XLIFF",
            "fileExtensions": [
                ".xlf"
            ],
            "contentTypes": [
                "application/xliff+xml"
            ],
            "versions": [
                "1.0",
                "1.1",
                "1.2"
            ]
        },
        {
            "format": "TSV",
            "fileExtensions": [
                ".tsv",
                ".tab"
            ],
            "contentTypes": [
                "text/tab-separated-values"
            ],
            "versions": []
        }
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
