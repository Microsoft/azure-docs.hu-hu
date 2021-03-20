---
title: Csomópont típusának hozzáadása Azure Service Fabric-fürthöz
description: Megtudhatja, hogyan bővítheti Service Fabric-fürtöt egy virtuálisgép-méretezési csoport hozzáadásával.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719325"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Service Fabric-fürt méretezése virtuálisgép-méretezési csoport hozzáadásával
Ez a cikk azt ismerteti, hogyan méretezhető egy Azure Service Fabric-fürt, ha új csomópont-típust ad hozzá egy meglévő fürthöz. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A virtuálisgép-méretezési csoportok egy Azure-beli számítási erőforrás, amely készletként telepíti és felügyeli a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni. Service Fabric-fürt létrehozása után vízszintesen méretezheti a fürtöt úgy, hogy új csomópont-típust (virtuálisgép-méretezési csoport) ad hozzá egy meglévő fürthöz.  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>További méretezési csoport hozzáadása meglévő fürthöz
Ha egy új csomópont-típust (amely egy virtuálisgép-méretezési csoport) használ egy meglévő fürthöz, az [elsődleges csomópont típusának frissítése](service-fabric-scale-up-primary-node-type.md)hasonló, kivéve, ha nem ugyanazt a NodeTypeRef használja; Természetesen nem fogja letiltani a virtuálisgép-méretezési csoportok aktív használatát, és nem fogja elveszíteni a fürt rendelkezésre állását, ha nem frissíti az elsődleges csomópont típusát. 

A NodeTypeRef tulajdonság deklarálva van a virtuálisgép-méretezési csoporton belül Service Fabric bővítmény tulajdonságaiban:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Emellett ezt az új csomópont-típust is hozzá kell adnia a Service Fabric fürterőforrás-erőforráshoz:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [méretezheti fel az elsődleges csomópont típusát](service-fabric-scale-up-primary-node-type.md)
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.

