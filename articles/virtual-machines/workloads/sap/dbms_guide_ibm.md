---
title: IBM Db2 Azure Virtual Machines DBMS üzembe helyezése SAP számítási feladatokhoz | Microsoft Docs
description: IBM Db2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, Db2, SAP, IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/20/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e9030558779be3e417383f9f32612ee3e834a1c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788078"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>IBM Db2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz

A Microsoft Azure az IBM Db2-ben futó, Linux, UNIX és Windows (LUW) rendszereken futó meglévő SAP-alkalmazásokat Azure-beli virtuális gépekre. Az SAP on IBM Db2 for LUW segítségével a rendszergazdák és a fejlesztők továbbra is a helyszínen elérhető fejlesztési és felügyeleti eszközöket használhatják.
Az SAP Business Suite AZ IBM Db2 for LUW-on való futtatásával kapcsolatos általános információk a következő oldalon találhatók: SAP Community Network (SCN). <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>

Az Sap on Db2 for LUW Azure-on való használatával kapcsolatos további információkért és frissítésekért lásd: SAP Note [2233094]. 

Az azure-beli SAP számítási feladatokkal kapcsolatos különböző cikkek jelentek meg.  Javasoljuk, hogy az [Azure-beli SAP](./get-started.md) számítási feladatból indulva – Első lépések, majd válassza ki az érdeklődési területet

A következő SAP-megjegyzések a Azure-beli SAP területével kapcsolatos kérdésekhez kapcsolódnak:

| Megjegyzés száma |Cím |
| --- |--- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure-beli virtuálisgép-típusok |
| [2015553] |SAP on Microsoft Azure: Támogatási előfeltételek |
| [1999351] |Továbbfejlesztett Azure-monitorozás hibaelhárítása SAP-hez |
| [2178632] |Fő monitorozási metrikák az SAP on Microsoft Azure |
| [1409604] |Virtualizálás Windows rendszeren: Továbbfejlesztett figyelés |
| [2191498] |SAP Linuxon az Azure-ral: Továbbfejlesztett monitorozás |
| [2233094] |DB6: SAP-alkalmazások az Azure-ban AZ IBM DB2 for Linux, UNIX és Windows használatával – További információk |
| [2243692] |Linux on Microsoft Azure (IaaS) virtuális gépen: SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7.x: Telepítés és frissítés |
| [1597355] |Helycserére vonatkozó javaslat Linux esetén |

A dokumentum pr-read dokumentumában olvassa el a Megfontolandó szempontok az [Azure Virtual Machines DBMS](dbms_guide_general.md) üzembe helyezéséhez SAP számítási feladatokhoz és egyéb útmutatókat az [Azure-dokumentáció SAP számítási feladataihoz.](./get-started.md) 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM Db2 for Linux, UNIX és Windows verziótámogatás
Az SAP on IBM Db2 for LUW Microsoft Azure Virtual Machine Servicesen a Db2 10.5-ös verziójától támogatott.

A támogatott SAP-termékekről és az Azure-beli virtuálisgép-típusokról az SAP [1928533-as megjegyzésében található további információ.]

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM Db2 for Linux, UNIX és Windows Configuration Guidelines for SAP Installations in Azure VMs (IBM Db2 for Linux, UNIX és Windows konfigurációs útmutató Azure-beli virtuális gépeken történő SAP-telepítéshez)
### <a name="storage-configuration"></a>Tárolási konfiguráció
Az SAP számítási feladatokhoz használható Azure-tárolótípusok áttekintéséhez tekintse meg az Azure Storage types for SAP workload [(Azure-tárolótípusok SAP](./planning-guide-storage.md) számítási feladatokhoz) Minden adatbázisfájlt az Azure Block Storage csatlakoztatott lemezén kell tárolni (Windows: NTFS, Linux: xfs vagy ext3). Az adatbázisfájlok nem támogatnak bármilyen hálózati  meghajtót vagy távoli megosztást, például az alábbi Azure-szolgáltatásokat: 

* [Microsoft Azure File Service](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)

Az Azure Page BLOB Storage-on vagy Managed Disks-on alapuló lemezek használatával az SAP számítási feladatokhoz készült [Azure Virtual Machines DBMS üzembe](dbms_guide_general.md) helyezésének szempontjaival kapcsolatos utasítások a Db2 DBMS-sel üzemelő példányok esetében is érvényesek.

Ahogy azt a dokumentum általános részében már említettük, az Azure-lemezek IOPS-átviteli sebességére vonatkozó kvóták létezikak. A pontos kvóták a használt virtuálisgép-típustól függnek. A virtuálisgép-típusok és a kvóták listája itt [(Linux)][virtual-machines-sizes-linux] és [itt (Windows) található.][virtual-machines-sizes-windows]

