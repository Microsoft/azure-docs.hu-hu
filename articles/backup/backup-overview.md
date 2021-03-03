---
title: Mi az az Azure Backup?
description: Áttekintést nyújt a Azure Backup szolgáltatásról, valamint arról, hogy miként járul hozzá az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához.
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 2879d104d534733651ba76ab5521541adad0a51c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725356"
---
# <a name="what-is-the-azure-backup-service"></a>Mi az az Azure App Service?

Az Azure Backup szolgáltatás egyszerű, biztonságos és költséghatékony megoldásokat kínál adatai biztonsági mentéséhez és a Microsoft Azure-felhőből történő helyreállításához.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Mire használhatom a biztonsági mentést?

- **Helyszíni biztonsági** mentés fájlok, mappák, rendszerállapotok a [Microsoft Azure Recovery Services (MARS) ügynök](backup-support-matrix-mars-agent.md)használatával. Vagy használja a DPM vagy a Azure Backup Server (MABS) ügynököt a helyszíni virtuális gépek ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) és [VMware](backup-azure-backup-server-vmware.md)) és más helyszíni [munkaterhelések](backup-mabs-protection-matrix.md) elleni védelemhez
- **Azure-beli virtuális gépek**  -  [Készítsen biztonsági másolatot a teljes Windows/Linux rendszerű virtuális gépekről](backup-azure-vms-introduction.md) (a biztonsági mentési bővítményekkel), vagy készítsen biztonsági másolatot a fájlokról, mappákról és rendszerállapotról a [Mars-ügynök](backup-azure-manage-mars.md)
- **Azure-Managed Disks**  -  [Azure Managed Disks biztonsági mentése (előzetes verzió)](backup-managed-disks.md)
- **Azure Files megosztások**  -  [Azure-fájlmegosztás biztonsági mentése egy Storage-fiókba](backup-afs.md)
- **SQL Server Azure-beli virtuális gépeken**  -   [Azure-beli virtuális gépeken futó SQL Server adatbázisok biztonsági mentése](backup-azure-sql-database.md)
- **Adatbázisok SAP HANA Azure-beli virtuális gépeken**  -  [Azure-beli virtuális gépeken futó SAP HANA-adatbázisok biztonsági mentése](backup-azure-sap-hana-database.md)
- **Azure Database for PostgreSQL-kiszolgálók (előzetes verzió)**  -   [Készítsen biztonsági mentést az Azure PostgreSQL-adatbázisokról, és őrizze meg a biztonsági mentéseket akár 10 évig](backup-azure-database-postgresql.md)
- **Azure-Blobok (előzetes verzió)**  -  [Azure-Blobok operatív biztonsági mentésének áttekintése (előzetes verzió)](blob-backup-overview.md)

![Azure Backup áttekintése](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?

A Azure Backup a következő kulcsfontosságú előnyöket nyújtja:

- Helyszíni **biztonsági mentés kiszervezése**: Azure Backup egyszerű megoldást kínál a helyszíni erőforrások felhőbe történő biztonsági mentéséhez. Rövid és hosszú távú biztonsági mentést készíthet, anélkül, hogy bonyolult, helyszíni biztonsági mentési megoldásokat kellene üzembe helyeznie.
- **Azure IaaS virtuális gépek biztonsági mentése**: Azure Backup független és elkülönített biztonsági mentéseket biztosít az eredeti adatvesztés elleni védelemhez. A biztonsági másolatok egy helyreállítási tárban vannak tárolva, a helyreállítási pontok beépített kezelésével. A konfiguráció és a méretezhetőség egyszerű, a biztonsági mentések optimalizáltak, és szükség esetén egyszerűen visszaállíthatók.
- **Könnyen méretezhető** – a Azure Backup az Azure-felhő mögöttes teljesítményének és korlátlan skálájának használatával biztosítja a magas rendelkezésre állást karbantartási vagy figyelési terhelés nélkül.
- **Korlátlan adatátviteli** lehetőség: a Azure Backup nem korlátozza az átvitt bejövő vagy kimenő adatok mennyiségét, vagy a továbbított adatok díját.
  - Kimenő adatokon a Recovery Services-tárolóból a visszaállítási művelet során átvitt adatok értendők.
  - Ha az Azure import/export szolgáltatással nagy mennyiségű adat importálására szolgáló offline kezdeti biztonsági mentést hajt végre, akkor a rendszer a bejövő adatokhoz kapcsolódó költségeket is felhasználja.  [További információk](backup-azure-backup-import-export.md).
- **Adatbiztonság megőrzése**: Azure Backup biztosít olyan megoldásokat, amelyekkel biztonságossá teheti az adatátvitelt és [a nyugalmi](backup-azure-security-feature-cloud.md) [állapotban lévő](backup-azure-security-feature.md) adatok védelmét.
- **Központosított figyelés és felügyelet**: a Azure Backup [beépített monitorozási és riasztási képességeket](backup-azure-monitoring-built-in-monitor.md) biztosít egy Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. A figyelés és a jelentéskészítés méretezését a [Azure monitor használatával](backup-azure-monitoring-use-azuremonitor.md)is növelheti.
- Alkalmazás **-konzisztens biztonsági másolatok beolvasása**: az alkalmazás-konzisztens biztonsági mentés azt jelenti, hogy egy helyreállítási pont rendelkezik az összes szükséges adattal a biztonsági másolat visszaállításához. A Azure Backup az alkalmazással konzisztens biztonsági mentéseket biztosít, amelyek biztosítják, hogy az adatokat nem kell visszaállítani további javításokkal. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.
- **Rövid és hosszú távú adatok megőrzése**: [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tárolókat használhat a rövid és hosszú távú adatmegőrzéshez.
- **Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Azure Backup esetén a helyszíni tárolóeszközök használata díjmentes. Az Azure Backup automatikusan foglalja le és kezeli a Backup-tárolót, és használatalapú modellt alkalmaz. Így csak a felhasznált tárterületért kell fizetnie. [További](https://azure.microsoft.com/pricing/details/backup) információ a díjszabásról.
- **Több tárolási lehetőség** – Azure Backup háromféle replikálási lehetőséget kínál a tárterület/az adatmennyiség nagyfokú rendelkezésre állása érdekében.
  - A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) háromszor replikálja az adatait (az adatait három példányban hozza létre) egy adatközpont tárolási méretezési egységében. Az adatok összes másolata ugyanabban a régióban található. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.
  - A [geo-redundáns tárolás (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) az alapértelmezett és ajánlott replikációs lehetőség. A GRS az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS több mint LRS, de a GRS magasabb szintű tartósságot biztosít az adataihoz, még akkor is, ha van regionális leállás.
  - A [Zone-redundáns tárolás (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) a [rendelkezésre állási zónákban](../availability-zones/az-overview.md#availability-zones)replikálja az adatait, és biztosítja az adattárolást és a rugalmasságot ugyanabban a régióban. A ZRS nem rendelkezik állásidővel. Így az [adattárolást](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)igénylő kritikus fontosságú munkaterhelések, valamint az állásidő nélkül is készíthető biztonsági mentés a ZRS-ben.

## <a name="next-steps"></a>Következő lépések

- [Tekintse át](backup-architecture.md) a különböző biztonsági mentési forgatókönyvek architektúráját és összetevőit.
- [Ellenőrizze](backup-support-matrix.md) a biztonsági mentés támogatási követelményeit és korlátozásait, valamint az [Azure virtuális gépek biztonsági mentését](backup-support-matrix-iaas.md).
