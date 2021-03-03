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
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734044"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Azure-alkalmazás leküldése | műveleti csoportonként 10 Azure-alkalmazás műveletei. | Ugyanaz, mint az alapértelmezett |
| E-mail | 1 000 e-mail művelet egy műveleti csoportban.<br>Legfeljebb 100 e-mail egy órán belül.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/alerts/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| ITSM | 10 ITSM művelet egy műveleti csoportban. | Ugyanaz, mint az alapértelmezett | 
| Logikai alkalmazás | 10 logikai alkalmazás műveletei egy műveleti csoportban. | Ugyanaz, mint az alapértelmezett |
| Forgatókönyv | 10 runbook művelet egy műveleti csoportban. | Ugyanaz, mint az alapértelmezett |
| SMS | 10 SMS művelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 SMS-üzenet jelenik meg.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/alerts/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| Hang | 10 hangművelet egy műveleti csoportban.<br>5 percenként legfeljebb 1 hanghívás.<br>Tekintse meg a [díjszabási információkat](../articles/azure-monitor/alerts/alerts-rate-limiting.md)is. | Ugyanaz, mint az alapértelmezett |
| Webhook | 10 webhook-művelet egy műveleti csoportban.  A webhook-hívások maximális száma 1500 percenként, előfizetésben. A további korlátozások a [Művelet-specifikus információkon](../articles/azure-monitor/alerts/action-groups.md#action-specific-information)érhetők el.  | Ugyanaz, mint az alapértelmezett |