---
title: Webhelynaplók elemzése az Azure Data Lake Analytics használatával
description: Ismerje meg, hogyan elemezheti a webhelyek naplóit a Azure Data Lake Analytics használatával a U-SQL függvények és lekérdezések futtatásához.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: d2764e5e3994eb7b80a8e7cf62fb7f437323c97e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015298"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Webhelynaplók elemzése az Azure Data Lake Analytics használatával
Megtudhatja, hogyan elemezheti a webhelyek naplóit a Data Lake Analytics használatával, különösen annak megállapításához, hogy mely hivatkozó hibák léptek fel a webhelyre.

## <a name="prerequisites"></a>Előfeltételek
* **Visual studio 2015 vagy Visual studio 2013**.
* **[Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)**.

    A Visual Studio Data Lake eszközeinek telepítése után a Visual Studio **eszközök** menüjében megjelenik egy **Data Lake** elem:

    ![U-SQL Visual Studio menü](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **A Data Lake Analytics és a Visual studióhoz készült Data Lake Tools alapszintű ismerete**. Első lépésként tekintse meg a következőt:

  * [U-SQL-szkript fejlesztése a Visual studióhoz készült Data Lake Tools használatával](data-lake-analytics-data-lake-tools-get-started.md).
* **Egy Data Lake Analytics-fiók.**  Lásd: [Azure Data Lake Analytics fiók létrehozása](data-lake-analytics-get-started-portal.md).
* **A mintaadatok telepítése.** Az Azure Portalon nyissa meg Data Lake Analytics fiókot, és kattintson a bal oldali menüben a **minta parancsfájlok** elemre, majd kattintson a **mintaadatok másolása** lehetőségre. 

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
A U-SQL-parancsfájlok létrehozása és tesztelése előtt először csatlakoznia kell az Azure-hoz.

### <a name="to-connect-to-data-lake-analytics"></a>A Data Lake Analytics szolgáltatáshoz való kapcsolódáshoz

1. Nyissa meg a Visual Studiót.
2. Kattintson **Data Lake > lehetőségek és beállítások** elemre.
3. Kattintson a **Bejelentkezés** gombra, vagy **változtassa** meg a felhasználót, ha valaki bejelentkezett, és kövesse az utasításokat.
4. A beállítások és beállítások párbeszédpanel bezárásához kattintson **az OK** gombra.

### <a name="to-browse-your-data-lake-analytics-accounts"></a>A Data Lake Analytics-fiókok tallózása

1. A Visual studióból nyissa meg a **Server Explorert** a **CTRL + ALT + S** billentyűkombinációval.
2. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája, ha vannak ilyenek. A studióból nem hozhatók létre Data Lake Analytics fiókok. Fiók létrehozásához tekintse meg az első [lépések a Azure Data Lake Analytics az Azure Portal használatával](data-lake-analytics-get-started-portal.md) vagy a [Azure Data Lake Analytics használatának első lépései a Azure PowerShell használatával](data-lake-analytics-get-started-powershell.md)című témakört.

## <a name="develop-u-sql-application"></a>U-SQL-alkalmazás fejlesztése
Egy U-SQL-alkalmazás többnyire U-SQL-szkript. További információ az U-SQL-ről: Ismerkedés [az u-](data-lake-analytics-u-sql-get-started.md)SQL használatával.

Hozzáadhat további felhasználó által definiált operátorokat is az alkalmazáshoz.  További információ: [U-SQL felhasználó által definiált operátorok fejlesztése Data Lake Analytics feladatokhoz](data-lake-analytics-u-sql-develop-user-defined-operators.md).

### <a name="to-create-and-submit-a-data-lake-analytics-job"></a>Data Lake Analytics-feladat létrehozása és elküldése

1. Kattintson a **fájl > új > projekt** elemre.

2. Válassza ki a U-SQL projekt típust.

   ![új U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy Script.usql fájllal.

4. Adja meg a következő parancsfájlt a script. usql fájlba:

   ```usql
   // Create a database for easy reuse, so you don't need to read from a file very time.
   CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

   // Create a Table valued function. TVF ensures that your jobs fetch data from he weblog file with the correct schema.
   DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
   CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
   RETURNS @result TABLE
   (
       s_date DateTime,
       s_time string,
       s_sitename string,
       cs_method string,
       cs_uristem string,
       cs_uriquery string,
       s_port int,
       cs_username string,
       c_ip string,
       cs_useragent string,
       cs_cookie string,
       cs_referer string,
       cs_host string,
       sc_status int,
       sc_substatus int,
       sc_win32status int,
       sc_bytes int,
       cs_bytes int,
       s_timetaken int
   )
   AS
   BEGIN

       @result = EXTRACT
           s_date DateTime,
           s_time string,
           s_sitename string,
           cs_method string,
           cs_uristem string,
           cs_uriquery string,
           s_port int,
           cs_username string,
           c_ip string,
           cs_useragent string,
           cs_cookie string,
           cs_referer string,
           cs_host string,
           sc_status int,
           sc_substatus int,
           sc_win32status int,
           sc_bytes int,
           cs_bytes int,
           s_timetaken int
       FROM @"/Samples/Data/WebLog.log"
       USING Extractors.Text(delimiter:' ');
       RETURN;
   END;

   // Create a table for storing referrers and status
   DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
   @weblog = SampleDBTutorials.dbo.WeblogsView();
   CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
   (
       INDEX idx1
       CLUSTERED(Year ASC)
       DISTRIBUTED BY HASH(Year)
   ) AS

   SELECT s_date.Year AS Year,
       s_date.Month AS Month,
       s_date.Day AS Day,
       cs_referer,
       sc_status,
       COUNT(DISTINCT c_ip) AS cnt
   FROM @weblog
   GROUP BY s_date,
           cs_referer,
           sc_status;
   ```

    Az U-SQL megismeréséhez tekintse meg a [Data Lake Analytics u-SQL-nyelv – első lépések](data-lake-analytics-u-sql-get-started.md)című témakört.

5. Adjon hozzá egy új U-SQL-szkriptet a projekthez, és adja meg a következőket:

   ```usql
   // Query the referrers that ran into errors
   @content =
       SELECT *
       FROM SampleDBTutorials.dbo.ReferrersPerDay
       WHERE sc_status >=400 AND sc_status < 500;

   OUTPUT @content
   TO @"/Samples/Outputs/UnsuccessfulResponses.log"
   USING Outputters.Tsv();
   ```

6. Váltson vissza az első U-SQL-szkriptre, majd a **Küldés** gomb mellett adja meg az Analytics-fiókját.

7. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre. Ellenőrizze az eredményeket a kimenet ablaktáblán.

8. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre.

9. Ellenőrizze, hogy az **Analytics-fiók** az a hely, ahol futtatni szeretné a feladatot, majd kattintson a **Submit (Küldés**) gombra. Az elküldés után az eredmények és a feladatra mutató hivatkozás megjelenik a Data Lake Tools for Visual Studio Eredmények ablakában.

10. Várjon, amíg a rendszer sikeresen befejezi a feladatot.  Ha a feladatot nem sikerült végrehajtani, valószínűleg hiányzik a forrásfájl.  Tekintse meg az oktatóanyag előfeltételeit ismertető szakaszt. További hibaelhárítási információkért lásd: [Azure Data Lake Analytics feladatok figyelése és hibaelhárítása](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    A feladatok befejezésekor a következő képernyő jelenik meg:

    ![a adatközpont-elemzési webnaplók webnaplóinak elemzése](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Most ismételje meg a 7-10-es lépést a **Script1. usql** esetében.

### <a name="to-see-the-job-output"></a>Feladat kimenetének megtekintése

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, bontsa ki a **Storage Accounts** (Tárfiókok) elemet, kattintson a jobb gombbal az alapértelmezett Data Lake-tárfiókra, majd kattintson az **Explorer** elemre.
2. A mappa megnyitásához kattintson duplán a **minták** elemre, majd kattintson duplán a **kimenetek** elemre.
3. Kattintson duplán a **UnsuccessfulResponses. log naplófájlra**.
4. Azt is megteheti, hogy duplán kattint a kimeneti fájlra a feladatok gráf nézetében, hogy közvetlenül a kimenetre navigáljon.

## <a name="next-steps"></a>Következő lépések
A Data Lake Analytics különböző eszközökkel való használatának megismeréséhez lásd:

* [Ismerkedés a Data Lake Analytics az Azure Portal használatával](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a .NET SDK-val](./data-lake-analytics-get-started-cli.md)