---
title: 'Oktatóanyag: az Azure HDInsight Apache Spark-beli adatelemzések elemzése Power BI'
description: Oktatóanyag – a Microsoft Power BI használata a tárolt HDInsight-fürtök megjelenítéséhez Apache Spark
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 3e0632b2ad1ac237d8899e9d3bdc7f1d3350fa76
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057932"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Oktatóanyag: Apache Spark-adatelemzés a HDInsight Power BI használatával

Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg a Microsoft Power BIt az Azure HDInsight lévő Apache Spark-fürtökben lévő adatmegjelenítéshez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

* Végezze el az [Oktatóanyag: Adatok betöltése és lekérdezések futtatása az Azure HDInsight egy Apache Spark-fürtjén cikkben leírtakat](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Nem kötelező: [Power bi próbaverziós előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Az adatok ellenőrzése

Az [előző oktatóanyagban](apache-spark-load-data-run-query.md) létrehozott [Jupyter notebook](https://jupyter.org/) kódot tartalmaz egy tábla létrehozásához `hvac` . Ez a táblázat az összes HDInsight Spark-fürtön elérhető CSV-fájlon alapul `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` . Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

1. A Jupyter Notebook illessze be a következő kódot, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód segítségével a táblák meglétét ellenőrizhetjük.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A kimenet a következőképpen fog kinézni:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-show-tables.png" alt-text="Táblák megjelenítése a Sparkban" border="true":::

    Ha az oktatóanyag elindítása előtt már bezárta a notebookot, a `hvactemptable` törlődik, így az nem szerepel a kimenetben.  A BI-eszközökkel csak a metaadattárban tárolt Hive-táblákhoz (ezeket **Hamis** érték jelzi az **isTemporary** oszlopban) lehet hozzáférni. Ebben az oktatóanyagban kapcsolatot létesít az Ön által létrehozott **HVAC**-táblával.

2. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód elvégzi a tábla adatainak ellenőrzését.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A kimenet a következőképpen fog kinézni:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-select-limit.png" alt-text="A HVAC-tábla sorainak megjelenítése a Sparkban" border="true":::

3. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Állítsa le a notebookot az erőforrások felszabadításához.

## <a name="visualize-the-data"></a>Az adatok vizualizációja

Ebben a szakaszban a Power BI-t fogja használni a vizualizációk, jelentések és irányítópultok a Spark-fürt adataiból való létrehozásához.

### <a name="create-a-report-in-power-bi-desktop"></a>Jelentés készítése a Power BI Desktopban

A Spark használatba vételének első lépései a csatlakozás a fürthöz a Power BI Desktopban, az adatok betöltése a fürtből, és az ezen adatokon alapuló, alapszintű vizualizáció létrehozása.

1. Nyissa meg a Power BI Desktopot. Ha megnyílik, zárjuk be a Start-up Splash képernyőt.

2. A **Kezdőlap** lapon navigáljon az **adatok lekérése**  >  **továbbiak..**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png " alt-text="Adatok Beolvasása Power bi Desktop a HDInsight Apache Spark" border="true":::er = "true":::

3. `Spark`A keresőmezőbe írja be a **Azure HDInsight Spark**, majd válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png " alt-text="Adatok Beolvasása Power BI Apache Spark bi" border="true":::er = "true":::

4. Adja meg a fürt URL-címét (az űrlapon `mysparkcluster.azurehdinsight.net` ) a **kiszolgáló** szövegmezőben.

5. Az **adatkapcsolati mód** területen válassza a **DirectQuery** lehetőséget. Ez után válassza az **OK** gombot.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Adja meg a HDInsight bejelentkezési fiókjának adatait, majd válassza a **kapcsolat** lehetőséget. Az alapértelmezett fióknév az *admin*.

7. Válassza ki a `hvac` táblázatot, várjon, amíg megtekinti az adatelőnézetet, majd válassza a **Betöltés** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png " alt-text="Spark-fürt felhasználóneve és jelszava" border="true":::d "Border =" true ":::

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.

8. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén:

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget.

    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png " alt-text="érték oszlopok hozzáadása" border="true":::t = "érték oszlopok hozzáadása" Border = "true":::

        A diagram a következőképpen fog kinézni:

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png " alt-text="terület gráf Sum" border="true":::lt-text = "terület gráf összege" Border = "true":::

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Válassza ki a **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a vizualizációk ablaktáblán, amelyen a **Sum (összeg** ) lehetőség van kiválasztva.

    3. Válassza ki a **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a vizualizációk ablaktáblán, és válassza az **átlag** lehetőséget az egyes épületek tényleges és megcélzott hőmérsékletének lekéréséhez.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png " alt-text="értékek átlaga" border="true":::t = "az értékek átlaga" Border = "true":::

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png " alt-text="terület gráf" border="true":::. png "Alt-text =" terület gráf "Border =" true ":::

9. Navigáljon a **fájl**  >  **mentése** mezőbe, írja be a `BuildingTemperature` fájl nevét, majd kattintson a Mentés gombra.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI szolgáltatásban (opcionális)

A Power BI szolgáltatás segítségével jelentéseket és irányítópultokat oszthat meg a vállalaton belül. Ebben a szakaszban először az adatkészlet és a jelentés közzétételét végezzük el. Ezután rögzíteni fogjuk a jelentést egy irányítópulton. Az irányítópultok jellemzően a jelentésekben lévő adathalmazokra koncentrálnak. Csak egy vizualizáció szerepel a jelentésben, de továbbra is hasznos lehet a lépések végrehajtásához.

1. Nyissa meg a Power BI Desktopot.

1. A **Kezdőlap** lapon válassza a **Közzététel** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png " alt-text="Közzététel Power bi Desktop" border="true"::: Asztal "Border =" true ":::

1. Válasszon ki egy munkaterületet, és tegye közzé az adatkészletet, majd válassza a **kiválasztás** lehetőséget. A következő képen az alapértelmezett **Saját munkaterület** mező van kiválasztva.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png " alt-text="Válassza ki a munkaterületet, és tegye közzé az adatkészletet és jelentést az" border="true":::UE ":::

1. A közzététel sikeres végrehajtása után válassza a **Power bi BuildingTemperature. Pbix megnyitása** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png " alt-text="Sikeres közzététel, kattintson ide a hitelesítő adatok megadásához" border="true":::er = "true":::

1. A Power BI szolgáltatás válassza a **hitelesítő adatok megadása** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png " alt-text="Adja meg a hitelesítő adatokat Power bi szolgáltatás" border="true":::"Border =" true ":::

1. Válassza a **hitelesítő adatok szerkesztése** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png " alt-text="Hitelesítő adatok szerkesztése az Power bi szolgáltatás" border="true":::e "Border =" true ":::

1. Adja meg a HDInsight bejelentkezési fiókjának adatait, majd válassza a **Bejelentkezés** lehetőséget. Az alapértelmezett fióknév az *admin*.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png " alt-text="Bejelentkezés a Spark-fürtbe" border="true"::: Spark-fürt "Border =" true ":::

1. A bal oldali ablaktáblán válassza a **munkaterületek**  >  **saját munkaterület**-  >  **jelentések** elemet, majd kattintson a **BuildingTemperature** elemre.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png " alt-text="Jelentés a bal oldali ablaktábla" border="true":::Order = "true":::

    A **BuildingTemperature** elemnek a bal oldali ablaktábla **ADATKÉSZLETEK** területén is meg kell jelennie.

    A Power BI Desktopban létrehozott vizualizáció mostantól a Power BI szolgáltatásban is elérhető.

1. Vigye a kurzort a vizualizáció fölé, majd válassza a jobb felső sarokban található rögzítés ikont.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png " alt-text="Jelentés az Power bi szolgáltatás" border="true":::szolgáltatásban "Border =" true ":::

1. Válassza az új irányítópult lehetőséget, írja be a nevet `Building temperature` , majd válassza a **PIN-kód** lehetőséget.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png " alt-text="Rögzítés az új irányítópultra" border="true"::: "Border =" true ":::

1. A jelentésben válassza az **Ugrás az irányítópultra** lehetőséget.

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. A jelentésekkel és irányítópultokkal kapcsolatos további információkért tekintse meg a Power BI Power BI és [irányítópultok](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) [jelentései](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan jelenítheti meg a Microsoft Power BIt az Azure HDInsight lévő Apache Spark-fürtökben lévő információk megjelenítéséhez. A következő cikkből megtudhatja, hogyan hozhat létre gépi tanulási alkalmazást.

> [!div class="nextstepaction"]
> [Machine learning-alkalmazás létrehozása](./apache-spark-ipython-notebook-machine-learning.md)