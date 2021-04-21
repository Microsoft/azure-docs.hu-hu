---
title: Adatbázis visszaállítása biztonsági másolatból
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerje meg az időponthoz időben való visszaállítást, amellyel akár 35 nap alatt visszaállíthatja Azure SQL Database egy adatbázist Azure SQL Managed Instance példányban.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 670176d7478ddab3d17e15526df512dfa7e99fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762064"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Helyreállítás automatikus adatbázis-biztonsági mentések használatával – Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az adatbázis automatikus biztonsági mentésével az alábbi lehetőségek érhetők el az [adatbázisok helyreállításához.](automated-backups-overview.md) A következőket teheti:

- Hozzon létre egy új adatbázist ugyanazon a kiszolgálón a megőrzési időtartamon belül egy megadott időpontra helyreállítva.
- Hozzon létre egy adatbázist ugyanazon a kiszolgálón egy törölt adatbázis törlési ideje szerint helyreállítva.
- Hozzon létre egy új adatbázist bármely, ugyanabban a régióban található kiszolgálón, a legutóbbi biztonsági mentések pontra helyreállítva.
- Hozzon létre egy új adatbázist bármely másik régióban található bármely kiszolgálón, a legutóbbi replikált biztonsági mentések pontra helyreállítva.

Ha a biztonsági mentés [hosszú](long-term-retention-overview.md)távú megőrzését konfigurálta, bármely kiszolgálón létrehozhat egy új adatbázist bármely hosszú távú megőrzési biztonsági másolatból.

> [!IMPORTANT]
> A visszaállítás során nem írhatja felül a meglévő adatbázist.

Ha a Standard vagy a Prémium szolgáltatási szintet használja, az adatbázis-visszaállítás további tárolási költségeket is felvehet. A többletköltség akkor merül fel, ha a visszaállított adatbázis maximális mérete meghaladja a céladatbázis szolgáltatási és teljesítményszintje által tartalmazott tárterületet. A további tárhellyel kapcsolatos díjszabási részletekért lásd a SQL Database [oldalon.](https://azure.microsoft.com/pricing/details/sql-database/) Ha a felhasznált terület tényleges mennyisége kisebb a benne foglalt tárterületnél, elkerülheti ezt a többletköltséget, ha a maximális adatbázisméretet a benne foglalt mennyiségre használhatja.

## <a name="recovery-time"></a>Helyreállítási idő

Az adatbázisok automatikus biztonsági másolatokkal történő visszaállításának helyreállítási idejét számos tényező befolyásolja:

- Az adatbázis mérete.
- Az adatbázis számítási mérete.
- Az érintett tranzakciónaplók száma.
- A visszaállítási pontra való helyreállításhoz visszajátszható tevékenység mennyisége.
- A hálózati sávszélesség, ha a visszaállítás egy másik régióba történik.
- Az egyidejű visszaállítási kérések száma, amely a célterületen van feldolgozva.

Nagy méretű vagy nagyon aktív adatbázisokban a visszaállítás több órát is igénybe vehet. Ha a régióban elhúzódó leállás következik be, előfordulhat, hogy a vészhelyreállítási folyamat túl sok georedundáns visszaállítási kérést ad ki. Ha túl sok a kérés, az egyes adatbázisok helyreállítási ideje megnőhet. A legtöbb adatbázis-visszaállítás kevesebb mint 12 óra alatt befejeződik.

Korlátozva van, hogy egy előfizetésen belül mennyi egyidejű visszaállítási kérést lehet kiadni. Ezek a korlátozások az időponthoz kötött, a georedundáns és a hosszú távon megőrzött biztonsági másolatokból történő visszaállítások minden kombinációjára vonatkoznak.

| **Üzembehelyezési lehetőség** | **Egyszerre feldolgozható kérések maximális száma** | **Egyszerre beküldhető kérések maximális száma** |
| :--- | --: | --: |
|**Önálló adatbázis (előfizetésenként)**|30|100|
|**Rugalmas készlet (készletenként)**|4|2000|


Nincs beépített módszer a teljes kiszolgáló visszaállítására. A feladat elvégzésének egy példáját a következő [Azure SQL Database: Teljes kiszolgáló-helyreállítás.](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)

> [!IMPORTANT]
> Az automatikus biztonsági mentések használatával történő helyreállításhoz az SQL Server-közreműködői vagy a SQL Managed Instance-közreműködői szerepkör tagja kell lennie (a helyreállítási célhelytől függően), vagy az előfizetés tulajdonosának kell lennie. További információ: [Azure RBAC: Beépített szerepkörök.](../../role-based-access-control/built-in-roles.md) A helyreállításhoz használhatja a Azure Portal, a PowerShellt vagy a REST API. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Az önálló, készletbekészletett vagy példány-adatbázisokat visszaállíthatja egy korábbi időpontra a Azure Portal, [a PowerShell](/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST API.](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.) A kérés bármilyen szolgáltatási szintet vagy számítási méretet megadhat a visszaállított adatbázishoz. Győződjön meg arról, hogy elegendő erőforrással rendelkezik a kiszolgálón, amelyre az adatbázist visszaállítja. 

Ha elkészült, a visszaállítás egy új adatbázist hoz létre ugyanazon a kiszolgálón, mint az eredeti adatbázis. A visszaállított adatbázisért normál díjszabás szerint kell fizetni a szolgáltatási szint és a számítási méret alapján. Az adatbázis visszaállításának befejezéséig nem kell költségeket fizetni.

Helyreállítási célokból általában egy korábbi pontra kell visszaállítani az adatbázist. A visszaállított adatbázist az eredeti adatbázis helyett is használhatja, vagy használhatja adatforrásként az eredeti adatbázis frissítéséhez.

- **Adatbázis cseréje**

  Ha azt szeretné, hogy a visszaállított adatbázis az eredeti adatbázist helyettesítsen, meg kell adnia az eredeti adatbázis számítási méretét és szolgáltatási rétegét. Ezután átnevezheti az eredeti adatbázist, és a T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) parancsával eredeti nevet adhat a visszaállított adatbázisnak.

