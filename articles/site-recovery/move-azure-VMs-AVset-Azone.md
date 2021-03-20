---
title: Virtuális gépek áthelyezése egy Azure-régióba a rendelkezésre állási zónák használatával Azure Site Recovery
description: Megtudhatja, hogyan helyezhet át virtuális gépeket egy másik régióban lévő rendelkezésre állási zónába Site Recovery
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 8224ae4a48bb4915492240c414b90edb86a4c258
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93393132"
---
# <a name="move-azure-vms-into-availability-zones"></a>Azure-beli virtuális gépek áthelyezése Availability Zonesba

Ez a cikk azt ismerteti, hogyan helyezhet át Azure-beli virtuális gépeket egy rendelkezésre állási zónába egy másik régióban. Ha egy másik zónába szeretne áttérni ugyanabban a régióban, [tekintse át ezt a cikket](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).


Availability Zones az Azure-ban segíti az alkalmazások és az adatok adatközpont-meghibásodások elleni védelmében. Az egyes rendelkezésre állási zónák egy vagy több, önálló áramellátással, hűtéssel, és hálózattal rendelkező adatközpontból állnak. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban. A régión belüli Availability Zones fizikai elkülönítése segít az adatközpont-hibák elleni védelemben az alkalmazások és az adatok védelme terén. A Availability Zones az Azure a virtuális gépek (VM-EK) rendelkezésre állására vonatkozó, 99,99%-os szolgáltatói szerződést (SLA) biztosít. A Availability Zones a kiválasztott régiókban támogatottak, ahogyan az a [Availability Zones támogató régiókban](../availability-zones/az-region.md)szerepel.

Olyan helyzetekben, ahol a virtuális gépek *egyetlen példányban* vannak üzembe helyezve egy adott régióban, és szeretné javítani a rendelkezésre állást azáltal, hogy ezeket a virtuális gépeket egy rendelkezésre állási zónába helyezi, ezt Azure site Recovery használatával teheti meg. Ez a művelet a következő kategóriákba rendezhető:

- Egypéldányos virtuális gépek áthelyezése egy adott régióban lévő Availability Zonesba
- Virtuális gépek áthelyezése egy rendelkezésre állási csoportba egy adott régióban lévő Availability Zonesba

> [!IMPORTANT]
> Ha az Azure-beli virtuális gépeket egy másik régióban lévő rendelkezésre állási zónába kívánja áthelyezni, azt javasoljuk, hogy használja az [Azure-erőforrást](../resource-mover/move-region-availability-zone.md). Az erőforrás-mozgató nyilvános előzetes verzióban érhető el, és a következőket biztosítja:
> - Egyetlen központ az erőforrások régiók közötti áthelyezéséhez.
> - Csökkentheti a mozgatási időt és a bonyolultságot. Minden, amire szüksége van, egyetlen helyen kell lennie.
> - Egyszerű és egységes felület a különböző típusú Azure-erőforrások áthelyezéséhez.
> - Egyszerű módszer az áthelyezni kívánt erőforrások függőségeinek azonosítására. Ezzel a művelettel a kapcsolódó erőforrásokat együtt helyezheti át, így minden a várt módon működik a megcélzott régióban, az áthelyezést követően.
> - A forrás régió erőforrásainak automatikus törlése, ha az áthelyezés után törölni szeretné őket.
> - Tesztelés: Kipróbálhatja a mozgatást, majd elvetheti, ha nem szeretne teljes áthelyezést végezni.



## <a name="check-prerequisites"></a>Előfeltételek ellenőrzése

