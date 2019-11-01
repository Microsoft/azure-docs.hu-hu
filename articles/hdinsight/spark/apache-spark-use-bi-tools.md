---
title: 'Oktatóanyag: az Azure HDInsight Apache Spark-beli adatelemzések elemzése Power BI'
description: Oktatóanyag – a Microsoft Power BI használata a tárolt HDInsight-fürtök megjelenítéséhez Apache Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: c2d6a5acba304d7421b000cab2ee5cee5b85e5ce
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241353"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Oktatóanyag: Apache Spark-adatelemzés a HDInsight Power BI használatával

Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg a [Microsoft Power BIt](https://powerbi.microsoft.com/) az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)lévő Apache Spark-fürtökben lévő adatmegjelenítéshez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* A cikkből megtudhatja [, hogyan tölthet be és futtathat lekérdezéseket egy Apache Spark-fürtön az Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power bi Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Nem kötelező: [Power bi próbaverziós előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Az adatok ellenőrzése

Az [előző oktatóanyagban](apache-spark-load-data-run-query.md) létrehozott [Jupyter notebook](https://jupyter.org/) kódot tartalmaz egy `hvac` tábla létrehozásához. Ez a táblázat a `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`összes HDInsight Spark-fürtön elérhető CSV-fájlon alapul. Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

1. Illessze be a Jupyter notebookból a következő kódot, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód segítségével a táblák meglétét ellenőrizhetjük.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A kimenet a következőképpen fog kinézni:

    ![Táblák megjelenítése a Sparkban](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Ha az oktatóanyag elindítása előtt már bezárta a notebookot, a `hvactemptable` törlődik, így az nem szerepel a kimenetben.  A BI-eszközökkel csak a metaadattárban tárolt Hive-táblákhoz (ezeket **Hamis** érték jelzi az **isTemporary** oszlopban) lehet hozzáférni. Ebben az oktatóanyagban kapcsolatot létesít az Ön által létrehozott **HVAC**-táblával.

2. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód elvégzi a tábla adatainak ellenőrzését.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A kimenet a következőképpen fog kinézni:

    ![A HVAC-tábla sorainak megjelenítése a Sparkban](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Állítsa le a notebookot az erőforrások felszabadításához.

## <a name="visualize-the-data"></a>Az adatok vizualizációja

Ebben a szakaszban a Power BI-t fogja használni a vizualizációk, jelentések és irányítópultok a Spark-fürt adataiból való létrehozásához.

### <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban

A Spark használatba vételének első lépései a csatlakozás a fürthöz a Power BI Desktopban, az adatok betöltése a fürtből, és az ezen adatokon alapuló, alapszintű vizualizáció létrehozása.

> [!NOTE]  
> A jelen cikkben bemutatott összekötő jelenleg előzetes verzióban érhető el. Visszajelzéseit a [Power BI-közösség](https://community.powerbi.com/) webhelyén vagy a [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) oldalon keresztül küldheti el.

1. Nyissa meg a Power BI Desktopot. Ha megnyílik, zárjuk be a Start-up Splash képernyőt.

2. A **Kezdőlap** lapon navigáljon az **adatok lekérése** > **továbbiak..** .

    ![Adatok beolvasása Power BI Desktop a HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Adatok beolvasása Power BI Apache Spark BI-ból")

3. Adja meg `Spark` a keresőmezőbe, válassza a **Azure HDInsight Spark**lehetőséget, majd válassza a **kapcsolat**lehetőséget.

    ![Adatok beolvasása Power BI Apache Spark BI-ból](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Adatok beolvasása Power BI Apache Spark BI-ból")

4. Adja meg a fürt URL-címét (az űrlap `mysparkcluster.azurehdinsight.net`) a **kiszolgáló** szövegmezőben.

5. Az **adatkapcsolati mód**területen válassza a **DirectQuery**lehetőséget. Ezután kattintson az **OK** gombra.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Adja meg a HDInsight bejelentkezési fiókjának adatait, majd válassza a **kapcsolat**lehetőséget. Az alapértelmezett fióknév az *admin*.

7. Válassza ki a `hvac` táblázatot, várjon, amíg megtekinti az adatelőnézetet, majd válassza a **Betöltés**lehetőséget.

    ![Spark-fürt felhasználóneve és jelszava](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-fürt felhasználóneve és jelszava")

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.

8. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén:

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget.

    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        ![érték oszlopok hozzáadása](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "érték oszlopok hozzáadása")

        A diagram a következőképpen fog kinézni:

        ![körzeti gráf összege](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "körzeti gráf összege")

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Válassza ki a **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a vizualizációk ablaktáblán, amelyen a **Sum (összeg** ) lehetőség van kiválasztva.

    3. Válassza ki a **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a vizualizációk ablaktáblán, és válassza az **átlag** lehetőséget az egyes épületek tényleges és megcélzott hőmérsékletének lekéréséhez.

        ![értékek átlaga](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "értékek átlaga")

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        ![körzeti gráf](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "körzeti gráf")

9. Lépjen a **fájl** > **Mentés**elemre, írja be a fájl nevét `BuildingTemperature`, majd kattintson a **Save (Mentés**) gombra.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI szolgáltatásban (opcionális)

A Power BI szolgáltatás segítségével jelentéseket és irányítópultokat oszthat meg a vállalaton belül. Ebben a szakaszban először az adatkészlet és a jelentés közzétételét végezzük el. Ezután rögzíteni fogjuk a jelentést egy irányítópulton. Az irányítópultok jellemzően a jelentésekben lévő adathalmazokra koncentrálnak. Csak egy vizualizáció szerepel a jelentésben, de továbbra is hasznos lehet a lépések végrehajtásához.

1. Nyissa meg a Power BI Desktopot.

1. A **Kezdőlapon** kattintson a **Közzététel** gombra.

    ![Közzététel a Power BI Desktopból](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Közzététel a Power BI Desktopból")

1. Válasszon ki egy munkaterületet az adatkészlet és a jelentés közzétételéhez, majd kattintson a **Kiválasztás** gombra. A következő képen az alapértelmezett **Saját munkaterület** mező van kiválasztva.

    ![Válassza ki a munkaterületet az adatkészlet és a jelentés közzétételéhez](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Válassza ki a munkaterületet az adatkészlet és a jelentés közzétételéhez") 

1. Ha a közzététel sikeresen megtörtént, kattintson a **„BuildingTemperature.pbix” fájl megnyitása a Power BI-ban** elemre.

    ![Sikeres közzététel, kattintson ide a hitelesítő adatok megadásához](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Sikeres közzététel, kattintson ide a hitelesítő adatok megadásához") 

1. A Power BI szolgáltatás felületén kattintson a **Hitelesítő adatok megadása** elemre.

    ![Hitelesítő adatok megadása Power BI szolgáltatás](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Hitelesítő adatok megadása Power BI szolgáltatás")

1. Kattintson a **Hitelesítő adatok szerkesztése** gombra.

    ![Hitelesítő adatok szerkesztése a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Hitelesítő adatok szerkesztése a Power BI szolgáltatásban")

1. Adja meg a HDInsight bejelentkezési fiókadatait, majd kattintson a **Bejelentkezés** gombra. Az alapértelmezett fióknév az *admin*.

    ![Bejelentkezés a Spark-fürtbe](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Bejelentkezés a Spark-fürtbe")

1. A bal oldali panelen lépjen a **Munkaterületek** > **Saját munkaterület** > **JELENTÉSEK** részre, majd kattintson a **BuildingTemperature** elemre.

    ![Jelentés a bal oldali ablaktábla jelentések területén](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Jelentés a bal oldali ablaktábla jelentések területén")

    A **BuildingTemperature** elemnek a bal oldali ablaktábla **ADATKÉSZLETEK** területén is meg kell jelennie.

    A Power BI Desktopban létrehozott vizualizáció mostantól a Power BI szolgáltatásban is elérhető. 

1. Vigye az egérmutatót a vizualizáció fölé, majd kattintson a jobb felső sarokban lévő rögzítés ikonra.

    ![Jelentés a Power BI szolgáltatás](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Jelentés a Power BI szolgáltatás")

1. Válassza az „Új irányítópult” lehetőséget, adja meg a `Building temperature` nevet, majd kattintson a **Rögzítés** gombra.

    ![Rögzítés új irányítópultra](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Rögzítés új irányítópultra")

1. A jelentésben kattintson az **Ugrás az irányítópultra** gombra.

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. A jelentésekkel és irányítópultokkal kapcsolatos további információkért tekintse meg a Power BI Power BI és [irányítópultok](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) [jelentései](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, a gazdasági érzékek törlik a fürtöket, ha nincsenek használatban.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan jelenítheti meg a [Microsoft Power BIt](https://powerbi.microsoft.com/) az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)lévő Apache Spark-fürtökben lévő információk megjelenítéséhez. A következő cikkben megismerkedhet a Sparkban regisztrált adatok olyan BI-elemzési eszközökbe történő áthúzásának lehetőségével, mint például a Power BI.

> [!div class="nextstepaction"]
> [Apache Spark streaming-feladatok futtatása](apache-spark-eventhub-streaming.md)