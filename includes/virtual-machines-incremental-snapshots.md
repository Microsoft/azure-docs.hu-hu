---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026355"
---
## <a name="overview"></a>Áttekintés
Az Azure Storage lehetővé teszi a Blobok Pillanatképek készítését. A pillanatképek az adott időpontban rögzítik a blob állapotát. Ebben a cikkben egy olyan forgatókönyvet ismertetünk, amelyben a virtuális gépek lemezeiről készített biztonsági mentések a pillanatképek használatával kezelhetők. Ezt a módszert akkor használhatja, ha úgy dönt, hogy nem használja a Azure Backup és a helyreállítási szolgáltatást, és egyéni biztonsági mentési stratégiát kíván létrehozni a virtuális gépek lemezeihez. Az üzleti vagy kritikus fontosságú számítási feladatokat futtató virtuális gépek esetében ajánlott [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) használni a biztonsági mentési stratégia részeként.  

Az Azure-beli virtuális gépek lemezeit az Azure Storage-ban az oldal blobként tárolja a rendszer. Mivel ebben a cikkben a virtuálisgép-lemezekre vonatkozó biztonsági mentési stratégiát ismertetjük, a pillanatképeket a Blobok kontextusában tekintjük át. A pillanatképekkel kapcsolatos további tudnivalókért tekintse meg a [blob pillanatképének létrehozását](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)ismertető témakört.

## <a name="what-is-a-snapshot"></a>Mi az a pillanatkép?
A blob-pillanatkép egy adott időpontban rögzített blob írásvédett verziója. A pillanatkép létrehozása után a fájl olvasható, másolható vagy törölhető, de nem módosítható. A pillanatképek lehetővé teszik a Blobok biztonsági mentését, amikor az adott pillanatban megjelenik. A REST 2015-04-05-es verziójában teljes pillanatképeket másolhat. A REST 2015-07-08-es és újabb verzióiban növekményes pillanatképeket is másolhat.

## <a name="full-snapshot-copy"></a>Teljes pillanatkép másolása
A pillanatképek egy másik Storage-fiókba másolhatók blobként, hogy megőrizze az alap blob biztonsági mentését. A pillanatképet az alap blobon keresztül is másolhatja, amely hasonló a blob korábbi verzióra való visszaállításához. Ha egy pillanatképet egy Storage-fiókból egy másikba másol, a kiinduló oldal blobjának megegyező helyet foglal el. Ezért a teljes Pillanatképek egyetlen Storage-fiókból a másikba való másolása lassú, és sok helyet foglal a célként megadott Storage-fiókban.

> [!NOTE]
> Ha az alap blobot egy másik célra másolja, a blob pillanatképeit nem másolja a rendszer. Hasonlóképpen, ha egy másolattal felülír egy alap blobot, a rendszer nem érinti az alap blobhoz társított pillanatképeket, és az alap blob neve alatt érintetlen marad.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Lemezek biztonsági mentése Pillanatképek használatával
A virtuálisgép-lemezek biztonsági mentési stratégiája a lemez vagy az oldal blobjának rendszeres időközönkénti pillanatképeit készíti el, és átmásolja őket egy másik Storage-fiókba olyan eszközökkel, mint a [másolási blob](/rest/api/storageservices/Copy-Blob) vagy a [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md). A pillanatképeket átmásolhatja egy másik nevű célhelyre. Az eredményül kapott cél oldal blobja egy írható oldal blobja, és nem pillanatkép. A cikk későbbi részében a virtuális gépek lemezeinek pillanatképekkel történő biztonsági mentésének lépéseit ismertetjük.

### <a name="restore-disks-using-snapshots"></a>Lemezek visszaállítása Pillanatképek használatával
Ha a lemez olyan stabil verzióra való visszaállítása, amelyet korábban rögzítettek a biztonsági mentési Pillanatképek egyikében, a pillanatképet a kiinduló oldal blobján keresztül másolhatja. A pillanatképnek a kiinduló oldal blobba való előléptetését követően a pillanatkép továbbra is megmarad, de a forrás egy olyan másolattal van felülírva, amely olvasható és írható is lehet. A cikk későbbi részében leírjuk a lemez korábbi verziójának a pillanatképből való visszaállításának lépéseit.

### <a name="implementing-full-snapshot-copy"></a>Teljes pillanatkép-másolás megvalósítása
A teljes pillanatkép-másolást a következő módon hajthatja végre:

* Először készítsen pillanatképet az alap blobról a [Snapshot blob](/rest/api/storageservices/Snapshot-Blob) művelettel.
* Ezután másolja a pillanatképet egy célként megadott Storage-fiókba [másolási blob](/rest/api/storageservices/Copy-Blob)használatával.
* Ismételje meg ezt a folyamatot az alap blob biztonsági másolatának karbantartásához.

