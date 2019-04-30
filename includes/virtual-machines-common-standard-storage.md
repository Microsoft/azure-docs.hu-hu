---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: yuemlu
ms.service: storage
ms.topic: include
ms.date: 01/08/2019
ms.author: yuemlu
ms.custom: include file
ms.openlocfilehash: ad57d373422e0fc310e51ac31f2a2e76999abf22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127158"
---
# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Költséghatékony Standard Storage és a nem felügyelt és felügyelt Azure-beli Virtuálisgép-lemezek

Az Azure standard szintű Storage lemeztámogatást megbízható, költséghatékony virtuális gépek késleltetést toleráló-alapú számítási feladatait. Blobok, táblák, üzenetsorok és fájlokat is támogatja. A standard szintű Storage merevlemezes (HDD) meghajtók tárolják az adatokat. Az virtuális gépek használatakor lemez is használható standard SSD és HDD fejlesztési és tesztelési célra, a kritikus fontosságú számítási feladatokhoz, és a prémium szintű SSD-lemezeket kevésbé kritikus fontosságú éles üzemű alkalmazás számára. Standard szintű Storage az összes Azure-régióban érhető el. 

Ez a cikk foglalkozik, a standard szintű SSD és HDD-lemezek használatát. Blobok, táblák, üzenetsorok és fájlokat a Storage használatával kapcsolatos további információkért lásd: [Storage bemutatása](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Lemeztípusok

A standard szintű lemezek létrehozása az Azure virtuális gépek két módja van:

**Nem felügyelt lemezek**: Az ilyen típusú lemez a eredeti módszer, amellyel kezelheti a VHD-fájlokat, amelyek megfelelnek a virtuális gépek lemezeihez használt tárfiókok. VHD-fájlokat a storage-fiókok lap blobként vannak tárolva. Nem felügyelt lemezek minden olyan Azure virtuális gép méretét, beleértve a virtuális gépeket, amelyek bevételszerzésre használják a Premium Storage, a DSv2 és GS-sorozat például lehet rendelni. Az Azure virtuális gépek támogatják a több standard szintű lemezek csatolása lehetővé teszi a tárhelyet a maximális hossz 256 Tib-ra. Ha az előzetes verzió lemezméretek használ, legfeljebb körülbelül 2 PiB tárhelyet rendelkezhet.

[**Az Azure Managed Disks**](../articles/virtual-machines/windows/managed-disks-overview.md): Ez a funkció az Ön számára a virtuális gépek lemezeihez használt tárfiókok kezeli. A (prémium szintű SSD, Standard SSD vagy Standard HDD) típusát és méretét adja meg lemez van szüksége, és az Azure létrehozza és felügyeli a lemezt Ön helyett. Nem kell aggódnia a tárfiókok méretezhetőségének korlátai belül marad – az Azure végzi, amely az Ön számára biztosítása érdekében helyezi el a lemezek több tárfiókon keresztül.

Annak ellenére, hogy mindkét típusú lemezek elérhetők, a számos funkcióinak kihasználása Managed Disks használatát javasoljuk.

Az Azure standard szintű Storage, látogasson el [kezdje meg ingyenesen](https://azure.microsoft.com/pricing/free-trial/). 

A felügyelt lemezekkel rendelkező virtuális gép létrehozása információkért lásd: egyet a következő cikkekben talál.

* [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Linux virtuális gép létrehozása az Azure parancssori felülettel](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Standard szintű Storage-szolgáltatások

Vessünk egy pillantást a standard szintű Storage funkcióit. További részletekért tekintse meg [Azure Storage bemutatása](../articles/storage/common/storage-introduction.md).

**Standard szintű Storage**: Az Azure standard szintű Storage támogatja az Azure Disks, Azure-Blobok, az Azure Files, Azure-táblák és Azure-üzenetsorok. Standard szintű Storage szolgáltatás használatához kezdje [hozzon létre egy Azure Storage-fiók](../articles/storage/common/storage-quickstart-create-account.md).

**Standard SSD-lemezeket:** Standard SSD-lemezeket, mint a standard szintű HDD lemezek megbízhatóbb teljesítményt nyújtanak, és jelenleg érhetők el. Standard SSD-lemez régiók rendelkezésre állása kapcsolatos további információkért lásd: [régiók rendelkezésre állása Standard SSD-lemez](../articles/virtual-machines/windows/faq-for-disks.md#standard-ssds-azure-regions).

**A standard szintű HDD-lemezek:** A standard szintű HDD lemezek többek között például a DSv2 és GS-sorozat a Premium Storage használt mérete sorozatú virtuális gépek az Azure virtuális gépekhez csatolható. Standard HDD lemez csak egy virtuális géphez csatolható. Azonban egy vagy több ezeket a lemezeket is csatlakoztatható egy virtuális géphez, akár a Virtuálisgép-méret definiált lemezek maximális száma. A standard szintű Storage méretezhetőségi és Teljesítménycéljai a következő szakaszban ismertetünk, az előírások részletesebben.

**Standard szintű lapblob**: Standard szintű lapblobok, amely tárolja az állandó lemezt a virtuális gépek használják, és közvetlenül a REST, például a más típusú Azure-Blobok keresztül is elérhető. [Lapblobok](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) véletlenszerű olvasási és írási műveletekhez optimalizált 512 bájtos oldalak gyűjteményei. 

**Tárreplikáció:** A legtöbb olyan régióban standard szintű storage-fiókban lévő adatok lehet helyileg replikált vagy georeplikált egyszerre több adatközpontban. A négy típusú replikácó a helyileg redundáns tárolás (LRS), a Zónaredundáns tárolás (ZRS), a Georedundáns tárolás (GRS) és az írásvédett Georedundáns tárolás (RA-GRS). Felügyelt lemezek a standard szintű Storage jelenleg támogatja a helyileg redundáns tárolás (LRS) csak. További információkért tekintse meg [Tárreplikáció](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Méretezhetőségi és teljesítménycélok

Ebben a szakaszban ismertetünk, a méretezhetőségi és teljesítménycéljai figyelembe kell vennie a standard szintű storage használata esetén.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Korlátozások – nem vonatkozik a managed Disks szolgáltatásba

| **Erőforrás** | **Alapértelmezett korlát** |
|--------------|-------------------|
| Storage-fiók / TB  | 500 TB |
| Maximális bejövő<sup>1</sup> tárfiókonként (USA régió) | Ha GRS/zrs esetén engedélyezve van, 20 GB/s az lrs esetén 10 GB/s |
| Maximális kimenő<sup>1</sup> tárfiókonként (USA régió) | Ha engedélyezett a RA-GRS/GRS/zrs esetén, 30 GB/s az lrs esetén 20 GB/s |
| Maximális bejövő<sup>1</sup> tárfiókonként (Európai és ázsiai régióban) | Ha GRS/zrs esetén engedélyezve van, 10 GB/s az lrs esetén 5 GB/s |
| Maximális kimenő<sup>1</sup> tárfiókonként (Európai és ázsiai régióban) | Ha engedélyezett a RA-GRS/GRS/zrs esetén, 15 GB/s az lrs esetén 10 GB/s |
| Összes kérelem Egységár (1 KB-os objektumméret feltételezve) storage-fiók | Legfeljebb 20 000 IOPS, másodpercenként entitások vagy üzenetek / másodperc |

<sup>1</sup> bejövő küld a storage-fiók összes adatot (kérést) hivatkozik. Kimenő adatforgalom egy tárfiók felől fogadott összes adatot (választ) hivatkozik.

További információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycéljai](../articles/storage/common/storage-scalability-targets.md).

Ha az alkalmazása igényeit meghaladja a skálázhatósági célokat, az egy tárfiókban, hozhat létre az alkalmazás több tárfiók használata, és az adatok particionálása az adott tárfiókon keresztül. Azt is megteheti akkor is, hogy az Azure Managed Disks, és az Azure kezeli a particionálást és az Ön számára az adatok elhelyezésére.

### <a name="standard-disks-limits"></a>A standard szintű lemezek korlátok

A bemeneti/kimeneti műveletek másodpercenként (IOPS) és a standard szintű lemezek (sávszélesség) átviteli ellentétben a prémium szintű lemezek, nincs kiépítve. A standard szintű lemezek teljesítménye művelettől függ, a virtuális gép méretét, hogy a lemez csatolva, és a lemez kívánt méretét.

Ha a számítási feladatok nagy teljesítményű, kis késleltetésű lemeztámogatás van szüksége, érdemes a Premium Storage tárolást használ. Prémium szintű Storage további előnyei ismeri, a Microsoft [nagy teljesítményű Premium Storage és Azure Virtuálisgép-lemezek](../articles/virtual-machines/windows/premium-storage.md).

## <a name="snapshots-and-copy-blob"></a>A pillanatképek és a blob másolásához

A Storage szolgáltatásban a VHD-fájl egy lapblob. A lapblobok pillanatképeket készíthet, és másolja őket egy másik helyre, például egy másik tárfiókot.

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

Létrehozhat [növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md) ugyanúgy, mint a nem felügyelt a standard szintű lemezek pillanatképek használata standard szintű tárolást. Azt javasoljuk, hogy pillanatképeket létrehozni, és másoljon ezen pillanatképek georedundáns standard szintű tárfiók, ha a forrás lemez egy helyileg redundáns társzolgáltatás fiókjába. További információkért lásd: [Azure Storage Redundanciabeállításainál](../articles/storage/common/storage-redundancy.md).

Ha egy lemezt egy virtuális Géphez van csatlakoztatva, bizonyos API-műveletek nem engedélyezettek a lemezeken. Például nem végezhető el egy [a Blob másolásához](/rest/api/storageservices/Copy-Blob) műveletet, hogy a blob, amíg a lemezt egy virtuális Géphez van csatlakoztatva. Ehelyett először hozzon létre egy adott blob pillanatképe használatával a [Blob pillanatkép](/rest/api/storageservices/Snapshot-Blob) – REST API-metódus, és hajtsa végre a [a Blob másolásához](/rest/api/storageservices/Copy-Blob) a pillanatkép másolása a csatlakoztatott lemezen. Másik lehetőségként válassza le a lemezt, és hajtsa végre a szükséges műveleteket.

A pillanatképek georedundáns másolatait másolhatja pillanatképek egy helyileg redundáns tárolás fiókból georedundáns standard storage-fiókba az AzCopy és a Blob másolásához. További információkért lásd: [adatátvitel az AzCopy parancssori segédprogrammal](../articles/storage/common/storage-use-azcopy.md) és [a Blob másolásához](/rest/api/storageservices/Copy-Blob).

A standard szintű storage-fiókok ellen a lapblobok REST-műveleteket végez a részletes információkért lásd: [Azure Storage szolgáltatások REST API](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemez pillanatképét, a standard szintű felügyelt lemezként tárolt felügyelt lemez csak olvasható példányát. Növekményes pillanatképek Managed Disks esetében jelenleg nem támogatottak, de a jövőben támogatott.

Ha egy felügyelt lemezt egy virtuális Géphez van csatlakoztatva, bizonyos API-műveletek nem engedélyezettek a lemezeken. Például nem hozható létre egy közös hozzáférésű jogosultságkód (SAS) a másolási műveletek végrehajtásához, amíg a lemezt egy virtuális Géphez van csatlakoztatva. Ehelyett először hozzon létre a lemez pillanatképét, és végezze el a pillanatkép másolatát. Azt is megteheti válassza le a lemezt, és előállít egy közös hozzáférésű jogosultságkód (SAS) a másolási művelet végrehajtásához.

## <a name="pricing-and-billing"></a>Árak és számlázás

Standard szintű Storage használata esetén az alábbi számlázási szempontok érvényesek:

* Standard szintű storage nem felügyelt lemez/adatok mérete
* Standard szintű felügyelt lemezekre
* Standard szintű storage-pillanatképek
* Kimenő adatforgalom
* Tranzakciók

**Nem kezelt adatok és a lemez mérete:** A nem felügyelt lemezek és egyéb adatok (blobok, táblák, üzenetsorok és fájlok) a számlázás a csak mennyiségének területet használ. Például ha egy virtuális gép rendelkezik 127 GB, de a virtuális gép, amelynek lapblob kiépítése valójában csak 10 GB-nyi területet használja, fizetnie 10 GB lemezterület. Standard szintű storage akár támogatjuk 8191 GB, és a standard szintű nem felügyelt lemezek legfeljebb 4095 GB-ig. 

**Felügyelt lemezek:** A standard szintű felügyelt lemezek díjszabása a kiépített a lemez mérete függ. Azure maps-a kiépítési méret (kerekítve) a legközelebbi Managed Disks beállítás, az alábbi táblázatban megadott módon. Minden egyes felügyelt lemezéhez vannak leképezve a támogatott kiosztott méretek egyikét, és ennek megfelelően történik. Például ha standard szintű felügyelt lemez létrehozása, és adja meg a 200 GB kiosztott méretét, számítjuk fel a díjszabás a S15 lemez típusa alapján.

Csillaggal szintben méretek jelenleg előzetes verzióban érhető el.

| **Felügyelt standard HDD <br>lemez típusa** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60*** | **S70*** | **S80*** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Lemezméret        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 GiB (2 TiB) | 4095 GiB (4 TiB) | 8192 GiB (8 TiB) | 16,385 giB (16 TiB) | 32 767 giB (32 TiB) |


**A pillanatképek**: Standard szintű lemezek pillanatképei a megnövelt kapacitás, a pillanatképek által használt számlázzuk ki. A pillanatképek információkért lásd: [létrehozása egy pillanatképet egy Blobról](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Kimenő adatforgalom**: [kimenő adatforgalom](https://azure.microsoft.com/pricing/details/data-transfers/) (adatok csak az Azure adatközpontok kimenő adatforgaloma) díjak lépnek fel a sávszélesség-használat.

**Tranzakció**: Azure 0.0036 $ / 100 000 tranzakció standard szintű tárolóra vonatkozó költségek. Tranzakciónak számít az adatok tárolóba írása és tárolóból való olvasása is.

A standard szintű Storage, a virtuális gépek és a Managed Disks díjszabása részletes információkért lásd:

* [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/)
* [Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [A Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Az Azure Backup szolgáltatás támogatása

Nem felügyelt lemezekkel rendelkező virtuális gépeket az Azure Backup segítségével készíthető. [További részletekért](../articles/backup/backup-azure-vms-first-look-arm.md).

Használhatja az Azure Backup szolgáltatás a Managed Disks egy biztonsági mentési feladat létrehozása idő-alapú biztonsági mentések, könnyű VM-helyreállítás és a biztonsági másolatok megőrzési házirendeket. Tudjon meg többet erről a [használata az Azure Backup szolgáltatás a felügyelt lemezekkel rendelkező virtuális gépek](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>További lépések

* [A Microsoft Azure Storage bemutatása](../articles/storage/common/storage-introduction.md)

* [Tárfiók létrehozása](../articles/storage/common/storage-quickstart-create-account.md)

* [Managed Disks – áttekintés](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Linux virtuális gép létrehozása az Azure parancssori felülettel](../articles/virtual-machines/linux/quick-create-cli.md)