- **Adat-helyreállítás**

  Ha azt tervezi, hogy adatokat fog lekérni a visszaállított adatbázisból egy felhasználói vagy alkalmazáshiba utáni helyreállításhoz, olyan adat-helyreállítási szkriptet kell írnia és végrehajtania, amely kinyeri az adatokat a visszaállított adatbázisból, és az eredeti adatbázisra vonatkozik. Bár a visszaállítási művelet hosszú időt vehet igénybe, a visszaállítási adatbázis a visszaállítási folyamat során végig látható az adatbázislistában. Ha törli az adatbázist a visszaállítás során, a visszaállítási művelet megszakad, és nem számítunk fel díjat az adatbázisért, amely nem fejeződött be.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Időponthoz időben való visszaállítás a Azure Portal

Egy vagy többpéldányos adatbázist az adott időpontra a visszaállításra kívánt adatbázis áttekintési paneljének egy adott időpontra való visszaállításával Azure Portal.

#### <a name="sql-database"></a>SQL Database

Ha az adatbázist a megfelelő időpontra Azure Portal, nyissa meg az adatbázis áttekintő lapját, és válassza az eszköztár **Visszaállítás** pontját. Válassza ki a biztonsági mentési forrást, és válassza ki azt az időponthoz készített biztonsági mentési pontot, amelyből új adatbázist fog létrehozni.

  ![Képernyőkép az adatbázis-visszaállítási lehetőségekről a SQL Database.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Ha egy felügyeltpéldány-adatbázist a Azure Portal segítségével egy adott időpontra is vissza akar állítani, nyissa meg az adatbázis áttekintő lapját, és válassza az eszköztár **Visszaállítás** pontját. Válassza ki azt az időponthoz készített biztonsági mentési pontot, amelyből új adatbázist fog létrehozni.

  ![Képernyőkép a felügyelt SQL-példány adatbázis-visszaállítási beállításairól.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Az adatbázisok biztonsági másolatból történő programozott visszaállításáról lásd: Programozott helyreállítás [automatikus biztonsági mentésekkel.](recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Törölt adatbázis visszaállítása

A törölt adatbázisokat visszaállíthatja a törlési időpontra vagy egy korábbi időpontra ugyanazon a kiszolgálón vagy ugyanazon felügyelt példányon. Ezt a következő parancsokkal tudja Azure Portal, [a PowerShell](/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST (createMode=Restore) használatával.](/rest/api/sql/databases/createorupdate) A törölt adatbázisok visszaállításához hozzon létre egy új adatbázist a biztonsági másolatból.

> [!IMPORTANT]
> Ha töröl egy kiszolgálót vagy felügyelt példányt, az összes adatbázisa is törlődik, és nem állítható helyre. Törölt kiszolgálót vagy felügyelt példányt nem lehet visszaállítani.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Törölt adatbázis-visszaállítás a Azure Portal

A törölt adatbázisokat visszaállítja a Azure Portal a kiszolgáló vagy a felügyelt példány erőforrásából.

> [!TIP]
> Eltarthat néhány percig, amíg a nemrég törölt  adatbázisok megjelennek a törölt adatbázisok oldalán a Azure Portal, vagy ha programozott módon jeleníti meg a törölt [adatbázisokat.](#programmatic-recovery-using-automated-backups)

#### <a name="sql-database"></a>SQL Database

A törölt adatbázisok törlési időpontra való helyreállításához a Azure Portal nyissa meg a kiszolgáló áttekintő lapját, és válassza a **Törölt adatbázisok lehetőséget.** Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be annak az új adatbázisnak a nevét, amely a biztonsági másolatból visszaállított adatokkal fog létrejönni.

  ![Képernyőkép a törölt adatbázis visszaállításáról](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Ha a felügyelt adatbázist a felügyelt adatbázis használatával Azure Portal, nyissa meg a felügyelt példány áttekintési oldalát, és válassza a **Törölt adatbázisok lehetőséget.** Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be annak az új adatbázisnak a nevét, amely a biztonsági másolatból visszaállított adatokkal fog létrejönni.

  ![Képernyőkép a törölt Azure SQL Managed Instance adatbázisról](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Törölt adatbázis-visszaállítás a PowerShell használatával

Az alábbi példaszkprogramokkal visszaállíthatja a törölt adatbázist egy SQL Database vagy SQL Managed Instance PowerShell használatával.

#### <a name="sql-database"></a>SQL Database

A törölt adatbázisok visszaállítását bemutató PowerShell-példaszk Azure SQL Database lásd: Adatbázis visszaállítása [a PowerShell használatával.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

A törölt példányok adatbázisának visszaállítását bemutató PowerShell-példaszk szkriptért lásd: Törölt példányadatbázis visszaállítása a [PowerShell használatával](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> A törölt adatbázisok programozott módon történő visszaállításáról lásd: Helyreállítás programozott módon, automatikus biztonsági [mentésekkel.](recovery-using-backups.md)

## <a name="geo-restore"></a>Georedundáns visszaállítás

> [!IMPORTANT]
> A georedundáns visszaállítás csak a georedundáns biztonsági mentési tárolóval konfigurált SQL-adatbázisokhoz vagy felügyelt [példányokhoz érhető el.](automated-backups-overview.md#backup-storage-redundancy)

A legutóbbi georeplikált biztonsági mentések SQL Database bármely azure-régió bármely felügyelt példányán visszaállíthatja az adatbázist bármely SQL Database-kiszolgálón vagy példányadatbázison. A georedúnó-visszaállítás georeplikált biztonsági mentést használ forrásként. Georedún-visszaállítást akkor is kérhet, ha az adatbázis vagy az adatközpont egy kimaradás miatt nem érhető el.

A földrajzi visszaállítás az alapértelmezett helyreállítási lehetőség, ha az adatbázis egy, az üzemeltetési régióban történt incidens miatt nem érhető el. Az adatbázist bármely másik régióban található kiszolgálón visszaállíthatja. A biztonsági mentések készítésének és a georeplikált adatoknak egy másik régióban lévő Azure-blobba való replikálása között késés van. Ennek eredményeképpen a visszaállított adatbázis akár egy órával az eredeti adatbázis mögött is lehet. Az alábbi ábrán egy adatbázis-visszaállítás látható egy másik régióban utoljára elérhető biztonsági másolatból.

![Georedúnó-visszaállítás ábrája](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Georedún helyreállítás a Azure Portal

A Azure Portal létrehoz egy új egy- vagy felügyeltpéldány-adatbázist, és kiválasztja az elérhető georedukt helyreállító biztonsági mentést. Az újonnan létrehozott adatbázis tartalmazza a georedurált biztonsági mentési adatokat.

#### <a name="sql-database"></a>SQL Database

Ha egyetlen adatbázist földrajzi Azure Portal a választott régióban és kiszolgálón található adatbázisból, kövesse az alábbi lépéseket:

1. Az **Irányítópulton** válassza **a**  >  **Létrehozási SQL Database** lehetőséget. Az Alapvető **beállítások lapon** adja meg a szükséges adatokat.
2. Válassza **a További beállítások lehetőséget.**
3. A **Meglévő adatok használata beállításhoz válassza** a Biztonsági mentés **lehetőséget.**
4. A **Biztonsági mentés** beállításnál válasszon ki egy biztonsági másolatot az elérhető georedúnó-visszaállítási biztonsági másolatok listájából.

    ![Képernyőkép a Create SQL Database beállításokról](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

A biztonsági másolatból hozzon létre egy új adatbázist. Amikor adatbázist hoz létre a Azure SQL Database, az tartalmazza a visszaállított georedúnó-visszaállítási biztonsági mentést.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Ha egy felügyeltpéldány-adatbázist szeretne georeduktálni a Azure Portal-ból egy kiválasztott régióban található meglévő felügyelt példányra, válasszon ki egy felügyelt példányt, amelyen vissza szeretné állítani az adatbázist. Kövesse az alábbi lépéseket:

1. Válassza **az Új adatbázis lehetőséget.**
2. Írjon be egy kívánt adatbázisnevet.
3. A **Meglévő adatok használata alatt válassza a** Biztonsági mentés **lehetőséget.**
4. Válasszon ki egy biztonsági másolatot az elérhető georedúnis biztonsági másolatok listájából.

    ![Képernyőkép az Új adatbázis beállításairól](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Egy új adatbázis létrehozásának befejezése. A példányadatbázis létrehozásakor az tartalmazza a visszaállított georedúnó-visszaállítási biztonsági másolatot.

### <a name="geo-restore-by-using-powershell"></a>Geored visszaállítás a PowerShell használatával

#### <a name="sql-database"></a>SQL Database

Ha olyan PowerShell-parancsfájlt keres, amely bemutatja, hogyan végezhet georedún-visszaállítást egyetlen adatbázison, tekintse meg az egyetlen adatbázis korábbi időpontra való visszaállítását [a PowerShell](scripts/restore-database-powershell.md)használatával.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Ha olyan PowerShell-szkriptet keres, amely bemutatja, hogyan végezhet georeduktikai visszaállítást egy felügyeltpéldány-adatbázison, tekintse meg [a felügyeltpéldány-adatbázis](../managed-instance/scripts/restore-geo-backup.md)másik földrajzi régióba való visszaállítását a PowerShell használatával.

### <a name="geo-restore-considerations"></a>Georedúnitikai szempontok

Földrajzi másodlagos adatbázison nem hajthat végre időponthoz időben végzett visszaállítást. Ezt csak egy elsődleges adatbázison lehet megtenni. A kimaradásból való helyreállítás georedúnit használatával kapcsolatos részletes információkért lásd: [Helyreállítás kimaradás után.](../../key-vault/general/disaster-recovery-guidance.md)

> [!IMPORTANT]
> A georedúmentás a legapvetőbb vészhelyreállítási megoldás, amely a SQL Database és SQL Managed Instance. Automatikusan létrehozott, legfeljebb 1 órás helyreállításipont-célkitűzéssel (RPO) és legfeljebb 12 órás becsült helyreállítási idővel létrehozott georeplikált biztonsági másolatra támaszkodik. Nem garantálja, hogy a célterület képes lesz helyreállítani az adatbázisokat egy regionális kimaradás után, mivel a kereslet hirtelen növekedése várható. Ha az alkalmazás viszonylag kis méretű adatbázisokat használ, és nem kritikus fontosságú a vállalat számára, a georedúmentáció megfelelő vészhelyreállítási megoldás. 
>
> A nagy méretű adatbázisokat igénylő, üzletmenet-folytonosságot igénylő, üzleti fontosságú alkalmazásokhoz használjon automatikus [feladatátvételi csoportokat.](auto-failover-group-overview.md) Sokkal alacsonyabb RPO-t és helyreállítási időre vonatkozó célkitűzést kínál, és a kapacitás mindig garantált. 
>
> További információ az üzletmenet-folytonossági lehetőségekről: [Az üzletmenet folytonosságának áttekintése.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatic-recovery-using-automated-backups"></a>Programozott helyreállítás automatizált biztonsági mentésekkel

Használhatja a Azure PowerShell vagy a REST API is a helyreállításhoz. Az alábbi táblázatok az elérhető parancsokat ismertetik.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a SQL Database és SQL Managed Instance, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kifejlesztésére. A parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/) Az Az modulban és a Azure Resource Manager modulokban található parancsok argumentumai nagy mértékben azonosak.

#### <a name="sql-database"></a>SQL Database

Önálló vagy készletbekészletett adatbázis visszaállításához lásd: [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Visszaállít egy adatbázist. |

  > [!TIP]
  > Az adatbázis időponthoz időben való visszaállítását bemutató PowerShell-parancsfájlminta: Adatbázis visszaállítása [a PowerShell használatával.](scripts/restore-database-powershell.md)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Felügyeltpéldány-adatbázis visszaállításához lásd: [Restore-AzSqlInstanceDatabase.](/powershell/module/az.sql/restore-azsqlinstancedatabase)

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Lekért egy vagy több felügyelt példányt. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Lekért egy példányadatbázist. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Visszaállít egy példányadatbázist. |

### <a name="rest-api"></a>REST API

Adatbázis visszaállítása a következő REST API:

| API | Leírás |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Visszaállít egy adatbázist. |
| [Adatbázis-állapot létrehozása vagy frissítése](/rest/api/sql/operations) |A visszaállítási művelet során visszaadja az állapotot. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Az adatbázis Azure CLI használatával való visszaállításához lásd: [az sql db restore.](/cli/azure/sql/db#az_sql_db_restore)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Felügyeltpéldány-adatbázis Azure CLI-n keresztüli visszaállításához lásd: [az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore).

## <a name="summary"></a>Összefoglalás

Az automatikus biztonsági mentések megvédik az adatbázisokat a felhasználói és alkalmazáshibáktól, az adatbázisok véletlen törlésétől és a hosszan tartó kimaradástól. Ez a beépített funkció minden szolgáltatási szinthez és számítási mérethez elérhető.

## <a name="next-steps"></a>Következő lépések

- [Az üzletmenet folytonossága – áttekintés](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database biztonsági mentések készítése](automated-backups-overview.md)
- [Hosszú távú megőrzés](long-term-retention-overview.md)
- A gyorsabb helyreállítási lehetőségekkel kapcsolatban lásd: [Aktív georeplikáció](active-geo-replication-overview.md) vagy automatikus [feladatátvételi csoportok.](auto-failover-group-overview.md)