Amíg a lemezenkénti aktuális IOPS-kvóta elegendő, az összes adatbázisfájl tárolható egyetlen csatlakoztatott lemezen. Míg mindig külön kell elkülönítenünk az adatfájlokat és a tranzakciós naplófájlokat különböző lemezeken/VHD-ken.

A teljesítménnyel kapcsolatos szempontokért tekintse meg az SAP telepítési útmutatóiban található "Az adatbázis-könyvtárak biztonsági és teljesítményével kapcsolatos szempontok" fejezetet is.

Alternatív megoldásként használhatja a Windows-tárolókészleteket (csak a Windows Server 2012-ben és újabb verziókban érhető el) az SAP számítási feladatok [azure Virtual Machines DBMS-telepítésével](dbms_guide_general.md) kapcsolatos szempontok, Linux rendszeren az LVM vagy az mdadm alapján egy nagy logikai eszköz több lemezen való létrehozásához.

<!-- log_dir, sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

Azure M-sorozatú virtuális gépek esetén a tranzakciónaplókba való írás késése az Azure Premium Storage teljesítményéhez képest tényezőkkel csökkenthető az Azure írásgyorsító. Ezért a Db2 tranzakciónaplók kötetét írásgyorsító virtuális merevlemez(öke)hez kell üzembe helyeznie az Azure-beli virtuális merevlemez(öke)t. A részleteket a dokumentumban [olvashatja](../../how-to-enable-write-accelerator.md)írásgyorsító.

