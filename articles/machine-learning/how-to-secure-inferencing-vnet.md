---
title: Biztonságos következtetési környezetek virtuális hálózatokkal
titleSuffix: Azure Machine Learning
description: Elkülönített Azure-Virtual Network használatával biztosíthatja a Azure Machine Learning következtetési környezetet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 610ab82bfc4665fbb30aa3d3bc0448fa9338689c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872550"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Biztonságossá Azure Machine Learning következtetési környezetben virtuális hálózatokkal

Ebből a cikkből megtudhatja, hogyan biztosíthatja a következtetési környezetek biztonságossá Azure Machine Learning.

Ez a cikk egy ötrészes sorozat negyedik része, amely végigvezeti egy Azure Machine Learning biztosításán. Javasoljuk, hogy először olvassa el az [első rész: VNet](how-to-network-security-overview.md) áttekintése részt, hogy először a teljes architektúrát értse meg. 

Tekintse meg a sorozat többi cikkét:

[1. Virtuális hálózat áttekintése](how-to-network-security-overview.md)  >  [A munkaterület biztonságossáése](how-to-secure-workspace-vnet.md)  >  [3. A betanító környezet védelme](how-to-secure-training-vnet.md)  >  **4. A következtetési környezet**  >  [5. védelme. Studio-funkciók engedélyezése](how-to-enable-studio-virtual-network.md)

Ebből a cikkből megtudhatja, hogyan biztosíthatja a következő következtetési erőforrások biztonságát egy virtuális hálózatban:
> [!div class="checklist"]
> - Alapértelmezett Azure Kubernetes Service (AKS-) fürt
> - Privát AKS-fürt
> - AKS-fürt privát kapcsolatokkal
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Előfeltételek

+ A gyakori [virtuális hálózati forgatókönyvekkel](how-to-network-security-overview.md) és a teljes virtuális hálózati architektúrával kapcsolatos további információért olvassa el a Hálózati biztonság áttekintése cikket.

+ Meglévő virtuális hálózat és alhálózat, amely a számítási erőforrásokkal használható.

