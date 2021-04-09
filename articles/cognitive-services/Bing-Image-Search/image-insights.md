---
title: Képelemzések beolvasása – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a Bing Image Search APIt a rendszerképekkel kapcsolatos további információk megjelenítéséhez.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 9dffc16a1d1171343fa10fce10fddba2a12c2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96348245"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Képelemzések beolvasása a Bing Image Search API

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

> [!IMPORTANT]
> Ahelyett, hogy a/images/details-végpontot használja a képelemzések lekéréséhez, használja a [Visual Search](../bing-visual-search/overview.md) , mivel ez átfogóbb megállapításokat tesz lehetővé.


Minden rendszerkép tartalmaz egy elemzési jogkivonatot, amelyet a rendszerképpel kapcsolatos információk lekéréséhez használhat. Lekérheti például a kapcsolódó rendszerképek gyűjteményét, a képet tartalmazó weblapokat, illetve azon kereskedők listáját, amelyeken megvásárolhatja a képen látható terméket.  

Egy képpel kapcsolatos információk beszerzéséhez rögzítse a rendszerkép [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) tokenjét a válaszban.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Ezután hívja meg a rendszerkép részletei végpontot, és állítsa a [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) lekérdezési paramétert a (z) jogkivonatára `imageInsightsToken` .  

A lekérdezni kívánt eredmények megadásához állítsa be a `modules` lekérdezési paramétert. Az összes felismerés beszerzéséhez állítsa a következőre: `modules` `All` . Ha csak a feliratot és a gyűjtési eredményeket szeretné lekérni, állítsa a következőre: `modules` `Caption%2CCollection` . A lehetséges információk teljes listáját lásd: [modulok](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested). Nem minden kép érhető el. A válasz tartalmazza az összes kért bepillantást, ha van ilyen.

Az alábbi példa az előző rendszerkép összes elérhető elemzését kéri.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Ismert rendszerkép beolvasása

Ha van egy olyan rendszerkép URL-címe, amelyről elemzést szeretne kapni, használja a [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) lekérdezési paramétert a [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) paraméter helyett a rendszerkép megadásához. Ha a képfájl is rendelkezésre áll, elküldheti a rendszerkép bináris fájlját egy POST-kérelem törzsében. Ha POST-kérést használ, a fejlécet a következőre kell `Content-Type` beállítani: `multipart/data-form` . Mindkét lehetőség esetén a rendszerkép mérete nem haladhatja meg az 1 MB-ot.  

Ha rendelkezik a rendszerkép URL-címével, a következő példa azt szemlélteti, hogyan kérhető le egy rendszerképet.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Az összes képelemzés beolvasása  

