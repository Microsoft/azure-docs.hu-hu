---
title: Magas rendelkezésre állás, vész-helyreállítás, Üzletmenet-folytonosság
description: Ismerje meg a magas rendelkezésre állást, a vész-helyreállítási (HADR) és az üzletmenet-folytonossági lehetőségeket SQL Server Azure-beli virtuális gépeken, például az Always On rendelkezésre állási csoportok, a feladatátvevő fürt példánya, az adatbázis-tükrözés, a naplózási szállítás és a biztonsági mentés & az Azure Storage-ba történő
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: f4d870f458607ceb0f05812b5c0c066ce810448e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508316"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Üzletmenet-folytonosság és HADR az Azure-ban SQL Server Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az üzletmenet folytonossága vészhelyzet esetén, a helyreállítás megtervezése és az adataik nagyfokú rendelkezésre állásának biztosítása. Az Azure Virtual Machines SQL Server csökkentheti a magas rendelkezésre állású és vész-helyreállítási (HADR) adatbázis-megoldás költségeit. 

A legtöbb SQL Server HADR-megoldás támogatott a virtuális gépeken (VM), mind az Azure-ban, mind a hibrid megoldásokban. Egy csak Azure-megoldásban a teljes HADR rendszer fut az Azure-ban. A hibrid konfigurációban a megoldás egy része az Azure-ban fut, a másik pedig a helyszínen fut a szervezeten belül. Az Azure-környezet rugalmassága lehetővé teszi, hogy részben vagy egészben helyezze át az Azure-ba, hogy kielégítse a SQL Server adatbázis-rendszereinek költségvetési és HADR követelményeit.

Ez a cikk az Azure-beli virtuális gépeken SQL Server számára elérhető üzletmenet-folytonossági megoldásokat hasonlítja össze. 

## <a name="overview"></a>Áttekintés

Gondoskodjon arról, hogy az adatbázis-rendszer a szolgáltatói szerződés (SLA) által igényelt HADR képességekkel rendelkezik. Az a tény, hogy az Azure olyan magas rendelkezésre állási mechanizmusokat biztosít, mint például a Cloud Services szolgáltatás-gyógyulása és a virtuális gépek helyreállításának meghibásodása, nem garantálja, hogy teljesíti az SLA-t. Habár ezek a mechanizmusok segítenek a virtuális gép magas rendelkezésre állásának védelmében, nem védik a virtuális gépen futó SQL Server rendelkezésre állását. 

Előfordulhat, hogy a SQL Server-példány meghiúsul, miközben a virtuális gép online állapotú és kifogástalan állapotban van. Még az Azure által biztosított magas rendelkezésre állási mechanizmusok is lehetővé teszik a virtuális gépek leállását olyan események miatt, mint a szoftveres vagy hardveres hibák helyreállítása, valamint az operációs rendszer frissítései.

