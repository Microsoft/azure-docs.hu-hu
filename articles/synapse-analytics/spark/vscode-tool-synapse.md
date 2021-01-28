---
title: Oktatóanyag – Spark & kaptár eszközei a VSCode-hez (Spark-alkalmazás)
description: Oktatóanyag – a VSCode Spark & struktúrájának eszközei a Pythonban írt Spark-alkalmazások fejlesztéséhez és kiszolgáló nélküli Apache Spark készletbe való beküldéséhez.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943772"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>Oktatóanyag: Apache Spark-alkalmazások létrehozása a VSCode a szinapszis-munkaterület használatával

Ismerje meg, hogyan használható a Visual Studio Code Apache Spark & kaptár Tools. Apache Hive batch-feladatok, interaktív struktúra-lekérdezések és PySpark-parancsfájlok létrehozásához és elküldéséhez használhatja az eszközöket Apache Spark számára. Először is leírjuk, hogyan telepíthet Spark & kaptár-eszközöket a Visual Studio Code-ban. Ezután megtudhatja, hogyan küldhet feladatokat a Spark & kaptár eszközein.

A Spark & struktúra eszközei a Visual Studio Code által támogatott platformokon telepíthetők. Vegye figyelembe a következő előfeltételeket a különböző platformokon.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő elemek szükségesek:

