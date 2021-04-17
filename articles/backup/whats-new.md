---
title: Az Azure Backup újdonságai
description: További tudnivalók a Azure Backup.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 68e0e5cc0876840c30ab9e428a2b96bd7d667756
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516331"
---
# <a name="whats-new-in-azure-backup"></a>Az Azure Backup újdonságai

Azure Backup folyamatosan fejleszti és új funkciókat ad ki, amelyek javítják az Azure-beli adatok védelmét. Ezek az új funkciók kiterjesztik az adatvédelmet az új számítási feladatokra, növelik a biztonságot és javítják a biztonsági mentési adatok rendelkezésre állását. Emellett új felügyeleti, monitorozási és automatizálási képességeket is biztosítanak.

Az új kiadásokról további információt a lap könyvjelzőivel vagy a frissítésekre való [feliratkozáskor itt olvashat.](https://azure.microsoft.com/updates/?query=backup)

## <a name="updates-summary"></a>Frissítések összegzése

- 2021. március
  - [Általánosan elérhető az Azure Disk Backup](#azure-disk-backup-is-now-generally-available)
  - [A Backup Center mostantól általánosan elérhető](#backup-center-is-now-generally-available)
  - [Archív szint támogatása Azure Backup (előzetes verzió)](#archive-tier-support-for-azure-backup-in-preview)
- 2021. február
  - [Biztonsági mentés az Azure Blobshoz (előzetes verzió)](#backup-for-azure-blobs-in-preview)
- 2021. január
  - [Azure Disk Backup (előzetes verzió)](#azure-disk-backup-in-preview)
  - [Titkosítás az ügyfelek által felügyelt kulcsokkal (általánosan elérhető)](#encryption-at-rest-using-customer-managed-keys)
- 2020. november
  - [Azure Resource Manager sablon az Azure-fájlmegosztás (AFS) biztonsági mentéséhez](#azure-resource-manager-template-for-afs-backup)
  - [Növekményes biztonsági SAP HANA Azure-beli virtuális gépeken (előzetes verzió)](#incremental-backups-for-sap-hana-databases-in-preview)
- 2020. szeptember
  - [Backup Center (előzetes verzió)](#backup-center-in-preview)
  - [Biztonsági Azure Database for PostgreSQL (előzetes verzió)](#backup-azure-database-for-postgresql-in-preview)
  - [Szelektív lemezes biztonsági mentés és visszaállítás](#selective-disk-backup-and-restore)
  - [Régiók közötti visszaállítás SQL Server és SAP HANA Azure-beli virtuális gépeken (előzetes verzió)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Legfeljebb 32 lemezzel (általánosan elérhető) virtuális gépek biztonsági mentésének támogatása](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Az SQL egyszerűsített biztonsági mentési konfigurációs élménye Azure-beli virtuális gépeken](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [Biztonsági SAP HANA RHEL Azure Virtual Machines (előzetes verzió)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [Zónaredundáns tárolás (ZRS) biztonsági mentési adatokhoz (előzetes verzió)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Az Azure-beli SQL Server és SAP HANA számítási feladatok soft delete parancsa](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>Általánosan elérhető az Azure Disk Backup

Azure Backup pillanatkép-életciklus-kezelés az Azure Managed Disks számára a pillanatképek rendszeres létrehozásának automatizálásával és a beállított időtartamok számára való megőrzésével a Backup szabályzat használatával.

További információ: [Overview of Azure Disk Backup (Az Azure Disk Backup áttekintése).](disk-backup-overview.md)

## <a name="backup-center-is-now-generally-available"></a>A Backup Center mostantól általánosan elérhető

A Backup Center leegyszerűsíti az adatvédelem nagy léptékű kezelését azáltal, hogy lehetővé teszi egyetlen központi konzolról a biztonsági mentések felügyeletének felderítését, szabályozását, figyelét, működtetését és optimalizálását.

További információ: [A Backup Center áttekintése.](backup-center-overview.md)

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Archív szint támogatása Azure Backup (előzetes verzió)

Azure Backup lehetővé teszi, hogy csökkentse a hosszú távú megőrzési biztonsági mentések költségeit az Azure-beli virtuális gépek archív rétegének és az Azure-beli virtuális gépeken SQL Server tárolási rétegnek köszönhetően.

További információ: [Archív szint támogatása (előzetes verzió).](archive-tier-support.md)

## <a name="backup-for-azure-blobs-in-preview"></a>Biztonsági mentés az Azure Blobshoz (előzetes verzió)

A blobok operatív biztonsági mentése egy felügyelt, helyi adatvédelmi megoldás, amely lehetővé teszi a blokkblobok védelmét a különféle adatvesztési forgatókönyvekkel szemben, például a sérülésekkel, a blobtörlésekkel és a tárfiók véletlen törlésével szemben. Az adatok helyileg, magában a forrás tárfiókban tárolódnak, és szükség esetén helyreállíthatóak egy kiválasztott időpontra. Így egyszerű, biztonságos és költséghatékony módon védi a blobokat.

A blobok működési biztonsági mentése integrálható a Backup Centerbe, valamint a Biztonsági mentések kezelési képességeivel, így egyetlen ablaktáblán felügyelheti, monitorozhatja, üzemeltetheti és elemezheti a biztonsági másolatokat nagy méretekben.

További információ: [Overview of operational backup for Azure Blobs (in preview) (Az Azure Blobs operatív](blob-backup-overview.md)biztonsági mentésének áttekintése (előzetes verzióban) ).

## <a name="azure-disk-backup-in-preview"></a>Azure Disk Backup (előzetes verzió)

Az Azure Disk Backup egy kulcsrakész megoldást kínál, amely pillanatkép-életciklus-felügyeleti funkciókat biztosít az [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) számára azáltal, hogy automatizálja a pillanatképek rendszeres létrehozását, és a biztonsági mentési szabályzat használatával egy konfigurált időtartamra megőrzi azt. A lemez pillanatképei infrastruktúraköltség nélkül kezelhetők, és nincs szükség egyéni szkriptkezelésre vagy felügyeleti többletterhelésre. Ez egy összeomlás-konzisztens biztonsági mentési megoldás, amely [](../virtual-machines/disks-incremental-snapshots.md) több napi biztonsági mentést támogató növekményes pillanatképek használatával készít időponthoz készült biztonsági mentést egy felügyelt lemezről. Emellett ügynök nélkül működik, és nincs hatással az éles alkalmazások teljesítményére. Támogatja az operációs rendszer és az adatlemezek biztonsági mentését és visszaállítását (beleértve a megosztott lemezeket is), függetlenül attól, hogy jelenleg csatlakoznak-e egy futó Azure-beli virtuális géphez.

További információ: [Azure Disk Backup (előzetes verzióban).](disk-backup-overview.md)

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Titkosítás az ügyfelek által kezelt kulcsokkal

Általánosan elérhető az ügyfelek által kezelt kulcsokkal való titkosítás támogatása. Így titkosíthatja a Helyreállítási tárban tárolt biztonsági mentési adatokat az Azure Key Vaultban tárolt saját kulcsokkal. A helyreállítási tárban a biztonsági másolatok titkosításához használt titkosítási kulcs különbözhet a forrás titkosításához használt kulcstól. Az adatok védelme egy AES 256-alapú adattitkosítási kulccsal (DEK), amely pedig a tárolóban tárolt kulcsokkal Key Vault. A platform által felügyelt kulcsokkal (alapértelmezés szerint elérhető) titkosítással szemben ez nagyobb irányítást biztosít a kulcsok felett, és segít jobban megfelelni a megfelelőségi igényeknek.

További információ: Biztonsági mentési adatok titkosítása ügyfél által kezelt [kulcsokkal.](encryption-at-rest-with-cmk.md)

## <a name="azure-resource-manager-template-for-afs-backup"></a>Azure Resource Manager sablon az AFS biztonsági mentéséhez

Azure Backup már támogatja a meglévő Azure-fájlmegosztások biztonsági mentésének konfigurálását Azure Resource Manager (ARM) sablon használatával. A sablon egy meglévő Azure-fájlmegosztás védelmét konfigurálja a helyreállítási tár és a biztonsági mentési szabályzat megfelelő részleteinek megadásával. Igény szerint új Recovery Services-tárolót és biztonsági mentési szabályzatot is létrehoz, és regisztrálja a fájlmegosztást tartalmazó tárfiókot a Recovery Services-tárolóban.

További információ: Azure Resource Manager [sablonjainak Azure Backup.](backup-rm-template-samples.md)

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Növekményes biztonsági mentések SAP HANA adatbázisokhoz (előzetes verzióban)

Azure Backup már támogatja az Azure-beli virtuális gépeken SAP HANA adatbázisok növekményes biztonsági mentését. Ez gyorsabb és költséghatékonyabb biztonsági mentést tesz lehetővé a SAP HANA adatairól.

További információkért tekintse [meg](/sap-hana-faq-backup-azure-vm.yml#policy) a biztonsági mentési szabályzatok létrehozása során elérhető különböző lehetőségeket, valamint a biztonsági mentési szabályzat létrehozását SAP HANA [adatbázisokhoz.](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)

## <a name="backup-center-in-preview"></a>Backup Center (előzetes verzió)

Azure Backup egy új natív felügyeleti képességet, amely lehetővé teszi a teljes biztonsági mentési tulajdon kezelését egy központi konzolról. A Backup Center lehetővé teszi az adatvédelem nagy léptékű monitorozható, működtethető, irányítható és optimalizálható, egységes módon, az Azure natív felügyeleti megoldásokkal összhangban való felügyeletét.

A Backup Center segítségével összesítő nézetet kap a leltárról az előfizetések, helyek, erőforráscsoportok, tárolók és akár bérlők Azure Lighthouse. A Backup Center egy műveletközpont is, ahol egyetlen helyről aktiválhatja a biztonsági mentéssel kapcsolatos tevékenységeket, például a biztonsági mentés konfigurálását, visszaállítását, szabályzatok vagy tárolók létrehozását. Emellett a közvetlen integráció és a Azure Policy lehetővé teszi a környezet szabályozását és a megfelelőség biztonsági mentésből való nyomon követését. A biztonsági mentések nagy léptékű konfigurálásán Azure Backup azure-szabályzatok is lehetővé teszik.

További információ: [A Backup Center áttekintése.](backup-center-overview.md)

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Biztonsági Azure Database for PostgreSQL (előzetes verzió)

Azure Backup Azure Database Services és az Azure Database Services együttesen egy nagyvállalati szintű biztonsági mentési megoldást hoznak létre az Azure PostgreSQL-hez (amely jelenleg előzetes verzióban érhető el). Most már megfelelhet az adatvédelmi és megfelelőségi igényeinek egy ügyfél által vezérelt biztonsági mentési szabályzattal, amely akár 10 évig is lehetővé teszi a biztonsági másolatok megőrzését. Így részletesen szabályozhatja a biztonsági mentési és visszaállítási műveleteket az egyes adatbázisok szintjén. Hasonlóképpen, könnyedén visszaállíthat PostgreSQL-verziókat vagy blobtárolóba.

További információ: biztonsági [Azure Database for PostgreSQL készítése.](backup-azure-database-postgresql.md)

## <a name="selective-disk-backup-and-restore"></a>Szelektív lemezes biztonsági mentés és visszaállítás

Azure Backup támogatja a virtuális gép összes lemezének (operációs rendszernek és adatnak) együttes biztonsági mentését a virtuális gép biztonsági mentési megoldásával. A szelektív lemezek biztonsági mentési és visszaállítási funkcióját használva mostantól biztonsági másolatot készíthet a virtuális gépeken lévő adatlemezek egy részével. Ez hatékony és költséghatékony megoldást kínál a biztonsági mentési és visszaállítási igényekhez. Minden helyreállítási pont csak azokat a lemezeket tartalmazza, amelyek a biztonsági mentési műveletben szerepelnek.

További információ: Azure-beli virtuális gépek szelektív lemezes biztonsági [mentése és visszaállítása.](selective-disk-backup-restore.md)

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Régiók közötti visszaállítás SQL Server és SAP HANA (előzetes verzióban)

A régiók közötti visszaállítás bevezetésével azonnal kezdeményezhet visszaállításokat egy másodlagos régióban a környezet elsődleges régiójában a valós állásidővel kapcsolatos problémák csökkentése érdekében. Így a másodlagos régió teljesen ügyfél által vezérelt lesz. Azure Backup a másodlagos régióba replikált biztonsági másolatokat használja az ilyen visszaállításhoz.

Mostantól az Azure-beli virtuális gépek régiók közötti visszaállításának támogatása mellett a funkció ki lett terjeszteni az SQL- és SAP HANA-adatbázisok Azure-beli virtuális gépeken való visszaállítására is.

További információkért lásd: Régiók közötti [visszaállítás SQL-adatbázisok esetén](restore-sql-database-azure-vm.md#cross-region-restore) és Régiók közötti visszaállítás SAP HANA adatbázisok [esetén.](sap-hana-db-restore.md#cross-region-restore)

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Legfeljebb 32 lemezzel lévő virtuális gépek biztonsági mentésének támogatása

Eddig a Azure Backup virtuális gépenként 16 felügyelt lemezt támogatott. A Azure Backup virtuális gépenként legfeljebb 32 felügyelt lemez biztonsági mentését támogatja.

További információkért lásd a [virtuálisgép-tárolók támogatási mátrixát.](backup-support-matrix-iaas.md#vm-storage-support)

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Egyszerűbb biztonsági mentési konfiguráció sql-hez Azure-beli virtuális gépeken

Az Azure-beli virtuális gépeken SQL Server biztonsági másolatok konfigurálása mostantól még egyszerűbb, ha a beágyazott biztonsági mentés konfigurációja a virtuális gép paneljére van Azure Portal. Mindössze néhány lépésben engedélyezheti a biztonsági mentést a SQL Server a meglévő adatbázisok és a jövőben hozzáadott adatbázisok védelme érdekében.

További információ: [Biztonsági SQL Server a virtuális gép paneljére.](backup-sql-server-vm-from-vm-pane.md)

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>Biztonsági SAP HANA RHEL Azure-beli virtuális gépeken (előzetes verzió)

Azure Backup az Azure natív biztonsági mentési megoldása, és az SAP BackInt minősítéssel rendelkezik. Azure Backup már támogatja a Red Hat Enterprise Linux (RHEL), amely az egyik legszélesebb körben használt Linux operációs rendszer, SAP HANA.

További információkért lásd a SAP HANA biztonsági mentési [forgatókönyv támogatási mátrixát.](sap-hana-backup-support-matrix.md#scenario-support)

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>Zónaredundáns tárolás (ZRS) biztonsági mentési adatokhoz (előzetes verzió)

Az Azure Storage kiváló egyensúlyt biztosít a nagy teljesítmény, a magas rendelkezésre állás és a nagy adattűrés között a különböző redundanciával. Azure Backup biztonsági mentésre is kiterjesztheti ezeket az előnyöket, a biztonsági másolatok helyileg redundáns tárolásban (LRS) és georedundáns tárolásban (GRS) is tárolhatók. Most további tartóssági lehetőségek is rendelkezésre állnak a zónaredundáns tárolás (ZRS) további támogatásával.

További információ: [Set storage redundancy for the Recovery Services vault (Tárolóredundania beállítása a Recovery Services-tárolóhoz).](backup-create-rs-vault.md#set-storage-redundancy)

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>A számítási feladatokhoz SQL Server SAP HANA törlés

Egyre nagyobbak a biztonsági problémákkal, például a kártevő szoftverekkel, a zsarolóprogramokkal és a behatolással kapcsolatos aggodalmak. Ezek a biztonsági problémák költségesek, mind a pénz, mind az adatok szempontjából. Az ilyen támadások elleni védelem érdekében a Azure Backup biztonsági funkciókat biztosít a biztonsági mentési adatok védelméhez még a törlés után is.

Az egyik ilyen funkció a soft delete. A helyreállított törléssel még akkor is megőrizzük a biztonsági mentési adatokat 14 további napig, ha egy rosszindulatú szereplő töröl egy biztonsági másolatot (vagy véletlenül törli a biztonsági mentési adatokat), így a rendszer adatvesztés nélkül helyreállítja a biztonsági mentési elemet. A "soft delete" állapotban a biztonsági másolatok adatainak további 14 napos megőrzési időszaka nem jár költséggel.

Mostantól az Azure-beli virtuális gépek esetében a SQL Server és SAP HANA azure-beli virtuális gépeken a gépeken a soft delete szolgáltatás is védve van.

További információkért lásd az Azure-beli virtuális gépen található SQL Server és az [Azure-beli](soft-delete-sql-saphana-in-azure-vm.md)virtuális SAP HANA gépek számítási feladataiban való működést.

## <a name="next-steps"></a>Következő lépések

- [Azure Backup útmutató és ajánlott eljárások](guidance-best-practices.md)