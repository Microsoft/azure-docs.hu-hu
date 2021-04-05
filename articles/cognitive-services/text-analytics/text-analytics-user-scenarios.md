---
title: Példa a Text Analytics API felhasználói forgatókönyvei
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan integrálhatja a Text Analytics APIt a szolgáltatásaiba és folyamataiba.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97505358"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Példa a Text Analytics API felhasználói forgatókönyvei

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett természetes nyelvi feldolgozást biztosít szövegen keresztül. Ez a cikk néhány példát mutat be arra, hogyan integrálhatja az API-t üzleti megoldásaiba és folyamataiba. 

## <a name="analyze-survey-results"></a>Felmérés eredményeinek elemzése

Elemzéseket készíthet az ügyfelek és az alkalmazottak felmérési eredményeiről azáltal, hogy a nyers szöveges válaszokat a Hangulatelemzés használatával dolgozza fel. Összesítheti az elemzés, a nyomon követés és a bevezetések eredményeit.

![Egy rendszerkép, amely leírja, hogyan végezheti el az ügyfelek és az alkalmazottak felméréseit.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Rögzített bejövő ügyfelek hívásainak elemzése

Kinyerheti az ügyfelek által kezdeményezett bepillantásokat beszéd – szöveg, Hangulatelemzés és Kulcsszókeresés használatával. Jelenítse meg az eredményeket Power BI irányítópulton vagy egy portálon az ügyfelek jobb megismeréséhez, az ügyfélszolgálat trendjeinek kiemeléséhez és az ügyfelek bevonásához. API-kéréseket küldhet a jelentéskészítési kötegként, vagy valós időben a beavatkozáshoz. Tekintse meg a mintakód [a githubon](https://github.com/rlagh2/callcenteranalytics).

![Egy rendszerkép, amely leírja, hogyan automatizálható az ügyfélszolgálati hívások beszerzése az adatelemzések használatával](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Támogatási incidensek feldolgozása és kategorizálása

Az Kulcsszókeresés és az entitások felismerésével feldolgozhatja a strukturálatlan szöveges formátumban küldött támogatási kérelmeket. A kinyert kifejezésekkel és entitásokkal kategorizálhatja az erőforrás-tervezésre és a trend elemzésére vonatkozó kéréseket.

![Egy rendszerkép, amely leírja, hogyan használható a Key kifejezés kinyerése és az entitások felismerése az incidensek jelentéseinek és trendjeinek kategorizálásához](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>A termék közösségi média-hírcsatornáinak monitorozása

Felhasználói termékkel kapcsolatos visszajelzések figyelése a termék Twitter-vagy Facebook-oldalán. Az adatok segítségével elemezheti az ügyfelek hangulatát az új termékek bevezetésekor, kinyerheti a funkciókkal és szolgáltatásokkal kapcsolatos fontos kifejezéseket, vagy megtekintheti az ügyfelekre vonatkozó panaszokat. Tekintse meg a [Microsoft Power automatizáló sablon](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)példáját.

![Egy rendszerkép, amely leírja, hogyan figyelheti meg a termékeit és a vállalati visszajelzéseket a közösségi médián a kulcsfontosságú kifejezés kibontásával](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Bizalmas adatokat tartalmazó dokumentumok besorolása és kivonása

Az elnevezett entitások felismerésével azonosíthatja a dokumentumok személyes és bizalmas információit. A dokumentumok osztályozása vagy kivonása, hogy biztonságosan megoszthatók legyenek.

![Egy rendszerkép, amely leírja, hogyan használható a személyes adatok észlelése és kivonása a dokumentumok használatával](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>A vélemények kitermelésének elvégzése

A termék vagy szolgáltatás felmérésekben, vásárlói visszajelzésekben vagy a szövegben való megjelenésével kapcsolatos véleményeket tartalmazó csoportok véleményezése. Ezzel segítséget nyújt a termékek üzembe helyezéséhez és fejlesztéséhez, a marketinggel kapcsolatos erőfeszítésekhez, illetve a termék vagy szolgáltatás teljesítményének kiemeléséhez. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Példa a hotelre vonatkozó véleményekre.":::

## <a name="next-steps"></a>Következő lépések

* [Mi a Text Analytics API?](overview.md)
* [Kérelem küldése a Text Analytics APInak az ügyféloldali kódtár használatával](quickstarts/client-libraries-rest-api.md)
