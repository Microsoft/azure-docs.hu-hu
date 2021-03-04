---
title: A virtuális gépek elemzésének módosítása
description: A VM-elemzések integrációja az Application Change Analysis Integration segítségével megtekintheti az általa érintett virtuális gépeken végrehajtott módosításokat.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046771"
---
# <a name="change-analysis-in-vm-insights"></a>A virtuális gépek elemzésének módosítása
A VM-elemzések integrációja az [Application Change Analysis](../app/change-analysis.md) Integration segítségével megtekintheti az általa érintett virtuális gépeken végrehajtott módosításokat.

## <a name="overview"></a>Áttekintés
Tegyük fel, hogy van egy virtuális gép, amely lassan fut, és meg szeretné vizsgálni, hogy a konfiguráció legutóbbi módosításai hatással lehetnek-e a teljesítményére. A virtuális gép teljesítményét a VM-megállapítások segítségével tekintheti meg, és megtudhatja, hogy az elmúlt egy órában növekszik-e a memóriahasználat. A Change Analysis segítségével eldöntheti, hogy az adott időszakban végrehajtott konfigurációs változások okozták-e ezt a növekedést.

Az Application Change Analysis Service összesíti az [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) változásait, valamint a beágyazott tulajdonságok változásait, például a hálózati biztonsági szabályokat a Azure Resource Managerból. 

## <a name="enabling-change-analysis"></a>Változások elemzésének engedélyezése
A virtuálisgép-elemzésekben a Change Analysis bevezetéséhez regisztrálnia kell a *Microsoft. ChangeAnalysis* erőforrás-szolgáltatót. Amikor első alkalommal indítja el a virtuálisgép-elemzéseket vagy az alkalmazások módosításának elemzését a Azure Portalban, a rendszer automatikusan regisztrálja az erőforrás-szolgáltatót. Az Application Change Analysis egy ingyenes szolgáltatás, amely nem rendelkezik teljesítménnyel az erőforrásokon.

## <a name="view-change-analysis"></a>Változási elemzés megtekintése
A Change Analysis a VM-elemzések **teljesítmény** vagy **Térkép** lapján érhető el a **módosítás** lehetőség kiválasztásával. 

[![Változások vizsgálata](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Kattintson a **módosítások vizsgálata** gombra, hogy elindítsa az alkalmazás változásának elemzése oldalt a virtuális gép számára. A felsorolt módosításokat áttekintve ellenőrizheti, hogy vannak-e olyanok, amelyek okozhatták a problémát. Ha nem biztos benne, hogy egy adott változásról van szó, hivatkozhat a **change by** oszlopra, és meghatározhatja a módosítást végző személyt.

[![Változás részletei](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Következő lépések
- További információ az [alkalmazások változási elemzéséről](../app/change-analysis.md).
- További információ az [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md)-ról. 

