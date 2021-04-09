---
title: Az Azure-beli virtuális gépek karbantartási ellenőrzésének áttekintése a Azure Portal használatával
description: Megtudhatja, hogyan szabályozhatja, hogy az Azure-beli virtuális gépek karbantartását hogyan kell alkalmazni a karbantartási vezérlők használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 290a1e8da4e9b3e8eff171ab2d5837bfc9c381b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552422"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>A platform frissítéseinek kezelése a karbantartási ellenőrzéssel 

A karbantartási vezérlő használatával olyan platform-frissítéseket kezelhet, amelyek nem igényelnek újraindítást. Az Azure gyakran frissíti az infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindításának javítása érdekében. A legtöbb frissítés átlátható a felhasználók számára. Bizonyos érzékeny munkaterhelések, például a játékok, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók nem tolerálják a virtuális gépek lefagyasztásának vagy leválasztásának néhány másodpercét. A karbantartási ellenőrzés lehetőséget biztosít a platform frissítéseinek megvárni, és egy 35 napos időszakon belül alkalmazza őket. 

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre.

A karbantartási ellenőrzéssel a következőket teheti:
- A Batch frissítése egyetlen frissítési csomagba.
- Várjon akár 35 napra a frissítések alkalmazásához. 
- A platform frissítéseinek automatizálása karbantartási ütemterv konfigurálásával vagy [Azure functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)használatával.
- A karbantartási konfigurációk az előfizetések és az erőforráscsoportok között működnek. 

## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek [dedikált gazdagépen](./dedicated-hosts.md)kell lenniük, vagy egy elkülönített virtuálisgép- [mérettel](isolation.md)kell létrehozni.
- Ha a karbantartási ütemterv be van jelentve, legalább 2 óráig kell lennie.
- 35 nap elteltével a rendszer automatikusan alkalmazza a frissítést.
- A felhasználónak **erőforrás-közreműködői** hozzáféréssel kell rendelkeznie.

## <a name="management-options"></a>Felügyeleti beállítások

A karbantartási konfigurációkat a következő lehetőségek bármelyikével hozhatja létre és kezelheti:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

Azure Functions minta: [karbantartási frissítések ütemezése karbantartási ellenőrzéssel és Azure Functionsával](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Következő lépések

További információ: [karbantartás és frissítések](maintenance-and-updates.md).
