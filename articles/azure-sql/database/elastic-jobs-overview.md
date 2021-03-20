---
title: Elastic Database feladatok (előzetes verzió)
description: Elastic Database feladatok (előzetes verzió) konfigurálása a Transact-SQL (T-SQL) parancsfájlok futtatásához egy vagy több adatbázison keresztül Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: f9a026ed47d662b80ef01e505bfbcf8f32d20b04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792174"
---
# <a name="create-configure-and-manage-elastic-jobs-preview"></a>Rugalmas feladatok létrehozása, konfigurálása és kezelése (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre, konfigurálhat és kezelhet rugalmas feladatokat.

Ha nem használt rugalmas feladatokat, [Ismerkedjen meg a Azure SQL Database feladat-automatizálási fogalmakkal](job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Az ügynök létrehozása és konfigurálása

1. Hozzon létre vagy azonosítson egy üres S0 vagy egy magasabb adatbázist. Ez az adatbázis lesz felhasználva a *feladatok adatbázisa* számára a rugalmas feladatok ügynökének létrehozása során.
2. Hozzon létre egy rugalmas feladatot a [portálon](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) vagy a [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent)-lel.

   ![Rugalmas feladatok ügynökének létrehozása](./media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Feladatok létrehozása, futtatása és kezelése

1. Hozzon létre egy hitelesítő adatot a feladatok végrehajtásához a *feladatok adatbázisban* a [PowerShell](elastic-jobs-powershell-create.md) vagy a [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-credential-for-job-execution)használatával.
2. Adja meg a célcsoportot (azokat az adatbázisokat, amelyeken a feladatot futtatni szeretné) a [PowerShell](elastic-jobs-powershell-create.md) vagy a [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-target-group-servers)használatával.
3. Hozzon létre feladatügynök-hitelesítő adatokat minden adatbázisban, ahol a feladat futni fog [(adja hozzá a felhasználót (vagy a szerepkört) a csoport minden adatbázisához)](logins-create-manage.md). Példaként tekintse meg a [PowerShell-oktatóanyagot](elastic-jobs-powershell-create.md).
4. Hozzon létre egy feladatot a [PowerShell](elastic-jobs-powershell-create.md) vagy a [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases)használatával.
5. Adjon hozzá feladatlépéseket a [PowerShell](elastic-jobs-powershell-create.md) vagy a [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases) használatával.
6. Futtasson egy feladatot a [PowerShell](elastic-jobs-powershell-create.md#run-the-job) vagy a [T-SQL](elastic-jobs-tsql-create-manage.md#begin-unplanned-execution-of-a-job)használatával.
7. A feladatok végrehajtási állapotának figyelése a portál, a [PowerShell](elastic-jobs-powershell-create.md#monitor-status-of-job-executions) vagy a [T-SQL](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)használatával.

   ![Portál](./media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>A feladatok futtatásához használt hitelesítő adatok

A feladatok [adatbázishoz kötődő hitelesítő adatokat](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) használnak a célcsoport által végrehajtáskor meghatározott adatbázisokhoz történő csatlakozáshoz. Ha egy célcsoport kiszolgálókat vagy készleteket tartalmaz, az adatbázishoz kötődő hitelesítő adatokat a rendszer a master adatbázishoz történő csatlakozáshoz használja az elérhető adatbázisok számbavétele céljából.

Lehetséges, hogy a feladatfuttatáshoz szükséges megfelelő hitelesítő adatok beállítása nem egyértelmű, ezért tartsa szem előtt a következőket:

- Az adatbázis-hatókörrel rendelkező hitelesítő adatokat létre kell hozni a *feladatok adatbázisában*.
- Az **összes célként megadott adatbázisnak [megfelelő engedélyekkel](/sql/relational-databases/security/permissions-database-engine) kell rendelkeznie ahhoz, hogy a feladatok sikeresen befejeződik** ( `jobuser` az alábbi ábrán).
- A hitelesítő adatok újra felhasználhatók a feladatok között, a hitelesítő adatok jelszava pedig titkosítva van, és a feladat objektumaihoz csak olvasási hozzáféréssel rendelkező felhasználók férhetnek hozzá.

Az alábbi kép segítséget nyújt a megfelelő hitelesítő adatok megértéséhez és beállításához. **Ne feledje létrehozni a felhasználót minden olyan adatbázisban (minden *célként megjelölt felhasználói adatbázisban*), ahol a feladatot futtatni szeretné**.

![Rugalmas feladatokhoz tartozó hitelesítő adatok](./media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

Néhány megfontolandó ajánlott eljárás a rugalmas feladatokkal végzett munkához:

- Az API-k felhasználását korlátozza megbízható személyekre.
- A hitelesítő adatok a feladatlépés végrehajtásához szükséges minimális engedélyekkel rendelkezzenek. További információ: [Engedélyezés és engedélyek](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- A kiszolgáló és/vagy a készlet célcsoportjának használatakor a rendszer erősen javasolja, hogy hozzon létre egy külön hitelesítő adatokat, amelyekkel a főadatbázison megtekintheti vagy listázhatja azokat az adatbázisokat, amelyekkel a feladatok végrehajtása előtt kibonthatja a kiszolgáló (k) és/vagy a készlet (ek) adatbázis-listáját.

## <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

Jelenleg az előzetes verzió 100 feladat egyidejű futtatására képes.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

Állítsa be a feladat által futtatott egyidejű adatbázisok számát úgy, hogy a `sp_add_jobstep` tárolt eljárás `@max_parallelism` paraméterét a T-SQL-ben vagy `Add-AzSqlElasticJobStep -MaxParallelism` a PowerShellben állítja be.

## <a name="best-practices-for-creating-jobs"></a>Ajánlott eljárások feladatok létrehozásához

### <a name="idempotent-scripts"></a>Idempotens szkriptek
A feladatok T-SQL-szkriptjeinek [idempotensnek](https://en.wikipedia.org/wiki/Idempotence) kell lenniük. Az **idempotens** azt jelenti, hogy a sikeres szkript újrafuttatása esetén ugyanaz az eredmény várható. A szkript végrehajtása átmeneti hálózati problémák következtében meghiúsulhat. Ebben az esetben a feladat automatikusan újrapróbálkozik, és a megadott számú alkalommal ismételten futtatja a szkriptet, mielőtt feladná azt. Az idempotens szkriptek akkor is azonos eredménnyel járnak, ha már két (vagy több) alkalommal sikeresen futottak.

A legegyszerűbb módszer, ha ellenőrzi az objektum meglétét, mielőtt létrehozná.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Ehhez hasonlóan a szkriptnek sikeresen végrehajthatónak kell lennie az észlelt feltételek logikai tesztelése és azokkal ellentétes műveletek révén.



## <a name="next-steps"></a>Következő lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell-lel](elastic-jobs-powershell-create.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql-create-manage.md)