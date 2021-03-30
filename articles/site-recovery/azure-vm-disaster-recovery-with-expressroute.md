---
title: Azure ExpressRoute Azure-beli virtuális gép vész-helyreállításának integrálása Azure Site Recovery
description: Útmutatás az Azure-beli virtuális gépek vész-helyreállításának beállításához Azure Site Recovery és az Azure ExpressRoute használatával
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88654378"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>ExpressRoute integrálása az Azure-beli virtuális gépek vész-helyreállításával


Ez a cikk azt ismerteti, hogyan integrálható az Azure-beli ExpressRoute az Azure-beli virtuális gépek másodlagos Azure-régióba való bevezetésének [Azure site Recoveryával](site-recovery-overview.md).

Site Recovery az Azure-beli virtuális gépek Azure-ba történő replikálásával lehetővé teszi az Azure-beli virtuális gépek vész-helyreállítását.

- Ha az Azure-beli virtuális gépek az [Azure Managed](../virtual-machines/managed-disks-overview.md)Disks-t használják, a rendszer a virtuális gép-és a másodlagos régió replikált felügyelt lemezére replikál
- Ha az Azure-beli virtuális gépek nem felügyelt lemezeket használnak, a rendszer replikálja a virtuálisgép-fájlokat egy Azure Storage-fiókba.
- A replikációs végpontok nyilvánosak, de az Azure-beli virtuális gépek replikációs forgalma nem az interneten keresztül történik.

A ExpressRoute lehetővé teszi a helyszíni hálózatok kibővítését a Microsoft Azure felhőbe privát kapcsolaton keresztül, amelyet egy kapcsolati szolgáltató biztosít. Ha a ExpressRoute konfigurálva van, az a következőképpen integrálódik a Site Recovery:

- Az Azure- **régiók közötti replikáció során**: az Azure-beli virtuális gép vész-helyreállítási replikációs forgalma csak az Azure-ban érhető el, és a ExpressRoute nincs szükség vagy replikációra használni. Ha azonban egy helyszíni helyről csatlakozik az Azure-beli virtuális gépekhez az elsődleges Azure-helyen, számos problémát figyelembe kell vennie az Azure-beli virtuális gépek vész-helyreállításának beállításakor.
- **Feladatátvétel az Azure-régiók között**: leállások esetén az Azure-beli virtuális gépeket az elsődlegesről a másodlagos Azure-régióra hajtja végre. A másodlagos régióba való feladatátvételt követően számos lépést kell elvégeznie ahhoz, hogy a másodlagos régióban lévő Azure-beli virtuális gépeket a ExpressRoute használatával lehessen elérni.


## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené, győződjön meg róla, hogy megértette a következő fogalmakat:

