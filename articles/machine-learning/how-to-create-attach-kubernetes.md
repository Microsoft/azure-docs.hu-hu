---
title: Azure Kubernetes-szolgáltatás létrehozása és csatlakoztatása
titleSuffix: Azure Machine Learning
description: Az Azure Kubernetes Service (ak) használatával gépi tanulási modellt telepíthet webszolgáltatásként. Megtudhatja, hogyan hozhat létre egy új AK-fürtöt Azure Machine Learningon keresztül. Azt is megtudhatja, hogyan csatolhat egy meglévő AK-fürtöt a Azure Machine Learning-munkaterülethez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: cade5a4329cdfc11c1b256ba01e9764f60a476a6
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667860"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása és csatolása

A Azure Machine Learning betanított gépi tanulási modelleket telepíthet az Azure Kubernetes Service-be. Először azonban __létre kell hoznia__ egy Azure Kubernetes Service (ak) fürtöt az Azure ml-munkaterületről, vagy __csatolnia__ kell egy meglévő AK-fürtöt. Ez a cikk a fürt létrehozásával és csatolásával kapcsolatos információkat tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

- Ha Azure-Virtual Network használatát tervezi az Azure ML-munkaterület és az AK-fürt közötti kommunikáció biztonságossá tételéhez, olvassa el a [hálózati elkülönítést a & következtetések betanítása](how-to-enable-virtual-network.md) című cikkben.

## <a name="limitations"></a>Korlátozások

- Ha egy alapszintű Load Balancer (BLB) helyett a fürtben telepített **standard Load Balancerra (SLB)** van szüksége, hozzon létre egy fürtöt az AK-portálon/CLI/SDK-ban, majd **CSATOLja** a pénzmosás-munkaterülethez.

- Ha olyan Azure Policy rendelkezik, amely korlátozza a nyilvános IP-címek létrehozását, az AK-fürt létrehozása sikertelen lesz. Az ak-nak nyilvános IP-címet kell megadnia a [kimenő forgalomhoz](/azure/aks/limit-egress-traffic). A kimenő forgalomról szóló cikk emellett útmutatást nyújt a kimenő forgalomnak a fürtön keresztül a nyilvános IP-címen történő zárolásához, kivéve néhány teljes tartománynevet. A nyilvános IP-címek engedélyezésének két módja van:
    - A fürt a BLB vagy a SLB által alapértelmezés szerint létrehozott nyilvános IP-címet is használhatja, vagy
    - A fürt nyilvános IP-cím nélkül hozható létre, és a nyilvános IP-cím egy felhasználó által megadott útvonallal rendelkező tűzfallal van konfigurálva. További információ: a [fürt kimenő adatainak testreszabása felhasználó által definiált útvonalon](/azure/aks/egress-outboundtype).
    
    A pénzmosás-vezérlési sík nem kommunikál ehhez a nyilvános IP-címhez. A központi telepítések esetében az AK-vezérlési síkon beszél. 

- Ha egy AK-fürtöt **csatlakoztat** , amelynek [engedélyezett IP-tartománya engedélyezve van az API-kiszolgáló eléréséhez](/azure/aks/api-server-authorized-ip-ranges), engedélyezze a pénzmosás-vezérlési sík IP-tartományait az AK-fürthöz. A pénzmosás-vezérlési sík a párosított régiókban van üzembe helyezve, és következtetéseket helyez üzembe az AK-fürtön. Az API-kiszolgálóhoz való hozzáférés nélkül a következtetések nélküli hüvelyek nem helyezhetők üzembe. A [párosított régiók](/azure/best-practices-availability-paired-regions) [IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=56519) is használhatja, ha egy AK-fürtben engedélyezi az IP-tartományokat.

    A jóváhagyott IP-címtartományok csak standard Load Balancer esetén működnek.

- Ha privát AK-fürtöt szeretne használni (az Azure Private link használatával), először létre kell hoznia a fürtöt, majd **csatolnia** kell a munkaterülethez. További információt a [privát Azure Kubernetes Service-fürt létrehozása](/azure/aks/private-clusters)című témakörben talál.

- Az AK-fürthöz tartozó számítási névnek egyedinek kell lennie az Azure ML-munkaterületen belül.
    - A név megadása kötelező, és legfeljebb 3 – 24 karakter hosszúságú lehet.
    - Az érvényes karakterek a kis-és nagybetűk, a számjegyek és a karakter.
    - A névnek betűvel kell kezdődnie.
    - A névnek egyedinek kell lennie az Azure-régióban lévő összes számításban. Ha a választott név nem egyedi, akkor riasztás jelenik meg.
   
 - Ha a modelleket **GPU** -csomópontokra vagy **FPGA** -csomópontokra (vagy bármely konkrét SKU-ra) szeretné telepíteni, akkor létre kell hoznia egy FÜRTÖt az adott SKU-val. Nem támogatott másodlagos csomópont-készlet létrehozása meglévő fürtben, valamint modellek üzembe helyezése a másodlagos csomópont-készletben.
 
