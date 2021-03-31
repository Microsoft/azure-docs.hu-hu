---
title: Ismerkedés a Elastic Database eszközökkel
description: Az Azure SQL Database Elastic Database Tools funkciójának alapvető magyarázata, beleértve egy könnyen futtatható minta alkalmazást is.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 74343b2f05bb4a59e475449c87524ff66cdd605d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919544"
---
# <a name="get-started-with-elastic-database-tools"></a>Ismerkedés a Elastic Database eszközökkel
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebből a dokumentumból megtekintheti a [rugalmas adatbázis-ügyféloldali függvénytár](elastic-database-client-library.md) fejlesztői felületét, ha egy minta alkalmazást futtat. A minta alkalmazás létrehoz egy egyszerű, többrészes alkalmazást, és felderíti a Azure SQL Database Elastic Database Tools funkciójának főbb képességeit. A szegmensek közötti [Térkép felügyeletére](elastic-scale-shard-map-management.md), az [Adatfüggő útválasztásra](elastic-scale-data-dependent-routing.md)és a [többrétegű lekérdezések](elastic-scale-multishard-querying.md)használatára összpontosít. Az ügyféloldali kódtár a .NET-hez és a Javához is elérhető.

## <a name="elastic-database-tools-for-java"></a>Java-eszközök Elastic Database

### <a name="prerequisites"></a>Előfeltételek

* Java Developer Kit (JDK), 1,8-es vagy újabb verzió
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database vagy helyi SQL Server példány

### <a name="download-and-run-the-sample-app"></a>A minta alkalmazás letöltése és futtatása

A JAR-fájlok létrehozásához és a minta projekt első lépéseihez tegye a következőket:

