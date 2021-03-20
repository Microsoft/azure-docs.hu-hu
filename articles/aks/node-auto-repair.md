---
title: Az Azure Kubernetes Service (ak)-csomópontok automatikus javítása
description: Ismerje meg a csomópontok automatikus javításának funkcióit, valamint azt, hogy az AK Hogyan javítja a törött munkavégző csomópontokat.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89490105"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Azure Kubernetes szolgáltatás (ak) csomópontjának automatikus javítása

Az AK folyamatosan ellenőrzi a munkavégző csomópontok állapotát, és a csomópontok automatikus javítását hajtja végre, ha azok nem megfelelő állapotba kerülnek. Ez a dokumentum tájékoztatja a kezelőket arról, hogy az automatikus csomópont-javítási funkció hogyan viselkedik a Windows-és Linux-csomópontokon. Az AK-s javításokon kívül az Azure-beli virtuálisgép-platform [karbantartási feladatokat hajt végre Virtual Machineson][vm-updates] is. Az AK-ban és az Azure-beli virtuális gépeken a fürtök szolgáltatásbeli fennakadások minimalizálására is használható.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>A nem megfelelő állapotú csomópontok ellenőrzése az AK-ban

Az AK szabályok segítségével határozza meg, hogy egy csomópont nem kifogástalan állapotú-e, és javításra szorul. Az AK a következő szabályok alapján határozza meg, hogy szükséges-e az automatikus javítás.

* A csomópont egy 10 perces időkereten belül jelentést **küld az** egymást követő ellenőrzéseken
* A csomópont 10 percen belül nem jelent állapotot.

A csomópontok állapotát manuálisan is megtekintheti a kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Az automatikus javítás működése

> [!Note]
> Az AK a javítási műveleteket kezdeményezi a felhasználói fiókhoz tartozó **AK-remediátortal**.

Ha egy csomópont nem kifogástalan állapotú a fenti szabályok alapján, és 10 egymást követő percen belül sérült marad, a következő műveleteket végzi el a rendszer.

1. A csomópont újraindítása
1. Ha az újraindítás sikertelen, a csomópont rendszerképének alaphelyzetbe állítása
1. Ha az Alaphelyzetbe állítás sikertelen, új csomópont létrehozása és alaphelyzetbe állítása

Ha egyik művelet sem sikeres, a további szervizeléseket az AK-mérnökök vizsgálják meg. Ha egy állapot-ellenőrzés során több csomópont nem kifogástalan állapotú, akkor minden egyes csomópontot külön kell kijavítani, mielőtt egy másik javítás megkezdődik.

## <a name="next-steps"></a>Következő lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
