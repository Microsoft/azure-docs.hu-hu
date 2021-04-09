---
title: Service Fabric felügyelt fürt üzembe helyezése (előzetes verzió)
description: Ebben az oktatóanyagban egy Service Fabric felügyelt fürtöt fog üzembe helyezni a teszteléshez.
ms.topic: tutorial
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: fd54e6f8c5272c3a8fe0e0162ca2e57968b24642
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791711"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster-preview"></a>Oktatóanyag: Service Fabric felügyelt fürt üzembe helyezése (előzetes verzió)

Ebben az oktatóanyag-sorozatban a következőket fogjuk megvitatni:

> [!div class="checklist"]
> * Service Fabric felügyelt fürt üzembe helyezése 
> * [Service Fabric felügyelt fürt felskálázása](tutorial-managed-cluster-scale.md)
> * [Csomópontok hozzáadása és eltávolítása Service Fabric felügyelt fürtben](tutorial-managed-cluster-add-remove-node-type.md)
> * [Alkalmazás központi telepítése Service Fabric felügyelt fürtön](tutorial-managed-cluster-deploy-app.md)

A sorozat ezen része a következőket ismerteti:

> [!div class="checklist"]
> * Csatlakozás az Azure-fiókhoz
> * Új erőforráscsoport létrehozása
> * Service Fabric felügyelt fürt üzembe helyezése
> * Elsődleges csomópont típusának hozzáadása a fürthöz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik Azure-előfizetéssel

* Telepítse a [Service FABRIC SDK-t és a PowerShell-modult](service-fabric-get-started.md).

* Telepítse [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric) (vagy újabb).

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Cserélje le az értékét `<your-subscription>` Az Azure-fiókjához tartozó előfizetés-karakterláncra, és kapcsolódjon a következőhöz:

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>Új erőforráscsoport létrehozása

Ezután hozza létre a felügyelt Service Fabric fürthöz tartozó erőforráscsoportot, és cserélje le `<your-rg>` `<location>` a kívánt csoport nevét és helyét.

> [!NOTE]
> A nyilvános előzetes verzióban támogatott régiók a következők:,,,, `centraluseuap` `eastus2euap` `eastasia` `northeurope` `westcentralus` és `eastus2` .

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Service Fabric felügyelt fürt üzembe helyezése

### <a name="create-a-service-fabric-managed-cluster"></a>Felügyelt Service Fabric-fürt létrehozása

Ebben a lépésben egy Service Fabric felügyelt fürtöt fog létrehozni a New-AzServiceFabricManagedCluster PowerShell-paranccsal. Az alábbi példa egy myCluster nevű fürtöt hoz létre a myResourceGroup nevű erőforráscsoport-csoportban. Ez az erőforráscsoport a eastus2 régió előző lépésében lett létrehozva.

Ebben a lépésben adja meg a saját értékeit a következő paraméterekhez:

* **Fürt neve**: adjon meg egy egyedi nevet a fürtnek, például *mysfcluster*.
* **Rendszergazdai jelszó**: adja meg az RDP-hez a fürtben található mögöttes virtuális gépeken használandó jelszót.
* **Ügyféltanúsítvány ujjlenyomata**: adja meg a fürt eléréséhez használni kívánt ügyféltanúsítvány ujjlenyomatát. Ha nem rendelkezik tanúsítvánnyal, kövesse [a tanúsítvány beállítása és lekérése](../key-vault/certificates/quick-create-portal.md) saját aláírású tanúsítvány létrehozásához című témakört.
* **Fürt SKU**: adja meg a telepítendő [Service Fabric felügyelt fürt típusát](overview-managed-cluster.md#service-fabric-managed-cluster-skus) . *Alapszintű* Az SKU-fürtök csak tesztelési célú központi telepítésekhez használhatók, és nem engedélyezik a csomópontok hozzáadását vagy eltávolítását.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Elsődleges csomópont típusának hozzáadása a Service Fabric felügyelt fürthöz

Ebben a lépésben egy elsődleges csomópont-típust ad hozzá az imént létrehozott fürthöz. Minden Service Fabric fürtnek rendelkeznie kell legalább egy elsődleges csomópont-típussal.

Ebben a lépésben adja meg a saját értékeit a következő paraméterekhez:

* **Csomópont típusa neve**: adjon meg egy egyedi nevet a fürthöz hozzáadandó csomópont-típushoz, például "NT1".

> [!NOTE]
> Ha a felvenni kívánt csomópont típusa a fürt első vagy egyetlen csomópontja, akkor az elsődleges tulajdonságot kell használni.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

A parancs végrehajtása több percet is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Miután az üzembe helyezés befejeződött, keresse meg a Service Fabric Explorer értéket a portál Service Fabric felügyelt fürt erőforrásának áttekintés lapján. Ha a rendszer rákérdez a tanúsítványra, használja azt a tanúsítványt, amelyhez az ügyfél ujjlenyomatát adták a PowerShell-parancsban.

## <a name="next-steps"></a>Következő lépések

Ebben a lépésben létrehozta és üzembe helyezte az első Service Fabric felügyelt fürtöt. A fürtök méretezésével kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Service Fabric felügyelt fürt felskálázása](tutorial-managed-cluster-scale.md)
