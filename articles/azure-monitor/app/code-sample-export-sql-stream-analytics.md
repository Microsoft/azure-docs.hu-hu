---
title: Exportálás az Azure-ból az SQL-be Application Insights | Microsoft Docs
description: Application Insights-SQL-adatbázis folyamatos exportálása a Stream Analytics használatával.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 25d28bb0fc35ef76231c085dc1d9d8a1234a264c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587757"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Útmutató: exportálás az SQL rendszerbe Application Insights használatával Stream Analytics
Ez a cikk bemutatja, hogyan helyezheti át a telemetria-adatait az [Azure Application Insightsból][start] a [folyamatos exportálás][export] és a [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/)használatával Azure SQL Databaseba. 

A folyamatos exportálás JSON formátumban helyezi át a telemetria adatait az Azure Storage-ba. A JSON-objektumokat a Azure Stream Analytics használatával elemezzük, és sorokat hozunk létre az adatbázis-táblában.

(Általánosságban a folyamatos Exportálás az alkalmazások telemetria saját elemzését teszi lehetővé Application Insights. A kód mintáját úgy alakíthatja át, hogy más műveleteket hajtson végre az exportált telemetria, például az adatösszesítést.)

Kezdjük azzal a feltételezéssel, hogy már rendelkezik a figyelni kívánt alkalmazással.

Ebben a példában az oldal nézetét fogjuk használni, de ugyanezt a mintát egyszerűen kiterjesztheti más adattípusokra, például az egyéni eseményekre és kivételekre is. 

## <a name="add-application-insights-to-your-application"></a>Application Insights hozzáadása az alkalmazáshoz
Első lépések:

1. [Application Insights beállítása a weblapok számára](./javascript.md). 
   
    (Ebben a példában az ügyféloldali böngészők adatainak feldolgozására koncentrálunk, de Application Insightst is beállíthat a [Java](./java-get-started.md) -vagy [ASP.net](./asp-net.md) -alkalmazás kiszolgálói oldalára, és feldolgozhatja a kérelmeket, a függőségeket és az egyéb kiszolgálói telemetria.)
2. Tegye közzé az alkalmazást, és tekintse meg a Application Insights-erőforrásban megjelenő telemetria-információkat.

## <a name="create-storage-in-azure"></a>Tároló létrehozása az Azure-ban
A folyamatos exportálás mindig az adatokat egy Azure Storage-fiókba exportálja, ezért először létre kell hoznia a tárolót.

