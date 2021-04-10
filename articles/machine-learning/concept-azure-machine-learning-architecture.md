---
title: Architektúra & főbb fogalmak
titleSuffix: Azure Machine Learning
description: Ez a cikk a Azure Machine Learningt alkotó architektúra, feltételek és fogalmak magas szintű megismerését ismerteti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: dc1954c97da0d7f40deaf0f4efa7ca99793107bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503691"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>A Azure Machine Learning működése: architektúra és fogalmak

Ismerje meg a [Azure Machine learning](overview-what-is-azure-ml.md)architektúráját és fogalmait.  Ez a cikk az összetevők magas szintű megismerését és azok együttműködésének módját ismerteti a gépi tanulási modellek létrehozásának, üzembe helyezésének és karbantartásának folyamatában.

## <a name="workspace"></a><a name="workspace"></a> Munkaterület

A [Machine learning-munkaterület](concept-workspace.md) a Azure Machine learning legfelső szintű erőforrása.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Ábra: a munkaterület és összetevői Azure Machine Learning architektúrája":::

A munkaterület a következő központi hely:

* Kezelheti a modellek betanításához és üzembe helyezéséhez használt erőforrásokat, például a [számításokat](#compute-instance)
* Tárolja a Azure Machine Learning használatakor létrehozott eszközöket, beleértve a következőket:
  * [Környezetek](#environments)
  * [Kísérletek](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Adatkészletek](#datasets-and-datastores)
  * [Modellek](#models)
  * [Végpontok](#endpoints)

A munkaterület más Azure-erőforrásokat is tartalmaz, amelyeket a munkaterület használ:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): regisztrálja a betanítás során használt Docker-tárolókat, és amikor üzembe helyez egy modellt. A költségek csökkentése érdekében az ACR csak a telepítési lemezképek létrehozásakor jön létre.
+ [Azure Storage-fiók](https://azure.microsoft.com/services/storage/): a munkaterület alapértelmezett adattára.  A Azure Machine Learning számítási példányokhoz használt Jupyter-jegyzetfüzeteket is itt tárolja.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): a modellekkel kapcsolatos figyelési információkat tárolja.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): a számítási célok és a munkaterület által igényelt egyéb bizalmas információk által használt titkokat tárolja.

A munkaterületeket másokkal is megoszthatja.

## <a name="computes"></a>Kiszámítja

<a name="compute-targets"></a> A [számítási cél](concept-compute-target.md) bármely olyan számítógép vagy gép, amelyet a betanítási parancsfájl futtatásához vagy a szolgáltatás központi telepítésének üzemeltetéséhez használ. Számítási célként a helyi gépet vagy egy távoli számítási erőforrást is használhat.  A számítási célokkal megkezdheti a képzést a helyi gépen, majd kibővítheti a felhőt a betanítási szkript módosítása nélkül.

A Azure Machine Learning a gépi tanulási feladatokhoz konfigurált két teljes körűen felügyelt felhőalapú virtuális gépet (VM) mutatja be:

* <a name="compute-instance"></a>**Számítási példány**: a számítási példány egy olyan virtuális gép, amely a gépi tanuláshoz telepített több eszközt és környezetet is tartalmaz. A számítási példány elsődleges használata a fejlesztési munkaállomás számára szükséges.  Megkezdheti a telepítés megkezdése nélkül megjelenő minta-jegyzetfüzetek futtatását. A számítási példányok számítási célként is használhatók a betanítási és a következtetési feladatokhoz.

* **Számítási fürtök**: a számítási fürtök több csomópontos skálázási képességekkel rendelkező virtuális gépekből álló fürtök. A számítási fürtök jobban illeszkednek a nagyméretű feladatok és az éles számítási célokhoz.  A fürt automatikusan méretezi a feladatokat, amikor elküld egy feladatot.  Használjon képzési számítási célt vagy fejlesztési/tesztelési célú üzembe helyezést.

A számítási célok betanításával kapcsolatos további információkért lásd: [számítási célok betanítása](concept-compute-target.md#train).  További információ az üzembe helyezési számítási célokról: [telepítési célok](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Adatkészletek és adattárolók

[**Azure Machine learning adatkészletek**](concept-data.md#datasets)  megkönnyítik az adataik elérését és a velük való munkát. Adatkészlet létrehozásával az adatforrás helyére mutató hivatkozást hozhat létre, valamint a metaadatok másolatát is. Mivel az adatok a meglévő helyükön maradnak, nem számítunk fel extra tárolási költséget, és nem kockáztatjuk az adatforrások integritását.

További információ: [Azure Machine learning adatkészletek létrehozása és regisztrálása](how-to-create-register-datasets.md).  Az adatkészletek használatával kapcsolatos további példákért tekintse meg a [minta jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Az adatkészletek az adattárakat [használják az Azure Storage-](concept-data.md#datastores) szolgáltatásokhoz való biztonságos kapcsolódásra. Az adattárolók a hitelesítési hitelesítő adatok és az eredeti adatforrások integritásának veszélyeztetése nélkül tárolják a kapcsolatok adatait. A kapcsolati adatokat, például az előfizetési AZONOSÍTÓját és a jogkivonat-engedélyezést a munkaterülethez társított Key Vault tárolják, így biztonságosan hozzáférhet a tárolóhoz anélkül, hogy a parancsfájlban rögzített kódokat kellene megtennie.

## <a name="environments"></a>Környezetek

[Munkaterület](#workspace)  >  **Környezetek**

A [környezet](concept-environments.md) az a környezet beágyazása, ahol a gépi tanulási modell betanítása vagy pontozása zajlik. A környezet meghatározza a Python-csomagokat, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré.  

A kódok használatával kapcsolatban tekintse meg a [környezetek használatáról](how-to-use-environments.md#manage-environments)szóló témakör "környezetek kezelése" című szakaszát.

## <a name="experiments"></a>Kísérletek

[Munkaterület](#workspace)  >  **Kísérletek**

A kísérlet egy adott parancsfájlból származó számos Futtatás csoportosítása. Mindig egy munkaterülethez tartozik. Futtatáskor a kísérlet nevét adja meg. A futtatással kapcsolatos információkat a kísérlet alatt tárolja a rendszer. Ha a név nem létezik a kísérlet elküldésekor, automatikusan létrejön egy új kísérlet.
  
Egy kísérletre példaként tekintse meg az [oktatóanyag: az első modell betanítása](tutorial-1st-experiment-sdk-train.md)című témakört.

### <a name="runs"></a>Futtatás

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  **Futtatás**

A Futtatás egy képzési parancsfájl egyetlen végrehajtása. Egy kísérlet általában több futtatást is tartalmaz.

Azure Machine Learning rögzíti az összes futtatást, és a következő információkat tárolja a kísérletben:

* A futtatással kapcsolatos metaadatok (timestamp, időtartam stb.)
* A parancsfájl által naplózott metrikák
* A kísérlet által összegyűjtött vagy kifejezetten az Ön által feltöltött kimeneti fájlok
* A futtatást megelőzően a parancsfájlokat tartalmazó könyvtár pillanatképe

A futtatást akkor kell létrehoznia, ha parancsfájlt küld a modell betanításához. Egy Futtatás nulla vagy több gyermek futtatásával is rendelkezhet. Előfordulhat például, hogy a legfelső szintű Futtatás két gyermeket futtat, amelyek mindegyike rendelkezhet saját gyermekével.

### <a name="run-configurations"></a>Konfigurációk futtatása

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  [Futtatás](#runs)  >  **Konfiguráció futtatása**

A futtatási konfiguráció határozza meg, hogyan fusson egy parancsfájl egy adott számítási célhelyen. A konfiguráció segítségével megadhatja a parancsfájlt, a számítási célt és az Azure ML-környezetet a futtatáshoz, a feladatokra vonatkozó elosztott konfigurációkat és néhány további tulajdonságot. További információ a futtatások konfigurálható lehetőségeinek teljes készletéről: [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig).

A futtatási konfiguráció a betanítási parancsfájlt tartalmazó könyvtárban található fájlban maradhat.   Vagy egy memóriában tárolt objektumként is létrehozható, és futtatásra is elküldhető.

Példa a futtatási konfigurációkra: [képzések futtatásának konfigurálása](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Pillanatképek

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  [Futtatás](#runs)  >  **Pillanatkép**

Amikor elküld egy futtatást, Azure Machine Learning tömöríti azt a könyvtárat, amely a parancsfájlt zip-fájlként tartalmazza, és elküldi a számítási célnak. Ekkor a rendszer kibontja a zip-fájlt, és ott futtatja a parancsfájlt. Azure Machine Learning a zip-fájlt pillanatképként tárolja a futtatási rekord részeként. Bárki, aki hozzáféréssel rendelkezik a munkaterülethez, böngészhet egy futtatási rekordot, és letöltheti a pillanatképet.

### <a name="logging"></a>Naplózás

Azure Machine Learning automatikusan naplózza a szabványos futtatási metrikákat. [A PYTHON SDK használatával azonban tetszőleges mérőszámokat is naplózhat](how-to-track-experiments.md).

Több módon is megtekintheti a naplókat: valós időben figyelheti a futtatási állapotot, vagy megtekintheti az eredményeket a befejezés után. További információkért lásd: [ml-futtatási naplók figyelése és megtekintése](how-to-monitor-view-training-logs.md).


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. Ez a parancsfájl-futtatási konfiguráció vagy a ML-folyamat használatával elküldött futtatásokkal működik. Az SDK-ból vagy Machine Learning parancssori felületről küldött futtatások esetén is működik.

További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

### <a name="training-workflow"></a>Betanítási munkafolyamat

Amikor kísérletet futtat egy modell betanítására, a következő lépések történnek. Ezeket az alábbi betanítási munkafolyamat-diagram szemlélteti:

* A Azure Machine Learning a rendszer az előző szakaszban mentett kód pillanatképéhez tartozó pillanatkép-AZONOSÍTÓval hívja meg.
* Azure Machine Learning létrehoz egy futtatási azonosítót (nem kötelező) és egy Machine Learning szolgáltatási jogkivonatot, amelyet később a számítási célok, például a Machine Learning Compute/virtuális gépek használják a Machine Learning szolgáltatással való kommunikációhoz.
* Kiválaszthat egy felügyelt számítási célt (például Machine Learning Compute) vagy egy nem felügyelt számítási célt (például virtuális gépeket) a betanítási feladatok futtatásához. Mindkét forgatókönyv esetén az alábbi adatfolyamatok érhetők el:
   * Virtuális gépek/HDInsight, a Microsoft-előfizetés egyik kulcstartójában elérhető SSH hitelesítő adatokkal. Azure Machine Learning a következő számítási célra futtatja a felügyeleti kódot:

   1. Előkészíti a környezetet. (A Docker lehetőséget biztosít a virtuális gépek és a helyi számítógépek számára. A következő lépésekből megtudhatja, hogyan működik a kísérletek futtatása a Docker-tárolókban Machine Learning Compute.)
   1. Letölti a kódot.
   1. Beállítja a környezeti változókat és a konfigurációkat.
   1. Futtatja a felhasználói parancsfájlokat (az előző szakaszban említett kód-pillanatképet).

   * Machine Learning Compute, amely egy munkaterület által felügyelt identitáson keresztül érhető el.
Mivel Machine Learning Compute felügyelt számítási cél (azaz a Microsoft felügyeli), a Microsoft-előfizetése alatt fut.

   1. Ha szükséges, a távoli Docker-konstrukció indult el.
   1. A felügyeleti kód a felhasználó Azure Files megosztására íródik.
   1. A tároló egy kezdeti paranccsal indult el. Ez a felügyeleti kód az előző lépésben leírtak szerint.

* A futtatást követően lekérdezheti a futtatott és a metrikákat. Az alábbi folyamatábrán ez a lépés akkor következik be, amikor a betanítási cél a futtatási metrikákat a Cosmos DB-adatbázisban lévő tárterületről Azure Machine Learningra írja vissza. Az ügyfelek hívhatják Azure Machine Learning. Machine Learning a Cosmos DB-adatbázisból lekéri a metrikákat, majd visszaküldi azokat az ügyfélnek.

[![Betanítási munkafolyamat](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modellek

Legegyszerűbben a modell egy olyan kódrészlet, amely bemenetet és kimenetet hoz létre. A gépi tanulási modell létrehozásához ki kell választania egy algoritmust, amely lehetővé teszi az adatgyűjtést és a [hiperparaméterek beállítása hangolását](how-to-tune-hyperparameters.md). A képzés egy olyan ismétlődő folyamat, amely egy betanított modellt hoz létre, amely bemutatja a modell betanítási folyamatát.

Azure Machine Learningon kívül betanított modellt is létrehozhat. A modellt úgy is betaníthatja, hogy elküld egy [kísérlet](#experiments) [futtatását](#runs) egy [számítási célra](#compute-targets) Azure Machine Learningban. A modell megkezdése után [regisztrálja a modellt](#register-model) a munkaterületen.

Azure Machine Learning a Framework agnosztikus. Modell létrehozásakor bármilyen népszerű Machine learning-keretrendszert használhat, például a Scikit-Learn, a XGBoost, a PyTorch, a TensorFlow és a Chainer platformot.

Példa a modell Scikit-Learn használatával történő betanítására [: oktatóanyag: képbesorolási modell Betanítása Azure Machine Learningsal](tutorial-train-models-with-aml.md).


### <a name="model-registry"></a><a name="register-model"></a> Modell beállításjegyzéke

[Munkaterület](#workspace)  >  **Modellek**

A **modell beállításjegyzékében** nyomon követheti a Azure Machine learning munkaterület összes modelljét.

A modelleket név és verzió alapján azonosítjuk. Minden alkalommal, amikor egy olyan modellt regisztrál, amelynek a neve megegyezik egy meglévővel, a beállításjegyzék feltételezi, hogy ez egy új verzió. A verzió növekményes, és az új modell ugyanazon a néven van regisztrálva.

A modell regisztrálása esetén további metaadat-címkéket is megadhat, majd használhatja a címkéket a modellek keresésekor.

> [!TIP]
> A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a Azure Machine Learning munkaterületen. A regisztráció után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

Egy aktív telepítés által használt regisztrált modell nem törölhető.

A modellek regisztrálására példát a [képbesorolási modell Betanítása Azure Machine Learningkal](tutorial-train-models-with-aml.md)című témakörben talál.

## <a name="deployment"></a>Üzembe helyezés

A [regisztrált modellt](#register-model) szolgáltatás-végpontként kell üzembe helyezni. A következő összetevőkre van szüksége:

* **Környezet**. Ez a környezet beágyazza a modellnek a következtetéshez való futtatásához szükséges függőségeket.
* **Pontozási kód**. Ez a szkript fogadja a kéréseket, a modell használatával szerzi be a kérelmeket, és visszaadja az eredményeket.
* **Következtetési konfiguráció**. A következtetési konfiguráció határozza meg a környezetet, a bejegyzés parancsfájlját és a modell szolgáltatásként való futtatásához szükséges egyéb összetevőket.

További információ ezekről az összetevőkről: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Végpontok

[Munkaterület](#workspace)  >  **Végpontok**

A végpontok a modell példányai egy olyan webszolgáltatásba, amely a felhőben vagy egy IoT modulban üzemeltethető az integrált eszközök üzembe helyezéséhez.

#### <a name="web-service-endpoint"></a>Webszolgáltatás végpontja

A modell webszolgáltatásként való telepítésekor a végpont Azure Container Instances, Azure Kubernetes Service-ben vagy FPGA-ben is üzembe helyezhető. A szolgáltatást a modellből, a parancsfájlból és a társított fájlokból hozza létre. Ezek egy alap tároló-rendszerképbe kerülnek, amely a modell végrehajtási környezetét tartalmazza. A rendszerkép egy elosztott terhelésű, HTTP-végponttal rendelkezik, amely a webszolgáltatásnak küldött pontozási kérelmeket fogadja.

A webszolgáltatások figyeléséhez Application Insights telemetria vagy Model telemetria is engedélyezheti. A telemetria-adatbázis csak Ön számára érhető el.  A rendszer a Application Insights és a Storage-fiók példányaiban tárolja. Ha engedélyezte az automatikus skálázást, az Azure automatikusan méretezi az üzembe helyezést.

Az alábbi ábrán egy webszolgáltatási végpontként üzembe helyezett modell következtetési munkafolyamata látható:

A részletek a következők:

* A felhasználó egy olyan ügyfél használatával regisztrálja a modellt, mint a Azure Machine Learning SDK-t.
* A felhasználó létrehoz egy rendszerképet egy modell, egy pontszám-fájl és más modell függőségei használatával.
* A Docker-rendszerkép létrehozása és tárolása Azure Container Registryban történik.
* A webszolgáltatás üzembe helyezése a számítási célra (Container Instances/ak) történik az előző lépésben létrehozott rendszerkép használatával.
* A pontozási kérés részleteit Application Insights tárolja a rendszer, amely a felhasználó előfizetésében található.
* A telemetria a Microsoft/Azure-előfizetésre is leküldve.

[![Következtetési munkafolyamat](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Egy modell webszolgáltatásként való üzembe helyezésére példa: [lemezkép besorolási modell telepítése Azure Container Instancesban](tutorial-deploy-models-with-aml.md).

#### <a name="real-time-endpoints"></a>Valós idejű végpontok

Ha a tervezőben egy betanított modellt telepít, [valós idejű végpontként helyezheti üzembe a modellt](tutorial-designer-automobile-price-deploy.md). Egy valós idejű végpont általában egyetlen kérést kap a REST-végponton keresztül, és valós időben előrejelzést ad vissza. Ez ellentétben áll a kötegelt feldolgozással, amely egyszerre több értéket dolgoz fel, és a befejezés után menti az eredményeket egy adattárba.

#### <a name="pipeline-endpoints"></a>Folyamat végpontjai

A folyamat végpontjai lehetővé teszik a [ml-folyamatok](#ml-pipelines) programozott módon való meghívását egy Rest-végponton keresztül. A folyamat végpontjai lehetővé teszik a folyamat munkafolyamatainak automatizálását.

A folyamat végpontja közzétett folyamatok gyűjteménye. Ez a logikai szervezet lehetővé teszi több folyamat kezelését és meghívását ugyanazzal a végponttal. A folyamat végpontján lévő összes közzétett folyamat verziószáma. Kiválaszthatja a végpont alapértelmezett folyamatát, vagy megadhat egy verziót a REST-hívásban.
 

#### <a name="iot-module-endpoints"></a>IoT modul végpontjai

Az üzembe helyezett IoT-végpont egy olyan Docker-tároló, amely tartalmazza a modellt, valamint a hozzá tartozó parancsfájlt vagy alkalmazást, valamint a további függőségeket. Ezeket a modulokat a Azure IoT Edge Edge-eszközökön történő üzembe helyezésével végezheti el.

Ha engedélyezte a figyelést, az Azure a modellből gyűjt telemetria adatokat a Azure IoT Edge modulban. A telemetria adatai csak Ön számára érhetők el, és a Storage-fiók példányában vannak tárolva.

Azure IoT Edge biztosítja, hogy a modul fut, és figyeli az azt üzemeltető eszközt. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

A [Azure Machine learning CLI](reference-azure-machine-learning-cli.md) az Azure CLI egy platformfüggetlen parancssori felülete az Azure platformhoz. Ez a bővítmény parancsokat biztosít a gépi tanulási tevékenységek automatizálásához.

### <a name="ml-pipelines"></a>ML-folyamatok

[A gépi tanulási](concept-ml-pipelines.md) folyamatokkal olyan munkafolyamatokat hozhat létre és kezelhet, amelyek összegyűjtik a gépi tanulási fázisokat. Egy folyamat például magában foglalhatja az adatelőkészítést, a modell betanítását, a modell üzembe helyezését és a következtetések/pontozási fázisokat. Az egyes fázisok több lépést is magukban foglalhatnak, amelyek mindegyike felügyelet nélkül futtatható különböző számítási célokban. 

A folyamat lépései újrafelhasználhatók, és az előző lépések újbóli futtatása nélkül is futtathatók, ha a lépések eredménye nem módosult. Például áttaníthatja a modelleket anélkül, hogy újra kellene futtatnia a költséges adatelőkészítési lépéseket, ha az adatváltozás nem változott. A folyamatok azt is lehetővé teszik, hogy az adatszakértők működjenek együtt a gépi tanulási munkafolyamatok különálló területein való munka során.

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Azure Machine Learning a következő figyelési és naplózási képességeket biztosítja:

* Az __Adatszakértőknél__ nyomon követheti a kísérletek és a naplózási információkat a betanítási futtatásokból. További információért tekintse át a következő cikkeket:
   * [Betanítási futtatások indítása, figyelése és megszakítása](how-to-manage-runs.md)
   * [A betanítási futtatások mérőszámainak naplózása](how-to-track-experiments.md)
   * [Kísérletek nyomon követése az MLflow használatával](how-to-use-mlflow.md)
   * [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)
* A __rendszergazdák__ számára a munkaterületről, a kapcsolódó Azure-erőforrásokról, valamint az olyan eseményekről, mint az erőforrások létrehozásának és törlésének Azure monitor használatával figyelhetők. További információ: [a Azure Machine learning figyelése](monitor-azure-machine-learning.md).
* A __DevOps__ vagy a __MLOps__ a webszolgáltatásként vagy IoT Edge-modulként telepített modellek által létrehozott információkat figyelheti a központi telepítésekkel kapcsolatos problémák azonosításához és a szolgáltatásnak küldött adatok gyűjtéséhez. További információ: a [modell adatainak összegyűjtése](how-to-enable-data-collection.md) és [a figyelés Application Insightssal](how-to-enable-app-insights.md).

## <a name="interacting-with-your-workspace"></a>Interakció a munkaterülettel

### <a name="studio"></a>Studio

[Azure Machine learning Studio](overview-what-is-machine-learning-studio.md) a munkaterületen található összes összetevőt webes nézetben jeleníti meg.  Megtekintheti az adatkészletek, kísérletek, folyamatok, modellek és végpontok eredményeit és részleteit.  A Studióban is kezelheti a számítási erőforrásokat és az adattárolókat.

A Studióban a Azure Machine Learning részét képező interaktív eszközök is elérhetők:

+ [Azure Machine learning Designer](concept-designer.md) a munkafolyamat lépéseinek kód írásához való elvégzése nélkül
+ Webes felület az [automatizált gépi tanuláshoz](concept-automated-ml.md)
+ [Azure Machine learning jegyzetfüzetek](how-to-run-jupyter-notebooks.md) segítségével írhat és futtathat saját kódot az integrált Jupyter notebook-kiszolgálókon.
+ [Adatcímkéző projektek](how-to-create-labeling-projects.md) az adatcímkéző projektek létrehozásához, kezeléséhez és figyeléséhez

### <a name="programming-tools"></a>Programozási eszközök

> [!IMPORTANT]
> Az alábbi megjelölésű eszközök (előzetes verzió) jelenleg nyilvános előzetes verzióban érhetők el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  A szolgáltatással bármilyen Python-környezetben dolgozhat a [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro)-val.
+ A szolgáltatás használata bármely R-környezetben az R- [hez készült Azure Machine learning SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) -val (előzetes verzió).
+ A [Azure Machine learning Designer](concept-designer.md) használatával hajtsa végre a munkafolyamat lépéseit anélkül, hogy kódot kellene írnia. 
+ Az automatizáláshoz használja a [Azure Machine learning CLI](./reference-azure-machine-learning-cli.md) -t.
+ A [számos Modelles megoldás-gyorsító](https://aka.ms/many-models) (előzetes verzió) a Azure Machine Learningra épít, és lehetővé teszi több száz vagy akár több ezer gépi tanulási modell betanítását, üzemeltetését és felügyeletét.

## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning megkezdéséhez tekintse meg a következőt:

* [Mi az Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Azure Machine Learning-munkaterület létrehozása](how-to-manage-workspace.md)
* [Oktatóanyag (1. rész): modell betanítása](tutorial-train-models-with-aml.md)