A Geo-redundáns tárolás (GRS) az Azure-ban a Geo-Replication nevű funkcióval valósítható meg. Előfordulhat, hogy a GRS nem megfelelő vész-helyreállítási megoldás az adatbázisokhoz. Mivel a Geo-replikáció aszinkron módon küld adatokat, a legutóbbi frissítések elvesznek a katasztrófák során. A földrajzi replikálási korlátozásokkal kapcsolatos további információkért lásd a [geo-replikáció támogatását](#geo-replication-support) ismertető szakaszt.

## <a name="deployment-architectures"></a>Üzembe helyezési architektúrák
Az Azure támogatja ezeket a SQL Server technológiákat az üzletmenet folytonossága érdekében:

* [Always On rendelkezésre állási csoportok](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On feladatátvevő fürt példányai (FCIs)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [Napló szállítása](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage-ban](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [Adatbázis-tükrözés](/sql/database-engine/database-mirroring/database-mirroring-sql-server) – elavult SQL Server 2016

A technológiákat kombinálhatja egy olyan SQL Server megoldás megvalósításával, amely magas rendelkezésre állású és vész-helyreállítási képességekkel is rendelkezik. A használt technológiától függően a hibrid üzembe helyezéshez szükség lehet egy VPN-alagútra az Azure Virtual Network használatával. Az alábbi részekben néhány példa a telepítési architektúrára.

## <a name="azure-only-high-availability-solutions"></a>Csak az Azure-ban: magas rendelkezésre állású megoldások

Magas rendelkezésre állású megoldást használhat a always on rendelkezésre állási csoportokat tartalmazó adatbázis-szinten való SQL Serverra. Magas rendelkezésre állású megoldást is létrehozhat egy példány szintjén, az Always On feladatátvételi fürt példányain. A további védelem érdekében hozzon létre redundanciát mindkét szinten a rendelkezésre állási csoportok létrehozásával a feladatátvevő fürt példányain. 

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Az azonos régióban található Azure-beli virtuális gépeken futó rendelkezésre állási replikák magas rendelkezésre állást biztosítanak. Konfigurálnia kell egy tartományvezérlő virtuális gépet, mivel a Windows feladatátvételi fürtszolgáltatáshoz Active Directory tartomány szükséges.<br/><br/> A nagyobb redundancia és rendelkezésre állás érdekében az Azure-beli virtuális gépek különböző [rendelkezésre állási zónákban](../../../availability-zones/az-overview.md) helyezhetők üzembe a [rendelkezésre állási csoport áttekintése című témakörben](availability-group-overview.md)leírtak szerint. Ha a rendelkezésre állási csoportban lévő SQL Server virtuális gépek rendelkezésre állási zónákba vannak telepítve, akkor az Azure- [standard Load Balancer](../../../load-balancer/load-balancer-overview.md) a figyelőhöz az Azure [SQL virtuális gép CLI](./availability-group-az-commandline-configure.md) -ben és az [Azure gyorsindító-sablonok](availability-group-quickstart-template-configure.md) cikkeiben leírtak szerint.<br/> ![Diagram, amely az "elsődleges replika", a "másodlagos replika" és a "fájlmegosztás tanúsító" nevű "WSFC-fürt" feletti "tartományvezérlőt" jeleníti meg.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>További információ: [rendelkezésre állási csoportok konfigurálása az Azure-ban (GUI)](./availability-group-quickstart-template-configure.md). |
| **Feladatátvevő fürt példányai** |A feladatátvevő fürtök példányai SQL Server virtuális gépeken támogatottak. Mivel a (z)%-os szolgáltatáshoz megosztott tárterület szükséges, öt megoldás fog működni SQL Server Azure-beli virtuális gépeken: <br/><br/> – Azure-beli [megosztott lemezek](failover-cluster-instance-azure-shared-disks-manually-configure.md) használata a Windows Server 2019-hez. A megosztott felügyelt lemezek olyan Azure-termékek, amelyek lehetővé teszik, hogy egyszerre több virtuális géphez csatolja a felügyelt lemezeket. A fürtben lévő virtuális gépek a fürtözött alkalmazás által választott foglalás alapján elolvashatják vagy írhatják a csatlakoztatott lemezt a SCSI-alapú állandó foglalások (SCSI PR) használatával. Az SCSI PR egy iparági szabványnak megfelelő tárolási megoldás, amelyet a helyi hálózaton (SAN) futó alkalmazások használnak. A felügyelt lemezeken az SCSI PR engedélyezése lehetővé teszi, hogy az Azure-ba is áttelepítse ezeket az alkalmazásokat. <br/><br/>– [Közvetlen tárolóhelyek \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md) használatával biztosíthatja a Windows Server 2016-es és újabb szoftveres virtuális San-t.<br/><br/>– [Prémium fájlmegosztás](failover-cluster-instance-premium-file-share-manually-configure.md) használata a Windows Server 2012-es és újabb verzióihoz. A prémium szintű fájlmegosztás SSD-biztonsági mentéssel, konzisztensen alacsony késéssel, és teljes mértékben támogatott a-vel való használatra.<br/><br/>– A partneri megoldás által a fürtözéshez támogatott tárterület használata. A SIOS DataKeeper használó konkrét példáért lásd a blogbejegyzés [feladatátvételi fürtszolgáltatását és a SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>-Megosztott blokkos tároló használata távoli iSCSI-tárolóhoz az Azure ExpressRoute keresztül. Például a NetApp Private Storage (NPS) egy iSCSI-célt tesz elérhetővé az ExpressRoute-n keresztül az Azure-beli virtuális gépek Equinix.<br/><br/>A Microsoft-partnerek megosztott tárolási és adatreplikációs megoldásaiért forduljon a szállítóhoz a feladatátvételi adatokhoz való hozzáféréssel kapcsolatos esetleges problémákhoz.<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>Csak az Azure-ban: vész-helyreállítási megoldások
Az Azure-ban a rendelkezésre állási csoportokkal, az adatbázis-tükrözéssel, illetve a tárolási Blobokkal történő biztonsági mentéssel és visszaállítással vész-helyreállítási megoldást használhat a SQL Server adatbázisaihoz.

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Az Azure-beli virtuális gépek több adatközpontjában futó rendelkezésre állási replikák a vész-helyreállítás érdekében. Ez a régiók közötti megoldás segít megvédeni a teljes hely kimaradását. <br/> ![Olyan diagram, amely két olyan régiót mutat be, amely az "elsődleges replika" és a "másodlagos replika" egy "aszinkron véglegesítve" kapcsolattal van összekötve.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>Egy adott régión belül minden replikának ugyanabban a felhőalapú szolgáltatásban és ugyanazon a virtuális hálózaton belül kell lennie. Mivel mindegyik régió külön virtuális hálózattal rendelkezik, ezek a megoldások hálózat – hálózat kapcsolatot igényelnek. További információ: [hálózat – hálózat közötti kapcsolatok konfigurálása a Azure Portal használatával](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Részletes útmutatásért lásd: [SQL Server always on rendelkezésre állási csoport konfigurálása különböző Azure-régiók között](availability-group-manually-configure-multiple-regions.md).|
| **Adatbázis-tükrözés** |A különböző adatközpontokban futó elsődleges és tükrözött kiszolgálók a vész-helyreállításhoz. Ezeket kiszolgálói tanúsítványok használatával kell telepítenie. SQL Server adatbázis-tükrözés nem támogatott az Azure-beli virtuális gépen SQL Server 2008 vagy SQL Server 2008 R2 esetén. <br/>![Diagram, amely a "résztvevő" kifejezést mutatja egy olyan régióban, amely a "nagy teljesítményű" egy másik régióban található tükrözéshez csatlakozik.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage-ban** |A termelési adatbázisok biztonsági mentése egy másik adatközpontban, a vész-helyreállítás érdekében közvetlenül a blob Storage-ba.<br/>![Diagram, amely egy másik régióban található "Blob Storage" biztonsági mentést tartalmazó régió "adatbázisát" mutatja.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>További információ: [SQL Server biztonsági mentése és visszaállítása Azure-beli virtuális gépeken](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **SQL Server replikálása és feladatátvétele az Azure-ba Azure Site Recovery** |Az Azure-adatközpontok üzemi SQL Server példánya közvetlenül az Azure Storage-ba replikálódik egy másik Azure-adatközpontban a vész-helyreállítás érdekében.<br/>![Diagram, amely egy másik adatközpontban az "ASR-replikáció" használatával egy "adatbázis" egy Azure-adatközpontban látható. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>További információkért tekintse meg a [SQL Server SQL Server vész-helyreállítási és-Azure site Recovery használatával történő védelemmel](../../../site-recovery/site-recovery-sql.md)foglalkozó témakört. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hibrid IT: vész-helyreállítási megoldások
Az Azure Blob Storage szolgáltatással a rendelkezésre állási csoportok, az adatbázis-tükrözés, a naplózás és a biztonsági mentés és visszaállítás segítségével vész-helyreállítási megoldást használhat a SQL Server adatbázisaihoz a hibrid informatikai környezetben.

| Technológia | Példa architektúrák |
| --- | --- |
| **Rendelkezésre állási csoportok** |Néhány rendelkezésre állási másodpéldány az Azure-beli virtuális gépeken és a helyszínen futó más replikákban a helyek közötti vész-helyreállításhoz. Az üzemi hely lehet akár helyszíni, akár egy Azure-adatközpontban is.<br/>![Rendelkezésre állási csoportok](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>Mivel az összes rendelkezésre állási másodpéldánynak ugyanabban a feladatátvevő fürtben kell lennie, a fürtnek mindkét hálózatra (több alhálózatos feladatátvevő fürtre) kell terjednie. Ehhez a konfigurációhoz VPN-kapcsolat szükséges az Azure és a helyszíni hálózat között.<br/><br/>Az adatbázisok sikeres vész-helyreállításához a vész-helyreállítási helyen is telepítenie kell egy replika tartományvezérlőt.|
| **Adatbázis-tükrözés** |Egy partner, amely egy Azure-beli virtuális gépen, a másik pedig a helyszínen fut a helyek közötti vész-helyreállításhoz kiszolgálói tanúsítványok használatával. A partnereknek nem kell ugyanabban a Active Directory tartományban lennie, és nincs szükség VPN-kapcsolatra.<br/>![Adatbázis-tükrözés](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>Egy másik adatbázis-tükrözési forgatókönyv egy olyan partnert foglal magában, amely egy Azure-beli virtuális gépen fut, a másik pedig ugyanazon a Active Directory tartományban fut a helyek közötti vész-helyreállítás érdekében. [Az Azure-beli virtuális hálózat és a helyszíni hálózat közötti VPN-kapcsolatra](../../../vpn-gateway/tutorial-site-to-site-portal.md) van szükség.<br/><br/>Az adatbázisok sikeres vész-helyreállításához a vész-helyreállítási helyen is telepítenie kell egy replika tartományvezérlőt. SQL Server adatbázis-tükrözés nem támogatott az Azure-beli virtuális gépen SQL Server 2008 vagy SQL Server 2008 R2 esetén. |
| **Napló szállítása** |Egy Azure-beli virtuális gépen és a helyszínen futó másik kiszolgálón, amely a helyek közötti vész-helyreállítást végzi. A naplózás a Windows fájlmegosztás függvénye, így az Azure-beli virtuális hálózat és a helyszíni hálózat közötti VPN-kapcsolat szükséges.<br/>![Naplóküldés](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>Az adatbázisok sikeres vész-helyreállításához a vész-helyreállítási helyen is telepítenie kell egy replika tartományvezérlőt. |
| **Biztonsági mentés és visszaállítás az Azure Blob Storage-ban** |A helyszíni éles adatbázisok biztonsági mentése közvetlenül az Azure Blob Storage-ba, a vész-helyreállítás érdekében.<br/>![Biztonsági mentés és visszaállítás](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>További információ: [SQL Server biztonsági mentése és visszaállítása az Azure Virtual Machinesban](../../../azure-sql/virtual-machines/windows/backup-restore.md). |
| **SQL Server replikálása és feladatátvétele az Azure-ba Azure Site Recovery** |A helyszíni üzemi SQL Server példány közvetlenül az Azure Storage-ba replikálódik a vész-helyreállítás érdekében.<br/>![Replikálás Azure Site Recovery használatával](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>További információkért tekintse meg a [SQL Server SQL Server vész-helyreállítási és-Azure site Recovery használatával történő védelemmel](../../../site-recovery/site-recovery-sql.md)foglalkozó témakört. |


## <a name="free-dr-replica-in-azure"></a>Ingyenes DR-replika az Azure-ban

Ha rendelkezik frissítési [garanciával](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3), hibrid vész-helyreállítási (Dr) terveket is alkalmazhat SQL Server a passzív vész-helyreállítási példányra vonatkozó további licencelési költségek nélkül.

Használhat például két ingyenes passzív formátumú másodlagos zónák, ha mindhárom replikát az Azure-ban üzemelteti: 

![Két ingyenes passzív, ha minden az Azure-ban](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

Vagy konfigurálhat egy hibrid feladatátvételi környezetet is, amely egy licenccel rendelkező elsődleges helyszíni, egy ingyenes passzív, HA pedig egy ingyenes passzív a DR helyszíni környezethez, és egy ingyenes passzív DR az Azure-ban:

![Három szabad passzív, ha a környezet hibrid egy elsődleges helyszíni replikával](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

További információkért tekintse meg a [termék licencelési feltételeit](https://www.microsoft.com/licensing/product-licensing/products). 

A kedvezmény engedélyezéséhez lépjen a [SQL Server virtuális gép erőforrására](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Válassza a **Konfigurálás** lehetőséget a **Beállítások** területen, majd válassza a vész- **helyreállítási** lehetőséget a **SQL Server licenc** alatt. Jelölje be a jelölőnégyzetet annak ellenőrzéséhez, hogy ez a SQL Server VM passzív replikaként lesz-e használatban, majd válassza az **alkalmaz** lehetőséget a beállítások mentéséhez. 

![Vész-helyreállítási replika konfigurálása az Azure-ban](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Fontos szempontok az Azure-beli SQL Server HADR
Az Azure-beli virtuális gépek, a tárolók és a hálózatkezelés eltérő működési jellemzőkkel rendelkeznek, mint a helyszíni, nem virtualizált informatikai infrastruktúra. Az Azure-beli HADR SQL Server megoldás sikeres megvalósítása megköveteli, hogy Ismerje meg ezeket a különbségeket, és tervezze meg a megoldását.

### <a name="high-availability-nodes-in-an-availability-set"></a>Magas rendelkezésre állású csomópontok rendelkezésre állási csoportokban
Az Azure-beli rendelkezésre állási csoportok lehetővé teszik, hogy a magas rendelkezésre állású csomópontokat különálló tartalék tartományokra és frissítési tartományokra helyezze. Az Azure platform egy frissítési tartományt és egy tartalék tartományt rendel a rendelkezésre állási csoportba tartozó egyes virtuális gépekhez. Ez az adatközponton belüli konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen, és teljesítse az 99,95%-os Azure SLA-t. 

Magas rendelkezésre állású telepítés konfigurálásához helyezzen minden résztvevő SQL Server virtuális gépet ugyanabban a rendelkezésre állási csoportba, hogy elkerülje az alkalmazás vagy az adatvesztést a karbantartási események során. Ugyanahhoz a rendelkezésre állási csoporthoz csak az azonos felhőalapú szolgáltatásban lévő csomópontok vehetnek részt. További információk: [Virtuális gépek rendelkezésre állásának kezelése](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Magas rendelkezésre állású csomópontok egy rendelkezésre állási zónában
A rendelkezésre állási zónák egy Azure-régióban található egyedi fizikai helyek. Minden zóna egy vagy több független energiaellátással, hűtéssel és hálózatkezeléssel felszerelt adatközpontból áll. A rendelkezésre állási zónák régión belüli fizikai elkülönítése segíti az alkalmazások és az adatok adatközpontbeli meghibásodások elleni védettségét azáltal, hogy legalább egy virtuális gép elérhetővé válik, és megfelel az 99,99%-os Azure SLA-nak. 

A magas rendelkezésre állás konfigurálásához helyezze a részt SQL Server virtuális gépeket a régió rendelkezésre állási zónái között. A rendelkezésre állási zónák közötti hálózati átvitel további díjakat is igénybe vesz. További információ: [rendelkezésre állási zónák](../../../availability-zones/az-overview.md). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>A feladatátvevő fürt működése az Azure hálózatkezelésében
A nem RFC-kompatibilis DHCP szolgáltatás az Azure-ban azt eredményezheti, hogy a feladatátvevő fürt bizonyos konfigurációi sikertelenek lesznek. Ez a hiba azért fordul elő, mert a fürt hálózati neve ismétlődő IP-címet kap, például ugyanazt az IP-címet, mint a fürtcsomópontok egyikét. Ez a probléma olyan rendelkezésre állási csoportok használata esetén jelent problémát, amelyek a Windows feladatátvételi fürtszolgáltatástól függenek.

Két csomópontos fürt létrehozásakor és online állapotba állításakor vegye figyelembe a következőket:

1. A fürt online állapotba kerül, majd a CSOMÓPONT1 dinamikusan hozzárendelt IP-címet kér a fürt hálózati neveként.
2. A DHCP szolgáltatás nem biztosít IP-címet a NODE1's saját IP-címétől, mert a DHCP-szolgáltatás felismeri, hogy a kérés a CSOMÓPONT1 származik.
3. A Windows azt észleli, hogy a CSOMÓPONT1 és a feladatátvevő fürt hálózati neve között ismétlődő címek vannak hozzárendelve, és az alapértelmezett fürtcsomópont nem fog online állapotba jutni.
4. Az alapértelmezett fürtözött csoport a CSOMÓPONT2-re kerül. A CSOMÓPONT2 a NODE1's IP-címét a fürt IP-címével kezeli, és az alapértelmezett fürtöt online állapotba helyezi.
5. Amikor a CSOMÓPONT2 megpróbál kapcsolatot létesíteni a CSOMÓPONT1, a CSOMÓPONT1-re irányuló csomagok soha nem hagyják el a CSOMÓPONT2, mert a NODE1's IP-címet önmagára oldja fel. A CSOMÓPONT2 nem tud kapcsolatot létesíteni a CSOMÓPONT1, majd elveszíti a kvórumot, és leállítja a fürtöt.
6. A CSOMÓPONT1 csomagokat küldhet a CSOMÓPONT2, de a CSOMÓPONT2 nem tud válaszolni. A CSOMÓPONT1 elveszíti a kvórumot, és leállítja a fürtöt.

Ezt a forgatókönyvet elkerülheti úgy, hogy nem használt statikus IP-címet rendel hozzá a fürt hálózati neveként, hogy a fürt hálózatnév online állapotba kerüljön. Használhat például egy kapcsolaton belüli IP-címet, például a 169.254.1.1-t. A folyamat leegyszerűsítése érdekében lásd: a [Windows feladatátvevő fürt konfigurálása az Azure-ban rendelkezésre állási csoportok számára](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

További információ: [rendelkezésre állási csoportok konfigurálása az Azure-ban (GUI)](./availability-group-quickstart-template-configure.md).

### <a name="support-for-availability-group-listeners"></a>A rendelkezésre állási csoport figyelők támogatása
A rendelkezésre állási csoport figyelők a Windows Server 2012-es vagy újabb verzióját futtató Azure-beli virtuális gépeken támogatottak. Ezt a támogatást a rendelkezésre állási csoport csomópontjait használó Azure-beli virtuális gépeken engedélyezett elosztott terhelésű végpontok használatával lehet elvégezni. A figyelőkhöz speciális konfigurációs lépéseket kell követnie, amelyek az Azure-ban és a helyszínen futó összes ügyfélalkalmazás esetében működnek.

A figyelő beállításának két fő lehetősége van: külső (nyilvános) vagy belső. A külső (nyilvános) figyelő internetkapcsolattal rendelkező Load balancert használ, és az interneten keresztül elérhető nyilvános virtuális IP-címhez van társítva. A belső figyelő belső terheléselosztó használatával csak az azonos virtuális hálózatban lévő ügyfeleket támogatja. A terheléselosztó típusaként engedélyeznie kell a közvetlen kiszolgáló visszaküldését. 

Ha a rendelkezésre állási csoport több Azure-alhálózatra is kiterjed (például az Azure-régiókat keresztező központi telepítés), az ügyfél-kapcsolódási karakterláncnak tartalmaznia kell a következőt: `MultisubnetFailover=True` . Ennek eredményeképpen a párhuzamos kapcsolódás megkísérli a különböző alhálózatokban lévő replikákat. A figyelő beállításával kapcsolatos útmutatásért lásd: [ILB-figyelő konfigurálása a rendelkezésre állási csoportokhoz az Azure-ban](availability-group-listener-powershell-configure.md).


Továbbra is csatlakozhat az egyes rendelkezésre állási replikához, ha közvetlenül a szolgáltatási példányhoz csatlakozik. Emellett mivel a rendelkezésre állási csoportok visszamenőleg kompatibilisek az adatbázis-tükrözési ügyfelekkel, csatlakozhat a rendelkezésre állási replikához, például az adatbázis-tükrözési partnerekhez, ha a replikák az adatbázis-tükrözéshez hasonlóan vannak konfigurálva:

* Van egy elsődleges replika és egy másodlagos replika.
* A másodlagos replika nem olvashatóként van konfigurálva (**olvasható másodlagos** beállítás: **nem**).

Az alábbi példa egy olyan ügyfél-kapcsolódási karakterláncot mutat be, amely megfelel ehhez az adatbázis-tükrözéshez hasonló konfigurációnak a ADO.NET vagy a SQL Server Native Client használatával:

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

További információ az ügyfelek kapcsolatáról:

* [A kapcsolatok sztring kulcsszavainak használata SQL Server Native Client](/sql/relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client)
* [Ügyfelek összekötése egy adatbázis-tükrözési munkamenettel (SQL Server)](/sql/database-engine/database-mirroring/connect-clients-to-a-database-mirroring-session-sql-server)
* [Csatlakozás a rendelkezésre állási csoport figyelője számára a hibrid informatikai szolgáltatásban](/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [A rendelkezésre állási csoport figyelők, az ügyfélkapcsolatok és az alkalmazások feladatátvétele (SQL Server)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)
* [Database-Mirroring kapcsolatok karakterláncok használata rendelkezésre állási csoportokkal](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover)

### <a name="network-latency-in-hybrid-it"></a>Hálózati késés a hibrid informatikai szolgáltatásban
A HADR-megoldás üzembe helyezésével feltételezhető, hogy a helyszíni hálózat és az Azure közötti magas hálózati késés fordul elő. Amikor replikákat telepít az Azure-ba, szinkron véglegesítés helyett aszinkron végrehajtást használjon a szinkronizálási módhoz. Ha a helyszíni és az Azure-ban egyaránt telepíti az adatbázis-tükrözési kiszolgálókat, a magas szintű biztonsági mód helyett használja a nagy teljesítményű módot.

### <a name="geo-replication-support"></a>Geo-replikáció támogatása
A Geo-replikáció az Azure-lemezeken nem támogatja ugyanazon adatbázis adatfájlját és naplófájlját, hogy külön lemezen legyenek tárolva. A GRS egymástól függetlenül és aszinkron módon replikálja az egyes lemezek változásait. Ez a mechanizmus garantálja az írási sorrendet a földrajzilag replikált másolaton belül egy lemezen, a több lemez földrajzilag replikált példányai között azonban nem. Ha úgy konfigurálja az adatbázist, hogy az adatfájlját és a naplófájlját külön lemezeken tárolja, akkor a katasztrófa utáni helyreállított lemezek a naplófájlban lévő adatfájlnak egy újabb másolatát is tartalmazhatják, amely megtöri a SQL Server írási naplóját, és a sav tulajdonságait (a tranzakciók egységességét, következetességét, elkülönítését és tartósságát). 

Ha nem szeretné letiltani a Geo-replikációt a Storage-fiókon, akkor az adatbázis összes adatfájlját és naplófájlját egy adott lemezen tárolja. Ha az adatbázis mérete miatt egynél több lemezt kell használnia, a korábban felsorolt vész-helyreállítási megoldások egyikét kell telepítenie az adatredundancia biztosítása érdekében.

## <a name="next-steps"></a>Következő lépések

Döntse el, hogy egy [rendelkezésre állási csoport](availability-group-overview.md) vagy egy [feladatátvevő fürt példánya](failover-cluster-instance-overview.md) a legjobb üzletmenet-folytonossági megoldás-e a vállalat számára. Ezután tekintse át az [ajánlott eljárásokat](hadr-cluster-best-practices.md) a környezet konfigurálásához a magas rendelkezésre állás és a vész-helyreállítás érdekében.