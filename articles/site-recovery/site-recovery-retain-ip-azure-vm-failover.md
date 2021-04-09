---
title: IP-címek megtartása az Azure-beli virtuális gép feladatátvétele után Azure Site Recovery
description: Ismerteti, hogyan lehet megőrizni az IP-címeket, ha az Azure-beli virtuális gépek feladatátvételét egy másodlagos régióba Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023550"
---
# <a name="retain-ip-addresses-during-failover"></a>IP-cím megőrzése a feladatátvétel során

A [Azure site Recovery](site-recovery-overview.md) lehetővé teszi az Azure-beli virtuális gépek vész-helyreállítását azáltal, hogy a virtuális gépeket egy másik Azure-régióba replikálja, a meghibásodás esetén feladatátvételt hajt végre, és visszaadja az elsődleges régiónak, amikor a dolgok normális kerékvágásba kerülnek.

A feladatátvétel során érdemes megtartani az IP-címzést a céltartományban megegyező módon:

- Alapértelmezés szerint az Azure-beli virtuális gépek vész-helyreállításának engedélyezésekor Site Recovery a forrás erőforrás-beállítások alapján hozza létre a cél erőforrásokat. A statikus IP-címmel konfigurált Azure-beli virtuális gépek esetében a Site Recovery a célként megadott virtuális gép esetében ugyanazt az IP-címet próbálja kiépíteni, ha az nincs használatban. Ha részletesen meg kívánja tekinteni, hogyan kezeli Site Recovery a címzést, [tekintse át ezt a cikket](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Az egyszerű alkalmazások esetében az alapértelmezett konfiguráció elegendő. Összetettebb alkalmazások esetén előfordulhat, hogy további erőforrást kell kiépítenie, hogy a kapcsolat a várt módon működjön a feladatátvétel után.


Ez a cikk néhány példát mutat be az IP-címek összetettebb példákban való megőrzésére. Ilyenek például a következők:

- Feladatátvétel az Azure-ban futó összes erőforrással rendelkező vállalatnál
- Feladatátvétel a hibrid üzembe helyezést és a helyszíni és az Azure-ban egyaránt futó erőforrásokat használó vállalatok számára

## <a name="resources-in-azure-full-failover"></a>Erőforrások az Azure-ban: teljes feladatátvétel

Az A vállalat rendelkezik az Azure-ban futó összes alkalmazásával.

### <a name="before-failover"></a>Feladatátvétel előtt

Itt látható az architektúra a feladatátvétel előtt.

- Az A vállalat azonos hálózatokkal és alhálózatokkal rendelkezik a forrás-és a cél Azure-régiókban.
- A helyreállítási időre vonatkozó célkitűzés (RTO) csökkentése érdekében a vállalat replika csomópontokat használ a SQL Server always on, a tartományvezérlőkön stb. Ezek a replika-csomópontok a céltartományban eltérő VNet találhatók, így képesek a forrás-és a célcsoportok közötti VPN-helyek közötti kapcsolat létesítésére. Ez nem lehetséges, ha ugyanaz az IP-címtartomány van használatban a forrásban és a célhelyen.  
- A feladatátvétel előtt a hálózati architektúra a következő:
    - Az elsődleges régió az Azure Kelet-Ázsia
        - Kelet-Ázsia rendelkezik egy VNet (**forrás VNet**), és a 10.1.0.0/16 címtartomány.
        - A Kelet-Ázsia a VNet három alhálózatán felosztja a munkaterheléseket:
            - **1. alhálózat**: 10.1.1.0/24
            - **2. alhálózat**: 10.1.2.0/24
            - **3. alhálózat**: 10.1.3.0/24
    - Másodlagos (cél) régió az Azure Délkelet-Ázsiában
        - A Délkelet-Ázsiában a **forrás VNet** azonos helyreállítási VNet (**Recovery VNet**) rendelkezik.
        - A Délkelet-ázsiai további VNet (**Azure VNet**) rendelkezik, és a 10.2.0.0/16 címtartomány is rendelkezésre áll.
        - Az **Azure VNet** tartalmaz egy alhálózatot (**4. alhálózat**), amely 10.2.4.0/24 címtartományt tartalmaz.
        - A (z) SQL Server always on, a tartományvezérlőhöz és a (z) **4. alhálózatban** található replika-csomópontok.
    - A forrás-és az **Azure-VNET** VPN-helyek közötti kapcsolattal **VNet** össze.
    - A **helyreállítási VNet** nem kapcsolódik más virtuális hálózathoz.
    - Az **A vállalat** hozzárendeli/ellenőrzi a replikált elemek cél IP-címeit. A cél IP-cím megegyezik az egyes virtuális gépek forrás IP-címével.

![Erőforrások az Azure-ban a teljes feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után

Ha a forrás regionális leállás következik be, az A vállalat az összes erőforrását átveheti a célként megadott régióba.

- Ha a cél IP-címek már a feladatátvétel előtt vannak érvényben, a vállalat a feladatátvételt, és automatikusan kapcsolatokat létesíthet a **helyreállítási VNet** és az **Azure VNet** közötti feladatátvételt követően. Ezt a következő ábra szemlélteti.
- Az alkalmazás követelményeitől függően a két virtuális hálózatok (a **helyreállítási VNet** és az **Azure VNet**) közötti kapcsolatok meghozhatók a célként megadott régióban, a (közbenső lépés) vagy a feladatátvétel után.
  - A vállalat [helyreállítási terveket](site-recovery-create-recovery-plans.md) használhat a kapcsolatok létrehozásához.
  - Csatlakozhatnak a virtuális hálózatok a VNet-társítás vagy a helyek közötti VPN használatával.
      - A VNet-társítás nem használ VPN-átjárót, és eltérő korlátozásokkal rendelkezik.
      - A VNet-társítás [díjszabása](https://azure.microsoft.com/pricing/details/virtual-network) eltérően van kiszámítva, mint a VNet – VNet VPN Gateway [díjszabása](https://azure.microsoft.com/pricing/details/vpn-gateway). A feladatátvételek esetében általában azt javasoljuk, hogy ugyanazt a csatlakozási módszert használja, mint a forrásoldali hálózatokat, beleértve a kapcsolat típusát, hogy csökkentse a kiszámíthatatlan hálózati incidenseket.

    ![Erőforrások az Azure-ban – teljes feladatátvétel](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Erőforrások az Azure-ban: elkülönített alkalmazások feladatátvétele

Előfordulhat, hogy az alkalmazás szintjén kell átadnia a feladatátvételt. Például egy dedikált alhálózaton található adott alkalmazás vagy alkalmazás-rétegek feladatátvételéhez.

- Ebben az esetben, bár megtarthatja az IP-címzést, általában nem tanácsos, mert növeli a kapcsolati következetlenségek esélyét. Az alhálózati kapcsolat más alhálózatokhoz is elvész ugyanazon az Azure-VNet belül.
- Az alhálózati szintű alkalmazások feladatátvételének jobb módja, ha a feladatátvételhez eltérő cél IP-címeket használ (ha más alhálózatokhoz kell kapcsolódnia a forrás-VNet), illetve az egyes alkalmazások elkülönítését a forrás régiójában lévő saját dedikált VNet. Az utóbbi megközelítéssel kapcsolatot létesíthet a különböző hálózati hálózatok között, és ugyanazokat a viselkedést Emulálhatja, amikor feladatátvételt hajt végre a célként megadott régióban.  

Ebben a példában a vállalat egy dedikált virtuális hálózatok helyezi el az alkalmazásokat a forrás régióban, és kapcsolatot létesít a virtuális hálózatok között. Ezzel a kialakítással elszigetelt alkalmazások feladatátvételét végezheti el, és megtarthatja a forrás magánhálózati IP-címeit a célként megadott hálózaton.

### <a name="before-failover"></a>Feladatátvétel előtt

A feladatátvétel előtt az architektúra a következő:

- Az alkalmazás virtuális gépei az elsődleges Azure Kelet-Ázsia régióban találhatók:
    - **App1** A virtuális gépek a VNet **forrás 1. VNet** találhatók: 10.1.0.0/16.
    - **App2** A virtuális gépek a VNet **forrás VNet 2**: 10.2.0.0/16 mappában találhatók.
    - A **forrás 1. VNet** két alhálózattal rendelkezik.
    - A **2. forrás VNet** két alhálózattal rendelkezik.
- Másodlagos (cél) régiója az Azure Délkelet-ázsiai – Délkelet-Ázsia helyreállítási virtuális hálózatok (**1. helyreállítási VNet 1** és **2. helyreállítási VNet**), amelyek azonosak a **Source VNet 1** és a **Source VNet 2** használatával.
        - A **helyreállítási VNet 1** és a **Recovery VNet 2** rendszernek két alhálózata van, amelyek megfelelnek az **1. forrás VNet** és a 2 – Délkelet-ázsiai forrás **VNet** . további VNet (**Azure VNet**) rendelkezik, és a címtartomány 10.3.0.0/16.
        - Az **Azure VNet** tartalmaz egy alhálózatot (**4. alhálózat**), amely 10.3.4.0/24 címtartományt tartalmaz.
        – A replika-csomópontok a SQL Server always on, a tartományvezérlőn stb. a **4. alhálózatban** találhatók.
- A helyek közötti VPN-kapcsolatok száma: 
    - **Forrás VNet 1** és **Azure VNet**
    - **Forrás VNet 2** és **Azure VNet**
    - A **forrás VNet 1** és a **2. forrás** a VPN-helyek közötti kapcsolattal rendelkező VNet csatlakozik.
- A **helyreállítási VNet 1** és a **helyreállítási VNet 2** nem kapcsolódik más virtuális hálózatok.
- Az **A vállalat** konfigurálja a VPN-átjárókat a **helyreállítási VNet 1** és a **Recovery VNet 2** használatával a RTO csökkentése érdekében.  
- A **helyreállítási VNet1** és a **helyreállítási VNet2** nem kapcsolódnak más virtuális hálózatokhoz.
- A helyreállítási időre vonatkozó célkitűzés (RTO) csökkentése érdekében a VPN-átjárók a feladatátvétel előtt a **helyreállítási VNet1** és a **helyreállítási VNet2** vannak konfigurálva.

    ![Azure-erőforrások az alkalmazás feladatátvétele előtt](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után

Egy olyan leállás vagy probléma esetén, amely egyetlen alkalmazást érint (* * forrás VNet 2 a példánkban), az A vállalat a következőképpen állíthatja helyre az érintett alkalmazást:


- Válassza le a VPN-kapcsolatokat a forrás-és **VNet2**, valamint a **forrás** -és az **Azure-VNet** között a **VNet1** között.
- VPN-kapcsolatok létrehozása **a forrás-VNet1** és a **helyreállítási VNet2**, valamint a **helyreállítási VNet2** és az **Azure VNet** között.
- Virtuális gépek feladatátvétele a **forrás VNet2** a **helyreállítási VNet2**.

![Erőforrások az Azure-alkalmazás feladatátvételében](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Ezt a példát kiterjesztheti több alkalmazás és hálózati kapcsolat hozzáadására is. Javasoljuk, hogy a lehető legpontosabban kövesse a hasonló kapcsolati modellt, ha a forrásról a célra történő feladatátvételt hajt végre.
- A VPN-átjárók nyilvános IP-címeket és átjáró-ugrásokat használnak a kapcsolatok létrehozásához. Ha nem szeretne nyilvános IP-címeket használni, vagy ha el szeretné kerülni a további ugrásokat, a [támogatott Azure-régiók](../virtual-network/virtual-network-manage-peering.md#cross-region)között használhatja az [Azure VNet](../virtual-network/virtual-network-peering-overview.md) -társítást az egyenrangú virtuális hálózatokhoz.

## <a name="hybrid-resources-full-failover"></a>Hibrid erőforrások: teljes feladatátvétel

Ebben az esetben a **B vállalat** hibrid üzleti tevékenységet futtat, amely az Azure-on futó alkalmazás-infrastruktúra és a helyszínen futó további alkalmazások részét képezi. 

### <a name="before-failover"></a>Feladatátvétel előtt

A hálózati architektúra a feladatátvétel előtt néz ki.

- Az alkalmazás virtuális gépei az Azure Kelet-Ázsia-ban futnak.
- Kelet-Ázsia rendelkezik egy VNet (**forrás VNet**), és a 10.1.0.0/16 címtartomány.
  - A Kelet-Ázsia a **forrás VNet** három alhálózatán felosztja a munkaterheléseket:
    - **1. alhálózat**: 10.1.1.0/24
    - **2. alhálózat**: 10.1.2.0/24
    - **3. alhálózat**: 10.1.3.0/24, Azure-beli virtuális hálózat kihasználása a 10.1.0.0/16 címtartomány használatával. A virtuális hálózat neve **forrás VNet**
      - A másodlagos (cél) régió az Azure Délkelet-Ázsia:
  - A Délkelet-Ázsiában a **forrás VNet** azonos helyreállítási VNet (**Recovery VNet**) rendelkezik.
- A Kelet-Ázsiaban lévő virtuális gépek egy helyszíni adatközponthoz kapcsolódnak, amely az Azure ExpressRoute vagy a helyek közötti VPN-kapcsolattal rendelkezik.
- A RTO csökkentése érdekében a B vállalat a feladatátvétel előtt kiépíti az Azure Délkelet-ázsiai helyreállítási VNet tartozó átjárókat.
- A B vállalat hozzárendeli/ellenőrzi a replikált virtuális gépek céljának IP-címeit. A cél IP-cím megegyezik az egyes virtuális gépek forrás IP-címével.


![Helyszíni – Azure kapcsolat feladatátvétel előtt](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Feladatátvétel után


Ha a forrás regionális leállás következik be, a B vállalat az összes erőforrását átveheti a célként megadott régióba.

- A feladatátvételt megelőzően már meglévő cél IP-címekkel rendelkező B vállalat a feladatátvételt és a **helyreállítást a helyreállítási VNet** és az **Azure VNet** közötti feladatátvételt követően automatikusan képes létrehozni.
- Az alkalmazás követelményeitől függően a két virtuális hálózatok (a **helyreállítási VNet** és az **Azure VNet**) közötti kapcsolatok meghozhatók a célként megadott régióban, a (közbenső lépés) vagy a feladatátvétel után. A vállalat [helyreállítási terveket](site-recovery-create-recovery-plans.md) használhat a kapcsolatok létrehozásához.
- Az Azure Kelet-Ázsia és a helyszíni adatközpont közötti eredeti kapcsolatot le kell választani az Azure Délkelet-Ázsia és a helyszíni adatközpont közötti kapcsolat létrehozása előtt.
- A helyszíni útválasztás újra konfigurálva van, hogy a rendszer a célként megadott régióra és átjáróra mutasson a feladatátvétel után.

![Helyszíni – Azure kapcsolat feladatátvétel után](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hibrid erőforrások: elkülönített alkalmazások feladatátvétele

A B vállalat nem tudja felvenni az elkülönített alkalmazásokat az alhálózat szintjén. Ennek az az oka, hogy a forrás-és a helyreállítási virtuális hálózatok azonos a címtartomány, és az eredeti forrás és a helyszíni kapcsolatok aktívak.

 - Az alkalmazás rugalmassága érdekében a B vállalatnak minden alkalmazást saját dedikált Azure-VNet kell elhelyeznie.
 - Az egyes alkalmazások külön VNet a B vállalat feladatátvételt hajthat végre az elkülönített alkalmazásokban, és átirányíthatja a forrásoldali kapcsolatokat a célként megadott régióba.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a [helyreállítási tervekről](site-recovery-create-recovery-plans.md).
