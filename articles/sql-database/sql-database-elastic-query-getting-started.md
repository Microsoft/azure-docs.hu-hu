---
title: Kibővített Felhőbeli adatbázisok közötti jelentés (horizontális particionálás) | Microsoft Docs
description: Több adatbázison keresztüli jelentések készítése a több adatbázisra kiterjedő adatbázis-lekérdezések használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: cc59d7cb1ce09aad834130818e5af533719e04c1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568611"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>A kibővített felhőalapú adatbázisok (előzetes verzió) közötti jelentés

Egy [rugalmas lekérdezés](sql-database-elastic-query-overview.md)használatával több Azure SQL-adatbázisból is létrehozhat jelentéseket egyetlen kapcsolódási pontról. Az adatbázisoknak vízszintesen particionálva kell lenniük (más néven "szilánkokra").

Ha rendelkezik meglévő adatbázissal, tekintse meg [a meglévő adatbázisok áttelepítése a](sql-database-elastic-convert-to-use-elastic-tools.md)kibővített adatbázisokra című témakört.

A lekérdezéshez szükséges SQL-objektumok megismeréséhez tekintse meg a [horizontálisan particionált adatbázisok lekérdezését](sql-database-elastic-query-horizontal-partitioning.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

Töltse le és futtassa az [első lépéseket a Elastic Database Tools példával](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Szegmens Térkép-kezelő létrehozása a minta alkalmazás használatával
Itt létre kell hoznia egy szegmenses Térkép-kezelőt, és több szegmenst is kell létrehoznia, majd az adathalmazba helyezi az adatbeszúrást. Ha már előfordul, hogy a szegmensek a szilánkokkal lettek beállítva, akkor kihagyhatja a következő lépéseket, és átléphet a következő szakaszra.

1. Hozza létre és futtassa a **Elastic Database Tools** minta alkalmazás első lépéseit. Hajtsa végre a lépéseket a [minta alkalmazás letöltése és futtatása](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app)szakasz 7. lépésével. A 7. lépés végén a következő parancssor jelenik meg:

    ![parancssor][1]
2. A parancssorba írja be az "1" kifejezést, majd nyomja le az **ENTER**billentyűt. Ez létrehozza a szegmens Térkép-kezelőt, és két szegmenst hoz létre a kiszolgálóhoz. Ezután írja be a "3" kifejezést, és nyomja le az **ENTER**billentyűt. Ismételje meg a műveletet négyszer. Ez beszúrja a szegmensekben található mintavételi adatsorokat.
3. A [Azure Portal](https://portal.azure.com) három új adatbázist kell megjelenítenie a kiszolgálón:

   ![A Visual Studio megerősítése][2]

   Ezen a ponton a Elastic Database ügyféloldali kódtárak támogatják az adatbázisok közötti lekérdezéseket. Használja például a 4. lehetőséget a parancsablakban. A több szegmensből álló lekérdezés eredményei mindig a **Union összes** eredményét jelentik.

   A következő szakaszban egy minta adatbázis-végpontot hozunk létre, amely támogatja az adatszegmensekben található adatmennyiségek széles körű lekérdezését.

## <a name="create-an-elastic-query-database"></a>Rugalmas lekérdezési adatbázis létrehozása
1. Nyissa [](https://portal.azure.com) meg a Azure Portalt, és jelentkezzen be.
2. Hozzon létre egy új Azure SQL Database-adatbázist ugyanabban a kiszolgálón, mint a szegmens beállítása. Nevezze el a "ElasticDBQuery" adatbázist.

    ![Azure Portal és árképzési szintek][3]

    > [!NOTE]
    > meglévő adatbázist is használhat. Ha ezt megteheti, nem lehet az egyik olyan szegmens, amelynek a lekérdezéseit végre szeretné hajtani. Ezt az adatbázist fogjuk használni egy rugalmas adatbázis-lekérdezés metaadat-objektumainak létrehozásához.
    >

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása
### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis – hatókörrel rendelkező főkulcs és hitelesítő adatok
Ezek a szegmenses Térkép kezelőjéhez és a szegmensekhez való kapcsolódáshoz használatosak:

1. Nyissa meg SQL Server Management Studio vagy SQL Server Data Tools a Visual Studióban.
2. Kapcsolódjon a ElasticDBQuery adatbázishoz, és hajtsa végre a következő T-SQL-parancsokat:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    a "username" és a "password" értéknek meg kell egyeznie a 6. lépésében használt bejelentkezési adatokkal, amelyek a [rugalmas adatbázis-eszközök használatának első lépéseiben](sql-database-elastic-scale-get-started.md)a [minta alkalmazás letöltését és futtatását](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) ismertetik.

### <a name="external-data-sources"></a>Külső adatforrások
Külső adatforrás létrehozásához hajtsa végre a következő parancsot a ElasticDBQuery-adatbázisban:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 A "CustomerIDShardMap" a szegmens Térkép neve, ha a szegmensek közötti térképet és a szegmens Térkép kezelőjét a rugalmas adatbázis-eszközök használatával hozta létre. Ha azonban az egyéni beállítást használta ehhez a mintához, akkor az alkalmazásban kiválasztott szegmenses leképezési nevet kell megadni.

### <a name="external-tables"></a>Külső táblák
Hozzon létre egy külső táblát, amely megegyezik a szegmensek ügyfelek táblájával a következő parancs ElasticDBQuery-adatbázison való végrehajtásával:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Minta rugalmas adatbázis-lekérdezés végrehajtása T-SQL-lekérdezés
Miután meghatározta a külső adatforrást és a külső táblázatokat, mostantól teljes T-SQL-T használhat a külső táblákon.

A lekérdezés végrehajtása a ElasticDBQuery-adatbázisban:

    select count(CustomerId) from [dbo].[Customers]

Figyelje meg, hogy a lekérdezés összesíti az összes szegmens eredményét, és a következő kimenetet adja:

![Kimenet részletei][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Rugalmas adatbázis lekérdezési eredményeinek importálása az Excelbe
 Az eredményeket importálhatja egy lekérdezésből egy Excel-fájlba.

1. Indítsa el az Excel 2013 alkalmazást.
2. Navigáljon az **adatszalagra** .
3. Kattintson **a más forrásokból** lehetőségre, és kattintson **az SQL Server**lehetőségre.

   ![Excel-importálás más forrásokból][5]
4. Az **adatkapcsolatok varázslóban** adja meg a kiszolgáló nevét és a bejelentkezési hitelesítő adatokat. Ezután kattintson a **Next** (Tovább) gombra.
5. A párbeszédpanelen **válassza ki a kívánt adatkészletet tartalmazó adatbázist**, és válassza ki a **ElasticDBQuery** -adatbázist.
6. Válassza ki a **Customers (ügyfelek)** táblát a lista nézetben, és kattintson a **tovább**gombra. Ezután kattintson a **Befejezés**gombra.
7. Az **adatimportálás** űrlapon a **válassza ki, hogyan szeretné**megtekinteni az adatait a munkafüzetben, válassza a **tábla** lehetőséget, majd kattintson **az OK**gombra.

Az **ügyfelek** tábla összes sora, amely különböző szegmensekben van tárolva, feltölti az Excel-lapot.

Mostantól használhatja az Excel hatékony adatvizualizációs funkcióit. A kapcsolati sztringet használhatja a kiszolgáló nevével, az adatbázis nevével és a hitelesítő adatokkal a BI-és adatintegrációs eszközök a rugalmas lekérdezési adatbázishoz való csatlakoztatásához. Győződjön meg arról, hogy a SQL Server támogatott adatforrásként az eszköz számára. A rugalmas lekérdezési adatbázist és a külső táblákat ugyanúgy tekintheti meg, mint bármely más SQL Server adatbázishoz, és SQL Server táblákhoz, amelyeket az eszközhöz szeretne csatlakozni.

### <a name="cost"></a>Költség
A Elastic Database lekérdezési funkció használata nem díjköteles.

A díjszabással kapcsolatos információkért tekintse meg a [SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>További lépések

* A rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md).
* A vertikális particionálással kapcsolatos oktatóanyagért lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](sql-database-elastic-query-getting-started-vertical.md).
* A függőlegesen particionált információk szintaxisát és mintáit lásd [](sql-database-elastic-query-vertical-partitioning.md) : függőlegesen particionált adatlekérdezés
* A horizontálisan particionált információk szintaxisát és mintáit lásd [](sql-database-elastic-query-horizontal-partitioning.md) : vízszintesen particionált adatlekérdezés
* Lásd [:\_az \_SP távoli futtatása](https://msdn.microsoft.com/library/mt703714) olyan tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készleten.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
