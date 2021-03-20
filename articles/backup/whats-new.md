---
title: Az Azure Backup újdonságai
description: Ismerkedjen meg a Azure Backup új szolgáltatásaival.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 48c8c189d956c4003d24511a54975fe86a982832
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095714"
---
# <a name="whats-new-in-azure-backup"></a>Az Azure Backup újdonságai

A Azure Backup folyamatosan fejleszti és felszabadítja az Azure-ban tárolt adatai védelmét javító új funkciókat. Ezek az új funkciók kibővítik az adatvédelmet az új számítási feladatokra, javítják a biztonságot, és javítják a biztonsági mentési adatok rendelkezésre állását. Emellett új felügyeleti, monitorozási és automatizálási képességeket is felvehetnek.

Az új kiadásokról további információt az oldal könyvjelzővel vagy a [frissítésekre való feliratkozással](https://azure.microsoft.com/updates/?query=backup)olvashat.

## <a name="updates-summary"></a>Frissítések összegzése

- Március 2021
  - [A Backup Center már általánosan elérhető](#backup-center-is-now-generally-available)
  - [A Azure Backup archiválási szintjeinek támogatása (előzetes verzió)](#archive-tier-support-for-azure-backup-in-preview)
- 2021. február
  - [Azure-Blobok biztonsági mentése (előzetes verzió)](#backup-for-azure-blobs-in-preview)
- 2021. január
  - [Azure Disk Backup (előzetes verzió)](#azure-disk-backup-in-preview)
  - [Inaktív adatok titkosítása az ügyfél által felügyelt kulcsokkal (általánosan elérhető)](#encryption-at-rest-using-customer-managed-keys)
- 2020. november
  - [Az Azure file share (AFS) biztonsági mentésének Azure Resource Manager sablonja](#azure-resource-manager-template-for-afs-backup)
  - [Az Azure-beli virtuális gépeken futó SAP HANA adatbázisok növekményes biztonsági mentései (előzetes verzió)](#incremental-backups-for-sap-hana-databases-in-preview)
- 2020. szeptember
  - [Backup Center (előzetes verzió)](#backup-center-in-preview)
  - [Backup Azure Database for PostgreSQL (előzetes verzió)](#backup-azure-database-for-postgresql-in-preview)
  - [Szelektív lemez biztonsági mentése és visszaállítása](#selective-disk-backup-and-restore)
  - [Régiók közötti visszaállítás az Azure-beli virtuális gépeken lévő SQL Server és SAP HANA adatbázisokhoz (előzetes verzió)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [A legfeljebb 32 lemezzel rendelkező virtuális gépek biztonsági mentésének támogatása (általánosan elérhető)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Az SQL Azure-beli virtuális gépeken való egyszerűsített biztonsági mentési konfigurációs felülete](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Backup SAP HANA a RHEL Azure Virtual Machines (előzetes verzió)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [A zóna redundáns tárolója (ZRS) a biztonsági mentési adatként (előzetes verzió)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Az Azure-beli virtuális gépek SQL Server és SAP HANA számítási feladatainak törlése](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="backup-center-is-now-generally-available"></a>A Backup Center már általánosan elérhető

A Backup Center egyszerűbbé teszi az adatvédelem felügyeletét azáltal, hogy lehetővé teszi a biztonsági mentési felügyelet felderítését, irányítását, figyelését, üzemeltetését és optimalizálását egyetlen központi konzolról.

További információ: [a Backup Center áttekintése](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>A Azure Backup archiválási szintjeinek támogatása (előzetes verzió)

A Azure Backup mostantól lehetővé teszi a hosszú távú adatmegőrzési biztonsági másolatok költségeit az Azure-beli virtuális gépekre vonatkozó archív szint és az Azure Virtual Machines szolgáltatásban SQL Server.

További információ: az [archiválási szint támogatása (előzetes verzió)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Azure-Blobok biztonsági mentése (előzetes verzió)

A Blobok működési biztonsági mentése egy felügyelt, helyi adatvédelmi megoldás, amely lehetővé teszi a blokk Blobok védelmét a különböző adatvesztési forgatókönyvek, például a korrupció, a Blobok törlése, valamint a véletlen tárolási fiókok törlésével szemben. Az adattárolást a forrás Storage-fiókban helyileg tárolják, és szükség esetén a kiválasztott időpontra lehet visszaállítani. Így egyszerű, biztonságos és költséghatékony eszközöket biztosít a Blobok védelméhez.

A Blobok működési biztonsági mentése a Backup Center szolgáltatással együtt integrálható a biztonsági mentési felügyeleti funkciók között, így egyetlen üvegtábla biztosítható, amely a biztonsági másolatok szabályozására, figyelésére, üzemeltetésére és elemzésére használható.

További információ: [Az Azure-Blobok operatív biztonsági mentésének áttekintése (előzetes verzió)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Azure Disk Backup (előzetes verzió)

Az Azure Disk Backup olyan kulcsrakész megoldást kínál, amely a pillanatképek életciklusának kezelését teszi lehetővé az [azure Managed Disks](../virtual-machines/managed-disks-overview.md) számára a pillanatképek rendszeres létrehozásával, és a biztonsági mentési házirend használatával megőrzi a beállított időtartamot. A lemez-pillanatképeket az infrastrukturális költségek nélkül kezelheti, és nincs szükség egyéni parancsfájlok vagy felügyeleti terhelésre. Ez egy összeomlás-konzisztens biztonsági mentési megoldás, amely egy felügyelt lemez adott időpontban történő biztonsági mentését teszi lehetővé [növekményes Pillanatképek](../virtual-machines/disks-incremental-snapshots.md) használatával, és naponta több biztonsági mentést támogat. Ez egy ügynök nélküli megoldás is, amely nem befolyásolja az üzemi alkalmazások teljesítményét. Támogatja az operációs rendszer és az adatlemezek (beleértve a megosztott lemezeket is) biztonsági mentését és visszaállítását, függetlenül attól, hogy jelenleg egy futó Azure-beli virtuális géphez vannak-e csatolva.

További információ: [Azure Disk Backup (előzetes verzió)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Inaktív adatok titkosítása az ügyfél által felügyelt kulcsok használatával

Az ügyfelek által felügyelt kulcsok használatával már általánosan elérhető az inaktív titkosítás támogatása. Ez lehetővé teszi, hogy az Azure Key Vaultban tárolt saját kulcsokkal Titkosítsa a Recovery Services-tárolók biztonsági másolati adatait. A Recovery Services-tárolóban található biztonsági másolatok titkosításához használt titkosítási kulcs különbözhet a forrás titkosításához használttól. Az adatvédelmet AES 256-alapú adattitkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) védi, amely a Key Vaultban tárolt kulcsok használatával védett. A platform által felügyelt kulcsokkal (amely alapértelmezés szerint elérhető) a titkosításhoz képest nagyobb mértékben szabályozhatja a kulcsokat, és jobban teljesítheti a megfelelőségi igényeket.

További információ: [biztonsági mentési adatok titkosítása az ügyfél által felügyelt kulcsok használatával](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Az AFS biztonsági mentésének Azure Resource Manager sablonja

Azure Backup mostantól támogatja a meglévő Azure-fájlmegosztás biztonsági mentésének konfigurálását egy Azure Resource Manager-(ARM-) sablon használatával. A sablon egy meglévő Azure-fájlmegosztás védelmét konfigurálja úgy, hogy megadja a Recovery Services tár és a biztonsági mentési szabályzat megfelelő részleteit. Opcionálisan létrehoz egy új Recovery Services-tárolót és egy biztonsági mentési szabályzatot, és regisztrálja a fájlmegosztást tartalmazó Storage-fiókot a Recovery Services-tárolóban.

További információ: Azure Resource Manager- [sablonok Azure Backuphoz](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>SAP HANA adatbázisok növekményes biztonsági mentései (előzetes verzió)

A Azure Backup mostantól támogatja az Azure-beli virtuális gépeken üzemeltetett SAP HANA-adatbázisok növekményes biztonsági mentését. Ez lehetővé teszi, hogy a SAP HANA-adatai gyorsabb és költséghatékonyabb biztonsági mentést készítsenek.

További információkért tekintse meg [a biztonsági mentési szabályzat létrehozása során elérhető különböző lehetőségeket](sap-hana-faq-backup-azure-vm.md#policy) , valamint [a SAP HANA adatbázisok biztonsági mentési szabályzatának létrehozását](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)ismertető témakört.

## <a name="backup-center-in-preview"></a>Backup Center (előzetes verzió)

A Azure Backup lehetővé tette, hogy egy új natív felügyeleti képességgel kezelhesse a teljes biztonsági mentési hagyatékot egy központi konzolról. A Backup Center lehetővé teszi, hogy az Azure natív felügyeleti tapasztalataival összhangban egységes módon figyelje, működtesse, szabályozza és optimalizálja az adatvédelem méretezését.

A Backup Center segítségével összesített képet kaphat a leltárról az előfizetések, a helyszínek, az erőforráscsoportok, a tárolók és még a bérlők között az Azure Lighthouse használatával. A Backup Center egyben egy műveleti központ is, amelyből kiválthatja a biztonsági másolatok kapcsolódó tevékenységeit, például a biztonsági mentést, a visszaállítást, a házirendek vagy tárolók létrehozását, mindezt egyetlen helyről. A Azure Policy zökkenőmentes integrációja mellett mostantól szabályozhatja a környezetet, és nyomon követheti a megfelelőséget egy biztonsági mentési perspektívában. A Azure Backupra jellemző, beépített Azure-szabályzatok lehetővé teszik a biztonsági másolatok méretezését is.

További információ: [a Backup Center áttekintése](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Backup Azure Database for PostgreSQL (előzetes verzió)

A Azure Backup és az Azure Database Services együttesen létrehoz egy nagyvállalati szintű biztonsági mentési megoldást az Azure PostgreSQL-hez (mostantól előzetes verzióban érhető el). Az adatvédelmi és megfelelőségi igényeknek megfelelően egy ügyfél által vezérelt biztonsági mentési szabályzattal is találkozhat, amely lehetővé teszi a biztonsági másolatok megőrzését akár 10 évig. Ezzel a részletes szabályozással felügyelheti a biztonsági mentési és visszaállítási műveleteket az egyes adatbázis szintjén. Hasonlóképpen, a PostgreSQL-verziókon vagy a blob Storage-ban is könnyedén visszaállíthatók.

További információ: [Azure Database for PostgreSQL biztonsági mentés](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Szelektív lemez biztonsági mentése és visszaállítása

Azure Backup támogatja a virtuális gép összes lemezének (operációs rendszerének és adattípusának) biztonsági mentését egy virtuális gépen, a virtuális gépek biztonsági mentési megoldásával együtt. Most, hogy a szelektív lemezek biztonsági mentési és visszaállítási funkcióját használja, biztonsági mentést készíthet a virtuális gépek adatlemezei egy részhalmazáról. Ez hatékony és költséghatékony megoldást kínál a biztonsági mentési és visszaállítási igények kielégítésére. Az egyes helyreállítási pontok csak a biztonsági mentési művelet részét képező lemezeket tartalmazzák.

További információ: [szelektív lemezek biztonsági mentése és visszaállítása az Azure Virtual Machines szolgáltatásban](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Régiók közötti visszaállítás SQL Server és SAP HANA (előzetes verzióban)

A régiók közötti visszaállítás bevezetésével mostantól egy másodlagos régióban visszaállíthatja a visszatárolást, amely csökkenti a valós állásidőt a környezete elsődleges régiójában. Így a másodlagos régió teljesen visszaállítja az ügyfél által vezérelt vezérlést. A Azure Backup a másodlagos régióba replikált biztonsági másolatokat használja az ilyen visszaállításokhoz.

Az Azure-beli virtuális gépek régiók közötti visszaállításának támogatása mellett a szolgáltatás kibővült az SQL-és SAP HANA-adatbázisok Azure-beli virtuális gépeken való visszaállítására is.

További információkért lásd: [tartományok közötti visszaállítás az SQL-adatbázisokhoz](restore-sql-database-azure-vm.md#cross-region-restore) és [a SAP HANA-adatbázisok régiók közötti visszaállításához](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>A legfeljebb 32 lemezzel rendelkező virtuális gépek biztonsági mentésének támogatása

Eddig Azure Backup 16 felügyelt lemezt támogatott virtuális gépenként. A Azure Backup jelenleg legfeljebb 32 felügyelt lemez biztonsági mentését támogatja virtuális gépenként.

További információkért lásd a [VM Storage támogatási mátrixát](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>SQL-alapú biztonsági mentési konfiguráció az Azure-beli virtuális gépeken

Az Azure-beli virtuális gépeken lévő SQL Server biztonsági másolatainak konfigurálása mostantól még egyszerűbbé válik a Azure Portal virtuálisgép-ablaktábláján integrált beágyazott biztonsági mentési konfigurációval. Néhány lépésben engedélyezheti a SQL Server biztonsági mentését, hogy megvédje az összes meglévő adatbázist, valamint a jövőben hozzáadott fájlokat.

További információ: [SQL Server biztonsági mentése a virtuális gép paneljéről](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Backup SAP HANA a RHEL Azure Virtual Machines szolgáltatásban (előzetes verzió)

A Azure Backup az Azure natív biztonsági mentési megoldása, amelyet az SAP BackInt tanúsított. Azure Backup már hozzáadta a Red Hat Enterprise Linux (RHEL) támogatását, amely az egyik legszélesebb körben használt Linux operációs rendszer, SAP HANA fut.

További információ: [SAP HANA adatbázis biztonsági mentési forgatókönyvének támogatási mátrixa](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>A zóna redundáns tárolója (ZRS) a biztonsági mentési adatként (előzetes verzió)

Az Azure Storage nagyszerű egyensúlyt biztosít a nagy teljesítményű, magas rendelkezésre állású és magas adatrugalmassággal és a különböző redundancia-lehetőségekkel. Azure Backup lehetővé teszi, hogy kiterjessze ezeket az előnyöket a biztonsági másolatok adataira is, a biztonsági másolatok tárolásához a helyileg redundáns tárolás (LRS) és a Geo-redundáns tárolás (GRS) segítségével. Most további tartóssági lehetőségek állnak rendelkezésre a zóna redundáns tárolásának (ZRS) támogatásával.

További információ: Storage- [redundancia beállítása a Recovery Services-tárolóhoz](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server és SAP HANA számítási feladatokhoz tartozó helyreállítható törlés

A biztonsági problémákkal, például a kártevővel, a ransomware és a behatolással kapcsolatos problémák egyre nagyobbak. Ezek a biztonsági problémák költségesek lehetnek a pénz és az adatmennyiség tekintetében. Az ilyen támadások elleni védelem érdekében a Azure Backup biztonsági funkciókat biztosít a biztonsági mentési adatainak a törlés után még a védelme érdekében.

Az egyik ilyen funkció a Soft delete. A helyreállítható törléssel akkor is, ha egy rosszindulatú színész töröl egy biztonsági másolatot (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat 14 további napig tart, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. A "Soft Delete" állapotban lévő biztonsági mentési adatok megőrzésének további 14 napja nem jár semmilyen költséggel.

Az Azure-beli virtuális gépekre vonatkozó, a Soft delete támogatása mellett az Azure-beli virtuális gépeken SQL Server és SAP HANA munkaterhelések is védve vannak a Soft delete használatával.

További információ: az [Azure virtuális gépen futó SQL Server rendszerhez készült Soft delete és SAP HANA az Azure-beli virtuális](soft-delete-sql-saphana-in-azure-vm.md)gépek számítási feladataihoz.

## <a name="next-steps"></a>Következő lépések

- [Azure Backup útmutató és ajánlott eljárások](guidance-best-practices.md)