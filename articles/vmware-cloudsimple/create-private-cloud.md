---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple saját felhő létrehozása
description: Ismerteti, hogyan hozhat létre CloudSimple-alapú privát felhőt a VMware számítási feladatok felhőbe való kiterjesztéséhez, működési rugalmassággal és folytonossággal
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896277"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple saját felhő létrehozása

A privát felhő egy elkülönített VMware-verem, amely támogatja az ESXi-gazdagépeket, a vCenter, a vSAN és a NSX. A privát felhők kezelése a CloudSimple-portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományában. A verem dedikált csomópontokon és elszigetelt operációs rendszer nélküli hardveres csomópontokon fut.

A privát felhő létrehozása segít a hálózati infrastruktúra számos gyakori igényének kielégítésében:

* **Növekedés**. Ha elérte a meglévő infrastruktúra hardveres frissítési pontját, a privát felhő lehetővé teszi, hogy az új hardveres befektetés nélkül bővítse a-t.

* **Gyors bővítés**. Ha bármilyen ideiglenes vagy nem tervezett kapacitás merül fel, a privát felhő lehetővé teszi a további kapacitás késleltetés nélküli létrehozását.

* **Fokozott védelem**. A három vagy több csomópontból álló privát felhővel automatikus redundanciát és magas rendelkezésre állású védelmet érhet el.

* **Hosszú távú infrastrukturális igények**. Ha az adatközpontok kapacitása vagy a költségek csökkentése érdekében szeretné átstrukturálni a költségeket, a privát felhő lehetővé teszi az adatközpontok kivonását és a felhőalapú megoldásba való átállást, miközben továbbra is kompatibilis a vállalati műveletekkel.

Privát felhő létrehozásakor egyetlen vSphere-fürtöt és az abban a fürtben létrehozott összes felügyeleti virtuális gépet kap.

## <a name="before-you-begin"></a>Előkészületek

A csomópontokat a privát felhő létrehozása előtt kell kiépíteni. A csomópontok kiépítésével kapcsolatos további információkért lásd: [csomópontok kiépítése az Azure VMware-megoldáshoz a CloudSimple használatával](create-nodes.md).

CIDR-tartomány kiosztása a vSphere/vSAN alhálózatokhoz a privát felhőben. A privát felhő egy vCenter-kiszolgáló által kezelt elkülönített VMware stack-környezetként (ESXi-gazdagépekkel, vCenter, vSAN és NSX) hozható létre. A felügyeleti összetevők a vSphere/vSAN alhálózatok CIDR kiválasztott hálózatban vannak telepítve. A hálózati CIDR tartománya különböző alhálózatokra van osztva az üzembe helyezés során. A vSphere/vSAN alhálózati címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal. A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák. A vSphere/vSAN alhálózatokkal kapcsolatos további információkért lásd: a VLAN-ok és az alhálózatok áttekintése.

* Minimális vSphere/vSAN alhálózatok CIDR tartományának előtagja:/24
* VSphere/vSAN alhálózatok maximális CIDR-tartományának előtagja:/21


## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Új privát felhő létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services** kifejezésre.
3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.
4. Az **Áttekintés** lapon kattintson a **privát felhő létrehozása** elemre a CloudSimple-portál új böngésző lapjának megnyitásához. Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.

    ![Privát felhő létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. A CloudSimple-portálon adja meg a saját felhő nevét.
6. Válassza ki a saját felhő **helyét** .
7. Válassza ki a **csomópont típusát**, amely megfelel az Azure-ban kiépített lehetőségeknek.
8. **Csomópontok számának** meghatározása  A privát felhő létrehozásához legalább három csomópont szükséges.

    ![Privát felhő létrehozása – alapszintű információ](media/create-private-cloud-basic-info.png)

9. Kattintson a **Tovább: speciális beállítások** elemre.
10. Adja meg a vSphere/vSAN alhálózatok CIDR tartományát. Győződjön meg arról, hogy a CIDR-tartomány nem fedi átfedésben a helyszíni vagy más Azure-alhálózatokkal (virtuális hálózatokkal) vagy az átjáró-alhálózattal.

    **CIDR-tartomány beállításai:** /24,/23,/22, vagy/21. Az a/24 CIDR-tartomány legfeljebb kilenc csomópontot támogat, a/23 CIDR-tartomány akár 41 csomópontot is támogat, a/22 és/21 CIDR tartomány pedig legfeljebb 64 csomópontot támogat (a csomópontok maximális száma a privát felhőben).

    > [!IMPORTANT]
    > A vSphere/vSAN CIDR-tartomány IP-címei a saját felhőalapú infrastruktúra számára vannak fenntartva.  Ne használja az IP-címet ebben a tartományban bármely virtuális gépen.

11. Kattintson **a Tovább gombra: Áttekintés és létrehozás**.
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson az **előző** gombra.
13. Kattintson a **Létrehozás** lehetőségre.

Elindul a felhőalapú kiépítési folyamat. A privát felhő üzembe helyezése akár két óráig is eltarthat.

A meglévő privát felhő kibővítésével kapcsolatos útmutatásért lásd: [privát felhő kibontása](expand-private-cloud.md).
