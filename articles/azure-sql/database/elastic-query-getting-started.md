---
title: A kibővített felhőalapú adatbázisok közötti jelentés
description: Több adatbázison keresztüli jelentések készítése a több adatbázisra kiterjedő adatbázis-lekérdezések használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: 586dad7439cc57ed2c863ee5f6692e12f7a78c50
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92781226"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>A kibővített felhőalapú adatbázisok (előzetes verzió) közötti jelentés
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [rugalmas lekérdezés](elastic-query-overview.md)használatával több adatbázisból is létrehozhat jelentéseket egyetlen kapcsolódási pontról. Az adatbázisoknak vízszintesen particionálva kell lenniük (más néven "szilánkokra").

Ha rendelkezik meglévő adatbázissal, tekintse meg [a meglévő adatbázisok áttelepítése a kibővített adatbázisokra](elastic-convert-to-use-elastic-tools.md)című témakört.

A lekérdezéshez szükséges SQL-objektumok megismeréséhez tekintse meg a [horizontálisan particionált adatbázisok lekérdezését](elastic-query-horizontal-partitioning.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

Töltse le és futtassa az [első lépéseket a Elastic Database Tools példával](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Szegmens Térkép-kezelő létrehozása a minta alkalmazás használatával
Itt létre kell hoznia egy szegmenses Térkép-kezelőt, és több szegmenst is kell létrehoznia, majd az adathalmazba helyezi az adatbeszúrást. Ha már előfordul, hogy a szegmensek a szilánkokkal lettek beállítva, akkor kihagyhatja a következő lépéseket, és átléphet a következő szakaszra.

1. Hozza létre és futtassa a **Elastic Database Tools** minta alkalmazás első lépéseit. Ehhez kövesse a cikk szakaszának [letöltés és Futtatás a minta](elastic-scale-get-started.md#download-and-run-the-sample-app-1)alkalmazást. Az összes lépés befejezését követően a következő parancssor jelenik meg:

    ![parancssor][1]
2. A parancssorba írja be az "1" kifejezést, majd nyomja le az **ENTER** billentyűt. Ez létrehozza a szegmens Térkép-kezelőt, és két szegmenst hoz létre a kiszolgálóhoz. Ezután írja be a "3" kifejezést, és nyomja le az **ENTER** billentyűt. Ismételje meg a műveletet négyszer. Ez beszúrja a szegmensekben található mintavételi adatsorokat.
3. A [Azure Portal](https://portal.azure.com) három új adatbázist kell megjelenítenie a kiszolgálón:

   ![A Visual Studio megerősítése][2]

   Ezen a ponton a Elastic Database ügyféloldali kódtárak támogatják az adatbázisok közötti lekérdezéseket. Használja például a 4. lehetőséget a parancsablakban. A több szegmensből álló lekérdezés eredményei mindig a **Union összes** eredményét jelentik.

   A következő szakaszban egy minta adatbázis-végpontot hozunk létre, amely támogatja az adatszegmensekben található adatmennyiségek széles körű lekérdezését.

## <a name="create-an-elastic-query-database"></a>Rugalmas lekérdezési adatbázis létrehozása

1. Nyissa meg a [Azure Portalt](https://portal.azure.com) , és jelentkezzen be.
2. Hozzon létre egy új adatbázist a Azure SQL Databaseban ugyanabban a kiszolgálón, mint a szegmens beállítása. Nevezze el a "ElasticDBQuery" adatbázist.

    ![Azure Portal és árképzési szintek][3]

    > [!NOTE]
    > meglévő adatbázist is használhat. Ha ezt megteheti, nem lehet az egyik olyan szegmens, amelynek a lekérdezéseit végre szeretné hajtani. Ezt az adatbázist fogjuk használni egy rugalmas adatbázis-lekérdezés metaadat-objektumainak létrehozásához.
    >

## <a name="create-database-objects"></a>Adatbázis-objektumok létrehozása
### <a name="database-scoped-master-key-and-credentials"></a>Adatbázis – hatókörrel rendelkező főkulcs és hitelesítő adatok
Ezek a szegmenses Térkép kezelőjéhez és a szegmensekhez való kapcsolódáshoz használatosak:

1. Nyissa meg SQL Server Management Studio vagy SQL Server Data Tools a Visual Studióban.
2. Kapcsolódjon a ElasticDBQuery adatbázishoz, és hajtsa végre a következő T-SQL-parancsokat:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    a "username" és a "password" utasításnak meg kell egyeznie a (z) szakasz 3. lépésében használt bejelentkezési adatokkal, és a **Elastic Database eszközök első lépései** című cikkben ismertetett módon [futtathatja a minta alkalmazást](elastic-scale-get-started.md#download-and-run-the-sample-app) .

### <a name="external-data-sources"></a>Külső adatforrások
Külső adatforrás létrehozásához hajtsa végre a következő parancsot a ElasticDBQuery-adatbázisban:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 A "CustomerIDShardMap" a szegmens Térkép neve, ha a szegmensek közötti térképet és a szegmens Térkép kezelőjét a rugalmas adatbázis-eszközök használatával hozta létre. Ha azonban az egyéni beállítást használta ehhez a mintához, akkor az alkalmazásban kiválasztott szegmenses leképezési nevet kell megadni.

### <a name="external-tables"></a>Külső táblák
Hozzon létre egy külső táblát, amely megegyezik a szegmensek ügyfelek táblájával a következő parancs ElasticDBQuery-adatbázison való végrehajtásával:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Minta rugalmas adatbázis-lekérdezés végrehajtása T-SQL-lekérdezés
Miután meghatározta a külső adatforrást és a külső táblázatokat, mostantól teljes T-SQL-T használhat a külső táblákon.

A lekérdezés végrehajtása a ElasticDBQuery-adatbázisban:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Figyelje meg, hogy a lekérdezés összesíti az összes szegmens eredményét, és a következő kimenetet adja:

![Kimenet részletei][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Rugalmas adatbázis lekérdezési eredményeinek importálása az Excelbe
 Az eredményeket importálhatja egy lekérdezésből egy Excel-fájlba.

1. Indítsa el az Excel 2013 alkalmazást.
2. Navigáljon az **adatszalagra** .
3. Kattintson **a más forrásokból** lehetőségre, és kattintson **az SQL Server** lehetőségre.

   ![Excel-importálás más forrásokból][5]
4. Az **adatkapcsolatok varázslóban** adja meg a kiszolgáló nevét és a bejelentkezési hitelesítő adatokat. Ezután kattintson a **Tovább** gombra.
5. A párbeszédpanelen **válassza ki a kívánt adatkészletet tartalmazó adatbázist**, és válassza ki a **ElasticDBQuery** -adatbázist.
6. Válassza ki a **Customers (ügyfelek)** táblát a lista nézetben, és kattintson a **tovább** gombra. Ezután kattintson a **Befejezés** gombra.
7. Az **adatimportálás** űrlapon a **válassza ki, hogyan szeretné megtekinteni az adatait a munkafüzetben**, válassza a **tábla** lehetőséget, majd kattintson **az OK** gombra.

Az **ügyfelek** tábla összes sora, amely különböző szegmensekben van tárolva, feltölti az Excel-lapot.

Mostantól használhatja az Excel hatékony adatvizualizációs funkcióit. A kapcsolati sztringet használhatja a kiszolgáló nevével, az adatbázis nevével és a hitelesítő adatokkal a BI-és adatintegrációs eszközök a rugalmas lekérdezési adatbázishoz való csatlakoztatásához. Győződjön meg arról, hogy a SQL Server támogatott adatforrásként az eszköz számára. A rugalmas lekérdezési adatbázist és a külső táblákat ugyanúgy tekintheti meg, mint bármely más SQL Server adatbázishoz, és SQL Server táblákhoz, amelyeket az eszközhöz szeretne csatlakozni.

### <a name="cost"></a>Költségek
A Elastic Database lekérdezési funkció használata nem díjköteles.

A díjszabással kapcsolatos információkért tekintse meg a [SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Következő lépések

* A rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés áttekintése](elastic-query-overview.md).
* A vertikális particionálással kapcsolatos oktatóanyagért lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](elastic-query-getting-started-vertical.md).
* A függőlegesen particionált információk szintaxisát és mintáit lásd: [függőlegesen particionált adatlekérdezés](elastic-query-vertical-partitioning.md)
* A horizontálisan particionált információk szintaxisát és mintáit lásd: [vízszintesen particionált adatlekérdezés](elastic-query-horizontal-partitioning.md)
* Lásd: az [SP \_ \_ távoli futtatása](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) olyan tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készleten.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->