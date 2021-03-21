---
title: A Bing Spell Check API használata
titleSuffix: Azure Cognitive Services
description: Ismerje meg az API-val kapcsolatos Bing Spell Check módokat, beállításokat és egyéb információkat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 2031c31c6ea083452bbdbb95be74adf29be1f858
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349316"
---
# <a name="using-the-bing-spell-check-api"></a>A Bing Spell Check API használata

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ebből a cikkből megtudhatja, hogyan végezheti el a Bing Spell Check API használatát a kontextusbeli nyelvtan és a helyesírás-ellenőrzés végrehajtásához. Habár a legtöbb helyesírás-ellenőrző szótár alapú szabálykészlet alapján használható, a Bing helyesírás-ellenőrzője a gépi tanulást és a statisztikai gépi fordítást használja a pontos és a kontextusbeli javítások biztosításához. 

## <a name="spell-check-modes"></a>Helyesírás-ellenőrzési módok

Az API kétféle ellenőrzési módot támogat: `Proof` és `Spell`.  Próbálja ki a példákat [itt](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Bizonyítás – dokumentumok esetén 

Az alapértelmezett mód a `Proof`. A `Proof` helyesírási mód biztosítja a legátfogóbb ellenőrzéseket, és a nagybetűs írásmód, az alapvető központozás és más funkciók hozzáadásával segíti a dokumentumok létrehozását. Azonban ez a mód kizárólag az en-US (angol-amerikai), es-ES (spanyol), pt-BR (portugál) piacokon érhető el (megjegyzés: a spanyol és portugál piac esetében csak béta verzióban). Minden más piachoz állítsa be a lekérdezési paramétert Spell módra. 

> [!NOTE]
> Ha a lekérdezési szöveg hossza meghaladja az 4096-ot, a rendszer a 4096 karakterre csonkolja, majd feldolgozza azt. 

### <a name="spell----for-web-searchesqueries"></a>Helyesírás – webes keresések/lekérdezések

A `Spell` agresszívabb, hogy jobb keresési eredményeket adhasson. A `Spell` mód megtalálja a legtöbb helyesírási hibát, de nem talál meg néhány olyan nyelvtani hibát, például a nagybetűket és a szóismétlést, amelyeket a `Proof` észlel.

> [!NOTE]
> * A maximális támogatott lekérdezési hossz alább látható. Ha a lekérdezés túllépi a maximális hosszúságot, a lekérdezés és annak eredménye nem módosul.
>    * 130 karakter a következő nyelvi kódokhoz: en, de, es, fr, pl, PT, SV, ru, NL, NB, TR-TR, it, ZH, ko. 
>    * 65 karakter az összes többinél.
> * A helyesírási mód nem támogatja a szögletes zárójel karaktereit ( `[` és `]` ) a lekérdezésekben, és inkonzisztens eredményeket eredményezhet. Javasoljuk, hogy a spell mód használatakor távolítsa el őket a lekérdezésből.

## <a name="market-setting"></a>Piac beállítása

A kérésben a lekérdezési paraméterrel meg kell adni egy [piaci kódot](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) `mkt` . Az API egyébként a kérelem IP-címe alapján az alapértelmezett piacot fogja használni.


## <a name="http-post-and-get-support"></a>HTTP-POST és támogatás KÉRÉSe

Az API támogatja a HTTP POST és a HTTP GET metódust is. Az ellenőrizni kívánt szöveg hosszától függ, hogy melyiket érdemes használnia. Ha a sztringek mindig rövidebbek 1500 karakternél, használja a GET-et. Ha azonban 10 000 karakterig szeretné támogatni a sztringeket, akkor használja a POST-ot. A szöveges sztring bármilyen érvényes UTF-8 karaktert tartalmazhat.

A következő példa egy szöveges sztring helyesírását és nyelvtanát ellenőrző POST-kérelmet mutat. A teljesség kedvéért a példa tartalmazza a [mode](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) lekérdezési paramétert (ezt ki lehetett volna hagyni, hiszen a `mode` alapértelmezett értéke a Proof). A [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) lekérdezési paraméter tartalmazza az ellenőrizni kívánt sztringet.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Ha a HTTP GET metódust használja, hozzáadhatja a `text` lekérdezési paramétert az URL lekérdezési sztringjéhez
  
Az alábbiakban az előző kérelemre adott válasz látható. A válasz tartalmaz egy [SpellCheck](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck) objektumot. 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
A [flaggedTokens](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) mező listázza a helyesírási és nyelvtani hibákat, amelyeket az API talált a [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) sztringben. A `token` mező tartalmazza a kicserélendő szót. A token megtalálásához a `text` sztringben használjon nullaalapú eltolást a `offset` mezőben. Majd kicserélheti az ott talált szót a `suggestion` mezőben található szóval. 

Ha a `type` mező RepeatedToken, továbbra is kicserélheti a tokent a `suggestion` mezővel, de emellett valószínűleg el kell távolítania a záró szóközt.

## <a name="throttling-requests"></a>Kérelmek szabályozása

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Következő lépések

- [Mi a Bing Spell Check API?](../overview.md)
- [A Bing Spell Check API 7-es verzió referenciája](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)