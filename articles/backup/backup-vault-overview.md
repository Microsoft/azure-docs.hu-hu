---
title: A Backup-tárolók áttekintése
description: A Backup-tárolók áttekintése.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92091287"
---
# <a name="backup-vaults-overview"></a>Backup-tárolók áttekintése

Ez a cikk a Backup-tároló funkcióit ismerteti. A Backup-tároló egy Azure-beli tárolási entitás, amely a Azure Backup által támogatott új munkaterhelésekre vonatkozó biztonsági mentési adatok tárolására szolgál. A Backup-tárolók használatával a különböző Azure-szolgáltatások, például a Azure Database for PostgreSQL-kiszolgálók és a Azure Backup által támogatott újabb munkaterhelések biztonsági mentési adatai tárolhatók. A Backup-tárolók megkönnyítik a biztonsági másolatok rendszerezését, miközben minimalizálják a felügyeleti terhelést. A Backup-tárolók az Azure Azure Resource Manager modelljén alapulnak, amely a következő szolgáltatásokat nyújtja:

- **Továbbfejlesztett funkciók a biztonsági mentési adatokat biztonságossá tételéhez**: a Backup-tárolókkal a Azure Backup biztonsági képességeket biztosít a Felhőbeli biztonsági másolatok védelméhez. A biztonsági funkciók biztosítják, hogy biztonságban legyenek a biztonsági másolatok, és biztonságosan helyreállítsák az adatokat, még akkor is, ha a termelési és a biztonsági mentési kiszolgálók biztonsága sérül. [További információ](backup-azure-security-feature.md)

