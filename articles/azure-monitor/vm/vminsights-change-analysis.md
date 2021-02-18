---
title: Azure Monitor for VMs elemzésének módosítása
description: Azure Monitor for VMs integráció az Application Change Analysis Integration használatával megtekintheti az általa érintett virtuális gépeken végrehajtott módosításokat.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618238"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Azure Monitor for VMs elemzésének módosítása
Azure Monitor for VMs integráció az [Application Change Analysis](../app/change-analysis.md) Integration használatával megtekintheti az általa érintett virtuális gépeken végrehajtott módosításokat.

## <a name="overview"></a>Áttekintés
Tegyük fel, hogy van egy virtuális gép, amely lassan fut, és meg szeretné vizsgálni, hogy a konfiguráció legutóbbi módosításai hatással lehetnek-e a teljesítményére. A virtuális gép teljesítményét a Azure Monitor for VMs használatával tekintheti meg, és megtudhatja, hogy az elmúlt órában növekszik-e a memória kihasználtsága. A Change Analysis segítségével eldöntheti, hogy az adott időszakban végrehajtott konfigurációs változások okozták-e ezt a növekedést.

Az Application Change Analysis Service összesíti az [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) változásait, valamint a beágyazott tulajdonságok változásait, például a hálózati biztonsági szabályokat a Azure Resource Managerból. 

## <a name="enabling-change-analysis"></a>Változások elemzésének engedélyezése
A Change Analysis Azure Monitor for VMsban való bevezetéséhez regisztrálnia kell a *Microsoft. ChangeAnalysis* erőforrás-szolgáltatót. Amikor első alkalommal elindítja Azure Monitor for VMs vagy az alkalmazás változásának elemzését a Azure Portal, az erőforrás-szolgáltató automatikusan regisztrálva lesz. Az Application Change Analysis egy ingyenes szolgáltatás, amely nem rendelkezik teljesítménnyel az erőforrásokon.

## <a name="view-change-analysis"></a>Változási elemzés megtekintése
A Change Analysis a Azure Monitor for VMs **teljesítmény** vagy **Térkép** lapján érhető el a **módosítás** lehetőség választásával. 

[![Változások vizsgálata](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Kattintson a **módosítások vizsgálata** gombra, hogy elindítsa az alkalmazás változásának elemzése oldalt a virtuális gép számára. A felsorolt módosításokat áttekintve ellenőrizheti, hogy vannak-e olyanok, amelyek okozhatták a problémát. Ha nem biztos benne, hogy egy adott változásról van szó, hivatkozhat a **change by** oszlopra, és meghatározhatja a módosítást végző személyt.

[![Változás részletei](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Következő lépések
- További információ az [alkalmazások változási elemzéséről](../app/change-analysis.md).
- További információ az [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md)-ról. 