- Kiszolgáló nélküli Apache Spark készlet. Kiszolgáló nélküli Apache Spark készlet létrehozásához tekintse meg a [Apache Spark-készlet létrehozása Azure Portal használatával](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)című témakört.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Monó](https://www.mono-project.com/docs/getting-started/install/). A Mono csak Linux és macOS rendszerekhez szükséges.
- [PySpark interaktív környezet a Visual Studio Code](../../hdinsight/set-up-pyspark-interactive-environment.md)-hoz.
- Helyi könyvtár. Ez a cikk a  **C:\HD\Synaseexample**-t használja.

## <a name="install-spark--hive-tools"></a>A Spark & Kas eszközeinek telepítése

Az előfeltételek teljesítése után a Spark & kaptár Tools for Visual Studio Code-ot a következő lépésekkel telepítheti:

1. Nyissa meg a Visual Studio Code-ot.

2. A menüsávban navigáljon a   >  **bővítmények** megtekintéséhez.

3. A keresőmezőbe írja be a **Spark & kaptár** kifejezést.

4. Válassza ki a **Spark & struktúra eszközöket** a keresési eredmények közül, majd válassza a **telepítés** lehetőséget:

     ![Spark & struktúra a Visual Studio Code Python telepítéséhez](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. Szükség esetén válassza az **Újratöltés** lehetőséget.

## <a name="open-a-work-folder"></a>Munkahelyi mappa megnyitása

Az alábbi lépéseket követve nyisson meg egy munkahelyi mappát, és hozzon létre egy fájlt a Visual Studio Code-ban:

1. A menüsávban navigáljon a **fájl**  >  **megnyitása mappába..**  >  . **C:\HD\Synaseexample**, majd kattintson a **mappa kiválasztása** gombra. A mappa a bal oldali **Explorer** nézetben jelenik meg.

2. A **Explorer** nézetben válassza ki a **Synaseexample** mappát, majd válassza az **új fájl** ikont a munkahelyi mappa mellett:

     ![Visual Studio Code új fájl ikon](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. Nevezze el az új fájlt a `.py` (Spark script) fájlkiterjesztés használatával. Ez a példa az **HelloWorld.py**-t használja.

## <a name="connect-to-your-spark-pools"></a>Kapcsolódás a Spark-készletekhez

Jelentkezzen be az Azure-előfizetésbe a Spark-készletekhez való kapcsolódáshoz.

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

Az Azure-hoz való kapcsolódáshoz kövesse az alábbi lépéseket:

1. A menüsávban navigáljon a   >  **parancs paletta megtekintése...** elemre, és írja be az **Azure: bejelentkezés**:

     ![A Spark & struktúra eszközei a Visual Studio Code-beli bejelentkezéshez](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. Az Azure-ba való bejelentkezéshez kövesse a bejelentkezési utasításokat. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio Code ablak alján található állapotsorban.

## <a name="set-the-default-spark-pool"></a>Az alapértelmezett Spark-készlet beállítása

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **Synaseexample** mappát, ha be van zárva.  

2. Válassza ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld.py** -fájlt. Megnyílik a parancsfájl-szerkesztőben.

3. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **szinapszis: alapértelmezett Spark-készlet beállítása** lehetőséget.  

4. Ha még nem tette meg, [kapcsolódjon](#connect-to-your-spark-pools) az Azure-fiókjához.

5. Válasszon egy Spark-készletet az aktuális parancsfájlhoz tartozó alapértelmezett Spark-készletként. Az eszközök automatikusan frissítik a **.VSCode\settings.jsa** konfigurációs fájlban:

     ![A fürtkonfiguráció alapértelmezett konfigurációjának beállítása](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>Interaktív szinapszis PySpark-lekérdezések küldése a Spark-készletbe

A felhasználók a következő módokon végezhetik el a szinapszis PySpark interaktív használatával a Spark-készleten:

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>A szinapszis PySpark interaktív parancsának használata a file-ban
A PySpark interaktív parancs használatával küldje el a lekérdezéseket, kövesse az alábbi lépéseket:

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **Synaseexample** mappát, ha be van zárva.  

2. Hozzon létre egy új **HelloWorld.py** -fájlt a [korábbi](#open-a-work-folder) lépéseket követve.

3. Másolja és illessze be a következő kódot a parancsfájlba:

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. A PySpark/szinapszis Pyspark kernel telepítéséhez szükséges üzenet az ablak jobb alsó sarkában jelenik meg. A **telepítés** gombra kattintva folytathatja a PySpark/szinapszis PySpark telepítését; vagy kattintson a **kihagyás** gombra a lépés kihagyásához.

     ![a pyspark kernel telepítése](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. Ha később is telepítenie kell, navigáljon a fájl beállításait **tartalmazó**  >    >  **Beállítások** területére, majd törölje a jelet a **Hdinsight: a Pyspark kihagyása** jelölőnégyzet bejelölésének engedélyezése lehetőségre. 
    
     ![pyspark-telepítés kihagyásának engedélyezése](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. Ha a telepítés sikeresen megtörtént a 4. lépésben, a "PySpark/szinapszis Pyspark telepítése sikeres" üzenet jelenik meg az ablak jobb alsó sarkában. Az ablak újratöltéséhez kattintson az **Újratöltés** gombra.

     ![a pyspark telepítése sikerült](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. A menüsávban navigáljon a parancs-paletta **megtekintéséhez**.  >  **..** vagy használja a **SHIFT + CTRL + P** billentyűparancsot, és írja be a **Python: Select tolmács elemet a Jupyter-kiszolgáló indításához**.

     ![a jupyter-kiszolgáló indításához válassza a tolmács elemet.](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. Válassza ki az alábbi Python-beállítást.

     ![Válassza az alábbi lehetőséget](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. A menüsávban navigáljon a parancs-paletta **megtekintéséhez**.  >  **..** vagy használja a **SHIFT + CTRL + P** billentyűparancsot, és adja meg a **fejlesztő: újratöltési ablak** elemet.

     ![ablak újratöltése](./media/vscode-tool-synapse/reload-window.png)

10. Ha még nem tette meg, [kapcsolódjon](#connect-to-your-spark-pools) az Azure-fiókjához.

11. Válassza ki az összes kódot, kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **szinapszis: Pyspark Interactive** elemet a lekérdezés elküldéséhez. 

     ![pyspark interaktív helyi menü](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. Ha nem adott meg alapértelmezett Spark-készletet, válassza ki a Spark-készletet. Néhány pillanat elteltével a **Python interaktív** eredményei egy új lapon jelennek meg. Kattintson a PySpark elemre a kernel **PySpark** való átváltásához, majd küldje el újra a kiválasztott kódot, és a kód sikeresen lefusson. Az eszközök lehetővé teszik a teljes parancsfájl helyett a kód egy blokkjának beküldését a helyi menü használatával:

     ![interaktív](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Interaktív lekérdezés végrehajtása a (z) #%% comment használatával

1. Adja hozzá **#%%** a kódot, mielőtt beolvassa a notebookot.

     ![#%% hozzáadása](./media/vscode-tool-synapse/run-cell.png)

2. Kattintson a **Futtatás cellára**. Néhány pillanat elteltével a Python interaktív eredményei egy új lapon jelennek meg. Kattintson a PySpark elemre a kernel a **szinapszis PySpark** való átváltásához, majd kattintson ismét a **futtatási cella** elemre, és a kód futtatása sikeresen megtörténik. 

     ![cella eredményeinek futtatása](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>IPYNB-támogatás kihasználása a Python-bővítményből

1. Létrehozhat egy Jupyter Notebook parancsot a parancssorból, vagy létrehoz egy új. ipynb fájlt a munkaterületen. További információ: [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support)

2. Kattintson a **cella futtatása** gombra, és kövesse a megjelenő utasításokat az **alapértelmezett Spark-készlet beállításához** (az alapértelmezett fürtöt/készletet minden alkalommal meg kell határoznia a jegyzetfüzet megnyitása előtt), majd **újra kell tölteni** az ablakot.

     ![az alapértelmezett Spark-készlet beállítása és újratöltése](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. Kattintson a PySpark elemre a kernel a **szinapszis PySpark** való átváltásához, majd kattintson egy idő elteltével a **Futtatás cellára**, az eredmény megjelenik.

     ![ipynb-eredmények futtatása](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. Az MS-Python >= 2020.5.78807 verziója nem támogatott ezen a mértéknél [ismert probléma](#known-issues).
>  
>2. Váltson a szinapszis Pyspark kernelre, és tiltsa le az automatikus beállítások az Azure Portalon való alkalmazását. Ellenkező esetben hosszú időt vehet igénybe a fürt felébresztése és a szinapszis-kernel első használatba vételének beállítása. 
>
>    ![automatikus beállítások](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>PySpark-kötegelt feladatok elküldése a Spark-készletbe

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **Synaseexample** mappát, ha le van zárva.  

2. Hozzon létre egy új **BatchFile.py** -fájlt a [korábbi](#open-a-work-folder) lépések követésével.

3. Másolja és illessze be a következő kódot a parancsfájlba:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. Ha még nem tette meg, [kapcsolódjon](#connect-to-your-spark-pools) az Azure-fiókjához.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **szinapszis: PySpark batch** elemet.

6. Válasszon ki egy Spark-készletet, amely a PySpark-feladatot elküldi a következőnek:

     ![A Python-feladatok eredményének kimenete](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Miután elküldte a Batch-feladatot a Spark-készletbe, a küldési naplók megjelennek a **kimenet** ablakban a Visual Studio Code-ban. A **Spark felhasználói felület** URL-címe és a **Spark-alkalmazás felhasználói felületének** URL-címe is látható. A feladatok állapotának nyomon követéséhez megnyithatja az URL-címet egy böngészőben.

## <a name="access-and-manage-synapse-workspace"></a>A szinapszis munkaterület elérése és kezelése

Az Azure Explorerben különböző műveleteket hajthat végre a Spark & kaptár eszközök VSCode. Az Azure Explorerben.

![Azure-rendszerkép](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>Munkaterület elindítása

1. Az Azure Explorerben navigáljon a **szinapszishoz**, bontsa ki, majd jelenítse meg a szinapszis-előfizetések listáját.

     ![szinapszis-kezelő](./media/vscode-tool-synapse/synapse-explorer.png)

2. Kattintson a szinapszis munkaterület előfizetésére, bontsa ki, majd jelenítse meg a munkaterület listáját.

3. Kattintson a jobb gombbal egy munkaterületre, majd válassza a **Apache Spark-alkalmazások megtekintése** lehetőséget, a szinapszis Studio webhely Apache Spark alkalmazás lapja megnyílik.

     ![kattintson a jobb gombbal a munkaterületre](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![szinapszis Studio-alkalmazás](./media/vscode-tool-synapse/synapse-studio-application.png)

4. Kibonthat egy munkaterületet, az **alapértelmezett tárolót** és a **Spark-készleteket** .

5. Kattintson a jobb gombbal az **alapértelmezett tárolóra**, a **Másolás teljes elérési útját** , és **nyissa meg a szinapszis Studióban** . 

     ![kattintson a jobb gombbal az alapértelmezett tárolóra](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - A **teljes elérési út másolása** gombra kattintva a rendszer átmásolja az elsődleges ADLS Gen2-fiók URL-címét, és beillesztheti azt, ahová szüksége van a 。

     - Kattintson a **Megnyitás a szinapszis Studióban** elemre, és megnyílik az elsődleges Storage-fiók a szinapszis Studióban.

     ![alapértelmezett tároló a szinapszis Studióban](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. Bontsa ki az **alapértelmezett tárolót**, az elsődleges Storage-fiók megjelenik.

7. A **Spark-készletek** kibontásával a munkaterület összes Spark-készlete megjelenik.

     ![tárterület kibontása](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>Ismert problémák

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>az MS-Python >= 2020.5.78807 verziója nem támogatott ezen a mértékben 

"Nem sikerült csatlakozni a Jupyter notebookhoz." ismert probléma a Python-verzió >= 2020.5.78807. A probléma elkerülése érdekében javasoljuk, hogy a felhasználók az MS-Python **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** -verzióját használják.

![ismert problémák](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](../overview-what-is.md)
- [Új Apache Spark-készlet létrehozása az Azure szinapszis Analytics-munkaterülethez](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