## <a name="incremental-snapshot-copy"></a>Növekményes pillanatkép másolása
A [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) API új funkciója sokkal jobb megoldást kínál az oldal Blobok vagy lemezek pillanatképének biztonsági mentésére. Az API az alap blob és a pillanatképek közötti változások listáját adja vissza, ami csökkenti a biztonsági mentési fiókban használt tárterület méretét. Az API támogatja a Premium Storage és a standard szintű tárolóban lévő blobokat. Ezzel az API-val gyorsabb és hatékonyabb biztonsági mentési megoldásokat hozhat létre az Azure-beli virtuális gépekhez. Ez az API a REST 2015-07-08-es és újabb verzióival lesz elérhető.

A növekményes pillanatkép-másolás lehetővé teszi, hogy az egyik Storage-fiókból a másikba másolja a következő különbséget:

* Az alap blob és a hozzá tartozó pillanatkép vagy
* Az alap blob két pillanatképe

A következő feltételek teljesülnek,

* A blobot Jan-1-2016 vagy újabb verzióban hozták létre.
* A blob nem lett felülírva a [PutPage](/rest/api/storageservices/Put-Page) vagy a [blob másolása](/rest/api/storageservices/Copy-Blob) két pillanatkép között.

>[!NOTE]
>Ez a funkció prémium és standard szintű Azure-Blobok esetén érhető el.

Ha pillanatképeket használó egyéni biztonsági mentési stratégiával rendelkezik, a pillanatképek egy másik Storage-fiókból a másikba való másolása lassú lehet, és sok tárolóhelyet tud használni. Ahelyett, hogy a teljes pillanatképet egy biztonsági mentési Storage-fiókba másolja, megírhatja az egymást követő Pillanatképek közötti különbséget egy biztonsági mentési oldal blobba. Így a másolási idő és a biztonsági mentések tárolására szolgáló terület lényegesen csökken.

### <a name="implementing-incremental-snapshot-copy"></a>Növekményes pillanatkép-másolat implementálása
A növekményes Pillanatképek másolását a következő módon hajthatja végre:

* Készítsen pillanatképet az alap blobról a [Snapshot blob](/rest/api/storageservices/Snapshot-Blob)használatával.
* Másolja a pillanatképet a cél biztonsági mentési Storage-fiókba, vagy bármely más Azure-régióban, a [blob másolása](/rest/api/storageservices/Copy-Blob)használatával. Ez a biztonsági mentési oldal blobja. Készítsen pillanatképet a biztonsági mentési oldal blobról, és tárolja azt a biztonsági mentési fiókban.
* Készítsen egy pillanatképet az alap blobról a Snapshot blob használatával.
* A [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges)használatával megszerezheti az alap blob első és második pillanatképének különbségét. Az új **prevsnapshot** paraméterrel megadhatja annak a pillanatképnek a DateTime értékét, amelyről a különbséget szeretné kapni. Ha ez a paraméter megtalálható, a REST-válasz csak azokat a lapokat tartalmazza, amelyek módosultak a cél pillanatképe és a korábbi pillanatkép között, beleértve a lapok törlését is.
* A [PutPage](/rest/api/storageservices/Put-Page) használatával alkalmazza ezeket a módosításokat a biztonsági mentési oldal blobján.
* Végül készítsen pillanatképet a biztonsági mentési oldal blobról, és tárolja azt a biztonsági mentési Storage-fiókban.

A következő szakaszban részletesen ismertetjük, hogyan kezelheti a lemezek biztonsági mentését növekményes Pillanatképek másolásával

## <a name="scenario"></a>Eset
Ebben a szakaszban egy olyan forgatókönyvet ismertetünk, amely egy egyéni biztonsági mentési stratégiát tartalmaz a virtuális gépek lemezei számára a pillanatképek használatával.

Vegyünk egy olyan DS-sorozatú Azure-beli virtuális gépet, amelyhez prémium szintű Storage P30-lemez van csatolva. A *mypremiumdisk* nevű P30-lemezt egy *mypremiumaccount* nevű Premium Storage-fiók tárolja. A *mypremiumdisk* biztonsági mentésének tárolására a *mybackupstdaccount* nevű standard Storage-fiók használható. 12 óránként szeretnénk megőrizni a *mypremiumdisk* pillanatképét.

A Storage-fiók létrehozásáról a Storage- [fiók létrehozása](../articles/storage/common/storage-account-create.md)című témakörben olvashat bővebben.

