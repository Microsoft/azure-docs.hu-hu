---
title: Azure CDN erőforrások állapotának monitorozása | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure CDN erőforrásainak állapotát a Azure Resource Health használatával.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92778161"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Az Azure CDN-erőforrások állapotának figyelése
  
A Azure CDN Resource Health az [Azure Resource Health](../service-health/resource-health-overview.md)részhalmaza.  Az Azure Resource Health szolgáltatással figyelheti a CDN-erőforrások állapotát, és gyakorlati útmutatást kaphat a problémák elhárításához.

>[!IMPORTANT] 
>A Azure CDN Resource Health szolgáltatás jelenleg csak a globális CDN-kézbesítés és az API-képességek állapotának megfelelő fiókokkal rendelkezik.  Azure CDN erőforrás-állapot nem ellenőrzi az egyes CDN-végpontokat.
>
>A Azure CDN erőforrás-állapotot megtápláló jelek akár 15 percet is igénybe vehetnek.

## <a name="how-to-find-azure-cdn-resource-health"></a>Azure CDN erőforrás állapotának megkeresése

1. A [Azure Portal](https://portal.azure.com)navigáljon a CDN-profilhoz.

2. Kattintson a **Beállítások** gombra.

    ![Beállítások gomb](./media/cdn-resource-health/cdn-profile-settings.png)

3. A *támogatás + hibaelhárítás* területen kattintson az **erőforrás állapota** elemre.

    ![CDN-erőforrás állapota](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>A *Súgó + támogatás* panelen a *Resource Health (erőforrás állapota* ) csempén felsorolt CDN-erőforrásokat is megtalálhatja.  A kör alakúra kattintva gyorsan elérheti a *Súgó + támogatás szolgáltatást* **?** a portál jobb felső sarkában.
>
> ![Súgó és támogatás](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-specifikus üzenetek

A Azure CDN erőforrás-állapottal kapcsolatos állapotok alább találhatók.

|Üzenet | Javasolt művelet |
|---|---|
|Előfordulhat, hogy egy vagy több CDN-végpontot leállított, eltávolított vagy helytelenül konfigurált | Előfordulhat, hogy egy vagy több CDN-végpontot leállított, eltávolított vagy helytelenül konfigurált.|
|Sajnáljuk, de a CDN-kezelési szolgáltatás jelenleg nem érhető el | Térjen vissza ide az állapot frissítéseihez; Ha a probléma a várt megoldási idő után sem szűnik meg, forduljon az ügyfélszolgálathoz.|
|Előfordulhat, hogy a CDN-végpontokra negatív hatással lesznek CDN-szolgáltatóink fennálló problémái | Térjen vissza ide az állapot frissítéseihez; A hibakeresési eszköz segítségével megtudhatja, hogyan tesztelheti a forrás-és a CDN-végpontot; Ha a probléma a várt megoldási idő után sem szűnik meg, forduljon az ügyfélszolgálathoz. |
|A CDN-végpontok konfigurációs módosításainak terjesztése során késedelmek tapasztalhatók | Térjen vissza ide az állapot frissítéseihez; Ha a konfiguráció módosításait a rendszer nem terjeszti teljes mértékben a várt időpontig, forduljon az ügyfélszolgálathoz.|
|Sajnáljuk, de a kiegészítő portál betöltése közben problémák merültek fel | Térjen vissza ide az állapot frissítéseihez; Ha a probléma a várt megoldási idő után sem szűnik meg, forduljon az ügyfélszolgálathoz.|
Sajnáljuk, de problémák merültek fel néhány CDN-szolgáltatónkkal kapcsolatban | Térjen vissza ide az állapot frissítéseihez; Ha a probléma a várt megoldási idő után sem szűnik meg, forduljon az ügyfélszolgálathoz. |

## <a name="next-steps"></a>Következő lépések

- [Az Azure Resource Health áttekintése](../service-health/resource-health-overview.md)
- [CDN-tömörítéssel kapcsolatos problémák elhárítása](./cdn-troubleshoot-compression.md)
- [404 hibával kapcsolatos hibák elhárítása](./cdn-troubleshoot-endpoint.md)