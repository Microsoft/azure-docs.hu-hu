---
title: Cognitive Services erőforrás létrehozása az Azure felügyeleti ügyféloldali kódtár használatával
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services-erőforrások létrehozása és kezelése az Azure felügyeleti ügyféloldali kódtár használatával.
services: cognitive-services
keywords: kognitív szolgáltatások, kognitív intelligencia, kognitív megoldások, AI-szolgáltatások
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 03/15/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e042ac263d3a30a9790ba6a3a3d404e5e9cb9aad
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472155"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Gyors útmutató: Cognitive Services-erőforrás létrehozása az Azure felügyeleti ügyféloldali kódtár használatával

Ezzel a rövid útmutatóval Azure Cognitive Services-erőforrásokat hozhat létre és kezelhet az Azure felügyeleti ügyféloldali kódtár használatával.

Az Azure Cognitive Services a felhőalapú szolgáltatások olyan családja, amely REST API-kkal és ügyféloldali kódtárakkal segíti a fejlesztőket abban, hogy az alkalmazásaikban kognitív intelligenciát hozzanak létre. A fejlesztőknek nincs szükségük a közvetlen mesterséges intelligencia (AI) vagy az adattudományi képességek vagy az ismeretek elérésére a siker érdekében. Az Azure Cognitive Services lehetővé teszi, hogy a fejlesztők könnyen hozzá tudják adni a kognitív funkciókat az alkalmazásaikban olyan kognitív megoldásokkal, amelyek megtekinthetik, meghallgatják, megértették, megértik, sőt, akár

Az egyes AI-szolgáltatásokat az Azure-előfizetésében létrehozott Azure- [erőforrások](../azure-resource-manager/management/manage-resources-portal.md) jelölik. Miután létrehozta az erőforrást, használhatja a generált kulcsokat és végpontokat az alkalmazások hitelesítéséhez.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