Az Azure-beli virtuális gépek biztonsági [mentésének megtervezéséhez tekintse meg az Azure VM biztonsági mentések tervezése](../articles/backup/backup-azure-vms-introduction.md)című témakört.

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>A lemezek biztonsági mentésének lépései növekményes Pillanatképek használatával
A következő lépések azt ismertetik, hogyan lehet pillanatképeket készíteni a *mypremiumdisk* , és karbantartani a biztonsági mentéseket a *mybackupstdaccount*-ben. A Backup egy *mybackupstdpageblob* nevű szabványos oldal blob. A biztonsági mentési oldal blobja mindig ugyanazt az állapotot tükrözi, mint a *mypremiumdisk* utolsó pillanatképe.

1. Hozza létre a biztonsági mentési oldal blobját a Premium Storage-lemezhez a *mypremiumdisk_ss1* nevű *mypremiumdisk* pillanatképének elkészítése révén.
2. Másolja ezt a pillanatképet a mybackupstdaccount néven *mybackupstdpageblob* nevű oldal-blobba.
3. Készítsen pillanatképet *mybackupstdpageblob_ss1* nevű *mybackupstdpageblob* , és tárolja a [Pillanatkép-blobot](/rest/api/storageservices/Snapshot-Blob) a *mybackupstdaccount*-ben.
4. A biztonsági mentés ablakában hozzon létre egy pillanatképet a *mypremiumdisk*, *mypremiumdisk_ss2*, és tárolja azt a *mypremiumaccount*-ben.
5. Szerezze be a két pillanatkép közötti növekményes módosításokat, *mypremiumdisk_ss2* és *mypremiumdisk_ss1* a [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) on *mypremiumdisk_ss2* használatával a **prevsnapshot** paramétert a *mypremiumdisk_ss1* timestamp értékre állítva. Írja be ezeket a növekményes módosításokat a *mybackupstdaccount* biztonsági mentési oldalának blob- *mybackupstdpageblob* . Ha a növekményes módosítások törölve vannak, akkor azokat törölni kell a biztonsági mentési oldal blobból. A [PutPage](/rest/api/storageservices/Put-Page) használatával a biztonsági mentési oldal blobjának növekményes módosításait írhatja.
6. Készítsen pillanatképet a biztonsági mentés oldal blob *mybackupstdpageblob*, amelynek neve *mybackupstdpageblob_ss2*. Törölje az előző pillanatkép- *mypremiumdisk_ss1* a Premium Storage-fiókból.
7. Ismételje meg a 4-6. lépést minden biztonsági mentési ablakon. Ily módon a *mypremiumdisk* biztonsági másolatait egy standard Storage-fiókban is megtarthatja.

![Lemez biztonsági mentése növekményes Pillanatképek használatával](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>A lemezek pillanatképből való visszaállításának lépései
A következő lépések azt ismertetik, hogyan állíthatja vissza a prémium szintű lemezt, *mypremiumdisk* a Backup Storage-fiók *mybackupstdaccount* egy korábbi pillanatképre.

1. Adja meg azt az időpontot, ameddig a prémium szintű lemezt vissza kívánja állítani. Tegyük fel, hogy pillanatkép *mybackupstdpageblob_ss2*, amely a Backup Storage-fiók *mybackupstdaccount* van tárolva.
2. A mybackupstdaccount-ben léptesse elő a pillanatkép- *mybackupstdpageblob_ss2* az új biztonsági mentési alapoldal blob- *mybackupstdpageblobrestored*.
3. Készítsen pillanatképet a visszaállított biztonsági mentési oldal blobról, amelynek neve *mybackupstdpageblobrestored_ss1*.
4. Másolja a visszaállított oldal blob- *mybackupstdpageblobrestored* a *mybackupstdaccount* -ről a *mypremiumaccount* -re az új prémium szintű lemez *mypremiumdiskrestored*.
5. Készítsen pillanatképet a *mypremiumdiskrestored*-ről, amely a későbbi növekményes biztonsági másolatok készítéséhez *mypremiumdiskrestored_ss1* .
6. Irányítsa a DS sorozatú virtuális gépet a visszaállított lemez *mypremiumdiskrestored* , és válassza le a régi *mypremiumdisk* a virtuális gépről.
7. Indítsa el az előző szakaszban leírt biztonsági mentési folyamatot a visszaállított lemez *mypremiumdiskrestored*, a *mybackupstdpageblobrestored* a biztonsági mentési oldal blobjának használatával.

![Lemez visszaállítása pillanatképből](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Következő lépések
A következő hivatkozások segítségével többet tudhat meg a blob Pillanatképek létrehozásáról és a virtuális gépek biztonsági mentési infrastruktúrájának megtervezéséről.

* [BLOB pillanatképének létrehozása](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [A virtuális gép biztonsági mentési infrastruktúrájának megtervezése](../articles/backup/backup-azure-vms-introduction.md)