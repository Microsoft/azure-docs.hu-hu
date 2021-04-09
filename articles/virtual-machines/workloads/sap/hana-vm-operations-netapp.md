---
title: Azure-beli virtuális gépek ANF-konfigurációjának SAP HANAa | Microsoft Docs
description: Azure NetApp Files Storage-javaslatok a SAP HANAhoz.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, pillanatkép
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666622"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>NFS 4.1-es verziójú kötetek az SAP HANA-hoz készült Azure NetApp Filesban

A Azure NetApp Files olyan natív NFS-megosztásokat biztosít, amelyek a **/Hana/Shared**, a **/Hana/Data** és a **/Hana/log** kötetek esetében használhatók. A **/Hana/Data** -és **/HANA/log** -KÖTETEK ANF-alapú NFS-megosztások használata a v 4.1 NFS protokoll használatát igényli. A **/Hana/Data** és a **/Hana/log** kötetek használatakor a rendszer nem támogatja az NFS-protokoll v3-as verziójának használatát a ANF-megosztások létrehozásakor. 


> [!IMPORTANT]
> A Azure NetApp Fileson implementált NFS v3 protokoll **nem** támogatott a **/Hana/Data** és a **/Hana/log**. Az NFS 4,1 használata kötelező a **/Hana/Data** és a **/Hana/log** kötetek számára a funkcionális szempontból. Míg a **/Hana/Shared** -kötet esetében az NFS v3 vagy az NFS v 4.1 protokoll használható a funkcionális nézetből.

## <a name="important-considerations"></a>Fontos szempontok

Az SAP NetWeaver és SAP HANA Azure NetApp Filesének megfontolása során vegye figyelembe a következő fontos szempontokat:

- A minimális kapacitási készlet 4 TiB  
- A minimális kötet mérete 100 GiB
- Azure NetApp Files és minden olyan virtuális gép, amelyben Azure NetApp Files kötetek vannak csatlakoztatva, ugyanabban az Azure-Virtual Network vagy egymással azonos régióban lévő [virtuális hálózatokban](../../../virtual-network/virtual-network-peering-overview.md) kell lennie
- Fontos, hogy az Azure NetApp-tároló közelében üzembe helyezett virtuális gépek alacsony késéssel rendelkezzenek.  
- A kiválasztott virtuális hálózatnak rendelkeznie kell egy, a Azure NetApp Files delegált alhálózattal
- Győződjön meg arról, hogy az adatbázis-kiszolgáló és a ANF-kötet késése 1 ezredmásodpercnél kisebb
- Az Azure NetApp-kötetek átviteli sebessége a mennyiségi kvóta és a szolgáltatási szint függvénye, a [Azure NetApp Files szolgáltatási szintjén](../../../azure-netapp-files/azure-netapp-files-service-levels.md)dokumentálva. A HANA Azure NetApp-kötetek méretezése esetén győződjön meg arról, hogy az eredményül kapott átviteli sebesség megfelel a HANA rendszerkövetelményeinek.
- Próbálja meg "konszolidálni" a köteteket, hogy nagyobb mennyiségű teljesítményt érjenek el, például egy kötetet/sapmnt,/usr/SAP/Trans,... Ha lehetséges  
- Azure NetApp Files az [exportálási szabályzatot](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): szabályozhatja az engedélyezett ügyfeleket, a hozzáférési típust (olvasási&írás, csak olvasható stb.). 
- Azure NetApp Files a szolgáltatás még nem ismeri a zónát. Jelenleg Azure NetApp Files funkció nincs telepítve az Azure-régió összes rendelkezésre állási zónájában. Vegye figyelembe, hogy egyes Azure-régiókban lehetséges a késés következményei.   
- A <b>SID</b>adm felhasználói azonosítójának és a virtuális gépekhez tartozó csoport azonosítójának `sapsys` meg kell egyeznie Azure NetApp Filesban található konfigurációval. 

> [!IMPORTANT]
> SAP HANA munkaterhelések esetében a kis késleltetés kritikus fontosságú. A Microsoft képviselőjével együttműködve biztosíthatja, hogy a virtuális gépek és a Azure NetApp Files kötetek központi telepítése közel legyen.  

