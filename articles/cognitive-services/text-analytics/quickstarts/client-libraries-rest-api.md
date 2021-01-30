---
title: 'Gyors útmutató: a Text Analytics ügyféloldali függvénytárat használó szöveges adatbányászat'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval az Azure-Cognitive Services Text Analytics API használatával elvégezheti a hangulati elemzést és egyebeket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/20/2021
ms.author: aahi
keywords: szöveg-adatbányászat, érzelmek elemzése, szöveges elemzés
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 78d2f0a7b247ac6a9b7bdf5f4fc8b6c7b5ef6eea
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090710"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Gyors útmutató: az Text Analytics ügyféloldali kódtár és REST API használata

Ebből a cikkből megtudhatja, hogyan kezdheti el a Text Analytics ügyféloldali kódtár és a REST API használatát. A következő lépésekkel kipróbálhatja a bányászati szöveghez tartozó példákat:

* Hangulatelemzés
* Vélemény bányászata
* Nyelvfelismerés
* Entitások felismerése
* Személyes azonosítási adatok felismerése
* Kulcskifejezések kinyerése


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil verziója: `3.0` .
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt.
> * Ha Text Analyticst szeretne használni az állapothoz vagy az aszinkron műveletekhez, tekintse meg a következő példát a githubon: [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) vagy [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil verziója: `3.0` .
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt.
Ha Text Analyticst szeretne használni az állapothoz vagy az aszinkron műveletekhez, tekintse meg a következő példát a githubon: [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) vagy [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil verziója: `3.0` .
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt.
> * A [böngészőben](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)a Text Analytics ügyféloldali kódtár ezen verzióját is futtathatja.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil verziója: `3.0` .
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.
> * Az ebben a cikkben található kód az egyszerűség kedvéért a szinkron metódusokat és a nem biztonságos hitelesítő adatokat tároló szolgáltatást használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi dokumentációt. Ha Text Analyticst szeretne használni az állapothoz vagy az aszinkron műveletekhez, tekintse meg a következő példát a githubon: [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) vagy [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil verziója: `3.0` .
>    * Ügyeljen arra, hogy csak az Ön által használt verzió utasításait kövesse.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megoldás megismerése](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [A Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)
