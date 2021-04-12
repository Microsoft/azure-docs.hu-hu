---
title: Adatbázis visszaállítása biztonsági másolatból
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Ismerje meg az időponthoz való visszaállítást, amely lehetővé teszi egy adatbázis visszaállítását Azure SQL Database vagy egy Azure SQL felügyelt példányban, akár 35 napig.
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
ms.openlocfilehash: 0c3db3b3f22f9f2639012068924708537f9ada77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795331"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Helyreállítás automatikus adatbázis-biztonsági másolatokkal – Azure SQL Database & SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A következő beállítások érhetők el az adatbázis-helyreállításhoz az [automatikus adatbázis-biztonsági mentések](automated-backups-overview.md)használatával. A következőket teheti:

- Hozzon létre egy új adatbázist ugyanarra a kiszolgálóra, amely a megőrzési időtartamon belül egy megadott időpontra lett helyreállítva.
- Hozzon létre egy adatbázist ugyanazon a kiszolgálón, amely a törölt adatbázis törlési idejére lett helyreállítva.
- Hozzon létre egy új adatbázist minden olyan kiszolgálón, amely ugyanabban a régióban található, és a legújabb biztonsági másolatok pontjára lett helyreállítva.
- Hozzon létre egy új adatbázist bármely más régióban lévő kiszolgálón, amely a legutóbbi replikált biztonsági másolatok pontjára lett helyreállítva.

Ha a [biztonsági mentés hosszú távú megőrzését](long-term-retention-overview.md)állította be, létrehozhat egy új adatbázist is a hosszú távú adatmegőrzési biztonsági mentésből bármely kiszolgálón.

> [!IMPORTANT]
> A meglévő adatbázisok nem írhatók felül a visszaállítás során.

Ha a standard vagy prémium szintű szolgáltatási szintet használja, az adatbázis-visszaállítás további tárolási költséget eredményezhet. Az extra költségek akkor merülnek fel, ha a visszaállított adatbázis maximális mérete nagyobb, mint a céladatbázis szolgáltatási szintjéhez és a teljesítmény szintjéhez tartozó tárterület mennyisége. A további tárterület részletes díjszabását a [SQL Database díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/sql-database/)tekintheti meg. Ha a felhasznált terület tényleges mennyisége kevesebb, mint a tárhelyek mennyisége, akkor ezt a többletköltséget elkerülheti, ha a maximális adatbázis méretét a befoglalt mennyiségre állítja.

## <a name="recovery-time"></a>Helyreállítás ideje

Az adatbázis automatikus biztonsági mentések használatával történő visszaállításának helyreállítási idejét számos tényező befolyásolja:

- Az adatbázis mérete.
- Az adatbázis számítási mérete.
- A tranzakciós naplók száma.
- Azon tevékenység mennyisége, amelyet újra kell játszani a visszaállítási pontra történő helyreállításhoz.
- A hálózati sávszélesség, ha a visszaállítás egy másik régióba történik.
- A megcélzott régióban feldolgozott egyidejű visszaállítási kérelmek száma.

Nagyméretű vagy nagyon aktív adatbázisok esetén a visszaállítás több órát is igénybe vehet. Ha a régióban elhúzódó leállás következik be, előfordulhat, hogy a vészhelyreállítási folyamat túl sok georedundáns visszaállítási kérést ad ki. Ha túl sok a kérés, az egyes adatbázisok helyreállítási ideje megnőhet. A legtöbb adatbázis-visszaállítás kevesebb, mint 12 óra alatt fejeződik be.

Korlátozva van, hogy egy előfizetésen belül mennyi egyidejű visszaállítási kérést lehet kiadni. Ezek a korlátozások az időponthoz kötött, a georedundáns és a hosszú távon megőrzött biztonsági másolatokból történő visszaállítások minden kombinációjára vonatkoznak.

| **Üzembehelyezési lehetőség** | **Egyszerre feldolgozható kérések maximális száma** | **Egyszerre beküldhető kérések maximális száma** |
| :--- | --: | --: |
|**Önálló adatbázis (előfizetésenként)**|30|100|
|**Rugalmas készlet (készletenként)**|4|2000|


