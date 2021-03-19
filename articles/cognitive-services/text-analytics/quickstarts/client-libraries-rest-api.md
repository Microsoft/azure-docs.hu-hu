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
ms.date: 03/11/2021
ms.author: aahi
keywords: szöveg-adatbányászat, érzelmek elemzése, szöveges elemzés
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: d0fae3c12d4315d829ad4d505e7157e3bf919d99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599060"
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
* [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)