- Fürt létrehozásakor vagy csatolásakor kiválaszthatja, hogy a fürtöt __fejlesztési-tesztelési__ vagy __éles__környezetben kívánja-e létrehozni. Ha nem éles környezetben szeretné létrehozni a __fejlesztéshez__, az __ellenőrzéshez__és a __teszteléshez__ egy AK-fürtöt, állítsa be a __fürt célját__ a __dev-test__értékre. Ha nem határozza meg a fürt célját, a rendszer létrehoz egy __éles__ fürtöt. 

    > [!IMPORTANT]
    > A __fejlesztési és tesztelési__ fürtök nem alkalmasak a termelési szint forgalmára, és növelhetik a következtetési időt. A fejlesztési és tesztelési fürtök szintén nem garantálják a hibatűrést.

- Fürt létrehozásakor vagy csatolásakor, ha a fürtöt __éles__környezetben fogja használni, legalább 12 __virtuális processzort__kell tartalmaznia. A virtuális processzorok száma a fürtben lévő __csomópontok számának__ és a kiválasztott virtuálisgép-méret által biztosított __magok__ számának szorzatával számítható ki. Ha például "Standard_D3_v2" virtuálisgép-méretet használ, amely 4 virtuális maggal rendelkezik, akkor a csomópontok számának 3 vagy annál nagyobb értéket kell választania.

    A __fejlesztési-tesztelési__ fürtök esetében legalább 2 virtuális processzort kell újrakövetelni.

- Az Azure Machine Learning SDK nem támogatja az AK-fürtök méretezését. A fürt csomópontjainak méretezéséhez használja a Azure Machine Learning Studióban az AK-fürt felhasználói felületét. Csak a csomópontok számát módosíthatja, nem a fürt virtuálisgép-méretét. Az AK-fürtök csomópontjainak méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

    - [Csomópontok számának manuális skálázása egy AK-fürtben](../aks/scale-cluster.md)
    - [Fürt autoskálázásának beállítása az AK-ban](../aks/cluster-autoscaler.md)

## <a name="azure-kubernetes-service-version"></a>Az Azure Kubernetes Service verziója

Az Azure Kubernetes szolgáltatás lehetővé teszi, hogy különböző Kubernetes-verziók használatával hozzon létre egy fürtöt. További információ az elérhető verziókról: [támogatott Kubernetes-verziók az Azure Kubernetes szolgáltatásban](/azure/aks/supported-kubernetes-versions).

Ha az alábbi módszerek egyikével **hozza létre** az Azure Kubernetes Service-fürtöt, akkor a létrehozott fürt *verziója nem választható* :

* Azure Machine Learning Studio vagy a Azure Portal Azure Machine Learning szakasza.
* Machine Learning bővítmény az Azure CLI-hez.
* Azure Machine Learning SDK.

Az AK-fürtök létrehozásának módszerei a fürt __alapértelmezett__ verzióját használják. *Az alapértelmezett verzió módosul az idő múlásával* , mivel az új Kubernetes verziók elérhetővé válnak.

Meglévő AK-fürt **csatolásakor** az összes jelenleg támogatott AK-verziót támogatjuk.

> [!NOTE]
> Lehetnek olyan Edge-esetek, amikor egy régebbi fürttel rendelkezik, amely már nem támogatott. Ebben az esetben a csatolási művelet hibát ad vissza, és felsorolja a jelenleg támogatott verziókat.
>
> **Előnézeti** verziókat is csatolhat. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül is elérhetők, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. Az előzetes verziójú verziók használatának támogatása korlátozott lehet. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Elérhető és alapértelmezett verziók

Az elérhető és az alapértelmezett AK-verziók megkereséséhez használja az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -parancsot az [AK Get-Versions](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions)paranccsal. A következő parancs például az USA nyugati régiójában elérhető verziókat adja vissza:

```azurecli-interactive
az aks get-versions -l westus -o table
```

A parancs kimenete az alábbi szöveghez hasonló:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

A fürt Azure Machine Learning használatával történő **létrehozásakor** használt alapértelmezett verzió megkereséséhez használja a `--query` paramétert az alapértelmezett verzió kiválasztásához:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

A parancs kimenete az alábbi szöveghez hasonló:

```text
Result
--------
1.16.13
```

