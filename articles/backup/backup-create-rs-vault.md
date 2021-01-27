---
title: Recovery Services-tárolók létrehozása és konfigurálása
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat Recovery Services tárolókat, amelyek a biztonsági mentéseket és a helyreállítási pontokat tárolják. Megtudhatja, hogyan használható a régiók közötti visszaállítás a másodlagos régióban való visszaállításhoz.
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 51d095ab68bc9277cb610bd226039ffbde8d7ddf
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915726"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása és konfigurálása

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Tárolási redundancia beállítása

A Azure Backup automatikusan kezeli a tároló tárterületét. Meg kell adnia a tárterület replikálásának módját.

> [!NOTE]
> A **tároló replikálási típusának** (helyileg redundáns vagy földrajzilag redundáns Recovery Services) módosítását el kell végezni ahhoz, hogy a biztonsági mentések a tárban legyenek konfigurálva. A biztonsági mentés konfigurálása után a módosítás lehetőség le lesz tiltva.
>
>- Ha még nem konfigurálta a biztonsági mentést, [kövesse az alábbi lépéseket](#set-storage-redundancy) a beállítások áttekintéséhez és módosításához.
>- Ha már konfigurálta a biztonsági mentést, és át kell térnie a GRS-ről a LRS-re, [tekintse át ezeket a megkerülő megoldásokat](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. Az **Recovery Services** -tárolók ablaktáblán válassza ki az új tárolót. A **Beállítások** szakaszban válassza a  **Tulajdonságok** lehetőséget.
1. A **Tulajdonságok** alatt a **biztonsági mentés konfigurálása** területen válassza a **frissítés** lehetőséget.

1. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés** gombra.

     ![Az új tároló tárolási konfigurációjának beállítása](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Javasoljuk, hogy ha az Azure-t elsődleges biztonsági mentési tárolási végpontként használja, folytassa az alapértelmezett **geo-redundáns** beállítás használatát.
   - Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amellyel csökkentheti az Azure Storage-költségeit.
   - További információ a [földrajzi](../storage/common/storage-redundancy.md#geo-redundant-storage) és [helyi](../storage/common/storage-redundancy.md#locally-redundant-storage) redundanciáról.
   - Ha állásidő nélkül szeretné rendelkezésre állást biztosítani egy régióban, garantálhatja az [adattárolást, majd a zóna – redundáns tároló](../storage/common/storage-redundancy.md#zone-redundant-storage)elemet.

>[!NOTE]
>A tár tárolási replikációs beállításai nem vonatkoznak az Azure-fájlmegosztás biztonsági mentésére, mert az aktuális megoldás a pillanatkép-alapú, és a tárolóra nem kerül át adatok. A pillanatképek tárolása ugyanabban a Storage-fiókban történik, mint a mentett fájlmegosztás.

## <a name="set-cross-region-restore"></a>Régiók közötti visszaállítás beállítása

A Restore Option **Cross region Restore (CRR)** lehetővé teszi az adatvisszaállítást egy másodlagos, Azure-beli [párosított régióban](../best-practices-availability-paired-regions.md).

A következő adatforrásokat támogatja:

- Azure-beli virtuális gépek (általánosan elérhető)
- Azure-beli virtuális gépeken üzemeltetett SQL-adatbázisok (előzetes verzió)
- Azure-beli virtuális gépeken üzemeltetett adatbázisok SAP HANA (előzetes verzió)

A régiók közötti visszaállítás a következőket teszi lehetővé:

- a naplózási vagy megfelelőségi követelmények betartásának elvégzése
- az adatvisszaállítás az elsődleges régióban katasztrófa esetén

A virtuális gépek visszaállításakor visszaállíthatja a virtuális gépet vagy a lemezét. Ha az Azure-beli virtuális gépeken üzemeltetett SQL-/SAP HANA-adatbázisokból végez visszaállítást, akkor visszaállíthatja az adatbázisokat vagy a fájljaikat.

A szolgáltatás kiválasztásához válassza a **tartományok közötti visszaállítás engedélyezése** lehetőséget a **biztonsági mentési konfiguráció** ablaktáblán.

Mivel ez a folyamat tárolási szinten van, [díjszabási vonzatok](https://azure.microsoft.com/pricing/details/backup/)vannak.

>[!NOTE]
>Előkészületek:
>
>- A támogatott felügyelt típusok és régiók listáját a [támogatási mátrixban](backup-support-matrix.md#cross-region-restore) tekintheti meg.
>- Az Azure-beli virtuális gépek régió-visszaállítási (CRR) funkciója mostantól általánosan elérhető az összes Azure-beli nyilvános régióban.
>- Az SQL és SAP HANA adatbázisok régiók közötti visszaállítása előzetes verzióként érhető el az összes Azure-beli nyilvános régióban.
>- A CRR bármely GRS-tárolóhoz engedélyezhető a tár szintjén (alapértelmezés szerint kikapcsolva).
>- A választás után akár 48 órát is igénybe vehet, hogy a biztonsági mentési elemek elérhetők legyenek a másodlagos régiókban.
>- Az Azure-beli virtuális gépekhez jelenleg CRR csak az Azure Resource Manger Azure-beli virtuális gépek esetében támogatott. A klasszikus Azure-beli virtuális gépek nem támogatottak.  Ha a további felügyeleti típusok támogatják a CRR-t, akkor a **rendszer automatikusan** regisztrálja őket.
>- A régiók közötti visszaállítás **jelenleg nem állítható vissza** GRS vagy LRS, ha az első alkalommal kezdeményezték a védelmet.

### <a name="configure-cross-region-restore"></a>Régiók közötti visszaállítás konfigurálása

A GRS-redundanciával létrehozott tárolók tartalmazzák a régiók közötti visszaállítási szolgáltatás konfigurálásának lehetőségét. Minden GRS-tárolóban megjelenik egy szalagcím, amely a dokumentációra hivatkozik. A tár CRR konfigurálásához nyissa meg a biztonsági mentési konfiguráció ablaktáblát, amely a funkció engedélyezésének lehetőségét tartalmazza.

 ![Biztonsági mentési konfiguráció szalagcíme](./media/backup-azure-arm-restore-vms/banner.png)

1. A portálon nyissa meg a Recovery Services-tároló > **tulajdonságait** (a **Beállítások** alatt).
1. A **biztonsági mentés konfigurálása** területen válassza a **frissítés** lehetőséget.
1. A funkció engedélyezéséhez válassza a **régiók közötti visszaállítás engedélyezése ebben** a tárolóban lehetőséget.

   ![Régiók közötti visszaállítás engedélyezése](./media/backup-azure-arm-restore-vms/backup-configuration.png)

A CRR biztonsági mentésével és visszaállításával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Régiók közötti visszaállítás Azure-beli virtuális gépekhez](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Régiók közötti visszaállítás SQL-adatbázisokhoz](restore-sql-database-azure-vm.md#cross-region-restore)
- [Régiók közötti visszaállítás SAP HANA adatbázisokhoz](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Titkosítási beállítások megadása

Alapértelmezés szerint a Recovery Services-tárolóban lévő adatai a platform által felügyelt kulcsokkal vannak titkosítva. A végponttól nem szükséges explicit művelet, hogy engedélyezze ezt a titkosítást, és minden olyan munkaterhelésre vonatkozik, amelyről biztonsági mentés készül a Recovery Services-tárolóba.  Dönthet úgy, hogy saját kulcsot használ a tárolóban található biztonsági másolatok titkosításához. Ezt az ügyfél által felügyelt kulcsoknak nevezzük. Ha a biztonsági mentési adatait saját kulccsal szeretné titkosítani, meg kell adni a titkosítási kulcsot, mielőtt bármely elem védve legyen a tárolóban. Miután engedélyezte a titkosítást a kulccsal, nem vonható vissza.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Tároló konfigurálása az ügyfél által felügyelt kulcsok használatával történő titkosításhoz

Ha úgy szeretné konfigurálni a tárolót, hogy az ügyfél által felügyelt kulcsokkal Titkosítsa a szolgáltatást, ezeket a lépéseket a következő sorrendben kell követnie:

1. Felügyelt identitás engedélyezése a Recovery Services-tárolóban

1. Engedélyek kiosztása a tárolóhoz a Azure Key Vault található titkosítási kulcs eléréséhez

1. A Azure Key Vault eltávolításának és törlésének engedélyezése

1. A titkosítási kulcs kiosztása a Recovery Services-tárolóhoz

A fenti lépések utasításait [ebben a cikkben](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)találja.

## <a name="modifying-default-settings"></a>Alapértelmezett beállítások módosítása

Javasoljuk, hogy a tárban a biztonsági mentések konfigurálása előtt tekintse át a **Tárolóreplikáció típusa** és a **Biztonsági beállítások** alapértelmezett beállításait.

- Alapértelmezés szerint a **tárolási replikálás típusa** **geo-redundáns** (GRS) értékre van állítva. A biztonsági mentés konfigurálása után a módosítás lehetőség le lesz tiltva.
  - Ha még nem konfigurálta a biztonsági mentést, [kövesse az alábbi lépéseket](#set-storage-redundancy) a beállítások áttekintéséhez és módosításához.
  - Ha már konfigurálta a biztonsági mentést, és át kell térnie a GRS-ről a LRS-re, [tekintse át ezeket a megkerülő megoldásokat](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- Az újonnan létrehozott tárolók alapértelmezés szerint a **Soft delete** lehetőséggel védik a biztonsági mentési adatok véletlen vagy kártékony törlési **funkcióit** . A beállítások áttekintéséhez és módosításához [kövesse az alábbi lépéseket](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>A GRS és a LRS közötti váltás a biztonsági mentés konfigurálása után

Mielőtt úgy döntene, hogy a GRS a helyileg redundáns tárterületre (LRS) helyezi át, tekintse át a forgatókönyvnek megfelelő alacsonyabb és nagyobb adattartósság közötti kompromisszumot. Ha a GRS-ből a LRS-be kell lépnie, akkor két lehetőség közül választhat. A biztonsági mentési adatok megőrzése érdekében az üzleti követelményektől függnek:

- [Nem kell megőriznie az előző biztonsági mentést.](#dont-need-to-preserve-previous-backed-up-data)
- [Meg kell őriznie az előző biztonsági mentéssel](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Nem kell megőriznie az előző biztonsági mentést.

Egy új LRS-tárolóban a munkaterhelések védelme érdekében a jelenlegi védelmet és adatokat törölni kell a GRS-tárban, és újra kell konfigurálni a biztonsági másolatokat.

>[!WARNING]
>A következő művelet romboló, ezért nem vonható vissza. A rendszer véglegesen törli a védett kiszolgálóhoz társított biztonsági mentési adatokat és biztonsági másolati elemeket. Legyen óvatos.

Állítsa le és törölje az aktuális védelmet a GRS-tárolón:

1. A GRS-tároló tulajdonságaiban tiltsa le a nem kötelező törlést. A Soft delete letiltásához kövesse az [alábbi lépéseket](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) .

1. Állítsa le a védelmet, és törölje a biztonsági mentéseket a meglévő GRS-tárból. A tároló irányítópultjának menüjében válassza a **biztonsági másolati elemek elemet**. Az itt felsorolt elemeket, amelyeket át kell helyezni a LRS-tárba, el kell távolítani a biztonsági másolati adatokkal együtt. Lásd: [védett elemek törlése a felhőben](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) és a [védett elemek törlése a helyszínen](backup-azure-delete-vault.md#delete-protected-items-on-premises).

1. Ha az AFS (Azure-fájlmegosztás), az SQL-kiszolgálók vagy a SAP HANA-kiszolgálók áthelyezését tervezi, akkor regisztrálnia kell a regisztrációt is. A tároló irányítópultjának menüjében válassza a **biztonsági mentési infrastruktúra** elemet. Tekintse meg [az SQL Server regisztrációjának](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)megszüntetését, az [Azure-fájlmegosztáshoz társított Storage-fiók regisztrációjának](manage-afs-backup.md#unregister-a-storage-account)megszüntetését és a [SAP HANA példány regisztrációjának](sap-hana-db-manage.md#unregister-an-sap-hana-instance)megszüntetését ismertető témakört.

1. Miután eltávolította őket a GRS-tárolóból, folytassa a számítási feladatok biztonsági mentésének konfigurálását az új LRS-tárolóban.

#### <a name="must-preserve-previous-backed-up-data"></a>Meg kell őriznie az előző biztonsági mentéssel

Ha meg kell őriznie a védett adatok védelmét a GRS-tárolóban, és egy új LRS-tárolóban kell folytatnia a védelmet, bizonyos számítási feladatokhoz korlátozott lehetőségek állnak rendelkezésre:

- A MARS esetében [leállíthatja a védelmet az adat megőrzése](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) és az ügynök regisztrálása az új LRS-tárolóban.

  - Azure Backup szolgáltatás továbbra is megőrzi a GRS-tároló összes meglévő helyreállítási pontját.
  - A helyreállítási pontok megőrzéséhez a GRS-tárolóban kell fizetnie.
  - Az GRS-tárolóban csak a lejárt helyreállítási pontokra vonatkozó biztonsági másolatokat állíthatja vissza.
  - Az LRS-tárolón létre kell hoznia egy új kezdeti replikát.

- Egy Azure-beli virtuális gép esetében [leállíthatja a védelmet](backup-azure-manage-vms.md#stop-protecting-a-vm) a virtuális gép számára a GRS-tárolóban, áthelyezheti a virtuális gépet egy másik erőforráscsoporthoz, majd védetté teheti a virtuális GÉPET a LRS-tárolóban. Tekintse meg a virtuális gép másik erőforráscsoporthoz való áthelyezésével kapcsolatos [útmutatást és korlátozásokat](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) .

  Egy virtuális gépet egyszerre csak egy tárolóban lehet védeni. Az új erőforráscsoport virtuális gépe azonban a LRS-tárolóban is védhető, mivel ez egy másik virtuális gép.

  - Azure Backup szolgáltatás megőrzi azokat a helyreállítási pontokat, amelyekről biztonsági mentés készül a GRS-tárolóban.
  - A helyreállítási pontok megőrzéséhez a GRS-tárolóban kell fizetnie (további részletekért lásd: [Azure Backup díjszabása](azure-backup-pricing.md) ).
  - Szükség esetén visszaállíthatja a virtuális gépet a GRS-tárból.
  - Az új erőforrásban található virtuális gép LRS-tárolójának első biztonsági mentése kezdeti replika lesz.

## <a name="next-steps"></a>Következő lépések

[További](backup-azure-recovery-services-vault-overview.md) információ Recovery Services-tárolók.
[További](backup-azure-delete-vault.md) információ Recovery Services tárolók törlése.
