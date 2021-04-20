---
title: Backup-tárolók áttekintése
description: A Backup-tárolók áttekintése.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: e2d720da9474a35870de01559201d22c9e5b567f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739079"
---
# <a name="backup-vaults-overview"></a>Backup-tárolók áttekintése

Ez a cikk a Backup-tárolók funkcióit ismerteti. A Backup-tárolók olyan tárolási entitások az Azure-ban, amelyek biztonsági mentési adatokat tartalmaznak bizonyos, a Azure Backup számítási feladatokhoz. A Backup-tárolók használatával biztonsági mentési adatokat is Azure Database for PostgreSQL Azure-szolgáltatások, például a Azure Database for PostgreSQL kiszolgálók és a Azure Backup számítási feladatok számára. A biztonsági mentési tárolók megkönnyítik a biztonsági mentési adatok rendszerezését, miközben minimalizálják a felügyeleti többletterhelést. A Backup-tárolók az Azure Azure Resource Manager modellen alapulnak, amely a következő funkciókat biztosítja:

- **Továbbfejlesztett képességek az adatok** biztonsági mentésének biztonságának biztosítása érdekében: A Backup-tárolók Azure Backup biztonsági képességeket biztosítanak a felhőalapú biztonsági mentések védelméhez. A biztonsági funkciók biztosítják a biztonsági másolatok biztonságát és az adatok biztonságos helyreállítását még akkor is, ha az éles és a biztonsági mentési kiszolgálók biztonsága sérül. [További információ](backup-azure-security-feature.md)

