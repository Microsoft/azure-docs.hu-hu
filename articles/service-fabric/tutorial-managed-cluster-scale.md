---
title: Service Fabric felügyelt fürt felskálázása (előzetes verzió)
description: Ebből az oktatóanyagból megtudhatja, hogyan bővítheti Service Fabric felügyelt fürt csomópont-típusát.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 769bcb339b2cc1419c7a3d92d0f08130029a9f95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785431"
---
# <a name="tutorial-scale-out-a-service-fabric-managed-cluster-preview"></a>Oktatóanyag: Service Fabric felügyelt fürt felskálázása (előzetes verzió)

Ebben az oktatóanyag-sorozatban a következőket fogjuk megvitatni:

> [!div class="checklist"]
> * [Service Fabric felügyelt fürt üzembe helyezése.](tutorial-managed-cluster-deploy.md)
> * Service Fabric felügyelt fürt felskálázása
> * [Csomópont-típusok hozzáadása és eltávolítása Service Fabric felügyelt fürtben](tutorial-managed-cluster-add-remove-node-type.md)
> * [Alkalmazás központi telepítése Service Fabric felügyelt fürtön](tutorial-managed-cluster-deploy-app.md)

A sorozat ezen része a következőket ismerteti:

> [!div class="checklist"]
> * Service Fabric felügyelt fürtcsomópont méretezése

## <a name="prerequisites"></a>Előfeltételek

* Egy Service Fabric felügyelt fürt (lásd: [*felügyelt fürt üzembe helyezése*](tutorial-managed-cluster-deploy.md)).
* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) vagy újabb (lásd: [*install Azure PowerShell*](/powershell/azure/install-az-ps)).

## <a name="scale-a-service-fabric-managed-cluster"></a>Service Fabric felügyelt fürt méretezése
Módosítsa a példányszámot úgy, hogy növelje vagy csökkentse a méretezni kívánt csomópont típusú csomópontok számát. A csomópont-típusok neve a Azure Resource Manager sablonban (ARM-sablon) található a fürt központi telepítésében vagy a Service Fabric Explorer.  

> [!NOTE]
> Ha a csomópont típusa elsődleges, akkor nem fog tudni egy alapszintű SKU-fürt 3 csomópontja alatt, a standard SKU-fürthöz pedig 5 csomópontot.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "FE"
$instanceCount = "7"

Set-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -name $nodeTypeName -InstanceCount $instanceCount -Verbose
```

A fürt automatikusan megkezdi a frissítését, és néhány perc múlva látni fogja a további csomópontokat.

## <a name="next-steps"></a>Következő lépések

Ebben a lépésben egy csomópont-típust méretezett egy Service Fabric felügyelt fürtön. A csomópont-típusok hozzáadásával és eltávolításával kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Csomópont-típusok hozzáadása és eltávolítása Service Fabric felügyelt fürthöz](tutorial-managed-cluster-add-remove-node-type.md)
