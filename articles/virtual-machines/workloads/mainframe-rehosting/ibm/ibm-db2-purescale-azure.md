---
title: IBM DB2-pureScale az Azure-ban
description: Ebben a cikkben egy olyan architektúrát mutatunk be, amely egy IBM DB2 pureScale-környezetet futtat az Azure-ban.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 08e7a594fba3e660ea1a2a8561bcdeed236b1dfb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561585"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2-pureScale az Azure-ban

Az IBM DB2 pureScale-környezet az Azure-hoz készült adatbázis-fürtöt biztosít magas rendelkezésre állással és méretezhetőséggel a Linux operációs rendszereken. Ez a cikk egy olyan architektúrát mutat be, amely az Azure-beli DB2-pureScale futtatására használható.

## <a name="overview"></a>Áttekintés

A vállalatok régóta használják a hagyományos kapcsolati adatbázis-kezelő rendszer (RDBMS) platformokat az online tranzakció-feldolgozási (OLTP) igények kielégítésére. Manapság sok a nagyszámítógép-alapú adatbázis-környezetek áttelepítése az Azure-ba a kapacitás bővítése, a költségek csökkentése és a folyamatos üzemeltetési költségek fenntartása érdekében. Az áttelepítés gyakran a régi platform korszerűsítésének első lépése. 

Az utóbbi időben a nagyvállalati ügyfelek a z/OS-ben üzemeltetett IBM DB2-környezetet az Azure-ban az IBM DB2 pureScale-be működtették. A DB2 pureScale adatbázis-fürt megoldás magas rendelkezésre állást és méretezhetőséget biztosít a Linux operációs rendszereken. Az ügyfél a DB2-pureScale telepítése előtt egyetlen virtuális gépen (VM) önálló, Felskálázási példányként futtatta a DB2-t. 

Bár az eredeti környezet nem azonos, az IBM DB2 pureScale Linux rendszeren hasonló magas rendelkezésre állási és méretezhetőségi funkciókat biztosít, mint az IBM DB2 a z/OS-ben, amely párhuzamos Sysplex konfigurációban fut a nagyszámítógépeken. Ebben az esetben a fürt iSCSI-kapcsolaton keresztül csatlakozik egy megosztott tároló fürthöz. A GlusterFS fájlrendszert, egy ingyenes, méretezhető, nyílt forráskódú elosztott fájlrendszert használunk, amely kifejezetten a felhőalapú tároláshoz van optimalizálva. Az IBM azonban már nem támogatja ezt a megoldást. Az IBM támogatásának fenntartása érdekében egy támogatott iSCSI-kompatibilis fájlrendszert kell használnia. A Microsoft a Közvetlen tárolóhelyek (S2D) lehetőséget kínálja

