---
title: Gépi tanulási modellek üzembe helyezése a Kubernetes Service-ben
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti Azure Machine Learning modelleket webszolgáltatásként a Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 6030462fc7c9678200aa14fa852a82d35f8703b6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877821"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Modell üzembe helyezése egy Azure Kubernetes Service fürtön

Megtudhatja, hogyan helyezhet Azure Machine Learning modellt webszolgáltatásként a Azure Kubernetes Service (AKS) használatával. Azure Kubernetes Service nagy léptékű éles környezetekben használható. Ha az alábbi képességek közül egy vagy többre van szüksége, használja az Azure Kubernetes Service-t:

- __Gyors válaszidő__
- __Az üzembe helyezett szolgáltatás__ automatikus skálázása
- __Logging__
- __Modelladatok gyűjtése__
- __Hitelesítés__
- __TLS-letöltés__
- __Hardvergyorsítási__ lehetőségek, például GPU és helyszínen programozható kaputömbök (FPGA)

Az üzembe helyezést Azure Kubernetes Service a munkaterülethez csatlakoztatott AKS-fürtön __kell üzembe helyeznie.__ Az AKS-fürtök munkaterülethez való csatlakoztatásával kapcsolatos információkért lásd: Create and attach an Azure Kubernetes Service cluster (Új fürt [létrehozása Azure Kubernetes Service csatlakoztatása).](how-to-create-attach-kubernetes.md)

> [!IMPORTANT]
> Javasoljuk, hogy a webszolgáltatásban való üzembe helyezés előtt helyileg hibakeresést futtason. További információ: [Helyi hibakeresés](./how-to-troubleshoot-deployment-local.md)
>
> További információ: Azure Machine Learning – [Üzembe helyezés helyi notebookba](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).

- A munkaterületen regisztrált gépi tanulási modell. Ha még nem regisztrált modellel, tekintse meg a modellek üzembe [helyezésének mikéntjéhez és helyéhez lásd:](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine Learning szolgáltatáshoz,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK-hoz](/python/api/overview/azure/ml/intro)vagy a [Azure Machine Learning Visual Studio Code-bővítményhez.](tutorial-setup-vscode-extension.md)

- A __cikkben__ említett Python-kódrészletek feltételezik, hogy a következő változók vannak beállítva:

    * `ws` – Állítsa be a munkaterületet.
    * `model` – Állítsa be a regisztrált modellt.
    * `inference_config` – Állítsa be a modell következtetési konfigurációját.

    A változók beállításával kapcsolatos további információkért lásd: [Modellek üzembe](how-to-deploy-and-where.md)helyezése.

- A __cikkben__ a CLI-kódrészletek feltételezik, hogy létrehozott egy `inferenceconfig.json` dokumentumot. További információ a dokumentum létrehozásáról: [Modellek üzembe](how-to-deploy-and-where.md)helyezése.

- Egy Azure Kubernetes Service fürt a munkaterülethez csatlakoztatva. További információ: [Fürt létrehozása és csatolása Azure Kubernetes Service fürthöz.](how-to-create-attach-kubernetes.md)

    - Ha GPU-csomópontokon vagy FPGA-csomópontokon (vagy bármely adott termékváltozatban) szeretne modelleket üzembe helyezni, létre kell hoznia egy fürtöt az adott termékváltozattal. A másodlagos csomópontkészlet meglévő fürtben való létrehozása és a modellek a másodlagos csomópontkészletben való üzembe helyezése nem támogatott.

## <a name="understand-the-deployment-processes"></a>Az üzembe helyezési folyamatok

