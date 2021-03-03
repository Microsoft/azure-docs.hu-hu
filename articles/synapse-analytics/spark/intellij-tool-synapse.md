---
title: Oktatóanyag – Azure Toolkit for IntelliJ (Spark-alkalmazás)
description: Oktatóanyag – a Azure Toolkit for IntelliJ használatával fejlesztheti a Scala-ben írt Spark-alkalmazásokat, és elküldheti azokat egy kiszolgáló nélküli Apache Spark készletbe.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: a4512a8f75d59f617058a50846d7578a90b9d062
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675820"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Oktatóanyag: Apache Spark-alkalmazás létrehozása a IntelliJ a szinapszis-munkaterület használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Toolkit for IntelliJ beépülő modult Apache Spark alkalmazások fejlesztéséhez, amelyek a [Scala](https://www.scala-lang.org/)-ben íródtak, majd a IntelliJ integrált fejlesztői környezetből (ide) közvetlenül beküldhetik a kiszolgáló nélküli Apache Spark készletbe. A beépülő modult többféleképpen is használhatja:

- Egy Spark-készleten fejlesztheti és küldheti el a Scala Spark-alkalmazásokat.
- Hozzáférhet a Spark-készletek erőforrásaihoz.
- A Scala Spark alkalmazást helyileg fejlesztheti és futtathatja.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> - A Azure Toolkit for IntelliJ beépülő modul használata
> - Apache Spark alkalmazások fejlesztése
> - Alkalmazás beküldése a Spark-készletekbe

## <a name="prerequisites"></a>Előfeltételek

