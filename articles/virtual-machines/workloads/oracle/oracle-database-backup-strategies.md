---
title: Oracle Database az Azure-ban Virtual Machines biztonsági mentési stratégiák
description: Az Oracle-adatbázisok Azure-beli linuxos virtuálisgép-környezetben történő biztonsági mentésének lehetőségei.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: adb455e7a49ead1e8ba8e9845a4e56542528d0b1
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063857"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database az Azure Linux virtuális gépek biztonsági mentési stratégiái

Az adatbázis biztonsági mentései védik az adatbázist a tárolási összetevő meghibásodása és az adatközpont meghibásodása miatti adatvesztéssel szemben. Az emberi hibák helyreállítására is használhatók, és az adatbázisok fejlesztési vagy tesztelési célú klónozása is lehetséges. 

Az Azure-ban, mivel az összes tárterület nagy mennyiségű, és egy vagy több lemez elvesztése nem eredményez adatbázis-kimaradást, a biztonsági mentések leggyakrabban az emberi hibák elleni védelemre, a klónozási műveletek vagy az adatmegőrzési célokra szolgálnak. Emellett olyan védelmet is jelentenek a regionális kimaradások ellen, amikor a vész-helyreállítási technológia, például a DataGuard konfigurálása nincs használatban. Ebben az esetben a biztonsági mentéseket a földrajzilag redundáns replikáció használatával különböző Azure-régiókban kell tárolni, hogy az elsődleges adatbázis-régión kívül is elérhető legyen.

## <a name="azure-storage"></a>Azure Storage 

Az [Azure Storage szolgáltatás](../../../storage/common/storage-introduction.md) a Microsoft felhőalapú tárolási megoldása a modern adattárolási forgatókönyvekhez. Az Azure Storage számos szolgáltatást kínál, amelyek segítségével külső tárolókat csatlakoztathat az Azure Linux virtuális géphez, amely az Oracle-adatbázisok biztonsági mentési adathordozójának megfelelő. A biztonsági mentési vagy visszaállítási művelet elindításához, illetve az Azure Storage szolgáltatásba való másolásához a biztonsági mentési eszköz, például az Oracle Oláh Anna szükséges.
 
Az Azure Storage szolgáltatás a következő előnyöket kínálja:

-  Tartós és magas rendelkezésre állású. A redundancia biztosítja az adatok biztonságát átmeneti hardverhiba esetén. Az összes tárterület alapértelmezés szerint háromszoros tükrözött. Dönthet úgy is, hogy az adatközpontokban vagy földrajzi régiókban lévő adatok replikálására is lehetőséget nyújt a helyi katasztrófák vagy a természeti katasztrófák elleni további védelem érdekében. Az így replikált adatok magas rendelkezésre állásúak maradnak váratlan meghibásodás esetén is.

-  Biztonságos. Az Azure Storage-fiókba írt összes adathalmazt a szolgáltatás titkosítja. Az Azure Storage használatával részletesen szabályozhatja, hogy ki férhet hozzá az adatokhoz.

-  Méretezhető. Az Azure Storage nagymértékben skálázható, hogy megfeleljen a mai alkalmazások adattárolási és teljesítménybeli igényeinek.

-  Felügyelt. Az Azure kezeli a hardveres karbantartást, a frissítéseket és a kritikus problémákat.

-  Hozzáférhető. Az Azure Storage tárterületen lévő adatok a világon bárhonnan elérhetők HTTP- vagy HTTPS-kapcsolaton keresztül. A Microsoft számos nyelven biztosít ügyféloldali kódtárakat az Azure Storage-hoz, többek között a .NET, a Java, a Node.js, a Python, a PHP, a Ruby, a Go és mások számára, valamint egy érett REST API. Az Azure Storage támogatja az Azure PowerShell és az Azure CLI szkriptjeit. Az Azure Portal és az Azure Storage Explorer emellett egyszerű vizuális megoldásokat nyújt az adatok használatához.

Az Azure Storage platform a következő adatszolgáltatásokat tartalmazza, amelyek alkalmasak az Oracle-adatbázis biztonsági mentési adathordozóként való használatára:

