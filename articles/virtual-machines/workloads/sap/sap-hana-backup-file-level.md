---
title: SAP HANA Azure Backup a fájl szintjén | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatásban SAP HANA két fő biztonsági mentési lehetőség áll rendelkezésre, ez a cikk a fájlok szintjén SAP HANA Azure Backupt ismerteti.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667983"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup a fájl szintjén

## <a name="introduction"></a>Bevezetés

Ez a cikk az [Azure Virtual Machines SAP HANAának biztonsági mentési útmutatóját](./sap-hana-backup-guide.md)ismerteti, amely áttekintést nyújt az első lépésekről, valamint a Azure Backup szolgáltatás és a tárolási Pillanatképek további részleteiről. 

Az Azure-beli különböző virtuálisgép-típusok különböző számú virtuális merevlemezt tesznek lehetővé. A pontos részleteket a [Linux rendszerű virtuális gépek méreteiben dokumentáljuk az Azure-ban](../../sizes.md). Az ebben a dokumentációban említett tesztek esetében egy GS5 Azure-beli virtuális gépet használunk, amely lehetővé teszi a 64-hez csatlakoztatott adatlemezek használatát. Nagyobb SAP HANA rendszerek esetében előfordulhat, hogy jelentős számú lemezre van szükség az adatok és a naplófájlok esetében, valószínűleg az optimális lemezes i/o-átviteli sebességhez. Az Azure-beli virtuális gépeken SAP HANA üzemelő példányok javasolt lemez-konfigurációval kapcsolatos további részletekért olvassa el az Azure-beli [virtuális gépek tárolási konfigurációjának SAP HANAát](./hana-vm-operations-storage.md)ismertető cikket. A javaslatok a helyi biztonsági mentésekre vonatkozó lemezterület-ajánlásokat is tartalmaznak.

A Backup/Restore fájl szintjén való kezelésének szabványos módja a fájl-alapú biztonsági mentés SAP HANA studión keresztül vagy SAP HANA SQL-utasításokon keresztül. További információért olvassa el [SAP HANA SQL-és rendszernézetek referenciáját](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html).

![Ez az ábra a SAP HANA Studio Backup (biztonsági mentés) menüpontjának párbeszédpanelét jeleníti meg](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Ez az ábra a SAP HANA Studio Backup (biztonsági mentés) menüjének párbeszédpanelét jeleníti meg. A fájltípus kiválasztásakor az &quot; &quot; egyiknek meg kell adnia egy elérési utat a fájlrendszerben, ahol a SAP HANA a biztonságimásolat-fájlokat írja. A visszaállítás ugyanúgy működik.

Habár ez a választás egyszerű és egyenes előre hangzik, néhány szempontot figyelembe kell venni. Az Azure-beli virtuális gépekre korlátozott számú adatlemez csatlakoztatható. Előfordulhat, hogy a virtuális gép fájlrendszerén nem lehet kapacitást tárolni SAP HANA biztonságimásolat-fájlok tárolásához, az adatbázis méretétől és a lemez átviteli sebessége követelményeitől függően, amely több adatlemezen is tartalmazhat szoftvereket. A biztonságimásolat-fájlok áthelyezésének különböző lehetőségei, valamint a fájlméretre vonatkozó korlátozások és teljesítmény kezelése a jelen cikk későbbi részében található.

Egy másik lehetőség, amely a teljes kapacitással kapcsolatos nagyobb szabadságot nyújt, az Azure Blob Storage. Habár egyetlen blob is 1 TB-ra van korlátozva, egy blob-tároló teljes kapacitása jelenleg 500 TB. Emellett a felhasználók dönthetnek úgy, hogy kiválasztják az úgynevezett ritka elérésű &quot; &quot; blob Storage-t, ami költséghatékony. Tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szinteket](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) a ritka blob Storage szolgáltatással kapcsolatos részletekért.

A további biztonság érdekében a SAP HANA biztonsági mentések tárolásához használjon földrajzilag replikált Storage-fiókot. A tárterület-redundancia és a tárolási replikáció részleteiért lásd az [Azure Storage-redundancia](../../../storage/common/storage-redundancy.md) című témakört.