- Győződjön [meg arról,](../availability-zones/az-region.md)hogy a célként megadott régió támogatja-e a Availability Zones. Győződjön meg arról, hogy a [forrás régió és a cél régió kombinációja támogatott](./azure-to-azure-support-matrix.md#region-support). Tájékozott döntést hozhat a megcélzott régióban.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Győződjön meg a fiók engedélyeiről. Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. A virtuális gép replikálásának engedélyezéséhez, és végül az Azure Site Recovery használatával másolhatja az Adatmásolást a célhelyre, a következőket kell tennie:

    1. Engedély virtuális gép létrehozásához az Azure-erőforrásokban. A *virtuálisgép-közreműködő* beépített szerepköre rendelkezik ezekkel az engedélyekkel, amelyek a következőket tartalmazzák:
        - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
        - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
        - Engedély a kiválasztott tárfiókra történő íráshoz

    2. Engedély Azure Site Recovery feladatok kezeléséhez. A *site Recovery közreműködő* szerepkör minden olyan engedéllyel rendelkezik, amely a Recovery Services-tárolóban lévő site Recovery műveletek kezeléséhez szükséges.

## <a name="prepare-the-source-vms"></a>A forrásként szolgáló virtuális gépek előkészítése

1. A virtuális gépeknek felügyelt lemezeket kell használniuk, ha Site Recovery használatával szeretné áthelyezni őket egy rendelkezésre állási zónába. A nem felügyelt lemezeket használó meglévő Windows-alapú virtuális gépeket átalakíthatja a felügyelt lemezek használatára. Kövesse a [Windows rendszerű virtuális gépek nem felügyelt lemezekről felügyelt lemezekre való konvertálása](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md)című témakör lépéseit. Győződjön meg arról, hogy a rendelkezésre állási csoport *felügyelt* van konfigurálva.
2. Győződjön meg arról, hogy az összes legújabb főtanúsítvány megtalálható az áthelyezni kívánt Azure-beli virtuális gépeken. Ha a legújabb főtanúsítványok nem jelennek meg, a célként megadott régióba történő Adatmásolást biztonsági korlátozások miatt nem lehet engedélyezni.

3. A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. A leválasztott környezetekben kövesse a Windows Update és a tanúsítvány frissítési folyamatait a szervezet számára.

4. Linux rendszerű virtuális gépek esetén kövesse a linuxos forgalmazója által biztosított útmutatást a legújabb megbízható főtanúsítványok és a tanúsítvány-visszavonási lista lekéréséhez a virtuális gépen.
5. Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozására.

6. Ellenőrizze [a virtuális gépek kimenő csatlakozási követelményeit](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

7. Azonosítsa a forrás hálózatkezelési elrendezést és az ellenőrzéshez jelenleg használt erőforrásokat, beleértve a terheléselosztó, a NSG és a nyilvános IP-címet.

## <a name="prepare-the-target-region"></a>A cél régió előkészítése

1. Győződjön meg arról, hogy az Azure-előfizetése lehetővé teszi a virtuális gépek létrehozását a vész-helyreállítási célra használt régióban. Ha szükséges, forduljon az ügyfélszolgálathoz, és engedélyezze a szükséges kvótát.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha Site Recovery használatával másol Adatmásolást a célhelyre, akkor a célként megadott virtuális gép méretének vagy a lehető legközelebb méretének kell lennie.

3. Hozzon létre egy célként megadott erőforrást a forrás hálózatkezelési elrendezésben azonosított összes összetevőhöz. Ez a művelet biztosítja, hogy a célként megadott régióba való kivágás után a virtuális gépek a forrásban lévő összes funkcióval és funkcióval rendelkezzenek.

    > [!NOTE]
    > Azure Site Recovery automatikusan felkeresi és létrehoz egy virtuális hálózati és Storage-fiókot, amikor engedélyezi a forrás virtuális gép replikálását. Ezeket az erőforrásokat előre is létrehozhatja és hozzárendelheti a virtuális géphez a replikálás engedélyezése lépés részeként. Más erőforrások esetében azonban, ahogy azt később is említettük, manuálisan kell létrehoznia a célként megadott régióban.

     A következő dokumentumok azt mutatják be, hogyan hozhatja létre az Ön számára releváns leggyakrabban használt hálózati erőforrásokat a forrásként szolgáló virtuális gép konfigurációja alapján.

    - [Hálózati biztonsági csoportok](../virtual-network/manage-network-security-group.md)
    - [Terheléselosztók](../load-balancer/index.yml)
    - [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    
   Bármely más hálózati összetevő esetében tekintse meg a hálózatkezelés [dokumentációját](../index.yml?pivot=products&panel=network).

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a célhelyen egy redundáns terheléselosztó van használatban. További információk: [standard Load Balancer és Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md).

4. Ha azt szeretné, hogy a célként megadott régióba való Kivágás előtt tesztelje a konfigurációt, manuálisan [hozzon létre nem éles hálózatot](../virtual-network/quick-create-portal.md) a célként megadott régióban. Ezt a megközelítést javasoljuk, mert az éles környezettel való minimális beavatkozást okoz.

## <a name="enable-replication"></a>A replikáció engedélyezése
A következő lépések végigvezetik a Azure Site Recovery az adatreplikálás engedélyezéséhez a célként megadott régióba, mielőtt végül áthelyezi őket a Availability Zonesba.

> [!NOTE]
> Ezek a lépések egyetlen virtuális gépre vonatkoznak. Ugyanezt több virtuális gépre is kiterjesztheti. Nyissa meg a Recovery Services tárolót, válassza a **+ replikálás** lehetőséget, majd válassza ki a megfelelő virtuális gépeket.

1. A Azure Portal válassza a **virtuális gépek** lehetőséget, majd válassza ki azt a virtuális gépet, amelyet át szeretne helyezni Availability Zonesba.
2. A **Műveletek** részen válassza a **Vészhelyreállítás** elemet.
3. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja. Győződjön meg arról, hogy a régió [támogatja](../availability-zones/az-region.md) a Availability Zones.
4. Válassza a **Tovább: speciális beállítások** lehetőséget.
5. Válassza ki a megfelelő értékeket a cél előfizetéshez, a cél virtuálisgép-erőforráscsoporthoz és a virtuális hálózathoz.
6. A **rendelkezésre állási** szakaszban válassza ki azt a rendelkezésre állási zónát, amelybe át szeretné helyezni a virtuális gépet. 
   > [!NOTE]
   > Ha nem látja a rendelkezésre állási csoport vagy a elérhetőség zóna beállítását, győződjön meg arról, hogy az [Előfeltételek](#prepare-the-source-vms) teljesülnek, és a forrásként szolgáló virtuális gépek [előkészítése](#prepare-the-source-vms) befejeződött.
  

7. Válassza a **replikáció engedélyezése** lehetőséget. Ez a művelet elindít egy feladatot a virtuális gép replikálásának engedélyezéséhez.

## <a name="check-settings"></a>Beállítások keresése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. A Virtuális gép menüben válassza a **Vészhelyreállítás** elemet.
2. Megtekintheti a replikációs állapotot, a létrehozott helyreállítási pontokat, valamint a forrás-és a célcsoportokat a térképen.


## <a name="test-the-configuration"></a>A konfiguráció tesztelése

1. A virtuális gép menüben válassza a vész-  **helyreállítás** lehetőséget.
2. Válassza a **feladatátvételi teszt** ikont.
3. A feladatátvételi **teszt** területen válassza ki a feladatátvételhez használandó helyreállítási pontot:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. Válassza ki azt a tesztelési cél Azure-beli virtuális hálózatot, amelyre át szeretné helyezni az Azure-beli virtuális gépeket a konfiguráció teszteléséhez. 

    > [!IMPORTANT]
    > Javasoljuk, hogy használjon külön Azure-beli virtuálisgép-hálózatot a tesztelési hibákhoz, nem pedig azt a célként megadott régióban lévő éles hálózatot, amelyben át szeretné helyezni a virtuális gépeket.

4. Az áthelyezés tesztelésének megkezdéséhez kattintson **az OK gombra**. A folyamat nyomon követéséhez válassza ki a virtuális gépet, és nyissa meg a tulajdonságait. Vagy kiválaszthatja a **feladatátvételi teszt** feladatot a tároló neve > **Beállítások**  >  **feladatok**  >  **site Recovery feladatok** lehetőségre.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelésének részeként létrehozott virtuális gépet, válassza a **feladatátvételi teszt** törlése elemet a replikált elemnél. A **jegyzetek** területen jegyezze fel és mentse a teszttel kapcsolatos megfigyeléseket.

## <a name="move-to-the-target-region-and-confirm"></a>Lépjen a célhelyre, és erősítse meg

1.  A virtuális gép menüben válassza a vész-  **helyreállítás** lehetőséget.
2. Válassza a **feladatátvétel** ikont.
3. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamat a **feladatok** lapon követhető. 
5. A feladatok befejezése után győződjön meg arról, hogy a virtuális gép a várt módon jelenik meg a cél Azure-régióban.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ezzel befejezi az áthelyezési folyamatot a célként megadott régióba. Várjon, amíg befejeződik a véglegesítés.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás elvetése a forrás régióban

Nyissa meg a virtuális gépet. Válassza a **replikáció letiltása** lehetőséget. Ez a művelet leállítja a virtuális gép adatok másolásának folyamatát.  

> [!IMPORTANT]
> Az előző lépésben elkerülheti, hogy az áthelyezés után Site Recovery replikáció után ne kelljen fizetni. A forrás replikálási beállításai automatikusan törlődnek. Vegye figyelembe, hogy a replikáció részeként telepített Site Recovery bővítmény nem törlődik, és manuálisan el kell távolítani.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megnövelte az Azure-beli virtuális gépek rendelkezésre állását a rendelkezésre állási csoport vagy a rendelkezésre állási zónába való áttéréssel. Most már beállíthatja az áthelyezett virtuális gép vész-helyreállítását.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)
