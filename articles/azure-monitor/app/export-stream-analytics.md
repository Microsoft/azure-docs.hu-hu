---
title: Exportálás az Azure Application Insights Stream Analytics használatával | Microsoft Docs
description: Stream Analytics a Application Insightsból exportált adatok folyamatos átalakítását, szűrését és átirányítását is elvégezheti.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: a517bddd8981554b7fb5044d33b6c6777df51e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719797"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Stream Analytics használata az exportált adatok feldolgozásához Application Insights

A [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ideális eszköz a [Application Insightsból exportált](export-telemetry.md)adatok feldolgozásához. A Stream Analytics különböző forrásokból származó adatok lekérésére használható. Átalakíthatja és szűrheti az adatmennyiséget, majd átirányíthatja azt különböző mosdók számára.

Ebben a példában egy olyan adaptert hozunk létre, amely Application Insights, átnevezi és feldolgozza a mezőket, és a Power BIba helyezi azokat.

> [!WARNING]
> A [Application Insights-adatPower BIek megjelenítésének](./export-power-bi.md)sokkal jobb és egyszerűbb módja is. Az itt látható elérési út szemlélteti az exportált adatfeldolgozás módját.

![Az SA és a PBI közötti exportálás diagramjának letiltása](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Tároló létrehozása az Azure-ban
A folyamatos exportálás mindig az adatokat egy Azure Storage-fiókba exportálja, ezért először létre kell hoznia a tárolót.

1. Hozzon létre egy "klasszikus" Storage-fiókot az előfizetésében a [Azure Portalban](https://portal.azure.com).
   
   ![A Azure Portal területen válassza az új, az adattároló](./media/export-stream-analytics/030.png)
2. Tároló létrehozása
   
    ![Az új tárolóban válassza a tárolók lehetőséget, kattintson a tárolók csempére, majd adja hozzá a](./media/export-stream-analytics/040.png)
3. A tárolási hozzáférési kulcs másolása
   
    Hamarosan be kell állítania a stream Analytics szolgáltatás bemenetét.
   
    ![A tárolóban nyissa meg a beállításokat, a kulcsokat, és készítsen másolatot az elsődleges elérési kulcsról](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Folyamatos exportálás indítása az Azure Storage-ba

A [folyamatos exportálás](export-telemetry.md) a Application Insights adatait az Azure Storage-ba helyezi át.

1. A Azure Portal tallózással keresse meg az alkalmazáshoz létrehozott Application Insights-erőforrást.
   
    ![Válassza a Tallózás, Application Insights, alkalmazás](./media/export-stream-analytics/050.png)
2. Hozzon létre egy folyamatos exportálást.
   
    ![Beállítások kiválasztása, folyamatos exportálás, Hozzáadás](./media/export-stream-analytics/060.png)

    Válassza ki a korábban létrehozott Storage-fiókot:

    ![Az Exportálás célhelyének beállítása](./media/export-stream-analytics/070.png)

    Állítsa be a megtekinteni kívánt események típusát:

    ![Eseménytípus kiválasztása](./media/export-stream-analytics/080.png)

1. Némi adatmennyiséget is felhalmozhat. Dőljön hátra, és hagyja, hogy a felhasználók egy ideig használják az alkalmazást. A telemetria a következő helyen jelenik meg: statisztikai diagramok a [metrika-kezelőben](../essentials/metrics-charts.md) és az egyes események a [diagnosztikai keresésben](./diagnostic-search.md).
   
    És az is, hogy az adatai exportálva lesznek a tárhelyre. 
2. Vizsgálja meg az exportált adatgyűjtést. A Visual Studióban válassza a **Megtekintés/Cloud Explorer** lehetőséget, majd nyissa meg az Azure/Storage elemet. (Ha nem rendelkezik ezzel a menüponttal, telepítenie kell az Azure SDK-t: Nyissa meg az új projekt párbeszédpanelt, és nyissa meg a Visual C#/Cloud/Get Microsoft Azure SDK-t a .NET-hez.)
   
    ![Képernyőfelvétel: a megtekinteni kívánt események típusának beállítása.](./media/export-stream-analytics/04-data.png)
   
    Jegyezze fel az elérési út nevének közös részét, amely az alkalmazás neve és a kialakítási kulcsból származik.

Az események JSON formátumú blob-fájlokba íródnak. Az egyes fájlok egy vagy több eseményt is tartalmazhatnak. Ezért szeretnénk beolvasni az események adatait, és kiszűrni a kívánt mezőket. Az adatkezeléshez mindenféle dolgot használhatunk, de a tervünk szerint a Stream Analytics segítségével adatcsatornán keresztül Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics példány létrehozása

A [Azure Portal](https://portal.azure.com/)válassza ki a Azure stream Analytics szolgáltatást, és hozzon létre egy új stream Analytics feladatot:

![A Stream Analytics-feladatoknak a Azure Portalban való létrehozásának főoldalát bemutató képernyőkép.](./media/export-stream-analytics/SA001.png)

![Képernyőkép, amely az új Stream Analytics feladatok létrehozásakor szükséges adatokat jeleníti meg.](./media/export-stream-analytics/SA002.png)

Az új feladatok létrehozásakor válassza az **Ugrás erőforráshoz** lehetőséget.

![Képernyőfelvétel: az új Stream Analytics feladatok központi telepítésének sikerességét jelző üzenet.](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Új bemenet hozzáadása

![Képernyőkép, amely bemutatja, hogyan adhat hozzá bemeneteket a Stream Analytics feladathoz.](./media/export-stream-analytics/SA004.png)

Állítsa be úgy, hogy a folyamatos exportálási blob bemenetét használja:

![Képernyőfelvétel: a Stream Analytics-feladatok konfigurálásának beállítása a folyamatos exportálási Blobok bemenetének megadásához.](./media/export-stream-analytics/SA0005.png)

Most szüksége lesz az elsődleges hozzáférési kulcsra a Storage-fiókjából, amelyet korábban említett. Adja meg ezt a Storage-fiók kulcsaként.

### <a name="set-path-prefix-pattern"></a>Elérésiút-előtag beállítása minta

**Ügyeljen arra, hogy a dátumformátumot éééé-hh-nn (kötőjelekkel) állítsa be.**

Az elérési út előtagja minta megadja, hogy a Stream Analytics hol találja meg a tárolóban lévő bemeneti fájlokat. Be kell állítania, hogy a folyamatos exportálás hogyan tárolja az adattárolást. Állítsa be a következőhöz hasonlót:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

Ebben a példában:

* `webapplication27` a Application Insights erőforrás neve az **összes kisbetű**.
* `1234...` a Application Insights erőforrás rendszerállapot-kulcsa, a **kötőjelek kihagyása**. 
* `PageViews` az elemezni kívánt adattípust adja meg. A rendelkezésre álló típusok a folyamatos exportálásban beállított szűrőtől függenek. Vizsgálja meg az exportált adattípusokat, és tekintse meg az [adatmodell exportálása](export-data-model.md)című témakört.
* `/{date}/{time}` egy, a szó szerint írt minta.

> [!NOTE]
> Ellenőrizze a tárterületet, és győződjön meg arról, hogy a megfelelő elérési utat kapja.

## <a name="add-new-output"></a>Új kimenet hozzáadása

Most válassza ki a feladatot, > **kimenet**  >  **hozzáadása** elemet.

![Képernyőfelvétel: a Stream Analytics feladatainak kiválasztásával új kimenetet adhat hozzá.](./media/export-stream-analytics/SA006.png)


![Válassza ki az új csatornát, kattintson a kimenetek, Hozzáadás, Power BI](./media/export-stream-analytics/SA010.png)

Adja meg a **munkahelyi vagy iskolai fiókját** , hogy engedélyezze stream Analytics számára a Power bi-erőforrás elérését. Ezután kitalál egy nevet a kimenethez, valamint a cél Power BI adatkészlethez és táblához.

## <a name="set-the-query"></a>A lekérdezés beállítása

A lekérdezés a bemenetről a kimenetre irányítja a fordítást.

A teszt függvénnyel ellenőrizze, hogy a megfelelő kimenetet kapja-e. Adja meg a bemenetek lapról vett mintaadatok adatait.

### <a name="query-to-display-counts-of-events"></a>Események számának megjelenítésére szolgáló lekérdezés

A lekérdezés beillesztése:

```SQL
SELECT
  flat.ArrayValue.name,
  count(*)
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.[event]) as flat
GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* exportálás – a bemenet a stream bemenetéhez megadott alias.
* PBI – a kimeneti alias definiálva
* [Külső alkalmazás-GetElements](/stream-analytics-query/apply-azure-stream-analytics) használunk, mert az esemény neve beágyazott JSON-tömbben van. Ezután a válassza ki az esemény nevét, valamint az adott névvel rendelkező példányok számát az adott időszakban. A [Group By](/stream-analytics-query/group-by-azure-stream-analytics) záradék egy perc alatt csoportosítja az elemeket.

### <a name="query-to-display-metric-values"></a>Metrikai értékek megjelenítésére szolgáló lekérdezés

```SQL
SELECT
  A.context.data.eventtime,
  avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.context.custom.metrics) as flat
GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime
```

* Ez a lekérdezés a metrikák telemetria beolvassa az esemény időpontját és a metrika értékét. A metrikai értékek egy tömbön belül találhatók, ezért a sorok kinyeréséhez a külső GetElements mintát használjuk. Ebben az esetben a "myMetric" a metrika neve.

### <a name="query-to-include-values-of-dimension-properties"></a>A dimenzió tulajdonságainak értékeit tartalmazó lekérdezés

```SQL
WITH flat AS (
SELECT
  MySource.context.data.eventTime as eventTime,
  InstanceId = MyDimension.ArrayValue.InstanceId.value,
  BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
FROM MySource
OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
)
SELECT
  eventTime,
  InstanceId,
  BusinessUnitId
INTO AIOutput
FROM flat
```

* Ez a lekérdezés a dimenzió tulajdonságainak értékeit tartalmazza anélkül, hogy a dimenzió tömbben rögzített indexben lévő adott dimenzión kellene lennie.

## <a name="run-the-job"></a>A feladat futtatása

Kiválaszthat egy dátumot a múltban, hogy elindítsa a feladatot.

![Válassza ki a feladatot, és kattintson a lekérdezés lehetőségre. Illessze be az alábbi mintát.](./media/export-stream-analytics/SA008.png)

Várjon, amíg a feladatok futnak.

## <a name="see-results-in-power-bi"></a>Eredmények megtekintése Power BI

> [!WARNING]
> A [Application Insights-adatPower BIek megjelenítésének](./export-power-bi.md)sokkal jobb és egyszerűbb módja is. Az itt látható elérési út szemlélteti az exportált adatfeldolgozás módját.


Nyissa meg Power BIt munkahelyi vagy iskolai fiókjával, és válassza ki a Stream Analytics feladatának kimenetében megadott adatkészletet és táblát.

![A Power BI területen válassza ki az adatkészletet és a mezőket.](./media/export-stream-analytics/200.png)

Ezt az adatkészletet most már használhatja jelentésekben és irányítópultokon [Power BIban](https://powerbi.microsoft.com).

![A képernyőképen egy Power BI adatkészletből készített jelentés látható.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Nincs adat?

* Győződjön meg arról, hogy a dátumformátum helyesen van megadva éééé-hh-nn [értékre](#set-path-prefix-pattern) (kötőjelekkel).

## <a name="next-steps"></a>Következő lépések
* [Folyamatos exportálás](export-telemetry.md)
* [Részletes adatmodell-referenciák a tulajdonságok típusaihoz és értékeihez.](export-data-model.md)
* [Application Insights](./app-insights-overview.md)
