---
title: Azure Monitor for VMs engedélyezése Resource Manager-sablonok használatával
description: Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Monitor for VMs egy vagy több Azure-beli virtuális gép vagy virtuálisgép-méretezési csoport számára Azure PowerShell vagy Azure Resource Manager sablonok használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a719be730c76d8e334195fdc9b35bbcad0d06b13
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619785"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Azure Monitor for VMs engedélyezése Resource Manager-sablonok használatával
Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy virtuális gép vagy virtuálisgép-méretezési csoport számára Resource Manager-sablonok használatával. Ez az eljárás a következő módon használható:

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport
- Az Azure arc-hoz csatlakoztatott hibrid virtuális gép

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre és konfiguráljon egy log Analytics munkaterületet](../insights/vminsights-configure-workspace.md). 
- A [támogatott operációs rendszerekkel](../insights/vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport operációs rendszere támogatott legyen. 

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Létrehoztuk például Azure Resource Manager sablonokat a virtuális gépek és a virtuálisgép-méretezési csoportok bevezetéséhez. Ezek a sablonok olyan forgatókönyveket tartalmaznak, amelyekkel engedélyezheti a figyelést egy meglévő erőforráson, és létrehozhat egy olyan új erőforrást, amelyen engedélyezve van a figyelés.

>[!NOTE]
>A sablont ugyanabba az erőforráscsoporthoz kell telepíteni, amelyben a virtuális gép vagy a virtuálisgép-méretezési csoport engedélyezve van.


A Azure Resource Manager-sablonok a GitHub-tárházból [letölthető](https://aka.ms/VmInsightsARMTemplates) archív fájlban (. zip) vannak megadva. A fájl tartalma olyan mappákat tartalmaz, amelyek az egyes telepítési forgatókönyveket egy sablon és egy paraméter fájlja alapján jelölik. A futtatása előtt módosítsa a paramétereket tartalmazó fájlt, és adja meg a szükséges értékeket. 

A letöltési fájl a következő sablonokat tartalmazza különböző forgatókönyvekhez:

- A **ExistingVmOnboarding** -sablon lehetővé teszi, hogy Azure monitor for VMS, ha a virtuális gép már létezik.
- A **NewVmOnboarding** sablon egy virtuális gépet hoz létre, és lehetővé teszi Azure monitor for VMS számára a figyelését.
- A **ExistingVmssOnboarding** -sablon lehetővé teszi, hogy Azure monitor for VMS, ha a virtuálisgép-méretezési csoport már létezik.
- A **NewVmssOnboarding** sablon virtuálisgép-méretezési csoportokat hoz létre, és lehetővé teszi a Azure monitor for VMS számára a figyelését.
- A **ConfigureWorkspace** -sablon úgy konfigurálja a log Analytics munkaterületet, hogy támogassa a Azure monitor for VMS a Linux és a Windows operációs rendszer teljesítményszámlálói által kínált megoldások és gyűjtemények engedélyezésével.

>[!NOTE]
>Ha a virtuálisgép-méretezési csoportok már jelen voltak, és a frissítési szabályzat **manuálisra** van állítva, akkor a **ExistingVmssOnboarding** Azure Resource Manager-sablon futtatása után a rendszer alapértelmezés szerint nem engedélyezi a példányok számára a Azure monitor for VMS. A példányokat manuálisan kell frissítenie.

## <a name="deploy-templates"></a>Sablonok üzembe helyezése
A sablonok a [Resource Manager-sablonok bármely üzembe helyezési módszerével](../../azure-resource-manager/templates/deploy-powershell.md) üzembe helyezhetők, beleértve az alábbi példákat a PowerShell és a parancssori felület használatával.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Következő lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el.

- A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).

- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:.