Nincs beépített módszer a teljes kiszolgáló visszaállítására. A feladat végrehajtásával kapcsolatos példát a [Azure SQL Database: teljes kiszolgáló helyreállítása](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)című témakörben talál.

> [!IMPORTANT]
> Az automatizált biztonsági másolatok használatával történő helyreállításhoz a SQL Server közreműködő szerepkör vagy az SQL felügyelt példány közreműködői szerepkör (a helyreállítási célhelytől függően) tagjának kell lennie az előfizetésben, vagy az előfizetés tulajdonosának kell lennie. További információt az [Azure RBAC: beépített szerepkörök](../../role-based-access-control/built-in-roles.md)című témakörben talál. A helyreállítást a Azure Portal, a PowerShell vagy a REST API használatával végezheti el. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A Azure Portal, a [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)vagy a [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)használatával visszaállíthat egy önálló, készletezett vagy példány-adatbázist egy korábbi időpontra. A kérelem bármilyen szolgáltatási szintet vagy számítási méretet megadhat a visszaállított adatbázis számára. Győződjön meg arról, hogy elegendő erőforrása van azon a kiszolgálón, amelyhez az adatbázist állítja vissza. 

Ha elkészült, a Restore egy új adatbázist hoz létre az eredeti adatbázissal megegyező kiszolgálón. A visszaállított adatbázist a szolgáltatás szintjétől és a számítási mérettől függően a normál díjszabás szerint számítjuk fel. Az adatbázis visszaállítása után nem számítunk fel díjat.

Általában egy adatbázis visszaállítása egy korábbi helyre helyreállítás céljából. A visszaállított adatbázist felhasználhatja az eredeti adatbázis helyett, vagy adatforrásként használhatja az eredeti adatbázis frissítéséhez.

- **Adatbázis cseréje**

  Ha azt szeretné, hogy a visszaállított adatbázis helyettesítse az eredeti adatbázist, akkor meg kell adnia az eredeti adatbázis számítási méretét és a szolgáltatási szintet. Ezután átnevezheti az eredeti adatbázist, és megadhatja az eredeti nevet a visszaállított adatbázisnak az [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) parancs használatával a T-SQL-ben.

- **Adathelyreállítás**

  Ha a visszaállított adatbázisból szeretne adatokat lekérni egy felhasználói vagy alkalmazáshiba általi helyreállításhoz, írnia kell és végre kell hajtania egy olyan adathelyreállítási parancsfájlt, amely kinyeri az adatokat a visszaállított adatbázisból, és az eredeti adatbázisra vonatkozik. Bár a visszaállítási művelet végrehajtása hosszú időt is igénybe vehet, a visszaállítási folyamat során az adatbázis-lista látható. Ha a visszaállítás során törli az adatbázist, a visszaállítási művelet meg lesz szakítva, és nem kell fizetnie az adatbázisnak, amely nem végezte el a visszaállítást.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Időponthoz való visszaállítás Azure Portal használatával

A Azure Portalban visszaállítani kívánt adatbázis áttekintés paneljéről egy adott időpontra állíthatja helyre az egy-vagy példány-adatbázist.

#### <a name="sql-database"></a>SQL Database

