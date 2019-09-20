---
title: A ML Services-fürt kezelése a HDInsight-ben – Azure
description: Ismerje meg, hogyan kezelheti az Azure HDInsight különböző feladatait a ML Services-fürtökön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: e0ce8b97df6f2d6e95255d3f4dfc9f76fa08a594
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123553"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>A ML Services-fürt kezelése az Azure HDInsight

Ebből a cikkből megtudhatja, hogyan kezelheti az Azure HDInsight meglévő ML-szolgáltatások fürtjét olyan feladatok elvégzéséhez, mint például több egyidejű felhasználó hozzáadása, egy ML-es szolgáltatási fürthöz való távoli csatlakozás, a számítási környezet módosítása stb.

## <a name="prerequisites"></a>Előfeltételek

* Egy ML Services-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , majd a **ml-szolgáltatások** kiválasztása a **fürt típusához**.

* Egy Secure Shell-(SSH-) ügyfél: Az SSH-ügyfél használatával távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: az [SSH használata a HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="enable-multiple-concurrent-users"></a>Több párhuzamos felhasználó engedélyezése

Engedélyezheti több egyidejű felhasználó számára a HDInsight ML-szolgáltatások fürtjét azáltal, hogy további felhasználókat ad hozzá a peremhálózati csomóponthoz, amelyen a RStudio közösségi verziója fut. HDInsight-fürt létrehozásakor két felhasználót kell megadnia, egy HTTP-felhasználót és egy SSH-felhasználót:

![HDI Azure Portal bejelentkezési paraméterek](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- **Fürt bejelentkezési felhasználóneve**: HTTP-felhasználó a létrehozott HDInsight-fürtöket védő HDInsight-átjárón át történő hitelesítéshez. Ez a HTTP-felhasználó fér hozzá az Apache Ambari felhasználói felületéhez, Apache Hadoop a fonal felhasználói felületéhez, valamint az egyéb felhasználói felületi összetevőkhöz.
- **Secure Shell- (SSH-) felhasználónév**: SSH-felhasználó, aki a fürtöt biztonságos felületen keresztül éri el. Ez a felhasználó a Linux rendszerben az összes főcsomópont, munkavégző csomópont és élcsomópont felhasználója. Így Secure Shellt használhat a távoli fürt bármely csomópontjának elérésére.

A HDInsight ML Services-fürtben használt R Studio Server Community verziója csak a Linux-felhasználónevet és-jelszót fogadja bejelentkezési mechanizmusként. Nem támogatja a jogkivonatok átadását. Ha tehát az R Studio első alkalommal próbál hozzáférni egy ML Services-fürtön, kétszer kell bejelentkeznie.

- Először jelentkezzen be a HTTP-felhasználó hitelesítő adataival a HDInsight-átjárón keresztül.

- Ezután használja az SSH-felhasználó hitelesítő adatait a RStudio való bejelentkezéshez.
  
Jelenleg HDInsight-fürt üzembe helyezésekor csak egy SSH felhasználói fiókot lehet létrehozni. Így ahhoz, hogy több felhasználó hozzáférhessen a HDInsight-on található ML Services-fürthöz, további felhasználókat kell létrehoznia a Linux rendszerben.

Mivel a RStudio a fürt peremhálózati csomópontján fut, több lépés is van:

1. A meglévő SSH-felhasználó használata a peremhálózati csomópontba való bejelentkezéshez
2. További Linux-felhasználók hozzáadása az élcsomópontban
3. Az RStudio Community verziójának használata a létrehozott felhasználóval

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>1\. lépés: A létrehozott SSH-felhasználó használata a peremhálózati csomópontba való bejelentkezéshez

Kövesse az alábbi utasításokat a [Kapcsolódás a HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md) az Edge-csomópont eléréséhez. A HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net`lévő ml-szolgáltatások fürt peremhálózati csomópontjának címe.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>2\. lépés: További Linux-felhasználók hozzáadása az élcsomópontban

Felhasználó élcsomóponthoz adásához hajtsa végre a következő parancsokat:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

Az alábbi képernyőfelvételen a kimenetek láthatók.

![képernyőkép-kimenet egyidejű felhasználók számára](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

Ha a rendszer az "aktuális Kerberos-jelszó:" üzenetet kéri, csak nyomja meg az **ENTER** billentyűt a mellőzéséhez. A `useradd` parancs `-m` kapcsolója jelzi, hogy a rendszer létrehoz egy kezdőmappát a felhasználó számára, amely szükséges az RStudio Community verziójához.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>3\. lépés: Az RStudio Community verziójának használata a létrehozott felhasználóval

A https://CLUSTERNAME.azurehdinsight.net/rstudio/ RStudio elérése. Ha első alkalommal jelentkezik be a fürt létrehozása után, adja meg a fürt rendszergazdai hitelesítő adatait, majd a létrehozott SSH-felhasználói hitelesítő adatokat. Ha nem ez az első bejelentkezés, csak a létrehozott SSH-felhasználó hitelesítő adatait adja meg.

Az eredeti hitelesítő adatokkal (alapértelmezés szerint ez a *sshuser*) párhuzamosan is bejelentkezhet egy másik böngészőablakból.

Figyelje meg azt is, hogy az újonnan felvett felhasználók nem rendelkeznek gyökérjogosultságokkal a Linux rendszerben, de ugyanolyan hozzáférésük van a távoli HDFS- és WASB-tárolón az összes fájlhoz.

## <a name="connect-remotely-to-microsoft-ml-services"></a>Távoli kapcsolódás a Microsoft ML-szolgáltatásokhoz

Beállíthatja a HDInsight Spark számítási környezet elérését az asztalon futó ML-ügyfél távoli példányáról. Ehhez meg kell adnia a beállításokat (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches és sshProfileScript) a RxSpark számítási környezet az asztalon való definiálásakor: Példa:

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

További információ: "a Microsoft Machine Learning Server használata Apache Hadoop ügyfélként" című rész a [RevoScaleR használata Apache Spark számítási környezetben](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)

## <a name="use-a-compute-context"></a>Számítási környezet használata

A számítási környezetekkel vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.  A számítási környezet RStudio-kiszolgálóval való beállításának példáját lásd: [R-szkript végrehajtása egy ml Services-fürtön az Azure HDInsight a RStudio-kiszolgáló használatával](machine-learning-services-quickstart-job-rstudio.md).

## <a name="distribute-r-code-to-multiple-nodes"></a>R-kód elosztása több csomópontra

A HDInsight ML-szolgáltatásaival meglévő R-kódokat is végrehajthat, és a használatával `rxExec`futtathatja a fürt több csomópontján. Ez a függvény akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő kód az `rxExec` használatának példája:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Ha továbbra is a Spark-környezetet használja, ez a parancs visszaadja azon munkavégző csomópontok csomópontnév értékét `(Sys.info()["nodename"])` , amelyeken a kód fut. Négy csomópontos fürtön például a következő kódrészlethez hasonló kimenetet kell kapnia:

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>Hozzáférés a Apache Hive és a parketta szolgáltatáshoz

A HDInsight ML-szolgáltatások lehetővé teszik, hogy a Spark számítási környezetében a skálázási függvények a kaptárban és a parkettában lévő adatokhoz közvetlen hozzáférést használjanak. Ezek a képességek az RxHiveData és RxParquetData nevű új ScaleR adatforrás-függvényeken keresztül érhetők el, amelyek a Spark SQL-en keresztül töltenek adatokat közvetlenül a Spark DataFrame-be a ScaleR által végzett elemzéshez.

A következő kód tartalmazza az új függvények használatának néhány mintakódját:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Az új függvények használatával kapcsolatos további információkért tekintse meg az online súgót a ml-szolgáltatásokban a `?RxHivedata` és `?RxParquetData` a parancsok használatával.  

## <a name="install-additional-r-packages-on-the-cluster"></a>További R-csomagok telepítése a fürtön

### <a name="to-install-r-packages-on-the-edge-node"></a>R-csomagok telepítése a peremhálózati csomóponton

Ha további R-csomagokat szeretne telepíteni a peremhálózati csomóponton, akkor közvetlenül az r `install.packages()` -konzolról is használhatja, miután az SSH-n keresztül csatlakozott a peremhálózati csomóponthoz. 

### <a name="to-install-r-packages-on-the-worker-node"></a>R-csomagok telepítése a munkavégző csomóponton

Ha az R-csomagokat a fürt munkavégző csomópontjain szeretné telepíteni, parancsfájl-műveletet kell használnia. A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek, például további R-csomagok telepíthetők. 

> [!IMPORTANT]  
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Ne használja ezt az eljárást a fürt létrehozása során, mert a parancsfájl a teljes mértékben konfigurált ML-szolgáltatásokra támaszkodik.

1. Kövesse a [fürtök testreszabása parancsfájl-művelettel](../hdinsight-hadoop-customize-cluster-linux.md)című témakör lépéseit.

3. A **parancsfájl küldése művelethez**adja meg a következő információkat:

   * A **parancsfájl típusa**beállításnál válassza az **Egyéni**lehetőséget.

   * A **név mezőben**adja meg a parancsfájl művelet nevét.

     * A **bash-parancsfájl URI azonosítójának**megadása `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`:. Ez az a szkript, amely további R-csomagokat telepít a munkavégző csomóponton.

   * Jelölje be a jelölőnégyzetet a **feldolgozóhoz**.

   * **Paraméterek**: A telepítendő R-csomagok. Például: `bitops stringr arules`

   * Jelölje be a jelölőnégyzetet a **parancsfájl működésének**megőrzéséhez.  

   > [!NOTE]
   > 1. Alapértelmezés szerint a rendszer az összes R-csomagot a Microsoft MRAN adattárának pillanatképéről telepíti, amely megfelel a telepített ML Server verziójának. Ha a csomagok újabb verzióját szeretné telepíteni, akkor van némi inkompatibilitási kockázat. Ez a fajta telepítés azonban lehetséges, ha a `useCRAN` parancsot adja meg a csomaglista első elemeként, például: `useCRAN bitops, stringr, arules`.  
   > 2. Néhány R csomag további Linux rendszerű könyvtárakat igényel. A kényelmes használat érdekében a HDInsight ML-szolgáltatások előre telepítve vannak az első 100 legnépszerűbb R-csomaghoz szükséges függőségekkel. Ha azonban a telepített R csomag(ok) további könyvtárakat telepítését igényli(k), akkor le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >    A szkriptműveletek fejlesztésével kapcsolatos további információért lásd: [Szkriptművelet fejlesztése](../hdinsight-hadoop-script-actions-linux.md).

   ![Parancsfájl-Azure Portal küldése művelet](./media/r-server-hdinsight-manage/submit-script-action.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.

## <a name="next-steps"></a>További lépések

* [Az ML-szolgáltatások fürtjének üzembe helyezése a HDInsighton](r-server-operationalize.md)
* [Számítási környezeti beállítások a HDInsight található ML Service-fürthöz](r-server-compute-contexts.md)
* [Azure Storage-lehetőségek az ML-szolgáltatások HDInsighton belüli fürtjéhez](r-server-storage.md)