Az IBM Db2 LUW 11.5 megjelent a 4 KB-os szektorméret támogatása. Régebbi Db2-verziók esetében 512 bájtos szektorméretet kell használni. prémium SSD 4 KB-os natív lemezek 512 bájtos emulációval. Az ultralemez alapértelmezés szerint 4 KB-os szektorméretet használ. Az ultralemez létrehozása során engedélyezheti az 512 bájtos szektorméretet. A részletek [azure-beli ultralemezek használatával érhetők el.](../../disks-enable-ultra-ssd.md#deploy-an-ultra-disk---512-byte-sector-size) Ez az 512 bájtos szektorméret előfeltétele a 11.5-nél alacsonyabb IBM Db2 LUW-verzióknak.

A És könyvtárak Db2-tárolási útvonalaihoz Tárolókészleteket használó Windows rendszeren `log_dir` `sapdata` 512 KB-os fizikailemez-szektorméretet `saptmp` kell megadnia. Windows-tárolókészletek használata esetén manuálisan kell létrehoznia a tárolókészleteket a parancssori felületen a paraméter `-LogicalSectorSizeDefault` használatával. További információ: <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.


## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>Javaslat az IBM Db2 üzemelő példányának virtuális gépére és lemezszerkezetére

Az IBM Db2 for SAP NetWeaver Applications az SAP támogatási megjegyzés [1928533-as]megjegyzésében felsorolt bármely virtuálisgép-típus esetében támogatott.  Az IBM Db2-adatbázisok futtatásához ajánlott virtuálisgép-Esd_v4/Eas_v4/Es_v3 és M/M_v2-sorozat nagyméretű, több terabájtos adatbázisokhoz. Az IBM Db2 tranzakciós naplólemez írási teljesítménye az M sorozatú virtuális gépek engedélyezésével írásgyorsító. 

Az alábbiakban egy alapkonfigurációt követünk a különböző méretekhez és az SAP DB2-környezetekben való használathoz, kicsitől a nagyig. A lista az Azure Premium Storage-on alapul. Az Azure Ultra disk azonban teljes mértékben támogatott a Db2-ben, és használható is. Az ultralemez-konfiguráció meghatározásához használja a kapacitás, a teljesítmény-átvitel és az adatlok-átvitel IOPS értékeit. A /db2/ /log_dir IOPS körülbelül <SID> 5000-re korlátozva. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>Extra kis MÉRETŰ SAP-rendszer: adatbázisméret 50–200 GB: példa Megoldáskezelőre
| Virtuális gép neve /mérete |Db2 csatlakoztatási pont |Prémium szintű Azure-lemez |NR of Disks |IOPS |Átviteli sebesség [MB/s] |Méret [GB] |Burst IOPS |Burst Thr [GB] | Csíkméret | Gyorsítótárazás |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  ||  |
|vCPU: 4 |/db2/ <SID> /sapdata |P10 |2 |1,000  |200  |256  |7,000  |340  |256 KB |ReadOnly |
|RAM: 32 GiB |/db2/ <SID> /saptmp |P6 |1 |240  |50  |128  |3,500  |170  | ||
| |/db2/ <SID> /log_dir |P6 |2 |480  |100  |128  |7,000  |340  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3,500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>Kis MÉRETŰ SAP-rendszer: 200–750 GB adatbázisméret: small Business Suite
| Virtuális gép neve / mérete |Db2 csatlakoztatási pont |Prémium szintű Azure-lemez |NR of Disks |IOPS |Átviteli sebesség [MB/s] |Méret [GB] |Burst IOPS |Burst Thr [GB] | Csíkméret | Gyorsítótárazás |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU: 16 |/db2/ <SID> /sapdata |P15 |4 |4,400  |500  |1.024  |14,000  |680  |256 KB |ReadOnly |
|RAM: 128 GiB |/db2/ <SID> /saptmp |P6 |2 |480  |100  |128  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P15 |2 |2200  |250  |512  |7,000  |340  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3,500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>Közepes SAP-rendszer: adatbázisméret 500–1000 GB: small Business Suite
| Virtuális gép neve /mérete |Db2 csatlakoztatási pont |Prémium szintű Azure-lemez |NR of Disks |IOPS |Átviteli sebesség [MB/s] |Méret [GB] |Burst IOPS |Burst Thr [GB] | Csíkméret | Gyorsítótárazás |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU: 32 |/db2/ <SID> /sapdata |P30 |2 |10,000  |400  |2.048  |10,000  |400  |256 KB |ReadOnly |
|RAM: 256 GiB |/db2/ <SID> /saptmp |P10 |2 |1,000  |200  |256  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P20 |2 |4,600  |300  |1.024  |7,000  |340  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P15 |1 |1100  |125  |256  |3,500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>Nagyméretű SAP-rendszer: 750–2000 GB adatbázisméret: Business Suite
| Virtuális gép neve / mérete |Db2 csatlakoztatási pont |Prémium szintű Azure-lemez |NR of Disks |IOPS |Átviteli sebesség [MB/s] |Méret [GB] |Burst IOPS |Burst Thr [GB] | Csíkméret | Gyorsítótárazás |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3,500  |170  || |
|vCPU: 64 |/db2/ <SID> /sapdata |P30 |4 |20 000  |800  |4.096  |20 000  |800  |256 KB |ReadOnly |
|RAM: 504 GiB |/db2/ <SID> /saptmp |P15 |2 |2200  |250  |512  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P20 |4 |9,200  |600  |2.048  |14,000  |680  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P20 |1 |2300  |150  |512  |3,500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>Nagyméretű több terabájtos SAP-rendszer: 2 TB+-os adatbázisméret: Global Business Suite rendszer
| Virtuális gép neve /mérete |Db2 csatlakoztatási pont |Prémium szintű Azure-lemez |NR of Disks |IOPS |Átviteli sebesség [MB/s] |Méret [GB] |Burst IOPS |Burst Thr [GB] | Csíkméret | Gyorsítótárazás |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3,500  |170  || |
|vCPU: 128 |/db2/ <SID> /sapdata |P40 |4 |30,000  |1.000  |8.192  |30,000  |1.000  |256 KB |ReadOnly |
|RAM: 2048 GiB |/db2/ <SID> /saptmp |P20 |2 |4,600  |300  |1.024  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P30 |4 |20 000  |800  |4.096  |20 000  |800  |64 KB |WriteAccelerator |
| |/db2/ <SID> /offline_log_dir |P30 |1 |5000  |200  |1.024  |5000  |200  || |


### <a name="backuprestore"></a>Biztonsági mentés/visszaállítás
Az IBM Db2 for LUW biztonsági mentési/visszaállítási funkciói ugyanúgy támogatottak, mint a szabványos Windows Server operációs rendszerek és a Hyper-V esetében.

Győződjön meg arról, hogy érvényes adatbázis-biztonsági mentési stratégia van érvényben. 

Az operációs rendszer nélküli üzemelő példányok biztonsági mentési/visszaállítási teljesítménye attól függ, hogy hány kötet olvasható párhuzamosan, és mekkora lehet ezeknek a kötetnek az átviteli teljesítménye. Emellett a biztonsági mentés tömörítése által használt CPU-használat jelentős szerepet játszhat a legfeljebb nyolc processzorszálat is elenyő virtuális gépeken. Ezért a következőt feltételezhetjük:

* Minél kevesebb lemezt használnak az adatbáziseszközök tárolására, annál kisebb a teljes átviteli sebesség az olvasásban
* Minél kisebb a processzorszálak száma a virtuális gépen, annál súlyosabb a biztonsági mentés tömörítésének hatása
* Minél kevesebb cél (Stripe-könyvtárak, lemezek), amelyekre a biztonsági másolat írható, annál kisebb az átviteli sebesség

Az írni kívánt célok számának növeléséhez az igényeinek megfelelően két lehetőség közül választhat:

* A biztonsági mentési célkötet több lemezre való csíkozása az adott csíkozott kötet IOPS-átviteli sebességének növelése érdekében
* Több célkönyvtár használata a biztonsági másolat írásához

>[!NOTE]
>A Db2 Windows rendszeren nem támogatja a Windows VSS technológiát. Ennek eredményeképpen az Azure Backup Service alkalmazás-konzisztens virtuálisgép-biztonsági mentése nem hasznos a Db2 DBMS-ben üzembe helyezett virtuális gépekhez.

### <a name="high-availability-and-disaster-recovery"></a>Magas rendelkezésre állás és vészhelyreállítás

#### <a name="linux-pacemaker"></a>Linux Pacemaker

A Pacemaker db2 magas rendelkezésre állású vészhelyreállítása (HADR) támogatott. Az SLES és az RHEL operációs rendszerek is támogatottak. Ez a konfiguráció lehetővé teszi az IBM Db2 magas rendelkezésre állását az SAP-hez. Üzembe helyezési útmutatók:
* SLES: [Az IBM Db2 LUW](dbms-guide-ha-ibm.md) magas rendelkezésre állása azure-beli virtuális gépeken SUSE Linux Enterprise Server Pacemaker segítségével 
* RHEL: Az IBM Db2 LUW magas rendelkezésre állása azure-beli virtuális [gépeken a Red Hat Enterprise Linux Serveren](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Windows-fürtkiszolgáló

A Microsoft Cluster Server (MSCS) nem támogatott.

A DB2 magas rendelkezésre állású vészhelyreállítás (HADR) támogatott. Ha a HA-konfiguráció virtuális gépei működnek a névfeloldáson, az Azure-ban végzett beállítás nem különbözik a helyszíni telepítéstől. Nem ajánlott kizárólag IP-címfeloldásra támaszkodni.

Ne használjon Geo-Replication az adatbázislemezeket tároló tárfiókok esetében. További információkért lásd: Az [Azure Virtual Machines DBMS](dbms_guide_general.md)üzembe helyezésének szempontjai SAP számítási feladatokhoz. 

### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
A Windowson üzemelő Db2-példányok esetében erősen ajánlott a gyorsított hálózathasználat Azure-funkcióinak használata az Azure Gyorsított hálózathasználata [dokumentumban leírtak szerint.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Vegye figyelembe az Azure-beli virtuális Virtual Machines DBMS sap számítási feladatokhoz való üzembe helyezésével kapcsolatos [szempontokat is.](dbms_guide_general.md) 


### <a name="specifics-for-linux-deployments"></a>Linuxos üzemelő példányok részletesen
Ha a lemezenkénti aktuális IOPS-kvóta elegendő, az összes adatbázisfájl tárolható egyetlen lemezen. Míg mindig külön kell elkülönítenünk az adatfájlokat és a tranzakciós naplófájlokat különböző lemezeken/VHD-ken.

Ha egyetlen Azure-beli virtuális merevlemez IOPS- vagy I/O-átviteli sebessége nem elegendő, használhatja az LVM -et (Logikaikötet-kezelő) vagy az MDADM-et az SAP számítási feladatokhoz használt [Azure Virtual Machines DBMS](dbms_guide_general.md) üzembe helyezésével kapcsolatos szempontok dokumentumban leírtak szerint egy nagy logikai eszköz több lemezen történő létrehozásához.
Az sapdata és saptmp könyvtárak Db2 tárolási útvonalát tartalmazó lemezekhez 512 KB méretű fizikai lemez szektorméretet kell megadnia.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>Egyéb
Az összes többi általános terület, például az Azure rendelkezésre állási készletek vagy az SAP-monitorozás a Megfontolandó szempontok az [Azure Virtual Machines DBMS](dbms_guide_general.md) üzembe helyezéséhez SAP számítási feladatokhoz az IBM Database-adatbázissal együtt üzemelő virtuális gépek esetében dokumentumban leírtak szerint vonatkozik.

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692


## <a name="next-steps"></a>Következő lépések
Olvassa el a cikket 

- [Az Azure Virtual Machines DBMS SAP számítási feladatokhoz való üzembe helyezésével kapcsolatos szempontok](dbms_guide_general.md)



[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md