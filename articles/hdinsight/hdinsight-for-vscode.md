---
title: Azure HDInsight a Visual Studio Code-hoz
description: Megtudhatja, hogyan használhatja a Visual Studio Code-hoz készült Spark & Kas-eszközöket (Azure HDInsight). Lekérdezések és parancsfájlok létrehozásához és elküldéséhez használja az eszközöket.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: 1209c49cbd988a02b1e3503994fdbdac3f26216a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946133"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>A Spark &-struktúra eszközeinek használata a Visual Studio Code-hoz

Ismerje meg, hogyan használható a Visual Studio Code Apache Spark & kaptár Tools. Apache Hive batch-feladatok, interaktív struktúra-lekérdezések és PySpark-parancsfájlok létrehozásához és elküldéséhez használhatja az eszközöket Apache Spark számára. Először is leírjuk, hogyan telepíthet Spark & kaptár-eszközöket a Visual Studio Code-ban. Ezután megtudhatja, hogyan küldhet feladatokat a Spark & kaptár eszközein.  

A Spark & struktúra eszközei a Visual Studio Code által támogatott platformokon telepíthetők. Vegye figyelembe a következő előfeltételeket a különböző platformokon.

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek végrehajtásához a következő elemek szükségesek:

- Egy Azure-beli HDInsight-fürt. Fürt létrehozásához tekintse meg [az Ismerkedés a HDInsight szolgáltatással](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)című témakört. Vagy használjon egy Apache Livy-végpontot támogató Spark és kaptár fürtöt.
- [Visual Studio Code](https://code.visualstudio.com/).
- [Monó](https://www.mono-project.com/docs/getting-started/install/). A Mono csak Linux és macOS rendszerekhez szükséges.
- [PySpark interaktív környezet a Visual Studio Code](set-up-pyspark-interactive-environment.md)-hoz.
- Helyi könyvtár. Ez a cikk a  **C:\HD\HDexample**-t használja.

## <a name="install-spark--hive-tools"></a>A Spark & Kas eszközeinek telepítése

Az előfeltételek teljesítése után a Spark & kaptár Tools for Visual Studio Code-ot a következő lépésekkel telepítheti:

1. Nyissa meg a Visual Studio Code-ot.

2. A menüsávban navigáljon a   >  **bővítmények** megtekintéséhez.

3. A keresőmezőbe írja be a **Spark & kaptár** kifejezést.

4. Válassza ki a **Spark & struktúra eszközöket** a keresési eredmények közül, majd válassza a **telepítés** lehetőséget:

   ![Spark & struktúra a Visual Studio Code Python telepítéséhez](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Szükség esetén válassza az **Újratöltés** lehetőséget.

## <a name="open-a-work-folder"></a>Munkahelyi mappa megnyitása

Az alábbi lépéseket követve nyisson meg egy munkahelyi mappát, és hozzon létre egy fájlt a Visual Studio Code-ban:

1. A menüsávban navigáljon a **fájl**  >  **megnyitása mappába..**  >  . **C:\HD\HDexample**, majd kattintson a **mappa kiválasztása** gombra. A mappa a bal oldali **Explorer** nézetben jelenik meg.

2. A **Explorer** nézetben válassza ki a **HDexample** mappát, majd válassza az **új fájl** ikont a munkahelyi mappa mellett:

   ![Visual Studio Code új fájl ikon](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Nevezze el az új fájlt a `.hql` (kaptár-lekérdezések) vagy a `.py` (Spark script) fájlkiterjesztés használatával. Ez a példa a **HelloWorld. HQL**-t használja.

## <a name="set-the-azure-environment"></a>Az Azure-környezet beállítása

A nemzeti Felhőbeli felhasználók számára az alábbi lépéseket követve állítsa be először az Azure-környezetet, majd az **Azure: bejelentkezés** parancs használatával jelentkezzen be az Azure-ba:

1. Navigáljon a **fájl**  >  **beállításait** tartalmazó  >  **beállításokhoz**.
2. Keressen rá a következő sztringre: **Azure: felhő**.
3. Válassza ki a nemzeti felhőt a listából:

   ![Bejelentkezési bejegyzés alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Kapcsolódás Azure-fiókhoz

Ahhoz, hogy parancsfájlokat küldhessen a fürtökhöz a Visual Studio Code-ból, a felhasználó bejelentkezhet az Azure-előfizetésbe, vagy [csatolhat HDInsight-fürtöt](#link-a-cluster)is. A HDInsight-fürthöz való csatlakozáshoz használja az ESP-fürthöz tartozó Ambari Felhasználónév/jelszó vagy tartományhoz csatlakoztatott hitelesítő adatokat. Az Azure-hoz való kapcsolódáshoz kövesse az alábbi lépéseket:

1. A menüsávban navigáljon a   >  **parancs paletta megtekintése...** elemre, és írja be az **Azure: bejelentkezés**:

   ![A Spark & struktúra eszközei a Visual Studio Code-beli bejelentkezéshez](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Az Azure-ba való bejelentkezéshez kövesse a bejelentkezési utasításokat. A csatlakozás után az Azure-fiók neve megjelenik a Visual Studio Code ablak alján található állapotsorban.  

## <a name="link-a-cluster"></a>Fürt csatolása

### <a name="link-azure-hdinsight"></a>Hivatkozás: Azure HDInsight

Az [Apache Ambari](https://ambari.apache.org/)által felügyelt Felhasználónév használatával összekapcsolhatja a normál fürtöt, vagy a biztonságos Hadoop-fürtöt tartományi Felhasználónév használatával is összekapcsolhatja (például: `user1@contoso.com` ).

1. A menüsávban navigáljon a   >  **parancs paletta megtekintése...** elemre, és írja be a **Spark/kaptár: fürt csatolása** elemet.

   ![A Command paletta link cluster parancsa](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Válassza a csatolt fürt típusa **Azure HDInsight** elemet.

3. Adja meg a HDInsight-fürt URL-címét.

4. Adja meg a Ambari felhasználónevét; az alapértelmezett érték a **rendszergazda**.

5. Adja meg a Ambari jelszavát.

6. Válassza ki a fürt típusát.

7. Adja meg a fürt megjelenítendő nevét (nem kötelező).

8. Tekintse át az ellenőrzés **kimeneti** nézetét.

   > [!NOTE]  
   > A rendszer a csatolt felhasználónevet és jelszót használja, ha a fürt mind az Azure-előfizetésbe bejelentkezett, mind a fürthöz csatlakozik.  

### <a name="link-generic-livy-endpoint"></a>Hivatkozás: általános Livy végpont

1. A menüsávban navigáljon a   >  **parancs paletta megtekintése...** elemre, és írja be a **Spark/kaptár: fürt csatolása** elemet.

2. Válassza a csatolt fürt típusa **általános Livy végpont** elemet.

3. Adja meg az általános Livy-végpontot. Például: http \: //10.172.41.42:18080.

4. Válassza az **Alap** vagy a **none** engedélyezési típust.  Ha az **alapszintű** lehetőséget választja:  
   
   1. Adja meg a Ambari felhasználónevét; az alapértelmezett érték a **rendszergazda**.  

   2. Adja meg a Ambari jelszavát.

5. Tekintse át az ellenőrzés **kimeneti** nézetét.

## <a name="list-clusters"></a>Fürtök listázása

1. A menüsávban navigáljon a   >  **parancs paletta** megtekintése... elemre, és írja be a **Spark/kaptár: List cluster** parancsot.

2. Válassza ki a kívánt előfizetést.

3. Tekintse át a **kimeneti** nézetet. Ez a nézet megjeleníti a társított fürtöt (vagy fürtöket) és az Azure-előfizetéshez tartozó összes fürtöt:

   ![Alapértelmezett fürtkonfiguráció beállítása](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Az alapértelmezett fürt beállítása

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Válassza ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld. HQL** fájlt. Megnyílik a parancsfájl-szerkesztőben.

3. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **Spark/kaptár: alapértelmezett fürt beállítása** lehetőséget.  

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Válasszon ki egy fürtöt az aktuális parancsfájl alapértelmezett fürtjéként. Az eszközök automatikusan frissítik a **.VSCode\settings.jsa** konfigurációs fájlban:

   ![A fürtkonfiguráció alapértelmezett konfigurációjának beállítása](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Interaktív struktúra-lekérdezések és a kaptár batch-parancsfájlok beküldése

A Spark & kaptár Tools for Visual Studio Code használatával interaktív kaptár-lekérdezéseket és a fürtökhöz tartozó Batch-parancsfájlokat küldhet.

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Válassza ki a [korábban](#open-a-work-folder)létrehozott **HelloWorld. HQL** fájlt. Megnyílik a parancsfájl-szerkesztőben.

3. Másolja és illessze be a következő kódot a kaptár-fájlba, majd mentse:

   ```hiveql
   SELECT * FROM hivesampletable;
   ```

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, és válassza a **struktúra: interaktív** lehetőséget a lekérdezés elküldéséhez, vagy használja a Ctrl + Alt + I billentyűparancsot.  Válassza a **struktúra: batch** elemet a parancsfájl elküldéséhez, vagy használja a CTRL + ALT + H billentyűparancsot.  

6. Ha nem adott meg alapértelmezett fürtöt, válasszon ki egy fürtöt. Az eszközök lehetővé teszik a teljes parancsfájl helyett a kód egy blokkjának beküldését a helyi menü használatával. Néhány pillanat elteltével a lekérdezés eredménye megjelenik egy új lapon:

   ![Interaktív Apache Hive lekérdezés eredménye](./media/hdinsight-for-vscode/interactive-hive-result.png)

   - **Eredmények** panel: a teljes eredményt CSV-, JSON-vagy Excel-fájlként mentheti a helyi elérési útra, vagy csak több sort választhat.

   - **Üzenetek** panel: Ha kijelöl egy **sorszámot, a rendszer** a futó parancsfájl első sorára ugrik.

## <a name="submit-interactive-pyspark-queries"></a>Interaktív PySpark-lekérdezések küldése

A felhasználók a következő módokon végezhetik el PySpark interaktív módon:

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>A PySpark interaktív parancs használata a file-ban
A PySpark interaktív parancs használatával küldje el a lekérdezéseket, kövesse az alábbi lépéseket:

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha be van zárva.  

2. Hozzon létre egy új **HelloWorld.py** -fájlt a [korábbi](#open-a-work-folder) lépéseket követve.

3. Másolja és illessze be a következő kódot a parancsfájlba:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. A PySpark/szinapszis Pyspark kernel telepítéséhez szükséges üzenet az ablak jobb alsó sarkában jelenik meg. A **telepítés** gombra kattintva folytathatja a PySpark/szinapszis PySpark telepítését; vagy kattintson a **kihagyás** gombra a lépés kihagyásához.

   ![A képernyőképen a PySpark telepítésének kihagyására szolgáló lehetőség látható.](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. Ha később is telepítenie kell, navigáljon a fájl beállításait **tartalmazó**  >    >  **Beállítások** területére, majd törölje a jelet a **HDInsight: a Pyspark kihagyása** jelölőnégyzet bejelölésének engedélyezése lehetőségre. 
    
    ![A képernyőképen a Pyspark-telepítés kihagyásának engedélyezése látható.](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. Ha a telepítés sikeres volt a 4. lépésben, a "PySpark telepítése sikeres" üzenet jelenik meg az ablak jobb alsó sarkában. Az ablak újratöltéséhez kattintson az **Újratöltés** gombra.

   ![a pyspark telepítése sikerült](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)


7. A menüsávban navigáljon a parancs-paletta **megtekintéséhez**.  >  **..** vagy használja a **SHIFT + CTRL + P** billentyűparancsot, és írja be a **Python: Select tolmács elemet a Jupyter-kiszolgáló indításához**.

   ![a jupyter-kiszolgáló indításához válassza a tolmács elemet.](./media/hdinsight-for-vscode/select-interpreter-to-start-jupyter-server.png)

8. Válassza ki az alábbi Python-beállítást.

   ![Válassza az alábbi lehetőséget](./media/hdinsight-for-vscode/choose-the-below-option.png)
    
9. A menüsávban navigáljon a parancs-paletta **megtekintéséhez**.  >  **..** vagy használja a **SHIFT + CTRL + P** billentyűparancsot, és adja meg a **fejlesztő: újratöltési ablak** elemet.

    ![ablak újratöltése](./media/hdinsight-for-vscode/reload-window.png)

10. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

11. Válassza ki az összes kódot, kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **Spark: PySpark Interactive/szinapszis: PySpark Interactive** parancsot a lekérdezés elküldéséhez. 

    ![pyspark interaktív helyi menü](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

12. Ha nem adott meg alapértelmezett fürtöt, válassza ki a fürtöt. Néhány pillanat elteltével a **Python interaktív** eredményei egy új lapon jelennek meg. Kattintson a PySpark elemre a kernel **PySpark/szinapszis PySpark** való átváltásához, és a kód futtatása sikeresen megtörténik. Ha a szinapszis Pyspark kernelre szeretne váltani, tiltsa le az Azure Portal automatikus beállításait. Ellenkező esetben hosszú időt vehet igénybe a fürt felébresztése és a szinapszis-kernel első használatba vételének beállítása. Ha az eszközök azt is lehetővé teszik, hogy a teljes parancsfájl helyett programkódot küldjön a helyi menü használatával:

    ![pyspark interaktív Python interaktív ablak](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

13. Adja meg a **%% info** értéket, majd nyomja le a SHIFT + ENTER billentyűkombinációt a feladatok adatainak megtekintéséhez (nem kötelező):

    ![pyspark interaktív megtekintési feladatának adatai](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

Az eszköz a **Spark SQL** -lekérdezést is támogatja:

  ![pyspark interaktív nézet eredménye](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>Interaktív lekérdezés végrehajtása a (z) #%% comment használatával

1. **#%%** A notebook-élmény beszerzéséhez vegye fel a következőt:

   ![#%% hozzáadása](./media/hdinsight-for-vscode/run-cell.png)

2. Kattintson a **Futtatás cellára**. Néhány pillanat elteltével a Python interaktív eredményei egy új lapon jelennek meg. Kattintson a PySpark elemre a kernel PySpark/szinapszis PySpark való átváltásához, majd kattintson ismét a **cella futtatása** elemre, és a kód futtatása sikeresen megtörténik.

   ![cella eredményeinek futtatása](./media/hdinsight-for-vscode/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>IPYNB-támogatás kihasználása a Python-bővítményből

1. Létrehozhat egy Jupyter Notebook parancsot a parancssorból, vagy létrehoz egy új. ipynb fájlt a munkaterületen. További információ: [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support)

2. Kattintson a **cella futtatása** gombra, és kövesse a megjelenő utasításokat az **alapértelmezett Spark-készlet beállításához** (az alapértelmezett fürtöt/készletet minden alkalommal meg kell határoznia a jegyzetfüzet megnyitása előtt), majd **újra kell tölteni** az ablakot.

   ![az alapértelmezett Spark-készlet beállítása és újratöltése](./media/hdinsight-for-vscode/set-the-default-spark-pool-and-reload.png)

3. Kattintson a PySpark elemre a kernel **PySpark/szinapszis PySpark** való átváltásához, majd a **futtatási cella** lehetőségre egy idő elteltével az eredmény megjelenik.

   ![ipynb-eredmények futtatása](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
> A (z) " [MS-python >= 2020.5.78807 verziója nem támogatott ezen a bővítményen"](#issues-changed) megoldva. Frissítse az **MS-Pythont** a **legújabb verzióra** .

## <a name="submit-pyspark-batch-job"></a>PySpark-kötegelt feladatok elküldése

1. Nyissa meg újra a [korábban](#open-a-work-folder)tárgyalt **HDexample** mappát, ha le van zárva.  

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

4. [Kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához, vagy kapcsolja össze a fürtöt, ha még nem tette meg.

5. Kattintson a jobb gombbal a parancsfájl-szerkesztőre, majd válassza a **Spark: PySpark batch** vagy a **szinapszis: PySpark batch** _ elemet.

6. Válasszon ki egy olyan fürtöt vagy Spark-készletet, amely a PySpark-feladatot a következőre küldi:

   ![A Python-feladatok eredményének kimenete](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Miután elküldte a Python-feladatot, a küldési naplók megjelennek a Visual Studio Code-ban az _ *kimeneti** ablakban. A Spark felhasználói felület URL-címe és a fonal felhasználói felületének URL-címe is megjelenik. Ha Apache Spark készletbe küldi a Batch-feladatot, a Spark-előzmények felhasználói felületének URL-címe és a Spark-alkalmazás felhasználói felületének URL-címe is megjelenik. A feladatok állapotának nyomon követéséhez megnyithatja az URL-címet egy böngészőben.

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integráció a HDInsight Identity Broker (HIB) szolgáltatással

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Kapcsolódás a HDInsight ESP-fürthöz az ID Broker (HIB) használatával

A normál lépéseket követve bejelentkezhet az Azure-előfizetésbe, hogy csatlakozhasson a HDInsight ESP-fürthöz az ID Broker (HIB) használatával. Bejelentkezés után a rendszer a fürt listáját fogja látni az Azure Explorerben. További útmutatásért lásd: [Kapcsolódás a HDInsight-fürthöz](#connect-to-an-azure-account).

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Kaptár/PySpark-feladatok futtatása egy HDInsight ESP-fürtön az ID Broker (HIB) szolgáltatással

A kaptár-feladatok futtatásához kövesse a normál lépéseket, hogy elküldje a feladatot HDInsight ESP-fürtre az ID Broker (HIB) használatával. További útmutatásért tekintse meg az [interaktív struktúra-lekérdezések és a kaptár batch-parancsfájlok beküldését](#submit-interactive-hive-queries-and-hive-batch-scripts) ismertető témakört.

Interaktív PySpark-feladat futtatásához kövesse a normál lépéseket, hogy a HDInsight ESP-fürtöt az ID Broker (HIB) segítségével küldje el. További útmutatásért tekintse meg az [interaktív PySpark-lekérdezések küldését](#submit-interactive-pyspark-queries) ismertető témakört.

PySpark batch-feladat futtatásához kövesse a normál lépéseket, hogy a HDInsight ESP-fürtöt küldje el az ID Broker (HIB) használatával. További útmutatásért tekintse meg a [PySpark batch-feladatok elküldése](#submit-pyspark-batch-job) című témakört.

## <a name="apache-livy-configuration"></a>Apache Livy-konfiguráció

Az [Apache Livy](https://livy.incubator.apache.org/) -konfiguráció támogatott. A munkaterület mappában található fájl **.VSCode\settings.js** is konfigurálhatja. A Livy-konfiguráció jelenleg csak a Python-szkripteket támogatja. További információ: [LIVY readme](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**A Livy-konfiguráció elindítása**

### <a name="method-1"></a>1. módszer  

1. A menüsorban navigáljon a **fájl**  >  **beállításai**  >  **Beállítások** elemre.
2. A **keresési beállítások** mezőbe írja be a **HDInsight Job beküldése: Livy conf** kifejezést.  
3. A megfelelő keresési eredményhez válassza a szerkesztés lehetőséget a **settings.js** .

### <a name="method-2"></a>2. módszer

Küldje el a fájlt, és figyelje meg, hogy a `.vscode` rendszer automatikusan hozzáadja a mappát a munkahelyi mappához. A Livy konfigurációját a.vscode\settings.jsbejelölésével tekintheti **meg**.

- A projekt beállításai:

  ![HDInsight Apache Livy-konfiguráció](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

  >[!NOTE]
  >A **driverMemory** és a **executorMemory** beállításnál állítsa be az értéket és az egységet. Például: 1g vagy 1024m.

- Támogatott Livy konfigurációk:

  **/Batches közzététele**
  
  **Kérelem törzse**

  | name | leírás | típus |
  | --- | --- | --- |
  | file | A végrehajtandó alkalmazást tartalmazó fájl | Elérési út (kötelező) |
  | proxyUser | A felhasználó megszemélyesítése a feladatok futtatásakor | Sztring |
  | className | Java/Spark fő osztály alkalmazása | Sztring |
  | args | Az alkalmazás parancssori argumentumai | Karakterláncok listája |
  | üvegek | A munkamenetben használandó tégelyek | Karakterláncok listája | 
  | pyFiles | A munkamenetben használni kívánt Python-fájlok | Karakterláncok listája |
  | fájlok | A munkamenetben használni kívánt fájlok | Karakterláncok listája |
  | driverMemory | Az illesztőprogram-folyamathoz használandó memória mennyisége | Sztring |
  | driverCores | Az illesztőprogram-folyamathoz használandó magok száma | Int |
  | executorMemory | A felhasználható memória mennyisége/végrehajtó folyamat | Sztring |
  | executorCores | Az egyes végrehajtók számára használandó magok száma | Int |
  | numExecutors | A munkamenet elindításához szükséges végrehajtók száma | Int |
  | Archívum | A munkamenetben használni kívánt archívumok | Karakterláncok listája |
  | üzenetsor | A beküldeni kívánt FONÁL-várólista neve| Sztring |
  | name | A munkamenet neve | Sztring |
  | conf | Spark-konfiguráció tulajdonságai | Kulcs leképezése = val |

  **Válasz törzse** A létrehozott batch-objektum.

  | name | leírás | típus |
  | --- | ---| --- |
  | ID (Azonosító) | Munkamenet-azonosító | Int |
  | appId | A munkamenet alkalmazás-azonosítója | Sztring |
  | alkalmazásadatokat | Részletes alkalmazásadatok | Kulcs leképezése = val |
  | napló | Naplózási sorok | Karakterláncok listája |
  | állapot |Köteg állapota | Sztring |

  > [!NOTE]
  > A hozzárendelt Livy konfiguráció a kimenet ablaktáblán jelenik meg a parancsfájl elküldésekor.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integráció az Azure HDInsight az Intézőből

Az **Azure HDInsight** Explorer segítségével közvetlenül megtekintheti a fürtökben található kaptár-táblázatot:

1. Ha még nem tette meg, [kapcsolódjon](#connect-to-an-azure-account) az Azure-fiókjához.

2. Válassza ki az **Azure** ikont a bal szélső oszlopból.

3. A bal oldali ablaktáblán bontsa ki az **Azure: HDINSIGHT** elemet. Megjelenik az elérhető előfizetések és fürtök.

4. Bontsa ki a fürtöt a kaptár metaadatainak adatbázisának és a tábla sémájának megtekintéséhez.

5. Kattintson a jobb gombbal a kaptár táblára. Például: **hivesampletable**. Válassza az **előnézet** lehetőséget.

   ![Spark & struktúra a Visual Studio Code előzetes struktúra-táblájához](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Megnyílik az **előnézet eredményei** ablak:

   ![A Spark & struktúra a Visual Studio Code előzetes verziójának eredményei ablak](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- EREDMÉNYEK panel

   A teljes eredményt CSV-, JSON-vagy Excel-fájlként mentheti a helyi elérési útra, vagy egyszerűen több sort is kijelölhet.

- ÜZENETEK panel

  1. Ha a tábla sorainak száma nagyobb, mint 100, a következő üzenet jelenik meg: "az első 100 sor jelenik meg a kaptár táblában."
  2. Ha a tábla sorainak száma kisebb vagy egyenlő, mint 100, a következő üzenet jelenik meg: "60 sor jelenik meg a kaptár táblában."
  3. Ha nincs tartalom a táblában, a következő üzenet jelenik meg: " `0 rows are displayed for Hive table.` "

     >[!NOTE]
     >
     >A Linux rendszerben telepítse a xclip-t a másolási tábla adatkezelésének engedélyezéséhez.
     >
     >![Spark & struktúra a Visual Studio Code-hoz Linux rendszerben](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>További funkciók

A Spark & kaptár a Visual Studio Code-hoz a következő funkciókat is támogatja:

- **IntelliSense automatikus kiegészítése**. Javaslatok a kulcsszavakra, a metódusokra, a változókra és az egyéb programozási elemekre. A különböző ikonok különböző típusú objektumokat jelölnek:

    ![Spark &-struktúra eszközei a Visual Studio Code IntelliSense-objektumokhoz](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense-hiba jelölője**. A nyelvi szolgáltatás a struktúra parancsfájljában a szerkesztési hibákat aláhúzza.     
- A **szintaxis kiemeli**. A nyelvi szolgáltatás különböző színeket használ a változók, kulcsszavak, adattípus, függvények és egyéb programozási elemek megkülönböztetéséhez:

    ![A Spark & a kaptár Tools for Visual Studio Code szintaxisának főbb jellemzői](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Csak olvasó szerepkör

Azok a felhasználók, akik csak olvasó szerepkört kapnak a fürthöz, nem küldhetnek feladatokat a HDInsight-fürtbe, és nem tekinthetik meg a struktúra-adatbázist. Forduljon a fürt rendszergazdájához, és frissítse a szerepkört a [Azure Portal](https://portal.azure.com/) [**HDInsight**](./hdinsight-migrate-granular-access-cluster-configurations.md#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) . Ha érvényes Ambari hitelesítő adatokkal rendelkezik, manuálisan is összekapcsolhatja a fürtöt a következő útmutatás segítségével.

### <a name="browse-the-hdinsight-cluster"></a>Tallózás a HDInsight-fürtben  

Amikor kijelöli az Azure HDInsight Explorer alkalmazást a HDInsight-fürt kibontásához, a rendszer arra kéri, hogy kapcsolja össze a fürtöt, ha a fürt csak olvasó szerepkörrel rendelkezik. A következő módszer használatával a fürthöz a Ambari hitelesítő adataival lehet hivatkozni.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Küldje el a feladatot a HDInsight-fürtnek

Ha a feladatot egy HDInsight-fürtre küldi, a rendszer arra kéri, hogy kapcsolja össze a fürtöt, ha a fürt csak olvasó szerepköre van. A következő lépések végrehajtásával hivatkozhat a fürtre Ambari hitelesítő adatok használatával.

### <a name="link-to-the-cluster"></a>A fürtre mutató hivatkozás

1. Érvényes Ambari-felhasználónevet adjon meg.
2. Adjon meg egy érvényes jelszót.

   ![Spark &-struktúra eszközei a Visual Studio Code felhasználónévhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![A Spark &-struktúra eszközei a Visual Studio Code Passwordhez](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

   > [!NOTE]
   >
   >A következő paranccsal `Spark / Hive: List Cluster` ellenőrizhető a csatolt fürt:
   >
   >![Spark &-struktúra eszközei a Visual Studio Code Readerhez csatolt](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

### <a name="browse-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2-fiók tallózása

Válassza ki az Azure HDInsight Explorert Data Lake Storage Gen2 fiók kibontásához. Ha az Azure-fiókja nem fér hozzá a Gen2-tárolóhoz, a rendszer kéri, hogy adja meg a Storage-hozzáférési kulcsot. A hozzáférési kulcs érvényesítése után a rendszer automatikusan kibontja a Data Lake Storage Gen2 fiókot.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Feladatok elküldése egy HDInsight-fürtbe Data Lake Storage Gen2

A HDInsight-fürthöz Data Lake Storage Gen2 használatával küldhet el feladatot. Ha az Azure-fiókja nem rendelkezik írási hozzáféréssel a Gen2-tárolóhoz, a rendszer kéri, hogy adja meg a tárolási hozzáférési kulcsot. A hozzáférési kulcs érvényesítése után a rendszer sikeresen elküldi a feladatot.

![Spark &-struktúra eszközei a Visual Studio Code AccessKey](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> A Storage-fiók elérési kulcsát a Azure Portal kérheti le. További információ: a [Storage-fiók elérési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md).

## <a name="unlink-cluster"></a>Fürt leválasztása

1. A menüsávban válassza a parancs-paletta **megtekintése** elemet  >  , majd írja be a **Spark/kaptár: fürt leválasztása****parancsot**.  

2. Válassza ki a leválasztani kívánt fürtöt.  

3. Tekintse meg az ellenőrzés **kimeneti** nézetét.  

## <a name="sign-out"></a>Kijelentkezés  

A menüsávban válassza a parancs-   >  **paletta** megtekintése elemet, majd írja be az **Azure: kijelentkezés** lehetőséget.

## <a name="issues-changed"></a>Megváltoztatott problémák

A "MS-Python >= 2020.5.78807 verzió nem támogatja ezt a bővítményt" című cikkben található probléma megoldásához frissítse az **MS-Pythont** a **legújabb verzióra** .


## <a name="next-steps"></a>Következő lépések

A Spark & kaptár Visual Studio Code-hoz való használatát bemutató videó: [spark &-struktúra a Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)-hoz.