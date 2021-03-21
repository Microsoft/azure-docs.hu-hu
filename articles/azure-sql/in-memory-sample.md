---
title: In-Memory minta
description: Próbálja ki Azure SQL Database In-Memory technológiákat a OLTP és a oszlopcentrikus mintával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b5a1035f8a213a6ce02dd3252ff7d3ddea46faf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92786581"
---
# <a name="in-memory-sample"></a>In-Memory minta
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database In-Memory technológiái lehetővé teszik az alkalmazás teljesítményének növelését, és az adatbázis költségeit is csökkentheti. A Azure SQL Database In-Memory technológiáinak használatával a teljesítménnyel kapcsolatos fejlesztéseket különböző számítási feladatokkal érheti el.

Ebben a cikkben két mintát láthat, amelyek bemutatják In-Memory OLTP használatát, valamint a Azure SQL Database oszlopcentrikus indexeit.

További információkért lásd:

- [Memóriában tárolt OLTP – áttekintés és használati forgatókönyvek](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (az első lépésekhez kapcsolódó esettanulmányokra és információkra mutató hivatkozásokat tartalmaz)
- [In-Memory OLTP dokumentációja](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Útmutató a oszlopcentrikus indexekhez](/sql/relational-databases/indexes/columnstore-indexes-overview)
- Hibrid tranzakciós/analitikus feldolgozás (HTAP), más néven [valós idejű operatív elemzés](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Telepítse a In-Memory OLTP mintát

A AdventureWorksLT minta adatbázisát a [Azure Portal](https://portal.azure.com/)néhány kattintással létrehozhatja. Az ebben a szakaszban ismertetett lépések bemutatják, hogyan bővítheti AdventureWorksLT-adatbázisát In-Memory OLTP-objektumokkal, és hogyan láthatja el a teljesítmény előnyeit.

A In-Memory OLTP egyszerűbb, de vizuálisan vonzó teljesítmény-bemutatója a következő helyen található:

- Kiadás: [memóriabeli OLTP-bemutató-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Forráskód: [a memóriában-OLTP-demo-Source-Code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

### <a name="installation-steps"></a>A telepítés lépései

1. A [Azure Portalban](https://portal.azure.com/)hozzon létre egy prémium vagy üzletileg kritikus adatbázist egy kiszolgálón. Állítsa a **forrást** a AdventureWorksLT mintaadatbázis értékre. Részletes útmutatás: [az első adatbázis létrehozása Azure SQL Databaseban](database/single-database-create-quickstart.md).

2. Kapcsolódjon az adatbázishoz SQL Server Management Studio [(SSMS.exe)](/sql/ssms/download-sql-server-management-studio-ssms)használatával.

3. Másolja a [memóriában tárolt OLTP Transact-SQL-szkriptet](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) a vágólapra. A T-SQL parancsfájl létrehozza a szükséges In-Memory objektumokat az 1. lépésben létrehozott AdventureWorksLT-mintaadatbázisban.

4. Illessze be a T-SQL-szkriptet a SSMS-be, majd hajtsa végre a parancsfájlt. A `MEMORY_OPTIMIZED = ON` záradék Create TABLEi utasítások elengedhetetlenek. Például:

```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```

### <a name="error-40536"></a>40536-es hiba

Ha a T-SQL-szkript futtatásakor a 40536-es hiba jelenik meg, futtassa a következő T-SQL-szkriptet annak ellenőrzéséhez, hogy az adatbázis támogatja-e a memóriában:

```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```

A **0** eredmény azt jelenti, hogy a In-Memory nem támogatott, és **1** azt jelenti, hogy támogatott. A probléma diagnosztizálásához győződjön meg arról, hogy az adatbázis a prémium szintű szolgáltatási szinten van.

### <a name="about-the-created-memory-optimized-items"></a>A létrehozott memóriára optimalizált elemek ismertetése

**Táblák**: a minta a következő memória-optimalizált táblákat tartalmazza:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Bemutató. DemoSalesOrderHeaderSeed
- Bemutató. DemoSalesOrderDetailSeed

A memóriára optimalizált táblákat a SSMS **Object Explorer** keresztül ellenőrizheti. Kattintson a jobb gombbal a táblák  >  **szűrő**  >  **beállításainak**  >  szűrése elemre. Az érték 1.

Vagy lekérdezheti a katalógus nézeteit, például a következőket:

```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```

**Natív módon lefordított tárolt eljárás**: megvizsgálhatja SalesLT.usp_InsertSalesOrder_inmem a katalógus nézet lekérdezésével:

```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```

&nbsp;

### <a name="run-the-sample-oltp-workload"></a>A minta OLTP számítási feladat futtatása

A következő két *tárolt eljárás* között az egyetlen különbség az, hogy az első eljárás a táblák memóriára optimalizált verzióit használja, míg a második eljárás a normál lemezes táblákat használja:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**

Ebből a szakaszból megtudhatja, hogyan használhatja a Handy **ostress.exe** segédprogramot a két tárolt eljárás a stresszes szinteken való végrehajtásához. Összehasonlíthatja, hogy mennyi ideig tart a két stressz futtatása.

ostress.exe futtatásakor azt javasoljuk, hogy a következőhöz tartozó paraméterek értékét adja át:

- Nagy számú egyidejű kapcsolat futtatása a-N100 használatával.
- A-R500 használatával a kapcsolatok mindegyike több százszor.

Azonban érdemes lehet sokkal kisebb értékekkel kezdeni, mint például a-N10 és a-R50, hogy minden megfelelően működjön.

### <a name="script-for-ostressexe"></a>Parancsfájl a ostress.exehoz

Ez a szakasz a ostress.exe parancssorba ágyazott T-SQL-parancsfájlt jeleníti meg. A parancsfájl a korábban telepített T-SQL-parancsfájl által létrehozott elemeket használja.

A következő szkript egy példaként szolgáló értékesítési rendelést szúr be öt sorral a következő memóriára optimalizált *táblákba*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem

```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```

Ha az előző T-SQL-parancsfájl *_ondisk* verzióját szeretné ostress.exe, akkor a *_inmem* alsztring mindkét előfordulását a *_ondisk* értékre kell cserélni. Ezek a cserék a táblák és a tárolt eljárások nevét érintik.

#### <a name="install-rml-utilities-and-ostress"></a>RML segédprogramok telepítése és `ostress`

Ideális esetben a ostress.exe Azure-beli virtuális gépen (VM) való futtatását tervezi. Hozzon létre egy [Azure-beli virtuális gépet](https://azure.microsoft.com/documentation/services/virtual-machines/) ugyanabban az Azure földrajzi régióban, ahol a AdventureWorksLT-adatbázis található. Ehelyett a ostress.exe futtatható a laptopon.

A virtuális gépen vagy a választott gazdagépen telepítse a Replay Markup Language (RML) segédprogramokat. A segédprogramok közé tartoznak a ostress.exe.

További információkért lásd:

- A [In-Memory OLTP tartozó mintaadatbázis](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp)ostress.exe-vitafóruma.
- [Mintaadatbázis In-Memory OLTP](/sql/relational-databases/in-memory-oltp/sample-database-for-in-memory-oltp).
- A [ostress.exetelepítéséhez szükséges blog ](https://techcommunity.microsoft.com/t5/sql-server-support/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql/ba-p/317910).

<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->

#### <a name="run-the-_inmem-stress-workload-first"></a>Először futtassa az *_inmem* Stress munkaterhelés-t

A ostress.exe parancssorának futtatásához használhatja a *RML* parancssori ablakát. A közvetlen parancssori paraméterek `ostress` :

- Az 100-es kapcsolatok párhuzamos futtatása (-N100).
- Minden egyes kapcsolatban futtassa a T-SQL-szkriptet 50-szor (-R50).

```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```

Az előző ostress.exe parancssorának futtatása:

1. Az adatbázis-adattartalom alaphelyzetbe állításához futtassa a következő parancsot a SSMS, és törölje az összes korábbi Futtatás által beszúrt adatokat:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Másolja az előző ostress.exe parancssor szövegét a vágólapra.

3. A `<placeholders>` megfelelő valós értékekkel cserélje le a paramétert a-S-U-P-d paraméterekre.

4. Futtassa a szerkesztett parancssort egy RML cmd-ablakban.

#### <a name="result-is-a-duration"></a>Az eredmény egy időtartam

Ha `ostress.exe` befejeződik, a futtatási időtartamot a RML cmd ablakban a kimenet utolsó soraként írja be. Például egy rövidebb teszt futtatása körülbelül 1,5 percig tartott:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`

#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Alaphelyzetbe állítás, szerkesztés *_ondisk*, majd újrafuttatás

Miután elvégezte a *_inmem* futtatásának eredményét, hajtsa végre a következő lépéseket a *_ondisk* futtatásához:

1. Állítsa alaphelyzetbe az adatbázist úgy, hogy az alábbi parancsot futtatja a SSMS az előző Futtatás által beszúrt összes érték törléséhez:

   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Szerkessze az ostress.exe parancssort az összes *_inmem* lecserélése a *_ondiskre*.

3. Futtassa újra ostress.exe a második alkalommal, és rögzítse az időtartam eredményét.

4. Ismét állítsa alaphelyzetbe az adatbázist (a felelősségteljes törléshez, ami nagy mennyiségű tesztelési adattal rendelkezhet).

#### <a name="expected-comparison-results"></a>Várt összehasonlító eredmények

A In-Memory tesztek azt mutatták, hogy a teljesítmény **kilenc alkalommal** javult ebben az egyszerű számítási feladatban, és `ostress` egy Azure-beli virtuális gépen fut, amely ugyanabban az Azure-régióban található, mint az adatbázis.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. a In-Memory Analytics-minta telepítése

Ebben a szakaszban összehasonlítja az i/o-és statisztikai eredményeket, ha oszlopcentrikus indexet használ egy hagyományos b-Tree indexhez képest.

A OLTP számítási feladatokhoz a valós idejű elemzések esetében általában nem fürtözött oszlopcentrikus index használata ajánlott. Részletekért lásd: a [Oszlopcentrikus indexek](/sql/relational-databases/indexes/columnstore-indexes-overview)ismertetése.

### <a name="prepare-the-columnstore-analytics-test"></a>A oszlopcentrikus Analytics-teszt előkészítése

1. A mintából hozzon létre egy friss AdventureWorksLT-adatbázist a Azure Portal használatával.
   - Használja ezt a pontos nevet.
   - Válassza ki a prémium szintű szolgáltatási szintet.

2. Másolja a [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) a vágólapra.
   - A T-SQL parancsfájl létrehozza a szükséges In-Memory objektumokat az 1. lépésben létrehozott AdventureWorksLT-mintaadatbázisban.
   - A szkript létrehozza a dimenzió táblát és két egyedkapcsolat táblát. Az egyedkapcsolat-táblák 3 500 000-sorokkal vannak feltöltve.
   - A szkript végrehajtása 15 percet is igénybe vehet.

3. Illessze be a T-SQL-szkriptet a SSMS-be, majd hajtsa végre a parancsfájlt. A **create index** utasításban a **oszlopcentrikus** kulcsszó elengedhetetlen, a következő módon:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. A AdventureWorksLT beállítása a 130 kompatibilitási szintre:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Az 130-as szint nem kapcsolódik közvetlenül a In-Memory funkciókhoz. Az 130-as szint azonban általában gyorsabb lekérdezési teljesítményt nyújt, mint a 120.

#### <a name="key-tables-and-columnstore-indexes"></a>Legfontosabb táblák és oszlopcentrikus indexek

- dbo. FactResellerSalesXL_CCI olyan tábla, amely egy fürtözött oszlopcentrikus indextel rendelkezik, *amely az adatszinten speciális* tömörítést tartalmaz.

- dbo. FactResellerSalesXL_PageCompressed egy olyan tábla, amely egyenértékű normál fürtözött indextel rendelkezik, amely csak az *oldal* szintjén tömörítve van.

#### <a name="key-queries-to-compare-the-columnstore-index"></a>A oszlopcentrikus index összehasonlítására szolgáló legfontosabb lekérdezések

[Több T-SQL-lekérdezési típus is futtatható](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) a teljesítmény növelésének megtekintéséhez. A T-SQL-parancsfájl 2. lépésében figyeljen erre a pár lekérdezésre. Csak egy sorban térnek el:

- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`

A fürtözött oszlopcentrikus index a FactResellerSalesXL \_ CCI-táblázatban található.

A következő T-SQL-szkript részletesen kinyomtatja az IO és az idő adatait az egyes táblák lekérdezéséhez.

```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

A P2 díjszabási csomaggal rendelkező adatbázisban a lekérdezés teljesítményének növelését a hagyományos indexszel összehasonlítva a fürtözött oszlopcentrikus index használatával lehet számítani. A P15 a oszlopcentrikus index használatával várhatóan körülbelül 57-szor a teljesítmény nyeresége.

## <a name="next-steps"></a>Következő lépések

- [1. gyors útmutató: In-Memory OLTP Technologies a T-SQL teljesítményének növeléséhez](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)

- [In-Memory OLTP használata meglévő Azure SQL-alkalmazásban](in-memory-oltp-configure.md)

- [In-Memory OLTP-tároló figyelése](in-memory-oltp-monitor-space.md) In-Memory OLTP

## <a name="additional-resources"></a>További források

### <a name="deeper-information"></a>Mélyebb információk

- [Ismerje meg, hogy a kvórum megduplázza a kulcsfontosságú adatbázis számítási feladatait, miközben 70%-kal csökkenti a DTU a In-Memory OLTP Azure SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Memóriában tárolt OLTP Azure SQL Database blogbejegyzésben](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Tudnivalók a In-Memory OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [Tudnivalók a oszlopcentrikus indexekről](/sql/relational-databases/indexes/columnstore-indexes-overview)

- [Tudnivalók a valós idejű operatív elemzésekről](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

- Lásd: [gyakori számítási feladatok mintái és áttelepítési megfontolások](/previous-versions/dn673538(v=msdn.10)) (amelyek a munkaterhelési mintákat ismertetik, ahol a In-Memory OLTP gyakran jelentős teljesítményt nyújt)

#### <a name="application-design"></a>Az alkalmazás kialakítása

- [Memóriában tárolt OLTP (memórián belüli optimalizálás)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)

- [In-Memory OLTP használata meglévő Azure SQL-alkalmazásban](in-memory-oltp-configure.md)

#### <a name="tools"></a>Eszközök

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)

- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)