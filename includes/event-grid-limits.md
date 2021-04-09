---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645481"
---
A következő korlátozások vonatkoznak a Azure Event Grid **témakörökre** (a System, az Custom és a partner témakörökre). 

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök az Azure-előfizetések esetében | 100 |
| Esemény-előfizetések/témakör | 500 |
| Közzétételi arány egyéni vagy partneri témakörben (bejövő forgalom) | 5 000 esemény/mp vagy 5 MB/s (amelyik előbb teljesül) |
| Esemény mérete | 1 MB  |
| Privát végponti kapcsolatok/témakör  | 64 | 
| IP-tűzfalszabályok egy témakörben | 16 | 

A következő korlátozások vonatkoznak Azure Event Grid **tartományokra**. 

| Erőforrás | Korlát |
| --- | --- |
| Témakörök/esemény tartománya | 100.000 |
| Esemény-előfizetések témakörben egy tartományon belül | 500 |
| Tartományi hatókörű esemény-előfizetések | 50 |
| Esemény-tartomány közzétételi sebessége (bejövő) | 5 000 esemény/mp vagy 5 MB/s (amelyik előbb teljesül) |
| Azure-előfizetéshez tartozó esemény-tartományok | 100 |
| Privát végponti kapcsolatok tartomány szerint | 64 | 
| IP-tűzfalszabályok tartományon belüli száma | 16 | 