Az "üzembe helyezés" szó a Kubernetesben és a Azure Machine Learning. Ebben a két kontextusban az "üzembe helyezés" különböző jelentéssel bír. A Kubernetesben a egy `Deployment` konkrét entitás, amely deklaratív YAML-fájllal van megadva. A Kubernetes meghatározott életciklussal és konkrét kapcsolatokkal rendelkezik más Kubernetes-entitásokkal, például a és a `Deployment` `Pods` `ReplicaSets` entitásokkal. A Kubernetesről dokumentumokból és videókból a Mi az a [Kubernetes? című cikkből olvashat.](https://aka.ms/k8slearning)

A Azure Machine Learning az "üzembe helyezést" általában a projekterőforrások elérhetővé és tisztítására használják. Az üzembe helyezés Azure Machine Learning a következő lépéseket kell figyelembe vennie:

1. Tömöríti a projektmappában lévő fájlokat, figyelmen kívül hagyva az .amlignore vagy .gitignore fájlban megadott fájlokat
1. A számítási fürt felméretezése (a Kuberneteshez kapcsolódik)
1. A dockerfile kiépítése vagy letöltése a számítási csomópontra (a Kuberneteshez kapcsolódik)
    1. A rendszer a következő kivonatát számítja ki: 
        - Az alapként felhozott rendszerkép 
        - Egyéni Docker-lépések [(lásd: Modell üzembe helyezése egyéni Docker-alapként használt rendszerkép használatával)](./how-to-deploy-custom-docker-image.md)
        - A Conda YAML-definíciója [(lásd: Create & use software environments in Azure Machine Learning](./how-to-use-environments.md))
    1. A rendszer ezt a kivonatot használja kulcsként a munkaterület-Azure Container Registry (ACR)
    1. Ha nem található, egyezést keres a globális ACR-ban
    1. Ha nem található, a rendszer egy új rendszerképet hoz létre (amely gyorsítótárazva lesz, és le lesz küldve a munkaterület ACR-be)
1. A tömörített projektfájl letöltése ideiglenes tárolóba a számítási csomóponton
1. A projektfájl kicsomagolása
1. A végrehajtás alatt álló számítási csomópont `python <entry script> <arguments>`
1. Naplók, modellfájlok és a munkaterülethez társított tárfiókba írt `./outputs` egyéb fájlok mentése
1. Számítási erőforrások leméretezése, beleértve az ideiglenes tárterület eltávolítását (a Kuberneteshez kapcsolódik)

### <a name="azure-ml-router"></a>Azure ML-útválasztó

Az előtér-összetevő (azureml-fe), amely a bejövő következtetési kérelmeket az üzembe helyezett szolgáltatásokhoz használja, automatikusan méretez. Az azureml-fe méretezése az AKS-fürt célja és mérete (a csomópontok száma) alapján van megszava. A fürt célja és a csomópontok konfigurálása az AKS-fürtök létrehozásakor vagy [csatolásakor történik.](how-to-create-attach-kubernetes.md) Fürtönként egy azureml-fe szolgáltatás futhat, amely több podon is futhat.

> [!IMPORTANT]
> Ha fejlesztési tesztként konfigurált fürtöt __használ,__ az önméretozó le van **tiltva.**

Az Azureml-fe vertikálisan felskálál, hogy több magot használjon, és (vízszintesen) több podot használjon. A felskálásról szóló döntés meghozatalakor a rendszer a bejövő következtetési kérelmek útválasztásának idejét használja. Ha ez az idő meghaladja a küszöbértéket, felskálás történik. Ha a bejövő kérések útválasztásának ideje továbbra is meghaladja a küszöbértéket, felskálás történik.

A le- és leméretezés cpu-használatot használ. Ha a cpu-használat küszöbértéke teljesül, az előtere először le lesz skálázva. Ha a CPU-használat a leskálás küszöbértéke alá csökken, leskálásos művelet történik. A fel- és felméretezés csak akkor történik meg, ha elegendő fürterőforrás áll rendelkezésre.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Az AKS következtetési fürtök kapcsolati követelményeinek ismertetése

Amikor Azure Machine Learning AKS-fürtöt hoz létre vagy csatol, az AKS-fürt a következő két hálózati modell egyikével lesz üzembe helyezni:
* Kubenet-hálózat – A hálózati erőforrások általában az AKS-fürt üzembe helyezésekor vannak létrehozva és konfigurálva.
* Azure Container Network Interface (CNI) hálózatkezelés – Az AKS-fürt a meglévő virtuális hálózati erőforrásokhoz és konfigurációkhoz csatlakozik.

Az első hálózati módban létrejön a hálózat, és megfelelően van konfigurálva a Azure Machine Learning service. A második hálózatépítési mód esetén, mivel a fürt a meglévő virtuális hálózathoz csatlakozik, különösen akkor, ha egyéni DNS-t használ a meglévő virtuális hálózathoz, az ügyfélnek külön figyelmet kell fordítania az AKS-következtetési fürt csatlakozási követelményeire, és biztosítania kell a DNS-feloldás és a kimenő kapcsolatok AKS-következtetéshez való hozzáférését.

Az alábbi ábra az AKS-következtetés összes csatlakozási követelményét rögzíti. A fekete nyilak a tényleges kommunikációt, a kék nyilak pedig a tartományneveket jelzik, ezeket az ügyfél által vezérelt DNS-nek fel kell oldania.

 ![Az AKS-következtetés csatlakozási követelményei](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>A DNS-feloldás általános követelményei
A meglévő VNET-en belüli DNS-feloldás az ügyfél ellenőrzése alatt áll. A következő DNS-bejegyzéseknek feloldhatónak kell lennie:
* AKS API-kiszolgáló \<cluster\> .hcp \<region\> formájában. . azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* Az ügyfél Azure Container Registry (ARC) \<ACR name\> .azurecr.io
* Azure Storage-fiók \<account\> .table.core.windows.net \<account\> és .blob.core.windows.net
* (Nem kötelező) AAD-hitelesítés esetén: api.azureml.ms
* Végpont tartománynevének pontozása, amelyet automatikusan hoz létre az Azure ML vagy az egyéni tartománynév. Az automatikusan létrehozott tartománynév így néz ki: \<leaf-domain-label \+ auto-generated suffix\> \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>A kapcsolódási követelmények időrendi sorrendben: a fürtök létrehozásától a modell üzembe helyezéséig

Az AKS-létrehozási vagy -csatolási folyamat során az Azure ML-útválasztó (azureml-fe) üzembe lesz helyezni az AKS-fürtben. Az Azure ML-útválasztó üzembe helyezéséhez az AKS-csomópontnak képesnek kell lennie a következőre:
* AZ AKS API-kiszolgáló DNS-ének feloldása
* Az MCR DNS-ének feloldása az Azure ML-útválasztó docker-rendszerképének letöltéséhez
* Rendszerképek letöltése az MCR-ről, ahol kimenő kapcsolatra van szükség

Közvetlenül az azureml-fe üzembe helyezése után megpróbál elindulni, és ehhez a következőre van szükség:
* AZ AKS API-kiszolgáló DNS-ének feloldása
* AKS API-kiszolgáló lekérdezése saját maga más példányainak felderítéséhez (ez egy több podos szolgáltatás)
* Csatlakozás saját maga más példányaihoz

Az azureml-fe elindulás után további kapcsolatra van szükség a megfelelő működéshez:
* Csatlakozás az Azure Storage-hoz a dinamikus konfiguráció letöltéséhez
* Az AAD-hitelesítési kiszolgáló DNS-api.azureml.ms, és kommunikáljon vele, amikor az üzembe helyezett szolgáltatás AAD-hitelesítést használ.
* AKS API-kiszolgáló lekérdezése az üzembe helyezett modellek felderítéséhez
* Kommunikáció az üzembe helyezett modell POD-iival

A modell üzembe helyezésekor a modell sikeres üzembe helyezéséhez az AKS-csomópontnak képesnek kell lennie a következőre: 
* Dns feloldása az ügyfél ACR-hez
* Rendszerképek letöltése az ügyfél ACR-ről
* A modellt tároló Azure-beliblobok DNS-ének feloldása
* Modellek letöltése az Azureblob-ból

A modell üzembe helyezése és a szolgáltatás indítása után az azureml-fe automatikusan felderíti azt az AKS API-val, és készen áll arra, hogy a kérést hozzá irányítsa. Képesnek kell lennie kommunikálni a modell-POD-okkal.
>[!Note]
>Ha az üzembe helyezett modellnek szüksége van bármilyen kapcsolatra (például külső adatbázis vagy más REST-szolgáltatás lekérdezése, blob letöltése stb.), akkor engedélyezni kell a DNS-feloldás és a szolgáltatások kimenő kommunikációját is.

## <a name="deploy-to-aks"></a>Üzembe helyezés az AKS-ben

Egy modell üzembe helyezéséhez a Azure Kubernetes Service hozzon létre egy üzembe helyezési __konfigurációt,__ amely leírja a szükséges számítási erőforrásokat. Például a magok száma és a memória. Emellett szükség van egy __következtetési__ konfigurációra is, amely leírja a modell és a webszolgáltatás gazdagépéhez szükséges környezetet. További információ a következtetési konfiguráció létrehozásáról: [Modellek](how-to-deploy-and-where.md)üzembe helyezése.

> [!NOTE]
> Az üzembe helyezni szükséges modellek száma üzemelő példányonként (tárolónként) legfeljebb 1000 modell lehet.

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

A példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő referenciadokumentumokat:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A parancssori felület használatával való üzembe helyezéshez használja a következő parancsot. Cserélje `myaks` le a helyére az AKS számítási cél nevét. Cserélje `mymodel:1` le a helyére a regisztrált modell nevét és verzióját. Cserélje `myservice` le a helyére a szolgáltatás nevét:

```azurecli-interactive
az ml model deploy --ct myaks -m mymodel:1 -n myservice --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

További információért tekintse meg az [az ml model deploy reference (az az ml model deploy referencia)](/cli/azure/ml/model#az_ml_model_deploy) referenciát.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

A VS Code használatával kapcsolatos információkért lásd: [Üzembe helyezés az AKS-be a VS Code-bővítményen keresztül.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> A VS Code-ban való üzembe helyezéshez az AKS-fürtöt előzetesen létre kell hozni vagy a munkaterülethez kell csatolni.

---

### <a name="autoscaling"></a>Automatikus skálázás

Az Azure ML-modellek üzembe helyezésének automatikus skálázását kezelő összetevő az azureml-fe, amely egy intelligens kérés-útválasztó. Mivel minden következtetési kérelem áthalad rajta, rendelkezik az üzembe helyezett modell(ök) automatikus méretezéséhez szükséges adatokkal.

> [!IMPORTANT]
> * **Ne engedélyezze a Kubernetes automatikus horizontális podméretezőt (HPA) a modell üzembe helyezéséhez.** Ennek hatására a két automatikus skálázású összetevő versengeni fog egymással. Az Azureml-fe az Azure ML által üzembe helyezett modellek automatikus skálázhatóságára lett tervezve, ahol a HPA-nak egy általános metrika, például a CPU-használat vagy egy egyéni metrikakonfiguráció alapján kellene kitalálnia vagy megközelítené a modell kihasználtságát.
> 
> * **Az Azureml-fe nem** skáláz a csomópontok számát az AKS-fürtökben, mivel ez váratlan költségnövekedéshez vezethet. Ehelyett **a modell** replikáinak számát skálázja a fizikai fürthatáron belül. Ha skáláznia kell a fürtön belüli csomópontok számát, manuálisan skálázhatja a fürtöt, vagy konfigurálhatja az [AKS-fürt automatikus skálázóját.](../aks/cluster-autoscaler.md)

Az automatikus skálázás az AKS-webszolgáltatás , és beállításával `autoscale_target_utilization` `autoscale_min_replicas` `autoscale_max_replicas` szabályozható. Az alábbi példa az automatikus skálázás engedélyezését mutatja be:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

A fel-/leskálával kapcsolatos döntések az aktuális tárolóreplikák kihasználtságán alapulnak. A foglalt replikák száma (kérés feldolgozása), és az aktuális replikák teljes száma az aktuális kihasználtság. Ha ez a szám nagyobb, `autoscale_target_utilization` mint , több replika jön létre. Ha alacsonyabb, akkor a replikák lecsökkennek. Alapértelmezés szerint a cél kihasználtsága 70%.

A replikák hozzáadására vonatkozó döntések lelkesek és gyorsak (körülbelül 1 másodperc). A replikák eltávolításával kapcsolatos döntések óvatosak (körülbelül 1 perc).

A szükséges replikákat a következő kóddal számíthatja ki:

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

A , és beállításával kapcsolatos `autoscale_target_utilization` `autoscale_max_replicas` további információkért tekintse meg az `autoscale_min_replicas` [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice) modul referenciáját.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellek üzembe helyezése az AKS-be szabályozott bevezetés használatával (előzetes verzió)

Modellverziók ellenőrzött módon elemezhetők és előléptethetők végpontok használatával. Egyetlen végpont mögött legfeljebb hat verzió helyezhető üzembe. A végpontok a következő képességeket biztosítják:

* Konfigurálja __az egyes végpontokra küldött pontozási forgalom százalékos arányát.__ Például a forgalom 20%-át a "test" végpontra, a 80%-át pedig az "éles" végpontra irányíthatja.

    > [!NOTE]
    > Ha nem veszi figyelembe a forgalom 100%-át, a fennmaradó százalékok az alapértelmezett __végpontverzióra__ vannak irányítva. Ha például úgy konfigurálja a "test" végpontverziót, hogy az le tudja kapni a forgalom 10%-át, és 30%-hoz "prod"-t, a rendszer a fennmaradó 60%-ot az alapértelmezett végpontverzióra küldi.
    >
    > A rendszer automatikusan alapértelmezettként konfigurálja az első létrehozott végpontverziót. Ezt a beállítást a végpontverzió létrehozásakor `is_default=True` vagy frissítésekkor módosíthatja.
     
* Jelölje meg a végpontverziót __vezérlőként vagy__ __kezelésként.__ Előfordulhat például, hogy a vezérlő az aktuális éles végpont verziója, a potenciális új modellek pedig kezelési verzióként vannak üzembe telepítve. A kezelési verziók teljesítményének kiértékelését követően, ha az egyik a jelenleginél jobb, előléptetjük az új termelési/vezérlési verzióra.

    > [!NOTE]
    > Csak egy __vezérlővel lehet.__ Több kezelés is lehet.

Az App Insights segítségével megtekintheti a végpontok és az üzembe helyezett verziók működési metrikákat.

### <a name="create-an-endpoint"></a>Végpont létrehozása
Ha készen áll a modellek üzembe helyezésére, hozzon létre egy pontozási végpontot, és telepítse az első verziót. Az alábbi példa bemutatja, hogyan helyezheti üzembe és hozhatja létre a végpontot az SDK használatával. Az első üzemelő példány az alapértelmezett verzióként lesz meghatározva, ami azt jelenti, hogy az összes verzióra vonatkozó meghatározatlan forgalom percentilis az alapértelmezett verzióra fog átmenni.  

> [!TIP]
> A következő példában a konfiguráció úgy állítja be a kezdeti végpontverziót, hogy az kezelje a forgalom 20%-át. Mivel ez az első végpont, egyben az alapértelmezett verzió is. Mivel a forgalom többi 80%-ában nincs más verzió, a rendszer az alapértelmezett verzióra is át lesz irányítva. Amíg nem helyez üzembe más, a forgalom egy százalékát átvesző verziókat, ez gyakorlatilag a forgalom 100%-át kapja meg.

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

Adjon hozzá egy másik verziót a végponthoz, és konfigurálja a verzióra áttért pontozási forgalom percentilisét. Kétféle verzió létezik: egy vezérlő és egy kezelési verzió. Több kezelési verzió is segíthet összehasonlítani őket egyetlen kontrollverzióval.

> [!TIP]
> A következő kódrészlet által létrehozott második verzió a forgalom 10%-át fogadja el. Az első verzió 20%-ra van konfigurálva, így a forgalomnak csak 30%-a van konfigurálva adott verziókhoz. A fennmaradó 70% az első végpontverzióra lesz elküldve, mivel ez egyben az alapértelmezett verzió is.

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

Frissítse a meglévő verziókat, vagy törölje őket egy végponton. Módosíthatja a verzió alapértelmezett típusát, vezérlőtípusát és a forgalom percentilisét. A következő példában a második verzió 40%-ra növeli a forgalmat, és mostantól ez az alapértelmezett.

> [!TIP]
> Az alábbi kódrészlet után a második verzió az alapértelmezett. Most már 40%-ra van konfigurálva, míg az eredeti verzió még mindig 20%-ra van konfigurálva. Ez azt jelenti, hogy a verziókonfigurációk nem veszik figyelembe a forgalom 40%-át. A rendszer a második verzióra fogja irányítva a megmaradt forgalmat, mert az már alapértelmezett. Hatékonyan fogadja a forgalom 80%-át.

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

A központi telepítés Azure Kubernetes Service __a kulcsalapú__ hitelesítés alapértelmezés szerint engedélyezve van. Emellett jogkivonat-alapú __hitelesítést is__ engedélyezhet. A jogkivonat-alapú hitelesítéshez az ügyfeleknek egy Azure Active Directory-fiókkal kell hitelesítési jogkivonatot kérniük, amely az üzembe helyezett szolgáltatás felé kérések igényléséhez használható.

A __hitelesítés__ letiltásához állítsa be a `auth_enabled=False` paramétert az üzembe helyezési konfiguráció létrehozásakor. Az alábbi példa letiltja az SDK-val való hitelesítést:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

További információ az ügyfélalkalmazásból való hitelesítésről: [Consume an Azure Machine Learning model deployed as a web service ..](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Hitelesítés kulcsokkal

Ha a kulcshitelesítés engedélyezve van, az módszerrel lekérhet egy elsődleges `get_keys` és egy másodlagos hitelesítési kulcsot:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Ha újra létre kell hoznia egy kulcsot, használja a [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Hitelesítés jogkivonatokkal

A jogkivonat-hitelesítés engedélyezéséhez állítsa be a paramétert az üzemelő példány létrehozásakor vagy `token_auth_enabled=True` frissítésekkor. Az alábbi példa engedélyezi a jogkivonatok SDK-val való hitelesítését:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Ha a jogkivonat-hitelesítés engedélyezve van, a metódussal lekérhet egy JWT-jogkivonatot és a jogkivonat `get_token` lejárati idejét:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> A jogkivonat ideje után új jogkivonatot kell `refresh_by` kérnie.
>
> A Microsoft határozottan javasolja, hogy a Azure Machine Learning munkaterületet ugyanabban a régióban hozza létre, mint a Azure Kubernetes Service fürtöt. A jogkivonattal való hitelesítéshez a webszolgáltatás hívást fog végezni arra a régióra, amelyben a Azure Machine Learning létre. Ha a munkaterület régiója nem érhető el, akkor nem fog tudni jogkivonatot lekérni a webszolgáltatáshoz még akkor sem, ha a fürt a munkaterülettől eltérő régióban van. Ez gyakorlatilag azt váltja ki, hogy a jogkivonat-alapú hitelesítés nem lesz elérhető, amíg a munkaterület régiója újra elérhetővé nem válik. Emellett minél nagyobb a távolság a fürt régiója és a munkaterület régiója között, annál hosszabb ideig tart lekérni a jogkivonatot.
>
> Jogkivonat lekérése érdekében az Azure Machine Learning SDK-t vagy [az az ml service get-access-token parancsot kell használnia.](/cli/azure/ml/service#az_ml_service_get_access_token)


### <a name="vulnerability-scanning"></a>Biztonsági rések vizsgálata

Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Érdemes engedélyeznie, Azure Security Center hogy a felhasználók beolvassa az erőforrásokat, és kövesse a rá vonatkozó javaslatokat. További információ: [Az Azure Kubernetes Services integrációja a Security Center.](../security-center/defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Következő lépések

* [Az Azure RBAC használata Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)
* [Biztonságos következtetési környezet az Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembehelyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi tanulási modell felhasználta](how-to-consume-web-service.md)
* [Monitor a Azure Machine Learning modelleket a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése éles modellekhez](how-to-enable-data-collection.md)