- A [INTELLIJ ötlet közösségi verziója](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Azure Toolkit beépülő 3.27.0 – 2019.2 – telepítés a [IntelliJ beépülő modulból](/java/azure/intellij/azure-toolkit-for-intellij-installation)
- [JDK (1,8-es verzió)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Scala beépülő modul – telepítés a [IntelliJ beépülő modulból](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).
- A következő előfeltétel csak a Windows-felhasználók számára érhető el:

  Ha a helyi Spark Scala alkalmazást Windows rendszerű számítógépen futtatja, előfordulhat, hogy a [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)című részben leírtak szerint kivételt tapasztal. A kivétel oka, hogy WinUtils.exe hiányzik a Windows rendszerből.
  A hiba elhárításához töltse le a [WinUtils végrehajtható fájlt](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) egy olyan helyre, mint például a **C:\WinUtils\bin**. Ezután adja hozzá **HADOOP_HOME** környezeti változót, és állítsa a változó értékét **C:\WinUtils** értékre.

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Spark Scala-alkalmazás létrehozása Spark-készlethez

1. Indítsa el a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** elemet az **új projekt** ablak megnyitásához.
2. Válassza ki a **Apache Spark/HDInsight** elemet a bal oldali ablaktáblán.
3. Válassza ki a **Spark Project with Samples (Scala)** elemet a főablakból.
4. A **Build eszköz** legördülő listából válassza ki a következő típusok egyikét:

   - **Maven** a Scala projekt-létrehozás varázsló támogatásához.
   - A **SBT** kezelése és a Scala-projekt kiépítése.

    ![IntelliJ IDEA új projekt párbeszédpanel](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Kattintson a **Tovább** gombra.
6. Az **új projekt** ablakban adja meg a következő információkat:

    | Tulajdonság | Leírás |
    | ----- | ----- |
    |Projektnév| Adjon meg egy nevet. Ebben az oktatóanyagban a következőt használjuk: `myApp`.|
    |Projekt &nbsp; helye| Adja meg a kívánt helyet a projekt mentéséhez.|
    |Projekt SDK| Előfordulhat, hogy az első ÖTLETE üres. Válassza az **új...** lehetőséget, és navigáljon a JDK-hez.|
    |Spark-verzió|A létrehozás varázsló a Spark SDK és a Scala SDK megfelelő verzióját integrálja. A szinapszis csak a **Spark 2.4.0** támogatja.|
    |||

    ![Az Apache Spark SDK kiválasztása](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Válassza a **Befejezés** gombot. A projekt elérhetővé válása néhány percet igénybe vehet.
8. A Spark-projekt automatikusan létrehoz egy összetevőt. Az összetevő megtekintéséhez hajtsa végre a következő műveleteket:

   a. A menüsávban navigáljon a **fájl**  >  **projekt szerkezete...** elemre.

   b. A **projekt szerkezete** **ablakban válassza az** összetevők elemet.

   c. A lelet megtekintése után válassza a **Mégse** lehetőséget.

    ![Az összetevő adatai a párbeszédpanelen](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. **LogQuery** keresése a **SajátPr**  >  **src**  >  **fő**  >  **Scala** >  **Sample** >  **LogQuery**. Ez az oktatóanyag a **LogQuery** -t használja a futtatáshoz.

   ![A Scala-osztály projektből való létrehozásának parancsai](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Kapcsolódás a Spark-készletekhez

Jelentkezzen be az Azure-előfizetésbe a Spark-készletekhez való kapcsolódáshoz.

### <a name="sign-in-to-your-azure-subscription"></a>Jelentkezzen be az Azure-előfizetésbe

1. A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

   ![A IntelliJ ÖTLETE az Azure Explorert mutatja be](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Az Azure Explorerben kattintson a jobb gombbal az **Azure** -csomópontra, majd válassza a **Bejelentkezés** lehetőséget.

   ![A IntelliJ IDEA Explorer jobb oldali gombjával kattintson az Azure elemre](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Az **Azure bejelentkezési** párbeszédpanelen válassza az **eszköz bejelentkezés** lehetőséget, majd válassza a **Bejelentkezés** lehetőséget.

    ![IntelliJ IDEA Azure-bejelentkezés](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Az **Azure-eszköz bejelentkezési** párbeszédpaneljén válassza a **Másolás&Megnyitás** elemet.

   ![IntelliJ IDEA Azure-eszköz bejelentkezés](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. A böngésző felületén illessze be a kódot, majd kattintson a **tovább** gombra.

   ![A Microsoft beírja a HDI kód párbeszédpanelt](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Adja meg az Azure-beli hitelesítő adatait, majd zárjuk be a böngészőt.

   ![A Microsoft e-mail-párbeszédpanelét írja be a HDI számára](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Miután bejelentkezett, az **előfizetések kiválasztása** párbeszédpanel felsorolja az összes olyan Azure-előfizetést, amely társítva van a hitelesítő adatokhoz. Válassza ki az előfizetését, majd válassza a **kiválasztás** lehetőséget.

    ![Az Előfizetések kiválasztása párbeszédpanel](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. Az **Azure Explorerben** bontsa ki a **szinapszis Apache Spark** elemét az előfizetésében található munkaterületek megtekintéséhez.

    ![IntelliJ IDEA Azure Explorer – fő nézet](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. A Spark-készletek megtekintéséhez tovább bővítheti a munkaterületet.

    ![Azure Explorer Storage-fiókok](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Spark Scala-alkalmazás távoli futtatása Spark-készleten

A Scala-alkalmazás létrehozása után távolról futtathatja azt.

1. A **futtatási/hibakeresési konfigurációk** ablak megnyitásához válassza az ikont.

    ![A Spark-alkalmazás beküldése az 1. HDInsight-parancsra](./media/intellij-tool-synapse/open-configuration-window.png)

2. A **Futtatás/hibakeresés konfiguráció** párbeszédpanelen válassza a elemet **+** , majd válassza **a Apache Spark a szinapszison** lehetőséget.

    ![A Spark-alkalmazás beküldése a HDInsight 2. parancsba](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. A **Futtatás/hibakeresés konfigurációk** ablakban adja meg a következő értékeket, majd kattintson az **OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Spark-készletek|Válassza ki azokat a Spark-készleteket, amelyeken futtatni szeretné az alkalmazást.|
    |Válassza ki a elküldeni kívánt összetevőt|Hagyja meg az alapértelmezett beállítást.|
    |Fő osztály neve|Az alapértelmezett érték a kijelölt fájl fő osztálya. Az osztályt a három pont (**..**.) gombra kattintva módosíthatja, és egy másik osztályt is választhat.|
    |Feladatok konfigurációi|Módosíthatja az alapértelmezett kulcsot és értékeket. További információ: [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Parancssori argumentumok|Szükség esetén megadhat argumentumokat szóközzel elválasztva a Main osztály számára.|
    |Hivatkozott tégelyek és hivatkozott fájlok|Megadhatja a hivatkozott tégelyek és fájlok elérési útját, ha van ilyen. Az Azure-beli virtuális fájlrendszerben is böngészheti a fájlokat, amelyek jelenleg csak ADLS Gen2 fürtöt támogatják. További információ: [Apache Spark konfiguráció] https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) és [erőforrások feltöltése a fürtbe](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Feladatok feltöltési tárolója|Kibontás a további beállítások megjelenítéséhez.|
    |Tárhelytípusa|Válassza az **Azure Blob használata** lehetőséget a **fürt alapértelmezett Storage-fiókjának** feltöltéséhez és a legördülő listából való feltöltéséhez.|
    |Tárfiók|Adja meg a Storage-fiókját.|
    |Storage-kulcs|Adja meg a tárolási kulcsot.|
    |Storage-tároló|A Storage- **fiók** és a **Storage-kulcs** megadása után válassza ki a tárolót a legördülő listából.|

    ![A Spark-beküldési párbeszédpanel 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Válassza a **SparkJobRun** ikont a projekt kijelölt Spark-készletbe való beküldéséhez. A **fürt távoli Spark-feladata** lapon a feladatok végrehajtási folyamata látható alul. Az alkalmazás leállításához kattintson a vörös gombra.

    ![Apache Spark beküldési ablak](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![A Spark beküldési párbeszédpanele 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Helyi futtatási/hibakeresési Apache Spark alkalmazások

Az alábbi útmutatást követve állíthatja be a helyi futtatást és a helyi hibakeresést a Apache Spark feladatokhoz.

### <a name="scenario-1-do-local-run"></a>1. forgatókönyv: helyi Futtatás

1. Nyissa meg a **futtatási/hibakeresési beállítások** párbeszédpanelt, és válassza a pluszjelet ( **+** ). Ezután válassza ki a **Apache Spark a szinapszis** lehetőségnél. Adja **meg a menteni** kívánt **Főosztály** nevét.

    ![IntelliJ futtatása hibakeresési konfigurációk helyi futtatása 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - A környezeti változók és a WinUtils.exe helye csak Windows-felhasználók számára használható.
    - Környezeti változók: a rendszerkörnyezeti változó automatikusan észlelhető, ha korábban már beállította, és nem kell manuálisan hozzáadnia.
    - [WinUtils.exe hely](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe): a WinUtils helyét a jobb oldalon található mappa ikonra kattintva adhatja meg.

2. Ezután kattintson a helyi lejátszás gombra.

    ![IntelliJ helyi futtatású hibakeresési konfigurációk futtatása 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Ha a helyi Futtatás befejeződött, ha a parancsfájl kimenetet tartalmaz, akkor a kimeneti fájl az **adatok**  >  **__alapértelmezett értéke__** alapján ellenőrizhető.

    ![IntelliJ projekt helyi futtatásának eredménye 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>2. forgatókönyv: helyi hibakeresés

1. Nyissa meg a **LogQuery** parancsfájlt, és állítsa be a töréspontokat.
2. Helyi hibakereséshez válassza a **helyi hibakeresés** ikont.

    ![IntelliJ projekt helyi futtatásának eredménye 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>A szinapszis munkaterület elérése és kezelése

Az Azure Explorerben különböző műveleteket hajthat végre Azure Toolkit for IntelliJon belül. A menüsávban navigáljon a **Megtekintés**  >  **eszközre a Windows**  >  **Azure Explorerben**.

### <a name="launch-workspace"></a>Munkaterület elindítása

1. Az Azure Explorerben navigáljon **Apache Spark a szinapszis** elemre, majd bontsa ki azt.

    ![IntelliJ IDEA Azure Explorer – fő nézet](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Kattintson a jobb gombbal a munkaterületre, majd válassza a **munkaterület elindítása** lehetőséget, majd megnyílik a webhely.

    ![Spark-feladatok nézet alkalmazás részletei 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark-Job View alkalmazás részletei 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark-konzol

Futtathatja a Spark helyi konzolt (Scala), vagy futtathatja a Spark Livy interaktív munkamenet-konzolt (Scala).

### <a name="spark-local-console-scala"></a>Spark helyi konzol (Scala)

Győződjön meg arról, hogy teljesítette a WINUTILS.EXE előfeltételt.

1. A menüsávban navigáljon **a**  >  **konfigurációk szerkesztése..**. lehetőségre.
2. A **Futtatás/hibakeresés konfigurációk** ablakban, a bal oldali ablaktáblán navigáljon a **Apache Sparkra a szinapszisban**  >  **[a Spark on szinapszis] SajátPr**.
3. A fő ablakban válassza a **helyileg futtatott** lapot.
4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Környezeti változók|Győződjön meg arról, hogy a HADOOP_HOME értéke helyes.|
    |WINUTILS.exe helye|Győződjön meg arról, hogy az elérési út helyes.|

    ![Helyi konzol beállítása – konfiguráció](./media/intellij-tool-synapse/local-console-synapse01.png)

5. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.
6. A menüsávban navigáljon az **eszközök**  >  **Spark-konzol**  >  **Futtatás Spark helyi konzol (Scala)** elemre.
7. Ezután két párbeszédpanel jelenik meg, amely megkérdezi, hogy szeretné-e automatikusan kijavítani a függőségeket. Ha igen, válassza az **Automatikus javítás** lehetőséget.

    ![IntelliJ ötlet Spark automatikus javítás dialog1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ ötlet Spark automatikus javítás Dialog2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja be a következőt: `sc.appName` , majd nyomja le a CTRL + ENTER billentyűkombinációt. Ekkor megjelenik az eredmény. A helyi konzolt a vörös gomb kiválasztásával állíthatja le.

    ![A IntelliJ IDEA helyi konzoljának eredménye](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy interaktív munkamenet-konzol (Scala)

Ez csak a 2018,2-es és a 2018,3-es IntelliJ támogatott.

1. A menüsávban navigáljon **a**  >  **konfigurációk szerkesztése..**. lehetőségre.

2. A **Futtatás/hibakeresés konfigurációk** ablakban, a bal oldali ablaktáblán navigáljon a **Apache Sparkra a szinapszisban**  >  **[a Spark on szinapszis] SajátPr**.

3. A főablakban válassza a **távoli Futtatás a fürtben** lapot.

4. Adja meg a következő értékeket, majd kattintson **az OK gombra**:

    |Tulajdonság |Érték |
    |----|----|
    |Fő osztály neve| Válassza ki a fő osztály nevét.| 
    |Spark-készletek|Válassza ki azokat a Spark-készleteket, amelyeken futtatni szeretné az alkalmazást.|
    ||

    ![Interaktív konzol beállított konfigurációja](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. A projekt területen navigáljon a **SajátPr**  >  **src**  >  **Main**  >  **Scala**-  >  **SajátPr**.

6. A menüsávban navigáljon az **eszközök**  >  **Spark-konzolon** a  >  **Spark Livy interaktív munkamenet-konzolja (Scala)** elemre.
7. A konzolnak az alábbi ábrához hasonlóan kell kinéznie. A konzol ablakában írja be a következőt: `sc.appName` , majd nyomja le a CTRL + ENTER billentyűkombinációt. Ekkor megjelenik az eredmény. A helyi konzolt a vörös gomb kiválasztásával állíthatja le.

    ![A IntelliJ IDEA interaktív konzoljának eredménye](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Kijelölés küldése a Spark-konzolra

Előfordulhat, hogy a szkript eredményét úgy szeretné látni, hogy elküld egy kódot a helyi konzolon vagy a Livy interaktív munkamenet-konzolján (Scala). Ehhez jelöljön ki egy kódot a Scala-fájlban, majd kattintson a jobb gombbal a **kijelölés küldése a Spark-konzolra** lehetőségre. A rendszer elküldi a kiválasztott kódot a konzolra, és készen áll. Az eredmény a konzolon a kód után fog megjelenni. A konzol a meglévő hibákat fogja ellenőriznie.

   ![Kijelölés küldése a Spark-konzolra](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](../overview-what-is.md)
- [Új Apache Spark-készlet létrehozása az Azure szinapszis Analytics-munkaterülethez](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)