Ha a Azure Portal használatával kívánja helyreállítani az adatbázist egy adott időpontra, nyissa meg az adatbázis áttekintése lapot, és válassza a **visszaállítás** lehetőséget az eszköztáron. Válassza ki a biztonsági mentési forrást, és válassza ki azt az időponthoz tartozó biztonsági mentési pontot, amelyből új adatbázist kíván létrehozni.

  ![Képernyőkép a SQL Database adatbázis-visszaállítási lehetőségeiről.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Felügyelt példány adatbázisának egy időpontra történő helyreállításához a Azure Portal segítségével nyissa meg az adatbázis-áttekintés lapot, és válassza a **visszaállítás** lehetőséget az eszköztáron. Válassza ki azt az időponthoz tartozó biztonsági mentési pontot, amelyből új adatbázist kíván létrehozni.

  ![Képernyőkép a felügyelt SQL-példány adatbázis-visszaállítási lehetőségeiről.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Egy adatbázis biztonsági másolatból való programozott visszaállításával kapcsolatban lásd: [programozott helyreállítás automatizált biztonsági mentéssel](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Adatbázis-visszaállítás törölve

A törölt adatbázist visszaállíthatja a törlési időre vagy egy korábbi időpontra ugyanazon a kiszolgálón vagy ugyanazon a felügyelt példányon. Ezt a Azure Portal, a [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)vagy a [Rest (CreateMode = Restore)](/rest/api/sql/databases/createorupdate)használatával végezheti el. Egy törölt adatbázist úgy állíthat vissza, hogy létrehoz egy új adatbázist a biztonsági mentésből.

> [!IMPORTANT]
> Ha töröl egy kiszolgálót vagy felügyelt példányt, az összes adatbázisa is törlődik, és nem állítható helyre. A törölt kiszolgálók vagy a felügyelt példányok nem állíthatók vissza.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Az adatbázis-visszaállítás törölve a Azure Portal használatával

A törölt adatbázisokat a kiszolgálóról vagy a felügyelt példányok erőforrásból kell visszaállítani a Azure Portalból.

> [!TIP]
> Több percet is igénybe vehet, hogy a nemrég törölt adatbázisok megjelenjenek a Azure Portal **törölt adatbázisok** lapján, vagy ha [programozott](#programmatic-recovery-using-automated-backups)módon jeleníti meg a törölt adatbázisokat.

#### <a name="sql-database"></a>SQL Database

Ha a törölt adatbázist a Azure Portal használatával szeretné visszaállítani a törlési időre, nyissa meg a kiszolgáló áttekintése lapot, és válassza a **törölt adatbázisok** elemet. Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be az új adatbázis nevét, amely a biztonsági másolatból visszaállított adatokkal lesz létrehozva.

  ![Képernyőfelvétel a törölt adatbázis visszaállításáról](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Felügyelt adatbázis helyreállításához a Azure Portal segítségével nyissa meg a felügyelt példányok áttekintése lapot, és válassza a **törölt adatbázisok** lehetőséget. Válassza ki a visszaállítani kívánt törölt adatbázist, és írja be az új adatbázis nevét, amely a biztonsági másolatból visszaállított adatokkal lesz létrehozva.

  ![Képernyőkép a törölt Azure SQL felügyelt példány-adatbázis visszaállításáról](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Adatbázis-visszaállítás törölve a PowerShell használatával

A következő parancsfájlokkal visszaállíthat egy törölt adatbázist SQL Database vagy SQL felügyelt példányhoz a PowerShell használatával.

#### <a name="sql-database"></a>SQL Database

A törölt adatbázisok Azure SQL Database-ben való visszaállítását bemutató minta PowerShell-parancsfájlhoz lásd: [adatbázis visszaállítása a PowerShell használatával](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL Managed Instance

A törölt példányok adatbázisának visszaállítását bemutató minta PowerShell-parancsfájlhoz lásd: [példány-adatbázis visszaállítása a PowerShell használatával](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> A törölt adatbázisok programozott visszaállításával kapcsolatban lásd: a [helyreállítás programozott módon történő végrehajtása automatizált biztonsági mentéssel](recovery-using-backups.md).

## <a name="geo-restore"></a>Georedundáns visszaállítás

> [!IMPORTANT]
> A Geo-visszaállítás csak az SQL-adatbázisok vagy a Geo-redundáns [biztonsági mentési tárolóval](automated-backups-overview.md#backup-storage-redundancy)konfigurált felügyelt példányok esetén érhető el.

A legutóbbi földrajzilag replikált biztonsági másolatokból bármely Azure-régióban felügyelt példányon visszaállíthat egy adatbázist bármely SQL Database-kiszolgálón vagy egy példány-adatbázison. A Geo-visszaállítás a forrásként egy földrajzilag replikált biztonsági mentést használ. Akkor is kérheti a Geo-visszaállítást, ha az adatbázis vagy az adatközpont egy leállás miatt nem érhető el.

A Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha az adatbázis nem érhető el az üzemeltetési régióban lévő incidens miatt. Az adatbázist bármely másik régióban található kiszolgálón visszaállíthatja. A biztonsági másolat készítése és az egy másik régióban lévő Azure-blobba való földrajzi replikálása között késés történik. Ennek eredményeképpen a visszaállított adatbázis akár egy óráig is elvégezhető az eredeti adatbázis mögött. Az alábbi ábrán egy másik régió utolsó elérhető biztonsági másolatából származó adatbázis-visszaállítás látható.

![Geo-visszaállítási ábra](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-visszaállítás a Azure Portal használatával

A Azure Portal létre kell hoznia egy új, egy vagy felügyelt példány-adatbázist, és ki kell választania egy elérhető geo-visszaállítási biztonsági mentést. Az újonnan létrehozott adatbázis tartalmazza a Geo-visszaállított biztonsági mentési adatkészleteket.

#### <a name="sql-database"></a>SQL Database

Az alábbi lépéseket követve geo-vissza lehet állítani egyetlen adatbázist a régió és a választott kiszolgáló Azure Portalból:

1. Az **irányítópulton** válassza a **Hozzáadás**  >  **Létrehozás SQL Database** elemet. Az **alapvető beállítások** lapon adja meg a szükséges adatokat.
2. Válassza a **További beállítások** lehetőséget.
3. A **meglévő adatfelhasználáshoz** válassza a **biztonsági mentés** lehetőséget.
4. A **biztonsági mentéshez** válassza ki a biztonsági mentést az elérhető geo-visszaállítási biztonsági mentések listájából.

    ![Képernyőkép a SQL Database létrehozásának lehetőségeiről](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Fejezze be az új adatbázis biztonsági másolatból való létrehozásának folyamatát. Amikor Azure SQL Databaseban hoz létre adatbázist, a visszaállított geo-visszaállítási biztonsági másolatot tartalmazza.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Felügyelt példányok adatbázisának geo-visszaállítása a Azure Portal egy meglévő felügyelt példányra egy tetszőleges régióban, válassza ki azt a felügyelt példányt, amelyen vissza kívánja állítani az adatbázist. Kövesse az alábbi lépéseket:

1. Válassza az **új adatbázis** lehetőséget.
2. Írja be a kívánt adatbázis nevét.
3. A **meglévő adat használata** területen válassza a **biztonsági mentés** lehetőséget.
4. Válasszon ki egy biztonsági másolatot az elérhető geo-visszaállítási biztonsági másolatok listájából.

    ![Képernyőfelvétel az új adatbázis-beállításokról](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Fejezze be az új adatbázis létrehozásának folyamatát. A példány-adatbázis létrehozásakor a visszaállított geo-visszaállítási biztonsági másolatot tartalmazza.

### <a name="geo-restore-by-using-powershell"></a>Geo-visszaállítás a PowerShell használatával

#### <a name="sql-database"></a>SQL Database

Egy olyan PowerShell-parancsfájl esetében, amely bemutatja, hogyan végezhető el a Geo-visszaállítás egyetlen adatbázishoz, lásd: [a PowerShell használata egy adott adatbázis egy korábbi időpontra való visszaállításához](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>SQL Managed Instance

A felügyelt példányok adatbázisának geo-visszaállítását bemutató PowerShell-szkriptet a következő témakörben talál: a [felügyelt példányok adatbázisának visszaállítása másik földrajzi régióra a PowerShell használatával](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Geo-visszaállítási megfontolások

A Geo-másodlagos adatbázison nem hajtható végre időponthoz való visszaállítás. Ezt csak elsődleges adatbázison teheti meg. További információ a Geo-visszaállítás a leállás utáni helyreállításhoz való használatáról: [áramkimaradás](../../key-vault/general/disaster-recovery-guidance.md)miatti helyreállítás.

> [!IMPORTANT]
> A Geo-visszaállítás a SQL Database és az SQL felügyelt példányában elérhető alapszintű katasztrófa-helyreállítási megoldás. A szolgáltatás automatikusan létrehozott geo-replikált biztonsági mentéseket egy helyreállítási időkorláttal (RPO), legfeljebb 1 órára, a becsült helyreállítási időt pedig akár 12 óráig is elvégezheti. Nem garantálja, hogy a célként megadott régió a regionális leállás után helyreállíthatja az adatbázisokat, mivel a kereslet jelentős növekedése várható. Ha az alkalmazás viszonylag kis adatbázisokat használ, és nem kritikus fontosságú a vállalat számára, a Geo-visszaállítás egy megfelelő vész-helyreállítási megoldás. 
>
> Az üzleti szempontból kritikus fontosságú alkalmazások esetében, amelyeknek nagy adatbázisokra van szükségük, és biztosítaniuk kell az üzletmenet folytonosságát, használja az [automatikus feladatátvételi csoportokat](auto-failover-group-overview.md). Sokkal alacsonyabb RPO és helyreállítási idő célkitűzést kínál, és a kapacitás mindig garantált. 
>
> További információ az üzletmenet-folytonossági lehetőségekről: [az üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md).

## <a name="programmatic-recovery-using-automated-backups"></a>Programozott helyreállítás automatizált biztonsági mentéssel

A helyreállításhoz Azure PowerShell vagy a REST API is használhatja. A következő táblázatok ismertetik az elérhető parancsok készletét.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modult továbbra is támogatja a SQL Database és az SQL felügyelt példánya, de az az. SQL modul jövőbeli fejlesztése is. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és Azure Resource Manager moduljaiban található parancsok argumentumai nagy mértékben azonosak.

#### <a name="sql-database"></a>SQL Database

Önálló vagy készletezett adatbázis visszaállításához tekintse meg a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)című témakört.

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Visszaállítás – AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Visszaállítja az adatbázist. |

  > [!TIP]
  > Az adatbázisok egy adott időpontban történő visszaállítását bemutató minta PowerShell-parancsfájlt az [adatbázis visszaállítása a PowerShell használatával](scripts/restore-database-powershell.md)című témakörben talál.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

A felügyelt példányok adatbázisának visszaállításával kapcsolatban lásd: [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Egy vagy több felügyelt példány beolvasása. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Lekéri egy példány-adatbázist. |
  | [Visszaállítás – AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Visszaállítja egy példány-adatbázist. |

### <a name="rest-api"></a>REST API

Adatbázis visszaállítása a REST API használatával:

| API | Leírás |
| --- | --- |
| [REST (createMode = helyreállítás)](/rest/api/sql/databases) |Visszaállítja az adatbázist. |
| [Adatbázis-létrehozási vagy-frissítési állapot beolvasása](/rest/api/sql/operations) |Visszaadja az állapotot egy visszaállítási művelet során. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Az adatbázis az Azure CLI használatával történő visszaállításához lásd [az az SQL db Restore (az SQL adatbázis visszaállítása](/cli/azure/sql/db#az-sql-db-restore)) című témakört.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

A felügyelt példányok adatbázisának az Azure CLI használatával történő visszaállításához lásd [az az SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Összefoglalás

Az automatikus biztonsági mentések védik az adatbázisokat a felhasználók és alkalmazások hibáiból, a véletlen adatbázis-törlésből és a hosszan tartó kimaradásokból. Ez a beépített képesség minden szolgáltatási réteghez és számítási mérethez elérhető.

## <a name="next-steps"></a>Következő lépések

- [Az üzletmenet folytonossága – áttekintés](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Automatikus biztonsági mentések SQL Database](automated-backups-overview.md)
- [Hosszú távú megőrzés](long-term-retention-overview.md)
- A gyorsabb helyreállítási lehetőségek megismeréséhez lásd: [aktív földrajzi replikálás](active-geo-replication-overview.md) vagy [automatikus feladatátvételi csoportok](auto-failover-group-overview.md).
