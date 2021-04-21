---
title: Az & alapfogalmai
titleSuffix: Azure Machine Learning
description: Ez a cikk magas szintű ismereteket nyújt az architektúráról, a fogalmakról és a fogalmakról, amelyekből a Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: f1eb7a5b4697801775d23091c610ab594b0b27ec
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813379"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>A Azure Machine Learning működése: Architektúra és fogalmak

Ismerje meg az architektúrát és a [Azure Machine Learning.](overview-what-is-azure-ml.md)  Ez a cikk magas szintű ismereteket nyújt az összetevőkről és azok együttes munkáiról, amelyek segítenek a gépi tanulási modellek építésében, üzembe helyezésében és karbantartásában.

## <a name="workspace"></a><a name="workspace"></a> Munkaterület

A [gépi tanulási munkaterület](concept-workspace.md) az erőforrások legfelső szintű Azure Machine Learning.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Ábra: Azure Machine Learning architektúra és összetevőinek létrehozása":::

A munkaterület a következő központosított hely:

* A modellek betanítása és üzembe helyezése során használt erőforrások, például számítási [erőforrások kezelése](#compute-instance)
* Az adattitkok használatakor létrehozott Azure Machine Learning, beleértve a következőket:
  * [Környezetek](#environments)
  * [Kísérletek](#experiments)
  * [Pipelines](#ml-pipelines)
  * [Adatkészletek](#datasets-and-datastores)
  * [Modellek](#models)
  * [Végpontok](#endpoints)

A munkaterület a munkaterület által használt egyéb Azure-erőforrásokat is tartalmazza:

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): Regisztrálja a betanítás során és a modell üzembe helyezésekor használt Docker-tárolókat. A költségek minimalizálása érdekében az ACR csak az üzembe helyezési lemezképek létrehozásakor jön létre.
+ [Azure Storage-fiók:](https://azure.microsoft.com/services/storage/)A munkaterület alapértelmezett adattárként szolgál.  A számítási példányokkal használt Jupyter-Azure Machine Learning is itt vannak tárolva.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)A modellek monitorozási információit tárolja.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)A számítási célok által használt titkos adatokat és a munkaterülethez szükséges egyéb bizalmas adatokat tárolja.

Munkaterületet másokkal is megoszthat.

## <a name="computes"></a>Computes (Számítás)

<a name="compute-targets"></a> A [számítási cél](concept-compute-target.md) bármely gép vagy gépcsoport, amely a betanító szkript futtatására vagy a szolgáltatás üzemelő példányának futtatására használható. Használhatja a helyi gépet vagy egy távoli számítási erőforrást számítási célként.  A számítási célokkal elindíthatja a betanításokat a helyi gépen, majd horizontálisan felméretezhet a felhőbe a betanító szkript módosítása nélkül.

Azure Machine Learning két teljes körűen felügyelt, felhőalapú virtuális gépet mutat be, amelyek gépi tanulási feladatokhoz vannak konfigurálva:

* <a name="compute-instance"></a>**Számítási példány:** A számítási példány egy virtuális gép, amely több, a gépi tanuláshoz telepített eszközt és környezetet tartalmaz. A számítási példányok elsődleges használata a fejlesztői munkaállomásra vonatkozik.  A mintajegyzetfüzetek futtatását beállítás nélkül is elkezdheti. A számítási példányok számítási célként is használhatók a betanítás és a dedigencia feladatokhoz.

* **Számítási fürtök:** A számítási fürtök többcsomópontos skálázási képességekkel és virtuális gépekből álló fürtök. A számítási fürtök jobban illeszkednek a nagy méretű feladatok és éles környezetek számítási céljaihoz.  A fürt automatikusan felméretez, amikor elküld egy feladatot.  Betanításra használt számítási célként vagy fejlesztési/tesztelési üzembe helyezéshez használható.

További információ a számítási célok betanításról: [Számítási célok betanítás.](concept-compute-target.md#train)  Az üzembe helyezési számítási célokkal kapcsolatos további információkért lásd: Üzembe [helyezési célok.](concept-compute-target.md#deploy)

## <a name="datasets-and-datastores"></a>Adatkészletek és adattárak

[**Azure Machine Learning adatkészletek**](concept-data.md#datasets)  megkönnyítik az adatokhoz való hozzáférést és a velük való munkát. Adatkészlet létrehozásával létrehoz egy hivatkozást az adatforrás helyére a metaadatok másolatával együtt. Mivel az adatok a meglévő helyen maradnak, nem jár további tárolási költséggel, és nem kockáztatja az adatforrások integritását.

További információ: [Adatkészletek létrehozása Azure Machine Learning regisztrálása.](how-to-create-register-datasets.md)  Az Adatkészletek használatával kapcsolatos további példákért tekintse meg a [mintajegyzetfüzeteket.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)

Az adatkészletek [adattárolók használatával](concept-data.md#datastores) csatlakoznak biztonságosan az Azure Storage-szolgáltatásokhoz. Az adattárak anélkül tárolják a kapcsolati adatokat, hogy a hitelesítési hitelesítő adatokat és az eredeti adatforrás integritását kockázatnak kiesik. A kapcsolati adatokat, például az előfizetés azonosítóját és a jogkivonatok hitelesítését a munkaterülethez társított Key Vault tárolják, így biztonságosan hozzáférhet a tárolóhoz anélkül, hogy a szkriptben nem kell kódokat írnia.

## <a name="environments"></a>Környezetek

[Munkaterület](#workspace)  >  **Környezetek**

A [környezet](concept-environments.md) a gépi tanulási modell betanítási vagy pontozási környezetének beágyazása. A környezet határozza meg a Python-csomagokat, a környezeti változókat és a szoftverbeállításokat a betanítás és pontozási szkriptek körül.  

Kódmintákért tekintse meg a Környezetek kezelése című [szakaszt a Környezetek használata című szakaszban.](how-to-use-environments.md#manage-environments)

## <a name="experiments"></a>Kísérletek

[Munkaterület](#workspace)  >  **Kísérletek**

A kísérlet egy adott szkriptből futtatott számos futtatás csoportosítása. Mindig egy munkaterülethez tartozik. A futtatás elküldésekor meg kell adnia a kísérlet nevét. A futtatás adatai a kísérlet alatt tárolódnak. Ha a név nem létezik a kísérlet elküldésekor, a rendszer automatikusan létrehoz egy új kísérletet.
  
Egy kísérlet használatának egy példájért lásd: [Oktatóanyag: Az első modell betanítás.](tutorial-1st-experiment-sdk-train.md)

### <a name="runs"></a>Futtatás

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  **Futtatás**

A futtatás egy betanító szkript egyetlen végrehajtása. Egy kísérlet általában több futtatásból fog része.

Azure Machine Learning összes futtatás rekordot tartalmaz, és a következő információkat tárolja a kísérletben:

* A futtatás metaadatai (időbélyeg, időtartam és így tovább)
* A szkript által naplózott metrikák
* A kísérlet által automatikusan vagy ön által kifejezetten feltöltött kimeneti fájlok
* A szkripteket tartalmazó könyvtár pillanatképe a futtatás előtt

Futtatás akkor jön létre, amikor elküldi a modellt betanító szkriptet. Egy futtatás nulla vagy több gyermekfuttathat. A legfelső szintű futtatás például két gyermekfutatot is futtathat, amelyek mindegyikének saját gyermekfuttatva lehet.

### <a name="run-configurations"></a>Konfigurációk futtatása

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  [Futtatás](#runs)  >  **Konfiguráció futtatása**

A futtatási konfiguráció határozza meg, hogyan fusson egy szkript egy adott számítási célon. A konfigurációval megadhatja a szkriptet, a számítási célt és a futtatni kívánt Azure ML-környezetet, az elosztott feladatspecifikus konfigurációkat és néhány további tulajdonságot. További információ a futtatás konfigurálható beállításairól: [ScriptRunConfig.](/python/api/azureml-core/azureml.core.scriptrunconfig)

A futtatási konfigurációk a betanítási szkriptet tartalmazó könyvtárban lévő fájlban is megmaradhatnak.   De létre is lehet hozva memória-objektumként, és futtatás elküldére használható.

Példa futtatási konfigurációkra: [Betanítási futtatás konfigurálása.](how-to-set-up-training-targets.md)

### <a name="snapshots"></a>Pillanatképek

[Munkaterület](#workspace)  >  [Kísérletek](#experiments)  >  [Futtatás](#runs)  >  **Pillanatkép**

Futtatás elküldésekor a Azure Machine Learning tömöríti a parancsfájlt tartalmazó könyvtárat zip-fájlként, és elküldi a számítási célnak. A rendszer ezután kibontja a zip-fájlt, és ott futtatja a szkriptet. Azure Machine Learning zip-fájlt is pillanatképként tárolja a futtatás rekordja részeként. A munkaterülethez hozzáféréssel bárki tallózhat egy futtatásrekordban, és letöltheti a pillanatképet.

### <a name="logging"></a>Naplózás

Azure Machine Learning automatikusan naplózza a standard futtatás metrikákat. A Python SDK-val azonban tetszőleges metrikákat [is naplózhat.](how-to-log-view-metrics.md)

A naplók többféleképpen is megtekinthetők: a futtatás állapotának valós idejű monitorozása vagy az eredmények megtekintése a befejezés után. További információ: [Gépi tanulási futtatás naplóinak figyelése és megtekintése.](how-to-log-view-metrics.md)


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git-követés és -integráció

Amikor elindít egy betanítás futtatását, ahol a forráskönyvtár egy helyi Git-adattár, az adattárra vonatkozó információk a futtatás előzményeiben tárolódnak. Ez a szkript-futtatási konfiguráció vagy az ML-folyamat használatával elküldött futtatásokkal működik. Az SDK-ból vagy a CLI-ről elküldött Machine Learning is működik.

További információ: [Git-integráció a Azure Machine Learning.](concept-train-model-git-integration.md)

### <a name="training-workflow"></a>Betanítás munkafolyamata

Amikor egy modellt betanító kísérletet futtat, a következő lépések történnek. Ezeket az alábbi betanítási munkafolyamat-diagram szemlélteti:

* Azure Machine Learning az előző szakaszban mentett kód-pillanatkép pillanatkép-azonosítójával hívjuk meg.
* Azure Machine Learning létrehoz egy futtatásazonosítót (nem kötelező) és egy Machine Learning-szolgáltatástokent, amelyet később olyan számítási célok használnak, mint a Machine Learning Compute/virtuális gépek a Machine Learning szolgáltatással való kommunikációhoz.
* Betanító feladatok futtatásához választhat egy felügyelt számítási célt (például Machine Learning Compute) vagy egy nem felügyelt számítási célt (például virtuális gépeket). Az adatfolyamok a következő két forgatókönyvhöz használhatók:
   * Virtuális gépek/HDInsight, amelyekhez SSH-hitelesítő adatokkal férnek hozzá a Microsoft-előfizetés kulcstartóiban. Azure Machine Learning felügyeleti kódot futtat a számítási célon, amely:

   1. Előkészíti a környezetet. (A Docker lehetőséget ad a virtuális gépek és a helyi számítógépek számára. A kísérletek Docker-tárolókon való futtatásának Machine Learning Compute a következő lépésekkel.)
   1. Letölti a kódot.
   1. Beállítja a környezeti változókat és konfigurációkat.
   1. Felhasználói szkripteket futtat (az előző szakaszban említett kód-pillanatképet).

   * Machine Learning Compute munkaterület által felügyelt identitáson keresztül érhető el.
Mivel Machine Learning Compute egy felügyelt számítási cél (azaz a Microsoft kezeli), az Ön Microsoft-előfizetése alatt fut.

   1. Szükség esetén meg is indul a távoli Docker-konstrukció.
   1. A felügyeleti kód a felhasználó Azure Files van írva.
   1. A tárolót egy kezdeti paranccsal indította el. Ez azt jelenti, hogy az előző lépésben leírt felügyeleti kód.

* A futtatás befejezése után lekérdezheti a futtatásokat és a metrikákat. Az alábbi folyamatábrán ez a lépés akkor történik, amikor a betanítási számítási cél visszaírja a futtatási metrikákat a Azure Machine Learning tárolóba a Cosmos DB adatbázisból. Az ügyfelek hívási Azure Machine Learning. Machine Learning le fogja lekért metrikákat a Cosmos DB adatbázisból, és visszaadja azokat az ügyfélnek.

[![Betanítás munkafolyamata](media/concept-azure-machine-learning-architecture/training-and-metrics.png)](media/concept-azure-machine-learning-architecture/training-and-metrics.png#lightbox)

## <a name="models"></a>Modellek

A legegyszerűbben a modell egy kód, amely bemenetet vesz fel, és kimenetet állít elő. A gépi tanulási modell létrehozásához ki kell választania egy algoritmust, meg kell adatokat kell használnia, és finomhangolnia kell [a hiperparamétereket.](how-to-tune-hyperparameters.md) A betanítás egy olyan iteratív folyamat, amely egy betanított modellt hoz létre, amely beágyazja a modell betanítási folyamat során tanultakat.

A betanított modelleket a modellen kívül is Azure Machine Learning. Vagy betaníthat egy modellt úgy is, [](#experiments) hogy [egy](#runs) kísérlet futtatását beküldi [egy](#compute-targets) számítási célnak a Azure Machine Learning. A modell létrehozása után regisztrálja [a modellt a](#register-model) munkaterületen.

Azure Machine Learning keretrendszertől független. Modell létrehozásakor bármilyen népszerű gépi tanulási keretrendszert használhat, például a Scikit-learnt, az XGBoostot, a PyTorch-t, a TensorFlow-t és a Chainert.

A modellek Scikit-learn használatával történő betanítása egy példáért [lásd: Oktatóanyag:](tutorial-train-models-with-aml.md)Képbesorolási modell betanítása a Azure Machine Learning.


### <a name="model-registry"></a><a name="register-model"></a> Modelljegyzék

[Munkaterület](#workspace)  >  **Modellek**

A **modelljegyzék** lehetővé teszi, hogy nyomon kövesse az összes modellt a Azure Machine Learning munkaterületen.

A modellek név és verzió alapján vannak azonosítva. Minden alkalommal, amikor egy meglévővel azonos nevű modellt regisztrál, a beállításjegyzék feltételezi, hogy az egy új verzió. A verzió növekszik, és az új modell ugyanazon a néven lesz regisztrálva.

A modell regisztrálásakor további metaadatcímkéket is meg lehet adni, majd a modellek keresésekor felhasználhatja a címkéket.

> [!TIP]
> A regisztrált modell a modellt felező egy vagy több fájl logikai tárolója. Ha például egy modell több fájlban van tárolva, regisztrálhatja őket egyetlen modellként a Azure Machine Learning munkaterületen. A regisztrációt követően letöltheti vagy üzembe helyezheti a regisztrált modellt, és fogadhatja az összes regisztrált fájlt.

Az aktív üzembe helyezés által használt regisztrált modellek nem törölhetők.

Példa egy modell regisztrálására: Képbesorolási modell [betanítása a Azure Machine Learning.](tutorial-train-models-with-aml.md)

## <a name="deployment"></a>Üzembe helyezés

A regisztrált modellt [szolgáltatásvégpontként](#register-model) kell üzembe helyeznie. A következő összetevőkre lesz szüksége:

* **Környezet**. Ez a környezet a modell következtetéshez való futtatásához szükséges függőségeket beágyazja.
* **Pontozási kód.** Ez a szkript fogadja a kérelmeket, pontokat ad a kérelmeknek a modell használatával, és visszaadja az eredményeket.
* **Következtetési konfiguráció:**. A következtetési konfiguráció megadja a környezetet, a belépési szkriptet és a modell szolgáltatásként való futtatásához szükséges egyéb összetevőket.

További információ ezekről az összetevőkről: [Modellek üzembe helyezése a Azure Machine Learning.](how-to-deploy-and-where.md)

### <a name="endpoints"></a>Végpontok

[Munkaterület](#workspace)  >  **Végpontok**

A végpontok a modell példányosítását jelentik a felhőben üzemeltetett webszolgáltatásban vagy egy IoT-modulban az integrált eszközök üzembe helyezéséhez.

#### <a name="web-service-endpoint"></a>Webszolgáltatás-végpont

Amikor webszolgáltatásként helyez üzembe egy modellt, a végpont üzembe helyezhető Azure Container Instances, Azure Kubernetes Service FPGA-kban. A szolgáltatást a modellből, a szkriptből és a társított fájlokból hozhatja létre. Ezek egy alap tároló-rendszerképbe kerülnek, amely a modell végrehajtási környezetét tartalmazza. A rendszerkép egy elosztott terhelésű, HTTP-végponttal rendelkezik, amely pontozási kéréseket fogad, amelyek a webszolgáltatásnak vannak elküldve.

Engedélyezheti a Application Insights telemetriát vagy modell-telemetriát a webszolgáltatás figyelése érdekében. A telemetriai adatok csak Az Ön számára érhetők el.  A rendszer a tárfiók és a Application Insights tárolja. Ha engedélyezte az automatikus skálázást, az Azure automatikusan skálázja az üzemelő példányát.

Az alábbi ábra egy webszolgáltatás-végpontként üzembe helyezett modell következtetési munkafolyamatát mutatja be:

A részletek a következőek:

* A felhasználó egy olyan ügyféllel regisztrálja a modellt, mint az Azure Machine Learning SDK.
* A felhasználó egy modellt, egy pontfájlt és más modellfüggőségeket használva hoz létre egy képet.
* A Docker-rendszerkép létrehozása és tárolása a következő Azure Container Registry.
* A webszolgáltatás üzembe helyezése a számítási célon (Container Instances/AKS) az előző lépésben létrehozott rendszerkép használatával.
* A pontozási kérelmek részletei a Application Insights, amely a felhasználó előfizetésében van tárolva.
* A telemetria a Microsoft-/Azure-előfizetésbe is le lesz küldve.

[![Következtetési munkafolyamat](media/concept-azure-machine-learning-architecture/inferencing.png)](media/concept-azure-machine-learning-architecture/inferencing.png#lightbox)


Példa egy modell webszolgáltatásként való üzembe helyezésére: Képbesorolási modell üzembe helyezése a [Azure Container Instances.](tutorial-deploy-models-with-aml.md)

#### <a name="real-time-endpoints"></a>Valós idejű végpontok

Amikor betanított modellt helyez üzembe a tervezőben, a modellt valós idejű [végpontként helyezheti üzembe.](tutorial-designer-automobile-price-deploy.md) A valós idejű végpontok általában egyetlen kérést kapnak a REST-végponton keresztül, és valós időben ad vissza előrejelzést. Ez ellentétben a kötegelt feldolgozással, amely egyszerre több értéket is feldolgoz, és a befejezés után egy adattárba menti az eredményeket.

#### <a name="pipeline-endpoints"></a>Folyamatvégpontokat

A folyamatvégpontokkal [](#ml-pipelines) programon keresztül, REST-végponton keresztül hívatja meg a gépi tanulási folyamatokat. A folyamatvégpontokkal automatizálhatja a folyamat-munkafolyamatokat.

A folyamatvégpont közzétett folyamatok gyűjteménye. Ez a logikai szervezés lehetővé teszi több folyamat kezelését és hívását ugyanazon végpont használatával. A folyamatvégponton közzétett összes közzétett folyamat verziószámozása. Kiválaszthat egy alapértelmezett folyamatot a végponthoz, vagy megadhat egy verziót a REST-hívásban.
 

#### <a name="iot-module-endpoints"></a>IoT-modulvégpont

Az üzembe helyezett IoT-modulvégpont egy Docker-tároló, amely tartalmazza a modellt, a társított szkripteket vagy alkalmazásokat, valamint az esetleges további függőségeket. Ezeket a modulokat peremeszközökön Azure IoT Edge üzembe.

Ha engedélyezte a monitorozást, az Azure telemetriai adatokat gyűjt a modellből a Azure IoT Edge modulban. A telemetriai adatok csak Ön számára érhetők el, és a tárfiók példányában tárolódnak.

Azure IoT Edge biztosítja, hogy a modul fut, és figyeli az azt üzemeltető eszközt. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

A [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md) az Azure CLI bővítménye, amely az Azure platform platformfüggetlen parancssori felülete. Ez a bővítmény parancsokat biztosít a gépi tanulási tevékenységek automatizálására.

### <a name="ml-pipelines"></a>Gépi tanulási folyamatok

Gépi [tanulási folyamatokkal](concept-ml-pipelines.md) olyan munkafolyamatokat hozhat létre és kezelhet, amelyek összefűzik a gépi tanulási fázisokat. A folyamatok tartalmazhatnak például adat-előkészítési, modellbeképzési, modelltelepítési és következtetési/pontozási fázisokat. Minden fázis több lépést is magában foglalhat, amelyek mind felügyelet nélkül futtathatók különböző számítási célokon. 

A folyamatlépések újrahasználhatók, és az előző lépések újrafuttatása nélkül futtathatók, ha a lépések kimenete nem változott. A modelleket például a költséges adatelőkészítési lépések újrafuttatása nélkül is újra lehet képezni, ha az adatok nem változnak. A folyamatok lehetővé teszik az adattudósok számára az együttműködést, miközben a gépi tanulási munkafolyamatok különböző területein dolgoznak.

## <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Azure Machine Learning a következő figyelési és naplózási képességeket biztosítja:

* Az __adattudósok__ számára figyelheti a kísérleteket, és naplózhatja a betanítás futtatásaiból származó adatokat. További információért tekintse át a következő cikkeket:
   * [Betanítás futtatásának elkezdése, figyelése és megszakítása](how-to-track-monitor-analyze-runs.md)
   * [A betanítási futtatások mérőszámainak naplózása](how-to-log-view-metrics.md)
   * [Kísérletek nyomon követése az MLflow használatával](how-to-use-mlflow.md)
   * [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)
* Rendszergazdák __esetén__ a munkaterülettel, a kapcsolódó Azure-erőforrásokkal, valamint az olyan eseményekkel kapcsolatos információkat figyelheti, mint az erőforrások létrehozása és törlése a Azure Monitor. További információ: [A](monitor-azure-machine-learning.md)Azure Machine Learning.
* A __DevOps és__ az __MLOps__ esetében figyelheti a webszolgáltatásként üzembe helyezett modellek vagy IoT Edge-modulok által létrehozott adatokat az üzemelő példányokkal kapcsolatos problémák azonosítása és a szolgáltatásnak elküldött adatok gyűjtése érdekében. További információkért lásd: [Modelladatok gyűjtése és](how-to-enable-data-collection.md) [Figyelás a Application Insights.](how-to-enable-app-insights.md)

## <a name="interacting-with-your-workspace"></a>A munkaterülettel való interakció

### <a name="studio"></a>Studio

[Azure Machine Learning stúdió](overview-what-is-machine-learning-studio.md) a munkaterületen lévő összes összetevő webes nézetét biztosítja.  Megtekintheti az adatkészletek, kísérletek, folyamatok, modellek és végpontok eredményeit és részleteit.  A studióban számítási erőforrásokat és adattárat is kezelhet.

A studióban elérheti az interaktív eszközöket is, amelyek a Azure Machine Learning:

+ [Azure Machine Learning a tervezőt,](concept-designer.md) hogy kód írása nélkül hajtsa végre a munkafolyamat lépéseit
+ Webes élmény automatizált [gépi tanuláshoz](concept-automated-ml.md)
+ [Azure Machine Learning notebookok](how-to-run-jupyter-notebooks.md) futtatásával saját kódot írhat és futtathat az integrált Jupyter notebookkiszolgálókban.
+ [Adatcímkéző projektek](how-to-create-labeling-projects.md) projektek létrehozásához, kezeléséhez és monitorozáshoz az adatok címkézéséhez

### <a name="programming-tools"></a>Programozási eszközök

> [!IMPORTANT]
> Az alább megjelölt (előzetes verzió) eszközök jelenleg nyilvános előzetes verzióban állnak rendelkezésre.
> Az előzetes verzió szolgáltatói szerződés nélkül érhető el, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  A Pythonhoz készült Azure Machine Learning SDK-val bármilyen [Python-környezetben interakcióba léphet a szolgáltatással.](/python/api/overview/azure/ml/intro)
+ Bármilyen R-környezetben együttműködhet a szolgáltatással az [Azure Machine Learning SDK for R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (előzetes verzió) használatával.
+ A [Azure Machine Learning használatával kód](concept-designer.md) írása nélkül hajthat végre munkafolyamat-lépéseket. 
+ Használja [Azure Machine Learning CLI-t](./reference-azure-machine-learning-cli.md) az automatizáláshoz.
+ A [Számos modell megoldásgyorsító](https://aka.ms/many-models) (előzetes verzió) a Azure Machine Learning, és lehetővé teszi gépi tanulási modellek száz vagy akár ezer gépi tanulási modell betanítát, működtetését és kezelését.

## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning első lépésekért lásd:

* [Mi az Azure Machine Learning?](overview-what-is-azure-ml.md)
* [Azure Machine Learning-munkaterület létrehozása](how-to-manage-workspace.md)
* [Oktatóanyag (1. rész): Modell betanítás](tutorial-train-models-with-aml.md)