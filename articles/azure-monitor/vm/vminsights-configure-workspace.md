---
title: Azure Monitor for VMs Log Analytics munkaterület konfigurálása
description: Útmutató a Azure Monitor for VMs által használt Log Analytics munkaterület létrehozásához és konfigurálásához.
ms.subservice: ''
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: b84f9cae848d53cf04e1b77810b347786e122c5b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619888"
---
# <a name="configure-log-analytics-workspace-for-azure-monitor-for-vms"></a>Azure Monitor for VMs Log Analytics munkaterület konfigurálása
Azure Monitor for VMs adatokat gyűjt a Azure Monitor egy vagy több Log Analytics munkaterületéről. Az ügynökök bevezetését megelőzően létre kell hoznia és konfigurálnia kell egy munkaterületet. Ez a cikk a munkaterület követelményeit és a Azure Monitor for VMs konfigurálását ismerteti.

## <a name="overview"></a>Áttekintés
Egyetlen előfizetés tetszőleges számú munkaterületet használhat a követelményektől függően. A munkaterület egyetlen követelménye, hogy egy támogatott helyen legyen elhelyezve, és a *VMInsights* -megoldással legyen konfigurálva.

Miután konfigurálta a munkaterületet, a rendelkezésre álló lehetőségek bármelyikével telepítheti a szükséges ügynököket a virtuális gépen és a virtuálisgép-méretezési csoporton, és megadhatja a munkaterületet, amellyel elküldheti őket. A Azure Monitor for VMs minden konfigurált munkaterületről gyűjt adatokat az előfizetésében.

> [!NOTE]
> Ha a Azure Portal használatával engedélyezi a Azure Monitor for VMs használatát egyetlen virtuális gépen vagy virtuálisgép-méretezési csoporton, lehetősége van egy meglévő munkaterület kiválasztására, vagy újat létrehozni. Ha még nem tette meg, akkor a *VMInsights* -megoldás erre a munkaterületre lesz telepítve. Ezt a munkaterületet más ügynökök számára is használhatja.


## <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek. 

A **log Analytics munkaterületek** menüjében lévő Azure Portal log Analytics munkaterületek eléréséhez.

[![Anlytics-munkaterületek naplózása](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Az alábbi módszerek bármelyikével létrehozhat egy új Log Analytics munkaterületet. A környezetében használt munkaterületek számának meghatározásához, valamint a hozzáférési stratégia kialakításához tekintse meg a [Azure monitor naplók üzembe helyezésének megtervezése](../platform/design-logs-deployment.md) című témakört.


* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

## <a name="supported-regions"></a>Támogatott régiók
Azure Monitor for VMs a [log Analytics által támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) egyikében log Analytics munkaterületet támogat, a következők kivételével:

- Középnyugat-Németország
- Dél-Korea középső régiója

>[!NOTE]
>Bármelyik régióban nyomon követheti az Azure-beli virtuális gépeket. Maguk a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés
A Azure Monitor for VMs szolgáltatásainak engedélyezéséhez és eléréséhez a munkaterületen a [log Analytics közreműködő szerepkörrel](../platform/manage-access.md#manage-access-using-azure-permissions) kell rendelkeznie. A teljesítmény-, állapot-és leképezési adat megtekintéséhez az Azure-beli virtuális gép [figyelési olvasójának szerepkörrel](../platform/roles-permissions-security.md#built-in-monitoring-roles) kell rendelkeznie. A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../platform/manage-access.md)című témakörben olvashat bővebben.

## <a name="add-vminsights-solution-to-workspace"></a>VMInsights-megoldás hozzáadása a munkaterülethez
Ahhoz, hogy egy Log Analytics munkaterület használható legyen a Azure Monitor for VMsval, telepíteni kell a *VMInsights* -megoldást. A munkaterület konfigurálásának módszereit az alábbi szakaszokban ismertetjük.

> [!NOTE]
> Amikor hozzáadja a *VMInsights* -megoldást a munkaterülethez, a munkaterülethez csatlakozó összes meglévő virtuális gép elkezdi az adatküldést a InsightsMetrics. A többi adattípussal kapcsolatos adatok gyűjtése addig nem történik meg, amíg hozzá nem adja a Dependency Agent a munkaterülethez csatlakozó meglévő virtuális gépekhez.

### <a name="azure-portal"></a>Azure Portal
A Azure Portal használatával három lehetőség közül választhat egy meglévő munkaterület konfigurálásához. Mindegyiket alább mutatjuk be.

Egyetlen munkaterület konfigurálásához lépjen a **Virtual Machines** lehetőségre a **Azure monitor** menüben, válassza ki a **többi** bevezetési beállítást, majd **konfigurálja a munkaterületet**. Válasszon egy előfizetést és egy munkaterületet, majd kattintson a **Konfigurálás** elemre.

[![Munkaterület konfigurálása](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Több munkaterület konfigurálásához válassza a Azure Portal **figyelés** menüjének **Virtual Machines** menüjének **munkaterület konfigurációja** lapját. Állítsa be a szűrési értékeket a meglévő munkaterületek listájának megjelenítéséhez. Jelölje be az egyes munkaterületek melletti jelölőnégyzetet az engedélyezéshez, majd kattintson a **kijelölt konfigurálása** elemre.

[![Munkaterület konfigurálása](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Ha a Azure Portal használatával engedélyezi a Azure Monitor for VMs használatát egyetlen virtuális gépen vagy virtuálisgép-méretezési csoporton, lehetősége van egy meglévő munkaterület kiválasztására, vagy újat létrehozni. Ha még nem tette meg, akkor a *VMInsights* -megoldás erre a munkaterületre lesz telepítve. Ezt a munkaterületet más ügynökök számára is használhatja.

[![Egyetlen virtuális gép engedélyezése a portálon](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-sablon
A Azure Monitor for VMs Azure Resource Manager sablonjai a GitHub-tárházból [letölthető](https://aka.ms/VmInsightsARMTemplates)archív fájlban (. zip) vannak megadva. Ez magában foglalja a **ConfigureWorkspace** nevű sablont, amely egy log Analytics munkaterületet konfigurál Azure monitor for VMshoz. Ezt a sablont a szabványos módszerek bármelyikével központilag telepítheti, beleértve az alábbi minta PowerShell-és CLI-parancsokat: 

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>További lépések
- Az ügynökök Azure Monitor for VMshoz való összekapcsolásához tekintse [meg az ügynökök Azure monitor for VMS](vminsights-enable-overview.md) .
- A megoldásból a munkaterületre továbbított adatok mennyiségének korlátozásához tekintse meg [Azure monitor (előzetes verzió) figyelési megoldásait](../insights/solution-targeting.md) .
