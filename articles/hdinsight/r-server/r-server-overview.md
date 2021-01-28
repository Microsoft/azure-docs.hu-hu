---
title: A ML-szolgáltatások bemutatása az Azure HDInsight
description: Ismerje meg, hogyan hozhat létre alkalmazásokat big data elemzéshez a HDInsight ML-szolgáltatásainak használatával.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943916"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Mi az az Azure HDInsight a ML-szolgáltatások?

A Microsoft Machine Learning Server központi telepítési lehetőségként érhető el, amikor HDInsight-fürtöket hoz létre az Azure-ban. Az ezt a lehetőséget biztosító fürt típusának neve **ml szolgáltatás**. Ez a képesség igény szerinti hozzáférést biztosít a HDInsight-alapú elemzések adaptálható és elosztott módszereihez.

A HDInsight ML-szolgáltatásai az R-alapú elemzések legújabb képességeit biztosítják szinte bármilyen méretű adatkészletekben. Az adatkészletek betölthetők az Azure Blobba vagy a Data Lake Storage szolgáltatásba. Az R-alapú alkalmazások a 8000 + nyílt forráskódú R-csomagokat használhatják. A skálázás, a Microsoft big data Analytics-csomagjának rutinja is elérhető.

A peremhálózati csomópont kényelmes helyet biztosít a fürthöz való kapcsolódáshoz és az R-parancsfájlok futtatásához. A peremhálózati csomópont lehetővé teszi a skálázás párhuzamosan elosztott funkcióinak futtatását a kiszolgáló magjai között. Azokat a fürt csomópontjain is futtathatja, ha a méretezőt a Hadoop Térkép használatával csökkenti. Apache Spark számítási környezeteket is használhat.

Az elemzésből eredő modellek vagy előrejelzések a helyszíni használatra tölthetők le. Más is lehetnek `operationalized` Az Azure-ban. Különösen a [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net)és a [Web Service](../../machine-learning/classic/deploy-a-machine-learning-web-service.md)használatával.

## <a name="get-started-with-ml-services-on-hdinsight"></a>Ismerkedés a HDInsight által nyújtott ML-szolgáltatásokkal

Ha egy ML Services-fürtöt szeretne létrehozni a HDInsight-ben, válassza ki a **ml Services** -fürt típusát. A ML-szolgáltatások fürtjének típusa ML Servert tartalmaz az adatcsomópontokon és a peremhálózati csomóponton. A peremhálózati csomópont kilépési zónaként szolgál a ML-szolgáltatások alapú elemzésekhez. A fürt létrehozásával kapcsolatos útmutatásért lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) .

## <a name="why-choose-ml-services-in-hdinsight"></a>Miért érdemes a ML-szolgáltatásokat választani a HDInsight-ben?

