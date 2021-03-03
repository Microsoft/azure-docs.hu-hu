---
title: Szinapszis Studio-jegyzetfüzetek
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és fejleszthet Azure-beli szinapszis Studio-jegyzetfüzeteket az adat-előkészítés és a vizualizációk elvégzéséhez.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: d5ff3fb988a7e907308ccccc8d0900d45a0601c0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671593"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Szinapszis Studio-jegyzetfüzetek létrehozása, fejlesztése és karbantartása az Azure szinapszis Analyticsben

A szinapszis Studio notebook egy webes felület, amellyel élő kódot, vizualizációkat és narratív szöveget tartalmazó fájlokat hozhat létre. A jegyzetfüzetek jó kiindulópontot nyújtanak az ötletek érvényesítéséhez és a gyors kísérletekhez az adatokból származó elemzések megszerzéséhez. A jegyzetfüzeteket is széles körben használják az adatok előkészítésében, az adatvizualizációban, a gépi tanulásban és más Big-adatokban.

Az Azure szinapszis Studio notebook használatával a következőket teheti:

* Első lépések a nulla telepítési erőfeszítéssel.
* A beépített nagyvállalati biztonsági funkciókkal megőrizheti az adatvédelmet.
* Elemezheti az adatokat nyers formátumokban (CSV, txt, JSON stb.), a feldolgozott fájlformátumokat (a parketta, a Delta Lake, az ork stb.) és az SQL táblázatos adatfájlokat a Spark és az SQL között.
* Legyen hatékony a fejlett szerzői képességek és beépített adatvizualizációk révén.

Ez a cikk bemutatja, hogyan használhatók a jegyzetfüzetek az Azure szinapszis Studióban.

## <a name="preview-of-the-new-notebook-experience"></a>Az új jegyzetfüzet-felület előzetes verziója
A szinapszis csapata az új jegyzetfüzetek összetevőt a szinapszis studióba hozta, hogy egységes notebook-élményt nyújtson a Microsoft ügyfeleinek, és maximalizálja a felderíthetőség, a termelékenység, a megosztás és az együttműködés terén. Az új jegyzetfüzet-élmény készen áll az előzetes verzióra. Ha be szeretné kapcsolni, tekintse meg az **előzetes verziójú funkciók** gombot a notebook eszközsoron. Az alábbi táblázat a meglévő jegyzetfüzetek (úgynevezett "klasszikus jegyzetfüzet") funkcióinak összehasonlítását rögzíti az új előzetes verzióval.  

|Szolgáltatás|Klasszikus jegyzetfüzet|Előnézeti jegyzetfüzet|
|--|--|--|
|% Run| Nem támogatott | &#9745;|
|% előzmények| Nem támogatott |&#9745;
|% betöltés| Nem támogatott |&#9745;|
|%% HTML| Nem támogatott |&#9745;|
|Egy cella mozgatása húzással| Nem támogatott |&#9745;|
|Állandó Display () kimenet|&#9745;| Nem elérhető |
|Összes megszakítása| &#9745;| Nem elérhető|
|Az összes fenti cella futtatása|&#9745;| Nem elérhető |
|Az összes alábbi cella futtatása|&#9745;| Nem elérhető |
|Szöveg cellájának formázása eszköztár gombjaival|&#9745;| Nem elérhető |
|Cella visszavonása művelet| &#9745;| Nem elérhető |


## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

Kétféleképpen hozhat létre jegyzetfüzetet. Létrehozhat egy új jegyzetfüzetet, vagy importálhat egy meglévő jegyzetfüzetet egy Azure-beli szinapszis-munkaterületre a **Object Explorerból**. Az Azure szinapszis Studio notebookok felismerik a standard Jupyter Notebook IPYNB-fájlokat.

