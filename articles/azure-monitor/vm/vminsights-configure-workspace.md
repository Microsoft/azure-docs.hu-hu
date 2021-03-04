---
title: Log Analytics munkaterület konfigurálása a virtuális gépekhez
description: Útmutató a virtuális gépek által használt Log Analytics munkaterület létrehozásához és konfigurálásához.
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: 5a0e04772cfc1c9be77c0ad8b32b0e93be8b9f54
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046720"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>Log Analytics munkaterület konfigurálása a virtuális gépekhez
A VM-elemzések a Azure Monitor egy vagy több Log Analytics munkaterületéről gyűjtik az adatokat. Az ügynökök bevezetését megelőzően létre kell hoznia és konfigurálnia kell egy munkaterületet. Ez a cikk a munkaterület követelményeit és a virtuális gépek megállapításához szükséges konfigurálását ismerteti.

## <a name="overview"></a>Áttekintés
Egyetlen előfizetés tetszőleges számú munkaterületet használhat a követelményektől függően. A munkaterület egyetlen követelménye, hogy egy támogatott helyen legyen elhelyezve, és a *VMInsights* -megoldással legyen konfigurálva.

Miután konfigurálta a munkaterületet, a rendelkezésre álló lehetőségek bármelyikével telepítheti a szükséges ügynököket a virtuális gépen és a virtuálisgép-méretezési csoporton, és megadhatja a munkaterületet, amellyel elküldheti őket. A VM-elemzések az előfizetésében bármely konfigurált munkaterületről gyűjtenek adatokat.

> [!NOTE]
> Ha a virtuálisgép-bepillantást egyetlen virtuális gépre vagy virtuálisgép-méretezési csoportra engedélyezi a Azure Portal használatával, lehetősége van kijelölni egy meglévő munkaterületet, vagy újat létrehozni. Ha még nem tette meg, akkor a *VMInsights* -megoldás erre a munkaterületre lesz telepítve. Ezt a munkaterületet más ügynökök számára is használhatja.


## <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

>[!NOTE]
>Az ebben a szakaszban ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek. 

A **log Analytics munkaterületek** menüjében lévő Azure Portal log Analytics munkaterületek eléréséhez.

[![Anlytics-munkaterületek naplózása](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

Az alábbi módszerek bármelyikével létrehozhat egy új Log Analytics munkaterületet. A környezetében használt munkaterületek számának meghatározásához, valamint a hozzáférési stratégia kialakításához tekintse meg a [Azure monitor naplók üzembe helyezésének megtervezése](../logs/design-logs-deployment.md) című témakört.


* [Azure Portal](../logs/quick-create-workspace.md)
* [Azure CLI](../logs/quick-create-workspace-cli.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>Támogatott régiók
A VM-alapú adatáttekintések Log Analytics munkaterületet támogatnak a [log Analytics által támogatott bármely régióban](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all) , a következők kivételével:

- Középnyugat-Németország
- Dél-Korea középső régiója

>[!NOTE]
>Bármelyik régióban nyomon követheti az Azure-beli virtuális gépeket. Maguk a virtuális gépek nem korlátozódnak a Log Analytics munkaterület által támogatott régiókra.

## <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés
A virtuális gépekhez tartozó funkciók engedélyezéséhez és eléréséhez a munkaterületen a [log Analytics közreműködő szerepkörrel](../logs/manage-access.md#manage-access-using-azure-permissions) kell rendelkeznie. A teljesítmény-, állapot-és leképezési adat megtekintéséhez az Azure-beli virtuális gép [figyelési olvasójának szerepkörrel](../roles-permissions-security.md#built-in-monitoring-roles) kell rendelkeznie. A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../logs/manage-access.md)című témakörben olvashat bővebben.

## <a name="add-vminsights-solution-to-workspace"></a>VMInsights-megoldás hozzáadása a munkaterülethez
Ahhoz, hogy egy Log Analytics munkaterület felhasználható legyen a virtuálisgép-megállapításokkal, telepíteni kell a *VMInsights* -megoldást. A munkaterület konfigurálásának módszereit az alábbi szakaszokban ismertetjük.

> [!NOTE]
> Amikor hozzáadja a *VMInsights* -megoldást a munkaterülethez, a munkaterülethez csatlakozó összes meglévő virtuális gép elkezdi az adatküldést a InsightsMetrics. A többi adattípussal kapcsolatos adatok gyűjtése addig nem történik meg, amíg hozzá nem adja a Dependency Agent a munkaterülethez csatlakozó meglévő virtuális gépekhez.

### <a name="azure-portal"></a>Azure Portal
A Azure Portal használatával három lehetőség közül választhat egy meglévő munkaterület konfigurálásához. Mindegyiket alább mutatjuk be.

Egyetlen munkaterület konfigurálásához lépjen a **Virtual Machines** lehetőségre a **Azure monitor** menüben, válassza ki a **többi** bevezetési beállítást, majd **konfigurálja a munkaterületet**. Válasszon egy előfizetést és egy munkaterületet, majd kattintson a **Konfigurálás** elemre.

[![Munkaterület konfigurálása](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

Több munkaterület konfigurálásához válassza a Azure Portal **figyelés** menüjének **Virtual Machines** menüjének **munkaterület konfigurációja** lapját. Állítsa be a szűrési értékeket a meglévő munkaterületek listájának megjelenítéséhez. Jelölje be az egyes munkaterületek melletti jelölőnégyzetet az engedélyezéshez, majd kattintson a **kijelölt konfigurálása** elemre.

[![Munkaterület konfigurálása](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Ha a virtuálisgép-bepillantást egyetlen virtuális gépre vagy virtuálisgép-méretezési csoportra engedélyezi a Azure Portal használatával, lehetősége van kijelölni egy meglévő munkaterületet, vagy újat létrehozni. Ha még nem tette meg, akkor a *VMInsights* -megoldás erre a munkaterületre lesz telepítve. Ezt a munkaterületet más ügynökök számára is használhatja.

[![Egyetlen virtuális gép engedélyezése a portálon](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager-sablon
A virtuálisgép-információk Azure Resource Manager sablonjai a GitHub-tárházból [letölthető](https://aka.ms/VmInsightsARMTemplates)archív fájlban (. zip) vannak megadva. Ez magában foglalja a **ConfigureWorkspace** nevű sablont, amely egy log Analytics munkaterületet KONFIGURÁL a virtuális gépekhez. Ezt a sablont a szabványos módszerek bármelyikével központilag telepítheti, beleértve az alábbi minta PowerShell-és CLI-parancsokat: 

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
- Az ügynökök a VM-információkhoz való összekapcsolásával kapcsolatban tekintse meg az [ügynökök](vminsights-enable-overview.md) bevezetését ismertető témakört.
- A megoldásból a munkaterületre továbbított adatok mennyiségének korlátozásához tekintse meg [Azure monitor (előzetes verzió) figyelési megoldásait](../insights/solution-targeting.md) .
