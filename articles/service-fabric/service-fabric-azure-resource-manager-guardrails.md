---
title: Service Fabric Azure Resource Manager üzembe helyezési guardrails
description: Ez a cikk áttekintést nyújt a Service Fabric-fürtök Azure Resource Manager használatával történő telepítésekor előforduló gyakori hibákról és azok elkerüléséről.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86247824"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
Service Fabric-fürt telepítésekor a rendszer behelyezi a guardrails-t, így az érvénytelen fürtkonfiguráció esetén sikertelen lesz a Azure Resource Manager központi telepítés. A következő szakaszokban áttekintheti a fürtökkel kapcsolatos gyakori problémákat és a problémák enyhítéséhez szükséges lépéseket. 

## <a name="durability-mismatch"></a>Nem megfelelő tartósság
### <a name="overview"></a>Áttekintés
Egy Service Fabric típusú csomópont tartóssági értéke egy Azure Resource Manager sablon két különböző szakaszában van definiálva. A virtuálisgép-méretezési csoport erőforrásának virtuálisgép-méretezési csoport bővítmény szakasza, valamint a Service Fabric fürterőforrás csomópont típusa szakasza. Követelmény, hogy az ezekben a fejezetekben szereplő tartóssági érték egyezzen, ellenkező esetben az erőforrás-telepítés sikertelen lesz.

A következő szakasz a virtuálisgép-méretezési csoport bővítmény tartóssági beállításának és a Service Fabric csomópont típusú tartóssági beállításnak a tartóssági eltérését mutatja be:  

**Virtuálisgép-méretezési csoport tartóssági beállítása**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric a csomópont típusának tartóssági beállítása** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Hibaüzenetek
* A virtuálisgép-méretezési csoport tartóssági eltérése nem egyezik a jelenlegi Service Fabric csomópont típusú tartóssági szinttel
* A virtuálisgép-méretezési csoport tartóssága nem felel meg a cél Service Fabric csomópont típusú tartóssági szintnek.
* A virtuálisgép-méretezési csoport tartóssága megfelel a jelenlegi Service Fabric tartóssági szintnek vagy a cél Service Fabric csomópont típusú tartóssági szintnek. 

### <a name="mitigation"></a>Kockázatcsökkentés
A fenti hibaüzenetek bármelyike által jelzett tartóssági eltérés kijavítása:
1. Módosítsa a tartóssági szintet a Azure Resource Manager sablon virtuálisgép-méretezési csoport bővítmény vagy Service Fabric csomópont típusa szakaszában, hogy az értékek megegyezzenek.
2. Telepítse újra a Azure Resource Manager sablont a frissített értékekkel.


## <a name="seed-node-deletion"></a>Mag csomópontjának törlése 
### <a name="overview"></a>Áttekintés
A Service Fabric-fürtök [megbízhatósági szint](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) tulajdonsággal rendelkeznek, amely meghatározza a fürt elsődleges csomópont-típusán futó rendszerszolgáltatások replikáinak számát. A szükséges replikák száma határozza meg, hogy hány csomópontot kell fenntartani a fürt elsődleges csomópont-típusában. Ha az elsődleges csomópont típusú csomópontok száma a megbízhatósági szinthez szükséges minimális érték alá esik, akkor a fürt instabillá válik.  

### <a name="error-messages"></a>Hibaüzenetek 
A rendszer a magok csomópontjának eltávolítási műveletét észlelte, és el lesz utasítva. 
* Ezzel a művelettel csak {0} a lehetséges magok csomópontjai maradnak a fürtben, míg a {1} minimális értékre van szükség.
* A {0} kifelé irányuló kezdőtőke-csomópontok eltávolításának {1} eredményeképpen a fürt a mag csomópont Kvórumának elvesztése miatt leáll. Az egyszerre eltávolítható mag-csomópontok maximális száma {2} .
 
### <a name="mitigation"></a>Kockázatcsökkentés 
Győződjön meg arról, hogy az elsődleges csomópont típusa elegendő Virtual Machines a fürtben megadott megbízhatósághoz. Nem távolíthat el virtuális gépet, ha a virtuálisgép-méretezési csoport a megadott megbízhatósági szinthez tartozó csomópontok minimális száma alá kerül.
* Ha a megbízhatósági szintet helyesen adta meg, győződjön meg arról, hogy az elsődleges csomópont-típusban elegendő csomópont van a megbízhatósági szinten. 
* Ha a megbízhatósági szint helytelen, indítson el egy változást a Service Fabric erőforráson, hogy először csökkentse a megbízhatósági szintet, mielőtt elindítja a virtuálisgép-méretezési csoport műveleteit, és várjon, amíg befejeződik.
* Ha a megbízhatósági szinten bronz van, kövesse az alábbi [lépéseket](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) a fürtben való méretezéshez.

## <a name="next-steps"></a>Következő lépések
* Fürt létrehozása a Windows Servert futtató virtuális gépeken vagy számítógépeken: [Service Fabric Windows Server-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* Hibaelhárítási Service Fabric: [útmutatók](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