![importálási jegyzetfüzet létrehozása](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Jegyzetfüzetek fejlesztése

A jegyzetfüzetek cellákból állnak, amelyek a kód vagy a szöveg különálló, egymástól függetlenül vagy csoportként futtatható blokkai.

### <a name="add-a-cell"></a>Cella hozzáadása

Több módon is hozzáadhat új cellákat a jegyzetfüzethez.

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

1. Bontsa ki a bal felső **+ cella** gombot, és válassza a **kódlap hozzáadása** vagy a **szöveg hozzáadása cellát**.

    ![cella hozzáadása a cellához – gomb](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Vigye a kurzort a két cella közötti területre, majd válassza a **kód hozzáadása** vagy a **szöveg hozzáadása** lehetőséget.

    ![cella – térköz hozzáadása](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [Billentyűparancsok használata a parancs módban](#shortcut-keys-under-command-mode). Az **a** billentyű lenyomásával szúrhat be egy cellát az aktuális cella fölé. A **B** billentyű lenyomásával szúrjon be egy cellát az aktuális cella alá.


# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

1. Bontsa ki a bal felső **+ cella** gombot, és válassza a **kód cellája** vagy a **Markdown cellát**.
    ![Hozzáadás – Azure-jegyzetfüzet – cella – cella gomb](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Válassza ki a cella elején található pluszjelet, és válassza a **kód cellája** vagy a **Markdown cella** lehetőséget.

    ![Azure-notebook-Cell-szóköz hozzáadása](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. [A Aznb billentyűparancsokat a parancs módban](#shortcut-keys-under-command-mode)használhatja. Az **a** billentyű lenyomásával szúrhat be egy cellát az aktuális cella fölé. A **B** billentyű lenyomásával szúrjon be egy cellát az aktuális cella alá.

---

### <a name="set-a-primary-language"></a>Elsődleges nyelv beállítása

Az Azure szinapszis Studio notebookok négy Apache Spark nyelvet támogatnak:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET Apache Sparkhoz (C#)

Az új hozzáadott cellák elsődleges nyelvét a felső parancssáv legördülő listájából állíthatja be.

   ![alapértelmezett – szinapszis – nyelv](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Több nyelv használata

Több nyelvet is használhat egy jegyzetfüzetben a cella elején található megfelelő nyelvi mágikus parancs megadásával. A következő táblázat a cella nyelveinek váltására szolgáló mágikus parancsokat sorolja fel.

|Magic parancs |Nyelv | Leírás |  
|---|------|-----|
|%% pyspark| Python | **Python** -lekérdezés végrehajtása a Spark-környezettel.  |
|%% Spark| Scala | **Scala** -lekérdezés végrehajtása a Spark-környezettel.  |  
|%% SQL| SparkSQL | **SparkSQL** -lekérdezés végrehajtása a Spark-környezettel.  |
|%% csharp | .NET a Spark C-hez # | .Net- **keretrendszer** végrehajtása Spark C#-lekérdezéshez Spark-környezettel. |

Az alábbi ábrán egy példa látható arra, hogyan írhat PySpark-lekérdezést a ( **z)%% PySpark** Magic paranccsal vagy egy SparkSQL-lekérdezéssel a (z)% **% SQL** Magic paranccsal a **Spark (Scala)** jegyzetfüzetben. Figyelje meg, hogy a jegyzetfüzet elsődleges nyelve a pySpark értékre van állítva.

   ![Szinapszis Spark Magic-parancsok](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Az adatnyelvek közötti hivatkozáshoz használjon ideiglenes táblázatokat

Az adat-és változók nem hivatkozhatnak közvetlenül különböző nyelveken a szinapszis Studio jegyzetfüzetben. A Sparkban egy ideiglenes tábla is szerepelhet a nyelvek között. Íme egy példa arra, hogyan lehet beolvasni egy `Scala` DataFrame a `PySpark` és a `SparkSQL` Spark Temp-tábla használatával megkerülő megoldásként.

1. Az 1. cellában olvassa el a DataFrame egy SQL Pool-összekötőről a Scala használatával, és hozzon létre egy ideiglenes táblát.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. A 2. cellában a Spark SQL használatával kérdezheti le az adatgyűjtést.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. A 3. cellában használja a PySpark-ben tárolt adatfájlokat.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-Style IntelliSense

Az Azure szinapszis Studio notebookok integrálva vannak a Monaco-szerkesztővel, hogy IDE-stílusú IntelliSense kerüljön a cellás szerkesztőbe. A szintaxis kiemelése, a hiba jelölője és az automatikus kód befejezése segít a kód írásához és a problémák gyorsabb azonosításához.

Az IntelliSense-funkciók a különböző nyelveken a lejárat különböző szintjein vannak. A következő táblázat segítségével megtekintheti a támogatott műveleteket.

|Nyelvek| Szintaxis kiemelése | Szintaktikai hiba jelölője  | Szintaxis kód befejezése | Változó kód befejezése| Rendszerfunkció kódja – Befejezés| Felhasználói függvény kódjának befejezése| Intelligens behúzás | Kód összecsukható|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Igen|
|Spark (Scala)|Igen|Igen|Igen|Igen|-|-|-|Igen|
|SparkSQL|Igen|Igen|-|-|-|-|-|-|
|.NET for Spark (C#)|Igen|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Szöveg cellájának formázása eszköztár gombjaival

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Az általános Markdown műveletek végrehajtásához használhatja a Text Cells eszköztár Format (formázás) gombját. Félkövér szöveggel, italicizing szöveggel, kódrészletek beszúrásával, Rendezetlen lista beszúrásával, rendezett lista beszúrásával és a rendszerkép URL-címének beszúrásával is magában foglalja.

  ![Szinapszis szöveg cellájának eszköztára](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

A Format gomb eszköztára még nem érhető el az előzetes verziójú jegyzetfüzethez. 

---

### <a name="undo-cell-operations"></a>Cella műveleteinek visszavonása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Kattintson a **Visszavonás** gombra, vagy nyomja le a **CTRL + Z** billentyűkombinációt a legutóbbi cella művelet visszavonásához. Most visszavonhatja a legutóbbi 20 korábbi cella műveleteit. 

   ![Szinapszis-visszavonási cellák](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

A cella visszavonása művelet még nem érhető el az előzetes verziójú jegyzetfüzetek esetében. 

---

### <a name="move-a-cell"></a>Cella áthelyezése

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Kattintson a három pontra (...), és nyissa meg a jobb szélen a további cella műveletek menüt. Ezután válassza a **cella áthelyezése felfelé** vagy a **cella lejjebb** lehetőséget az aktuális cella áthelyezéséhez. 

[A billentyűparancsokat a parancs módban](#shortcut-keys-under-command-mode)is használhatja. Nyomja le a **CTRL + ALT + ↑** billentyűkombinációt az aktuális cella mozgatásához. Nyomja le a **CTRL + ALT + ↓** billentyűkombinációt az aktuális cella lefelé való áthelyezéséhez.

   ![cella áthelyezése](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

Kattintson a cella bal oldali oldalára, és húzza a kívánt helyre. 
    ![Szinapszis cellák mozgatása](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Cella törlése

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Ha törölni szeretne egy cellát, válassza a három pontot (...), majd a jobb szélen kattintson a **cella törlése** lehetőségre. 

[A billentyűparancsokat a parancs módban](#shortcut-keys-under-command-mode)is használhatja. Az aktuális cella törléséhez nyomja le a **d, d** billentyűt.
  
   ![cella törlése](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

Cella törléséhez kattintson a cella jobb oldalán található Törlés gombra. 

[A billentyűparancsokat a parancs módban](#shortcut-keys-under-command-mode)is használhatja. Az aktuális cella törléséhez nyomja le a **SHIFT + D** billentyűkombinációt. 

   ![Azure-notebook – cella törlése](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Cella bemenetének összecsukása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Az aktuális cella alján található nyíl gombra kattintva összecsukhatja azt. A kibontásához kattintson a nyíl gombra a cella összecsukása közben.

   ![összecsukás – cella bemenet](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

Az aktuális cella bemenetének összecsukásához válassza a cella eszköztárán a **további parancsok** ellipszisek (...) lehetőséget, és adja meg a **bemenetet** . A kibontásához válassza ki a **rejtett bemenetet** , amíg a cella össze van csukva.

   ![Azure-jegyzetfüzet-összecsukás – cella bemenet](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Cella kimenetének összecsukása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Válassza a **kimenet összecsukása** gombot az aktuális cella kimenetének bal felső részén. A kibontásához jelölje be a **cella kimenetének megjelenítése** a cella kimenetének összecsukása közben.

   ![összeomlás – cella-kimenet](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

Az aktuális cella kimenetének összecsukásához válassza a cella eszköztárán a **további parancsok** ellipszisek (...) lehetőséget, majd a **kimenetet** . A kibontásához jelölje ki ugyanazt a gombot, amíg a cella kimenete rejtett.

   ![Azure-jegyzetfüzet-összecsukás – cella kimenet](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Jegyzetfüzetek futtatása

A kódok celláit egyenként vagy egyszerre is futtathatja a jegyzetfüzetben. Az egyes cellák állapota és előrehaladása a jegyzetfüzetben jelenik meg.

### <a name="run-a-cell"></a>Cella futtatása

A kód több módon is futtatható a cellában.

1. Vigye a kurzort a futtatni kívánt cellára, és válassza a **cella futtatása** gombot, vagy nyomja le a **CTRL + ENTER** billentyűkombinációt.

   ![Futtatás – 1. cella](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [Billentyűparancsok használata a parancs módban](#shortcut-keys-under-command-mode). Nyomja le a **SHIFT + ENTER** billentyűkombinációt az aktuális cella futtatásához, és válassza ki az alábbi cellát. Nyomja le az **ALT + ENTER** billentyűkombinációt az aktuális cella futtatásához és az alábbi új cella beszúrásához.

---

### <a name="run-all-cells"></a>Az összes cella futtatása
Az **összes futtatása** gomb kiválasztásával az aktuális jegyzetfüzet összes celláját sorba kell futtatni.

   ![Futtatás – összes cella](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>Az összes fenti vagy alatti cella futtatása

A jobb szélen található további cellahivatkozások menü eléréséhez válassza a három pontot (**..**.). Ezután válassza a **fenti cellák futtatása** lehetőséget, hogy az összes cellát az aktuálisan futó sorban futtassa. Válassza az **alábbi cellák futtatása** lehetőséget az összes cella az aktuális szakasz alatti futtatásához.

   ![futtatási cellák – fent vagy lent](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Az összes futó cella megszakítása
Az **összes megszakítása** gomb megnyomásával szakíthatja meg a várólistán várakozó futó cellákat vagy cellákat. 
   ![összes cella megszakítása](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

Az összes futó cella megszakítása még nem érhető el az előzetes verziójú jegyzetfüzetek esetében. 

---



### <a name="reference-notebook"></a>Hivatkozás notebook

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Nem támogatott.

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

```%run <notebook path>```A Magic paranccsal hivatkozhat egy másik jegyzetfüzetre az aktuális jegyzetfüzet környezetében. A hivatkozási jegyzetfüzetben definiált összes változó elérhető az aktuális jegyzetfüzetben. ```%run``` a Magic parancs támogatja a beágyazott hívásokat, de nem támogatja a rekurzív hívásokat. Kivételt fog kapni, ha az utasítás mélysége ötnél nagyobb. ```%run``` a parancs jelenleg csak a támogatja a jegyzetfüzet elérési útjának paraméterként való továbbítását. 

Példa: ``` %run /path/notebookA ```.

---


### <a name="cell-status-indicator"></a>Cella állapotának jelzője

Egy lépésenkénti cella-végrehajtási állapot jelenik meg a cella alatt, hogy megtekintse a jelenlegi folyamatát. A cella futásának befejezése után a rendszer megjeleníti a végrehajtás összegzését a teljes időtartammal és a befejezési időponttal, és a későbbiekben is megőrzi őket.

![cella állapota](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark folyamatjelzője

Az Azure szinapszis Studio notebook kizárólag Spark-alapú. A kód cellái a kiszolgáló nélküli Apache Spark készleten futnak távolról. A Spark-feladatok folyamatjelzője egy valós idejű folyamatjelzővel jelenik meg, amely a feladatok végrehajtási állapotának megértéséhez nyújt segítséget.
Az egyes feladatokhoz vagy fázisokhoz tartozó feladatok száma segít a Spark-feladat párhuzamos szintjének azonosításában. Az adott feladathoz (vagy fázishoz) tartozó Spark felhasználói felületen mélyebben is megtekintheti a feladatot (vagy fázis).


![Spark – folyamat – kijelző](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-munkamenet konfigurációja

Megadhatja az időtúllépés időtartamát, a számot és a végrehajtók számát, hogy az aktuális Spark-munkamenetet adja meg a **konfigurálási munkamenetben**. Indítsa újra a Spark-munkamenetet a konfigurációs módosítások érvénybe léptetéséhez. Az összes gyorsítótárazott jegyzetfüzet-változó törlődik.

[![munkamenet-kezelés](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>A Spark-munkamenet konfigurációjának Magic parancsa
A Spark-munkamenet beállításait a **(z)%% configure** paranccsal is megadhatja. A Spark-munkamenetet újra kell indítani, hogy a beállítások érvénybe lépnek. Javasoljuk, hogy a (z) **%% configure** a jegyzetfüzet elején fusson. Itt látható egy minta, amely az https://github.com/cloudera/livy#request-body érvényes paraméterek teljes listájára vonatkozik. 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```


## <a name="bring-data-to-a-notebook"></a>Adat beolvasása egy jegyzetfüzetbe

Adatok tölthetők be az Azure Blob Storageból, Azure Data Lake Store Gen 2 és az SQL-készletből, ahogy az alábbi kódban is látható.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>CSV-fájl olvasása Azure Data Lake Store Gen2 Spark-DataFrame

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>CSV-fájl olvasása az Azure Blob Storageból Spark-DataFrame

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Adatok beolvasása az elsődleges Storage-fiókból

Az elsődleges Storage-fiókban lévő adatelérést közvetlenül is elérheti. Nincs szükség a titkos kulcsok megadására. A Adatkezelőban kattintson a jobb gombbal egy fájlra, és válassza az **új jegyzetfüzet** lehetőséget, hogy megjelenjen egy új, automatikusan generált kivonó jegyzetfüzet.

![adatcella](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Jegyzetfüzetek mentése

A munkaterületen egyetlen jegyzetfüzet vagy az összes jegyzetfüzet is menthető.

1. Egyetlen jegyzetfüzeten végrehajtott módosítások mentéséhez kattintson a **Közzététel** gombra a jegyzetfüzet parancssáv sávján.

   ![közzététel – jegyzetfüzet](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Ha a munkaterületen lévő összes jegyzetfüzetet menteni szeretné, válassza az **összes közzététele** gombot a munkaterület parancssáv területén. 

   ![közzététel – mind](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

A jegyzetfüzet tulajdonságainál beállíthatja, hogy a cella kimenete megjelenjen-e a mentéskor.

   ![Jegyzetfüzet – tulajdonságok](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Mágikus parancsok
Az Azure szinapszis Studio notebookjában ismerős Jupyter Magic-parancsokat is használhat. Tekintse át az alábbi listát az aktuálisan elérhető Magic-parancsokkal. Ismertesse [a githubon a használati eseteit](https://github.com/MicrosoftDocs/azure-docs/issues/new) , hogy továbbra is felépíthetjük az igényeinek megfelelő Magic-parancsokat.

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Elérhető sorok varázsa: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time),% [timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Elérhető cella-varázslatok: [%% idő](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages),[%% configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

Elérhető sorok varázsa: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [% Time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time),% [timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% History](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% Run](#reference-notebook),% [Load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Elérhető cella-varázslatok: [%% idő](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% Capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% WriteFile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% SQL](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% Spark](#use-multiple-languages), [%% csharp](#use-multiple-languages), [%% HTML](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Jegyzetfüzet integrálása

### <a name="add-a-notebook-to-a-pipeline"></a>Jegyzetfüzet hozzáadása egy folyamathoz

A jobb felső sarokban található **Hozzáadás a folyamathoz** gombra kattintva hozzáadhat egy jegyzetfüzetet egy meglévő folyamathoz, vagy létrehozhat egy új folyamatot.

![Jegyzetfüzet hozzáadása a folyamathoz](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Paraméterek cellájának kijelölése

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

A jegyzetfüzet parametrizálja kattintson a három pontra (...), és nyissa meg a jobb szélen a további cella műveletek menüt. Ezután válassza a **paraméter cellájának váltása** lehetőséget a cella paraméterként való kijelöléséhez.

![váltás – paraméter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

A parametrizáljaéhez válassza a három pontot (...) a cella eszköztárán található **további parancsok** eléréséhez. Ezután válassza a **paraméter cellájának váltása** lehetőséget a cella paraméterként való kijelöléséhez.

![Azure-jegyzetfüzet – váltógomb – paraméter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory megkeresi a parameters (paraméterek) cellát, és ezt a cellát a végrehajtási időpontban átadott paraméterek alapértelmezett értékeiként kezeli. A végrehajtó motor egy új cellát fog hozzáadni a parameters (paraméterek) cella alatt a bemeneti paraméterekkel, hogy felülírja az alapértelmezett értékeket. Ha nincs kijelölve Parameters cella, a rendszer beszúrja az injektált cellát a jegyzetfüzet elejére.


### <a name="assign-parameters-values-from-a-pipeline"></a>Paraméterek értékének kiosztása egy folyamatból

Miután létrehozott egy jegyzetfüzetet a paraméterekkel, végrehajthatja azt egy olyan folyamatból, amely az Azure szinapszis notebook-tevékenységét futtatja. Miután hozzáadta a tevékenységet a csővezeték-vászonhoz, a **Beállítások** lap **alapparaméterek** szakaszában állíthatja be a paraméterek értékét. 

![Paraméter kiosztása](./media/apache-spark-development-using-notebooks/assign-parameter.png)

A paraméterek értékének kiosztásakor használhatja a [folyamat kifejezésének nyelvét](../../data-factory/control-flow-expression-language-functions.md) vagy a [rendszerváltozókat](../../data-factory/control-flow-system-variables.md).



## <a name="shortcut-keys"></a>Billentyűparancsok

A Jupyter-jegyzetfüzetekhez hasonlóan az Azure szinapszis Studio notebookok modális felhasználói felülettel rendelkeznek. A billentyűzet különböző műveleteket végez, attól függően, hogy a jegyzetfüzet-cella milyen módban van. A szinapszis Studio notebookok az alábbi két módot támogatják egy adott kódú cellához: parancs mód és szerkesztési mód.

1. A cella parancs módban van, ha nincs beírni kívánt szöveg. Ha egy cella parancs módban van, a jegyzetfüzetet teljes egészében szerkesztheti, de nem lehet egyéni cellákba írni. A parancs mód megadásához nyomja le `ESC` vagy az egér használatával válassza ki a cella szerkesztő területén kívüli lehetőséget.

   ![parancssori üzemmód](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. A szerkesztési módot egy szöveges kurzor jelzi, amely arra kéri, hogy írja be a szerkesztőt. Ha egy cella szerkesztési módban van, beírhatja a cellába. Adja meg a szerkesztési módot úgy, hogy lenyomja `Enter` vagy felhasználja az egeret a cella szerkesztői területein való kiválasztáshoz.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>A parancssori üzemmód alatti billentyűparancsok

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot az Azure szinapszis jegyzetfüzetekben.

| Művelet |A szinapszis Studio notebook parancsikonjai  |
|--|--|
|Futtassa az aktuális cellát, és válassza az alábbi lehetőséget. | SHIFT + ENTER |
|Az aktuális cella futtatása és az alábbi beszúrása | ALT + ENTER |
|Cella kijelölése| Fel |
|Válassza ki az alábbi cellát| Le |
|Cella beszúrása a fenti fölé| A |
|Cella beszúrása lent| B |
|A fent kijelölt cellák kiterjesztése| SHIFT + fel |
|A kiválasztott cellák kiterjesztése lejjebb| SHIFT + le|
|Cella mozgatása felfelé| CTRL + ALT + ↑ |
|Cella mozgatása lefelé| CTRL + ALT + ↓ |
|Kijelölt cellák törlése| D, D |
|Váltás szerkesztési módra| Enter |

# <a name="preview-notebook"></a>[Előnézeti jegyzetfüzet](#tab/preview)

| Művelet |A szinapszis Studio notebook parancsikonjai  |
|--|--|
|Futtassa az aktuális cellát, és válassza az alábbi lehetőséget. | SHIFT + ENTER |
|Az aktuális cella futtatása és az alábbi beszúrása | ALT + ENTER |
|Aktuális cella futtatása| Ctrl+Enter |
|Cella kijelölése| Fel |
|Válassza ki az alábbi cellát| Le |
|Előző cella kijelölése| K |
|Következő cella kiválasztása| J |
|Cella beszúrása a fenti fölé| A |
|Cella beszúrása lent| B |
|Kijelölt cellák törlése| SHIFT + D |
|Váltás szerkesztési módra| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Szerkesztési mód alatti billentyűparancsok


A következő billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot az Azure szinapszis-jegyzetfüzetekben szerkesztési módban.

| Művelet |A szinapszis Studio notebook parancsikonjai  |
|--|--|
|Kurzor mozgatása felfelé | Fel |
|Kurzor mozgatása lefelé|Le|
|Visszavonás|CTRL + Z|
|Ismétlés|CTRL + Y|
|Megjegyzés/Megjegyzés|CTRL +/|
|A Word törlése előtt|Ctrl + Backspace|
|Szó törlése a következő után|CTRL + DELETE|
|Ugrás a cella elejére|CTRL + Kezdőlap|
|Ugrás a cella végére |Ctrl + End|
|Ugrás egy szóval balra|CTRL + balra|
|Egy szó jobbra|Ctrl + jobb|
|Az összes kijelölése|CTRL + A|
|Behúzás| Ctrl +]|
|Kihúzás|CTRL + [|
|Váltás parancs módba| Esc |

---

## <a name="next-steps"></a>Következő lépések
- [Tekintse meg a szinapszis-minta jegyzetfüzeteket](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Rövid útmutató: Apache Spark-készlet létrehozása az Azure szinapszis Analyticsben webes eszközök használatával](../quickstart-apache-spark-notebook.md)
- [Az Azure szinapszis Analytics Apache Spark](apache-spark-overview.md)
- [Az Apache Sparkhoz készült .NET használata az Azure Stream Analyticsszel](spark-dotnet.md)
- [.NET Apache Spark dokumentációhoz](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)