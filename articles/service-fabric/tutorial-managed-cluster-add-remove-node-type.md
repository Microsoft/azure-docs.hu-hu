---
title: Service Fabric felügyelt fürthöz tartozó csomópont-típusok hozzáadása és eltávolítása (előzetes verzió)
description: Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá és távolíthat el egy Service Fabric felügyelt fürthöz tartozó csomópont-típusokat.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 860345f5b297edaeea9d099ac392243176dfaca7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410444"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>Oktatóanyag: csomópont-típusok hozzáadása és eltávolítása Service Fabric felügyelt fürtből (előzetes verzió)

Ebben az oktatóanyag-sorozatban a következőket fogjuk megvitatni:

> [!div class="checklist"]
> * [Service Fabric felügyelt fürt üzembe helyezése](tutorial-managed-cluster-deploy.md)
> * [Service Fabric felügyelt fürt felskálázása](tutorial-managed-cluster-scale.md)
> * Csomópontok hozzáadása és eltávolítása Service Fabric felügyelt fürtben
> * [Alkalmazás központi telepítése Service Fabric felügyelt fürtön](tutorial-managed-cluster-deploy-app.md)

A sorozat ezen része a következőket ismerteti:

> [!div class="checklist"]
> * Csomópont típusának hozzáadása Service Fabric felügyelt fürthöz
> * Csomópont-típus törlése Service Fabric felügyelt fürtből

## <a name="prerequisites"></a>Előfeltételek

* Egy Service Fabric felügyelt fürt (lásd: [*felügyelt fürt üzembe helyezése*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) vagy újabb (lásd: [*install Azure PowerShell*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true)).

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Csomópont típusának hozzáadása Service Fabric felügyelt fürthöz

Service Fabric felügyelt fürthöz Azure Resource Manager sablon, PowerShell vagy CLI használatával adhat hozzá csomópont-típust. Ebben az oktatóanyagban egy csomópont-típust adunk hozzá a Azure PowerShell használatával.

Új csomópont-típus létrehozásához három tulajdonságot kell meghatároznia:

* **Csomópont-típus neve**: a fürtben lévő összes csomópont-típustól egyedi név.
* **Példányszám**: az új csomópont típusú csomópontok kezdeti száma.
* Virtuálisgép- **méret**: virtuális gép SKU a csomópontokhoz. Ha nincs megadva, a rendszer az alapértelmezett értéket használja *Standard_D2* .

> [!NOTE]
> Ha a felvenni kívánt csomópont típusa a fürt első vagy egyetlen csomópontja, akkor az elsődleges tulajdonságot kell használni.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Csomópont típusának eltávolítása Service Fabric felügyelt fürtből

A csomópontok Service Fabric felügyelt fürtből való eltávolításához a PowerShell vagy a CLI eszközt kell használnia. Ebben az oktatóanyagban egy csomópont-típust fogunk eltávolítani Azure PowerShell használatával.

> [!NOTE]
> Nem lehet eltávolítani az elsődleges csomópont típusát, ha a fürt egyetlen elsődleges csomópont-típusa.  

Csomópont típusának eltávolítása:

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>További lépések

 Ebben a szakaszban hozzáadjuk és töröltük a csomópontok típusait. Az alkalmazások Service Fabric felügyelt fürtre történő központi telepítésének megismeréséhez tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Alkalmazás üzembe helyezése Service Fabric felügyelt fürtön](tutorial-managed-cluster-deploy-app.md)
