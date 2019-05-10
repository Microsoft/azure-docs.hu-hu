---
title: Azure SQL-adatbázis visszaállítása biztonsági másolatból |} A Microsoft Docs
description: Ismerje meg, amely lehetővé teszi, hogy állítsa vissza az Azure SQL Database egy korábbi időpontra az időt (legfeljebb 35 napra) időponthoz visszaállítás.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 47bf59adb33f3685b31430c652b31880d383833e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232643"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Automatikus biztonsági adatbázismentés használatával Azure SQL-adatbázis helyreállítása

Alapértelmezés szerint az SQL Database biztonsági mentések blob georeplikált tárolás (RA-GRS) vannak tárolva. Az alábbi lehetőségek érhetők el az adatbázis helyreállítási használatával [adatbázisok biztonsági mentése automatikus](sql-database-automated-backups.md):

- Hozzon létre egy új adatbázist ugyanazon a kiszolgálón az SQL Database adatmegőrzési időszakán belül egy adott időpontra vissza.
- Hozzon létre egy adatbázis ugyanazon a kiszolgálón az SQL Database és a Törlés időpontja, törölt adatbázis helyreállítása.
- Hozzon létre egy új adatbázist ugyanabban a régióban, hogy a legújabb biztonsági mentések a helyreállított bármely SQL Database-kiszolgálón.
- Hozzon létre egy új adatbázist bármely SQL Database-kiszolgáló helyreállítása, hogy a a legújabb replikált biztonsági mentések bármelyik más régióban található.

Ha konfigurálta a [biztonsági mentés hosszú távú megőrzés](sql-database-long-term-retention.md), egy új adatbázist is létrehozhat bármely LTR backup bármely SQL Database-kiszolgálón.

> [!IMPORTANT]
> Létező adatbázis visszaállítás során nem írható felül.

Standard vagy prémium szintű szolgáltatási csomag használatakor a visszaállított adatbázis tekintetében az extra tárterület költsége a következő feltételek:

- Állítsa vissza a P11 – p15 szintű, S4-S12, P1 – P6, vagy ha az adatbázis maximális mérete 500 GB-nál nagyobb.
- Állítsa vissza az S4-S12, P1 – P6 szintű, ha az adatbázis maximális mérete 250 GB-nál nagyobb.

A felesleges költségek esetén icurred a visszaállított adatbázis maximális mérete nagyobb, mint a céladatbázis service szolgáltatásszintet és teljesítményszintet szintű foglalt tárhely. Az extra tárterületek árából a bennefoglalt összegen felül külön kell fizetnie. Extra tárterület díjszabásáról, tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/). Ha a tényleges használatban lévő tárterület mennyisége kisebb, mint a foglalt tárhely, elkerülheti a Ez azzal a maximális adatbázisméret a csomagban foglalt adatmennyiségen kapcsolódik további költség.

> [!NOTE]
> [Adatbázisok biztonsági mentése automatikus](sql-database-automated-backups.md) használatosak, amikor létrehoz egy [adatbázis másolása](sql-database-copy.md).

## <a name="recovery-time"></a>Helyreállítási idő

Az automatikus biztonsági adatbázismentés használatával adatbázis visszaállítása a helyreállítási idő több tényezőtől hatással van:

- Az adatbázis mérete
- Az adatbázis számítási mérete
- Érintett tranzakciós naplók száma
- A tevékenység, amelyet a helyreállítást a visszaállítási pont megismétlését mennyisége
- A hálózati sávszélességet, ha a visszaállítást egy másik régióba
- A célrégióban feldolgozott egyidejű visszaállítási kérések száma

Nagy méretű és/vagy nagyon aktív adatbázis esetén a visszaállítás több óráig is eltarthat. Ha hosszan tartó szolgáltatáskimaradás egy régióban, akkor lehet, hogy nincsenek-e más régiókban által feldolgozott geo-visszaállítás kérések nagy számú. Ha sok kérelem, a helyreállítási idő növelhető az adatbázisok az adott régióban. A legtöbb adatbázis visszaállítása kisebb, mint 12 órát.

Egyetlen előfizetéshez a párhuzamos visszaállítási kérések száma korlátozások is.  Ezek a korlátozások vonatkoznak pont a idő visszaállítások. geo-visszaállítás, visszaállítás bármely kombinációja a hosszú távú adatmegőrzés céljából készített biztonsági):

| | **Feldolgozás alatt álló egyidejű kérelmek maximális száma** | **Az elküldött egyidejű kérelmek maximális száma** |
| :--- | --: | --: |
|Önálló adatbázis (előfizetésenként)|10|60|
|Rugalmas készlet (készletenként)|4|200|
||||

Jelenleg nem áll rendelkezésre beépített módszerrel a teljes kiszolgáló helyreállítása. A [az Azure SQL Database: Kiszolgáló-helyreállítás teljes](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) parancsfájl arra példát, hogyan végezheti el ezt a feladatot.

