---
title: Szószedet Azure Backup
description: Ez a cikk a Azure Backup használatának hasznos feltételeit határozza meg.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502025"
---
# <a name="azure-backup-glossary"></a>Szószedet Azure Backup

A jelen Szószedet a Azure Backup használatakor hasznos lehet.

>[!NOTE]
>
> - A "(munkaterhelés-specifikus kifejezés)" előtaggal megjelölt kifejezések azokra a kifejezésekre vonatkoznak, amelyek csak a Azure Backup által támogatott munkaterhelések adott részhalmazának kontextusában relevánsak.
> - A gyakran Azure Backup dokumentációban használt kifejezések, de más Azure-szolgáltatások esetében a kapcsolódó Azure-szolgáltatás dokumentációjában közvetlen hivatkozás is található.

## <a name="afs-azure-file-shares"></a>AFS (Azure-fájlmegosztás)

Tekintse meg [Azure Files dokumentációját](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Másodlagos hely helyreállítása

A helyreállítási pontról a biztonsági mentések eredeti helyétől eltérő helyre történt helyreállítás. Az Azure-beli virtuális gépek biztonsági mentésének használata esetén ez azt jelenti, hogy a virtuális gépet a biztonsági másolatokat tartalmazó eredeti kiszolgálótól eltérő kiszolgálóra kell visszaállítani. Az Azure-fájlmegosztás biztonsági mentésének használatakor ez azt jelenti, hogy az adatok egy olyan fájlmegosztás számára történnek, amely eltér a biztonsági másolatban szereplő fájlmegosztástól.

## <a name="application-consistent-backup"></a>Alkalmazás konzisztens biztonsági mentése

(Munkaterhelés-specifikus kifejezés)

Az alkalmazással konzisztens biztonsági másolatok rögzítik a memória tartalmát és a függőben lévő I/O-műveleteket. Az alkalmazás-konzisztens Pillanatképek egy [VSS](#vss-windows-volume-shadow-copy-service) VSS-író (vagy a Linux előtti vagy utáni parancsfájlok) használatával biztosítják, hogy az alkalmazásadatok a biztonsági mentés előtt konzisztensek legyenek. [További információ](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager- (ARM-) sablonok

Tekintse meg az [ARM-sablonok dokumentációját](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Autoprotection (adatbázisokhoz)

(Munkaterhelés-specifikus kifejezés)

Az automatikus védelem olyan képesség, amely lehetővé teszi az önálló SQL Server-példányok vagy a SQL Server always on rendelkezésre állási csoport összes adatbázisának automatikus védelmét. Nem csak a meglévő adatbázisok biztonsági mentését teszi lehetővé, hanem a jövőben felvehető összes adatbázist is védi.

## <a name="availability-storage-replication-types"></a>Rendelkezésre állás (tárolási replikálási típusok)

A Azure Backup háromféle replikációt biztosít a tárterület és az adatmennyiség nagyfokú rendelkezésre állása érdekében:

### <a name="lrs"></a>LRS

A [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) háromszor replikálja a biztonsági mentési adatait (a biztonsági másolatok három másolatát hozza létre) egy adatközpont tárolási méretezési egységében. A biztonsági mentési adatmennyiség összes másolata ugyanabban a régióban található. A LRS egy alacsony díjszabású lehetőség a biztonsági mentési adatok helyi hardveres meghibásodások elleni védelméhez.

### <a name="grs"></a>GRS

A [geo-redundáns tárolás (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) az alapértelmezett és ajánlott replikációs lehetőség. A GRS egy másodlagos régióba replikálja a biztonsági mentési adatait, több száz mérfölddel arrébb a forrásadatok elsődleges helyétől. A GRS több mint LRS, de a GRS magasabb fokú tartósságot biztosít a biztonsági mentési adataihoz, még akkor is, ha van regionális leállás.

>[!NOTE]
>Azon GRS-tárolók esetében, amelyeken engedélyezve van a régiók közötti visszaállítási funkció, a biztonsági mentési tár a GRS-ből az RA-GRS (olvasási hozzáférés Geo-Redundant Storage) verzióra frissül.

### <a name="zrs"></a>ZRS

A [Zone-redundáns tárolás (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) a [rendelkezésre állási zónákban](../availability-zones/az-overview.md#availability-zones)replikálja a biztonsági mentési adatait, garantálva a biztonsági mentési adattárolást és a rugalmasságot ugyanabban a régióban. Az [adattárolást](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) igénylő kritikus fontosságú számítási feladatok biztonsági mentése a ZRS-ben végezhető el.

## <a name="azure-command-line-interface-cli"></a>Azure parancssori felület (CLI)

Tekintse meg az [Azure CLI dokumentációját](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Tekintse meg [Azure Policy dokumentációját](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Tekintse meg [Azure PowerShell dokumentációját](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Tekintse meg [Azure Resource Manager dokumentációját](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Tekintse meg [Azure Disk Encryption dokumentációját](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Háttérbeli tárolás/Felhőbeli tárolás/biztonsági mentési tár

A biztonsági mentési példány által használt tényleges tárterület. A biztonsági mentési példányhoz tartozó összes megőrzési pont méretét tartalmazza (a biztonsági mentési és adatmegőrzési Szabályzatban meghatározottak szerint).

## <a name="bare-metal-backup"></a>Operációs rendszer nélküli biztonsági mentés

Az operációs rendszer fájljainak és a kritikus köteteken lévő összes információnak a biztonsági mentése, kivéve a felhasználói adatmennyiséget. Definíció szerint az operációs rendszer nélküli biztonsági mentés magában foglalja a rendszerállapot biztonsági mentését. Védelmet biztosít, ha a számítógép nem indul el, és mindent helyre kell állítani. [További információ](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Biztonsági mentési bővítmények/virtuálisgép-bővítmények

(Az Azure-beli virtuális gépek számítási feladatainak típusára jellemző)

Az Azure virtuálisgép-bővítmények olyan kisméretű alkalmazások, amelyek üzembe helyezés utáni konfigurációs és automatizálási feladatokat biztosítanak az Azure-beli virtuális gépeken. Azure Backup biztonsági mentést készít az Azure-beli virtuális gépekről a számítógépen futó Azure virtuálisgép-ügynök bővítményének telepítésével. Azure Backup automatikusan kezeli ezeket a bővítményeket, és a felhasználóknak nem kell manuálisan frissíteniük ezeket a bővítményeket.

## <a name="backup-instance--backup-item"></a>Biztonsági másolati példány/biztonsági másolati tétel

Egy adott biztonsági mentési és adatmegőrzési szabályzattal rendelkező tárolóba biztonsági mentést készítő adatforrás biztonsági mentési példányt vagy biztonsági mentési elemeket képez.

## <a name="backup-rule--backup-policy"></a>Biztonsági mentési szabály/biztonsági mentési szabályzat

A biztonsági mentési szabály egy felhasználó által definiált szabály, amely meghatározza, hogy mikor és milyen gyakran kell elvégezni a biztonsági mentéseket egy adatforrásban. Bizonyos számítási feladatokhoz a biztonsági mentési szabályzat lehetővé teszi az adatforráson alkalmazandó pillanatkép-módszer (teljes, növekményes, különbözeti) megadását is. A biztonsági mentési szabályzatok gyakran a biztonsági mentési szabályok és a megőrzési szabályok kombinációjával jönnek létre.

## <a name="backup-vault"></a>Biztonsági mentési tár

Egy Azure Resource Manager *Microsoft. DataProtection/BackupVaults* típusú erőforrás. A Backup-tárolók jelenleg a PostgreSQL-kiszolgálóhoz készült Azure-adatbázisok biztonsági mentéséhez használhatók. [További információ a Backup-tárolók](backup-azure-recovery-services-vault-overview.md)szolgáltatásról.

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (Üzletmenet-folytonosság és vész-helyreállítás)

A BCDR magában foglalja azokat a folyamatokat, amelyeket a szervezetnek el kell fogadnia annak biztosítása érdekében, hogy az alkalmazások és a munkaterhelések a tervezett és nem tervezett szolgáltatások, illetve az Azure-kimaradások során is működőképesek legyenek. [Ismerkedjen meg](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) az Azure által kínált különböző szolgáltatásokkal, amelyek segítenek a hangBCDR stratégia létrehozásában.

## <a name="churn"></a>Köpül

A két egymást követő biztonsági mentések között elmentett adatváltozások százalékos aránya. Ez az új adatok hozzáadása, illetve a meglévő adatok módosítása vagy törlése miatt lehet.

## <a name="crash-consistent-backup"></a>Összeomlás-konzisztens biztonsági mentés

(Munkaterhelés-specifikus kifejezés)

Az összeomlás-konzisztens Pillanatképek általában akkor fordulnak elő, ha egy Azure-beli virtuális gép le van állítva a biztonsági mentés időpontjában. A rendszer csak azokat az adatmennyiségeket rögzíti, amelyek már léteznek a lemezen a biztonsági mentés során. [További információ](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Régiók közötti visszaállítás (CRR)

A [visszaállítási lehetőségek](backup-azure-arm-restore-vms.md#restore-options)egyike a tartományok közötti visszaállítás (CRR) lehetővé teszi, hogy a másodlagos régióban lévő biztonsági másolati elemeket visszaállítsa, amely egy [Azure párosított régió](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="data-box"></a>Adatmező

Tekintse meg a következőt: [adatbox-dokumentáció](../databox/data-box-overview.md).

## <a name="datasource"></a>Adatforrás

Egy erőforrás (Azure-erőforrás, proxy-erőforrás vagy helyszíni erőforrás), amely a biztonsági mentésre jelölt. Például egy Azure-beli virtuális gép vagy egy Azure-fájlmegosztás.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Munkaterhelés-specifikus kifejezés)

Tekintse meg a [DPM dokumentációját](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Tekintse meg a [ExpressRoute dokumentációját](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Fájlrendszer-konzisztens biztonsági mentés

(Munkaterhelés-specifikus kifejezés)

A fájlrendszerrel konzisztens biztonsági másolatok következetességet biztosítanak azáltal, hogy az összes fájlról pillanatképet készítenek. [További információ](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Előtér-tároló/-forrás mérete

Azon adatforrások mérete, amelyekről biztonsági másolatot kell készíteni. Az adatforrás előtérbeli mérete határozza meg a [védett példányok](#protected-instance) számát.

## <a name="full-backup"></a>Teljes biztonsági mentés

Teljes biztonsági mentés esetén a teljes adatforrás egy másolatát tárolja minden biztonsági mentéshez.

## <a name="gfs-backup-policy"></a>GFS biztonsági mentési szabályzat

A GFS (nagyapa-apa-Son) biztonsági mentési szabályzata lehetővé teszi, hogy hetente, havonta és évenkénti biztonsági mentési ütemezést definiáljon a napi biztonsági mentési ütemezés mellett. A heti biztonsági másolatok "Sons" néven ismertek, a havi biztonsági másolatok "atyák", az éves biztonsági másolatok pedig "nagyapák" néven ismertek. A biztonsági másolatok mindegyik készlete beállítható úgy, hogy a különböző időpontokban megmaradjanak, ami lehetővé teszi a biztonsági másolatok megőrzési lehetőségeinek testreszabását. A GFS házirendek hasznosak a biztonsági mentések hosszú ideig történő megőrzése érdekében.

## <a name="iaas-vms--azure-vms"></a>IaaS virtuális gépek/Azure-beli virtuális gépek

Tekintse meg az [Azure virtuális gép dokumentációját](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Növekményes biztonsági mentés

A növekményes biztonsági mentések csak azokat a blokkokat tárolják, amelyek az előző biztonsági mentés óta módosultak.

## <a name="instant-restore"></a>Azonnali visszaállítás

(Munkaterhelés-specifikus kifejezés) Az azonnali visszaállítás magában foglalja a gépek közvetlenül a biztonsági mentési pillanatképből való visszaállítását a tár pillanatképének másolata helyett. Az azonnali visszaállítások gyorsabbak, mint a tárolók visszaállítása. Az elérhető azonnali visszaállítási pontok száma a pillanatképekhez konfigurált megőrzési időtartamtól függ. Jelenleg csak az Azure virtuális gépek biztonsági mentéséhez használható.

## <a name="iops"></a>IOPS

A bemeneti/kimeneti műveletek másodpercenkénti száma.

## <a name="item-level-restore"></a>Elemszintű visszaállítás

(Munkaterhelés-specifikus kifejezés)

Az egyes fájlok vagy mappák visszaállítása a gépen a helyreállítási pontból.

## <a name="job"></a>Feladat

Egy felhasználó vagy a Azure Backup szolgáltatás által létrehozott biztonsági másolattal kapcsolatos feladat. A feladatok lehetnek ütemezettek vagy igény szerint (alkalmi) is. Különböző típusú feladatok – biztonsági mentés, visszaállítás, védelem konfigurálása stb. [További információ a feladatokról](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(Munkaterhelés-specifikus kifejezés)

A Azure Backup Server segítségével az alkalmazások számítási feladatait, például a Hyper-V virtuális gépeket, a Microsoft SQL Server, a SharePoint Servert, a Microsoft Exchange-t és a Windows-ügyfeleket egyetlen konzolról biztosíthatja. A munkaterhelések biztonsági mentési funkcióinak nagy részét örökli a DPM, de néhány eltéréssel. [További információ](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Felügyelt lemezek

Tekintse át a [Managed Disks dokumentációját](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>MARS-ügynök

(Munkaterhelés-specifikus kifejezés)

Más néven **Azure Backup ügynök** vagy **Recovery Services ügynök**, a Mars-ügynököt a Azure Backup használja a helyszíni gépekről és az Azure-beli virtuális gépekről származó adatok biztonsági mentésére az Azure-beli biztonsági mentési Recovery Services-tárolóba. [További információ](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (hálózati biztonsági csoport)

Tekintse meg a [NSG dokumentációját](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Offline előkészítés

Az offline előkészítés arra a folyamatra utal, amely a kezdeti (teljes) biztonsági mentést offline állapotba helyezi, a hálózati sávszélesség használata nélkül. Egy olyan mechanizmust biztosít, amellyel a biztonsági mentési adatai átmásolhatók a fizikai tárolóeszközökre, amelyeket aztán egy közeli Azure-adatközpontba szállítanak, és egy Recovery Services-tárolóra tölthetők fel. [További információ](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Igény szerinti biztonsági mentés/alkalmi biztonsági mentés

Egy olyan biztonsági mentési feladatot, amelyet egy felhasználó egyszeri igények alapján aktivál, és nem az erőforráshoz konfigurált biztonsági mentési ütemterv (szabályzat) alapján.

## <a name="original-location-recovery-olr"></a>Eredeti helyre történő helyreállítás (OLR)

A visszaállítási pontról a biztonsági mentések helyétől a forrás helyére történt helyreállítás, amely a helyreállítási pontban tárolt állapotra vált. Az Azure-beli virtuális gépek biztonsági mentésének használata esetén ez azt jelenti, hogy a virtuális gépet a biztonsági mentések eredeti kiszolgálójára állítja vissza. Az Azure fájlmegosztás biztonsági mentésének használatakor ez azt jelenti, hogy az adatvisszaállítás a biztonsági másolatba mentett fájlmegosztás esetében jelent meg.

## <a name="passphrase"></a>Jelszót

(Munkaterhelés-specifikus kifejezés)

A hitelesítő adatok titkosítására és visszafejtésére szolgálnak a helyszíni vagy helyi gép a MARS-ügynökkel vagy az Azure-ból történő biztonsági mentése vagy visszaállítása során.

## <a name="private-endpoint"></a>Privát végpont

Tekintse meg a [Private Endpoint dokumentációját](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Védett példány

A védett példány arra a számítógépre, fizikai vagy virtuális kiszolgálóra vonatkozik, amelyet az Azure-ba történő biztonsági mentés konfigurálására használ.  **Számlázási szempontból** a gép védett példányainak száma a frontend méretének függvénye. Így az egyetlen biztonsági mentési példány (például egy, az Azure-ba mentett virtuális gép) több védett példánynak is megadható, a felület méretétől függően. [További információ](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (szerepköralapú hozzáférés-vezérlés)

Tekintse meg a [RBAC dokumentációját](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Helyreállítási pont/visszaállítási pont/adatmegőrzési pont/pont-idő (PIT)

Az eredeti, biztonsági mentés alatt álló adatmennyiség másolata. Egy adatmegőrzési pont egy időbélyeghez van társítva, így egy adott időpontra állíthatja vissza az elemeket.

## <a name="recovery-services-vault"></a>Recovery Services-tároló

*Microsoft. recoveryservices szolgáltatónál/Vault* típusú Azure Resource Manager-erőforrás. A Recovery Services-tárolók jelenleg a következő számítási feladatok biztonsági mentésére használhatók: Azure-beli virtuális gépek, SQL Azure-beli virtuális gépeken, SAP HANA az Azure-beli virtuális gépeken és az Azure-fájlmegosztás. A helyszíni számítási feladatok biztonsági mentésére is használható a MARS, Azure Backup Server (MABS) és a System Center DPM használatával. [További információ a Recovery Services](backup-azure-recovery-services-vault-overview.md)-tárolókkal kapcsolatban.

## <a name="resource-group"></a>Erőforráscsoport

Tekintse át a [Azure Resource Manager dokumentációját](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>REST API

Tekintse meg az [Azure REST API dokumentációját](/rest/api/azure/).

## <a name="retention-rule"></a>Adatmegőrzési szabály

Egy felhasználó által definiált szabály, amely meghatározza, hogy a biztonsági mentések meddig legyenek megőrizve.

## <a name="rpo-recovery-point-objective"></a>RPO (helyreállítási pont célkitűzése)

A RPO az adatvesztési helyzetekben lehetséges maximális adatvesztést jelzi. Ezt a biztonsági mentés gyakorisága határozza meg.

## <a name="rto-recovery-time-objective"></a>RTO (helyreállítási idő célkitűzése)

A RTO azt a maximális időtartamot jelzi, ameddig az adatvesztési forgatókönyv után az utolsó rendelkezésre álló időpontra vissza lehet állítani az adatgyűjtési időt.

## <a name="scheduled-backup"></a>Ütemezett biztonsági mentés

Az adott elemhez konfigurált biztonsági mentési szabályzat által automatikusan aktivált biztonsági mentési feladatok.

## <a name="secondary-region--paired-region"></a>Másodlagos régió/párosított régió

A regionális párok két régióból állnak, ugyanazon a földrajzon belül. Az egyik az elsődleges régió, a másik pedig a másodlagos régió. A párosított régiókat néhány Azure-szolgáltatás (beleértve a GRS-beállításokkal Azure Backup) használja az üzletmenet folytonosságának biztosításához és az adatvesztés elleni védelemhez. [További információ](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>Helyreállítható törlés

A Soft delete egy olyan szolgáltatás, amely segít megvédeni a biztonsági mentési adatmennyiségek véletlen törlését. A helyreállítható törléssel akkor is, ha egy rosszindulatú szereplő töröl egy biztonsági mentést (vagy véletlenül törli a biztonsági mentési adatmennyiséget), a biztonsági mentési adat további ideig marad, így a biztonsági mentési elem helyreállítása adatvesztés nélkül történik. [További információ](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Pillanatkép

A pillanatkép egy virtuális merevlemez (VHD) vagy egy Azure-fájlmegosztás teljes, írásvédett másolata. További információ a [lemezes pillanatképekről](../virtual-machines/windows/snapshot-copy-managed-disk.md) és a [fájl-pillanatképekről](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>A(z)

Tekintse meg a [Storage-fiók dokumentációját](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Előfizetés

Az Azure-előfizetés az erőforrások kiosztására használt logikai tároló a Azure-ban. Ez tartalmazza az összes erőforrást, például a virtuális gépeket, az adatbázisokat és sok más információt.

## <a name="system-state-backup"></a>Rendszerállapot biztonsági mentése

(Munkaterhelés-specifikus kifejezés)

Biztonsági másolatot készít az operációs rendszer fájljairól. Ez a biztonsági másolat lehetővé teszi a helyreállítást a számítógép indításakor, de a rendszerfájlok és a beállításjegyzék elvesznek. [További információ](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Bérlő

A bérlő egy szervezetet képvisel. Az Azure AD egy dedikált példánya, amelyet a szervezetek vagy alkalmazásfejlesztők kapnak, amikor kapcsolatot hoznak létre a Microsofttal, például regisztrálnak az Azure, a Microsoft Intune vagy a Microsoft 365 szolgáltatásra.

## <a name="tier"></a>Szint

A Azure Backup jelenleg a következő biztonsági mentési tárolási szinteket támogatja:

### <a name="snapshot-tier"></a>Pillanatkép szintje

(Munkaterhelés-specifikus kifejezés) A virtuális gép biztonsági mentésének első fázisában az elkészített pillanatkép a lemezzel együtt tárolódik. Ezt a tárolási formát pillanatfelvételi szinten nevezzük. A pillanatkép-csomagok visszaállítása gyorsabb (a tárolóból való visszaállításnál), mert a pillanatképek várakozási idejét nem törlik a tárolóból a visszaállítási művelet elindítása előtt.

### <a name="vault-standard-tier"></a>Vault-Standardi szintű

A Azure Backup által támogatott összes munkaterhelésre vonatkozó biztonsági mentési adatok tárolása a biztonsági mentési tárolóval rendelkező tárolókban történik, a Azure Backup által felügyelt tárolási fiókok automatikus méretezési készlete. A Vault-Standard szint egy online tárolási réteg, amely lehetővé teszi a biztonsági mentési információk elkülönített másolatának tárolását egy Microsoft által felügyelt bérlőben, így újabb védelmi réteget hozhat létre. Olyan munkaterhelések esetén, ahol a pillanatkép-réteg támogatott, a pillanatkép-rétegben és a tár-standard szinten is található a biztonsági mentési adatok másolata. A standard szintű tároló biztosítja, hogy a biztonsági mentési adatforrások akkor is elérhetők legyenek, ha az adatforrás biztonsági mentését törli vagy feltörték.

## <a name="unmanaged-disk"></a>Nem felügyelt lemez

Tekintse át a nem [felügyelt lemezek dokumentációját](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Tároló

Egy Azure-beli tárolási entitás, amely a biztonsági mentési adatgyűjtést. Emellett a RBAC és a számlázási egység is. Jelenleg két típusú tároló létezik – Recovery Services tár és Backup-tároló.

## <a name="vault-credentials"></a>Tároló hitelesítő adatai

A tároló hitelesítőadat-fájlja a portál által az egyes tárakhoz létrehozott tanúsítvány. Ezt használja a rendszer, amikor a helyszíni kiszolgálót regisztrálja a tárolóban. [További információ](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>VNET (Virtual Network)

Tekintse meg a [VNET dokumentációját](../virtual-network/virtual-networks-overview.md).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows Kötet árnyékmásolata szolgáltatás)

Tekintse meg a [VSS dokumentációját](/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Következő lépések

- [Azure Backup áttekintése](backup-overview.md)
- [Architektúra és összetevők Azure Backup](backup-architecture.md)