1. Az ügyféloldali kódtárat tartalmazó [GitHub-tárház](https://github.com/Microsoft/elastic-db-tools-for-java) klónozása a minta alkalmazással együtt.

2. Szerkessze a _./Sample/src/Main/Resources/Resource.properties_ fájlt a következő beállításához:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. A minta projekt létrehozásához futtassa a következő parancsot a _./Sample_ könyvtárban:

    ```
    mvn install
    ```

4. A minta projekt elindításához futtassa a következő parancsot a _./Sample_ könyvtárban:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Ha többet szeretne megtudni az ügyféloldali kódtár képességeiről, kísérletezzen a különböző lehetőségekkel. Tekintse át a kódot, és ismerkedjen meg a minta alkalmazás megvalósításával.

    ![Folyamat – Java][5]

Gratulálunk! Sikeresen felépítette és futtatta az első felhalmozott alkalmazást a Azure SQL Database Elastic Database eszközeinek használatával. A Visual Studióval vagy a SQL Server Management Studiotel csatlakozhat az adatbázishoz, és gyors áttekintést készíthet a minta által létrehozott szegmensekről. Ekkor megjelenik az új, példaként használt szegmens-adatbázisok és a minta által létrehozott szegmens Map Manager-adatbázis.

Az ügyféloldali kódtár saját Maven-projekthez való hozzáadásához adja hozzá a következő függőséget a POM-fájlhoz:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>A .NET Elastic Database eszközei

### <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2012 vagy újabb a C# használatával. Töltse le az ingyenes verziót a [Visual Studio letöltések](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)szolgáltatásban.
* NuGet 2,7 vagy újabb. A legújabb verzió beszerzéséhez lásd: a [NuGet telepítése](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>A minta alkalmazás letöltése és futtatása

A könyvtár telepítéséhez nyissa meg a [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)programot. A függvénytár a következő szakaszban ismertetett minta alkalmazással együtt települ.

A minta letöltéséhez és futtatásához kövesse az alábbi lépéseket:

1. Töltse le az [Azure SQL-első lépések minta rugalmas adatbázis-eszközeit](https://github.com/Azure/elastic-db-tools). Bontsa ki a mintát egy kiválasztott helyre.

2. Projekt létrehozásához nyissa meg a *ElasticDatabaseTools. SLN* megoldást a *rugalmas-db-Tools-Master* könyvtárból. 

3. Állítsa be a *ElasticScaleStarterKit* projektet indítási projektként.

4. A *ElasticScaleStarterKit* projektben nyissa meg a *App.config* fájlt. Ezután a fájl utasításait követve adja hozzá a kiszolgáló nevét és a bejelentkezési adatait (Felhasználónév és jelszó).

5. Hozza létre és futtassa az alkalmazást. Ha a rendszer kéri, engedélyezze a Visual Studio számára a megoldás NuGet-csomagjainak visszaállítását. Ez a művelet letölti a rugalmas adatbázis ügyféloldali függvénytárának legújabb verzióját a NuGet webhelyről.

6. Ha többet szeretne megtudni az ügyféloldali kódtár képességeiről, kísérletezzen a különböző lehetőségekkel. Figyelje meg, hogy az alkalmazás hogyan veszi át a konzol kimenetét, és nyugodtan vizsgálja meg a jelenetek mögötti kódot.

   ![Előrehaladás][4]

Gratulálunk! Sikeresen felépítette és futtatta az első felhalmozott alkalmazást a SQL Database Elastic Database eszközeinek használatával. A Visual Studióval vagy a SQL Server Management Studiotel csatlakozhat az adatbázishoz, és gyors áttekintést készíthet a minta által létrehozott szegmensekről. Ekkor megjelenik az új, példaként használt szegmens-adatbázisok és a minta által létrehozott szegmens Map Manager-adatbázis.

> [!IMPORTANT]
> Javasoljuk, hogy mindig a Management Studio legújabb verzióját használja, hogy szinkronizálva maradjon az Azure-ban és a SQL Database. [Az SQL Server Management Studio frissítése](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="key-pieces-of-the-code-sample"></a>A mintakód legfontosabb darabjai

* Szegmensek **és szegmens térképek kezelése**: a kód azt szemlélteti, hogyan használhatók a *ShardManagementUtils. cs* fájlban a szegmensek, tartományok és leképezések. További információ: adatbázisok horizontális [Felskálázása a szegmenses Térkép kezelőjével](https://go.microsoft.com/?linkid=9862595).  

* **Adatfüggő útválasztás**: a jobb oldali szegmenshez tartozó tranzakciók útválasztása a *DataDependentRoutingSample. cs* fájlban látható. További információ: [az Adatkezeléstől függő útválasztás](https://go.microsoft.com/?linkid=9862596).

* **Több szegmensre történő lekérdezés**: a *MultiShardQuerySample. cs* fájlban láthatók a több szegmens közötti lekérdezések. További információ: [több szegmensből álló lekérdezés](https://go.microsoft.com/?linkid=9862597).

* **Üres** szegmensek hozzáadása: az új üres szegmensek iterációs hozzáadását a *CreateShardSample. cs* fájl kódja hajtja végre. További információ: adatbázisok horizontális [Felskálázása a szegmenses Térkép kezelőjével](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Egyéb rugalmas skálázási műveletek

* **Meglévő szegmens felosztása**: a szegmensek felosztására szolgáló képességeket a felosztott egyesítési eszköz is megadja. További információ: adatok áthelyezése a kibővített [felhőalapú adatbázisok között](elastic-scale-overview-split-and-merge.md).

* A meglévő szegmensek **egyesítése**: a szegmens egyesítéseket a felosztási egyesítés eszköz használatával is elvégezheti. További információ: adatok áthelyezése a kibővített [felhőalapú adatbázisok között](elastic-scale-overview-split-and-merge.md).

## <a name="cost"></a>Költségek

A Elastic Database-eszközök könyvtára ingyenes. Elastic Database-eszközök használatakor az Azure-használat költségein túl semmilyen további díjat nem kell fizetnie.

Például a minta alkalmazás új adatbázisokat hoz létre. Ennek a képességnek a díja a kiválasztott SQL Database-kiadástól és az alkalmazás Azure-használattól függ.

A díjszabással kapcsolatos információkért tekintse meg a [SQL Database díjszabását](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Következő lépések

Elastic Database eszközökkel kapcsolatos további információkért tekintse meg a következő cikkeket:

* Kód mintái:
  * Eszközök Elastic Database ([.net](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Az Azure SQL-Entity Framework integrációs eszközeinek Elastic Database](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Szilánkok rugalmassága a script Centerben](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [rugalmas skálázási közlemény](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* 9. csatorna: [rugalmas méretezés – áttekintés videó](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Vitafórum: [Microsoft Q&Azure SQL Database](/answers/topics/azure-sql-database.html)
* A teljesítmény mérése: teljesítményszámlálók a szegmenses [Térkép kezelőjéhez](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG