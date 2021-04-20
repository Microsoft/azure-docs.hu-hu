---
title: Synapse Studio notebookok
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és fejleszthet Azure Synapse Studio-jegyzetfüzeteket az adatok előkészítéséhez és vizualizációhoz.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6859a2f8571c11e6ef93a5e5b1635cdbe39ad001
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737664"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Jegyzetfüzetek létrehozása, fejlesztése és Synapse Studio a Azure Synapse Analytics

A Synapse Studio jegyzetfüzet egy webes felület, amely élő kódot, vizualizációkat és narratív szöveget tartalmazó fájlok létrehozására használható. A jegyzetfüzetekkel remek ötleteket lehet érvényesíteni, és gyors kísérletekkel elemzéseket lehet kihozni az adatokból. A jegyzetfüzeteket széles körben használják az adatok előkészítéséhez, az adatvizualizációhoz, a gépi tanuláshoz és más big data-forgatókönyvekhez.

A Azure Synapse Studio-jegyzetfüzetekkel a következőt használhatja:

* Az első lépésekhez nincs szükség beállításra.
* A beépített vállalati biztonsági funkciókkal biztonságban tarthatja az adatokat.
* Elemezheti az adatokat nyers formátumokban (CSV, txt, JSON stb.), feldolgozott fájlformátumokban (parquet, Delta Lake, ORC stb.) és SQL táblázatos adatfájlokban a Spark és az SQL használatával.
* A továbbfejlesztett szerzői képességek és a beépített adatvizualizáció segítségével hatékonyan dolgozhat.

Ez a cikk a notebookok használatát ismerteti a Azure Synapse Studióban.

## <a name="preview-of-the-new-notebook-experience"></a>Az új jegyzetfüzet-felhasználói élmény előzetes verziója
A Synapse csapata a notebookok új összetevőjét Synapse Studio, hogy egységes jegyzetfüzet-élményt nyújtson a Microsoft ügyfeleinek, és maximalizálja a felderíthetőséget, a hatékonyságot, a megosztást és az együttműködést. Az új jegyzetfüzet-felhasználói élmény készen áll az előzetes verzióra. A **bekapcsoláshoz jelölje** be az Előzetes verziójú funkciók gombot a jegyzetfüzet eszköztárán. Az alábbi táblázat a meglévő jegyzetfüzetek (más néven "klasszikus jegyzetfüzet") és az új előzetes verzió közötti funkció-összehasonlítást rögzíti.  

|Szolgáltatás|Klasszikus jegyzetfüzet|Előzetes verziójú jegyzetfüzet|
|--|--|--|
|%run| Nem támogatott | &#9745;|
|%history| Nem támogatott |&#9745;
|%load (%load)| Nem támogatott |&#9745;|
|<a0/3/2| Nem támogatott |&#9745;|
|Cella áthelyezése húzással| Nem támogatott |&#9745;|
|Szövegcella formázása eszköztárgombokkal|&#9745;| Nem elérhető |
|Cella visszavonása művelet| &#9745;| Nem elérhető |


## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

Jegyzetfüzetet kétféleképpen hozhat létre. Létrehozhat egy új jegyzetfüzetet, vagy importálhat egy meglévő jegyzetfüzetet egy Azure Synapse munkaterületre a **Object Explorer.** Azure Synapse Studio-jegyzetfüzetek felismerik az IPYNB Jupyter Notebook szabványos fájlokat.

![importálási jegyzetfüzet létrehozása](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Jegyzetfüzetek fejlesztése

A jegyzetfüzetek cellákból állnak, amelyek különálló kód- vagy szövegblokkok, amelyek egymástól függetlenül vagy csoportként is futtathatóak.

### <a name="add-a-cell"></a>Cella hozzáadása

Új cellát többféleképpen is hozzáadhat a jegyzetfüzethez.

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

1. Bontsa ki a bal felső **+ Cella** gombot, és válassza a **Kódcella** hozzáadása vagy **a Szövegcella hozzáadása lehetőséget.**

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Vigye a kurzort a két cella közötti tér fölé, és válassza a Kód **hozzáadása vagy** a Szöveg **hozzáadása lehetőséget.**

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. A [Parancs módban használjon Billentyűparancsokat.](#shortcut-keys-under-command-mode) Nyomja **le az A** billentyűt egy cella beszúrása az aktuális cella fölé. Nyomja **le a B** billentyűt egy cella beszúrása az aktuális cella alá.


# <a name="preview-notebook"></a>[Előzetes verziójú jegyzetfüzet](#tab/preview)

1. Bontsa ki a bal felső **+ Cella** gombot, és válassza ki a **kódcellát** vagy a **Markdown-cellát.**
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Válassza a cella elején lévő pluszjelet, majd a **Kódcella** vagy a **Markdown-cella lehetőséget.**

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Parancs módban használja az [aznb-billentyűparancsokat.](#shortcut-keys-under-command-mode) Nyomja **le az A** billentyűt egy cella beszúrása az aktuális cella fölé. Nyomja **le a B** billentyűt egy cella beszúrása az aktuális cella alá.

---

### <a name="set-a-primary-language"></a>Elsődleges nyelv beállítása

Azure Synapse Studio-jegyzetfüzetek négy különböző nyelvet Apache Spark támogatnak:

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET for Apache Spark (C#)

Az új hozzáadott cellák elsődleges nyelvét a felső parancssáv legördülő listájából állíthatja be.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Több nyelv használata

Egy jegyzetfüzetben több nyelvet is használhat, ha a megfelelő nyelvi varázsparancsot adja meg egy cella elején. Az alábbi táblázat a cellanyelvek közötti váltáshoz használt magic parancsokat sorolja fel.

|Magic parancs |Nyelv | Description |  
|---|------|-----|
|<a0/3/300;pyspark| Python | **Python-lekérdezés végrehajtása** Spark-környezetben.  |
|<a0//3/-| Scala | Hajtson végre **egy Scala-lekérdezést** a Spark-környezetben.  |  
|©sql| SparkSQL | **SparkSQL-lekérdezés végrehajtása** a Spark-környezeten.  |
|<a0/3/*csharp<a1/ | .NET a Spark C-hez # | **.NET-lekérdezés végrehajtása Spark C#-lekérdezéshez** a Spark-környezetben. |

Az alábbi képen egy példa látható arra, hogyan írhat PySpark-lekérdezést a **spark(pyspark** magic) paranccsal vagy egy SparkSQL-lekérdezéssel a **Spark(Scala)** **jegyzetfüzetben** található .sql magic paranccsal. Figyelje meg, hogy a notebook elsődleges nyelve pySpark.

   ![Synapse Spark magic parancsok](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages&quot;></a>Ideiglenes táblák használata adatok különböző nyelveken való hivatkozására

Nem hivatkozhat adatokra vagy változókra közvetlenül különböző nyelveken egy Synapse Studio jegyzetfüzetben. A Sparkban az ideiglenes táblára több nyelven lehet hivatkozni. Az alábbi példa egy DataFrame beolvassát és egy Spark ideiglenes táblát használ `Scala` `PySpark` `SparkSQL` áthidaló megoldásként.

1. Az 1. cellában olvasson be egy DataFrame-et egy SQL-készlet összekötőből a Scala használatával, és hozzon létre egy ideiglenes táblát.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics(&quot;mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. A 2. cellában lekérdezheti az adatokat a Spark SQL használatával.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. A 3. cellában használja a PySparkban lévő adatokat.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-stílusú IntelliSense

Azure Synapse Studio-jegyzetfüzetek integrálva vannak a Monaco szerkesztővel, hogy IDE-stílusú IntelliSense-t vigyenek a cellaszerkesztőbe. A szintaxiskiemelő, a hibajelölő és az automatikus kódkiegegítések segítenek a kód írásában és a problémák gyorsabb azonosításában.

Az IntelliSense funkciói különböző szintű érettségi szinten vannak a különböző nyelveken. A támogatott lehetőségeket az alábbi táblázatban láthatja.

|Nyelvek| Szintaxiskiemelő | Szintaktikai hibajelölő  | Szintaxiskód-kiegészítés | Változókód-kiegészítés| Rendszerfunkció kód befejezése| Felhasználói függvény kód befejezése| Intelligens behúzás | Kódátcsatolás|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Igen|Igen|Igen|Igen|Igen|Igen|Igen|Yes|
|Spark (Scala)|Igen|Igen|Igen|Igen|-|-|-|Yes|
|SparkSQL|Igen|Yes|-|-|-|-|-|-|
|.NET for Spark (C#)|Yes|-|-|-|-|-|-|-|



### <a name="code-snippets"></a>Kódtöredékek

A Azure Synapse Studio-jegyzetfüzetek olyan kódrészleteket biztosítanak, amelyek megkönnyítik a gyakori kódminták begéplét, például a Spark-munkamenet konfigurálását, az adatok Spark DataFrame-ként való beolvasását vagy a matplotlib-diagramok rajzolását stb.

A kódrészletek az [IntelliSense-ban](#ide-style-intellisense) más javaslatokkal együtt jelennek meg. A kódrészletek tartalma igazodik a kódcella nyelvhez. Az elérhető kódrészletek a **Kódrészlet** beírásával jelennek meg, vagy bármely kulcsszó megjelenik a kódrészlet címében a kódcellaszerkesztőben. Ha például begépelte **az read** (olvasás) szöveget, láthatja a különböző adatforrások adatait beolvasó kódrészletek listáját.

![Synapse-kódrészletek](./media/apache-spark-development-using-notebooks/synapse-code-snippets.gif#lightbox)



### <a name="format-text-cell-with-toolbar-buttons"></a>Szövegcella formázása eszköztárgombokkal

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

A szövegcellák eszköztárában található formázási gombokkal gyakori Markdown-műveleteket is lehet tenni. Tartalmazza a félkövér formázást, a szöveg dőlt formázását, kódrészletek beszúrása, rendezetlen lista beszúrása, rendezett lista beszúrása és kép beszúrása AZ URL-címből.

  ![Synapse szöveges cella eszköztára](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

A Formázás gomb eszköztára még nem érhető el az előnézeti jegyzetfüzethez. 

---

### <a name="undo-cell-operations"></a>Cellaműveletek visszavonása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

A legutóbbi **cellaművelet** visszavonásához kattintson a visszavonás gombra, vagy nyomja le a **Ctrl+Z** billentyűkombinációt. Mostantól visszavonhatja a legutóbbi 20 cella-előzményműveletet. 

   ![Synapse-cellák visszavonása](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

A Cella visszavonása művelet még nem érhető el az előzetes verziójú jegyzetfüzethez. 

---

### <a name="move-a-cell"></a>Cella áthelyezése

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Kattintson a három pontra (...) a jobb oldalon lévő többi cellaművelet menüjének eléréséhez. Ezután válassza **a Cella áthelyezése felfelé** vagy **a Cella** áthelyezése lefelé lehetőséget az aktuális cella áthelyezéshez. 

Parancs módban [billentyűparancsokat is használhat.](#shortcut-keys-under-command-mode) Nyomja **le a Ctrl+Alt+↓** billentyűkombinációt az aktuális cella felfelé mozgatásával. Nyomja **le a Ctrl+Alt+↓** billentyűkombinációt az aktuális cella lefelé mozgatásával.

   ![cella áthelyezése](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Előzetes verziójú jegyzetfüzet](#tab/preview)

Kattintson egy cella bal oldalára, és húzza a kívánt helyre. 
    ![Cellák áthelyezése a Synapse-be](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Cella törlése

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Cella törléséhez kattintson a három pontra (...) a jobb oldalon található másik cellaműveletek menüjének eléréséhez, majd válassza a **Cella törlése lehetőséget.** 

Parancs módban [billentyűparancsokat is használhat.](#shortcut-keys-under-command-mode) Nyomja le **a D,D** billentyűt az aktuális cella törléséhez.
  
   ![cella törlése](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Előzetes verziójú jegyzetfüzet](#tab/preview)

Cella törléséhez kattintson a cella jobb oldali törlés gombjára. 

A parancs módban [billentyűparancsokat is használhat.](#shortcut-keys-under-command-mode) Az aktuális cella törléséhez nyomja le a **Shift+D** billentyűkombinációt. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Cellabemenet összecsukása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Az aktuális cella alján található nyíl gombra kattintva csukja össze. A kibontásához kattintson a nyíl gombra, amíg a cella össze van csukva.

   ![cellabemenet összecsukása](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

Válassza a **További parancsok** három pont (...) lehetőséget  a cella eszköztárán, és a bemenettel csukja össze az aktuális cella bemenetét. A kibontásához jelölje ki a **rejtett bemenetet,** amíg a cella össze van csukva.

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Cella kimenetének összecsukása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Kattintson **az aktuális cella kimenetének** bal felső sarkában található Kimenet összecsukása gombra az összecsukáshoz. A kibontásához válassza a **Cellakimenet** megjelenítése, amíg a cella kimenete össze van csukva.

   ![cellakimenet összecsukása](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

Válassza a **További parancsok** három pont (...) lehetőséget  a cella eszköztárán, és a kimenettel csukja össze az aktuális cella kimenetét. A kibontásához válassza ugyanazt a gombot, amíg a cella kimenete el van rejtve.

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Jegyzetfüzetek futtatása

A jegyzetfüzet kódcellái egyenként vagy egyszerre is futtathatók. Az egyes cellák állapota és előrehaladása a jegyzetfüzetben van ábrázolva.

### <a name="run-a-cell"></a>Cella futtatása

A kódot többféleképpen is futtathat egy cellában.

1. Vigye a kurzort a futtatni kívánt cellára, és kattintson a **Cella futtatása gombra,** vagy nyomja le a **Ctrl+Enter billentyűkombinációt.**

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. A [Parancs módban használjon Billentyűparancsokat.](#shortcut-keys-under-command-mode) Nyomja **le a Shift+Enter** billentyűkombinációt az aktuális cella futtatásához, és válassza ki az alábbi cellát. Nyomja **le az Alt+Enter** billentyűkombinációt az aktuális cella futtatásához és egy új cella beszúrása alá.

---

### <a name="run-all-cells"></a>Az összes cella futtatása
Kattintson az **Összes futtatása gombra** az aktuális jegyzetfüzet összes cellának egymás után való futtatásához.

   ![összes cella futtatása](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


### <a name="run-all-cells-above-or-below"></a>Az összes cella futtatása az alatt vagy felett

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

A jobb oldalon található másik cellaműveletek menüjének eléréséhez kattintson a három pontra (**...**). Ezután válassza **a Fenti Cellák futtatása lehetőséget** az aktuális fölötti összes cella egymás után való futtatásához. Válassza **az alábbi Cellák futtatása lehetőséget** az aktuális alatti cellák egymás után való futtatásához.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)

# <a name="preview-notebook"></a>[Előzetes verziójú jegyzetfüzet](#tab/preview)

Bontsa ki  az Összes futtatása  gomb legördülő listáját, majd válassza a Fenti Cellák futtatása lehetőséget az aktuális fölötti cellák egymás után való futtatásához. Válassza **az alábbi Cellák futtatása lehetőséget** az aktuális alatti cellák egymás után való futtatásához.

   ![azure-notebook-run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-aznb-run-cells-above-or-below.png)

---

### <a name="cancel-all-running-cells"></a>Az összes futó cella megszakítása

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)
Az Összes **visszavonása gombra** kattintva megszakíthatja az üzenetsorban várakozó cellákat vagy cellákat. 
   ![összes cella lemondása](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

Az Összes **visszavonása gombra** kattintva megszakíthatja az üzenetsorban várakozó cellákat vagy cellákat. 
   ![azure-notebook-cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-aznb-cancel-all.png) 

---



### <a name="notebook-reference"></a>Jegyzetfüzet-referencia

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Nem támogatott.

# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

A magic ```%run <notebook path>``` paranccsal hivatkozhat egy másik jegyzetfüzetre az aktuális jegyzetfüzet környezetében. A referencia-jegyzetfüzetben meghatározott összes változó elérhető az aktuális jegyzetfüzetben. ```%run``` A magic parancs támogatja a beágyazott hívásokat, de nem támogatja a rekurzív hívásokat. Kivételt fog kapni, ha az utasítás mélysége nagyobb ötnél. ```%run``` A parancs jelenleg csak a jegyzetfüzet elérési útjának paraméterként való átutasítását támogatja. 

Példa: ``` %run /path/notebookA ```.

> [!NOTE]
> A Jegyzetfüzet-hivatkozás nem támogatott a Synapse-folyamatban.
>
>

---


### <a name="cell-status-indicator"></a>Cella állapotjelzője

A cella végrehajtási állapota lépésről lépésre látható a cella alatt, így látható az aktuális folyamat. A cella futtatásának befejezése után megjelenik egy végrehajtási összegzés, amely a teljes időtartamot és a záró időt mutatja, és ott lesz megőrizve későbbi használatra.

![cellaállapot](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark-folyamatjelző

Azure Synapse Studio-jegyzetfüzet tisztán Spark-alapú. A kódcellák végrehajtása távolról, a Apache Spark a kiszolgáló nélküli kiszolgálón. A Spark-feladat folyamatjelzője egy valós idejű folyamatjelzővel jelenik meg, amely segít megérteni a feladat végrehajtásának állapotát.
Az egyes feladatok vagy fázisok tevékenységszáma segít azonosítani a Spark-feladat párhuzamos szintjét. Egy adott feladat (vagy fázis) Spark felhasználói felületének mélyebb részletezéséhez kattintson a feladat (vagy fázis) nevére mutató hivatkozásra.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-munkamenet konfigurációja

Az aktuális Spark-munkamenetnek a Munkamenet konfigurálása részen adhatja meg az időtúllépés időtartamát, számát és **méretét.** Indítsa újra a Spark-munkamenetet, hogy a konfigurációs módosítások érvénybe lépnek. Az összes gyorsítótárazott notebookváltozó törlődik.

[![munkamenet-kezelés](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark-munkamenet konfigurációs magic parancsa
A spark-munkamenet beállításait egy **magic paranccsal is megadhatja .configure**. A spark-munkamenetet újra kell indítani a beállítások hatásának érvénybe hozása érdekében. Javasoljuk, hogy a jegyzetfüzet elején futtassa **a következőt: .configure.** Megjelenik egy minta, amely az https://github.com/cloudera/livy#request-body érvényes paraméterek teljes listáját tartalmazza. 

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
> [!NOTE]
> A Spark-munkamenet konfigurációs magic parancsa nem támogatott a Synapse-folyamatban.
>
>

## <a name="bring-data-to-a-notebook"></a>Adatok behozása jegyzetfüzetbe

Az alábbi kódmintákban Azure Blob Storage, az Azure Data Lake Store Gen2-ről és az SQL-készletből is betölthet adatokat.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>CSV-fájl olvasása az Azure Data Lake Store Gen2-ről Spark DataFrame-ként

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>CSV-fájl olvasása Azure Blob Storage Spark DataFrame-ként

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

### <a name="read-data-from-the-primary-storage-account"></a>Adatok olvasása az elsődleges tárfiókból

Az elsődleges tárfiókban közvetlenül hozzáférhet az adatokhoz. A titkos kulcsokat nem kell megszabadni. A Adatkezelő kattintson a jobb gombbal egy fájlra, és válassza az **Új** jegyzetfüzet lehetőséget egy új jegyzetfüzet automatikus generált adatkivonattal való megnyitásához.

![adatokból cellákba](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Jegyzetfüzetek mentése

A munkaterületen egyetlen jegyzetfüzetet vagy az összes jegyzetfüzetet mentheti.

1. Ha egyetlen jegyzetfüzeten szeretne módosításokat menteni, kattintson a Notebook parancssáv **Közzététel** gombjára.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. A munkaterület összes jegyzetfüzetének mentéséhez válassza a Munkaterület parancssávjának **Összes** közzététele gombját. 

   ![összes közzététele](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

A jegyzetfüzet tulajdonságai között megadhatja, hogy a cellakimenetet is bele kell-e foglalni mentéskor.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Mágikus parancsok
A jól ismert Jupyter Magic-parancsokat használhatja Azure Synapse Studio-jegyzetfüzetben. Tekintse át az alábbi listát az aktuálisan elérhető magic parancsokként. Mondja el [nekünk a GitHubon](https://github.com/MicrosoftDocs/azure-docs/issues/new) a használattal kapcsolatos eseteket, hogy további, az igényeinek megfelelő varázsparancsokat építsünk ki.

> [!NOTE]
> A Synapse-folyamatban csak a következő varázsparancsok támogatottak: ©pyspark, ©spark, ©csharp, ©sql. 
>
>

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Elérhető sori varázsok: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Elérhető cellacsomólyák: [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [](#use-multiple-languages) [](#use-multiple-languages) [](#use-multiple-languages) [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)[](#spark-session-config-magic-command) [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [](#use-multiple-languages)<a0/*,* ,*,* *.* [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)



# <a name="preview-notebook"></a>[Előzetes verziójú jegyzetfüzet](#tab/preview)

Elérhető sori varázsok: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [%run](#notebook-reference), [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Rendelkezésre álló cellaredodálok: [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture) [](#use-multiple-languages) [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile) [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [](#spark-session-config-magic-command) [<a0/*,*](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)és <a3/2007555555555555 . [](#use-multiple-languages) [](#use-multiple-languages) [](#use-multiple-languages) [](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html)

--- 

## <a name="integrate-a-notebook"></a>Jegyzetfüzet integrálása

### <a name="add-a-notebook-to-a-pipeline"></a>Jegyzetfüzet hozzáadása folyamathoz

A jobb **felső sarokban** található Hozzáadás folyamathoz gombra kattintva adjon hozzá egy jegyzetfüzetet egy meglévő folyamathoz, vagy hozzon létre egy új folyamatot.

![Jegyzetfüzet hozzáadása folyamathoz](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Paramétercella jelölése

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

A jegyzetfüzet paraméteres beállításhoz kattintson a három pontra (...) a jobb oldalon lévő többi cellaművelet menüjének eléréséhez. Ezután válassza **a Paramétercella váltása lehetőséget** a cella paramétercellaként való kijelöléséhez.

![kapcsolóparaméter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Előzetes verziójú jegyzetfüzet](#tab/preview)

A jegyzetfüzet paraméteres megjelenítéséhez kattintson a három pontra (...) a további parancsok eléréséhez **a** cella eszköztárán. Ezután válassza **a Paramétercella váltása lehetőséget** a cella paramétercellaként való kijelöléséhez.

![azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory a cellaparamétereket, és ezt a cellát alapértelmezettként kezeli a végrehajtáskor átadott paraméterekhez. A végrehajtómotor új cellát ad hozzá a paramétercella alatt bemeneti paraméterekkel, hogy felülírja az alapértelmezett értékeket. Ha nincs paramétercella ki helyezve, az injektált cella a jegyzetfüzet tetejére lesz beszúrva.


### <a name="assign-parameters-values-from-a-pipeline"></a>Paraméterértékek hozzárendelése egy folyamatból

Miután létrehozott egy paraméterekkel egy jegyzetfüzetet, végrehajthatja azt egy folyamatból a Jegyzetfüzet Azure Synapse tevékenységgel. Miután hozzáadta a tevékenységet a folyamatvászonhoz, beállíthatja  a paraméterek értékeit a Beállítások lap Alapparaméterek **szakaszában.** 

![Paraméter hozzárendelése](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Paraméterértékek hozzárendelésekor használhatja a folyamatkifejezés [nyelvét](../../data-factory/control-flow-expression-language-functions.md) vagy a [rendszerváltozókat.](../../data-factory/control-flow-system-variables.md)



## <a name="shortcut-keys"></a>Billentyűparancsok

A Jupyter Notebookshoz hasonlóan a Azure Synapse Studio-jegyzetfüzetek is rendelkezik modális felhasználói felülettel. A billentyűzet különböző dolgokat tesz attól függően, hogy melyik módban van a notebookcella. Synapse Studio notebookok az alábbi két módot támogatják egy adott kódcellához: parancs mód és szerkesztési mód.

1. A cella parancsmódban van, ha nincs begépelni való szövegkurzor. Ha egy cella Parancs módban van, a jegyzetfüzetet egészként szerkesztheti, de nem írhatja be az egyes cellákba. A parancsmódot úgy adhatja meg, hogy lenyomja vagy az egérrel kijelöli a cellát `ESC` a szerkesztőterületen kívül.

   ![parancsmód](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. A szerkesztési módot egy szövegkurzor jelzi, amely arra kéri, hogy gépelje be a szerkesztőterületet. Ha egy cella szerkesztési módban van, begépelheti a cellába. A szerkesztési módot úgy adhatja meg, hogy lenyomja vagy az egérrel kijelöli a cellát `Enter` a szerkesztőterületen.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Parancs módban található billentyűparancsok

# <a name="classical-notebook"></a>[Klasszikus jegyzetfüzet](#tab/classical)

Az alábbi billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot Azure Synapse jegyzetfüzetben.

| Művelet |Synapse Studio notebook parancsikonjai  |
|--|--|
|Futtassa az aktuális cellát, és válassza az alábbi lehetőséget | Shift+Enter |
|Futtassa az aktuális cellát, és szúrja be az alábbit | Alt+Enter |
|Válassza ki a fenti cellát| Fel |
|Válassza ki az alábbi cellát| Le |
|Cella beszúrása fölé| A |
|Cella beszúrása alá| B |
|A fenti kijelölt cellák kiterjesztése| Shift+Fel |
|A kijelölt cellák kiterjesztése alább| Shift+Down|
|Cella áthelyezése felfelé| Ctrl+Alt+↓ |
|Cella lejjebb mozgatva| Ctrl+Alt+↓ |
|Kijelölt cellák törlése| D, D |
|Váltás szerkesztési módra| Enter |

# <a name="preview-notebook"></a>[Jegyzetfüzet előnézete](#tab/preview)

| Művelet |Synapse Studio jegyzetfüzet parancsikonjai  |
|--|--|
|Futtassa az aktuális cellát, és válassza az alábbi lehetőséget | Shift+Enter |
|Futtassa az aktuális cellát, és szúrja be az alábbit | Alt+Enter |
|Aktuális cella futtatása| Ctrl+Enter |
|Válassza ki a fenti cellát| Fel |
|Válassza ki az alábbi cellát| Le |
|Előző cella kijelölése| K |
|A következő cella kiválasztása| J |
|Cella beszúrása fölé| A |
|Cella beszúrása alá| B |
|Kijelölt cellák törlése| Shift+D |
|Váltás szerkesztési módra| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>Billentyűparancsok szerkesztési módban


Az alábbi billentyűleütés-billentyűparancsokkal könnyebben navigálhat és futtathat kódot Azure Synapse notebookban szerkesztési módban.

| Művelet |Synapse Studio jegyzetfüzet parancsikonjai  |
|--|--|
|A kurzor mozgatásával felfelé | Fel |
|A kurzor lefelé mozgatva|Le|
|Visszavonás|Ctrl + Z|
|Ismétlés|Ctrl + Y|
|Megjegyzés/Megjegyzés megjegyzésként való megjegyzésként való megszűkülése|Ctrl + /|
|Szó törlése korábban|Ctrl + Backspace|
|Szó törlése utána|Ctrl + Törlés|
|Ugrás a cellába indítás|Ctrl + Kezdőlap|
|Ugrás a cella végére |Ctrl + End|
|Ugrás egy szóval balra|Ctrl + Bal|
|Ugrás egy szóra jobbra|Ctrl + Jobb|
|Az összes kijelölése|Ctrl + A|
|Behúzás| Ctrl +]|
|Dedent (Dedent)|Ctrl + [|
|Váltás parancsmódra| Esc |

---

## <a name="next-steps"></a>Következő lépések
- [Tekintse meg a Synapse-mintajegyzetfüzeteket](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Rövid útmutató: Apache Spark készlet létrehozása Azure Synapse Analytics webes eszközök használatával](../quickstart-apache-spark-notebook.md)
- [Mi a Apache Spark a Azure Synapse Analytics](apache-spark-overview.md)
- [Az Apache Sparkhoz készült .NET használata az Azure Stream Analyticsszel](spark-dotnet.md)
- [A .NET for Apache Spark dokumentációja](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)