> [!IMPORTANT]
> Ha eltérés van a <b>SID</b>adm felhasználói azonosítója és a `sapsys` virtuális gép és az Azure NetApp-konfiguráció közötti csoportazonosító között, akkor az Azure NetApp köteteken található, a virtuális géphez csatlakoztatott fájlok engedélyei a következőként jelennek meg: `nobody` . Győződjön meg arról, hogy a megfelelő felhasználói azonosítót adta meg a <b>SID</b>adm számára, illetve a csoport azonosítóját `sapsys` , amikor [egy új rendszer](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) bekerül a Azure NetApp Filesba.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>HANA-adatbázis méretezése Azure NetApp Files

Az Azure NetApp-kötetek átviteli sebessége a kötet méretének és a szolgáltatási szintnek a függvénye, amely a [Azure NetApp Files szolgáltatási szintjén](../../../azure-netapp-files/azure-netapp-files-service-levels.md)van dokumentálva. 

Fontos, hogy tisztában legyen azzal, hogy mekkora a teljesítmény, és hogy van-e fizikai korlát a SVM (Storage virtuális gép) LIF (logikai interfész) számára.

Az alábbi táblázat azt mutatja be, hogy érdemes lehet egy nagyméretű "standard" kötetet létrehozni a biztonsági másolatok tárolásához, és nem érdemes olyan "Ultra" kötetet létrehozni, amely nagyobb, mint 12 TB, mert az egyetlen LIF fizikai sávszélesség-kapacitása túllépve. 

A LIF és az egyetlen Linux-munkamenet maximális átviteli sebessége 1,2 és 1,4 GB/s közé esik. Ha több átviteli sebességre van szüksége a/Hana/Data-hez, SAP HANA adatmennyiség-particionálással szalagos az I/O-tevékenységet az adatok újratöltése során, vagy HANA-visszaállítási pontok több, több NFS-megosztáson található HANA-adatfájl között. A HANA adatkötetek csíkozásáról a következő cikkekben olvashat bővebben:

