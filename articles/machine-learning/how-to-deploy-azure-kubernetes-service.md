---
title: ML modellek üzembe helyezése a Kubernetes szolgáltatásban
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning modelleket webszolgáltatásként az Azure Kubernetes Service használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 342ae2f590f4bf4ce88f64d6d545defff358ad72
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215221"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Modell üzembe helyezése Azure Kubernetes Service-fürtön

Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként az Azure Kubernetes szolgáltatásban (ak) a Azure Machine Learning használatával. Az Azure Kubernetes szolgáltatás kiválóan alkalmas a nagy léptékű éles környezetekben való üzembe helyezésre. Ha a következő lehetőségek közül egyet vagy többet szeretne használni, használja az Azure Kubernetes szolgáltatást:

- __Gyors válaszidő__
- A telepített szolgáltatás automatikus __skálázása__
- __Logging__
- __Modellezési adatgyűjtés__
- __Hitelesítés__
- __TLS-lezárás__
- __Hardveres gyorsítási__ lehetőségek, például GPU és mező – programozható Gate-tömbök (FPGA)

Az Azure Kubernetes szolgáltatásba való üzembe helyezéskor a __munkaterülethez csatlakoztatott__ AK-fürtbe helyezi üzembe a szolgáltatást. Az AK-fürtök munkaterülethez való csatlakoztatásával kapcsolatos információkért lásd: [Azure Kubernetes Service-fürt létrehozása és csatolása](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Javasoljuk, hogy a webszolgáltatásba való üzembe helyezés előtt helyileg végezzen hibakeresést. További információ: [helyi hibakeresés](./how-to-troubleshoot-deployment-local.md)
>
> További információ: Azure Machine Learning – [Üzembe helyezés helyi notebookba](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik regisztrált modellel, tekintse meg a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

- A cikkben szereplő __Python__ -kódrészletek azt feltételezik, hogy a következő változók vannak beállítva:

    * `ws` – Állítsa be a munkaterületre.
    * `model` – Állítsa be a regisztrált modellt.
    * `inference_config` – Állítsa be a modellre vonatkozó következtetési konfigurációt.

    A változók beállításával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- A cikkben szereplő __CLI__ -kódrészletek azt feltételezik, hogy létrehozott egy `inferenceconfig.json` dokumentumot. A dokumentum létrehozásával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- A munkaterülethez csatlakozó Azure Kubernetes Service-fürt. További információ: [Azure Kubernetes Service-fürt létrehozása és csatolása](how-to-create-attach-kubernetes.md).

    - Ha a modelleket GPU-csomópontokra vagy FPGA-csomópontokra (vagy bármely konkrét SKU-ra) szeretné telepíteni, akkor létre kell hoznia egy fürtöt az adott SKU-val. Nem támogatott másodlagos csomópont-készlet létrehozása meglévő fürtben, valamint modellek üzembe helyezése a másodlagos csomópont-készletben.

## <a name="understand-the-deployment-processes"></a>Az üzembe helyezési folyamatok ismertetése

Az "üzemelő példány" szó mind a Kubernetes, mind a Azure Machine Learning esetében használatos. Az "üzembe helyezés" a két kontextusban eltérő jelentésekkel rendelkezik. A Kubernetes-ben a a egy `Deployment` konkrét entitás, amely egy DEKLARATÍV YAML-fájllal van megadva. A Kubernetes `Deployment` meghatározott életciklussal és konkrét kapcsolatokkal rendelkezik más Kubernetes-entitásokkal, például a és a szolgáltatással `Pods` `ReplicaSets` . Megtudhatja, hogyan Kubernetes a docs és a videók a [Kubernetes?](https://aka.ms/k8slearning)című témakörben.

Azure Machine Learning az "üzembe helyezés" a projekt erőforrásainak elérhetővé tételéhez és tisztításához szükséges általánosabb értelemben használatos. Az üzembe helyezés részét Azure Machine Learning lépések a következők:

1. A Project mappában lévő fájlok tömörítése, figyelmen kívül hagyva a. amlignore vagy. gitignore fájlban megadott fájlokat.
1. A számítási fürt vertikális felskálázása (a Kubernetes-re vonatkozik)
1. A Docker kiépítése vagy letöltése a számítási csomópontra (a Kubernetes-re vonatkozik)
    1. A rendszer a következőképpen számítja ki a kivonatot: 
        - A kiinduló rendszerkép 
        - Egyéni Docker-lépések (lásd: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](./how-to-deploy-custom-docker-image.md))
        - A Conda-definíció YAML (lásd: [létrehozás & a szoftveres környezetek használata Azure Machine learning](./how-to-use-environments.md))
    1. A rendszer ezt a kivonatot használja a munkaterület Azure Container Registry (ACR) keresésének kulcsaként.
    1. Ha nem található, akkor a globális ACR-beli egyezést keresi
    1. Ha nem található, a rendszer létrehoz egy új rendszerképet (amely gyorsítótárazva lesz, és leküldi a munkaterület ACR-nek).
1. A tömörített projektfájl letöltése a számítási csomóponton lévő ideiglenes tárhelyre
1. Projektfájl kicsomagolása
1. A számítási csomópont végrehajtása `python <entry script> <arguments>`
1. A `./outputs` munkaterülethez társított Storage-fiókba írt naplók, modellező fájlok és egyéb fájlok mentése
1. A számítási felskálázás, beleértve az ideiglenes tárolók eltávolítását (a Kubernetes-re vonatkozik)

### <a name="azure-ml-router"></a>Azure ML-útválasztó

Az előtér-összetevő (azureml-FE), amely a bejövő következtetési kérelmeket átirányítja a telepített szolgáltatásokra, a rendszer igény szerint automatikusan méretezi. A azureml-Fe méretezése az AK-fürt célján és méretén (csomópontok száma) alapul. A fürt célját és csomópontjait AK- [fürt létrehozásakor vagy csatolásakor](how-to-create-attach-kubernetes.md)kell konfigurálni. Fürtben egy azureml-Fe szolgáltatás található, amely több hüvelyen is futhat.

> [!IMPORTANT]
> Ha olyan fürtöt használ, amely __dev-testként__ van konfigurálva, az önméretező **le van tiltva**.

A Azureml-Fe egyszerre több magot használ, és (horizontálisan) több hüvelyt használ. A vertikális Felskálázási döntés végrehajtásakor a rendszer a bejövő következtetési kérelmek továbbításához szükséges időt használja. Ha ez az idő meghaladja a küszöbértéket, felskálázás történik. Ha a bejövő kérelmek átirányításának ideje továbbra is meghaladja a küszöbértéket, kibővíthető.

A-re és a-re történő skálázáskor a rendszer a CPU-használatot használja. Ha a CPU-használat küszöbértéke teljesül, a rendszer először az előtér végét fogja méretezni. Ha a CPU-használat csökken a skálázási küszöbértékre, akkor a méretezési művelet történik. A fel-és kiskálázás csak akkor történik meg, ha elegendő fürterőforrás áll rendelkezésre.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Az AKS következtetési fürtök kapcsolati követelményeinek ismertetése

Ha Azure Machine Learning létrehoz vagy csatlakoztat egy AK-fürtöt, az AK-fürt a következő két hálózati modell egyikével van telepítve:
* Kubenet hálózatkezelés – a hálózati erőforrásokat általában a rendszer az AK-fürt üzembe helyezésével hozza létre és konfigurálja.
* Azure Container Network Interface (CNI) hálózatkezelés – Az AKS-fürt a meglévő virtuális hálózati erőforrásokhoz és konfigurációkhoz csatlakozik.

Az első hálózati mód esetében a hálózat létrehozása és konfigurálása a Azure Machine Learning szolgáltatás számára megfelelő. A második hálózati mód esetében, mivel a fürt meglévő virtuális hálózathoz van csatlakoztatva, különösen akkor, ha a meglévő virtuális hálózathoz egyéni DNS van használatban, az ügyfeleknek külön figyelmet kell fordítaniuk a kapcsolati követelményekre az AK-ra hivatkozó fürthöz, és biztosítaniuk kell a DNS-feloldást és a kimenő kapcsolatot az AK-alapú következtetésekhez.

Az alábbi ábra az AK-hoz kapcsolódó összes kapcsolódási követelményt rögzíti. A fekete nyilak a tényleges kommunikációt jelölik, a kék nyilak pedig a tartományneveket jelölik, amelyet az ügyfél által vezérelt DNS-nek fel kell oldania.

 ![Kapcsolati követelmények az AK-hoz való hivatkozáshoz](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Általános DNS-feloldási követelmények
A meglévő VNET belüli DNS-feloldás az ügyfél vezérlése alatt áll. A következő DNS-bejegyzéseket feloldhatónak kell lennie:
* AK API-kiszolgáló \<cluster\> . HCP. \<region\> . azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* Az ügyfél Azure Container Registry (ARC) a \<ACR name\> . azurecr.IO formájában
* Azure Storage-fiók \<account\> . table.Core.Windows.net és \<account\> . blob.Core.Windows.net formátumban
* Választható HRE-hitelesítés esetén: api.azureml.ms
* Pontozási végponti tartománynév, amelyet az Azure ML vagy Egyéni tartománynév automatikusan generált. Az automatikusan létrehozott tartománynév a következőhöz hasonlít: \<leaf-domain-label \+ auto-generated suffix\> . \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>A kapcsolódási követelmények időrendi sorrendben: a fürt létrehozásáról modellre történő telepítésre

Az AK létrehozása vagy csatolása folyamatban az Azure ML-útválasztó (azureml-FE) üzembe helyezése az AK-fürtön történik. Az Azure ML-útválasztó üzembe helyezéséhez az AK-csomópontnak képesnek kell lennie:
* DNS feloldása az AK API-kiszolgálóhoz
* A MCR DNS-feloldása az Azure ML-útválasztó Docker-rendszerképének letöltéséhez
* Lemezképek letöltése a MCR, ahol a kimenő kapcsolat szükséges

Közvetlenül a azureml-Fe üzembe helyezése után a rendszer megkísérli az indítást, és ehhez a következőket kell tennie:
* DNS feloldása az AK API-kiszolgálóhoz
* Az AK API-kiszolgáló lekérdezése saját maga is felderíthető (több-Pod szolgáltatás)
* Kapcsolódás más példányokhoz

A azureml-Fe elindítása után további kapcsolatra van szükség a megfelelő működéshez:
* Kapcsolódás az Azure Storage-hoz a dinamikus konfiguráció letöltéséhez
* Oldja fel a DNS-t a HRE hitelesítési kiszolgáló api.azureml.ms, és kommunikáljon vele, ha a telepített szolgáltatás HRE-hitelesítést használ.
* AK API-kiszolgáló lekérdezése telepített modellek felderítéséhez
* Kommunikáció az üzembe helyezett modell Hüvelyével

A modell üzembe helyezésének ideje alatt a sikeres modell üzembe helyezési AK-csomópontjának képesnek kell lennie a következőre: 
* A DNS feloldása az ügyfél ACR-hez
* Lemezképek letöltése az ügyfél ACR-ből
* A DNS feloldása az Azure-Blobokban, ahol a modell tárolva van
* Modellek letöltése az Azure-Blobokból

A modell üzembe helyezése és a szolgáltatás elindítása után a azureml-Fe automatikusan észleli az AK API-t, és készen áll arra, hogy átirányítsa a kérést. Képesnek kell lennie kommunikálni a modell Hüvelyével.
>[!Note]
>Ha a telepített modellhez bármilyen kapcsolat szükséges (például külső adatbázis vagy más REST-szolgáltatás lekérdezése, BLOG letöltése stb.), akkor a DNS-feloldást és a kimenő kommunikációt is engedélyezni kell a szolgáltatásokhoz.

## <a name="deploy-to-aks"></a>Üzembe helyezés az AKS-ben

Ha modellt szeretne üzembe helyezni az Azure Kubernetes szolgáltatásban, hozzon létre egy __központi telepítési konfigurációt__ , amely leírja a szükséges számítási erőforrásokat. Például a magok és a memória száma. Szüksége lesz egy __következtetésre__ is, amely leírja a modell és a webszolgáltatás üzemeltetéséhez szükséges környezetet. A következtetések konfigurációjának létrehozásáról további információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

> [!NOTE]
> A telepítendő modellek száma az üzemelő példányok esetében 1 000 modellre korlátozódik (tárolóként).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute?preserve-view=true&view=azure-ml-py)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?preserve-view=true&view=azure-ml-py)
* [Modell. Deploy](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A CLI használatával történő üzembe helyezéshez használja a következő parancsot. Cserélje le az `myaks` t az AK számítási cél nevére. Cserélje le a `mymodel:1` nevet a regisztrált modell nevére és verziójára. Cserélje le `myservice` a nevet a következő szolgáltatáshoz:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

További információ: az [ml Model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) Reference.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

A VS Code használatával kapcsolatos információkért lásd: [üzembe helyezés az AK-n keresztül a vs Code bővítménnyel](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A VS code-on keresztül történő üzembe helyezéshez az AK-fürt létrehozása vagy a munkaterülethez való csatolása szükséges.

---

### <a name="autoscaling"></a>Automatikus skálázás

Az Azure ML-modellek automatikus skálázását kezelő összetevő a azureml-FE, amely egy intelligens kérelem-útválasztó. Mivel az összes következtetési kérelem áthalad rajta, a szükséges adattal rendelkezik a telepített modell (ek) automatikus skálázásához.

> [!IMPORTANT]
> * Ne **engedélyezze a Kubernetes horizontális Pod autoskálázást (hPa) a modellek üzembe helyezéséhez**. Ez azt eredményezi, hogy a két automatikus skálázási összetevő versenyez egymással. A Azureml-Fe úgy lett kialakítva, hogy az Azure ML által üzembe helyezett modellek automatikusan méretezhetőek legyenek, ahol a HPA-nak ki kell találnia vagy közelítenie kell a modell kihasználtságát egy általános metrika, például a CPU-használat vagy egy egyéni metrika
> 
> * **A Azureml-Fe nem méretezi a csomópontok számát egy AK-fürtben**, mert ez váratlan költségmegtakarítást eredményezhet. Ehelyett a **modell replikáinak számát** a fizikai fürt határain belül méretezi. Ha a fürtön belüli csomópontok számát kell méreteznie, manuálisan méretezheti a fürtöt, vagy [konfigurálhatja az AK-fürt automéretezőjét](../aks/cluster-autoscaler.md).

Az automatikus skálázást `autoscale_target_utilization` `autoscale_min_replicas` a, a és az `autoscale_max_replicas` AK webszolgáltatáshoz tartozó beállítások szabályozzák. Az alábbi példa bemutatja, hogyan engedélyezheti az automatikus skálázást:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

A vertikális felskálázásra vagy lefelé irányuló döntések az aktuális tároló-replikák kihasználtságán alapulnak. A foglalt replikák (egy kérelem feldolgozása) száma osztva az aktuális replikák teljes számával. Ha ez a szám meghaladja a számot `autoscale_target_utilization` , a rendszer több replikát hoz létre. Ha alacsonyabb, a replikák csökkennek. Alapértelmezés szerint a cél kihasználtsága 70%.

A replikák hozzáadására vonatkozó döntések lelkesek és gyorsak (körülbelül 1 másodperc). A replikák eltávolítására vonatkozó döntések konzervatívak (körülbelül 1 perc).

A szükséges replikák kiszámításához használja a következő kódot:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

A, a és a beállításával kapcsolatos további tudnivalókért `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` tekintse meg a [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) modul referenciáját.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellek üzembe helyezése az AK-ban vezérelt bevezetéssel (előzetes verzió)

A modell verzióinak elemzése és előléptetése vezérelt módon, végpontok használatával. Akár hat verziót is üzembe helyezhet egyetlen végpont mögött. A végpontok a következő képességeket biztosítják:

* Adja __meg az egyes végpontok számára továbbított pontozási forgalom százalékos arányát__. Tegyük fel például, hogy a forgalom 20%-át a "teszt" végpontra és a 80%-ra irányítja

    > [!NOTE]
    > Ha nem veszi figyelembe a forgalom 100%-át, a fennmaradó százalékot a rendszer az __alapértelmezett__ végponti verzióra irányítja. Ha például a "teszt" végpont-verziót konfigurálja a forgalom 10%-ában, a "Prod" pedig 30%-ot, a fennmaradó 60%-ot az alapértelmezett végponti verzióra küldi a rendszer.
    >
    > Az első végponton létrehozott verzió automatikusan alapértelmezettként van konfigurálva. Ezt a `is_default=True` végpont verziójának létrehozásakor vagy frissítésekor lehet beállítani.
     
* Végpont verziójának címkézése __vezérlőelemként__ vagy __kezelésként__. Előfordulhat például, hogy az aktuális üzemi végpont verziója a vezérlő, míg a lehetséges új modellek kezelési verzióként vannak telepítve. A kezelési verziók teljesítményének kiértékelése után, ha az egyik a jelenlegi vezérlőt végzi el, az új éles környezetbe/szabályozásba kerül.

    > [!NOTE]
    > Csak __egy__ vezérlőt használhat. Több kezelést is használhat.

Az alkalmazások elemzése lehetővé teszi a végpontok és a telepített verziók működési mérőszámának megtekintését.

### <a name="create-an-endpoint"></a>Végpont létrehozása
Ha készen áll a modellek üzembe helyezésére, hozzon létre egy pontozási végpontot, és telepítse az első verziót. Az alábbi példa bemutatja, hogyan helyezheti üzembe és hozhatja létre a végpontot az SDK használatával. Az első üzembe helyezés alapértelmezett verzióként lesz meghatározva, ami azt jelenti, hogy az összes verzióban a nem meghatározott forgalom százalékos értéke az alapértelmezett verzió lesz.  

> [!TIP]
> A következő példában a konfiguráció a kezdeti végpont verzióját állítja be a forgalom 20%-ának kezelésére. Mivel ez az első végpont, ez az alapértelmezett verzió is. És mivel a forgalom más 80%-ában nem rendelkezünk más verziókkal, a rendszer az alapértelmezett értéket is átirányítja. Egészen addig, amíg a forgalom százalékos arányát elkerülő más verziók üzembe vannak helyezve, ez a forgalom 100%-át ténylegesen megkapja.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Verziók frissítése és hozzáadása egy végponthoz

Adjon hozzá egy újabb verziót a végponthoz, és konfigurálja a pontozási forgalom százalékos értékének megadását a verzióra. A verzióknak két típusa van, egy vezérlő és egy kezelési verzió. Több kezelési verzió is felhasználható egyetlen vezérlő verziójának összehasonlítására.

> [!TIP]
> A következő kódrészlet által létrehozott második verzió a forgalom 10%-át fogadja el. Az első verzió 20%-ra van konfigurálva, így a forgalom csak a 30%-a lesz konfigurálva adott verzióra. A fennmaradó 70%-ot az első végpont-verzióra küldi a rendszer, mert az egyben az alapértelmezett verzió is.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Meglévő verziók frissítése vagy törlése egy végponton. Megváltoztathatja a verzió alapértelmezett típusát, a vezérlő típusát és a forgalmi percentilis értéket. A következő példában a második verzió a 40%-ra növeli a forgalmat, és mostantól az alapértelmezett.

> [!TIP]
> A következő kódrészlet után a második verzió már alapértelmezett. Most már 40%-ra van konfigurálva, míg az eredeti verzió még 20%-ra van konfigurálva. Ez azt jelenti, hogy a forgalom 40%-át nem a verzió konfigurációi teszik ki. A maradék forgalom a második verzióra lesz átirányítva, mivel most már alapértelmezett. A forgalom 80%-át hatékonyan fogadja.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Webszolgáltatás-hitelesítés

Az Azure Kubernetes szolgáltatásba való üzembe helyezéskor a __kulcs alapú__ hitelesítés alapértelmezés szerint engedélyezve van. Engedélyezheti a __jogkivonat-alapú__ hitelesítést is. A jogkivonat-alapú hitelesítéshez az szükséges, hogy az ügyfelek egy Azure Active Directory fiókot használjanak a hitelesítési jogkivonat igényléséhez, amely a központilag telepített szolgáltatásra irányuló kérések elvégzésére szolgál.

A hitelesítés __letiltásához__ állítsa be a `auth_enabled=False` paramétert a központi telepítési konfiguráció létrehozásakor. Az alábbi példa az SDK használatával letiltja a hitelesítést:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Az ügyfélalkalmazások általi hitelesítéssel kapcsolatos információkért tekintse meg a [webszolgáltatásként üzembe helyezett Azure Machine learning modell](how-to-consume-web-service.md)felhasználását ismertető témakört.

### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha a kulcsos hitelesítés engedélyezve van, a `get_keys` metódussal kérheti le az elsődleges és a másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra kell létrehoznia egy kulcsot, használja a következőt [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

A jogkivonat-hitelesítés engedélyezéséhez állítsa be a `token_auth_enabled=True` paramétert egy központi telepítés létrehozásakor vagy frissítésekor. Az alábbi példa lehetővé teszi a jogkivonat-hitelesítés használatát az SDK-val:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Ha engedélyezve van a jogkivonat-hitelesítés, a metódus használatával kérhet `get_token` le egy JWT-tokent és a jogkivonat lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat időpontját követően új jogkivonatot kell kérnie `refresh_by` .
>
> A Microsoft nyomatékosan javasolja, hogy a Azure Machine Learning munkaterületet ugyanabban a régióban hozza létre, mint az Azure Kubernetes Service-fürtöt. A webszolgáltatások tokenekkel történő hitelesítéséhez a webszolgáltatás meghívja a Azure Machine Learning munkaterület létrehozásához használt régiót. Ha a munkaterület régiója nem érhető el, akkor sem fogja tudni lehívni a webszolgáltatáshoz tartozó jogkivonatot, ha a fürt a munkaterülettől eltérő régióban található. Ez gyakorlatilag a jogkivonat-alapú hitelesítés nem érhető el, amíg a munkaterület régiója újra elérhetővé nem válik. Emellett minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart a token beolvasása.
>
> Jogkivonat lekéréséhez az Azure Machine Learning SDK-t vagy az az [ml Service Get-Access-Token](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-get-access-token) parancsot kell használnia.


### <a name="vulnerability-scanning"></a>Biztonsági rések vizsgálata

Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Az erőforrások vizsgálatához és a javaslatainak követéséhez engedélyeznie kell Azure Security Center. További információ: az [Azure Kubernetes Services és a Security Center integrációja](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Következő lépések

* [Az Azure RBAC használata az Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)
* [Biztonságos következtetési környezet az Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
