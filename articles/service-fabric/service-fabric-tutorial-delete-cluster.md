---
title: Service Fabric-fürt törlése az Azure-ban
description: Ez az oktatóanyag azt mutatja be, hogyan kell törölni egy Azure-ban üzemeltetett Service Fabric-fürtöt és annak összes erőforrását. Dönthet úgy, hogy a fürtöt tartalmazó teljes erőforráscsoportot törli, de az erőforrások külön-külön is törölhetők.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 60c43adddd4799a639c2d58e0293086eb1776588
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791088"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Oktatóanyag: Az Azure-ban futó Service Fabric-fürtök törlése

Ez az oktatóanyag egy sorozat ötödik része, és bemutatja, hogyan törölheti az Azure-ban futó Service Fabric-fürtöt. A Service Fabric-fürtök teljes törléséhez a fürt által használt összes erőforrást is törölnie kell. Két lehetősége van: törölheti a fürtöt tartalmazó erőforráscsoportot (amivel a fürterőforrás mellett az erőforráscsoport összes többi erőforrását is törli), vagy törölheti az adott fürterőforrást és az ahhoz társított erőforrásokat (meghagyva az erőforráscsoport többi erőforrását).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport és a benne található összes erőforrás törlése
> * Kiválasztott erőforrások törlése az erőforráscsoportból

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban sablon használatával
> * [Fürt figyelése](service-fabric-tutorial-monitor-cluster.md)
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * Fürt törlése


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse az [Azure PowerShellt](/powershell/azure//install-az-ps) vagy az [Azure CLI](/cli/azure/install-azure-cli)-t.
* Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>A Service Fabric-fürtöt tartalmazó erőforráscsoport törlése
A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki azt az előfizetés-azonosítót, amellyel el szeretné távolítani a fürtöt.  Az [Azure Portalra](https://portal.azure.com) bejelentkezve keresheti meg az előfizetés azonosítóját. Törölje az erőforráscsoportot és az összes fürterőforrás-erőforrást a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmag vagy az [Group delete](/cli/azure/group) paranccsal.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>A fürterőforrás és a társított erőforrások célzott törlése
Ha az erőforráscsoport csak a törölni kívánt Service Fabric-fürthöz kapcsolódó erőforrásokat tartalmazza, egyszerűbb az egész erőforráscsoportot törölni. Ha csak az erőforráscsoport egyes erőforrásait szeretné törölni, és meg szeretné tartani a fürthöz nem kapcsolódó erőforrásokat, kövesse az alábbi lépéseket.

Listázza az erőforráscsoport erőforrásait:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Minden törölni kívánt erőforráshoz futtassa a következő szkriptet:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

A fürterőforrás törléséhez futtassa a következő szkriptet:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és a benne található összes erőforrás törlése
> * Kiválasztott erőforrások törlése az erőforráscsoportból

Most, hogy elvégezte az oktatóanyagot, tekintse át a következőket:
* Ismerje meg, hogyan vizsgálhat meg és kezelhet egy Service Fabric-fürtöt a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) használatával.
* Útmutató a fürtcsomópontok [Windows operációs rendszerének javításához](service-fabric-patch-orchestration-application.md) .
* Ismerje meg, hogyan összesítheti és gyűjtheti a [Windows-fürtökhöz](service-fabric-diagnostics-event-aggregation-wad.md) kapcsolódó eseményeket, és [beállíthatja log Analytics](service-fabric-diagnostics-oms-setup.md) a fürt eseményeinek figyeléséhez.