A HDInsight ML-szolgáltatásai a következő előnyöket biztosítják:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>A Microsoft és a nyílt forráskódú AI-innováció

  A ML-szolgáltatások a jól alkalmazható, elosztott algoritmusokat, például a [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), a [Revoscalepy csomagjai](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package)és a [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package)-készletet foglalják magukban. Ezek az algoritmusok a fizikai memória méreténél nagyobb adatméreteken működhetnek. Emellett számos platformon futnak, és elosztott módon működnek. További információ a Microsoft egyéni [R-csomagjainak](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) és a termékben található [Python-csomagok](/machine-learning-server/python-reference/introducing-python-package-reference) gyűjteményéről.
  
  A ML-szolgáltatások a nyílt forráskódú Közösségtől (R, Python és AI eszközkészlet) érkező Microsoft-innovációkat és-hozzájárulásokat hidak. Egyetlen nagyvállalati szintű platformon. Bármely R vagy Python nyílt forráskódú gépi tanulási csomag a Microsoft tulajdonában lévő bármilyen üzleti innovációval párhuzamosan működhet.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Egyszerű, biztonságos és nagy léptékű operacionalizálási és felügyelet

  A hagyományos paradigmák és környezetek révén a vállalatok sok időt és erőfeszítést fektetnek a operacionalizálási irányába. Ezzel a művelettel a költségek és a késések is megmaradnak, beleértve a fordítási időt is: modellek, iterációk, hogy érvényes és aktuális, szabályozási jóváhagyást és kezelési engedélyeket őrizzenek meg.

  A ML-szolgáltatások nagyvállalati szintű [operacionalizálási](/machine-learning-server/what-is-operationalization)biztosítanak. A gépi tanulási modell befejeződése után mindössze néhány kattintással létrehozhatja a webszolgáltatási API-kat. Ezek a [webszolgáltatások](/machine-learning-server/operationalize/concept-what-are-web-services) a felhőben egy kiszolgálói rácson futnak, és az üzletági alkalmazásokkal is integrálhatók. A rugalmas rácsra való üzembe helyezés lehetősége lehetővé teszi, hogy zökkenőmentesen méretezhető legyen az üzleti igényeknek megfelelően, a Batch és a valós idejű pontozás esetében is. Útmutatásért lásd: [MŰKÖDŐVÉ tenni ml szolgáltatások a HDInsight-on](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> A HDInsight-ben a ML-szolgáltatások fürtjének típusa csak a 3,6-es HDInsight támogatott. A HDInsight 3,6 a 2020. december 31-én való kivonásra van ütemezve.

## <a name="key-features-of-ml-services-on-hdinsight"></a>A HDInsight által nyújtott ML-szolgáltatások főbb jellemzői

A következő szolgáltatások a HDInsight ML-szolgáltatásaiban szerepelnek.

| Szolgáltatás kategóriája | Description |
|------------------|-------------|
| R-enabled | R- [csomagok](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) az r-ben írt megoldásokhoz, az r nyílt forráskódú eloszlásával és futásidejű infrastruktúrával a parancsfájlok futtatásához. |
| Python – engedélyezve | Python- [modulok](/machine-learning-server/python-reference/introducing-python-package-reference) a Pythonban írt megoldásokhoz, a Python és a futásidejű infrastruktúra nyílt forráskódú eloszlásával a parancsfájlok futtatásához.
| [Előre betanított modellek](/machine-learning-server/install/microsoftml-install-pretrained-models) | A vizuális elemzéshez és a szöveg hangulatának elemzéséhez, amely készen áll az Ön által megadott adatgyűjtésre. |
| [Üzembe helyezés és használat](r-server-operationalize.md) | `Operationalize` a kiszolgáló és a megoldások webszolgáltatásként való üzembe helyezése. |
| [Távoli végrehajtás](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Indítsa el a távoli munkameneteket a hálózaton található ML Services-fürtön az ügyfél munkaállomásán. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Adattárolási lehetőségek a HDInsight ML-szolgáltatásaihoz

A HDFS fájlrendszer alapértelmezett tárolója lehet Azure Storage-fiók vagy Azure Data Lake Storage. Az elemzés során a rendszer feltöltötte az adattárakat a fürtbe. Az adatkészletek még a fürt törlése után is elérhetők. A különböző eszközök kezelhetik az adatátvitelt a tárolóba. Az eszközök közé tartozik a Storage-fiók és a AzCopy segédprogram portálon alapuló feltöltési létesítménye.

A fürt létrehozása során engedélyezheti a további blob-és adattárakhoz való hozzáférést is. Nincs korlátozva a használatban lévő elsődleges tárterület beállítás.  A több Storage-fiók használatával kapcsolatos további információkért lásd: [Az Azure Storage lehetőségei a HDINSIGHT ml-szolgáltatásaihoz](./r-server-storage.md) .

A peremhálózati csomóponton a Azure Files tárolási lehetőségként is használhatja. Azure Files lehetővé teszi az Azure Storage-ban létrehozott fájlmegosztás használatát a Linux fájlrendszerben. További információkért lásd: [Az Azure Storage lehetőségei a HDInsight-on található ml-szolgáltatásokhoz](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>A ML-szolgáltatások peremhálózati csomópontjának elérése

A peremhálózati csomóponton böngésző vagy SSH/Putty használatával kapcsolódhat a Microsoft ML Serverhoz. Az R-konzol alapértelmezés szerint telepítve van a fürt létrehozásakor.  

## <a name="develop-and-run-r-scripts"></a>R-parancsfájlok fejlesztése és futtatása

Az R-szkriptek a 8000-es és a nyílt forráskódú R-csomagok bármelyikét használhatják. A párhuzamos és elosztott rutinokat a skálázhatósági könyvtárból is használhatja. Az Edge-csomóponton futó parancsfájlok az R-tolmácson belül futnak a csomóponton. A skálázási függvényeket a Térkép csökkentése (RxHadoopMR) vagy a Spark (RxSpark) számítási környezet által meghívó lépések kivételével. A függvények elosztott módon futnak az adatokhoz társított adatcsomópontok között. A környezeti beállításokkal kapcsolatos további információkért lásd: [számítási környezeti beállítások a HDInsight tartozó ml-szolgáltatásokhoz](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` egy modell

Az adatmodellezés befejezése után `operationalize` Az Azure-ból vagy a helyszíni környezetből származó új adatokra vonatkozó előrejelzéseket készíthet. Ez a folyamat pontozásként ismert. A pontozás a HDInsight, Azure Machine Learning és a helyszínen is elvégezhető.

### <a name="score-in-hdinsight"></a>Pontszám a HDInsight

A HDInsight való kiértékeléséhez írjon egy R-függvényt. A függvény meghívja a modellt, hogy előrejelzéseket készítsen egy új, a Storage-fiókba betöltött adatfájlra vonatkozóan. Ezután mentse vissza a jóslatokat a Storage-fiókba. Ezt a rutin igény szerint futtathatja a fürt peremhálózati csomópontján vagy egy ütemezett feladaton keresztül.

### <a name="score-in-azure-machine-learning-aml"></a>Pontszám Azure Machine Learning (pénzmosás)

A Azure Machine Learning használatának értékeléséhez használja a [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) néven ismert nyílt forráskódú Azure Machine learning R-csomagot a modell Azure-webszolgáltatásként való közzétételéhez. A kényelem érdekében ez a csomag előre telepítve van a peremhálózati csomóponton. Ezután használja a Azure Machine Learning található létesítményeket a webszolgáltatás felhasználói felületének létrehozásához, majd a pontozáshoz szükség szerint hívja meg a webszolgáltatást. Ezután alakítsa át a skálázási modell objektumait egyenértékű, nyílt forráskódú modell-objektumokra a webszolgáltatással való használatra. Ehhez az átalakításhoz használjon skálázhatósági kényszerítési funkciókat, például `as.randomForest()` az Ensemble-alapú modellekhez.

### <a name="score-on-premises"></a>Helyszíni pontozás

Ha a modellt a modell létrehozása után szeretné felhasználni: szerializálja a modellt az R-ben, töltse le, deszerializálja, majd használja az új adatgyűjtéshez. Az új adatforrások a HDInsight vagy a [webszolgáltatások](/machine-learning-server/operationalize/concept-what-are-web-services)használatával a pontszámban korábban ismertetett módszer használatával adhatók meg.

## <a name="maintain-the-cluster"></a>A fürt karbantartása

### <a name="install-and-maintain-r-packages"></a>R-csomagok telepítése és karbantartása

Az r-csomagok többsége szükséges a peremhálózati csomóponton, mivel az R-szkriptek legtöbb lépése ott fut. Ha további R-csomagokat szeretne telepíteni a peremhálózati csomóponton, használhatja az `install.packages()` r metódust.

Ha csak a skálázási függvénytár-rutinokat használja, általában nincs szükség további R-csomagokra. Előfordulhat, hogy további csomagok szükségesek a **rxExec** vagy a **RxDataStep** végrehajtásához az adatcsomópontokon.

A további csomagok a fürt létrehozása után parancsfájl-művelettel is telepíthetők. További információ: [a ml-szolgáltatások kezelése a HDInsight-fürtben](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Apache Hadoop MapReduce módosítása

A MapReduce-feladatok futtatásakor a rendelkezésre álló memória és a ML szolgáltatások is módosíthatók. Fürt módosításához használja az Apache Ambari felhasználói felületét a fürthöz. A Ambari felhasználói felületével kapcsolatos utasításokért lásd: [HDInsight-fürtök kezelése a Ambari webes felhasználói felületén](../hdinsight-hadoop-manage-ambari.md).

A rendelkezésre álló memória ML-szolgáltatásokhoz a Hadoop kapcsolók használatával módosítható a **RxHadoopMR**-hívásban:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>A fürt méretezése

A HDInsight lévő meglévő ML-szolgáltatások fürtje a portálon felfelé vagy lefelé is méretezhető. A vertikális felskálázásával további kapacitást szerezhet a nagyobb feldolgozási feladatokhoz. A fürtöket a tétlen állapotba állíthatja vissza. A fürtök méretezésével kapcsolatos utasításokért lásd: [HDInsight-fürtök kezelése](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>A szolgáltatás karbantartása

Az operációs rendszer karbantartását az alapul szolgáló Linux rendszerű virtuális gépeken végezheti egy HDInsight-fürtön, munkaidőn kívül. A karbantartást jellemzően a 3:30 ÓRAKOR (a virtuális gép helyi ideje szerint) végzik, minden hétfőn és csütörtökön. A frissítések nem befolyásolják egyszerre a fürt egynegyedét.

Előfordulhat, hogy a futó feladatok lelassulnak a karbantartás során. Azonban a befejezéshez továbbra is futniuk kell. Minden olyan egyéni szoftver-vagy helyi adatbázis, amelyet a karbantartási események megőrzik, kivéve, ha a fürt újraépítését igénylő katasztrofális hiba történik.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-beállítások a HDInsight ML-szolgáltatásaihoz

A HDInsight-fürt Linux Edge csomópontja az R-alapú elemzések kirakodási zónája. A HDInsight legújabb verziói a RStudio-kiszolgáló böngésző alapú IDE-verzióját biztosítják a peremhálózati csomóponton. A RStudio-kiszolgáló termelékenyebb, mint az R-konzol fejlesztése és végrehajtása.

Egy asztali IDE távoli MapReduce vagy Spark számítási környezeten keresztül érheti el a fürtöt. A lehetőségek a következők: [a Microsoft R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS), a RStudio és a Walware Eclipse-alapú StatET.

Nyissa meg az R-konzolt a peremhálózati csomóponton úgy, hogy beírja az **r** parancsot a parancssorba. A konzol felületének használata esetén célszerű R-szkriptet kialakítani egy szövegszerkesztőben. Ezután szükség szerint vágja ki és illessze be a szkript részét az R-konzolra.

## <a name="pricing"></a>Díjszabás

A ML-szolgáltatások HDInsight-fürtjéhez társított díjak a többi HDInsight hasonlóan vannak strukturálva. Ezek a mögöttes virtuális gépek méretén alapulnak a név, az adat és a peremhálózati csomópontok között. A Core-Hour felemelkedést is. További információ: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a HDInsight-fürtökön található ML-szolgáltatások használatáról, tekintse meg a következő cikkeket:

* [R-szkript végrehajtása egy ML Services-fürtön az Azure HDInsight az RStudio-kiszolgáló használatával](machine-learning-services-quickstart-job-rstudio.md)
* [Számítási környezeti beállítások az ML-szolgáltatások HDInsighton belüli fürtjében](r-server-compute-contexts.md)
* [A ML Services-fürt tárolási beállításai a HDInsight](r-server-storage.md)