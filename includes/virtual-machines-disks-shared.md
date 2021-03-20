---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 74c77356df4f35461a8b9f1459712cdcf7f77cbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560538"
---
Az Azure Shared Disks az Azure Managed Disks új funkciója, amely lehetővé teszi, hogy egyszerre több virtuális géphez (VM) csatolja a felügyelt lemezeket. A felügyelt lemezek több virtuális géphez való csatolásával új vagy meglévő fürtözött alkalmazásokat telepíthet át az Azure-ba.

## <a name="how-it-works"></a>Működés

A fürtben lévő virtuális gépek a fürtözött alkalmazás által az [állandó SCSI-foglalások](https://www.t10.org/members/w_spc3.htm) (SCSI PR) használatával kiválasztott foglalás alapján tudják olvasni vagy írni a csatlakoztatott lemezeket. Az SCSI PR egy iparági szabvány, amely a helyi hálózaton (SAN) futó alkalmazások által használható. A felügyelt lemezeken az SCSI PR engedélyezése lehetővé teszi, hogy áttelepítse ezeket az alkalmazásokat az Azure-ba.

A megosztott felügyelt lemezek olyan megosztott blokkos tárolót kínálnak, amely több virtuális gépről is elérhető, és ezek logikai egységként (LUN) vannak kitéve. Ezután a logikai egységeket a rendszer egy kezdeményező (virtuális gép) számára mutatja be egy cél (lemez) alapján. Ezek a logikai egységek úgy néznek ki, mint a Direct-Attached-Storage (DAS) vagy egy helyi meghajtó a virtuális géphez.

A megosztott felügyelt lemezek nem natív módon biztosítanak egy teljes körűen felügyelt fájlrendszert, amely SMB/NFS használatával érhető el. A fürt csomópontjainak kommunikációs és írási zárolását kezelő fürtszolgáltatást kell használnia, például a Windows Server feladatátvevő fürtöt (WSFC) vagy a pacemakert.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Operációsrendszer-követelmények

A megosztott lemezek több operációs rendszert támogatnak. Tekintse meg a támogatott operációs rendszerek [Windows](#windows) vagy [Linux](#linux) rendszerű részeit.

## <a name="disk-sizes"></a>Lemezek mérete

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Minta számítási feladatok

### <a name="windows"></a>Windows

Az Azure-beli megosztott lemezek a Windows Server 2008-es és újabb verzióiban támogatottak. A legtöbb Windows-alapú fürtözés a WSFC-ra épül, amely az összes alapvető infrastruktúrát kezeli a fürt csomópontjainak kommunikációjában, így az alkalmazások kihasználhatják a párhuzamos hozzáférési minták előnyeit. A WSFC a Windows Server verziójától függően a CSV- és nem CSV-alapú beállításokat is elérhetővé teszi. A részleteket lásd: [Feladatátvevő fürt létrehozásához](/windows-server/failover-clustering/create-failover-cluster).

Néhány a WSFC-n futó népszerű alkalmazások közül:

- [Az Azure Shared Disks (SQL Server Azure-beli virtuális gépeken) létrehozása](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Kibővíthető fájlkiszolgáló (SoFS) [sablon] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [sablon] (https://aka.ms/azure-shared-disk-sapacs-template)
- Általános célú fájlkiszolgáló (IW számítási feladat)
- Távoli asztali kiszolgáló felhasználói profillemez (RDS UPD)

### <a name="linux"></a>Linux

Az Azure-beli megosztott lemezek a következő címen támogatottak:
- [SUSE SLE az SAP és a SUSE SLE HA 15 SP1 és újabb verziókhoz](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18,04 és újabb verziók](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [A RHEL fejlesztői előzetes verziója bármely RHEL 8 verzióban](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

A Linux-fürtök képesek kihasználni a fürtszolgáltatásokat, például a [pacemakert](https://wiki.clusterlabs.org/wiki/Pacemaker). A pacemaker a [Corosync](http://corosync.github.io/corosync/)-ra épül, és lehetővé teszi a fürtök kommunikációját a magasan elérhető környezetekben üzembe helyezett alkalmazásokhoz. Egyes gyakori fürtözött fájlrendszerek közé tartozik a [OCFS2](https://oss.oracle.com/projects/ocfs2/) és a [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). A SCSI állandó foglalás (SCSI PR) és/vagy STONITH blokk Device (SBD) alapú fürtszolgáltatási modellek használhatók a lemezhez való egyeztetéshez. Az SCSI PR használatakor a foglalásokat és a regisztrációkat a [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) és [sg_persist](https://linux.die.net/man/8/sg_persist)segédprogramokkal is kezelheti.

## <a name="persistent-reservation-flow"></a>Állandó foglalási folyamat

A következő ábra egy minta 2 csomópontos fürtözött adatbázis-alkalmazást mutat be, amely az SCSI PR-t használja az egyik csomópontról a másikra történő feladatátvétel engedélyezéséhez.

![Két csomópontos fürt. A fürtön futó alkalmazások kezelik a lemezhez való hozzáférést.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

A folyamat a következő:

1. Az Azure VM1 és VM2-on futó fürtözött alkalmazás a lemezre való írásra vagy írásra is regisztrálja a szándékát.
1. A VM1 lévő alkalmazás példánya kizárólagos foglalást tart a lemezre való íráshoz.
1. Ezt a foglalást az Azure-lemezen kell kikényszeríteni, és az adatbázis már kizárólag a lemezre tud írni. A VM2 alkalmazás példányában lévő összes írás sikertelen lesz.
1. Ha a VM1 futó alkalmazás példánya leáll, a VM2-példány mostantól kezdeményezheti az adatbázis feladatátvételét és a lemez átvételét.
1. Ez a foglalás már érvényben van az Azure-lemezen, és a lemez már nem fogadja el az írásokat a VM1. A művelet csak a VM2 származó írásokat fogadja el.
1. A fürtözött alkalmazás elvégezheti az adatbázis feladatátvételét, és kézbesítheti a VM2 érkező kéréseket.

Az alábbi ábrán egy olyan gyakori fürtözött munkaterhelés látható, amely több csomópontból származó adatok olvasását mutatja be a lemezről a párhuzamos folyamatok futtatásához, például a gépi tanulási modellek betanításához.

![Négy csomópontos virtuálisgép-fürt, az egyes csomópontok az írási szándékot regisztrálják, az alkalmazás kizárólagos foglalást tart az írási eredmények megfelelő kezelésére.](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

A folyamat a következő:

1. Az összes virtuális gépen futó fürtözött alkalmazás a lemezre való íráshoz vagy íráshoz regisztrálja a szándékot.
1. A VM1 alkalmazás-példánya kizárólagos foglalást használ a lemezre való íráshoz, miközben a más virtuális gépekről érkező olvasási adatokat nyit meg a lemezre.
1. Ezt a foglalást az Azure-lemezen kell kikényszeríteni.
1. A fürt összes csomópontja most már képes olvasni a lemezről. A fürt összes csomópontja nevében csak egy csomópont írja vissza az eredményeket a lemezre.

### <a name="ultra-disks-reservation-flow"></a>Ultra lemezek foglalási folyamata

Az ultra-lemezek további szabályozást kínálnak, összesen két szabályozásra. Ennek köszönhetően az ultra-lemezek foglalási folyamata a korábbi szakaszban leírtak szerint működhet, vagy részletesebben szabályozhatja és terjesztheti a teljesítményt.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Egy tábla képe, amely a &quot;ReadOnly&quot; vagy &quot;olvasási/írási&quot; hozzáférést ábrázolja a foglalási tulajdonos, a regisztrált és mások számára.":::

## <a name="performance-throttles"></a>Teljesítmény-szabályozások

### <a name="premium-ssd-performance-throttles"></a>prémium SSD teljesítmény szabályozása

A prémium SSD esetében a lemez IOPS és átviteli sebessége rögzített, például egy P30 IOPS, 5000. Ez az érték marad, függetlenül attól, hogy a lemez 2 virtuális gép vagy 5 virtuális gép között van-e megosztva. A lemezre vonatkozó korlátok egyetlen virtuális gépről érhetők el, vagy két vagy több virtuális gép között oszthatók fel. 

### <a name="ultra-disk-performance-throttles"></a>Ultravékony lemezek teljesítményének szabályozása

Az ultra-lemezek egyedi képességgel rendelkeznek, amely lehetővé teszi a teljesítmény beállítását a módosítható attribútumok kiszűrésével, és lehetővé teszi a módosítását. Alapértelmezés szerint csak két módosítható attribútum létezik, de a megosztott Ultra-lemezek két további attribútummal rendelkeznek.


|Attribútum  |Leírás  |
|---------|---------|
|DiskIOPSReadWrite     |Az összes olyan virtuális gépen engedélyezett IOPS száma, amely a megosztási lemezt írási hozzáféréssel csatlakoztatja.         |
|DiskMBpsReadWrite     |A teljes átviteli sebesség (MB/s) engedélyezett az összes virtuális gépen, amely a megosztott lemezt írási hozzáféréssel csatlakoztatja.         |
|DiskIOPSReadOnly*     |Az összes olyan virtuális gépen engedélyezett IOPS teljes száma, amely a megosztott lemezt csatlakoztatja `ReadOnly` .         |
|DiskMBpsReadOnly*     |A teljes átviteli sebesség (MB/s) engedélyezett az összes olyan virtuális gépen, amely a megosztott lemezt csatlakoztatja `ReadOnly` .         |

\* Csak a megosztott Ultra-lemezekre vonatkozik

A következő képletek azt mutatják be, hogyan lehet beállítani a teljesítmény attribútumokat, mivel azok felhasználó által módosítható:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - 300 IOPS/GiB IOPS korlátja, legfeljebb 160K IOPS
    - Legalább 100 IOPS
    - A DiskIOPSReadWrite + DiskIOPSReadOnly legalább 2 IOPS/GiB
- DiskMBpsRead írási/DiskMBpsReadOnly:
    - Egy lemez átviteli korlátja minden kiépített IOPS esetében 256 KiB/s, lemezenként legfeljebb 2000 MB/s-onként
    - Minden egyes kiépített IOPS esetében a minimális garantált átviteli sebesség 4KiB/s, a teljes alapértéknek legalább 1 MBps-nek kell lennie

#### <a name="examples"></a>Példák

Az alábbi példák néhány forgatókönyvet mutatnak be, amelyek bemutatják, hogyan működhet a szabályozás a megosztott Ultra-lemezekkel, pontosabban.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Két csomópontos fürt fürt megosztott kötetei használatával

A következő példa egy, a fürtözött megosztott köteteket használó 2 csomópontos WSFC. Ezzel a konfigurációval mindkét virtuális gépnek egyidejű írási hozzáférése van a lemezhez, ami azt eredményezi, `ReadWrite` hogy a szabályozás a két virtuális gép között oszlik el, és a `ReadOnly` szabályozás nincs használatban.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Két csomópontos CSV-példa":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Két csomópontos fürt fürt megosztási kötetei nélkül

A következő példa egy olyan 2 csomópontos WSFC mutat be, amely nem használ fürtözött megosztott köteteket. Ezzel a konfigurációval csak egy virtuális gép rendelkezik írási hozzáféréssel a lemezhez. Ez azt eredményezi, `ReadWrite` hogy a szabályozás kizárólag az elsődleges virtuális gép esetében használatos, és a `ReadOnly` szabályozás csak a másodlagosnál használatos.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV-fájl két csomópontja nem rendelkezik a CSV-vel – példa":::

##### <a name="four-node-linux-cluster"></a>Négy csomópontos Linux-fürt

A következő példa egy 4 csomópontos linuxos fürtöt mutat be egyetlen író és három kibővíthető olvasóval. Ezzel a konfigurációval csak egy virtuális gép rendelkezik írási hozzáféréssel a lemezhez. Ez azt eredményezi, hogy a `ReadWrite` szabályozás kizárólag az elsődleges virtuális gép esetében használatos, és a `ReadOnly` másodlagos virtuális gépek által feldarabolt szabályozás.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Négy csomópontos Ultra-szabályozási példa":::

#### <a name="ultra-pricing"></a>Ultra díjszabás

Az ultra Shared Disks díjszabása a kiépített kapacitás, a teljes kiépített IOPS (diskIOPSReadWrite + diskIOPSReadOnly) és a kiépített átviteli sebesség összesen (diskMBpsReadWrite + diskMBpsReadOnly) alapján történik. A további virtuálisgép-csatlakoztatások esetében nem számítunk fel külön díjat. Például a következő konfigurációval rendelkező, ultra megosztott lemez (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) a 1024 GiB, az 10100 IOPS és az 601 MBps díj ellenében történik, függetlenül attól, hogy két virtuális géphez vagy öt virtuális géphez van-e csatlakoztatva.