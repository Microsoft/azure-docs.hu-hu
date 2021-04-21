---
title: 'Rövid útmutató: Szövegbányászat Text Analytics ügyféloldali kódtár használatával'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval hangulatelemzést végezhet, és egyéb műveleteket végezhet a Text Analytics API-Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/19/2021
ms.author: aahi
keywords: szövegbányászat, hangulatelemzés, szövegelemzés
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 527936e1da385a3622358fe12efa0f87af4b3fd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765053"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Rövid útmutató: A Text Analytics ügyféloldali kódtár és REST API

Ez a cikk az ügyféloldali kódtár és Text Analytics használatának REST API. Kövesse az alábbi lépéseket a szövegbányászathoz szükséges példakódok kipróbálása érdekében:

* Hangulatelemzés
* Véleménybányászat
* Nyelvfelismerés
* Entitások felismerése
* Személyes azonosításra vonatkozó információk felismerése
* Kulcskifejezések kinyerése


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil `3.0` verziója.
>    * Csak az Ön által használt verzióra vonatkozó utasításokat kövesse.
> * A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ. Éles forgatókönyvek esetén a kötegelt aszinkron metódusok használatát javasoljuk a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi referenciadokumentációt.
> * Ha állapot- vagy aszinkron Text Analytics szeretne használni, tekintse meg a példákat a [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) vagy [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) githubon

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil `3.0` verziója.
> * A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ. Éles forgatókönyvek esetén a kötegelt aszinkron metódusok használatát javasoljuk a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi referenciadokumentációt.
Ha állapot- vagy aszinkron Text Analytics szeretne használni, tekintse meg a példákat a [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) vagy [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics) githubon

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil `3.0` verziója.
>    * Csak az Ön által használt verzióra vonatkozó utasításokat kövesse.
> * A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ. Éles forgatókönyvek esetén a kötegelt aszinkron módszerek használatát javasoljuk a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi referenciadokumentációt.
> * Az ügyféloldali kódtár ezen verzióját Text Analytics [böngészőben is futtathatja.](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil `3.0` verziója.
>    * Csak az Ön által használt verzióra vonatkozó utasításokat kövesse.
> * A cikkben olvasható kód az egyszerűség kedvéért szinkron metódusokat és nem biztonságos hitelesítőadat-tárolót használ. Éles forgatókönyvek esetén a kötegelt aszinkron módszerek használatát javasoljuk a teljesítmény és a méretezhetőség érdekében. Tekintse meg az alábbi referenciadokumentációt. Ha állapot- vagy aszinkron Text Analytics szeretne használni, tekintse meg a [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) vagy Java GitHub-példáit [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * A Text Analytics API legújabb stabil `3.0` verziója.
>    * Csak az Ön által használt verzióra vonatkozó utasításokat kövesse.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megoldás megismerés](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [A Text Analytics áttekintése](../overview.md)
* [Hangulatelemzés](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvfelismerés](../how-tos/text-analytics-how-to-language-detection.md)