Egy rendszerkép összes elemzésének lekéréséhez állítsa a [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) lekérdezési paramétert a következőre: `All` . A kapcsolódó keresések lekéréséhez a kérelemnek tartalmaznia kell a felhasználó lekérdezési karakterláncát. Ez a példa a [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) használatával mutatja be a rendszerkép megadását.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A legfelső szintű objektum egy [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) objektum, a [képek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objektum helyett.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Entitások felismerése egy rendszerképben  

Az entitások felismerése funkció azonosítja a rendszerképben lévő entitásokat, jelenleg csak a felhasználók számára. A rendszerképben lévő entitások azonosításához állítsa a [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) lekérdezési paramétert a következőre: `RecognizedEntities` .  

> [!NOTE]
> Ezt a modult nem lehet más modullal megadni. Ha más modulokkal is megadja ezt a modult, a válasz nem tartalmazza az elismert entitásokat.  

Az alábbiakban bemutatjuk, hogyan adhatja meg a rendszerképet a [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) paraméter használatával. Ne feledje, hogy az URL-cím kódolja a lekérdezési paramétereket.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Az alábbiakban az előző kérelemre adott válasz látható. Mivel a rendszerkép két személyt tartalmaz, a válasz azonosítja az egyes személyek régióját. Ebben az esetben a rendszer elismerte a személyeket a CelebrityAnnotations és a CelebRecognitionAnnotations csoportokban. A Bing felsorolja az egyes csoportokban lévő személyeket annak valószínűsége alapján, hogy azok megfelelnek az eredeti rendszerképben szereplő személynek. A lista csökkenő megbízhatósági sorrendben van. A CelebRecognitionAnnotations csoport a legmagasabb szintű megbízhatóságot biztosítja a megfelelőnek.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

A `region` mező azonosítja a rendszerkép azon területét, ahol a Bing felismerte az entitást. Az emberek számára a régió a személy arcát jelképezi.  

A négyszög értékei az eredeti rendszerkép szélességéhez és magasságához képest, a 0,0 – 1,0 tartományba tartoznak. Ha például a rendszerkép 300x200, a bal oldali sarokban pedig az a pont (10, 20), a jobb alsó sarokban pedig pont (290, 150), akkor a normalizált négyszög a következő:  

-   Bal: 10/300 = 0,03333...  
-   Top: 20/200 = 0,1  
-   Jobb: 290/300 = 0,9667...  
-   Alsó: 150/200 = 0,75  

Használhatja azt a régiót, amelyet a Bing a későbbi megállapítások során ad vissza. Például a felismert entitás vizuálisan hasonló képeinek beszerzéséhez. További információ: a lemezképek vizuálisan hasonló és entitás-felismerő modulokkal való használatának megtartása. A következő táblázat a régió mezői és a képek körülvágásához használt lekérdezési paraméterek közötti leképezést mutatja.  

-   Bal oldali térképek a [Cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal) -hoz  
-   Top Maps – [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   Jobb Térkép az [autóhoz](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   Alsó Térkép a [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab) -hez  

## <a name="finding-visually-similar-images"></a>Vizuálisan hasonló rendszerképek keresése  

Az eredeti képhez hasonló rendszerképek kereséséhez állítsa a [modulok](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) lekérdezési paraméterét SimilarImages értékre.  

A következő kérelemből megtudhatja, hogyan kérhet le vizuálisan hasonló képeket. A kérelem a [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) lekérdezési paramétert használja az eredeti rendszerkép azonosítására. A relevancia javítása érdekében fel kell vennie a felhasználó lekérdezési karakterláncát.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Az alábbiakban az előző kérelemre adott válasz látható.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Képek levágása a vizuálisan hasonló és az entitás-felismerő modulok használatával  

Ha meg szeretné határozni, hogy a Bing milyen képet használ a képek vizuális megjelenítéséhez, vagy az entitások felismeréséhez, használja a [Cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)és [Car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) lekérdezési paramétereket. Alapértelmezés szerint a Bing a teljes rendszerképet használja.  

A paraméterek a Bing által az összehasonlításhoz használt régió jobb felső, bal felső sarkát és alsó sarkát adják meg. Az értékeket az eredeti rendszerkép szélességének és magasságának részeként határozza meg. A frakciós értékek a felső sarokban (0,0, 0,0) kezdődnek, és a jobb alsó sarokban (1,0, 1,0) végződik. Ha például meg szeretné adni, hogy a felső, bal oldali sarokban a bal oldalon lefelé haladva egy negyedet indít el a bal oldali oldalról, az `cal` 0,25 és 0,25 értékre állítva `cat` .  

A következő hívási folyamat a termesztési régió megadásának hatását mutatja be. Az első hívás nem tartalmazza a levágást és a Bing a rendszerkép közepén található két személyt.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

A válasz két felismert entitást mutat be.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

A második hívás a képet függőlegesen lefelé vágja, a Bing pedig egyetlen személyt ismer fel a rendszerkép jobb oldalán.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A válasz egy felismert entitást mutat be.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Vizuálisan hasonló termékek keresése  

Az eredeti képen található termékekhez hasonló termékeket tartalmazó képek megkereséséhez állítsa a [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) lekérdezési paramétert SimilarProducts értékre.  

Az alábbi kérelemből megtudhatja, hogyan kérhet le vizuálisan hasonló termékek képeit. A kérelem a [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) lekérdezési paramétert használja az előző kérelemben visszaadott eredeti rendszerkép azonosítására. A relevancia javítása érdekében fel kell vennie a felhasználó lekérdezési karakterláncát.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Az alábbiakban az előző kérelemre adott válasz látható. A válasz egy hasonló termék képét tartalmazza, és azt jelzi, hogy hány kereskedő kínálja online termékét, hogy vannak-e termékek minősítése, és a legalacsonyabb ár található (lásd a `aggregateOffer` mezőt).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

A terméket online biztosító kereskedők listájának lekéréséhez (lásd a [offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) mezőt), hívja meg újra az API-t, és állítsa a ShoppingSources értékre `modules` . Ezután állítsa a `insightsToken` lekérdezési paramétert a termék összegzési képében található jogkivonatra.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

A következő az előző kérelemre adott válasz.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```