Az egyik dedikált virtuális merevlemezt helyezhet SAP HANA biztonsági másolatokhoz egy olyan dedikált biztonsági mentési Storage-fiókban, amely földrajzilag replikálva van. Egy másik lehetséges, hogy átmásolja a SAP HANA biztonsági másolatokat tároló virtuális merevlemezeket egy földrajzilag replikált Storage-fiókba vagy más régióban található Storage-fiókba.

## <a name="azure-blobxfer-utility-details"></a>Az Azure blobxfer segédprogram részletei

Az Azure Storage-beli könyvtárak és fájlok tárolására a CLI vagy a PowerShell használatával, illetve az [Azure SDK](https://azure.microsoft.com/downloads/)-k egyikének használatával lehet létrehozni egy eszközt. Az adatok Azure Storage-ba történő másolásához használható használatra kész AzCopy is. (lásd: [adatok átvitele a AzCopy Command-Line segédprogrammal](../../../storage/common/storage-use-azcopy-v10.md)).

Ezért a blobxfer SAP HANA biztonságimásolat-fájlok másolására használták. Nyílt forráskód, amelyet számos ügyfél használ éles környezetben, és elérhető a [githubon](https://github.com/Azure/blobxfer). Ez az eszköz lehetővé teszi, hogy az egyiket közvetlenül az Azure Blob Storage-ba vagy az Azure-fájlmegosztásba másolja. Számos hasznos funkciót is kínál, például az MD5-kivonatot vagy az automatikus párhuzamosságot, ha több fájllal rendelkező könyvtárat másol.

## <a name="sap-hana-backup-performance"></a>Az SAP HANA biztonsági mentésének teljesítménye
Ebben a fejezetben a teljesítménnyel kapcsolatos szempontokat tárgyaljuk. Előfordulhat, hogy az elért számok nem jelentik a legutóbbi állapotot, mivel az Azure Storage-hoz való jobb teljesítmény érdekében folyamatos fejlesztés érhető el. Ennek eredményeképpen egyéni teszteket kell végeznie a konfigurációhoz és az Azure-régióhoz.

![Ez a képernyőkép a SAP HANA Studióban található SAP HANA biztonsági mentési konzolról](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Ez a képernyőkép a SAP HANA Studio SAP HANA biztonsági mentési konzolját mutatja be. Körülbelül 42 percet vett igénybe, hogy készítsen biztonsági másolatot a 230 GB-ról egyetlen Azure standard HDD Storage-lemezre, amely a HANA virtuális géphez a XFS fájlrendszer használatával van csatolva egy lemezen.

![Ez a képernyőkép a SAP HANA test VM-YaST](media/sap-hana-backup-file-level/one-backup-disk.png)

Ez a képernyőkép a SAP HANA test VM-YaST. Az 1 TB-os lemez SAP HANA biztonsági mentéshez is megtekinthető. Körülbelül 42 percet vett igénybe a 230 GB biztonsági mentése. Emellett a 5 200 GB-os lemezek csatolták és a szoftveres RAID-md0 is létrehozták, és ezek az öt Azure-adatlemezre épülnek.

![Ugyanazon biztonsági másolat megismétlése a szoftveres RAID-ben az öt csatolt Azure standard Storage-adatlemezek között](media/sap-hana-backup-file-level/five-backup-disks.png)

Ugyanezt a biztonsági mentést ugyanazzal a szoftveres RAID-sel megismételve, amely öt csatolt Azure standard Storage-adatlemezre mutat, a biztonsági mentés ideje 42 perctől 10 percre. A lemezeket a virtuális géphez való gyorsítótárazás nélkül csatlakoztatták. Ez a gyakorlat azt mutatja be, hogy milyen fontos a lemez írási sebessége a jó biztonsági mentési idő eléréséhez. Átválthat az Azure standard SSD Storage-ra vagy az Azure Premium Storagera, hogy tovább gyorsítsák az optimális teljesítmény érdekében. Általánosságban elmondható, hogy az Azure standard HDD-tároló nem ajánlott, és csak demonstrációs célokat szolgál. Javasoljuk, hogy az üzemi rendszerek számára legalább Azure standard SSD Storage-t vagy Azure-Premium Storage használjon.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>SAP HANA biztonságimásolat-fájlok másolása az Azure Blob Storage-ba
Előfordulhat, hogy a megadott teljesítményi számok, a biztonsági mentés időtartamának száma és a másolási időtartam száma nem az Azure-technológia legújabb állapotát képviseli. A Microsoft folyamatosan fejleszti az Azure Storage-t, hogy nagyobb átviteli sebességet és kisebb késéseket nyújtson. Ezért a számok csak demonstrációs célokat szolgálnak. Az Ön által választott Azure-régióban kell tesztelni az egyéni igényeket, hogy a lehető leghatékonyabban tudja megítélni a módszert.

Egy másik lehetőség a SAP HANA biztonsági mentési fájlok gyors tárolására az Azure Blob Storage. Egy blob-tároló legfeljebb 500 TB-os korláttal rendelkezik, amely elegendő a SAP HANA rendszerekhez, a M32ts, a M32ls, a M64ls és az Azure GS5 VM-típusaival, hogy elegendő SAP HANA biztonsági mentést lehessen tartani. Az ügyfelek választhatnak a gyakori és a ritkán &quot; &quot; &quot; &quot; használt BLOB Storage-tároló között (lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)).

A blobxfer eszközzel egyszerűen másolhatók a SAP HANA biztonsági másolatok fájljai közvetlenül az Azure Blob Storage-ba.

![Itt láthatja a teljes SAP HANA fájl biztonsági mentésének fájljait](media/sap-hana-backup-file-level/list-of-backups.png)

Megtekintheti a teljes SAP HANA fájl biztonsági mentésének fájljait. A négy fájlból nagyjából 230 GB méretűnek kell lennie.

![Körülbelül 3000 másodpercet vett igénybe a 230 GB másolása Azure standard Storage-fiók blob-tárolóba](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

A kezdeti tesztben nem használ MD5-kivonatot, nagyjából 3000 másodpercet vett igénybe a 230 GB másolása egy Azure standard Storage-fiók blob-tárolóba.

A HANA Studio biztonsági mentési konzol lehetővé teszi, hogy az egyik korlátozza a HANA biztonságimásolat-fájlok maximális fájlméretét. A mintavételezési környezetben nagyobb teljesítményre van szüksége, mivel egy nagyméretű 230 GB-os fájl helyett több kisebb biztonsági mentési fájl is van.

A biztonsági mentési fájlméret korlátozásának beállítása a HANA oldalán nem&#39;t a biztonsági mentés időpontjának javítása, mert a fájlok egymás után írhatók. A fájlméret korlátja 60 GB volt, ezért a biztonsági mentés négy nagy adatfájlt hozott létre a 230 GB-os egyetlen fájl helyett. A több biztonságimásolat-fájl használata szükséges lehet a HANA-adatbázisok biztonsági mentéséhez, ha a biztonsági mentési célok korlátozásokkal rendelkeznek a blob-méretek fájlméretéről.

![A blobxfer eszköz párhuzamosságának teszteléséhez a HANA biztonsági mentések maximális fájlmérete 15 GB-ra van állítva](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

A blobxfer eszköz párhuzamosságának teszteléséhez a HANA biztonsági mentések maximális fájlméretét 15 GB-ra állította, ami 19 biztonsági mentési fájlt eredményezett. Ez a konfiguráció azt az időpontot hozta, hogy a blobxfer a 230 GB-ot az Azure Blob Storage-ba másolja a 3000 másodperctől a 875 másodpercig.

Mivel a helyi lemezekkel más helyekre (például az Azure Blob Storage-ba) végzett biztonsági mentések másolását vizsgálja, vegye figyelembe, hogy az esetleges párhuzamos másolási folyamat által használt sávszélesség az egyes virtuálisgép-típusok hálózati vagy tárolási kvótája alapján történik. Ennek eredményeképpen el kell osztania a másolás időtartamát a hálózat és a tárolási sávszélesség között a virtuális gépen futó normál számítási feladatokhoz. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>SAP HANA biztonságimásolat-fájlok másolása NFS-megosztásba

Microsoft Azure natív NFS-megosztásokat kínál a [Azure NetApp Fileson](https://azure.microsoft.com/services/netapp/)keresztül. A biztonsági másolatok tárolásához és kezeléséhez különböző köteteket hozhat létre a kapacitásban. Ezeket a köteteket a NetApp technológiája alapján is elvégezheti. A Azure NetApp Files (ANF) három különböző szolgáltatási szinten érhető el, amelyek különböző tárolási sebességet biztosítanak. További részletekért olvassa el a [Azure NetApp Filesi szolgáltatási szintjeinek](../../../azure-netapp-files/azure-netapp-files-service-levels.md)leírását. A ANF-ből létrehozhat és csatlakoztathat egy NFS-kötetet a [gyors útmutató: Azure NetApp Files beállítása és az NFS-kötet létrehozása](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal)című cikkben leírtak szerint.

Az Azure natív NFS-kötetei az ANF-n keresztül történő használata mellett számos lehetőség áll rendelkezésre a saját, az Azure-ban NFS-megosztásokat biztosító központi telepítések létrehozására. Az összes hátránya, hogy ezeket a megoldásokat saját kezűleg kell telepítenie és kezelnie. Ezek közül néhányat a következő cikkek dokumentálnak:

- [Magas rendelkezésre állás az NFS-en SUSE Linux Enterprise Server Azure-beli virtuális gépeken](./high-availability-guide-suse-nfs.md)
- [Red Hat Enterprise Linuxon futó Azure-beli virtuális gépeken üzemelő GlusterFS SAP NetWeaverhez](./high-availability-guide-rhel-glusterfs.md)

A fent leírt módon létrehozott NFS-megosztások használhatók a HANA-alapú biztonsági másolatok közvetlen végrehajtásához vagy a helyi lemezeken az NFS-megosztásokon végrehajtott biztonsági másolatok másolásához.

> [!NOTE]
> SAP HANA támogatja az NFS v3 és NFS v4. x verzióját. Bármilyen más formátum, például az SMB és a CIFS fájlrendszer használata nem támogatott a HANA-alapú biztonsági mentések megírásához. Lásd még: [SAP-támogatás megjegyzés #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>SAP HANA biztonságimásolat-fájlok másolása Azure Files

Egy Azure Files-megosztást egy Azure Linux rendszerű virtuális gépen lehet csatlakoztatni. Az [Azure file Storage és a Linux használatát](../../../storage/files/storage-how-to-use-files-linux.md) ismertető cikk részletesen ismerteti a konfiguráció végrehajtásának módját. A Azure Files vagy az Azure Premium-fájlokra vonatkozó korlátozásokért olvassa el [Azure Files skálázhatósági és teljesítményi célok](../../../storage/files/storage-files-scale-targets.md)című cikket.

> [!NOTE]
> A SAP HANA nem támogatja az SMB és a CIFS fájlrendszer használatát a HANA-alapú biztonsági mentések írásához. Lásd még: [SAP-támogatási megjegyzés #1820529](https://launchpad.support.sap.com/#/notes/1820529). Ennek eredményeképpen ezt a megoldást csak a HANA-adatbázis biztonsági másolatának végső céljaként használhatja, amelyet közvetlenül a helyi csatlakoztatott lemezeken hajtottak végre.
> 

A Azure Fileson elvégzett tesztekben nem az Azure Premium Files körülbelül 929 másodpercet vett igénybe a 19 biztonsági mentési fájlnak a 230 GB-os teljes kötettel való másolásához. Reméljük, hogy a prémium szintű Azure-fájlok használata sokkal jobb. Azonban érdemes szem előtt tartania, hogy a gyors másolás érdekeit meg kell osztania a számítási feladatok hálózati sávszélességgel kapcsolatos követelményeivel. Mivel minden egyes Azure-beli virtuálisgép-típus kikényszeríti a hálózati sávszélesség kvótáját, a kvóta tartományán belül kell maradnia a számítási feladattal és a biztonságimásolat-fájlok másolatával.

![Ez az ábra azt mutatja, hogy körülbelül 929 másodpercet vett át a 19 SAP HANA biztonsági mentési fájl másolásához](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Az Azure Files SAP HANA biztonsági mentési fájljainak tárolása érdekes lehetőség lehet. Különösen az Azure Premium-fájlok jobb késésével és teljesítményével.

## <a name="next-steps"></a>Következő lépések
* [Az Azure Virtual Machines SAP HANA biztonsági mentési útmutatója](sap-hana-backup-guide.md) áttekintést nyújt az első lépésekről.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
