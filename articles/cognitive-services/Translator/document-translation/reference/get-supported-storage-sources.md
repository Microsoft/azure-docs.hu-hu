---
title: Támogatott tárolási források lekért metódusa
titleSuffix: Azure Cognitive Services
description: A get supported storage sources metódus visszaadja a támogatott tárolóforrások listáját.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: 03203206da6ae3ea9a1174aebafda0b58e22ea41
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864846"
---
# <a name="get-supported-storage-sources"></a>Támogatott tárforrások lekért száma

A Get supported storage sources metódus visszaadja a Document Translation szolgáltatás által támogatott tárolási források/lehetőségek listáját.

## <a name="request-url"></a>URL-cím kérése

Kérés küldése a `GET` következőre:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
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
|200|OK gombra. Sikeres kérés, és visszaadja a tárolási források listáját.|
|500|Belső kiszolgálóhiba.|
|Egyéb állapotkódok|<ul><li>Túl sok kérelem</li><li>A kiszolgáló ideiglenesen nem érhető el</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Támogatott tárforrások lekért válasza

### <a name="successful-get-supported-storage-sources-response"></a>A támogatott tárforrások sikeres lekért válasza
A lista alaptípusa a Get supported storage sources API (Támogatott tárolóforrások le szolgáltatása) API-ban található.

|Név|Típus|Description|
|--- |--- |--- |
|érték|sztring []|Objektumok listája.|


### <a name="error-response"></a>Hibaválasz

|Név|Típus|Description|
|--- |--- |--- |
|code|sztring|Magas szintű hibakódokat tartalmazó felsorolások. Lehetséges értékek:<br/><ul><li>InternalServerError</li><li>InvalidArgument (Érvénytelen nyelv)</li><li>InvalidRequest (Érvénytelen kérdés)</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Nem engedélyezett</li></ul>|
|message|sztring|Magas szintű hibaüzenetet kap.|
|innerError|InnerErrorV2|Új belső hibaformátum, amely megfelel Cognitive Services API-irányelveknek. Tartalmazza a következő kötelező tulajdonságokat: ErrorCode, üzenet és opcionális céltulajdonság, részletek (kulcs-érték pár), belső hiba (ez beágyazható).|
|innerError.code|sztring|Lekérte a kód hibasringet.|
|innerError.message|sztring|Magas szintű hibaüzenetet kap.|

## <a name="examples"></a>Példák

### <a name="example-successful-response"></a>Példa a sikeres válaszra

Az alábbi példa egy sikeres választ mutat be.

```JSON
{
  "value": [
    "AzureBlob"
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