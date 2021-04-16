---
title: Recovery Services-tárolók létrehozása és konfigurálása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhatja a biztonsági másolatokat és helyreállítási pontokat tároló Recovery Services-tárolókat. Megtudhatja, hogyan használhatja a régiók közötti visszaállítást másodlagos régióban való visszaállításhoz.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5e2983e473fac72d02f0fdbc8c307e96326ac0a6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518575"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása és konfigurálása

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Tárhelyredundania beállítása

Azure Backup automatikusan kezeli a tároló tárterületét. Meg kell adnia, hogyan replikálja a rendszer a tárolót.

> [!NOTE]
> A **helyreállítási tár** tárolási replikációs típusának (helyileg redundáns/georedundáns) módosítására a tároló biztonsági mentésének konfigurálása előtt van rá mód. A biztonsági mentés konfigurálása után a módosítás lehetősége le van tiltva.
>
>- Ha még nem konfigurálta a biztonsági mentést, kövesse az alábbi [lépéseket](#set-storage-redundancy) a beállítások áttekintéséhez és módosításához.
>- Ha már konfigurálta a biztonsági mentést, és GRS-ről LRS-re kell átlépnie, tekintse át [ezeket a megkerülő megoldásokat.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. A **Recovery Services-tárolók panelen** válassza ki az új tárolót. A Beállítások **szakaszban** válassza a Tulajdonságok **lehetőséget.**
1. A **Tulajdonságok alatt,** a **Biztonsági mentés konfigurációja alatt** válassza a Frissítés **lehetőséget.**

1. Válassza ki a tárreplikáció típusát, majd válassza a **Mentés lehetőséget.**

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Javasoljuk, hogy ha az Azure-t használja elsődleges biztonsági mentési tárolási végpontként, használja továbbra is az alapértelmezett **georedundáns** beállítást.
   - Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amellyel csökkentheti az Azure Storage-költségeit.
   - További információ a [földrajzi és helyi](../storage/common/storage-redundancy.md#geo-redundant-storage) [redundanciával](../storage/common/storage-redundancy.md#locally-redundant-storage) kapcsolatban.
   - Ha az adatok állásidő nélküli rendelkezésre állásra van szüksége egy régióban, ami garantálja az adatok tárolási helyét, válassza a zónaredundáns [tárolást.](../storage/common/storage-redundancy.md#zone-redundant-storage)

>[!NOTE]
>A tároló tárreplikációs beállításai nem relevánsak az Azure-fájlmegosztások biztonsági mentésekor, mivel az aktuális megoldás pillanatkép-alapú, és nincs átvitt adat a tárolóba. A pillanatképek ugyanabban a tárfiókban vannak tárolva, mint a biztonságimentett fájlmegosztás.

## <a name="set-cross-region-restore"></a>Régiók közötti visszaállítás beállítása

A Régiók közötti visszaállítás **(CRR)** visszaállítási lehetőséggel visszaállíthatja az adatokat egy másodlagos, [Azure párosított régióban.](../best-practices-availability-paired-regions.md)

A következő adatforrásokat támogatja:

- Azure-beli virtuális gépek (általánosan elérhető)
- Azure-beli virtuális gépeken üzemeltetett SQL-adatbázisok (előzetes verzió)
- SAP HANA Azure-beli virtuális gépeken üzemeltetett adatbázisok (előzetes verzió)

A régiók közötti visszaállítás a következőt teszi lehetővé:

- ha naplózási vagy megfelelőségi követelmény van
- az adatok visszaállítása katasztrófa esetén az elsődleges régióban

Virtuális gép visszaállításakor visszaállíthatja a virtuális gépet vagy annak lemezét. Ha Azure-beli virtuális gépeken üzemeltetett SQL/SAP HANA-adatbázisokból állítja vissza a visszaállítást, akkor visszaállíthatja az adatbázisokat vagy a fájljaikat.

A szolgáltatás kiválasztásához válassza a **Régiók közötti** visszaállítás engedélyezése lehetőséget a Biztonsági mentés **konfigurációja panelen.**

Mivel ez a folyamat a tárolási szinten van, [díjszabási következményekkel jár.](https://azure.microsoft.com/pricing/details/backup/)

>[!NOTE]
>Előkészületek:
>
>- Tekintse át [a támogatott](backup-support-matrix.md#cross-region-restore) felügyelt típusok és régiók listáját a támogatási mátrixban.
>- Az Azure-beli virtuális gépek régiók közötti visszaállítási (CRR) funkciója mostantól általánosan elérhető az összes nyilvános Azure-régióban.
>- Az SQL- és SAP HANA adatbázisok régióközi visszaállítása előzetes verzióban érhető el az összes nyilvános Azure-régióban.
>- A CRR bármely GRS-tárolóhoz egy tárolószintű feliratkozási funkció (alapértelmezés szerint ki van kapcsolva).
>- A feliratkozás után akár 48 órát is igénybe vehet, hogy a biztonsági másolati elemek elérhetők lesznek a másodlagos régiókban.
>- Jelenleg az Azure-beli virtuális gépek CRR-Azure Resource Manager azure-beli virtuális gépekhez és titkosított Azure-beli virtuális gépekhez. A klasszikus Azure-beli virtuális gépek nem támogatottak. Ha további felügyeleti típusok támogatják a CRR-t, a rendszer automatikusan **regisztrálja** őket.
>- A régiók **közötti visszaállítás jelenleg** nem állítható vissza GRS-re vagy LRS-re a védelem első elindításakor.
>- A másodlagos régió [RPO-ja](azure-backup-glossary.md#rpo-recovery-point-objective) jelenleg legfeljebb 12 óra az elsődleges régiótól, annak ellenére, hogy az írási hozzáférésű georedundáns [tárolás (RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) replikációja 15 perc.

### <a name="configure-cross-region-restore"></a>Régiók közötti visszaállítás konfigurálása

A GRS-redundanciával létrehozott tárolók esetében lehetőség van a régiók közötti visszaállítás konfigurálára. Minden GRS-tároló tartalmaz egy szalagcímet, amely a dokumentációra hivatkozik. A tároló CRR-beállítását a Biztonsági mentés konfigurációja panelen konfigurálhatja, amely a funkció engedélyezésének beállítását tartalmazza.

 ![Biztonsági mentés konfigurációjának szalagcíme](./media/backup-azure-arm-restore-vms/banner.png)

1. A portálon a Beállítások alatt > **meg** a Recovery Services-tárolót. 
1. A **Biztonsági mentés konfigurációja alatt** válassza a Frissítés **lehetőséget.**
1. A **funkció engedélyezéséhez válassza** a Régióközi visszaállítás engedélyezése ebben a tárolóban lehetőséget.

   ![Régiók közötti visszaállítás engedélyezése](./media/backup-azure-arm-restore-vms/backup-configuration.png)

A CRR-sel való biztonsági mentésről és visszaállításról az alábbi cikkekben talál további információt:

- [Régiók közötti visszaállítás Azure-beli virtuális gépeken](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Régiók közötti visszaállítás SQL-adatbázisokhoz](restore-sql-database-azure-vm.md#cross-region-restore)
- [Régiók közötti visszaállítás SAP HANA adatbázisokhoz](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Titkosítási beállítások megadása

Alapértelmezés szerint a Recovery Services-tárolóban található adatok titkosítása platform által felügyelt kulcsokkal történik. A titkosítás engedélyezéséhez nincs szükség explicit műveletekre, és minden olyan számítási feladatra vonatkozik, amelyről biztonságimentés folyamatban van a Helyreállítási tárban.  Dönthet úgy, hogy a saját kulcsát választja a tárolóban tárolt biztonsági mentési adatok titkosításához. Ezt ügyfél által kezelt kulcsoknak nevezzük. Ha a saját kulcsával szeretné titkosítani a biztonsági mentési adatokat, a titkosítási kulcsot meg kell adni, mielőtt bármilyen elem védve lenne ebben a tárolóban. Ha a kulccsal engedélyezi a titkosítást, az nem fordítható vissza.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Tároló konfigurálása ügyfél által kezelt kulcsokkal való titkosításhoz

Ha a tárolót ügyfél által kezelt kulcsokkal való titkosításra konfigurálja, a következő lépéseket kell követnie ebben a sorrendben:

1. Felügyelt identitás engedélyezése a Recovery Services-tárolóhoz

1. Engedélyek hozzárendelése a tárolóhoz a titkosítási kulcs eléréséhez a Azure Key Vault

1. A törlés és a végleges törlés elleni védelem engedélyezése a Azure Key Vault

1. A titkosítási kulcs hozzárendelése a Recovery Services-tárolóhoz

Az egyes lépésekre vonatkozó utasításokat ebben a [cikkben talál.](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)

## <a name="modifying-default-settings"></a>Alapértelmezett beállítások módosítása

Javasoljuk, hogy a tárban a biztonsági mentések konfigurálása előtt tekintse át a **Tárolóreplikáció típusa** és a **Biztonsági beállítások** alapértelmezett beállításait.

- **A Tárreplikáció típusa** alapértelmezés szerint **Georedundáns** (GRS) lesz. A biztonsági mentés konfigurálása után a módosítás lehetősége le van tiltva.
  - Ha még nem konfigurálta a biztonsági mentést, kövesse az alábbi [lépéseket](#set-storage-redundancy) a beállítások áttekintéséhez és módosításához.
  - Ha már konfigurálta a biztonsági mentést, és GRS-ről LRS-re kell átlépnie, tekintse át [ezeket a megkerülő megoldásokat.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

- **A soft delete** alapértelmezés szerint **Engedélyezve van** az újonnan létrehozott tárolókban, hogy megvédje a biztonsági mentési adatokat a véletlen vagy rosszindulatú törlésektól. [Kövesse az alábbi lépéseket](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) a beállítások áttekintéséhez és módosításához.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Váltás GRS-ről LRS-re a biztonsági mentés konfigurálása után

Mielőtt a GRS-ről a helyileg redundáns tárolásra (LRS) vált, tekintse át az alacsonyabb költségek és a magasabb adatmegőrzés közötti, az adott forgatókönyvnek megfelelő megoldásokat. Ha GRS-ről LRS-re kell átlépnie, két lehetőség közül választhat. Ezek a biztonsági másolatok adatainak megőrzésére vonatkozó üzleti követelményektől függenek:

- [Nem kell megőriznie a korábbi biztonságimentett adatokat](#dont-need-to-preserve-previous-backed-up-data)
- [Meg kell őriznie a korábbi biztonságimentett adatokat](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nem kell megőriznie a korábbi biztonságimentett adatokat

Az új LRS-tárolókban lévő számítási feladatok védelméhez törölni kell az aktuális védelmet és adatokat a GRS-tárolóból, és újra konfigurálni kell a biztonsági másolatokat.

>[!WARNING]
>A következő művelet kipusztító, és nem vonható vissza. A védett kiszolgálóhoz társított összes biztonsági mentési adat és biztonsági mentési elem véglegesen törölve lesz. Legyen óvatos.

Állítsa le és törölje a GRS-tároló aktuális védelmét:

1. Tiltsa le a soft delete parancsot a GRS-tároló tulajdonságai között. A [soft delete letiltásához](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) kövesse az alábbi lépéseket.

1. Állítsa le a védelmet, és törölje a biztonsági másolatokat a meglévő GRS-tárolóból. A Tároló irányítópult menüjében válassza a Biztonsági **mentési elemek lehetőséget.** Az itt felsorolt, az LRS-tárolóba áthelyezni szükséges elemeket a biztonsági mentési adataikkal együtt el kell távolítani. Tekintse meg, hogyan törölheti a védett [elemeket a felhőben,](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) és hogyan törölheti a [védett elemeket a helyszínen.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

1. Ha AFS-t (Azure-fájlmegosztásokat), SQL-kiszolgálókat vagy SAP HANA kiszolgálókat tervez áthelyezni, akkor a regisztrációt is meg kell újulni. A tároló irányítópultjának menüjében válassza a Biztonsági **mentési infrastruktúra lehetőséget.** Lásd az [SQL-kiszolgáló](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)regisztrációjának, az Azure-fájlmegosztásokkal társított tárfiókok regisztrációjának és a(SAP HANA [](manage-afs-backup.md#unregister-a-storage-account) [regisztrációjának) regisztrációját.](sap-hana-db-manage.md#unregister-an-sap-hana-instance)

1. Miután eltávolítja őket a GRS-tárolóból, továbbra is konfigurálja a számítási feladat biztonsági mentését az új LRS-tárolóban.

#### <a name="must-preserve-previous-backed-up-data"></a>Meg kell őriznie a korábbi biztonságimentett adatokat

Ha meg kell tartania az aktuális védett adatokat a GRS-tárolóban, és egy új LRS-tárolóban kell folytatnia a védelmet, néhány számítási feladathoz korlátozott lehetőségek állnak rendelkezésre:

- A MARS-hoz [leállíthatja](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) a védelmet az adatok megőrzésével, és regisztrálhatja az ügynököt az új LRS-tárolóban.

  - Azure Backup szolgáltatás továbbra is megőrzi a GRS-tároló összes meglévő helyreállítási pontjait.
  - Fizetnie kell, hogy a helyreállítási pontok a GRS-tárolóban maradnak.
  - A biztonsági másolatban tárolt adatokat csak a GRS-tároló le nem járatlan helyreállítási pontjain tudja visszaállítani.
  - Létre kell hoznunk az adatok új kezdeti replikáját az LRS-tárolóban.

- Azure-beli virtuális [](backup-azure-manage-vms.md#stop-protecting-a-vm) gépeknél leállíthatja a védelmet a GRS-tárolóban található virtuális gép adatainak megőrzésével, áthelyezheti a virtuális gépet egy másik erőforráscsoportba, majd megvédheti a virtuális gépet az LRS-tárolóban. Tekintse [meg a virtuális gépek](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) másik erőforráscsoportba való áthelyezésének útmutatóját és korlátait.

  Egy virtuális gép egyszerre csak egy tárolóban védható. Az új erőforráscsoportban található virtuális gép azonban védve lehet az LRS-tárolón, mivel az egy másik virtuális gépnek számít.

  - Azure Backup szolgáltatás megőrzi a GRS-tárolón biztonságimentett helyreállítási pontokat.
  - Fizetnie kell azért, hogy a helyreállítási pontok a GRS-tárolóban maradnak [(Azure Backup a](azure-backup-pricing.md) részletekért).
  - Szükség esetén vissza tudja állítani a virtuális gépet a GRS-tárolóból.
  - Az új erőforrásban található virtuális gép LRS-tárolóján az első biztonsági mentés egy kezdeti replika lesz.

## <a name="next-steps"></a>Következő lépések

[További információ](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók.
[További információ](backup-azure-delete-vault.md) Recovery Services-tárolók törlése.