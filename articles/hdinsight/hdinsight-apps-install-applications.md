---
title: Harmadik féltől származó alkalmazások telepítése az Azure HDInsight
description: Ismerje meg, hogyan telepíthet külső féltől származó Apache Hadoop alkalmazásokat az Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 46a22b78ade3d137f25b14e5cbdaff3264208228
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868392"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Külső gyártótól származó Apache Hadoop alkalmazások telepítése az Azure HDInsight

Ismerje meg, hogyan telepíthet külső gyártótól származó [Apache Hadoop](https://hadoop.apache.org/) alkalmazást az Azure HDInsight. A saját alkalmazások telepítéséről az [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md) című cikk tartalmaz útmutatást.

An méretű HDInsight alkalmazás egy olyan alkalmazás, amelyet a felhasználók telepíthetnek egy HDInsight-fürtre. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései.  

A közzétett alkalmazások a következő listában láthatók:

|Alkalmazás |Fürt típusa (i) | Leírás |
|---|---|---|
|[AtScale Intelligence platform](https://aws.amazon.com/marketplace/pp/AtScale-AtScale-Intelligence-Platform/B07BWWHH18) |Hadoop |A AtScale bekapcsolja a HDInsight-fürtöt egy kibővített OLAP-kiszolgálóra, amely lehetővé teszi, hogy több milliárd sornyi adatsort interaktív módon tudjon lekérdezni a már ismert BI-eszközökkel, a saját és a szeretettel – a Microsoft Excel, a Power BI, a tabló szoftver és a QlikView használatával. |
|[CDAP a HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |A CDAP az első olyan egységesített integrációs big data platform, amely felgyorsítja a Hadoop értékét, és lehetővé teszi az informatikai szolgáltatások számára az önkiszolgáló adattárolást. A nyílt forráskód és a bővíthető CDAP eltávolítja az innováció akadályait. Követelmények: 4 régiós csomópont, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |A Datameer önkiszolgáló méretezhető platformja az adatok előkészítésére, feltárására és szabályozására, valamint az összetett többforrású adatok értékes, üzleti használatra kész információkba való bekapcsolására, gyorsabb és intelligensebb elemzéseket tesz lehetővé nagyvállalati szinten. |
|[Dataiku DSS a HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS egy nagyvállalati adatelemzési platformon, amely lehetővé teszi az adatszakértők és az adatelemzők számára, hogy hatékonyabban tervezzék meg és futtassák az új adattermékeket és-szolgáltatásokat, a nyers adatelemzést pedig hatásos előrejelzésekként |
|[WANdisco Fusion HDI-alkalmazás](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBase, Storm, Kafka |Az adatok konzisztens elosztása egy elosztott környezetben nagy mennyiségű adatműveleti kérdés. A WANdisco Fusion egy nagyvállalati szintű szoftver, amely a strukturálatlan adatkonzisztencia bármely környezetbe való bekapcsolásával oldja meg ezt a problémát. |
|[H2O SparklingWater a HDInsight-hez](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |A H2O pezsgő a következő elosztott algoritmusokat támogatja: GLM, naiv Bayes, elosztott véletlenszerű erdő, gradiens-növelő gép, mély neurális hálózatok, mély tanulás, K-means, PCA, általánosított, alacsony rangú modellek, anomáliák észlelése, autokódolók. |
|[Striim Real-Time adatintegrációhoz a HDInsight-be](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBase, Storm, Spark, Kafka |A Striim ("Stream") egy végpontok közötti streaming adatintegrációs és intelligencia platform, amely lehetővé teszi a különböző adatfolyamok folyamatos betöltését, feldolgozását és elemzését. |
|[Jumbune Enterprise-Accelerating BigData Analitika](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |A Jumbune magas szinten segíti a vállalatok számára az 1. Felgyorsult a TEZ, a MapReduce & a Spark motoron alapuló struktúra, a Java, a Scala számítási feladatok teljesítménye. 2. Proaktív Hadoop-fürt figyelése, 3. Az adatminőség-kezelés létrehozása az elosztott fájlrendszerben. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Az Apache Kylin által működtetett Kyligence Enterprise lehetővé teszi a BI-t Big-alapú adatvédelemben. A Hadoop vállalati OLAP-motorja, a Kyligence Enterprise az iparági szabványoknak megfelelő adattárházat és BI-módszertant is lehetővé teszi az üzleti elemzők számára a Hadoop-beli építészek számára. |
|[Starburst Presto az Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |A Presto egy gyors és skálázható elosztott SQL-lekérdezési motor. Az Azure Storage és a számítási szolgáltatások elkülönítésének megtervezése tökéletes megoldás a Azure Data Lake Storage, az Azure Blob Storage, az SQL és a NoSQL adatbázisaiban és más adatforrásokban tárolt adatok lekérdezéséhez. |
|[StreamSets-adatgyűjtő a HDInsight-felhőhöz](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |A StreamSets adatgyűjtő egy egyszerű, nagy teljesítményű motor, amely valós időben továbbítja az adatstreameket. Az adatstreamek adatait az adatgyűjtő használatával irányíthatja és dolgozza fel. Egy 30 napos próbaverziós licenccel rendelkezik. |
|[Trifacta Wrangler vállalat](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBase |A Trifacta Wrangler Enterprise for HDInsight minden adatskála esetében támogatja a nagyvállalati szintű adatmennyiség-huzavona. A Trifacta Azure-on való futtatásának költsége a Trifacta-előfizetés költségeinek és a virtuális gépek Azure-infrastruktúra-költségeinek a kombinációja. |
|[Az egységesített adatplatform](https://www.crunchbase.com/organization/unifi-software) |Hadoop, HBase, Storm, Spark |Az egységesített adatplatform az önkiszolgáló Adateszközök zökkenőmentesen integrált csomagja, amely lehetővé teszi az üzleti felhasználó számára a növekményes bevételt, a költségek csökkentését és a működési komplexitást eredményező adatkihívások kezelését. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |A HDInsight Spark-fürthöz tartozó adatalkalmazás felbomlik. |
|[Vízvonal AI-Driven Data Catalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Az adatközpont-katalógusok, rendszerezések és az azokra vonatkozó információk a mesterséges intelligenciával az üzleti feltételekkel való automatikus címkézés érdekében. A vízvonal üzleti írástudó-katalógusa egy kritikus, sikeres összetevő az önkiszolgáló elemzési, megfelelőségi és irányítási, valamint informatikai felügyeleti kezdeményezésekhez. |

A cikkben szereplő utasítások az Azure Portalon alapulnak. A Azure Resource Manager sablont a portálról is exportálhatja, vagy beszerezheti a Resource Manager-sablon egy példányát a szállítóktól, és a Azure PowerShell és a klasszikus Azure CLI használatával üzembe helyezheti a sablont.  Lásd: [Apache Hadoop-fürtök létrehozása a HDInsight Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Előfeltételek
Ha egy meglévő HDInsight-fürtre kíván HDInsight-alkalmazásokat telepíteni, HDInsight-fürttel kell rendelkeznie. A fürt létrehozását lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md). HDInsight-alkalmazásokat HDInsight-fürt létrehozása közben is telepíthet.

## <a name="install-applications-to-existing-clusters"></a>Alkalmazások telepítése a meglévő fürtökre
Az alábbi eljárásból megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat egy meglévő HDInsight-fürtbe.

**HDInsight-alkalmazás telepítése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben navigáljon az **összes szolgáltatás**  >  **Analytics**  >  **HDInsight-fürthöz**.
3. Válasszon ki egy HDInsight-fürtöt a listából.  Ha még nincs ilyen fürtje, hozzon létre egyet most.  Lásd: [Fürtök létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. A **Beállítások** kategóriában válassza az **alkalmazások** lehetőséget. A fő ablakban megtekintheti a telepített alkalmazások listáját. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png" alt-text="HDInsight-alkalmazások portál menü":::
5. Válassza a **+ Hozzáadás** lehetőséget a menüből. Megtekintheti az elérhető alkalmazások listáját.  Ha a **+ Hozzáadás** szürkén jelenik meg, az azt jelenti, hogy a HDInsight-fürt ezen verziójához nem tartoznak alkalmazások.
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png" alt-text="HDInsight alkalmazások elérhető alkalmazások":::
6. Válasszon ki egyet az elérhető alkalmazások közül, majd kövesse az utasításokat a jogi feltételek elfogadásához.

A telepítési állapotot a portál értesítései között tekintheti meg (a portál tetején található harang ikonra kattintva). Az alkalmazás telepítése után az alkalmazás megjelenik a telepített alkalmazások listájában.

## <a name="install-applications-during-cluster-creation"></a>Alkalmazások telepítése fürtlétrehozás során

HDInsight-alkalmazásokat fürt létrehozása közben is telepíthet. A HDInsight-alkalmazásokat azt követően telepíti a rendszer, hogy a fürt létrejött, és futó állapotba került. Ha az alkalmazásokat a Azure Portal használatával szeretné telepíteni a fürt létrehozása során, a **konfiguráció + díjszabás** lapon válassza az **+ alkalmazás hozzáadása** lehetőséget.

:::image type="content" source="./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png" alt-text="Fürtözött konfigurációs alkalmazások Azure Portal":::

## <a name="list-installed-hdinsight-apps-and-properties"></a>Telepített HDInsight-alkalmazások és az alkalmazástulajdonságok listázása
A portál megjeleníti a fürthöz tartozó telepített HDInsight-alkalmazásokat, valamint az egyes telepített alkalmazásokhoz tartozó tulajdonságokat.

**HDInsight-alkalmazás és-megjelenítési tulajdonságok listázása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben navigáljon az **összes szolgáltatás**  >  **Analytics**  >  **HDInsight-fürthöz**.
3. Válasszon ki egy HDInsight-fürtöt a listából.
4. A **Beállítások** kategóriában válassza az **alkalmazások** lehetőséget. A fő ablakban megtekintheti a telepített alkalmazások listáját. 
   
    :::image type="content" source="./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png" alt-text="HDInsight alkalmazások telepített alkalmazásai":::
5. Válassza ki az egyik telepített alkalmazást a tulajdonság megjelenítéséhez. A tulajdonságok listája:

    |Tulajdonság | Leírás |
    |---|---|
    |Alkalmazás neve |Az alkalmazás neve. |
    |Állapot |Az alkalmazás állapota. |
    |Weblap |A peremhálózati csomóponton üzembe helyezett webalkalmazás URL-címe. A hitelesítő adatok ugyanazok, mint amelyeket a fürthöz beállított HTTP-felhasználónál használt. |
    |SSH-végpont |Az SSH használatával csatlakozhat a peremhálózati csomóponthoz. Az SSH hitelesítő adatai ugyanazok, mint amelyeket a fürthöz beállított SSH-felhasználónál használt. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Leírás | Az alkalmazás leírása. |

6. Egy alkalmazás törléséhez kattintson a jobb gombbal az alkalmazásra, majd a helyi menüben kattintson a **Törlés** parancsra.

## <a name="connect-to-the-edge-node"></a>Csatlakozás az élcsomóponthoz
Az élcsomóponthoz HTTP és SSH segítségével csatlakozhat. A végpont adatait megtalálja a [portálon](#list-installed-hdinsight-apps-and-properties). További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A HTTP végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított HTTP-felhasználónál használt; az SSH-végpont hitelesítő adatai ugyanazok, mint amelyeket a HDInsight-fürthöz beállított SSH-felhasználónál használt.

## <a name="troubleshoot"></a>Hibaelhárítás
Lásd: [A telepítési folyamat hibaelhárítása](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Következő lépések
* [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md): megtudhatja, hogyan helyezhet üzembe egy közzé nem tett HDInsight alkalmazást a HDInsight.
* [HDInsight-alkalmazások közzététele](hdinsight-apps-publish-applications.md): Megtudhatja, hogyan teheti közzé egyéni HDInsight-alkalmazásait az Azure Piactéren.
* [MSDN: Install an HDInsight application](/rest/api/hdinsight/hdinsight-application) (MSDN: HDInsight-alkalmazás telepítése): Megtudhatja, hogyan adhat meg HDInsight-alkalmazásokat.
* [Customize Linux-based HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster-linux.md) (Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek segítségével): megtudhatja, hogyan telepíthet további alkalmazásokat parancsfájlműveletek használatával.
* [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md): megtudhatja, hogyan hívhat Resource Manager-sablonokat HDInsight-fürtök létrehozásához.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Üres élcsomópontok használata a HDInsightban): a cikk bemutatja, hogyan lehet üres élcsomópontot használni egy HDInsight-fürt elérésére, HDInsight-alkalmazások tesztelésére és HDInsight-alkalmazások üzemeltetésére.