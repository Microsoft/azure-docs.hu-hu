---
title: Hozzon létre és csatoljon Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre új Azure Kubernetes Service fürtöt a Azure Machine Learning használatával, vagy hogyan csatolhat meglévő AKS-fürtöt a munkaterülethez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/08/2021
ms.openlocfilehash: 1c9434d137114560b5585b081961497412dfbf69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770254"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Fürt létrehozása és Azure Kubernetes Service csatolása

Azure Machine Learning gépi tanulási modellek üzembe helyezhetők a Azure Kubernetes Service. Először azonban létre  kell hoznia egy Azure Kubernetes Service-fürtöt az Azure ML-munkaterületről, vagy csatolnia kell __egy__ meglévő AKS-fürtöt. Ez a cikk a fürtök létrehozásáról és csatolásról is tartalmaz információkat.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).

- Az [Azure CLI-bővítmény Machine Learning szolgáltatáshoz,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK-hoz](/python/api/overview/azure/ml/intro)vagy a [Azure Machine Learning Visual Studio Code-bővítményhez.](tutorial-setup-vscode-extension.md)

- Ha azure-beli virtuális gépi tanulási Virtual Network tervezi az Azure ML-munkaterület és az AKS-fürt közötti kommunikáció biztonságát, olvassa el a Hálózatelszigetelés a betanítás és [& során](./how-to-network-security-overview.md) cikket.

## <a name="limitations"></a>Korlátozások

- Ha alapszintű **Load Balancer(BLB) helyett egy standard Load Balancer-fürtre (SLB)** van szüksége, hozzon létre egy fürtöt az AKS-portálon/CLI/SDK-ban, majd csatolja azt az AML-munkaterülethez. 

- Ha olyan hálózati Azure Policy, amely korlátozza a nyilvános IP-címek létrehozását, az AKS-fürt létrehozása sikertelen lesz. Az AKS-hez nyilvános IP-cím szükséges a [bejövő forgalomhoz.](../aks/limit-egress-traffic.md) A forgalomról való kivezetésről is olvashat, amely útmutatást nyújt a fürtből a nyilvános IP-címen keresztül bejövő forgalom zárolására vonatkozóan, néhány teljes tartománynév kivételével. A nyilvános IP-címek engedélyezésének két módja van:
    - A fürt használhatja az alapértelmezés szerint a BLB-hez vagy az SLB-hez létrehozott nyilvános IP-címet, vagy
    - A fürt nyilvános IP-cím nélkül is létre lehet hozni, majd a nyilvános IP-cím egy felhasználó által megadott útvonallal konfigurálható tűzfallal. További információkért [lásd: Fürtök ki- és becslének testreszabása felhasználó által megadott útvonalon.](../aks/egress-outboundtype.md)
    
    Az AML-vezérlősík nem tud ezzel a nyilvános IP-címmel beszélni. Az üzembe helyezéshez az AKS-vezérlősíkhoz beszél. 

