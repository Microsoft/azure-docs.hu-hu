---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c63cee1c451918569e9b7aa2e76209e8069d86ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92167652"
---
Azt határozza meg, hogy hány függvényt kell összesíteni a [Application Insights metrikáinak kiszámításakor](../articles/azure-functions/configure-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Tulajdonság |Alapértelmezett  | Leírás |
|---------|---------|---------| 
|batchSize|1000|Az összesíteni kívánt kérelmek maximális száma.| 
|flushTimeout|00:00:30|Az összesíteni kívánt időszak maximális időtartama.| 

A függvény meghívásait a rendszer összesíti, ha eléri a két korlát első számát.