1. Hozzon létre egy Storage-fiókot az előfizetésében a [Azure Portalban][portal].
   
    ![Azure Portal kattintson az új, az adattároló elemre. Válassza a klasszikus lehetőséget, majd a létrehozás lehetőséget. Adja meg a tároló nevét.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Tároló létrehozása
   
    ![Az új tárolóban válassza a tárolók lehetőséget, kattintson a tárolók csempére, majd adja hozzá a](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. A tárolási hozzáférési kulcs másolása
   
    Hamarosan be kell állítania a stream Analytics szolgáltatás bemenetét.
   
    ![A tárolóban nyissa meg a beállításokat, a kulcsokat, és készítsen másolatot az elsődleges elérési kulcsról](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Folyamatos exportálás indítása az Azure Storage-ba
1. A Azure Portal tallózással keresse meg az alkalmazáshoz létrehozott Application Insights-erőforrást.
   
    ![Válassza a Tallózás, Application Insights, alkalmazás](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Hozzon létre egy folyamatos exportálást.
   
    ![Beállítások kiválasztása, folyamatos exportálás, Hozzáadás](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Válassza ki a korábban létrehozott Storage-fiókot:

    ![Az Exportálás célhelyének beállítása](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Állítsa be a megtekinteni kívánt események típusát:

    ![Eseménytípus kiválasztása](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Némi adatmennyiséget is felhalmozhat. Dőljön hátra, és hagyja, hogy a felhasználók egy ideig használják az alkalmazást. A telemetria a következő helyen jelenik meg: statisztikai diagramok a [metrika-kezelőben](../essentials/metrics-charts.md) és az egyes események a [diagnosztikai keresésben](./diagnostic-search.md). 
   
    És az is, hogy az adatai exportálva lesznek a tárhelyre. 
2. Vizsgálja meg az exportált adatait a portálon – válassza a **Tallózás** lehetőséget, válassza ki a Storage-fiókját, majd a **tárolók** lehetőséget, vagy a Visual Studióban. A Visual Studióban válassza a **Megtekintés/Cloud Explorer** lehetőséget, majd nyissa meg az Azure/Storage elemet. (Ha nem rendelkezik ezzel a menüponttal, telepítenie kell az Azure SDK-t: Nyissa meg az új projekt párbeszédpanelt, és nyissa meg a Visual C#/Cloud/Get Microsoft Azure SDK-t a .NET-hez.)
   
    ![A Visual Studióban nyissa meg a Server Browser, az Azure, a Storage](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Jegyezze fel az elérési út nevének közös részét, amely az alkalmazás neve és a kialakítási kulcsból származik. 

Az események JSON formátumú blob-fájlokba íródnak. Az egyes fájlok egy vagy több eseményt is tartalmazhatnak. Ezért szeretnénk beolvasni az események adatait, és kiszűrni a kívánt mezőket. Az adatkezeléshez sokféle dolog van, de a tervünk szerint a Stream Analytics használatával helyezheti át az adatSQL Databaseba. Ez megkönnyíti a sok érdekes lekérdezés futtatását.

## <a name="create-an-azure-sql-database"></a>Azure SQL Database-adatbázis létrehozása
A [Azure Portal][portal]-előfizetésből való indítás után hozza létre az adatbázist (és egy új kiszolgálót, ha már van ilyen), amelybe az adatok írhatók.

![Új, adatkezelési, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Győződjön meg arról, hogy a kiszolgáló engedélyezi az Azure-szolgáltatásokhoz való hozzáférést:

![Tallózás, kiszolgálók, a kiszolgáló, a beállítások, a tűzfal, az Azure-hoz való hozzáférés engedélyezése](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Tábla létrehozása Azure SQL Databaseban
Kapcsolódjon az előző szakaszban létrehozott adatbázishoz az előnyben részesített felügyeleti eszközzel. Ebben az útmutatóban a [SQL Server felügyeleti eszközei](/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatát fogjuk használni.

![Kapcsolódás az Azure SQL Database-hez](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Hozzon létre egy új lekérdezést, és hajtsa végre a következő T-SQL-T:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![PageViewsTable létrehozása](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Ebben a példában a lap nézeteiből származó adatok szerepelnek. Ha meg szeretné tekinteni az elérhető egyéb adatokat, ellenőrizze a JSON-kimenetét, és tekintse meg az [adatexportálási modellt](./export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics példány létrehozása
A [Azure Portal](https://portal.azure.com/)válassza ki a Azure stream Analytics szolgáltatást, és hozzon létre egy új stream Analytics feladatot:

![Képernyőfelvétel: a stream Analytics-feladat lapja, a létrehozás gombbal kiemelve.](./media/code-sample-export-sql-stream-analytics/SA001.png)

![Új stream Analytics-feladatok](./media/code-sample-export-sql-stream-analytics/SA002.png)

Az új feladatok létrehozásakor válassza az **Ugrás erőforráshoz** lehetőséget.

![A képernyőképen az üzemelő példány sikeres üzenet jelenik meg, majd az erőforrás gombra.](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Új bemenet hozzáadása

![Képernyőfelvétel: megjeleníti a bemenetek lapot, ahol a Hozzáadás gomb van kiválasztva.](./media/code-sample-export-sql-stream-analytics/SA004.png)

Állítsa be úgy, hogy a folyamatos exportálási blob bemenetét használja:

![Képernyőfelvétel: az új bemeneti ablak, amelyben a bemeneti alias, a forrás és a Storage-fiók legördülő menüje van kiválasztva.](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Most szüksége lesz az elsődleges hozzáférési kulcsra a Storage-fiókjából, amelyet korábban említett. Adja meg ezt a Storage-fiók kulcsaként.

#### <a name="set-path-prefix-pattern"></a>Elérésiút-előtag beállítása minta

**Ügyeljen arra, hogy a dátumformátumot éééé-hh-nn (kötőjelekkel) állítsa be.**

Az elérési út előtagja minta meghatározza, hogy a Stream Analytics hogyan találja meg a bemeneti fájlokat a tárolóban. Be kell állítania, hogy a folyamatos exportálás hogyan tárolja az adattárolást. Állítsa be a következőhöz hasonlót:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

Ebben a példában:

* `webapplication27` a Application Insights erőforrás neve, amely az **összes kisbetűs**. 
* `1234...` a Application Insights erőforrás rendszerállapot-kulcsát **eltávolították a kötőjelekkel**. 
* `PageViews` az elemezni kívánt adattípus. A rendelkezésre álló típusok a folyamatos exportálásban beállított szűrőtől függenek. Vizsgálja meg az exportált adattípusokat, és tekintse meg az [adatmodell exportálása](./export-data-model.md)című témakört.
* `/{date}/{time}` egy, a szó szerint írt minta.

A Application Insights-erőforrás nevének és Rendszerállapotkulcsot beszerzéséhez nyissa meg az Essentials szolgáltatást az Áttekintés lapon, vagy nyissa meg a beállításokat.

> [!TIP]
> A minta függvény használatával győződjön meg arról, hogy helyesen adta meg a bemeneti útvonalat. Ha a művelet sikertelen, ellenőrizze, hogy a tárolóban található-e a kiválasztott időtartományhoz tartozó adat. Szerkessze a bemeneti definíciót, és győződjön meg arról, hogy a Storage-fiók, az elérésiút-előtag és a dátumformátum helyesen van beállítva.

 
## <a name="set-query"></a>Lekérdezés beállítása
A lekérdezés szakasz megnyitása:

Cserélje le az alapértelmezett lekérdezést a alábbiakra:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Figyelje meg, hogy az első néhány tulajdonság az oldal megtekintési adatára vonatkozik. Más telemetria-típusok exportálása különböző tulajdonságokkal fog rendelkezni. Tekintse [meg a tulajdonságok típusaira és értékeire vonatkozó részletes adatmodell-referenciát.](./export-data-model.md)

## <a name="set-up-output-to-database"></a>Kimenet beállítása az adatbázisba
Kimenetként válassza az SQL lehetőséget.

![A stream Analyticsben válassza a kimenetek lehetőséget.](./media/code-sample-export-sql-stream-analytics/SA006.png)

Határozza meg az adatbázist.

![Adja meg az adatbázis adatait](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zárjuk be a varázslót, és várjon egy értesítést, hogy a kimenet be lett állítva.

## <a name="start-processing"></a>Feldolgozás megkezdése
Indítsa el a feladatot a műveleti sávon:

![A stream Analyticsben kattintson a Start gombra.](./media/code-sample-export-sql-stream-analytics/SA008.png)

Megadhatja, hogy szeretné-e az adatok feldolgozását mostantól kezdve, vagy a korábbi adatokkal kezdeni. Az utóbbi akkor lehet hasznos, ha már fut egy ideig a folyamatos exportálás.

Néhány perc elteltével lépjen vissza SQL Server felügyeleti eszközeire, és tekintse meg a folyamatban lévő adatforgalmat. Például használjon a következőhöz hasonló lekérdezést:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Exportálás Power BI a Stream Analytics használatával](./export-power-bi.md)
* [részletes adatmodell-referenciák a tulajdonságok típusaihoz és értékeihez.](./export-data-model.md)
* [Folyamatos exportálás Application Insights](./export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[export]: ./export-telemetry.md
[metrics]: ../essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