Ha **programozottan szeretné megtekinteni az elérhető verziókat**, használja a [Container Service ügyfél-lista](https://docs.microsoft.com/rest/api/container-service/container%20service%20client/listorchestrators) rendszerszervezőket REST API. Az elérhető verziók megkereséséhez tekintse meg a következő bejegyzéseket: `orchestratorType` `Kubernetes` . A társított `orchestrationVersion` bejegyzések tartalmazzák a munkaterülethez csatlakoztatható elérhető **attached** verziókat.

A fürt Azure Machine Learningon keresztüli **létrehozásakor** használt alapértelmezett verzió megkereséséhez keresse meg azt a bejegyzést, ahol `orchestratorType` az a `Kubernetes` és `default` az `true` . A társított `orchestratorVersion` érték az alapértelmezett verzió. A következő JSON-kódrészlet egy példa bejegyzést mutat be:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Új AK-fürt létrehozása

**Becsült idő**: körülbelül 10 perc.

Egy AK-fürt létrehozása vagy csatolása egy egyszeri folyamat a munkaterülethez. Ezt a fürtöt több központi telepítéshez is felhasználhatja. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, létre kell hoznia egy új fürtöt, amikor legközelebb telepítenie kell. A munkaterülethez több AK-alapú fürtök is tartozhatnak.

Az alábbi példa bemutatja, hogyan hozhat létre egy új AK-fürtöt az SDK és a parancssori felület használatával:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

További információ: az [ml computetarget Create AK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az AK-fürtök portálon való létrehozásával kapcsolatos információkért lásd: [számítási célok létrehozása a Azure Machine learning Studióban](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Meglévő AK-fürt csatolása

**Becsült idő:** Körülbelül 5 percet vesz igénybe.

Ha már rendelkezik AK-fürttel az Azure-előfizetésben, és 1,17-es vagy alacsonyabb verziójú, akkor a lemezkép üzembe helyezéséhez használhatja azt.

> [!TIP]
> A meglévő AK-fürt a Azure Machine Learning munkaterülettől eltérő Azure-régióban is lehet.


> [!WARNING]
> Ne hozzon létre több, egyidejű mellékletet ugyanahhoz az AK-fürthöz a munkaterületről. Például egy AK-fürt csatolása egy munkaterülethez két különböző név használatával. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.
>
> Ha egy AK-fürtöt újra szeretne csatlakoztatni, például a TLS vagy más fürtkonfiguráció beállításának módosításához, először el kell távolítania a meglévő mellékletet a [AksCompute. leválasztás ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#detach--)használatával.

A következő cikkekből megtudhatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI vagy a portál használatával:

* [AKS-fürt létrehozása (parancssori felület)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest&preserve-view=true#az-aks-create)
* [AK-fürt létrehozása (portál)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest&preserve-view=true)
* [AK-fürt létrehozása (ARM-sablon az Azure Gyorsindítás sablonjain)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Az alábbi példa bemutatja, hogyan csatolhat egy meglévő AK-fürtöt a munkaterülethez:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Meglévő fürt parancssori felülettel való csatolásához le kell kérnie a meglévő fürt erőforrás-AZONOSÍTÓját. Az érték beszerzéséhez használja a következő parancsot. Cserélje le a `myexistingcluster` nevet az AK-fürt nevére. Cserélje le a helyére a `myresourcegroup` fürtöt tartalmazó erőforráscsoportot:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Ha a meglévő fürtöt a munkaterülethez szeretné csatolni, használja a következő parancsot. Cserélje le az `aksresourceid` értéket az előző parancs által visszaadott értékre. Cserélje le a- `myresourcegroup` t a munkaterületet tartalmazó erőforráscsoporthoz. Cserélje le a `myworkspace` nevet a munkaterület nevére.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

További információ: az [ml computetarget Attach AK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-attach-aks) -referenciája.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az AK-fürtök portálon való csatlakoztatásával kapcsolatos információkért lásd: [számítási célok létrehozása a Azure Machine learning Studióban](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="detach-an-aks-cluster"></a>AK-fürt leválasztása

A fürt munkaterületről való leválasztásához használja az alábbi módszerek egyikét:

> [!WARNING]
> A gépi tanuláshoz készült Azure Machine Learning Studio, SDK vagy az Azure CLI bővítmény használata az AK-fürtök leválasztásához **nem törli az AK**-fürtöt. A fürt törléséhez lásd: [Az Azure CLI használata az AK-val](/azure/aks/kubernetes-walkthrough#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A meglévő fürt munkaterületre való leválasztásához használja az alábbi parancsot. Cserélje le azt `myaks` a nevet, amelyet az AK-fürt a munkaterülethez csatol. Cserélje le a- `myresourcegroup` t a munkaterületet tartalmazó erőforráscsoporthoz. Cserélje le a `myworkspace` nevet a munkaterület nevére.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

Azure Machine Learning Studióban válassza ki a __számítás__, a __következtetési fürtök__és az eltávolítani kívánt fürtöt. A fürt leválasztásához használja a __leválasztási__ hivatkozást.

## <a name="next-steps"></a>Következő lépések

* [Modell üzembe helyezésének módja és helye](how-to-deploy-and-where.md)
* [Modell üzembe helyezése Azure Kubernetes Service-fürtön](how-to-deploy-azure-kubernetes-service.md)