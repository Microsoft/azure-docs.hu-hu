---
title: A hosszan futó tárolt eljárások kezelése az SQL-összekötőben
description: A Azure Logic Apps-beli SQL-összekötő használatakor időtúllépéssel rendelkező tárolt eljárások kezelése
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102967"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Tárolt eljárások időtúllépésének kezelése az SQL-összekötőben Azure Logic Apps

Ha a logikai alkalmazás az eredményhalmaz esetében olyan nagy méretű, hogy az [SQL-összekötő](../connectors/connectors-create-api-sqlazure.md) nem adja vissza az összes eredményt egyszerre, vagy ha az eredményhalmaz méretének és szerkezetének nagyobb mértékű vezérlését szeretné használni, létrehozhat egy [tárolt eljárást](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , amely a kívánt módon rendezi az eredményeket. Az SQL-összekötő számos háttérbeli funkciót biztosít, amelyeket [Azure Logic apps](../logic-apps/logic-apps-overview.md) használatával érhet el, így könnyebben automatizálhatja az SQL Database-táblákkal működő üzleti feladatokat.

Ha például több sor beolvasása vagy beillesztése történik, a logikai alkalmazás ezen sorokon keresztül ismételheti meg a sorokat, amíg az adott [korláton](../logic-apps/logic-apps-limits-and-config.md)belül [hurkot **nem**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) tud használni. Ha azonban a logikai alkalmazásnak több ezer vagy több millió sorból kell dolgoznia, csökkentenie kell az adatbázishoz intézett hívásokból eredő költségeket. További információ: [a tömeges adatok kezelése az SQL-összekötő használatával](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>A tárolt eljárás végrehajtásának időtúllépési korlátja

Az SQL-összekötő tárolt eljárásának időtúllépési korlátja [kevesebb, mint 2 perc](/connectors/sql/#known-issues-and-limitations). Előfordulhat, hogy egyes tárolt eljárások a korlátnál hosszabb ideig tartanak, ami `504 Timeout` hibát okoz. Előfordulhat, hogy ezek a hosszan futó folyamatok explicit módon vannak kódolva tárolt eljárásként. Az időtúllépési korlát miatt ezek az eljárások Azure Logic Apps okozhatnak problémákat. Bár az SQL-összekötő nem támogatja natív módon az aszinkron üzemmódot, megkerülheti ezt a problémát, és szimulálhatja ezt a módot egy SQL-befejezési trigger, natív SQL-áteresztő lekérdezés, egy állapotadatok és egy kiszolgálóoldali feladat használatával. Ehhez a feladathoz használhatja a [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) [Azure rugalmas feladat ügynökét](../azure-sql/database/elastic-jobs-overview.md) . A [helyszíni](/sql/sql-server/sql-server-technical-documentation) és az [Azure SQL felügyelt példányának](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)SQL Server használhatja a [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

Tegyük fel például, hogy a következő hosszan futó tárolt eljárással rendelkezik, amely hosszabb időt vesz igénybe, mint a Futtatás befejezésének határideje. Ha ezt a tárolt eljárást egy logikai alkalmazásból futtatja az SQL-összekötő használatával, hibaüzenet jelenik meg `HTTP 504 Gateway Timeout` .

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Ahelyett, hogy közvetlenül meghívja a tárolt eljárást, aszinkron módon futtathatja az eljárást a háttérben a feladat- *ügynök* használatával. A bemeneti és kimeneti adatokat egy olyan állapot táblában tárolhatja, amelyet aztán a logikai alkalmazáson keresztül kezelhet. Ha nincs szüksége a bemenetekre és a kimenetekre, vagy ha már megírta az eredményeket a tárolt eljárás egyik táblájába, egyszerűbbé teheti ezt a megközelítést.

> [!IMPORTANT]
> Győződjön meg arról, hogy a tárolt eljárás és az összes feladat *idempotens*, ami azt jelenti, hogy az eredmények befolyásolása nélkül többször is futhatnak. Ha az aszinkron feldolgozás sikertelen vagy időtúllépés miatt leáll, a feladat ügynöke újra próbálkozhat a lépéssel, így a tárolt eljárás többször is elvégezhető. Ha el szeretné kerülni a kimenet ismétlődését, mielőtt objektumokat hozna létre, tekintse át az [ajánlott eljárásokat és megközelítéseket](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts).

A következő szakasz azt ismerteti, hogyan használható az Azure rugalmas feladatok ügynöke a Azure SQL Databasehoz. SQL Server és az Azure SQL felügyelt példányai esetében használhatja a SQL Server Agent. Néhány felügyeleti részlet eltérő lesz, de az alapvető lépések változatlanok maradnak, mint a Azure SQL Database-feladatok ügynökének beállítása.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Azure SQL Databasei feladatkártya

A [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)tárolt eljárás futtatására képes feladat létrehozásához használja az [Azure rugalmas feladat ügynökét](../azure-sql/database/elastic-jobs-overview.md). Hozzon létre egy feladatot a Azure Portalban. Ez a módszer több tárolt eljárást is felvesz az ügynök által használt adatbázishoz, más néven az ügynök- *adatbázishoz*. Ezután létrehozhat egy olyan feladatot, amely futtatja tárolt eljárását a célként megadott adatbázisban, és a kimenetet a befejezés után rögzíti.

A feladatok létrehozása előtt be kell állítania az engedélyeket, a csoportokat és a célokat az [Azure rugalmas feladatok ügynökének teljes dokumentációjában](../azure-sql/database/elastic-jobs-overview.md)leírtak szerint. A következő szakaszokban leírtak szerint létre kell hoznia egy támogató táblát is a céladatbázisben.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Állapot tábla létrehozása paraméterek regisztrálásához és bemenetek tárolásához

Az SQL-ügynök feladatai nem fogadnak el bemeneti paramétereket. Ehelyett a céladatbázis hozzon létre egy olyan állapotjelző táblát, amelyben regisztrálja a paramétereket, és tárolja a tárolt eljárások meghívásához használandó bemeneteket. Az összes ügynöki feladatot a céladatbázis hajtja végre, de a feladatok tárolt eljárásai az ügynök adatbázisán futnak. 

Az állapot tábla létrehozásához használja a következő sémát:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Az eredményül kapott táblázat a [SQL Server Management Studio (SMS)](/sql/ssms/download-sql-server-management-studio-ssms)-ben való megjelenését mutatja be:

![Képernyőkép, amely a tárolt eljárás bemeneteit tároló létrehozott állapot táblát mutatja.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

A megfelelő teljesítmény biztosításához, és győződjön meg arról, hogy az ügynök feladata megtalálja a társított rekordot, a tábla a feladatok végrehajtási AZONOSÍTÓját ( `jobid` ) használja elsődleges kulcsként. Ha szeretné, a bemeneti paraméterekhez külön oszlopokat is hozzáadhat. A korábban leírt séma általánosabban több paramétert képes kezelni, de a által kiszámított méretre van korlátozva `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Legfelső szintű feladat létrehozása a tárolt eljárás futtatásához

A hosszú ideig futó tárolt eljárás végrehajtásához hozza létre ezt a legfelső szintű feladat ügynököt az ügynök adatbázisában:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Most adja hozzá a lépéseket a parametrizálja, a futtatott és a tárolt eljárás befejezéséhez szükséges feladatokhoz. Alapértelmezés szerint a feladatok lépésének időkorlátja 12 óra. Ha a tárolt eljárásnak több időre van szüksége, vagy ha azt szeretné, hogy az eljárás korábban időtúllépést igényel, akkor a `step_timeout_seconds` paramétert a másodpercben megadott másik értékre módosíthatja. Alapértelmezés szerint a lépés 10 beépített újrapróbálkozást tartalmaz az egyes újrapróbálkozások közötti leállítási időtúllépéssel, amelyet használhatja előnyére.

A következő lépéseket kell felvennie:

1. Várjon, amíg a paraméterek megjelennek a `LongRunningState` táblázatban.

   Ez az első lépés megvárja, amíg a paraméterek bekerülnek a `LongRunningState` táblázatba, ami a feladatok elindulása után hamarosan megtörténik. Ha a feladat-végrehajtási azonosító ( `jobid` ) nem jelenik meg a `LongRunningState` táblához, a lépés egyszerűen meghiúsul, és az alapértelmezett újrapróbálkozási vagy leállítási időtúllépés a várakozást adja:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Kérdezze le a paramétereket az állapot táblából, és adja át őket a tárolt eljárásnak. Ez a lépés a háttérben is futtatja az eljárást. 

   Ha a tárolt eljárás nem igényel paramétereket, közvetlenül hívja meg a tárolt eljárást. Ellenkező esetben a paraméter átadásához `@timespan` használja a (z `@callparams` ), amelyet további paraméterek továbbítására is kiterjeszt.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Fejezze be a feladatot, és jegyezze fel az eredményeket.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Indítsa el a feladatot, és adja át a paramétereket.

A feladatok elindításához használjon egy áteresztő natív lekérdezést az [ **SQL-lekérdezés végrehajtása** művelettel](/connectors/sql/#execute-a-sql-query-(v2)) , és azonnal küldje el a feladatok paramétereit az állapot táblába. Ha meg szeretné adni az `jobid` attribútumot a cél táblában, Logic apps hozzáadja a **minden** hurokhoz, amely az előző művelet tábla kimenetén halad át. Minden feladat-végrehajtási azonosító esetében futtasson egy **sor beszúrása** műveletet, amely a dinamikus adatkimenetet használja, `ResultSets JobExecutionId` hogy hozzáadja a feladat paramétereit a kicsomagoláshoz, és továbbítsa azokat a célként tárolt eljáráshoz.

![Képernyőkép, amely a feladat elindításához és paraméterek átadásához használt műveleteket mutatja a tárolt eljárásban.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Amikor a feladatok befejeződik, a feladatok frissítik a `LongRunningState` táblázatot, így könnyen aktiválhatja az eredményt az [ **elem módosításakor**](/connectors/sql/#when-an-item-is-modified-(v2)). Ha nincs szüksége a kimenetre, vagy ha már rendelkezik egy kimeneti táblát figyelő triggerrel, akkor kihagyhatja ezt a részt.

![Képernyőkép, amely az SQL-triggert jeleníti meg az elemek módosításakor.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server vagy Azure SQL felügyelt példányhoz tartozó feladatkártya

Ugyanezen forgatókönyv esetében használhatja a [SQL Server Agent](/sql/ssms/agent/sql-server-agent) [SQL Server a helyszíni](/sql/sql-server/sql-server-technical-documentation) és az [Azure SQL felügyelt példányán](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Bár egyes kezelési adatok eltérnek, az alapvető lépések változatlanok maradnak, mint a Azure SQL Database-feladatok ügynökének beállítása.

## <a name="next-steps"></a>Következő lépések

[Kapcsolódás SQL Serverhoz, Azure SQL Databasehoz vagy az Azure SQL felügyelt példányához](../connectors/connectors-create-api-sqlazure.md)

