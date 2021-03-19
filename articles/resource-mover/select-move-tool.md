---
title: Eszköz kiválasztása az Azure-erőforrások régiók közötti áthelyezéséhez
description: Az Azure-erőforrások régiók közötti áthelyezésére szolgáló beállítások és eszközök áttekintése
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90603356"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Eszköz kiválasztása az Azure-erőforrások áthelyezéséhez

Az Azure-ban a következőképpen helyezhet el erőforrásokat:

- **Erőforrások áthelyezése a régiók között**: az erőforrás áthelyezése az erőforrás-mozgató hubhoz vagy egy erőforráscsoporthoz. 
- **Erőforrások áthelyezése erőforráscsoport/előfizetések között**: áthelyezés egy erőforráscsoporthoz. 
- **Erőforrások áthelyezése az Azure-felhők között**: a Azure site Recovery szolgáltatás használatával áthelyezheti az erőforrásokat a nyilvános és a kormányzati felhők között.
- **Erőforrások áthelyezése a rendelkezésre állási zónák között ugyanabban a régióban**: a Azure site Recovery szolgáltatás használatával helyezze át az erőforrásokat a rendelkezésre állási zónák között ugyanabban az Azure-régióban.


## <a name="compare-move-tools"></a>Áthelyezési eszközök összehasonlítása

**Eszköz** | **A következő esetekben használja** | **További információ**
--- | --- | ---
**Áthelyezés az erőforráscsoport között** | Erőforrásokat helyezhet át egy másik erőforráscsoporthoz vagy előfizetésbe, vagy a régiók között.<br/><br/> Ha áthelyezi a régiókat, az erőforráscsoport kiválasztja az áthelyezni kívánt erőforrásokat, majd áthelyezi az erőforrás-mozgató központba, hogy ellenőrizze a függőségeket, és helyezze át az erőforrásokat a célként megadott régióba. | [Erőforrások áthelyezése másik erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Erőforrások áthelyezése egy másik régióba egy erőforráscsoport alapján](move-region-within-resource-group.md).
**Áthelyezés az erőforrás-mozgató hubhoz** | Erőforrások áthelyezése régiók között. <br/><br/> Áthelyezheti a célhelyre, vagy egy adott rendelkezésre állási zónába vagy rendelkezésre állási csoportba a célként megadott régión belül. | [Erőforrások áthelyezése a régiók között az erőforrás-mozgató központban]().
**Virtuális gépek áthelyezése Site Recovery** | Az Azure-beli virtuális gépek kormányzati és nyilvános felhők közötti áthelyezésére használható.<br/><br/> Akkor használja, ha a virtuális gépeket ugyanabban a régióban lévő rendelkezésre állási zónák között szeretné áthelyezni. |[Erőforrások áthelyezése a kormányzati/nyilvános felhők között](../site-recovery/region-move-cross-geos.md), [az erőforrások áthelyezése a rendelkezésre állási zónákba ugyanabban a régióban](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Következő lépések

[További](about-move-process.md) információ az erőforrás-mozgató összetevőkről és az áthelyezési folyamatról.