-  Azure Blobs: Nagymértékben skálázható objektumtároló szöveges és bináris adatokhoz. A Data Lake Storage Gen2on keresztül big data Analitika támogatását is magában foglalja.

-  Azure Files: felügyelt fájlmegosztás a Felhőbeli vagy helyszíni központi telepítésekhez.

-  Azure-lemezek: az Azure-beli virtuális gépek blokkolási szintű tárolási kötetei.

### <a name="cross-regional-storage-mounting"></a>Régiók közötti tárolás – csatlakoztatás

A biztonsági mentési tár régiók közötti elérésének képessége a BCDR fontos aspektusa, és hasznos lehet az adatbázisok különböző földrajzi régiókba történő klónozásához. Az Azure Cloud Storage öt [redundancia](../../../storage/common/storage-redundancy.md) -szintet biztosít
- Helyileg redundáns tárolás [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) , ahol az adatai háromszor replikálódnak az elsődleges régióban lévő egyetlen fizikai helyen belül.  
- A Zone redundáns tárolás [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) , ahol az adatait az elsődleges régióban lévő LRS védi, és az elsődleges régióban lévő három rendelkezésre állási zónában (az) keresztül szinkronban van replikálva. Minden az is LRS védett. 
- Geo-Redundant Storage [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) , ahol az adatait az elsődleges régióban lévő LRS védi, és aszinkron módon replikálható egy másodlagos régióba, ahol a LRS is védi.
- A Geo-Zone-redundáns tárolás [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) az összes Azure-beli rendelkezésre állási zónában szinkron módon másolja az adatait az elsődleges régióban az ZRS használatával. Ezután aszinkron módon másolja át az adatokat a másodlagos régió egy fizikai helyére. A LRS-ben az összes helyen található adatvédelmet is védi. 
- Olvasási hozzáférés Geo-Redundant Storage [(ra-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) és olvasási hozzáférés geo-Zone-redundáns tárolás [(ra-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) lehetővé teszi a csak olvasási hozzáférést a másodlagos régióba replikált adatszolgáltatásokhoz. 

#### <a name="blob-storage-and-file-storage"></a>BLOB Storage és file Storage

Ha Azure Filest használ az SMB vagy NFS 4,1 (előzetes verzió) protokollok használatával a biztonsági mentési tárolóként való csatlakoztatáshoz, vegye figyelembe, hogy a Azure Files nem támogatja az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS) és az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS). 

Továbbá, ha a biztonsági mentési tár követelménye nagyobb, mint 5 TiB Azure Files a [nagyméretű fájlmegosztás funkciót](../../../storage/files/storage-files-planning.md) igényli, amely nem támogatja a GRS vagy a GZRS redundanciát – csak a LRS-et támogatja. 

Az NFS 3,0 (előzetes verzió) protokoll használatával csatlakoztatott Azure Blob jelenleg csak a LRS és a ZRS redundanciát támogatja.  

Az Azure Blob bármely redundancia beállítással konfigurálva lehet a Blobfuse használatával.

#### <a name="recovery-services-vault"></a>Recovery Services-tároló 

A Recovery Services-tároló egy olyan felügyeleti entitás, amely eltárolja az idők során létrehozott biztonsági mentéseket, és egy felületet biztosít a biztonsági mentésekkel kapcsolatos műveletek végrehajtásához. Ezek közé tartoznak az igény szerinti biztonsági másolatok készítése, a visszaállítások végrehajtása, illetve a biztonsági mentési szabályzatok létrehozása.
A Azure Backup automatikusan kezeli a tároló tárterületét. Meg kell adnia, hogy a rendszer hogyan replikálja a tárterületet a létrehozáskor. A tárolóban lévő elemek védelme után nem módosítható. Regionális redundancia esetén válassza a Geo-redundáns beállítást.