- [A HANA rendszergazdai útmutatója](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog about SAP HANA – az adatkötetek particionálása](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP-Megjegyzés #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP-Megjegyzés #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Méret  | Átviteli sebesség standard | Átviteli sebesség (prémium) | Átviteli sebesség (Ultra) |
| --- | --- | --- | --- |
| 1 TB | 16 MB/s | 64 MB/s | 128 MB/s |
| 2 TB | 32 MB/s | 128 MB/s | 256 MB/s |
| 4 TB | 64 MB/s | 256 MB/s | 512 MB/s |
| 10 TB | 160 MB/s | 640 MB/s | 1 280 MB/s |
| 15 TB | 240 MB/s | 960 MB/s | 1 400 MB/s |
| 20 TB | 320 MB/s | 1 280 MB/s | 1 400 MB/s |
| 40 TB | 640 MB/s | 1 400 MB/s | 1 400 MB/s |

Fontos tisztában lenni azzal, hogy az adatbevitel ugyanarra az SSD-re történik a tárolási háttérben. A rendszer létrehozta a kapacitásépítési kvótát a környezet kezeléséhez.
A tárolási KPI-k az összes HANA-adatbázis méretével egyenlőek. Szinte minden esetben ez a feltételezés nem tükrözi a valóságot és az ügyfelek elvárásait. A HANA-rendszerek mérete nem feltétlenül jelenti azt, hogy egy kis rendszernek alacsony tárolási átviteli sebességre van szüksége, és egy nagy rendszernek nagy tárolási sebességre van szüksége. De általában nagyobb átviteli sebességre is számíthatunk nagyobb HANA-adatbázis-példányok esetén. Az SAP által a mögöttes hardverre vonatkozó méretezési szabályok következtében, például a nagyobb HANA-példányok több processzor-erőforrást és nagyobb párhuzamosságot biztosítanak olyan feladatokban, mint például az adatok betöltése egy példány újraindítása után. Ennek eredményeképpen a mennyiségi méreteket az ügyfelek elvárásainak és követelményeinek megfelelően kell elfogadni. És nem csak a tiszta kapacitásra vonatkozó követelmények vezérlik.

Az Azure-beli SAP-infrastruktúra megtervezése során tisztában kell lennie azzal, hogy az SAP által igényelt minimális tárterület-átviteli sebességre vonatkozó követelmények (a produkciós rendszerek esetében), amelyek az alábbi minimális átviteli sebességet fordítják le:

| Kötet típusa és I/O típusa | Az SAP által igényelt minimális KPI | Prémium szintű szolgáltatási szint | Ultra szolgáltatási szint |
| --- | --- | --- | --- |
| Napló kötetének írása | 250 MB/s | 4 TB | 2 TB |
| Adatmennyiség-írás | 250 MB/s | 4 TB | 2 TB |
| Adatmennyiség olvasása | 400 MB/s | 6,3 TB | 3,2 TB |

Mivel a rendszer mindhárom KPI-t igényli, a **/Hana/Data** -kötetet a minimális olvasási követelmények teljesítése érdekében a nagyobb kapacitás irányába kell méretezni.

A HANA-rendszerek esetében, amelyek nem igényelnek nagy sávszélességet, a ANF mérete kisebb lehet. Ha a HANA-rendszernek nagyobb átviteli sebességre van szüksége, a kötetet a kapacitás online átméretezésével lehet módosítani. Nincsenek KPI-k definiálva a biztonsági másolatok köteteihez. A biztonsági másolatok mennyiségének átviteli sebessége azonban elengedhetetlen a jól teljesítő környezetekhez. A log – és az adatmennyiség teljesítményét az ügyfelek elvárásainak megfelelően kell megtervezni.

> [!IMPORTANT]
> Az egyetlen NFS-köteten üzembe helyezett kapacitástól függetlenül az átviteli sebesség várhatóan a virtuális gép egy fogyasztója által kihasználható 1,2 – 1,4 GB/s sávszélességű sávszélességet eredményez. Ennek a ANF-ajánlat és a kapcsolódó Linux-munkamenetek az NFS-re vonatkozó korlátai mögötti architektúrájának kell megfelelnie. A teljesítmény-és átviteli sebesség a cikk [teljesítményének teljesítményteszt-tesztelési eredményei a Azure NetApp Files esetében](../../../azure-netapp-files/performance-benchmarks-linux.md) egy megosztott NFS-köteten lettek elvégezve, több ügyfél virtuális géppel és több munkamenet eredményeként. Ez a forgatókönyv különbözik az SAP-ben mérhető forgatókönyvtől. Az átviteli sebességet egyetlen virtuális gépről egy NFS-kötetre mérjük. ANF-on üzemeltetve.

Az adatokhoz és a naplóhoz tartozó minimális teljesítménybeli követelmények teljesítéséhez, valamint a **/Hana/Shared** vonatkozó irányelvek alapján az ajánlott méretek a következőképpen néznek ki:

| Kötet | Méret<br /> Premium Storagei szintű | Méret<br /> Ultra Storage-rétegek | Támogatott NFS-protokoll |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/Shared felskálázás | Min (1 TB, 1 x RAM)  | Min (1 TB, 1 x RAM) | v3 vagy v 4.1 |
| /Hana/Shared kibővíthető | 1 x RAM munkavégző csomópont<br /> /4 feldolgozó csomópont  | 1 x RAM munkavégző csomópont<br /> /4 feldolgozó csomópont  | v3 vagy v 4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 vagy v 4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 vagy v 4.1 |

Az NFS v 4.1 minden kötet esetében kifejezetten ajánlott

A biztonsági mentési kötetek mérete becslések. A pontos követelményeket a számítási feladatok és a műveleti folyamatok alapján kell meghatározni. Biztonsági mentések esetén több kötetet is összevonhat a különböző SAP HANA példányok számára egy (vagy két) nagyobb kötetre, ami alacsonyabb szolgáltatási szinttel rendelkezhet a ANF.

> [!NOTE]
> Az ebben a dokumentumban ismertetett Azure NetApp Files és méretezési javaslatok az SAP kifejezett minimális követelményeit célozzák meg az infrastruktúra-szolgáltatók felé. A valós ügyfelek központi telepítései és munkaterhelési forgatókönyvek esetében nem lehet elég. Ezeket a javaslatokat kiindulási pontként és alkalmazkodva használhatja az adott számítási feladatra vonatkozó követelmények alapján.  

Ezért érdemes lehet a ANF-kötetek hasonló átviteli sebességét üzembe helyezni az ultra Disk Storage-ban felsoroltak szerint. Azt is vegye figyelembe, hogy a kötetek mérete a különböző virtuálisgép-SKU-lemezeken a már megadottnál is elérhető.

> [!TIP]
> A Azure NetApp Files kötetek átméretezése dinamikusan, a kötetek szükségessége nélkül, a virtuális gépek leállításával `unmount` vagy a SAP HANA leállításával végezhető el. Ez lehetővé teszi a rugalmasságot, hogy megfeleljen az alkalmazás elvárásainak és a várhatóan nem várt átviteli igényeknek.

A ANF szolgáltatásban üzemeltetett, a készenléti csomóponttal rendelkező SAP HANA kibővített konfiguráció üzembe helyezéséről szóló dokumentáció az [Azure-beli virtuális gépek készenléti csomópontján SAP HANA kibővítve, az Azure NetApp Files on SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)használatával érhető el.


## <a name="availability"></a>Rendelkezésre állás
A ANF rendszerfrissítéseket és frissítéseket az ügyfél-környezet befolyásolása nélkül alkalmazza a rendszer. A megadott [SLA 99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Kötetek és IP-címek és kapacitási készletek
A ANF fontos tisztában lennie azzal, hogy az alapul szolgáló infrastruktúra hogyan épül fel. A kapacitási készlet csak struktúra, amely egyszerűbbé teszi a ANF számlázási modell létrehozását. A kapacitási készlet nem rendelkezik fizikai kapcsolattal a mögöttes infrastruktúrával. Ha létrehoz egy kapacitási készletet, akkor a rendszer csak olyan rendszerhéjt hoz létre, amely felszámítható, és nem több. Kötet létrehozásakor az első SVM (tárolási virtuális gép) több NetApp rendszerű fürtön jön létre. Ehhez a SVM egyetlen IP-cím jön létre a kötet eléréséhez. Ha több kötetet hoz létre, a rendszer az összes kötetet ezen a SVM osztja el ezen a többvezérlős NetApp-fürtön keresztül. Még akkor is, ha csak egyetlen IP-címet kap, az adatforgalom több vezérlőn keresztül történik. A ANF olyan logikával rendelkezik, amely automatikusan elosztja az ügyfelek számítási feladatait, ha a konfigurált tárterület kötetei vagy/vagy kapacitása eléri a belső, előre meghatározott szintet. Ilyen esetekben észreveheti, hogy egy új IP-cím lesz hozzárendelve a kötetek eléréséhez.

##<a name="log-volume-and-log-backup-volume"></a>A kötet és a napló biztonsági mentési kötetének naplózása
A "naplózási kötet" (**/Hana/log**) az online visszaállítási napló írásához használatos. Ezért ebben a kötetben nyitott fájlok találhatók, és nincs értelme pillanatképet készíteni a kötetről. Az online visszaállítási naplófájlok archiválása vagy biztonsági mentése a napló biztonsági másolati kötetére, miután megtelt az online ismétlési fájl, vagy egy visszaállítási napló biztonsági mentést hajt végre. Az ésszerű biztonsági mentési teljesítmény biztosításához a napló biztonsági mentési kötetének jó átviteli sebességre van szüksége. A tárolási költségek optimalizálása érdekében érdemes lehet összevonni több HANA-példány biztonsági másolatát. Így több HANA-példány ugyanazt a kötetet használja, és a biztonsági másolatokat különböző címtárakba írja. A konszolidáció használatával nagyobb átviteli sebességet érhet el, mivel a kötetnek nagyobbnak kell lennie. 

Ugyanez vonatkozik arra a kötetre, amelyet a teljes HANA-adatbázis biztonsági mentésének írására használ.  
 

## <a name="backup"></a>Backup
A streaming Backups és az Azure back Service biztonsági mentése mellett SAP HANA adatbázisokat az [Virtual Machines Azure-beli SAP HANA biztonsági mentési útmutatója](./sap-hana-backup-guide.md)című cikkben leírtak szerint Azure NetApp Files megnyithatja a tár-alapú Pillanatképek biztonsági mentésének lehetőségét. 

SAP HANA a következőket támogatja:

- Storage-alapú Pillanatképek biztonsági mentései SAP HANA 1,0 SPS7
- A Storage-alapú Snapshot Backup támogatja a többadatbázisos tároló (MDC) HANA-környezeteit SAP HANA 2,0 SPS4


A tárolási alapú Pillanatképek biztonsági mentései egy egyszerű, négy lépésből álló eljárás, 
1. HANA-(belső) adatbázis-pillanatkép létrehozása – egy tevékenység, amelyet vagy eszközöket kell végrehajtania 
1. SAP HANA az adatoknak az adattárakba való írásával konzisztens állapotot hoz létre a tárolón – a Hana-pillanatkép létrehozása miatt ezt a lépést hajtja végre.
1. Hozzon létre egy pillanatképet a **/Hana/Data** köteten a tárolón – egy lépést, melyet vagy eszközöket kell végrehajtania. Nem kell elvégeznie a pillanatképet a **/Hana/log** köteten
1. Törölje a HANA (belső) adatbázis pillanatképét, és folytassa a normál működést – ez a lépés, melyet vagy eszközöket kell végrehajtania

> [!WARNING]
> Ha hiányzik az utolsó lépés, vagy az utolsó lépés végrehajtása sikertelen, akkor a SAP HANA memóriája is jelentős hatással van, és leállt SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA ANF pillanatképének biztonsági mentése](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![ANF pillanatkép biztonsági mentése a SAP HANA part2-hoz](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Ez a pillanatkép-biztonsági mentési eljárás többféle módon kezelhető különféle eszközök használatával. Az egyik példa a GitHubon elérhető "ntaphana_azure. a" Python [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) -parancsfájl, amely a "jelenleg is", karbantartás vagy támogatás nélkül érhető el.



> [!CAUTION]
> A pillanatkép önmagában nem védett biztonsági másolat, mert ugyanazon a fizikai tárolón található, mint az imént készített pillanatkép. Minden nap legalább egy pillanatképet egy másik helyre kötelező "védelemmel ellátni". Ez ugyanabban a környezetben, egy távoli Azure-régióban vagy az Azure Blob Storage-ban is elvégezhető.


A CommVault biztonsági mentési termékek felhasználói számára a második lehetőség a CommVault IntelliSnap V. 11.21 és újabb verzió. A CommVault ezen vagy újabb verziói Azure NetApp Files támogatást nyújtanak. A [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) cikk további információkat tartalmaz.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>A pillanatkép biztonsági mentése az Azure Blob Storage használatával
Az Azure Blob Storage-ba történő biztonsági mentés költséghatékony és gyors módszer a ANF-alapú HANA-adatbázis tárolási pillanatképének mentésére. A pillanatképek Azure Blob Storage-tárolóba való mentéséhez a azcopy eszköz előnyben részesített. Töltse le az eszköz legújabb verzióját, és telepítse azt például abban a bin könyvtárban, ahol a GitHubról származó Python-szkript telepítve van.
Töltse le a legújabb azcopy eszközt:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

A legfejlettebb funkció a SZINKRONIZÁLÁSi lehetőség. Ha a szinkronizálás lehetőséget használja, a azcopy megtartja a forrást és a cél könyvtárat. A **--delete-Destination** paraméter használata fontos. A paraméter nélkül a azcopy nem törli a célhelyen lévő fájlokat, és a célhelyen a hely kihasználtsága növekedni fog. Hozzon létre egy blokk BLOB-tárolót az Azure Storage-fiókban. Ezután hozza létre a blob-tároló SAS-kulcsát, és szinkronizálja a pillanatkép-mappát az Azure Blob-tárolóval.

Ha például egy napi pillanatképet szinkronizálni kell az Azure Blob-tárolóval az adatok védelme érdekében. És csak az egyik pillanatképet kell megőrizni, az alábbi parancsot lehet használni.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikket:

- [SAP HANA magas rendelkezésre állás az Azure Virtual Machines szolgáltatásban](./sap-hana-availability-overview.md)