- Ha **olyan** AKS-fürtöt csatlakoztat, amelynek engedélyezett IP-címtartománya van az [API-kiszolgáló](../aks/api-server-authorized-ip-ranges.md)eléréséhez, engedélyezze az AML-vezérlősík IP-tartományát az AKS-fürt számára. Az AML-vezérlősík a párosított régiókban van telepítve, és következtetési podokat helyez üzembe az AKS-fürtön. Az API-kiszolgálóhoz való hozzáférés nélkül a következtetési podok nem helyezhetők üzembe. Az [IP-címtartományokat](https://www.microsoft.com/download/confirmation.aspx?id=56519) mindkét [párosított](../best-practices-availability-paired-regions.md) régióban használhatja, amikor engedélyezi az IP-címtartományokat egy AKS-fürtben.

    Az engedélyezett IP-címtartományok csak a standard Load Balancer.

- AKS-fürt csatolásakor annak ugyanabban az Azure-előfizetésben kell lennie, mint a Azure Machine Learning munkaterületen. 

- Ha privát AKS-fürtöt szeretne használni (Azure Private Link használatával), először létre kell  hoznia a fürtöt, majd csatolnia kell a munkaterülethez. További információ: Privát fürt [Azure Kubernetes Service.](../aks/private-clusters.md)

- Az AKS-fürt számítási nevének egyedinek kell lennie az Azure ML-munkaterületen belül. Betűket, számjegyeket és kötőjeleket tartalmazhat. Betűvel kell kezdődnie, betűvel vagy számmal kell végződnie, és 3–24 karakter hosszúságúnak kell lennie.
 
 - Ha GPU-csomópontokon  vagy **FPGA-csomópontokon** (vagy bármely adott termékváltozatban) szeretne modelleket üzembe helyezni, létre kell hoznia egy fürtöt az adott termékváltozattal. A másodlagos csomópontkészlet meglévő fürtben való létrehozása és a modellek a másodlagos csomópontkészletben való üzembe helyezése nem támogatott.
 
- Fürt létrehozásakor vagy csatolásakor kiválaszthatja, hogy a fürtöt __fejlesztési-tesztelési__ vagy éles környezetben hozza __létre.__ Ha éles környezet helyett __fejlesztési,__ ellenőrzési és tesztelési célokra szeretne  létrehozni egy AKS-fürtöt, állítsa a fürtöt __fejlesztési-tesztelési célra.__  Ha nem adja meg a fürt célját, létrejön egy __éles__ fürt. 

    > [!IMPORTANT]
    > A __fejlesztési-tesztelési__ fürtök nem alkalmasak éles szintű forgalomhoz, és növelhetik a következtetési időket. A fejlesztési/tesztelési fürtök szintén nem garantálják a hibatűrést.

- Fürt létrehozásakor vagy csatolásakor, ha a fürt éles környezetben lesz használva, akkor legalább 12 virtuális CPU-t __kell tartalmaznia.__ A virtuális processzorok száma a fürtben  lévő csomópontok számának  és a kiválasztott virtuálisgép-méret által megadott magok számának szorozása alapján számítható ki. Ha például a "Standard_D3_v2" virtuálisgép-méretet használja, amely 4 virtuális maggal rendelkezik, akkor a csomópontok számaként válassza a 3-as vagy nagyobb lehetőséget.

    __Fejlesztési-tesztelési fürt esetén__ legalább 2 virtuális CPU-t ajánlunk.

- Az Azure Machine Learning SDK nem támogatja az AKS-fürtök skálázását. A fürt csomópontjainak méretezéséhez használja az AKS-fürt felhasználói felületét a Azure Machine Learning stúdió. A csomópontok számát csak módosíthatja, a fürt virtuálisgép-méretét nem. Az AKS-fürtök csomópontjainak méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

    - [A csomópontok számának manuális skálázása egy AKS-fürtben](../aks/scale-cluster.md)
    - [Automatikus fürtméretozó beállítása az AKS-ban](../aks/cluster-autoscaler.md)

- __Ne frissítse közvetlenül a fürtöt YAML-konfigurációval.__ Bár az Azure Kubernetes Services YAML-konfiguráción keresztül támogatja a frissítéseket, Azure Machine Learning üzemelő példányok felülírják a módosításokat. Az egyetlen két YAML-mező,  amely nem lesz felülírva, a kérelemkorlátok, valamint a processzor és a __memória.__

- Az AKS-fürt létrehozása az Azure Machine Learning stúdió felhasználói felülettel, SDK-val vagy CLI-bővítménysel __nem__ idempotent. Ha ismét megpróbálja létrehozni az erőforrást, az azt a hibát fogja eredményezni, hogy már létezik ilyen nevű fürt.
    
    - Az AKS Azure Resource Manager létrehozása nem  idempotent, ha egy új sablont és a [Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) erőforrást használja az AKS-fürt létrehozásához. Ha egy már létező erőforrás frissítésére próbálja ismét használni a sablont, ugyanez a hibaüzenet jelenik meg.

## <a name="azure-kubernetes-service-version"></a>Az Azure Kubernetes Service verziója

Azure Kubernetes Service lehetővé teszi a fürtök különböző Kubernetes-verziók használatával való létrehozására. Az elérhető verziókról a támogatott [Kubernetes-verziók](../aks/supported-kubernetes-versions.md)című Azure Kubernetes Service.

Ha **a következő** Azure Kubernetes Service használatával hoz létre egy fürtöt, nincs választási lehetőség *a* létrehozott fürt verziójában:

* Azure Machine Learning stúdió vagy Azure Machine Learning szakasza Azure Portal.
* Machine Learning Azure CLI-hez.
* Azure Machine Learning SDK.

Az AKS-fürt létrehozásának ezen módszerei a fürt __alapértelmezett__ verzióját használják. *Az alapértelmezett verzió idővel megváltozik,* amint új Kubernetes-verziók válnak elérhetővé.

Meglévő  AKS-fürt csatolása esetén az összes jelenleg támogatott AKS-verziót támogatjuk.

> [!NOTE]
> Előfordulhatnak peremhálózati esetek, amelyekben egy régebbi fürt már nem támogatott. Ebben az esetben a csatolási művelet hibát ad vissza, és felsorolja a jelenleg támogatott verziókat.
>
> Csatolhatja az **előzetes verziókat.** Az előzetes verziójú funkciókat szolgáltatói szerződés nélkül biztosítjuk, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. Az előzetes verziók használatának támogatása korlátozott lehet. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Elérhető és alapértelmezett verziók

Az elérhető és alapértelmezett AKS-verziók kereséshez használja [az az aks get-versions Azure CLI-parancsot.](/cli/azure/aks#az_aks_get_versions) [](/cli/azure/install-azure-cli) A következő parancs például az USA nyugati régiójában elérhető verziókat adja vissza:

```azurecli-interactive
az aks get-versions -l westus -o table
```

A parancs kimenete a következő szöveghez hasonló:

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

A fürt létrehozásakor használt alapértelmezett  verzió Azure Machine Learning az paraméterrel választhatja ki `--query` az alapértelmezett verziót:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

A parancs kimenete a következő szöveghez hasonló:

```text
Result
--------
1.16.13
```

Ha programozott módon szeretné ellenőrizni az elérhető verziókat, használja a [Container Service Client – List Orchestrators](/rest/api/container-service/container%20service%20client/listorchestrators) REST API. Az elérhető verziók megkeresésében keresse meg a következő `orchestratorType` bejegyzéseket: `Kubernetes` . A társított bejegyzések tartalmazzák a munkaterülethez csatolható elérhető `orchestrationVersion` verziókat. 

A fürt létrehozásakor használt alapértelmezett  verzió megkereséhez Azure Machine Learning a bejegyzést, ahol a és `orchestratorType` a `Kubernetes` `default` `true` . A társított `orchestratorVersion` érték az alapértelmezett verzió. Az alábbi JSON-kódrészlet egy példabejegyzést mutat be:

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

## <a name="create-a-new-aks-cluster"></a>Új AKS-fürt létrehozása

**Becsült idő:** Körülbelül 10 perc.

Az AKS-fürtök létrehozása vagy csatolása a munkaterület egy egyszeres folyamata. Ezt a fürtöt több üzemelő példányhoz is felhasználhatja. Ha törli a fürtöt vagy az azt tartalmazó erőforráscsoportot, a következő üzembe helyezéskor új fürtöt kell létrehoznia. A munkaterülethez több AKS-fürt is csatolható.

Az alábbi példa bemutatja, hogyan hozhat létre új AKS-fürtöt az SDK és a CLI használatával:

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

A példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő referenciadokumentumokat:

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

További információkért tekintse meg az [az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) referenciát.

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az AKS-fürtök portálon való létrehozásával kapcsolatos információkért lásd: Számítási célok létrehozása a [Azure Machine Learning stúdió.](how-to-create-attach-compute-studio.md#inference-clusters)

---

## <a name="attach-an-existing-aks-cluster"></a>Meglévő AKS-fürt csatolása

**Becsült idő:** Körülbelül 5 perc.

Ha már rendelkezik AKS-fürttel az Azure-előfizetésében, használhatja azt a munkaterülettel.

> [!TIP]
> A meglévő AKS-fürt a saját munkaterületén kívül más Azure Azure Machine Learning is lehet.


> [!WARNING]
> Ne hozzon létre több egyidejű mellékletet ugyanannak az AKS-fürtnek a munkaterületről. Például egy AKS-fürt csatolása egy munkaterülethez két különböző névvel. Minden új melléklet megszakítja a korábbi melléklet(eket).
>
> Ha újra szeretne csatolni egy AKS-fürtöt, például a TLS vagy más fürtkonfigurációs beállítás módosításához, először el kell távolítania a meglévő mellékletet az [AksCompute.detach() használatával.](/python/api/azureml-core/azureml.core.compute.akscompute#detach--)

Az AKS-fürtök Azure CLI vagy portál használatával való létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [AKS-fürt létrehozása (parancssori felület)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [AKS-fürt létrehozása (portál)](../aks/kubernetes-walkthrough-portal.md)
* [AKS-fürt létrehozása (ARM-sablon az Azure gyorsindítási sablonokban)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

Az alábbi példa bemutatja, hogyan csatolhat meglévő AKS-fürtöt a munkaterülethez:

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

A példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő referenciadokumentumokat:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha meglévő fürtöt a CLI használatával kell csatolnia, le kell szereznie a meglévő fürt erőforrás-azonosítóját. Ezt az értéket a következő paranccsal használhatja. Cserélje `myexistingcluster` le a helyére az AKS-fürt nevét. Cserélje `myresourcegroup` le a helyére a fürtöt tartalmazó erőforráscsoportot:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Ez a parancs a következő szöveghez hasonló értéket ad vissza:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

A meglévő fürtöt a következő paranccsal csatlakoztathatja a munkaterülethez. Cserélje `aksresourceid` le a helyére az előző parancs által visszaadott értéket. Cserélje `myresourcegroup` le a helyére a munkaterületet tartalmazó erőforráscsoportot. Cserélje `myworkspace` le a helyére a munkaterület nevét.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

További információt az [az ml computetarget attach aks reference (az ml computetarget attach aks referenciája) tartalmaz.](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az AKS-fürtök portálon való csatolásának információiért lásd: Számítási célok létrehozása a [Azure Machine Learning stúdió.](how-to-create-attach-compute-studio.md#inference-clusters)

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>AKS-fürt létrehozása vagy csatolása TLS-letöltéssel
[AKS-fürt](how-to-create-attach-kubernetes.md)létrehozásakor vagy csatolásakor engedélyezheti a TLS-letöltést **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** és **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** konfigurációs objektumokkal. Mindkét metódus egy konfigurációs objektumot  ad vissza, amely enable_ssl metódussal rendelkezik, és a **enable_ssl** használatával engedélyezheti a TLS-t.

Az alábbi példa bemutatja, hogyan engedélyezheti a TLS-letöltést automatikus TLS-tanúsítvány-generációval és -konfigurációval a Háttérben a Microsoft-tanúsítvány használatával.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
Az alábbi példa bemutatja, hogyan engedélyezheti a TLS-megszakítást egyéni tanúsítvánnyal és egyéni tartománynévvel. Egyéni tartomány és tanúsítvány esetén úgy kell frissítenie a DNS-rekordot, hogy a pontozási végpont IP-címére mutasson. Lásd: [A DNS frissítése](how-to-secure-web-service.md#update-your-dns)

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> A modellek AKS-fürtön való üzembe helyezésének biztonságossá való használatával kapcsolatos további információkért tekintse meg [a TLS](how-to-secure-web-service.md) használatát webszolgáltatás biztonságossá Azure Machine Learning

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>AKS-fürt létrehozása vagy csatolása a belső ip Load Balancer ip-címmel való használathoz
AKS-fürt létrehozásakor vagy csatolásakor a fürtöt belső fürt használatára is Load Balancer. Belső hálózati Load Balancer az AKS-hez való üzemelő példányok pontozási végpontjai privát IP-címet fognak használni a virtuális hálózaton belül. Az alábbi kódrészletek azt mutatják be, hogyan konfigurálható belső Load Balancer egy AKS-fürthöz.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning nem támogatja a TLS belső biztonsági Load Balancer. A Load Balancer IP-címmel rendelkezik, és hogy a magánhálózati IP-cím lehet egy másik hálózaton, és a tanúsítványt vissza lehet szabadítatni. 

>[!NOTE]
> A következtetési környezet biztonságossá teről további információért lásd: Biztonságossá Azure Machine Learning [környezetek](how-to-secure-inferencing-vnet.md)

## <a name="detach-an-aks-cluster"></a>AKS-fürt leválasztása

A fürt munkaterületről való leválasztása a következő módszerek egyikével létezik:

> [!WARNING]
> Ha az AKS Azure Machine Learning stúdió, SDK vagy a gépi tanuláshoz készült Azure CLI-bővítmény használatával leválaszt egy AKS-fürtöt, az nem törli az **AKS-fürtöt.** A fürt törléséhez lásd: Az Azure CLI használata [az AKS-sel.](../aks/kubernetes-walkthrough.md#delete-the-cluster)

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A meglévő fürt a következő paranccsal leválasztható a munkaterületről. Cserélje le a helyére azt a nevet, amely szerint az `myaks` AKS-fürt a munkaterülethez van csatlakoztatva. Cserélje `myresourcegroup` le a helyére a munkaterületet tartalmazó erőforráscsoportot. Cserélje `myworkspace` le a helyére a munkaterület nevét.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Azure Machine Learning stúdió válassza a __Számítás,__ Következtetési fürtök __lehetőséget,__ majd az eltávolítani kívánt fürtöt. Válassza le __a__ fürtöt a Leválasztás hivatkozással.

---

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="update-the-cluster"></a>A fürt frissítése

Az Azure Machine Learning fürtben telepített Azure Kubernetes Service frissítéseit manuálisan kell alkalmazni. 

Ezeket a frissítéseket úgy alkalmazhatja, ha leválasztja a fürtöt a Azure Machine Learning munkaterületről, majd újra a fürtöt a munkaterülethez. Ha a fürtön engedélyezve van a TLS, meg kell adnunk a TLS-/SSL-tanúsítványt és a titkos kulcsot a fürt újraajánlásakor. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Ha már nincs TLS-/SSL-tanúsítványa és titkos kulcsa, vagy ha az Azure Machine Learning által létrehozott tanúsítványt használ, a fürt leválasztása előtt lekérheti a fájlokat a fürthöz való csatlakozással és a titkos kulcs lekérése `kubectl` `azuremlfessl` révén.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>A Kubernetes base-64 kódolású formátumban tárolja a titkos kódokat. A titkos kulcsok és összetevőit a 64-es kóddal kell dekódolni, mielőtt `cert.pem` `key.pem` a számára ad `attach_config.enable_ssl` hozzáférést. 

### <a name="webservice-failures"></a>Webszolgáltatás-hibák

Az AKS-ben számos webszolgáltatás-hiba hibakereséséhez csatlakozzon a fürthöz a `kubectl` használatával. Az `kubeconfig.json` AKS-fürthöz a következő futtatásával kaphatja meg:

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Következő lépések

* [Az Azure RBAC használata Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)
* [Modell üzembe helyezése egy Azure Kubernetes Service fürtön](how-to-deploy-azure-kubernetes-service.md)