> [!IMPORTANT]
> Szeretné használni az automatikus biztonsági másolatokból, csak az előfizetés az SQL Server közreműködői szerepköre tagjának és tekintse meg az előfizetés tulajdonosa - kell [RBAC: Beépített szerepkörök](../role-based-access-control/built-in-roles.md). A helyreállítást az Azure Portal, a PowerShell vagy a REST API használatával végezheti. A Transact-SQL nem használható.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Visszaállíthatja egy önálló, készletezett vagy példány adatbázis egy korábbi időpontra az Azure portal használatával időben [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), vagy a [REST API-val](https://docs.microsoft.com/rest/api/sql/databases). A kérelem megadhat bármilyen szolgáltatási rétegben, illetve a visszaállított adatbázis mérete számítási. Győződjön meg arról, hogy a szükséges erőforrásokat a kiszolgálón, amelyhez állítja vissza az adatbázist. Ha elkészült, egy új adatbázist létrejön az eredeti adatbázissal ugyanazon a kiszolgálón. A visszaállított adatbázis normál díját a szolgáltatási szint és a számítási méret alapján számlázzuk. Nem terheli az adatbázis-visszaállítás befejezéséig.

Általában állítsa vissza egy adatbázis egy korábbi időpontra, helyreállítási célból. Kezelje a visszaállított adatbázis az eredeti adatbázis helyett, vagy használja a forrásadatokat, az eredeti adatbázis frissítéséhez.

- **Adatbázis cseréje**

  Célja, hogy a visszaállított adatbázis az eredeti adatbázis helyett, ha meg kell adni a orinal adatbázisméret számítási és a szolgáltatási szint. Ezután nevezze át az eredeti adatbázist, és adjon a visszaállított adatbázis az eredeti nevét használja a [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) a T-SQL paranccsal.

- **Adat-helyreállítás**

  Ha azt tervezi, adatokat lekérni a visszaállított adatbázis helyreállítása egy felhasználónak vagy alkalmazásnak hiba, meg kell írási és végrehajtási adatok helyreállítási parancsfájlt, amely kinyeri az adatokat a visszaállított adatbázisból, és alkalmazza az eredeti adatbázist. Bár a visszaállítási művelet elvégzéséhez hosszú időt is igénybe vehet, az adatbázis visszaállítása közben a visszaállítási folyamat során az adatbázisok listája látható. Ha törli az adatbázist a visszaállítás során, a visszaállítási művelethez meg lesz szakítva, és nem kell fizetni az adatbázist, a visszaállítás nem fejeződött be.

Egyetlen, helyreállítása készletezett, vagy egy adatbázis-példány az Azure portal használatával, nyissa meg az adatbázishoz tartozó lap, és kattintson a **visszaállítása** az eszköztáron.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Programozott módon egy adatbázis visszaállítása biztonsági másolatból, lásd: [programozott módon teljesítő recovery segítségével automatizált biztonsági mentések](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Törölt adatbázis visszaállítása

Akkor is törölt adatbázis visszaállítása a fióktörlési idő és a egy korábbi időpontra ugyanazon a kiszolgálón az SQL Database az Azure portal használatával időben [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), vagy a [REST (createMode visszaállítási =)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Is [törölt adatbázis visszaállítása a PowerShell használatával felügyelt példány](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Egy PowerShell-példaszkript megjelenítő törölt adatbázis visszaállítása, lásd: [PowerShell-lel SQL-adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Ha törli egy Azure SQL Database server-példány, az összes adatbázis is törlődik, és nem állítható helyre. Jelenleg nem törölt kiszolgáló visszaállítása nem támogatott.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Törölt adatbázis visszaállítása az Azure portal használatával

Helyreállítani egy törölt adatbázist az Azure portal használatával, nyissa meg az oldal, a kiszolgáló és a műveletek területen, kattintson a **törölt adatbázisok**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Programozott módon a törölt adatbázis visszaállítása, lásd: [programozott módon teljesítő recovery segítségével automatizált biztonsági mentések](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Georedundáns helyreállítás

A biztonsági mentésekből legutóbbi georeplikált állíthatja vissza egy SQL-adatbázis egyetlen kiszolgálón bármely Azure-régióban. A GEO-visszaállítás georeplikált biztonsági használja forrásként. Akkor is, ha az adatbázis vagy az Adatközpont-leállás miatt elérhetetlenné igényelhet.

GEO-visszaállítás az alapértelmezett helyreállítási beállítást esetén az üzemeltetési régióban incidens miatt nem érhető el az adatbázist. Az adatbázist visszaállíthatja egy kiszolgálóhoz az összes. Ha egy biztonsági mentés készül, és egy Azure georeplikált késleltetés van egy másik régióban található blob. Ennek eredményeképpen a visszaállított adatbázis lehet akár egy óráig a orignal adatbázis mögött. Az alábbi ábrán látható az adatbázis helyreállítása egy másik régióban elérhető másolatból.

![A GEO-visszaállítás](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Egy PowerShell-példaszkript georedundáns visszaállítás végrehajtása megjelenítése, lásd: [PowerShell-lel SQL-adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md).

Időponthoz visszaállítást a geo-secondary jelenleg nem támogatott. Csak az elsődleges adatbázisok időponthoz visszaállítási teheti meg. A kimaradás utáni helyreállítása geo-visszaállítás használatával kapcsolatos részletes információkért lásd: [helyreállítás szolgáltatáskimaradás](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> A GEO-visszaállítás a legalapvetőbb vész-helyreállítási megoldás elérhető SQL Database-ben. Automatikusan létrehozza az RPO-val georeplikált biztonsági mentések vannak támaszkodik = 1 óra és a becsült helyreállítási idő legfeljebb 12 óra. Nem tudjuk garantálni, hogy a célrégióban kell az adatbázis(ok) visszaállítása után egy regionális ourage, mivel egy éles növelésének igény szerint nagy valószínűséggel lesznek a kapacitást. A geo-visszaállítás nem üzleti kritikus fontosságú alkalmazások, amelyek viszonylag kis méretű adatbázisokhoz, egy megfelelő vész-helyreállítási megoldást. A feltüntetett adatokért kritikus fontosságú alkalmazásai, amelyek nagy méretű adatbázisokhoz és üzletmenet-folytonossági biztosítania kell, használjon [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md). Egy sokkal rövidebb RPO és RTO kínál, és minden esetben garantáltan a kapacitást. Az üzletmenet-folytonossági lehetőségeket további információkért lásd: [üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>A GEO-visszaállítás az Azure portal használatával

A geo-visszaállítás során az adatbázis a [DTU-alapú modell megőrzési időszak](sql-database-service-tiers-dtu.md) vagy [Virtuálismag-alapú modell megőrzési időszak](sql-database-service-tiers-vcore.md) az Azure portal használatával, nyissa meg az SQL Database-adatbázisok oldalt, és kattintson **hozzáadása** . Az a **forrás kiválasztása** szövegbeviteli mezőben válasszon **Backup**. Adja meg a biztonsági mentés, amelyből a helyreállításhoz a régióban, és a választott kiszolgálón.

> [!Note]
> A GEO-visszaállítás az Azure portal használatával a felügyelt példány nem érhető el. Használja helyette a PowerShell.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programozott módon a helyreállítás biztonsági másolatokkal automatikus végrehajtása

Korábban már említettük, az Azure Portalon kívül, mint az adatbázis helyreállításának programozott módon az Azure PowerShell-lel vagy a REST API használatával elvégezhető. Az alábbi táblázatok ismertetik az elérhető parancsok.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

- Önálló vagy készletezett adatbázis visszaállítása, lásd: [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Lekér egy törölt adatbázist, amelyet visszaállíthat. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Lekéri egy adatbázis georedundáns biztonsági másolatát. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Visszaállít egy SQL-adatbázist. |

  > [!TIP]
  > Egy PowerShell-példaszkript egy adatbázis-időponthoz visszaállításhoz bemutató, lásd: [PowerShell-lel SQL-adatbázis visszaállítása](scripts/sql-database-restore-database-powershell.md).

- Felügyelt példány adatbázisok visszaállítását, lásd: [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Parancsmag | Leírás |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Lekérdezi egy vagy több felügyelt példányt. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Lekérdezi egy példány adatbázisok. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Visszaállít egy példány-adatbázist. |

### <a name="rest-api"></a>REST API

A REST API használatával egyetlen vagy készletezett adatbázisok visszaállítását:

| API | Leírás |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Visszaállít egy adatbázist |
| [Get létrehozása, vagy az adatbázis állapotának frissítése](https://docs.microsoft.com/rest/api/sql/operations) |A visszaállítási művelet során állapotának visszaadása |

### <a name="azure-cli"></a>Azure CLI

- Azure CLI-vel önálló vagy készletezett adatbázisok visszaállítását, lásd: [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).
- Felügyelt példányok Azure CLI-vel való visszaállításához, lásd: [az sql KÖZÉP2 visszaállítása](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Összegzés

Automatikus biztonsági mentést a felhasználói és az alkalmazáshibák, véletlen törlése és a hosszabb kimaradások az adatbázisok védelmét. A beépített kapacitásprofilokban szolgáltatási szintek és a számítási méret érhető el.

## <a name="next-steps"></a>További lépések

- Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
- További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
- Hosszú távú megőrzés kapcsolatos további információkért lásd: [hosszú távú megőrzés](sql-database-long-term-retention.md).
- Gyorsabb helyreállítási beállítások kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-active-geo-replication.md) vagy [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).
