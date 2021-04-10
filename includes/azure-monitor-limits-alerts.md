---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734064"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Metrikus riasztások (klasszikus) |100 aktív riasztási szabályok/előfizetés. | Telefonos támogatás |
| Metrikákhoz kapcsolódó riasztások |5 000 aktív riasztási szabályok/előfizetés az Azure Public, az Azure China 21Vianet és a Azure Government felhőkben. Ha eléri ezt a korlátot, vizsgálja meg, hogy használhat-e [azonos típusú több erőforrást használó riasztásokat](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 metrikai idő sorozatú riasztási szabály. | Hívja a támogatási szolgálatot. |
| Tevékenységnapló-riasztások | 100 aktív riasztási szabály/előfizetés (nem növelhető). | Ugyanaz, mint az alapértelmezett |
| Naplóriasztások | 512 aktív riasztási szabályok/előfizetés. 200 aktív riasztási szabályok/erőforrás. | Telefonos támogatás |
| Riasztási szabályok és műveleti szabályok leírásának hossza| Naplók keresése riasztások 4096 karakter<br/>Minden egyéb 2048 karakter | Ugyanaz, mint az alapértelmezett |