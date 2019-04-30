---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
origin.date: 02/21/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036263"
---
## <a name="timeout"></a>Függvény alkalmazás időtúllépési időtartama 

Függvényalkalmazás időtúllépési időtartamát határozza meg a functionTimeout tulajdonságot a [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) soubor projektu. Az alábbi táblázat az alapértelmezett és maximális értékek mindkét csomagok percek alatt, és mindkét futtatókörnyezet-verzió:

| Felkészülés | Verze modulu Runtime | Alapértelmezett | Maximum |
|------|---------|---------|---------|
| Használat | 1.x | 5 | 10 |
| Használat | 2.x | 5 | 10 |
| App Service | 1.x | Korlátlan | Korlátlan |
| App Service | 2.x | 30 | Korlátlan |

> [!NOTE] 
> Függvény alkalmazás időtúllépési beállítástól függetlenül 230 másodperc az a maximális, ameddig a kérelem egy HTTP által aktivált függvényt is igénybe vehet. Ez az, mert a [Azure Load Balancer üresjárati időkorlát alapértelmezett](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). A hosszabb feldolgozási időt, fontolja meg a [Durable Functions aszinkron minta](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) vagy [késlelteti a tényleges munka és a egy azonnali választ adja vissza](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).

