---
title: Az Azure Kubernetes Service (ak)-csomópontok automatikus javítása
description: Ismerje meg a csomópontok automatikus javításának funkcióit, valamint azt, hogy az AK Hogyan javítja a törött munkavégző csomópontokat.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105204"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes szolgáltatás (ak) csomópontjának automatikus javítása

Az AK folyamatosan figyeli a munkavégző csomópontok állapotát, és a csomópontok automatikus javítását hajtja végre, ha azok nem megfelelő állapotba kerülnek. Az Azure virtuális gép (VM) platformja [karbantartási feladatokat hajt végre a virtuális gépeken][vm-updates] . 

Az AK-ban és az Azure-beli virtuális gépeken a fürtök szolgáltatásbeli fennakadások minimalizálására is használható.

Ebből a dokumentumból megtudhatja, hogyan viselkedik az automatikus csomópont-javítási funkciók a Windows-és Linux-csomópontokon. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>A nem megfelelő állapotú csomópontok ellenőrzése az AK-ban

Az AK a következő szabályok alapján határozza meg, hogy a csomópont állapota sérült-e, és hogy javításra szorul: 
* A csomópont egy 10 perces időkereten **belül jelentést készít** az egymást követő ellenőrzésekhez.
* A csomópont 10 percen belül nem jelent állapotot.

A csomópontok állapotát manuálisan is megtekintheti a kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Az automatikus javítás működése

> [!Note]
> Az AK a javítási műveleteket kezdeményezi a felhasználói fiókhoz tartozó **AK-remediátortal**.

Ha az AK olyan nem kifogástalan állapotú csomópontot azonosít, amely 10 percen belül sérült marad, az AK a következő műveleteket végzi el:

1. Indítsa újra a csomópontot.
1. Ha az újraindítás sikertelen, a csomópontot rendszerképbe kell állítani.
1. Ha az Alaphelyzetbe állítás sikertelen, hozzon létre és rendszerképbe új csomópontot.

Ha az automatikus javítás nem sikerül, alternatív szervizeléseket vizsgálunk meg az AK-mérnökök. 

Ha az AK egy állapot-ellenőrzés során több nem megfelelő állapotú csomópontot talál, akkor minden egyes csomópontot külön kell kijavítani egy másik javítás megkezdése előtt.

## <a name="next-steps"></a>Következő lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