+ Az erőforrások virtuális hálózaton vagy alhálózaton való üzembe helyezéséhez a felhasználói fióknak engedéllyel kell rendelkeznie a következő műveletekhez az Azure szerepköralapú hozzáférés-vezérlésében (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" a virtuális hálózati erőforráson.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" az alhálózati erőforráson.

    További információ a hálózattal rendelkező Azure [RBAC-ről: Beépített hálózatépítési szerepkörök](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Egy AKS-fürt virtuális hálózaton való használatának a következő hálózati követelményeknek kell megfelelnie:

> [!div class="checklist"]
> * Kövesse a Speciális hálózat beállítása a Azure Kubernetes Service [(AKS) szolgáltatásban cikk előfeltételét.](../aks/configure-azure-cni.md#prerequisites)
> * Az AKS-példánynak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a munkaterület által egy virtuális hálózatban használt Azure Storage-fiók(ok)nak is ugyanabban a virtuális hálózatban kell lennie, mint az AKS-példánynak.

Ha AKS-t szeretne hozzáadni egy virtuális hálózathoz a munkaterülethez, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Machine Learning stúdió,](https://ml.azure.com/)majd válassza ki az előfizetését és a munkaterületét.

1. A __bal oldalon__ válassza a Számítás lehetőséget.

1. Válassza __a Dedigenciafürtök__ lehetőséget a középpontban, majd válassza a __+__ lehetőséget.

1. Az Új __következtetési fürt__ párbeszédpanelen válassza a Speciális __lehetőséget__ a __Hálózati konfiguráció alatt.__

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, hajtsa végre a következő műveleteket:

    1. Az __Erőforráscsoport legördülő__ listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A Virtuális __hálózat legördülő__ listában válassza ki az alhálózatot tartalmazó virtuális hálózatot.
    1. Az __Alhálózat legördülő__ listában válassza ki az alhálózatot.
    1. A __Kubernetes Service címtartománya mezőben__ adja meg a Kubernetes-szolgáltatás címtartományát. Ez a címtartomány osztály nélküli útválasztási Inter-Domain (CIDR) ip-címtartományt használ a fürt számára elérhető IP-címek meghatározásához. Nem lehet átfedésben az alhálózati IP-címtartományokkal (például 10.0.0.0/16).
    1. A __Kubernetes DNS-szolgáltatás IP-címe__ mezőbe írja be a Kubernetes DNS-szolgáltatás IP-címét. Ez az IP-cím a Kubernetes DNS-szolgáltatáshoz van rendelve. A Kubernetes-szolgáltatás címtartományában kell lennie (például 10.0.0.10).
    1. A __Docker-híd címe mezőbe__ írja be a Docker-híd címét. Ez az IP-cím a Docker Bridge-hez van rendelve. Nem lehet egyetlen alhálózati IP-címtartományban sem, sem a Kubernetes-szolgáltatás címtartományában (például 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute hálózati beállítások konfigurálása](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Amikor webszolgáltatásként helyez üzembe egy modellt az AKS-be, létrejön egy pontozási végpont a következtetési kérelmek kezeléséhez. Ha a virtuális hálózaton kívülről szeretné hívni, győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoportban engedélyezve van egy bejövő biztonsági szabály a pontozási végpont IP-címére.

    A pontozási végpont IP-címének megkereséhez keresse meg az üzembe helyezett szolgáltatás pontozási URI-ját. A pontozási URI megtekintésével kapcsolatos információkért lásd: Webszolgáltatásként üzembe helyezett [modell felhasználta.](how-to-consume-web-service.md#connection-information)

   > [!IMPORTANT]
   > Tartsa meg az NSG alapértelmezett kimenő szabályait. További információkért lásd a Biztonsági csoportok alapértelmezett [biztonsági szabályait.](../virtual-network/network-security-groups-overview.md#default-security-rules)

   [![Bejövő biztonsági szabály](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > A pontozási végponthoz a képen látható IP-cím az üzemelő példányok esetén eltérő lesz. Bár az összes üzemelő példány ugyanazt az IP-címet osztja meg egy AKS-fürtön, minden AKS-fürt eltérő IP-címmel fog üzemelni.

Az Azure Machine Learning SDK-val virtuális Azure Kubernetes Service is hozzáadhat. Ha már rendelkezik AKS-fürttel egy virtuális hálózatban, csatolja azt a munkaterülethez a [How to deploy to AKS (Üzembe helyezés az AKS-be) dokumentumban leírtak szerint.](how-to-deploy-and-where.md) A következő kód létrehoz egy új AKS-példányt egy nevű virtuális `default` hálózat alhálózatában: `mynetwork`

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

A létrehozási folyamat befejezése után deduktációt vagy modellpontozást futtathat egy virtuális hálózat mögötti AKS-fürtön. További információ: Üzembe helyezés [az AKS-be.](how-to-deploy-and-where.md)

További információ a kubernetes-Role-Based Access Control a Use [Azure RBAC for Kubernetes authorization (Azure RBAC használata Kubernetes-hitelesítéshez) című Role-Based Access Control.](../aks/manage-azure-rbac.md)

## <a name="network-contributor-role"></a>Hálózati közreműködői szerepkör

> [!IMPORTANT]
> Ha korábban létrehozott virtuális hálózat biztosításával hoz létre vagy csatol egy AKS-fürtöt, az AKS-fürt  szolgáltatásnév (SP) vagy felügyelt identitása számára a Hálózati közreműködő szerepkört kell megadja a virtuális hálózatot tartalmazó erőforráscsoportnak.
>
> Az identitás hálózati közreműködőként való hozzáadásához kövesse az alábbi lépéseket:

1. Az AKS szolgáltatásnév vagy felügyelt identitásazonosítójának megkereshez használja a következő Azure CLI-parancsokat. Cserélje `<aks-cluster-name>` le a helyére a fürt nevét. Cserélje le a helyére az `<resource-group-name>` AKS-fürtöt tartalmazó _erőforráscsoport nevét:_

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Ha ez a parancs értéket ad vissza, használja a következő parancsot a felügyelt identitás `msi` egyszerű azonosítójának azonosításához:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. A virtuális hálózatot tartalmazó erőforráscsoport azonosítóját az alábbi paranccsal találhatja meg. Cserélje le a helyére a virtuális hálózatot `<resource-group-name>` tartalmazó _erőforráscsoport nevét:_

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. A szolgáltatásnév vagy a felügyelt identitás hálózati közreműködőként való hozzáadásához használja a következő parancsot. Cserélje `<SP-or-managed-identity>` le a helyére a szolgáltatásnévhez vagy a felügyelt identitáshoz visszaadott azonosítót. Cserélje le a helyére a virtuális hálózatot tartalmazó `<resource-group-id>` erőforráscsoport által visszaadott azonosítót:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
További információ a belső terheléselosztás AKS-sel való használatával kapcsolatban: Belső terheléselosztás használata a [Azure Kubernetes Service.](../aks/internal-lb.md)

## <a name="secure-vnet-traffic"></a>Biztonságos virtuális hálózati forgalom

Az AKS-fürt és a virtuális hálózat között kétféle módszer létezik az adatforgalom elkülönítéséhez:

* __Privát AKS-fürt:__ Ez a megközelítés Azure Private Link a fürttel való kommunikáció biztonságossá helyezéséhez az üzembe helyezési/felügyeleti műveletekhez.
* __Belső AKS-terheléselosztás:__ Ez a megközelítés úgy konfigurálja az AKS-hez üzemelő példányok végpontját, hogy magánhálózati IP-címet használjanak a virtuális hálózaton belül.

> [!WARNING]
> A belső terheléselosztás nem működik kubenetet használó AKS-fürtökön. Ha egyszerre szeretne használni egy belső terheléselosztást és egy privát AKS-fürtöt, konfigurálja a privát AKS-fürtöt Azure Container Networking Interface (CNI). További információ: [Hálózati Azure CNI konfigurálása a Azure Kubernetes Service.](../aks/configure-azure-cni.md)

### <a name="private-aks-cluster"></a>Privát AKS-fürt

Alapértelmezés szerint az AKS-fürtök egy vezérlősíkkal vagy API-kiszolgálóval, nyilvános IP-címekkel vannak. Az AKS-t privát vezérlősík használatára konfigurálhatja egy privát AKS-fürt létrehozásával. További információ: Privát fürt [Azure Kubernetes Service.](../aks/private-clusters.md)

A privát AKS-fürt [](how-to-create-attach-kubernetes.md) létrehozása után csatlakoztassa a fürtöt a virtuális hálózathoz, hogy az Azure Machine Learning.

> [!IMPORTANT]
> Mielőtt privát kapcsolattal rendelkező AKS-fürtöt használ Azure Machine Learning, meg kell nyitnia egy támogatási incidenst a funkció engedélyezéséhez. További információ: [Kvóták kezelése és növelése.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

### <a name="internal-aks-load-balancer"></a>Belső AKS-terheléselosztás

Alapértelmezés szerint az AKS üzemelő példányai nyilvános [terheléselosztást használnak.](../aks/load-balancer-standard.md) Ebben a szakaszban megtudhatja, hogyan konfigurálhatja az AKS-t belső terheléselosztás használatára. Belső (vagy privát) terheléselosztást használ, ahol csak privát IP-k engedélyezettek előtereként. A belső terheléselosztás a virtuális hálózaton belüli forgalom terheléselosztásához használható

A privát terheléselosztás úgy engedélyezhető, hogy az AKS-t belső terheléselosztás _használatára konfigurálja._ 

#### <a name="enable-private-load-balancer"></a>Privát terheléselosztás engedélyezése

> [!IMPORTANT]
> A magánhálózati IP-cím nem engedélyezhető a Azure Kubernetes Service fürt létrehozásakor a Azure Machine Learning stúdió. Belső terheléselosztással létrehozhat egyet, ha a Python SDK-t vagy az Azure CLI-bővítményt használja a gépi tanuláshoz.

Az alábbi példák bemutatják, hogyan hozhat létre új AKS-fürtöt privát __IP-címmel/belső terheléselosztással__ az SDK és a CLI használatával:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> A CLI használatával csak belső terheléselosztással hozhat létre AKS-fürtöt. Nem létezik az az ml parancs egy meglévő fürt belső terheléselosztás használatára való frissítéséhez.

További információkért lásd az [az ml computetarget create aks](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks) referenciát.

---

Ha __meglévő fürtöt csatlakoztat a__ munkaterülethez, meg kell várnia, amíg a csatolási művelet után konfigurálja a terheléselosztást. A fürtök csatolásának információiért lásd: [Meglévő AKS-fürt csatolása.](how-to-create-attach-kubernetes.md)

A meglévő fürt csatolása után frissítheti a fürtöt egy belső terheléselosztási/magánhálózati IP-cím használatára:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>A Azure Container Instances (ACI) engedélyezése

Azure Container Instances a rendszer dinamikusan hoz létre egy modellt. Ha engedélyezni Azure Machine Learning, hogy ACI-t hozzon létre  a virtuális hálózaton belül, engedélyeznie kell az alhálózat delegálását az üzembe helyezés által használt alhálózathoz.

> [!WARNING]
> Ha virtuális Azure Container Instances használ, a virtuális hálózatnak a következőnek kell lennie:
> * Ugyanabban az erőforráscsoportban, mint a Azure Machine Learning munkaterületen.
> * Ha a munkaterület privát __végponttal__ rendelkezik, a munkaterülethez használt Azure Container Instances hálózatnak egy kell lennie a munkaterület privát végpontja által használt hálózattal.
>
> Ha virtuális Azure Container Instances használ, a munkaterülethez Azure Container Registry virtuális hálózaton belüli virtuális hálózat (ACR) nem lehet.

Ha az ACI-t virtuális hálózaton keresztül a munkaterülethez használja, kövesse az alábbi lépéseket:

1. Ha engedélyezni szeretné az alhálózat delegálását a virtuális hálózaton, használja az Alhálózat-delegálás hozzáadása vagy eltávolítása cikkben [található](../virtual-network/manage-subnet-delegation.md) információkat. Engedélyezheti a delegálást a virtuális hálózatok létrehozásakor, vagy hozzáadhatja egy meglévő hálózathoz.

    > [!IMPORTANT]
    > A delegálás engedélyezésekor használja `Microsoft.ContainerInstance/containerGroups` a értéket az __Alhálózat delegálása szolgáltatásértékként.__

2. A modell üzembe helyezése a [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)használatával, használja a `vnet_name` és a `subnet_name` paramétert. Állítsa be ezeket a paramétereket arra a virtuális hálózat nevére és alhálózatra, ahol engedélyezte a delegálást.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>A virtuális hálózat kimenő kapcsolatainak korlátozása

Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, engedélyeznie kell a hozzáférést a Azure Container Registry. Győződjön meg például arról, hogy a hálózati biztonsági csoportok (NSG) tartalmaznak egy szabályt, amely engedélyezi a hozzáférést az __AzureContainerRegistry.RegionName__ szolgáltatáscímkéhez, ahol a {RegionName} egy Azure-régió neve.

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy ötrészes virtuális hálózati sorozat negyedik része. A további cikkekből megtudhatja, hogyan biztosíthatja a virtuális hálózatok biztonságát:

* [1. rész: A virtuális hálózat áttekintése](how-to-network-security-overview.md)
* [2. rész: A munkaterület erőforrásainak biztonságossáése](how-to-secure-workspace-vnet.md)
* [3. rész: A betanító környezet védelme](how-to-secure-training-vnet.md)
* [5. rész: A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Lásd még az egyéni [DNS névfeloldáshoz való](how-to-custom-dns.md) használatával kapcsolatos cikket.