- ExpressRoute- [áramkörök](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute- [útválasztási tartományok](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [helyei](../expressroute/expressroute-locations.md).
- Azure-beli virtuális gép [replikációs architektúrája](azure-to-azure-architecture.md)
- Az Azure-beli virtuális gépek [replikálásának beállítása](azure-to-azure-tutorial-enable-replication.md) .
- Azure-beli virtuális gépek [feladatátvétele](azure-to-azure-tutorial-failover-failback.md) .


## <a name="general-recommendations"></a>Általános javaslatok

Az ajánlott eljáráshoz és a hatékony helyreállítási idő célkitűzésének (RTOs) a vész-helyreállításhoz való biztosításához az alábbiakat javasoljuk, amikor beállítja Site Recoveryt a ExpressRoute-nal való integráláshoz:

- Hálózati összetevők kiépítése a feladatátvétel előtt egy másodlagos régióba:
    - Ha engedélyezi az Azure-beli virtuális gépek replikálását, Site Recovery a forrás hálózati beállítások alapján automatikusan üzembe helyezhet hálózati erőforrásokat, például hálózatokat, alhálózatokat és átjárókat a cél Azure-régióban.
    - Site Recovery nem tud automatikusan beállítani hálózati erőforrásokat, például VNet átjárókat.
    - Javasoljuk, hogy a feladatátvétel előtt ezeket a további hálózati erőforrásokat is kiépítse. Kis állásidő van társítva ehhez a központi telepítéshez, és ez hatással lehet a teljes helyreállítási időre, ha az üzembe helyezés megtervezése során nem vette figyelembe.
- A vész-helyreállítási gyakorlatok rendszeres futtatása:
    - A próba adatveszteség és állásidő nélkül ellenőrzi a replikációs stratégiáját, és nincs hatással az éles környezetre. Segít elkerülni a RTO negatívan befolyásoló utolsó perces konfigurációs problémákat.
    - Ha feladatátvételi tesztet futtat a részletezéshez, javasoljuk, hogy használjon külön Azure virtuálisgép-hálózatot a replikáció engedélyezésekor beállított alapértelmezett hálózat helyett.
- Ha egyetlen ExpressRoute áramkörrel rendelkezik, használjon eltérő IP-címeket.
    - Javasoljuk, hogy használjon másik IP-címtartományt a célként megadott virtuális hálózathoz. Ez elkerüli a problémákat a kapcsolatok létrehozásakor a regionális kimaradások során.
    - Ha nem használhat külön címtartományt, ne felejtse el futtatni a vész-helyreállítási teszt feladatátvételét egy különálló, eltérő IP-címmel rendelkező tesztelési hálózaton. Egymást átfedő IP-címtartományt tartalmazó két virtuális hálózatok ugyanahhoz a ExpressRoute-áramkörhöz nem csatlakoztatható.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Azure-beli virtuális gépek replikálása a ExpressRoute használatakor


Ha egy elsődleges helyen szeretné beállítani az Azure-beli virtuális gépek replikálását, és a helyszíni helyről a ExpressRoute-en keresztül csatlakozik ezekhez a virtuális gépekhez, a következőkre lesz szüksége:

1. Engedélyezze az egyes Azure-beli virtuális gépek [replikációját](azure-to-azure-tutorial-enable-replication.md) .
2. Ha szeretné, Site Recovery beállítani a hálózatkezelést:
    - Amikor konfigurálja és engedélyezi a replikálást, Site Recovery beállítja a hálózatokat, alhálózatokat és átjáró-alhálózatokat a cél Azure-régióban, hogy azok megfeleljenek a forrás régiójában lévőknek. A Site Recovery a forrás és a cél virtuális hálózatok közötti leképezéseket is leképezi.
    - Ha nem szeretné, hogy ezt a Site Recovery automatikusan elvégezze, a replikálás engedélyezése előtt hozza létre a cél oldali hálózati erőforrásokat.
3. Egyéb hálózati elemek létrehozása:
    - Site Recovery nem hoz létre útválasztási táblákat, VNet-átjárókat, VNet, VNet vagy más hálózati erőforrásokat és kapcsolatokat a másodlagos régióban.
    - Ezeket a további hálózatkezelési elemeket a másodlagos régióban kell létrehoznia, mielőtt az elsődleges régióból feladatátvételt futtat.
    - A [helyreállítási tervek](site-recovery-create-recovery-plans.md) és automatizálási parancsfájlok segítségével beállíthatja és összekapcsolhatók ezek a hálózati erőforrások.
1. Ha egy hálózati virtuális berendezés (NVA) van üzembe helyezve a hálózati forgalom áramlásának szabályozására, vegye figyelembe a következőket:
    - Az Azure-beli virtuális gép replikálásának alapértelmezett rendszerútvonala a 0.0.0.0/0.
    - A NVA központi telepítései általában olyan alapértelmezett útvonalat (0.0.0.0/0) is meghatároznak, amely a NVA keresztül a kimenő internetes forgalmat kényszeríti. Az alapértelmezett útvonal akkor használatos, ha más útvonal-konfiguráció nem található.
    - Ebben az esetben előfordulhat, hogy a NVA túlterhelt, ha az összes replikációs forgalom a NVA halad át.
    - Ugyanez a korlátozás akkor is érvényes, ha az alapértelmezett útvonalakat használja az összes Azure VM-forgalom helyszíni környezetbe történő átirányításához.
    - Ebben az esetben javasoljuk, hogy [hozzon létre egy hálózati szolgáltatási végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózaton a Microsoft. Storage szolgáltatás számára, hogy a replikálási forgalom ne maradjon az Azure határán.

## <a name="replication-example"></a>Replikációs példa

A vállalati telepítések jellemzően több Azure-virtuális hálózatok oszlanak meg, és központi csatlakozási központtal rendelkeznek az internettel és a helyszíni helyekkel való külső kapcsolatokhoz. A hub és a küllős topológia jellemzően a ExpressRoute együtt használható.

![Helyszíni – Azure ExpressRoute feladatátvétel előtt](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Régió**. Az alkalmazások üzembe helyezése az Azure Kelet-Ázsia régióban történik.
- **Küllős virtuális hálózatok**. Az alkalmazások két küllős virtuális hálózatok vannak telepítve:
    - **Forrás vNet1**: 10.1.0.0/24.
    - **Forrás vNet2**: 10.2.0.0/24.
    - Minden küllős virtuális hálózat a **hub vNet** csatlakozik.
- **Hub-vNet**. Van egy hub vNet **forrás hub vNet**: 10.10.10.0/24.
  - Ez a hub-vNet Forgalomirányítóként működik.
  - Az alhálózatok közötti összes kommunikáció ezen a központban halad át.
    - **Hub vNet alhálózatai**. A hub vNet két alhálózattal rendelkezik:
    - **NVA alhálózat**: 10.10.10.0/25. Ez az alhálózat tartalmaz egy NVA (10.10.10.10).
    - **Átjáró-alhálózat**: 10.10.10.128/25. Ez az alhálózat egy olyan ExpressRoute-kapcsolathoz csatlakoztatott ExpressRoute-átjárót tartalmaz, amely a helyszíni helyre irányítja a privát társ-útválasztási tartományon keresztül.
- A helyszíni adatközpont egy ExpressRoute áramköri kapcsolattal rendelkezik Hongkongban.
- Az összes útválasztást az Azure Route Tables (UDR) vezérli.
- A virtuális hálózatok és a helyszíni adatközpont közötti összes kimenő forgalom a NVA keresztül irányítható.

### <a name="hub-and-spoke-peering-settings"></a>Központi és küllős peering-beállítások

#### <a name="spoke-to-hub"></a>A küllőt a központtal

**Irány** | **Beállítás** | **Állapot**
--- | --- | ---
A küllőt a központtal | Virtuális hálózati címek engedélyezése | Engedélyezve
A küllőt a központtal | Továbbított forgalom engedélyezése | Engedélyezve
A küllőt a központtal | Átjáró átvitelének engedélyezése | Disabled (Letiltva)
A küllőt a központtal | Átjárók eltávolítása | Engedélyezve

 ![Küllős a hub-társ konfiguráció](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>A központot a küllővel

**Irány** | **Beállítás** | **Állapot**
--- | --- | ---
A központot a küllővel | Virtuális hálózati címek engedélyezése | Engedélyezve
A központot a küllővel | Továbbított forgalom engedélyezése | Engedélyezve
A központot a küllővel | Átjáró átvitelének engedélyezése | Engedélyezve
A központot a küllővel | Átjárók eltávolítása | Disabled (Letiltva)

 ![Sugaras konfiguráció](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Példa lépései

A példánkban a következőnek kell történnie, amikor engedélyezi az Azure-beli virtuális gépek replikálását a forrásoldali hálózaton:

1. [Engedélyezi a replikációt](azure-to-azure-tutorial-enable-replication.md) egy virtuális gépen.
2. A Site Recovery replika-virtuális hálózatok, alhálózatokat és átjáró-alhálózatokat hoz létre a célként megadott régióban.
3. A Site Recovery leképezéseket hoz létre a forrásoldali hálózatok és az általa létrehozott replika-hálózatok között.
4. A virtuális hálózati átjárók, a virtuális hálózati átjárók és a virtuális hálózati társítások, illetve bármely más hálózati erőforrás vagy kapcsolat létrehozása manuálisan végezhető el.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Azure-beli virtuális gépek feladatátvétele a ExpressRoute használatakor

Ha az Azure-beli virtuális gépeket a Site Recovery használatával nem a cél Azure-régióra irányítja át, akkor a ExpressRoute [privát](../expressroute/expressroute-circuit-peerings.md#privatepeering)társításával férhet hozzájuk.

- Csatlakoztatnia kell a ExpressRoute a cél vNet egy új kapcsolattal. A meglévő ExpressRoute-kapcsolatok nem kerülnek automatikusan átvitelre.
- A ExpressRoute-kapcsolatok a célként megadott vNet való beállítása a ExpressRoute-topológiától függ.


### <a name="access-with-two-circuits"></a>Hozzáférés két áramkörrel

#### <a name="two-circuits-with-two-peering-locations"></a>Két áramkör két egymás közötti hellyel

Ez a konfiguráció segít megvédeni a ExpressRoute-áramköröket a regionális katasztrófák ellen. Ha az elsődleges egyenrangú hely leáll, a kapcsolatok a másik helyről is folytatódnak.

- Az éles környezethez kapcsolódó áramkör általában az elsődleges. A másodlagos áramkör általában alacsonyabb sávszélességgel rendelkezik, ami vészhelyzet esetén növelhető.
- A feladatátvételt követően kapcsolat hozható létre a másodlagos ExpressRoute-áramkörből a célként megadott vNet. Azt is megteheti, hogy vészhelyzet esetén az általános helyreállítási idő csökkentése érdekében beállította a kapcsolatokat, és készen áll a használatra.
- Az elsődleges és a cél virtuális hálózatok való egyidejű kapcsolatok esetén győződjön meg arról, hogy a helyszíni útválasztás csak a másodlagos áramkört és a kapcsolatot használja a feladatátvétel után.
- A forrás-és a célként megadott virtuális hálózatok új IP-címeket fogadhatnak, vagy megtarthatják ugyanezeket a feladatátvételt követően is. A másodlagos kapcsolatok mindkét esetben a feladatátvétel előtt is létrehozhatók.


#### <a name="two-circuits-with-single-peering-location"></a>Két áramkör egyetlen egyenrangú hellyel

Ez a konfiguráció segít az elsődleges ExpressRoute áramkör meghibásodása elleni védelemben, de ha az egyetlen ExpressRoute-társítási hely leáll, az mindkét áramkörre hatással van.

- A helyszíni adatközpontból a forrás-és a vNEt az elsődleges áramkörrel párhuzamosan kapcsolódhat, a cél vNet pedig a másodlagos áramkörrel.
- Az elsődleges és a célként való egyidejű kapcsolatok esetén győződjön meg arról, hogy a helyszíni útválasztás csak a másodlagos áramkört és a kapcsolatot használja a feladatátvétel után.
-   Nem lehet mindkét áramkört ugyanahhoz a vNet csatlakozni, ha az adatáramkörök ugyanazon a helyen jönnek létre.

### <a name="access-with-a-single-circuit"></a>Hozzáférés egyetlen áramkörrel

Ebben a konfigurációban csak egy Expressroute áramkör található. Bár az áramkör redundáns kapcsolattal rendelkezik, ha az egyik leáll, egyetlen útvonali áramkör nem biztosít rugalmasságot, ha a társítási régió leáll. Vegye figyelembe:

- Az Azure-beli virtuális gépeket bármely Azure-régióba replikálhatja [ugyanazon a földrajzi helyen](azure-to-azure-support-matrix.md#region-support). Ha a cél Azure-régió nem ugyanazon a helyen található, mint a forrás, akkor engedélyeznie kell a ExpressRoute Premiumot, ha egyetlen ExpressRoute áramkört használ. Ismerje meg a [ExpressRoute helyét](../expressroute/expressroute-locations.md) és a [ExpressRoute díjszabását](https://azure.microsoft.com/pricing/details/expressroute/).
- A forrás-és a célként megadott virtuális hálózatok nem csatlakoztatható egyszerre az áramkörhöz, ha ugyanazt az IP-címtartományt használja a célként megadott régióban. Ebben a forgatókönyvben:    
    -  Válassza le a forrás oldali kapcsolatot, majd hozza létre a cél oldali kapcsolatot. Ez a kapcsolati változás a Site Recovery helyreállítási terv részeként is megadható. Vegye figyelembe:
        - Regionális meghibásodás esetén, ha az elsődleges régió nem érhető el, a leválasztási művelet sikertelen lehet. Ez hatással lehet a megcélzott régióhoz való kapcsolódásra.
        - Ha a kapcsolatot a célként megadott régióban hozta létre, és az elsődleges régió később helyreállítja a csomagokat, akkor előfordulhat, hogy a csomagok akkor is csökkennek, ha két egyidejű kapcsolat megpróbál csatlakozni ugyanahhoz a címtartomány-kapcsolathoz.
        - Ennek megelőzése érdekében azonnal szakítsa meg az elsődleges kapcsolódást.
        - Miután a virtuális gép feladat-visszavételét az elsődleges régióba helyezi, az elsődleges kapcsolat újból létrehozható a másodlagos kapcsolat bontása után.
-   Ha a cél vNet eltérő címtartományt használ, egyszerre csatlakozhat a forrás-és a cél virtuális hálózatok ugyanabból a ExpressRoute-áramkörből.


## <a name="failover-example"></a>Példa feladatátvételre

A példánkban a következő topológiát használjuk:

- Két különböző ExpressRoute-áramkör két különböző egymás melletti helyen.
- A feladatátvételt követően megőrizheti az Azure-beli virtuális gépek magánhálózati IP-címeit.
- A cél helyreállítási régió az Azure Délkelet-Ázsia.
- A másodlagos ExpressRoute áramköri kapcsolat a Szingapúr egyik partneri peremén keresztül jön.

[Tekintse át ezt a cikket](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)egy olyan egyszerű topológia esetében, amely egyetlen ExpressRoute áramkört használ a feladatátvételt követően ugyanazzal az IP-címmel.

### <a name="example-steps"></a>Példa lépései
Ebben a példában a helyreállítás automatizálásához a következőket kell tennie:

1. A replikáció beállításához kövesse a lépéseket.
2. Hajtsa végre [Az Azure-beli virtuális gépek](azure-to-azure-tutorial-failover-failback.md)feladatátvételét, és végezze el ezeket a további lépéseket.

    a. Hozza létre az Azure ExpressRoute-átjárót a cél régió hub-VNet. Ehhez csatlakoztatnia kell a cél hub-vNet a ExpressRoute-áramkörhöz.

    b. Hozza létre a kapcsolatot a cél hub vNet a cél ExpressRoute áramkörhöz.

    c. Állítsa be a VNet-társításokat a megcélzott régió központja és a küllős virtuális hálózatok között. A célként megadott régió egyenrangú tulajdonságai ugyanazok, mint a forrásoldali régióban.

    d. Állítsa be a UDR a hub VNet, és a két küllős virtuális hálózatok.

    - A cél oldali UDR tulajdonságai ugyanazok, mint a forrásoldali oldalon, ha ugyanazokat az IP-címeket használják.
    - Ha a cél IP-címei eltérőek, a UDR ennek megfelelően módosítania kell.


A fenti lépésekkel egy [helyreállítási terv](site-recovery-create-recovery-plans.md)részeként lehet parancsfájlt készíteni. Az alkalmazás és a helyreállítási idő követelményeitől függően a fenti lépéseket a feladatátvétel elindítása előtt is el lehet végezni.

#### <a name="after-recovery"></a>Helyreállítás után

A virtuális gépek helyreállítását és a kapcsolatok befejezését követően a helyreállítási környezet a következő.

![Helyszíni – Azure ExpressRoute feladatátvétel után](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Következő lépések

További információ a [helyreállítási tervek](site-recovery-create-recovery-plans.md) használatáról az alkalmazások feladatátvételének automatizálásához.
