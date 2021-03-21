---
title: 'Hivatkozás: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Az Ubuntu Data Science Virtual Machineban található eszközök részletei
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661069"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Hivatkozás: Ubuntu (Linux) Data Science Virtual Machine

Az Ubuntu Data Science Virtual Machine elérhető eszközeinek listáját alább találja. 

## <a name="deep-learning-libraries"></a>Deep learning-kódtárak

### <a name="cntk"></a>CNTK

A Microsoft Cognitive Toolkit egy nyílt forráskódú Deep learning-eszközkészlet. A Python-kötések a gyökérszintű és py35 Conda környezetekben érhetők el. Emellett egy olyan parancssori eszközzel (CNTK) is rendelkezik, amely már szerepel az elérési úton.

A Python-jegyzetfüzetek a JupyterHub-ben érhetők el. Ha alapszintű mintát szeretne futtatni a parancssorban, futtassa a következő parancsokat a rendszerhéjban:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

További információkért lásd a [GitHub](https://github.com/Microsoft/CNTK) és a [CNTK wiki](https://github.com/Microsoft/CNTK/wiki)CNTK szakaszát.

### <a name="caffe"></a>Caffe

A Cafe a Berkeley vízió és a Learning Center mély tanulási keretrendszere. A/opt/caffe.-ben érhető el Példákat talál a/opt/caffe/examples.

### <a name="caffe2"></a>Caffe2

A Caffe2 az a Facebook, amely a Cafe-ra épül. A Python 2,7-es verzióban érhető el a Conda gyökérszintű környezetben. Az aktiválásához futtassa a következő parancsot a rendszerhéjból:

```bash
source /anaconda/bin/activate root
```

Néhány példa notebookok érhetők el a JupyterHub-ben.

### <a name="h2o"></a>H2O

A H2O egy gyors, memóriában futó, elosztott gépi tanulás és prediktív elemzési platform. A Python-csomagok a root és a py35 anaconda környezetekben is települnek. Az R-csomag is telepítve van. 

A H2O parancssorból való megnyitásához futtassa a parancsot `java -jar /dsvm/tools/h2o/current/h2o.jar` . Több [parancssori kapcsolót](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) is beállíthat. A folyamat webes felhasználói felületének eléréséhez tallózással keresse meg az `http://localhost:54321` első lépéseket. A minta jegyzetfüzetek a JupyterHub-ben is elérhetők.

### <a name="keras"></a>Keras

A kerasz egy magas szintű neurális hálózati API a Pythonban. TensorFlow, Microsoft Cognitive Toolkit vagy theano-on is futtatható. Ez a gyökér-és py35 Python-környezetekben érhető el.

### <a name="mxnet"></a>MXNet

A MXNet a hatékonyságot és a rugalmasságot célzó, mélyreható tanulási keretrendszer. R-és Python-kötéseket tartalmaz a DSVM. A minta jegyzetfüzetek a JupyterHub részét képezik, és a mintakód elérhető a/dsvm/Samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA-SZÁMJEGYEK

Az NVIDIA Deep learning GPU képzési rendszer, az úgynevezett SZÁMJEGYek, egy olyan rendszer, amely leegyszerűsíti a közös mély tanulási feladatokat. Ezen feladatok közé tartozik az adatok kezelése, a neurális hálózatok tervezése és betanítása a GPU-rendszereken, valamint a teljesítmény valós idejű figyelése a speciális vizualizációkkal.

A SZÁMJEGYek *számjegyek nevű* szolgáltatásként érhetők el. Indítsa el a szolgáltatást, és tallózással keresse meg az `http://localhost:5000` első lépéseket.

A SZÁMJEGYek a Conda-gyökér környezetében is Python-modulként települnek.

### <a name="tensorflow"></a>TensorFlow

A TensorFlow a Google mély tanulási könyvtára. Ez egy nyílt forráskódú, az adatfolyam-diagramok használatával numerikus számításhoz használható könyvtár. A TensorFlow a py35 Python-környezetben érhető el, és néhány minta jegyzetfüzetet tartalmaz a JupyterHub.

### <a name="theano"></a>Theano

A theano egy Python-könyvtár a hatékony numerikus számításokhoz. Ez a gyökér-és py35 Python-környezetekben érhető el. 

### <a name="torch"></a>Torch

A Torch egy tudományos számítástechnikai keretrendszer, amely széles körű támogatást nyújt a gépi tanulási algoritmusokhoz. A/dsvm/Tools/Torch-ben érhető el, az **interaktív munkamenet** és a LuaRocks csomagkezelő pedig a parancssorban érhető el. A/dsvm/Samples/Torch.-ben elérhető példák

A PyTorch a root anaconda-környezetben is elérhető. Példák a/dsvm/Samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning-kiszolgáló

Az R az adatelemzés és a gépi tanulás egyik legnépszerűbb nyelve. Ha az R-t szeretné használni az elemzéshez, a virtuális gép Microsoft Machine Learning Server a Microsoft R Open és a Math kernel Library használatával. A matematikai kernel könyvtára az analitikai algoritmusokban közös matematikai műveleteket optimalizálja. A Microsoft R Open 100 százalékkal kompatibilis a CRAN R-vel, és a CRANban közzétett R-kódtárak bármelyike telepíthető a Microsoft R Open szolgáltatásba. 

A Machine Learning Server az R-modellek méretezését és operacionalizálási is lehetővé teszi a webszolgáltatásokban. Az R-programokat szerkesztheti az egyik alapértelmezett szerkesztőben, például a RStudio, a VI vagy az Emacs használatával. Ha inkább az Emacs-szerkesztőt használja, a rendszer előre telepítette. Az Emacs ESS (Emacs Speaks Statistics) csomag leegyszerűsíti az R-fájlok használatát az Emacs editoron belül.

Az R-konzol megnyitásához írja be az **r** értéket a rendszerhéjba. Ez a parancs interaktív környezetbe kerül. Az R-program fejlesztéséhez általában egy olyan szerkesztőt használ, mint például az Emacs vagy a VI, majd futtatnia kell a parancsfájlokat az R-n belül. A RStudio teljes grafikus IDE-val rendelkezik az R-program fejlesztéséhez.

Az R-szkripttel az [első 20 R-csomagot](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) is telepítheti, ha szeretné. Ezt a szkriptet akkor futtathatja, ha az R interaktív felületén van. Ahogy korábban említettük, a felületet megnyithatja az **R** megadásával a shellben.  

## <a name="python"></a>Python

A anaconda Python a Python 2,7-es és 3,5-es környezetekkel van telepítve. Az 2,7-es környezet neve _root_, a 3,5-környezet neve pedig _py35_. Ez a disztribúció tartalmazza a Python alapját, valamint a legnépszerűbb matematikai, mérnöki és adatelemzési csomagokat 300.

A py35-környezet az alapértelmezett. A root (2,7) környezet aktiválásához használja a következő parancsot:

```bash
source activate root
```

A py35-környezet ismételt aktiválásához használja a következő parancsot:

```bash
source activate py35
```

A Python interaktív munkamenetének meghívásához írja be a **Pythont** a rendszerhéjba. 

További Python-kódtárak telepítése a Conda vagy a pip használatával. A pip esetében először aktiválja a megfelelő környezetet, ha nem szeretné, hogy az alapértelmezett:

```bash
source activate root
pip install <package>
```

Vagy a pip teljes elérési útját adhatja meg:

```bash
/anaconda/bin/pip install <package>
```

A Conda esetében mindig adja meg a környezet nevét (py35 vagy root):

```bash
conda install <package> -n py35
```

Ha grafikus felületen vagy X11-továbbítással rendelkezik, a **notebookshoz** megadásával nyithatja meg a NOTEBOOKSHOZ Python ide-t. Használhatja az alapértelmezett szövegszerkesztőket. Emellett a Spyder, a Python IDE-t is használhatja, amely az anaconda Python-disztribúciókkal együtt van csomagolva. A Spyder-nek grafikus asztali vagy X11-továbbításra van szüksége. A grafikus asztali számítógép a Spyder-ra mutató parancsikont tartalmaz.

## <a name="jupyter-notebook"></a>Jupyter notebook

Az anaconda-disztribúció egy Jupyter-jegyzetfüzettel, egy olyan környezettel is rendelkezik, amely megosztja a kódot és az elemzést. A Jupyter notebook a JupyterHub-on keresztül érhető el. A bejelentkezéshez a helyi Linux-felhasználónevet és-jelszót kell használnia.

A Jupyter notebook-kiszolgáló előre konfigurálva lett a Python 2, a Python 3 és az R kernelrel. A **Jupyter notebook** asztal ikon segítségével nyissa meg a böngészőt, és nyissa meg a notebook-kiszolgálót. Ha a virtuális gépet SSH-n vagy a X2Go-ügyfélen keresztül futtatja, akkor a Jupyter notebook-kiszolgáló is elérhető a következő helyen: `https://localhost:8000/` .

> [!NOTE]
> Ha bármilyen tanúsítványra vonatkozó figyelmeztetést kap, folytassa a művelettel.

A Jupyter notebook-kiszolgálót bármely gazdagépről elérheti. Adja meg a **https:// \<VM DNS name or IP address\> : 8000/**.

> [!NOTE]
> A 8000-es port alapértelmezés szerint a tűzfalon van megnyitva, amikor a virtuális gép ki van építve. 

Becsomagoltunk egy minta jegyzetfüzetet – egy Pythonban és egy az R-ben. A minták hivatkozását a notebook kezdőlapján láthatja, miután a helyi linuxos felhasználónevével és jelszavával hitelesíti magát a Jupyter notebookon. Új jegyzetfüzet létrehozásához válassza az **új** lehetőséget, majd válassza ki a megfelelő nyelvi kernelt. Ha nem jelenik meg az **új** gomb, a bal felső sarokban található **Jupyter** ikonra kattintva lépjen a notebook-kiszolgáló kezdőlapjára.

## <a name="apache-spark-standalone"></a>Önálló Apache Spark

A Linux DSVM a Apache Spark önálló példánya van előtelepítve a Spark-alkalmazások helyi fejlesztéséhez a nagyméretű fürtökön való tesztelés és üzembe helyezésük előtt. 

A PySpark-programokat a Jupyter kernelen keresztül is futtathatja. A Jupyter megnyitásakor válassza az **új** gombot, és az elérhető kernelek listáját kell megjelennie. A **Spark-Python** a PySpark kernel, amellyel Spark-alkalmazásokat hozhat létre a Python nyelv használatával. A Spark-programot a Python IDE, például a Notebookshoz vagy a Spyder használatával is létrehozhatja. 

Ebben az önálló példányban a Spark stack a hívó ügyfélprogramon belül fut. Ez a funkció a Spark-fürtön való fejlesztéshez képest gyorsabbá és egyszerűbbé teszi a problémák megoldását.

A Jupyter egy minta PySpark notebookot biztosít. A SparkML könyvtárban található a Jupyter ($HOME/notebooks/SparkML/pySpark) könyvtárában. 

Ha az R for Spark-t használja, használhatja a Microsoft Machine Learning Server, a Sparker vagy a sparklyr. 

Mielőtt a-t a Microsoft Machine Learning Server Spark-környezetében futtatja, egy egyszeri telepítési lépést kell elvégeznie, amely lehetővé teszi egy helyi egycsomópontos Hadoop-HDFS és egy fonal-példány engedélyezését. Alapértelmezés szerint a Hadoop Services telepítve van, de le van tiltva a DSVM. Az engedélyezéshez először a következő parancsokat kell futtatnia az első alkalommal:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

A Hadoop kapcsolódó szolgáltatásokat leállíthatja, ha a futtatásával nincs szüksége rájuk ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

A/dsvm/samples/MRS könyvtár egy olyan mintát biztosít, amely bemutatja, hogyan fejleszthető és tesztelhető Microsoft Machine Learning Server egy távoli Spark-környezetben (az önálló Spark-példány a DSVM).

## <a name="ides-and-editors"></a>Ide és szerkesztők

Számos Kódszerkesztő közül választhat, például a VI/Vim, az Emacs, a Notebookshoz, a RStudio és a IntelliJ. 

A Notebookshoz, a RStudio és a IntelliJ grafikus szerkesztők. A használatához be kell jelentkeznie egy grafikus asztalra. Az asztal és az alkalmazás menü parancsikonjainak használatával nyithatja meg őket.

A Vim és az Emacs szöveg-alapú szerkesztők. Az Emacs-ben az ESS-bővítmény csomag megkönnyíti az R használatát az Emacs editoron belül. További információkat az [ESS webhelyén](https://ess.r-project.org/)talál.

A LaTex a texlive-csomagon keresztül települ, valamint egy [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html)nevű Emacs-bővítményi csomaggal. Ez a csomag egyszerűsíti a LaTex-dokumentumok készítését az Emacson belül.  

## <a name="databases"></a>Adatbázisok

### <a name="graphical-sql-client"></a>Grafikus SQL-ügyfél

A mókus SQL egy grafikus SQL-ügyfél, amely különböző adatbázisokhoz (például Microsoft SQL Server és MySQL) tud csatlakozni, és SQL-lekérdezéseket futtat. A mókus SQL-t futtathatja grafikus asztali munkamenetből (például a X2Go-ügyfélen keresztül) egy asztali ikon használatával. Vagy futtathatja az ügyfelet a következő parancs használatával a rendszerhéjban:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Az első használat előtt állítsa be az illesztőprogramokat és az adatbázis-aliasokat. A JDBC-illesztőprogramok a következő helyen találhatók:/usr/share/Java/jdbcdrivers.

További információ: [mókus SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Microsoft SQL Serverhoz való hozzáféréshez használható parancssori eszközök

A SQL Server ODBC-illesztőprogram-csomagja két parancssori eszközt is tartalmaz:

- **BCP**: a BCP-eszköz tömegesen másol egy Microsoft SQL Server egy példánya és egy, a felhasználó által megadott formátumban tárolt adatfájl között. A BCP eszközzel nagy számú új sort importálhat SQL Server táblákba, vagy exportálhatja a táblákból az adatokból az adatfájlokat. Az adattáblázatba való importáláshoz az adott táblához létrehozott formátumú fájlt kell használnia. Vagy tisztában kell lennie a tábla struktúrájával és az oszlopokhoz érvényes adattípusokkal.

  További információ: [Csatlakozás a BCP-vel](/sql/connect/odbc/linux-mac/connecting-with-bcp).

- **Sqlcmd**: a Sqlcmd eszközzel megadhatja a Transact-SQL-utasításokat. A parancssorban megadhatja a rendszerfolyamatokat és a parancsfájlokat is. Ez az eszköz az ODBC-t használja a Transact-SQL-kötegek futtatásához.

  További információ: [Csatlakozás a Sqlcmd](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)-hoz.

  > [!NOTE]
  > Ez az eszköz a Linux és a Windows platform közötti különbségeket is tárgyalja. A részletekért tekintse meg a dokumentációt.

### <a name="database-access-libraries"></a>Adatbázis-hozzáférési kódtárak

A kódtárak az R és a Python alkalmazásban érhetők el az adatbázis-hozzáféréshez:

* Az R-ben a RODBC csomag vagy dplyr csomag használatával kérdezheti le és futtathatja az SQL-utasításokat az adatbázis-kiszolgálón.
* A Pythonban a pyodbc függvénytár adatbázis-hozzáférést biztosít az ODBC-vel az alapul szolgáló rétegként.  

## <a name="azure-tools"></a>Azure-eszközök

A következő Azure-eszközök vannak telepítve a virtuális gépre:

* **Azure CLI**: az Azure parancssori felületének használatával Azure-erőforrásokat hozhat létre és kezelhet a rendszerhéj-parancsokkal. Az Azure-eszközök megnyitásához adja meg az **Azure súgóját**. További információkért tekintse meg az [Azure CLI dokumentációs oldalát](/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer**: a Azure Storage Explorer egy grafikus eszköz, amellyel böngészhet az Azure Storage-fiókban tárolt objektumokon, valamint az Azure-blobokból és az onnan érkező adatok feltöltésével és letöltésével. A Storage Explorer az asztali parancsikon ikonján keresztül érheti el. A **StorageExplorer** megadásával is megnyithatja azt egy rendszerhéj-parancssorból. Be kell jelentkeznie egy X2Go-ügyfélről, vagy be kell állítania az X11-továbbítást.
* **Azure-kódtárak**: az alábbiakban néhány előre telepített függvénytár található.
  
  * **Python**: az Azure-hoz kapcsolódó kódtárak a Pythonban az *Azure*, a *azureml*, a *pydocumentdb* és a *pyodbc*. Az első három függvénytárban hozzáférhet az Azure Storage szolgáltatásaihoz, Azure Machine Learninghoz és Azure Cosmos DBhoz (NoSQL-adatbázis az Azure-ban). A pyodbc (a SQL Server Microsoft ODBC-illesztővel együtt) negyedik könyvtára lehetővé teszi a SQL Server, a Azure SQL Database és az Azure szinapszis-elemzések elérését a Pythonból egy ODBC-felület használatával. Az összes felsorolt könyvtár megjelenítéséhez adja meg a **pip-listát** . Mindenképpen futtassa ezt a parancsot a Python 2,7-es és a 3,5-es környezetekben.
  * **R**: az Azure-beli, r-hez kapcsolódó kódtárak a AzureML és a RODBC.
  * **Java**: az Azure Java-kódtárak listája a virtuális gép címtárának/dsvm/SDK/AzureSDKJava található. A legfontosabb kódtárak az Azure Storage és a Management API-k, a Azure Cosmos DB és a SQL Server JDBC-illesztőprogramjai.  

A [Azure Portal](https://portal.azure.com) az előre telepített Firefox böngészőből érhető el. A Azure Portal az Azure-erőforrások létrehozását, kezelését és figyelését is elvégezheti.

## <a name="azure-machine-learning"></a>Azure Machine Learning

A Azure Machine Learning egy teljes körűen felügyelt felhőalapú szolgáltatás, amely lehetővé teszi prediktív elemzési megoldások készítését, üzembe helyezését és megosztását. A kísérletek és modellek a Azure Machine Learning Studióban (előzetes verzió) is létrehozhatók. A Data Science Virtual Machine webböngészőjéből elérheti a [Microsoft Azure Machine learning](https://ml.azure.com)meglátogatásával.

Miután bejelentkezett Azure Machine Learning studióba, a kísérletezési vászon használatával létrehozhat egy logikai folyamatot a gépi tanulási algoritmusokhoz. A Azure Machine Learning üzemeltetett Jupyter-jegyzetfüzethez is hozzáférhet, és zökkenőmentesen dolgozhat Azure Machine Learning Studióban végzett kísérletekkel. 

Működővé tenni azokat a gépi tanulási modelleket, amelyeket a webszolgáltatási felületen való becsomagolással készített. A végrehajtott Machine learning-modellek lehetővé teszik bármely nyelven írt ügyfelek számára az előrejelzések meghívását ezekből a modellből. További információ: [Machine learning dokumentáció](https://azure.microsoft.com/documentation/services/machine-learning/).

Az R-vagy Python-modelleket a virtuális gépen is létrehozhatja, majd üzembe helyezheti őket éles környezetben Azure Machine Learningon. A funkció engedélyezéséhez az R (**AzureML**) és a Python (**AzureML**) függvénytárait telepítettük.

> [!NOTE]
> Ezek az utasítások a Data Science Virtual Machine Windows-verziójára lettek írva. A modelleknek a Azure Machine Learning való üzembe helyezésére vonatkozó információk azonban a Linux rendszerű virtuális gépre érvényesek.

## <a name="machine-learning-tools"></a>Gépi tanulási eszközök

A virtuális gép olyan gépi tanulási eszközöket és algoritmusokat tartalmaz, amelyek előre le lettek fordítva, és helyileg lettek telepítve. Ezek a következők:

* **Vowpal Wabbit**: gyors online tanulási algoritmus.
* **xgboost**: olyan eszköz, amely optimalizált, növelt fa algoritmusokat biztosít.
* **Csörgő**: egy R-alapú grafikus eszköz, amely megkönnyíti az adatfeltárást és a modellezést.
* **Python**: a anaconda Python a gépi tanulási algoritmusokkal, például a Scikit-Learn csomaggal rendelkezik. A parancs használatával más kódtárakat is telepíthet `pip install` .
* **LightGBM**: gyors, elosztott, nagy teljesítményű színátmenet-növelő keretrendszer döntési fa algoritmusok alapján.
* **R**: a gépi tanulási függvények széles tárháza érhető el az r-hez. az előre telepített kódtárak közé tartozik az LM, a GLM, a randomForest és a rpart. A következő parancs futtatásával más kódtárakat is telepíthet:

    ```r
    install.packages(<lib name>)
    ```

Íme néhány további információ a lista első három gépi tanulási eszközéről.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

A Vowpal Wabbit egy gépi tanulási rendszer, amely olyan technikákat használ, mint például az online, a kivonatolás, a allreduce, a csökkenés, a learning2search, az aktív és az interaktív tanulás.

Az eszköz alapszintű példán való futtatásához használja a következő parancsokat:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Ebben a könyvtárban más, nagyobb bemutatók is szerepelnek. A Vowpal Wabbit kapcsolatos további információkért tekintse meg a GitHub és a [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki) [jelen szakaszát](https://github.com/JohnLangford/vowpal_wabbit) .

### <a name="xgboost"></a>xgboost

A xgboost-könyvtár kialakítva és optimalizálva lett a kifejleszthető (fa) algoritmusokhoz. Ennek a könyvtárnak a célja, hogy leküldse a gépek számítási korlátait a nagy léptékű faszerkezetes növekedéshez, amely méretezhető, hordozható és pontos.

Ez a parancs parancssorként és R-függvénytárként is elérhető. Ha ezt a kódtárat szeretné használni az R-ben, elindíthat egy interaktív R-munkamenetet (az **r** beírásával a rendszerhéjban), és betöltheti a könyvtárat.

Íme egy egyszerű példa, amely az R-parancssorban is futtatható:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

A xgboost parancssor futtatásához a következő parancsokat kell futtatni a rendszerhéjban:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

A rendszer a megadott könyvtárba írja a. Model fájlt. Erről a bemutató példáról [a githubon](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification)talál információt.

A xgboost kapcsolatos további információkért tekintse meg a [xgboost dokumentációs oldalát](https://xgboost.readthedocs.org/en/latest/) és a [GitHub-tárházat](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

A csörgő (az **R** **a** analitikus **t** OOL **t** o **L** keres **E** asily) GUI-alapú adatelemzést és modellezést használ. Az adatok statisztikai és vizuális összefoglalóit, a könnyen modellezhető adatok átalakítását, a nem felügyelt és a felügyelt modelleket is felépíti az adatokból, a modellek grafikus megjelenítését, valamint az új adatkészletek pontszámát mutatja be. Emellett R-kódot is létrehoz, replikálja a műveleteket a felhasználói felületen, amely közvetlenül az R-ben futtatható, vagy további elemzés céljából használható kiindulási pontként.

A csörgő futtatásához grafikus asztali bejelentkezési munkamenetben kell lennie. A terminálon írja be az **r** parancsot az r-környezet megnyitásához. Az R-parancssorba írja be a következő parancsokat:

```R
library(rattle)
rattle()
```

Ekkor megnyílik egy grafikus felület, amely fülekből áll. A következő rövid útmutató lépéseit használhatja a Csörgőben egy minta időjárási adathalmaz használatához és egy modell létrehozásához. Néhány lépés során a rendszer felszólítja, hogy automatikusan telepítsen és töltsön be néhány szükséges R-csomagot, amely még nem szerepel a rendszeren.

> [!NOTE]
> Ha nem rendelkezik hozzáféréssel a csomag telepítéséhez a rendszerkönyvtárban (ez az alapértelmezett beállítás), az R-konzolon megjelenik egy üzenet, amely a csomagokat a személyes tárba telepíti. Ha ezeket az utasításokat látja, válaszoljon az **y** -re.

1. Válassza a **Végrehajtás** lehetőséget.
1. Megjelenik egy párbeszédpanel, amely megkérdezi, hogy szeretné-e használni a példa időjárási adatkészletet. A példa betöltéséhez válassza az **Igen** lehetőséget.
1. Válassza a **modell** fület.
1. Döntési fa létrehozásához válassza a **végrehajtás** elemet.
1. Válassza a **Rajzolás** lehetőséget a döntési fa megjelenítéséhez.
1. Válassza az **erdő** lehetőséget, majd válassza a **végrehajtás** lehetőséget egy véletlenszerű erdő kiépítéséhez.
1. Válassza ki a **kiértékelés** lapot.
1. Válassza ki a **kockázati** lehetőséget, majd válassza a **végrehajtás** elemet a két **kockázatos (halmozott)** teljesítménybeli ábrázolás megjelenítéséhez.
1. Válassza a **napló** lapot, hogy megjelenjen az előző műveletekhez generált R-kód.
   (A csörgő jelenlegi kiadásának hibája miatt be kell szúrnia egy karaktert a naplóba a napló **#** szövegének **Exportálás** előtt.)
1. A weather_script nevű R-parancsfájl mentéséhez kattintson az **Exportálás** gombra *. Az R* -t a kezdőmappa mappájába.

Kiléphet a csörgő és az R lehetőségből. Most már módosíthatja a generált R-szkriptet. Használhatja a parancsfájlt is, és bármikor futtathatja, hogy megismételje a csörgő felhasználói felületen végzett összes műveletet. Különösen az R-ben kezdőknek, így gyorsan elvégezheti az elemzést és a gépi tanulást egy egyszerű grafikus felületen, az R-ben pedig automatikusan generálhat kódot a módosításhoz vagy a megismeréshez.

## <a name="next-steps"></a>Következő lépések

További kérdései vannak? Hozzon létre egy [támogatási jegyet](https://azure.microsoft.com/support/create-ticket/).