- **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC):** Az Azure RBAC részletes hozzáférés-vezérlést biztosít az Azure-ban. [Az Azure számos beépített szerepkört](../role-based-access-control/built-in-roles.md)biztosít, Azure Backup három beépített szerepköre van a [helyreállítási pontok kezeléséhez.](backup-rbac-rs-vault.md) A Backup-tárolók kompatibilisek az Azure RBAC-val, amely korlátozza a biztonsági mentést és a visszaállításhoz való hozzáférést a felhasználói szerepkörök meghatározott készletéhez. [További információ](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Tárolási beállítások a Backup-tárolóban

A Backup-tároló egy olyan entitás, amely az idő során létrehozott biztonsági másolatokat és helyreállítási pontokat tárolja. A Backup-tároló a védett virtuális gépekhez társított biztonsági mentési szabályzatokat is tartalmazza.

- Azure Backup automatikusan kezeli a tároló tárterületét. Válassza ki az üzleti igényeinek megfelelő tárhelyredundaniát a Backup-tároló létrehozásakor.

- A tárhely-redundanciával kapcsolatos további [](../storage/common/storage-redundancy.md#geo-redundant-storage) információkért tekintse meg a geo- és helyi redundanciával [kapcsolatos cikkeket.](../storage/common/storage-redundancy.md#locally-redundant-storage)

## <a name="encryption-settings-in-the-backup-vault"></a>Titkosítási beállítások a Backup-tárolóban

Ez a szakasz a Backup-tárolóban tárolt biztonsági mentési adatok titkosításához rendelkezésre álló lehetőségeket ismerteti. Azure Backup szolgáltatás a **Backup Management Service alkalmazással** fér hozzá Azure Key Vault, de a Backup-tároló felügyelt identitásához nem.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági mentési adatok titkosítása platform által kezelt kulcsokkal

Alapértelmezés szerint minden adata platform által kezelt kulcsokkal van titkosítva. Ennek a titkosításnak az engedélyezéséhez nem kell explicit műveletet tennie. Minden olyan számítási feladatra vonatkozik, amelyről biztonsági mentést készít a Backup-tárolóban.

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása

A Backup-tároló egy olyan felügyeleti entitás, amely az idő során létrehozott helyreállítási pontokat tárolja, és felületet biztosít a biztonsági mentéssel kapcsolatos műveletek végrehajtásához. Ezek közé tartoznak az igény szerinti biztonsági másolatok készítése, a visszaállítások végrehajtása, illetve a biztonsági mentési szabályzatok létrehozása.

Backup-tároló létrehozásához kövesse az alábbi lépéseket.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.

### <a name="create-backup-vault"></a>Backup-tároló létrehozása

1. Írja **be a keresőmezőbe a** Backup-tárolók parancsot.
1. A **Szolgáltatások alatt** válassza a **Backup-tárolók lehetőséget.**
1. A **Backup-tárolók lapon** válassza a Hozzáadás **lehetőséget.**
1. Az Alapvető **beállítások lapon, a** **Projekt részletei** alatt győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, majd válassza az **Új erőforráscsoport** létrehozása lehetőséget. A névhez írja be a *myResourceGroup* nevet.

  ![Új erőforráscsoport létrehozása](./media/backup-vault-overview/new-resource-group.png)

1. A **Példány részletei területen** írja be a *myVault* nevet a **Backup-tároló** neveként, és válassza ki a választott régiót, amely ebben az esetben az *USA* keleti régiója az Ön **régiójához.**
1. Most válassza ki a **Storage redundanciát.** A tárhely-redundancia nem módosítható a tároló elemeinek védelme után.
1. Javasoljuk, hogy ha az Azure-t használja elsődleges biztonsági mentési tárolási végpontként, használja továbbra is az alapértelmezett **georedundáns** beállítást.
1. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amellyel csökkentheti az Azure Storage-költségeit.
1. További információ a [földrajzi és helyi](../storage/common/storage-redundancy.md#geo-redundant-storage) [redundanciával](../storage/common/storage-redundancy.md#locally-redundant-storage) kapcsolatban.

  ![Tárhely-redundancia kiválasztása](./media/backup-vault-overview/storage-redundancy.png)

1. Válassza az Áttekintés + létrehozás gombot az oldal alján.

    ![Válassza az Áttekintés + létrehozás lehetőséget](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Backup-tároló törlése

Ez a szakasz a Backup-tárolók törlését ismerteti. Útmutatásokat tartalmaz a függőségek eltávolításához, majd egy tároló törléséhez.

### <a name="before-you-start"></a>Előkészületek

A következő függőségek egyikével sem törölhet Backup-tárolót:

- Védett adatforrásokat (például Azure Database for PostgreSQL-kiszolgálókat) tartalmazó tároló nem törölhető.
- Biztonsági mentési adatokat tartalmazó tároló nem törölhető.

Ha a függőségek eltávolítása nélkül próbálja törölni a tárolót, a következő hibaüzeneteket fogja látni:

>A Backup-tároló nem törölhető, mert a tárolóban meglévő biztonsági mentési példányok vagy biztonsági mentési szabályzatok vannak. Törölje a tárolóban meglévő összes biztonsági mentési példányt és biztonsági mentési szabályzatot, majd próbálja meg törölni a tárolót.

### <a name="proper-way-to-delete-a-vault"></a>Tároló törlésének megfelelő módja

>[!WARNING]
A következő művelet kipusztító, és nem vonható vissza. A védett kiszolgálóhoz társított összes biztonsági mentési adat és biztonsági mentési elem véglegesen törölve lesz. Legyen óvatos.

A tároló megfelelő törléséhez az alábbi sorrendben kell követnie a lépéseket:

- Ellenőrizze, hogy vannak-e védett elemek:
  - A bal **oldali navigációs sávon** kattintson a Biztonságimásolat-példányok menüre. Először az itt felsorolt összes elemet törölni kell.

A lépések befejezése után folytathatja a tároló törlését.

### <a name="delete-the-backup-vault"></a>A Backup-tároló törlése

Ha nincs több elem a tárolóban, válassza a **Törlés lehetőséget** a tároló irányítópultján. Megjelenik egy megerősítő szöveg, amely rákérdezett, hogy törölni szeretné-e a tárolót.

![Tároló törlése](./media/backup-vault-overview/delete-vault.png)

1. Válassza **az Igen** lehetőséget annak ellenőrzéséhez, hogy törölni szeretné-e a tárolót. A tároló törlődik. A portál visszatér az Új **szolgáltatás menüre.**

## <a name="monitor-and-manage-the-backup-vault"></a>A Backup-tároló figyelése és kezelése

Ez a szakasz azt ismerteti, hogyan használhatja a Backup-tároló áttekintési irányítópultját a Backup-tárolók figyeléséhez és kezeléséhez.  Az áttekintő panel két csempét tartalmaz: **Feladatok és** **Példányok.**

![Áttekintő irányítópult](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Backup-példányok kezelése

A Feladatok **csempén** megtekintheti a Backup-tárolóban a biztonsági mentéssel és visszaállítással kapcsolatos összes feladat összesített nézetét. Ha kijelöli a csempén található számokat, további információkat is megtekintet egy adott adatforrástípushoz, művelettípushoz és állapothoz.

![Biztonságimásolat-példányok](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Biztonsági mentési feladatok kezelése

A **Biztonságimásolat-példányok** csempén a Backup-tárolóban tárolt összes biztonságimásolat-példány összesített nézete látható. Ha kijelöli a csempén található számokat, további információkat is megtekint a biztonsági mentési példányokkal kapcsolatban egy adott adatforrástípusra és védelmi állapotra vonatkozóan.

![Biztonsági mentési feladatok](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Következő lépések

- [Biztonsági mentés konfigurálása Azure PostgreSQL-adatbázisokon](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