Ez a cikk az Azure áttelepítéshez használt architektúrát ismerteti. Az ügyfél a Red Hat Linux 7,4-et használta a konfiguráció teszteléséhez. Ez a verzió az Azure piactéren érhető el. A Linux-disztribúció kiválasztása előtt ellenőrizze a jelenleg támogatott verziókat. Részletekért tekintse meg az [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) és a [GlusterFS](https://docs.gluster.org/en/latest/)dokumentációját.

Ez a cikk a DB2-megvalósítási terv kiindulási pontja. Az üzleti igények eltérőek lesznek, de ugyanaz az alapszintű minta is érvényes. Ezt az építészeti mintát használhatja az Azure-beli online analitikus feldolgozási (OLAP) alkalmazásokhoz is.

Ez a cikk nem fedi le az IBM DB2 for z/OS adatbázisnak a Linux rendszeren futó IBM DB2 pureScale való áthelyezésével kapcsolatos különbségeket és lehetséges áttelepítési feladatokat. És nem biztosítja a méretezési becsléseket és a számítási feladatok elemzését a DB2 z/OS-ről a DB2-pureScale való áttéréshez. 

A környezet legjobb DB2 pureScale architektúrájának meghatározásához javasoljuk, hogy teljes mértékben becsülje meg a méretezést, és hozzon ki egy hipotézist. A forrásrendszer esetében ügyeljen arra, hogy a DB2 z/OS párhuzamos Sysplex az adatmegosztási architektúra, a csatlakozási lehetőség konfigurációja és az elosztott adatközpont (DDF) használati statisztikái alapján vegye figyelembe.

> [!NOTE]
> Ez a cikk az DB2-Migrálás egyik módszerét ismerteti, de mások is vannak. A DB2-pureScale például virtualizált helyszíni környezetekben is futhatnak. Az IBM számos konfigurációban támogatja a DB2 használatát Microsoft Hyper-V. További információ: [DB2 pureScale Virtualization Architecture](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) az IBM Knowledge Centerben.

## <a name="architecture"></a>Architektúra

Az Azure magas rendelkezésre állásának és méretezhetőségének támogatásához kibővített, közös adatarchitektúrát használhat a DB2-pureScale. Az ügyfél áttelepítése a következő példa architektúrát használta.

![DB2-pureScale az Azure Virtual Machines szolgáltatásban tárolás és hálózatkezelés](media/pureScaleArchitecture.png "DB2-pureScale az Azure Virtual Machines szolgáltatásban tárolás és hálózatkezelés")


Az ábrán egy DB2 pureScale-fürthöz szükséges logikai rétegek láthatók. Ilyenek például a virtuális gépek egy ügyfélhez, a felügyelethez, a gyorsítótárazáshoz, az adatbázismotor és a megosztott tároláshoz. 

Az adatbázismotor csomópontjain kívül a diagram két, a fürt gyorsítótárazási létesítményeihez (CFs) használt csomópontot is tartalmaz. Maga az adatbázismotor legalább két csomópontot használ. A pureScale-fürthöz tartozó DB2-kiszolgáló neve tag. 

A fürt iSCSI-kapcsolaton keresztül csatlakozik egy három csomópontos megosztott tárolási fürthöz, hogy kibővíthető tárterületet és magas rendelkezésre állást biztosítson. A DB2 pureScale a Linux rendszerű Azure-beli virtuális gépekre van telepítve.

Ez a módszer egy olyan sablon, amelyet a szervezet méretének és méretezésének módosításához módosíthat. A következőkön alapul:

-   Két vagy több adatbázis-tag legalább két CF-csomóponttal van egyesítve. A csomópontok a közös memóriához és a Global Lock Manager-(GLM-) szolgáltatásokhoz tartozó globális puffert (GBP) kezelik a megosztott hozzáférés szabályozása érdekében, és az aktív tagoktól származó tartalom zárolását. Egy CF-csomópont elsődlegesként, a másik pedig másodlagos, feladatátvételi CF-csomópontként működik. A környezet egyetlen meghibásodási pontjának elkerülése érdekében a DB2 pureScale-fürtökhöz legalább négy csomópont szükséges.

-   Nagy teljesítményű megosztott tároló (az ábrán a P30 méretben látható). Mindegyik csomópont ezt a tárolót használja.

-   Nagy teljesítményű hálózatkezelés az adattagok és a megosztott tároló számára.

### <a name="compute-considerations"></a>Számítási megfontolások

Ez az architektúra az Azure-beli virtuális gépeken futtatja az alkalmazások, a tárolók és az adatszinteket. A [telepítés telepítési parancsfájljai](https://aka.ms/db2onazure) a következőket hozza létre:

-   Egy DB2 pureScale-fürt. Az Azure-ban szükséges számítási erőforrások típusa a beállítástól függ. Általánosságban elmondható, hogy két módszert használ:

    -   Többcsomópontos, nagy teljesítményű számítástechnikai (HPC) stílusú hálózat használata, ahol kis-és közepes méretű példányok férnek hozzá a megosztott tárolóhoz. Ebben a HPC-típusú konfigurációban az Azure memóriára optimalizált E-sorozat-vagy Storage-optimalizált L sorozatú [virtuális gépek](../../../sizes.md) biztosítják a szükséges számítási teljesítményt.

    -   Használjon kevesebb nagyméretű virtuálisgép-példányt az adatkezelők számára. Nagyméretű példányok esetén a legnagyobb memóriára optimalizált [M sorozatú](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuális gépek ideálisak a nagy mennyiségű memóriabeli számítási feladatokhoz. A DB2 futtatásához használt logikai partíció (LPAR) méretétől függően szükség lehet egy dedikált példányra.

-   A DB2 CF Memória-optimalizált virtuális gépeket használ, például az E-sorozatot vagy az L-sorozatot.

-   A \_ \_ Linux rendszerű standard DS4 v2 virtuális gépeket használó megosztott tárolóhelyek fürtje.

-   A felügyeleti Jumpbox egy \_ \_ Linux rendszerű standard DS2 v2 virtuális gép.  Egy másik lehetőség az Azure Bastion, amely biztonságos RDP/SSH-élményt biztosít a virtuális hálózatban lévő összes virtuális gép számára.

-   Az ügyfél egy \_ \_ Windows rendszerű standard DS3 v2 virtuális gép (teszteléshez használatos).

-   Nem *kötelező*. Tanúsító kiszolgáló. Erre csak a DB2-pureScale egyes korábbi verzióira van szükség. Ez a példa egy standard \_ DS3 \_ v2 virtuális gépet használ, amely a Linux rendszert futtatja (a DB2-pureScale esetében használatos).

> [!NOTE]
> A DB2 pureScale-fürtök legalább két DB2-példányt igényelnek. Szükség van egy gyorsítótár-példányra és egy Lock Manager-példányra is.

### <a name="storage-considerations"></a>A tárterülettel kapcsolatos szempontok

Az Oracle RAC-hoz hasonlóan a DB2 pureScale egy nagy teljesítményű blokk I/O-, kibővíthető adatbázis. Javasoljuk, hogy az igényeinek megfelelő legnagyobb [Azure Premium SSD](../../../disks-types.md) -beállítást használja. A kisebb tárolási lehetőségek a fejlesztési és tesztelési környezetekhez megfelelőek lehetnek, míg az éles környezetekben gyakran több tárolókapacitásra van szükség. A példában szereplő architektúra a [P30](https://azure.microsoft.com/pricing/details/managed-disks/) -t használja a IOPS és az ár méretének arányában. A mérettől függetlenül használja a Premium Storage a legjobb teljesítmény érdekében.

A DB2-pureScale egy megosztott – minden olyan architektúrát használ, ahol az összes adatok elérhetők az összes fürtcsomóponton. A Premium Storage-ot több példányban kell megosztani, akár igény szerint, akár dedikált példányokon.

A nagyméretű DB2 pureScale-fürtök 200 terabájt (TB) vagy több prémium szintű megosztott tárterületet igényelhetnek, 100 000-IOPS. A DB2 pureScale támogatja az Azure-ban használható iSCSI-blokk felületet. Az iSCSI-felülethez olyan megosztott S2D szükséges, amelyet egy másik eszközzel lehet megvalósítani. Ez a típusú megoldás létrehoz egy Virtual Storage Network (vSAN) eszközt az Azure-ban. A DB2 pureScale a vSAN használatával telepíti az adatmegosztáshoz használt fürtözött fájlrendszert a virtuális gépek között.

### <a name="networking-considerations"></a>Hálózati megfontolások

Az IBM a InfiniBand hálózatkezelést javasolja egy DB2 pureScale-fürt összes tagjához. A DB2 pureScale a távoli közvetlen memória-hozzáférést (RDMA) is használja, ahol elérhető, a CFs számára.

A telepítés során létre kell hoznia egy Azure- [erőforráscsoportot](../../../../azure-resource-manager/management/overview.md) , amely tartalmazza az összes virtuális gépet. Általánosságban elmondható, hogy az erőforrásokat az élettartamuk alapján csoportosítják, és ki fogják kezelni őket. Az architektúrában található virtuális gépek [gyorsított hálózatkezelést](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)igényelnek. Ez egy olyan Azure-szolgáltatás, amely egységes, rendkívül alacsony hálózati késést biztosít egy virtuális gép számára egy gyökérszintű I/O-virtualizálás (SR-IOV) használatával.

Minden Azure-beli virtuális gép üzembe helyezése egy alhálózattal rendelkező virtuális hálózatban történik: Main, Gluster FS előtér (gfsfe), Gluster FS háttérrendszer (bfsbe), DB2 pureScale (db2be) és DB2 pureScale előtér (db2fe). A telepítési parancsfájl az elsődleges hálózati [adaptereket](../../../windows/multiple-nics.md) is létrehozza a fő alhálózatban lévő virtuális gépeken.

Használjon [hálózati biztonsági csoportokat](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md) a virtuális hálózaton belüli hálózati forgalom korlátozására és az alhálózatok elkülönítésére.

Az Azure-ban a DB2-pureScale a TCP/IP protokollt kell használnia hálózati kapcsolódásként a tároláshoz.

## <a name="next-steps"></a>Következő lépések

-   [Az architektúra üzembe helyezése az Azure-ban](deploy-ibm-db2-purescale-azure.md)