Ha egy másodlagos, Azure-beli [párosított régióban](../../../best-practices-availability-paired-regions.md) kíván visszaállítani, engedélyezze a [régiók közötti visszaállítás](../../../backup/backup-create-rs-vault.md) funkciót. Ha a régiók közötti visszaállítás engedélyezve van, a biztonsági mentési tár átkerül a GRS-ből a Read-Access geo-redundáns tárolóba (RA-GRS). 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Az Azure Blob Storage egy nagy kapacitású, tartós és biztonságos felhőalapú tárolási szolgáltatás, amely nagy mennyiségű strukturálatlan adat tárolására szolgál, és ideális adathordozó a Oracle Database biztonsági mentésekhez. Az Azure Blob Storage az NFS v 3.0 protokoll vagy a Blobfuse (Linux FUSE) használatával csatlakoztatható az Azure Linux rendszerű virtuális gépekhez.

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

A [Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) egy nyílt forráskódú projekt, amely az Azure Blob Storage által támogatott virtuális fájlrendszerek biztosítására lett kifejlesztve. A libfuse nyílt forráskódú függvénytárával kommunikál a Linux FUSE kernel modullal, és a Azure Storage Blob REST API-k használatával valósítja meg a fájlrendszeri műveleteket. Az Blobfuse jelenleg Ubuntu és CentOS/RedHat disztribúciók esetében érhető el. A Kubernetes a [CSI-illesztőprogram](https://github.com/kubernetes-sigs/blob-csi-driver)használatával is elérhető. 

Noha az összes Azure-régióban mindenütt elérhető, és az összes Storage-fiók típussal működik, beleértve az általános célú v1/v2-t és a Azure Data Lake Store Gen2-t, a Blobfuse által kínált teljesítmény kevesebb, mint például az SMB vagy az NFS protokollt. Az adatbázis biztonsági mentési adathordozójának használatára az Azure Blob Storage csatlakoztatásához az SMB vagy az [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) protokoll használatát javasoljuk. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (előzetes verzió)

A hálózati fájlrendszer (NFS) v 3.0 protokoll Azure-támogatása már előzetes verzióban érhető el. Az [NFS](../../../storage/blobs/network-file-system-protocol-support.md) -támogatás lehetővé teszi, hogy a Windows-és Linux-ügyfelek blob Storage-tárolót csatlakoztassanak egy Azure-beli virtuális géphez. 

Az NFS 3,0 nyilvános előzetes verziója támogatja a standard szintű GPV2 rendelkező Storage-fiókokat a következő régiókban: 
- Kelet-Ausztrália
- Dél-Korea középső régiója
- USA déli középső régiója. 

A hálózati biztonság biztosításához az NFS-csatlakoztatáshoz használt Storage-fióknak egy VNet belül kell szerepelnie. A Azure Active Directory (AD) biztonság és a hozzáférés-vezérlési listák (ACL-ek) még nem támogatottak olyan fiókokban, amelyeken engedélyezve van az NFS 3,0 protokoll támogatása.

### <a name="azure-files"></a>Azure Files

A [Azure Files](../../../storage/files/storage-files-introduction.md) egy felhőalapú, teljes körűen felügyelt elosztott fájlrendszer, amely a helyszíni vagy felhőalapú Windows-, Linux-vagy MacOS-alapú ügyfelekhez csatlakoztatható.

A Azure Files teljes körűen felügyelt, többplatformos fájlmegosztást biztosít a felhőben, amelyek elérhetők a Server Message Block (SMB) protokoll és az NFS protokoll (előzetes verzió) használatával. Azure Files jelenleg nem támogatja a többprotokollos hozzáférést, így a megosztás csak NFS-megosztás vagy SMB-megosztás lehet. Azt javasoljuk, hogy az Azure-fájlmegosztás létrehozása előtt határozza meg, hogy melyik protokoll felel meg legjobban az igényeinek.

Az Azure-fájlmegosztás Azure Backup a Recovery Services-tárolón keresztül is védhető, így további védelmi réteget biztosít az Oracle Oláh Anna biztonsági mentései számára.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v 4.1 (előzetes verzió)

Az Azure-fájlmegosztás Linux-disztribúciókban is csatlakoztatható a hálózati fájlrendszer (NFS) v 4.1 protokolljának használatával. Az előzetes verzióban számos korlátozás vonatkozik a támogatott funkciókra, amelyek dokumentálva vannak [itt](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

Az előzetes verzióban Azure Files NFS v 4.1 a következő [régiókban](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)is korlátozott:
- USA keleti régiója (LRS és ZRS)
- USA 2. keleti régiója
- USA 2. nyugati régiója
- Nyugat-Európa
- Délkelet-Ázsia
- Az Egyesült Királyság déli régiója
- Kelet-Ausztrália

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3,0

Az Azure-fájlmegosztás az SMB kernel-ügyféllel is csatlakoztatható Linux-disztribúciókban. A Linux-disztribúciókban elérhető Common Internet File System (CIFS) protokoll az SMB dialektusa. Ha Linux rendszerű virtuális gépeken csatlakoztat Azure Files SMB-t, akkor a rendszer CIFS típusú fájlrendszerként csatlakoztatja, és a CIFS-csomagot telepíteni kell. 

Azure Files SMB általánosan elérhető az összes Azure-régióban, és ugyanazokat a teljesítmény-jellemzőket jeleníti meg, mint az NFS v 3.0 és a v 4.1 protokoll, ezért jelenleg az ajánlott módszer a Backup Storage-adathordozók Azure-beli linuxos virtuális gépekre való biztosításához.  

Az SMB két támogatott verziója érhető el, az SMB 2,1 és az SMB 3,0, és az utóbbi ajánlott, mivel támogatja a titkosítást az átvitel során. A különböző Linux kernel-verziók azonban eltérő támogatást biztosítanak az SMB 2,1 és a 3,0-es verzióhoz, ezért érdemes ellenőrizni a táblázatot, hogy az alkalmazás támogassa az SMB [3,0-et](../../../storage/files/storage-how-to-use-files-linux.md) . 

Mivel a Azure Files többfelhasználós fájlmegosztás szolgáltatásként lett kialakítva, bizonyos jellemzőkkel kell rendelkeznie, hogy jobban megfeleljenek a biztonsági mentési adathordozóknak. A gyorsítótárazás kikapcsolása és a létrehozott fájlokhoz tartozó felhasználói és csoportfiókok beállítása ajánlott.

## <a name="azure-netapp-files"></a>Azure NetApp Files

A [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) szolgáltatás egy teljes tárolási megoldás az Oracle-adatbázisok számára az Azure-beli virtuális gépeken. Nagyvállalati szintű, nagy teljesítményű, mért file Storage-ra épülő, a számítási feladatok bármely típusát támogatja, és alapértelmezés szerint magas rendelkezésre állású. Az Oracle Direct NFS-(dNFS-) illesztőprogrammal együtt Azure NetApp Files egy nagyon optimalizált tárolási réteget biztosít a Oracle Database számára.

A Azure NetApp Files hatékony, tárolási alapú pillanatképeket készít a mögöttes tárolási rendszeren, amely az írási (sor) mechanizmuson keresztüli átirányítás használatával működik. Noha a pillanatkép-másolatok rendkívül gyorsan elkészíthetők és visszaállíthatók, csak az első szintű védelmi szolgáltatásként szolgálnak, amely az adott szervezet szükséges visszaállítási műveleteinek túlnyomó többségét is figyelembe veszi, ami gyakran emberi hiba miatt helyreáll. A pillanatkép-másolatok azonban nem teljes biztonsági mentés. A biztonsági mentési és visszaállítási követelmények teljesítéséhez a külső pillanatkép-replikákat és/vagy egyéb biztonsági másolatokat egy távoli földrajzban kell létrehozni a regionális kimaradás elleni védelem érdekében. Ha többet szeretne megtudni a NetApp-fájlok Azure-beli Oracle-adatbázisokkal való használatáról, olvassa el ezt a [jelentést](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Azure Backup szolgáltatás

[A Azure Backup](../../../backup/backup-overview.md) egy teljes körűen felügyelt Pásti, amely egyszerű, biztonságos és költséghatékony megoldást kínál az adatok biztonsági mentésére és a Microsoft Azure felhőből való helyreállítására. Azure Backup a helyszíni ügyfelek, az Azure-beli virtuális gépek, a Azure Files-megosztások, valamint a SQL Server-, Oracle-, MySQL-, PostgreSQL-és SAP HANA-adatbázisok biztonsági mentését és visszaállítását az Azure-beli virtuális gépeken. 

A Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatvesztés elleni védelemhez. A biztonsági mentések a helyreállítási pontok beépített felügyeletét biztosító [Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md) -tárolóban tárolódnak. A konfiguráció és a méretezhetőség egyszerű, a biztonsági mentések optimalizáltak, és szükség esetén egyszerűen visszaállíthatók. Az Azure-felhő mögöttes teljesítményének és korlátlan skálájának használatával biztosítja a magas rendelkezésre állást karbantartási vagy figyelési terhelés nélkül. Azure Backup nem korlátozza a továbbított vagy kimenő adatok mennyiségét, vagy az átvitt adatok díját, valamint az adatok átvitelét és nyugalmát. 

A tartósság biztosításához Azure Backup több típusú replikációt biztosít a biztonsági mentési adatai számára.

- A helyileg redundáns tárolás [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) háromszor replikálja az adatait (az adatai három másolatát hozza létre) egy adatközpontban lévő tárolási méretezési egységben.
- A Geo-redundáns tárolás [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől).

A GRS-redundanciával létrehozott tárolók tartalmazzák a [régiók közötti visszaállítás](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) funkciót, amely lehetővé teszi az adatvisszaállítást egy másodlagos, Azure-beli párosított régióban.

A Azure Backup szolgáltatás olyan [keretrendszert](../../../backup/backup-azure-linux-app-consistent.md) biztosít a Windows és Linux rendszerű virtuális gépek biztonsági mentése során a különböző alkalmazásokhoz, mint például az Oracle, a MySQL, a Mongo db, a SAP HANA és a PostGreSQL, az Application konzisztens Pillanatképek néven. Ebbe beletartozik egy előzetes parancsfájl meghívása (az alkalmazások fokozatos leválasztása), mielőtt pillanatképet készít a lemezekről, és meghívja az utólagos parancsfájlt (az alkalmazások feloldásához szükséges parancsokat) a pillanatkép befejezése után, hogy az alkalmazásokat a normál módba adja vissza. A minta előtti parancsfájlokat és a parancsfájlokat a GitHubon is elérhetővé teheti, így a parancsfájlok létrehozása és karbantartása az Ön felelőssége. Az Oracle esetében az adatbázisnak archivelog módban kell lennie az online biztonsági mentések engedélyezéséhez, és a megfelelő adatbázis-kezdési és befejezési biztonsági mentési parancsok futtatása az előzetes és a parancsfájl-parancsfájlok futtatásával történik, amelyet létre kell hoznia és karban kell tartania. 

A Azure Backup mostantól elérhetővé teszi a jelenleg előzetes verzióban elérhető [továbbfejlesztett parancsfájl-előkészítő és parancsfájl-közzétételi keretrendszert](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), ahol a Azure Backup szolgáltatás a kiválasztott alkalmazásokhoz csomagolt, előre megírt parancsfájlokat és szkripteket is biztosít. Azure Backup felhasználó csak az alkalmazás nevét kell megadnia, majd az Azure virtuális gép biztonsági mentése automatikusan meghívja a megfelelő előparancsfájlokat és parancsfájlokat. A becsomagolt előzetes parancsfájlokat és a parancsfájlok utáni parancsfájlokat a Azure Backup csapat tartja karban, így a felhasználók biztosíthatják a parancsfájlok támogatását, tulajdonlását és érvényességét. Jelenleg a továbbfejlesztett keretrendszer támogatott alkalmazásai az Oracle és a MySQL, és a jövőben több alkalmazási típus is várható. A pillanatkép a tárterület teljes másolata, és nem növekményes vagy másolási írási pillanatkép, ezért ez egy hatékony médium az adatbázis visszaállításához.

## <a name="next-steps"></a>Következő lépések

- [Oracle Database gyors útmutató létrehozása](oracle-database-quick-create.md)
- [Oracle Database biztonsági mentése Azure Files](oracle-database-backup-azure-storage.md)
- [Oracle Database biztonsági mentése Azure Backup szolgáltatás használatával](oracle-database-backup-azure-backup.md)


