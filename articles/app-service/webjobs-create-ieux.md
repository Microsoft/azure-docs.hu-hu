---
title: Háttérben futó feladatok futtatása webjobs-feladatokkal
description: Ismerje meg, hogyan használhatók a webjobs a háttérben végzett feladatok futtatásához Azure App Serviceban. Számos parancsfájl-formátum közül választhat, és futtathatja őket CRON-kifejezésekkel.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452798"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Háttérben futó feladatok futtatása webjobs-feladatokkal Azure App Service

Az Azure-ban az Azure app Service webjobs- [feladataival](./webjobs-create-ieux-conceptual.md) biztosíthatja a háttérben futó feladatok futtatásának koncepcióját. Útmutató az üzembe helyezéshez <abbr title="Egy program vagy parancsfájl ugyanabban a példányban, mint egy webalkalmazás, egy API-alkalmazás vagy egy mobil alkalmazás.">WebJobs</abbr> végrehajtható fájl vagy parancsfájl feltöltéséhez a [Azure Portal](https://portal.azure.com) használatával. 

Három támogatott webjobs többek között:

* **Folyamatos**: azonnal elindul, jellemzően végtelen ciklusban fut.
* **Ütemezve**: az ütemezett triggertől indul
* **Manuális**: indítás manuális triggerből

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> További lépések

* [További információ a háttérbeli feladatokról webjobsként](./webjobs-create-ieux-conceptual.md)
* [Webjobs-feladatok naplózási előzményeinek megtekintése](./webjobs-create-ieux-view-log.md)

* A [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) segítségével számos programozási feladatot egyszerűsítheti

* Webjobs [-feladatok fejlesztése és üzembe helyezése a Visual Studióval](webjobs-dotnet-deploy-vs.md)
