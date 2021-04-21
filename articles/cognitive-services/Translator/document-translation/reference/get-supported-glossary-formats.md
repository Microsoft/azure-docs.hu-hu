---
title: Támogatott szószedetformátumok lekért metódusa
titleSuffix: Azure Cognitive Services
description: A get supported glossary formats metódus visszaadja a támogatott szószedetformátumok listáját.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: b2419c9d1fe32390afcd81b77d1cf32900b9fe8a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836219"
---
# <a name="get-supported-glossary-formats"></a>Támogatott szószedetformátumok lekért száma

A Get supported glossary formats metódus visszaadja a Document Translation szolgáltatás által támogatott szószedetformátumok listáját. A lista tartalmazza a gyakran használt fájlkiterjesztést.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/glossaries/formats
```

Ismerje meg, hogyan találhatja meg [az egyéni tartománynevét.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **A Document Translation szolgáltatásnak minden API-kérelemhez egyéni tartományvégpontra van szükség.**
> * Nem használhatja az erőforráskulcsok és -végpontok oldalán található végpontot Azure Portal, sem a globális fordítóvégpontot– a  `api.cognitive.microsofttranslator.com` dokumentumfordításra vonatkozó HTTP-kérések igénylésére.

## <a name="request-headers"></a>Kérésfejlécek

A kérelemfejlécek a következőek:

|Fejlécek|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Szükséges kérelemfejléc|

## <a name="response-status-codes"></a>Válasz állapotkódok

A kérés által visszaadott lehetséges HTTP-állapotkódok a következők.

|Állapotkód|Description|
|--- |--- |
|200|OK gombra. A támogatott szószedetfájl-formátumok listáját adja vissza.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|


## <a name="get-supported-glossary-formats-response"></a>Támogatott szószedetformátumok válasza

A lista alaptípusa a Get supported glossary formats API (Támogatott szószedetformátumok le szolgáltatása) API-ban van visszaadva.

### <a name="successful-get-supported-glossary-formats-response"></a>A támogatott szószedetformátumok sikeres lekért válasza

A lista alaptípusa a Get supported glossary formats API (Támogatott szószedetformátumok le szolgáltatása) API-ban érhető el.

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
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a következő kötelező tulajdonságokat: ErrorCode, üzenet és opcionális céltulajdonság, részletek (kulcs-érték pár), belső hiba (ez beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa sikeres válaszra

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
Az alábbiakban egy példa egy hibaválaszra. A többi hibakód sémája ugyanaz.

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