- **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)**: az Azure RBAC részletes hozzáférés-vezérlési felügyeletet biztosít az Azure-ban. Az [Azure számos beépített szerepkört kínál](../role-based-access-control/built-in-roles.md), és a Azure Backup három [beépített szerepkörrel rendelkezik a helyreállítási pontok kezeléséhez](backup-rbac-rs-vault.md). A Backup-tárolók kompatibilisek az Azure RBAC, amely korlátozza a biztonsági mentést, és visszaállítja a hozzáférést a megadott felhasználói szerepkörökhöz. [További információ](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Tárolási beállítások a Backup-tárolóban

A Backup-tároló egy olyan entitás, amely az idő múlásával létrehozott biztonsági mentéseket és helyreállítási pontokat tárolja. A Backup-tároló a védett virtuális gépekhez társított biztonsági mentési szabályzatokat is tartalmazza.

- A Azure Backup automatikusan kezeli a tároló tárterületét. Válassza ki az üzleti igényeknek megfelelő tárolási redundanciát a Backup-tároló létrehozásakor.

- A tárterület-redundanciával kapcsolatos további tudnivalókért tekintse meg a [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) és a [helyi](../storage/common/storage-redundancy.md#locally-redundant-storage) redundancia című cikket.

## <a name="encryption-settings-in-the-backup-vault"></a>Titkosítási beállítások a Backup-tárolóban

Ez a szakasz a Backup-tárolóban tárolt biztonsági mentési adatai titkosításához rendelkezésre álló lehetőségeket ismerteti.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Biztonsági másolatok titkosítása a platform által felügyelt kulcsokkal

Alapértelmezés szerint az összes adatait a platform által felügyelt kulcsok használatával titkosítja a rendszer. A titkosítás engedélyezéséhez nem szükséges explicit műveletet végrehajtani a végponttól. Ez a Backup-tárolóba mentett összes munkaterhelésre vonatkozik.

## <a name="create-a-backup-vault"></a>Backup-tároló létrehozása

A Backup-tároló egy olyan felügyeleti entitás, amely az idő múlásával létrehozott helyreállítási pontokat tárolja, és egy felületet biztosít a biztonsági mentéssel kapcsolatos műveletek elvégzéséhez. Ezek közé tartoznak az igény szerinti biztonsági másolatok készítése, a visszaállítások végrehajtása, illetve a biztonsági mentési szabályzatok létrehozása.

A Backup-tároló létrehozásához kövesse az alábbi lépéseket.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.

### <a name="create-backup-vault"></a>Backup-tároló létrehozása

1. A keresőmezőbe írja be a **Backup-tárolók** kifejezést.
1. A **szolgáltatások** területen válassza a **Backup-tárolók** lehetőséget.
1. A **Backup-tárolók** lapon válassza a **Hozzáadás** lehetőséget.
1. Az **alapvető beállítások lap** **projekt részletei** területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva, majd válassza az **Új erőforráscsoport létrehozása** lehetőséget. A név mezőbe írja be a *myResourceGroup* nevet.

  ![Új erőforráscsoport létrehozása](./media/backup-vault-overview/new-resource-group.png)

1. A **példány részletei** területen írja  be a myVault **nevet a Backup-tároló neveként** , és válassza ki a választott régiót, ebben az esetben az *USA keleti* **régióját**.
1. Most válassza ki a **tárhely-redundanciát**. A tárterület-redundancia nem módosítható az elemek tárolóba való védelme után.
1. Javasoljuk, hogy ha az Azure-t elsődleges biztonsági mentési tárolási végpontként használja, folytassa az alapértelmezett **geo-redundáns** beállítás használatát.
1. Ha nem az Azure-t használja az elsődleges biztonsági mentési tároló végpontjaként, válassza a **Helyileg redundáns** lehetőséget, amellyel csökkentheti az Azure Storage-költségeit.
1. További információ a [földrajzi](../storage/common/storage-redundancy.md#geo-redundant-storage) és [helyi](../storage/common/storage-redundancy.md#locally-redundant-storage) redundanciáról.

  ![Tárolási redundancia kiválasztása](./media/backup-vault-overview/storage-redundancy.png)

1. Kattintson a lap alján található felülvizsgálat + Létrehozás gombra.

    ![Válassza a felülvizsgálat + létrehozás lehetőséget](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Backup-tároló törlése

Ez a szakasz a Backup-tárolók törlését ismerteti. A függőségek eltávolítására, majd a tároló törlésére vonatkozó utasításokat tartalmaz.

### <a name="before-you-start"></a>Előkészületek

A Backup-tárolót nem törölheti a következő függőségek bármelyikével:

- Védett adatforrásokat (például Azure Database for PostgreSQL-kiszolgálókat) tartalmazó tárolót nem lehet törölni.
- Olyan tárolót nem lehet törölni, amely biztonsági másolatokat tartalmaz.

Ha a függőségek eltávolítása nélkül próbálja meg törölni a tárolót, a következő hibaüzenetek jelenhetnek meg:

>A mentési tároló nem törölhető, mert a tárolóban meglévő biztonsági mentési példányok vagy biztonsági mentési szabályzatok vannak. Törölje az összes olyan biztonsági mentési példányt és biztonsági mentési házirendet, amely megtalálható a tárolóban, majd próbálja meg törölni a tárat.

### <a name="proper-way-to-delete-a-vault"></a>Tár törlésének megfelelő módja

>[!WARNING]
A következő művelet romboló, ezért nem vonható vissza. A rendszer véglegesen törli a védett kiszolgálóhoz társított biztonsági mentési adatokat és biztonsági másolati elemeket. Legyen óvatos.

A tárolók megfelelő törléséhez az alábbi lépéseket kell követnie:

- Győződjön meg arról, hogy vannak-e védett elemek:
  - A bal oldali navigációs sávon válassza a **biztonsági mentési példányok** lehetőséget. Először az itt felsorolt összes elemet törölni kell.

A lépések elvégzése után továbbra is törölheti a tárolót.

### <a name="delete-the-backup-vault"></a>A Backup-tároló törlése

Ha nincs több elem a tárolóban, válassza a **Törlés** lehetőséget a tároló irányítópultján. Egy megerősítő szöveg jelenik meg, amely megkérdezi, hogy szeretné-e törölni a tárolót.

![Tároló törlése](./media/backup-vault-overview/delete-vault.png)

1. Válassza az **Igen** lehetőséget annak ellenőrzéséhez, hogy törölni kívánja-e a tárolót. A tár törölve. A portál visszatér az **új** szolgáltatás menüjébe.

## <a name="monitor-and-manage-the-backup-vault"></a>A Backup-tároló figyelése és kezelése

Ez a szakasz azt ismerteti, hogyan használható a Backup-tároló **áttekintése** irányítópult a Backup-tárolók monitorozásához és kezeléséhez. Az Áttekintés panel két csempét tartalmaz: **feladatok** és **példányok**.

![Áttekintő irányítópult](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Biztonsági mentési példányok kezelése

A **feladatok** csempén a Backup-tárolóban található összes biztonsági mentési és visszaállítási feladat összefoglaló nézetét kapja meg. A csempe bármelyik számának kiválasztásával további információkat tekinthet meg egy adott adatforrás-típussal, a művelet típusával és állapotával kapcsolatos feladatokkal kapcsolatban.

![Biztonsági mentési példányok](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Biztonsági mentési feladatok kezelése

A **biztonsági mentési példányok** csempén a Backup-tárolóban található összes biztonsági mentési példány összesített nézetét kapja meg. A csempe bármelyik számának kiválasztásával további információkat tekinthet meg a biztonsági mentési példányokról egy adott adatforrás-típus és egy védelmi állapot tekintetében.

![Biztonsági mentési feladatok](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Következő lépések

- [Biztonsági mentés konfigurálása az Azure PostgreSQL-adatbázisokban](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
