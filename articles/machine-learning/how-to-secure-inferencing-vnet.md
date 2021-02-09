---
title: Biztonságos következtetési környezetek virtuális hálózatokkal
titleSuffix: Azure Machine Learning
description: Egy elkülönített Azure-Virtual Network segítségével biztonságossá teheti Azure Machine Learning következtetési környezetét.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 80abe036ef4a08f7ed6f0f19cffc2abde70a6ea9
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981306"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Azure Machine Learning következtetési környezet biztonságossá tétele virtuális hálózatokkal

Ebből a cikkből megtudhatja, hogyan teheti biztonságossá a következtetési környezeteket Azure Machine Learning-beli virtuális hálózattal.

Ez a cikk egy öt részes sorozat negyedik része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy először olvassa el az első [részt: VNet – áttekintés](how-to-network-security-overview.md) az általános architektúra megismeréséhez. 

Tekintse meg a sorozat egyéb cikkeit:

[1. a VNet áttekintése](how-to-network-security-overview.md)  >  [védi a 3. munkaterületet](how-to-secure-workspace-vnet.md)  >  [. Gondoskodjon a 4. képzési környezet védelméről](how-to-secure-training-vnet.md)  >  **. Gondoskodjon az 5. következtetési környezet védelméről**  >  [. A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Ebből a cikkből megtudhatja, hogyan védheti meg a következő, a virtuális hálózatban található erőforrásokat:
> [!div class="checklist"]
> - Alapértelmezett Azure Kubernetes Service (ak) fürt
> - Privát AK-fürt
> - AK-fürt magánhálózati kapcsolattal
> - Azure Container Instances (ACI)


## <a name="prerequisites"></a>Előfeltételek

+ Olvassa el a [hálózati biztonság áttekintése című](how-to-network-security-overview.md) cikket a gyakori virtuális hálózati forgatókönyvek és az általános virtuális hálózati architektúra megismeréséhez.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használatra.

+ Ha erőforrásokat szeretne telepíteni egy virtuális hálózatba vagy alhálózatba, a felhasználói fióknak engedéllyel kell rendelkeznie a következő műveletekhez az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatásban:

    - "Microsoft. Network/virtualNetworks/JOIN/Action" a virtuális hálózati erőforráson.
    - "Microsoft. Network/virtualNetworks/subnet/JOIN/Action" az alhálózati erőforráson.

    A hálózatkezeléssel rendelkező Azure RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepköreit](../role-based-access-control/built-in-roles.md#networking) .

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Ha AK-fürtöt szeretne használni egy virtuális hálózaton, a következő hálózati követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * Kövesse az előfeltételeket a [speciális hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)](../aks/configure-azure-cni.md#prerequisites).
> * Az AK-példánynak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a virtuális hálózatban a munkaterület által használt Azure Storage-fiók (oka) t védi, azoknak ugyanabban a virtuális hálózatban kell lenniük, mint az AK-példány.


A következő lépésekkel adhatja hozzá az AK-t egy virtuális hálózathoz a munkaterületéhez:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/), majd válassza ki az előfizetést és a munkaterületet.

1. Válassza a bal oldali __számítás__ lehetőséget.

1. Válassza a középpontban a kikövetkeztetés __fürtök__ lehetőséget, majd válassza a lehetőséget __+__ .

1. A New következtető __fürt__ párbeszédpanelen válassza a __speciális__ a __hálózati konfiguráció__ alatt lehetőséget.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, hajtsa végre a következő műveleteket:

    1. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.
    1. Az __alhálózat__ legördülő listában válassza ki az alhálózatot.
    1. A __Kubernetes szolgáltatás címtartomány__ mezőjébe írja be a Kubernetes szolgáltatás címtartományt. Ez a címtartomány egy osztály nélküli Inter-Domain Routing (CIDR) jelölésű IP-címtartományt használ a fürt számára elérhető IP-címek definiálásához. Nem lehet átfedésben egyetlen alhálózati IP-tartománnyal sem (például 10.0.0.0/16).
    1. A __KUBERNETES DNS-szolgáltatás IP-címe__ mezőbe írja be a Kubernetes DNS-szolgáltatás IP-címét. Ezt az IP-címet a Kubernetes DNS szolgáltatáshoz rendeli a rendszer. A Kubernetes a szolgáltatási címtartomány (például 10.0.0.10) közé kell esnie.
    1. A __Docker Bridge-címe__ mezőbe írja be a Docker-híd címe mezőt. Ezt az IP-címet a Docker-híd rendeli hozzá. Nem lehet alhálózati IP-címtartományok vagy a Kubernetes szolgáltatási címtartomány (például 172.17.0.1/16).

   ![Azure Machine Learning: Machine Learning Compute virtuális hálózati beállítások](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Ha a modellt webszolgáltatásként helyezi üzembe az AK-ban, a rendszer egy pontozási végpontot hoz létre a következtetési kérelmek kezeléséhez. Győződjön meg arról, hogy a virtuális hálózatot vezérlő NSG-csoport rendelkezik egy bejövő biztonsági szabállyal, amely engedélyezi a pontozási végpont IP-címét, ha a virtuális hálózaton kívülről szeretné hívni.

    A pontozási végpont IP-címének megkereséséhez tekintse meg a központilag telepített szolgáltatás pontozási URI-JÁT. A pontozási URI megtekintésével kapcsolatos információkért lásd: [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md#connection-information).

   > [!IMPORTANT]
   > Tartsa meg a NSG alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](../virtual-network/network-security-groups-overview.md#default-security-rules)alapértelmezett biztonsági szabályai.

   [![Egy bejövő biztonsági szabály](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > A pontozási végpont rendszerképében megjelenő IP-cím eltérő lesz az üzemelő példányok esetében. Míg az összes üzemelő példány ugyanazon az IP-címen osztozik egy AK-fürtön, az egyes AK-fürtök eltérő IP-címmel rendelkeznek.

A Azure Machine Learning SDK-val is hozzáadhatja az Azure Kubernetes szolgáltatást egy virtuális hálózatban. Ha már van egy AK-fürtje egy virtuális hálózaton, csatolja azt a munkaterülethez a következő témakörben leírtak szerint: [üzembe helyezés az AK](how-to-deploy-and-where.md)-ban. A következő kód létrehoz egy új AK-példányt `default` egy nevű virtuális hálózat alhálózatában `mynetwork` :

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

A létrehozási folyamat befejezésekor futtathat következtetéseket vagy modell-pontozást egy virtuális hálózat mögötti AK-fürtön. További információ: [üzembe helyezés az AK](how-to-deploy-and-where.md)-ban.

A Role-Based Access Control Kubernetes való használatával kapcsolatos további információkért lásd: az [Azure RBAC használata a Kubernetes engedélyezéséhez](../aks/manage-azure-rbac.md).

## <a name="network-contributor-role"></a>Hálózati közreműködő szerepkör

> [!IMPORTANT]
> Ha AK-fürtöt hoz létre vagy csatol egy korábban létrehozott virtuális hálózattal, akkor meg kell adnia a szolgáltatásnév (SP) vagy a felügyelt identitást az AK-fürt számára a _hálózati közreműködő_ szerepkört a virtuális hálózatot tartalmazó erőforráscsoporthoz.
>
> Az identitás hálózati közreműködőként való hozzáadásához kövesse az alábbi lépéseket:

1. Az alábbi Azure CLI-parancsokkal keresheti meg az egyszerű szolgáltatásnév vagy a felügyelt identitás AZONOSÍTÓját. Cserélje le a `<aks-cluster-name>` nevet a fürt nevére. A helyére írja be az `<resource-group-name>` _AK-fürtöt tartalmazó_ erőforráscsoport nevét:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Ha a parancs egy értéket ad vissza `msi` , használja a következő parancsot a felügyelt identitás résztvevő-azonosítójának azonosításához:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. A virtuális hálózatot tartalmazó erőforráscsoport AZONOSÍTÓjának megkereséséhez használja a következő parancsot. Cserélje le a `<resource-group-name>` nevet a _virtuális hálózatot tartalmazó_ erőforráscsoport nevére:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Ha a szolgáltatásnevet vagy a felügyelt identitást hálózati közreműködőként szeretné felvenni, használja a következő parancsot. Cserélje le az értéket az `<SP-or-managed-identity>` egyszerű szolgáltatásnév vagy a felügyelt identitás számára visszaadott azonosítóra. Cserélje le a értéket a `<resource-group-id>` virtuális hálózatot tartalmazó erőforráscsoport által visszaadott azonosítóra:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
A belső terheléselosztó az AK-val való használatáról további információt a [belső Load Balancer használata az Azure Kubernetes szolgáltatással](../aks/internal-lb.md)című témakörben talál.

## <a name="secure-vnet-traffic"></a>Biztonságos VNet-forgalom

Az AK-fürt és a virtuális hálózat közötti forgalom elkülönítésére két módszer áll rendelkezésre:

* __Privát AK-fürt__: Ez a módszer az Azure Private-hivatkozást használja a fürttel való kommunikáció biztonságossá tételéhez üzembe helyezési/felügyeleti műveletekhez.
* __Belső AK-Load Balancer__: Ez a módszer konfigurálja a végpontot a központi telepítések számára, hogy a virtuális hálózaton belüli magánhálózati IP-címet használjanak.

> [!WARNING]
> A belső Load Balancer nem működik a kubenet-t használó AK-fürtökkel. Ha egy belső terheléselosztó és egy privát AK-fürt egyidejű használatát kívánja használni, konfigurálja a privát AK-fürtöt az Azure Container Network Interface (CNI) használatával. További információ: [Az Azure CNI hálózatkezelésének konfigurálása az Azure Kubernetes szolgáltatásban](../aks/configure-azure-cni.md).

### <a name="private-aks-cluster"></a>Privát AK-fürt

Alapértelmezés szerint az AK-fürtök vezérlési síkon vagy API-kiszolgálóval rendelkeznek nyilvános IP-címekkel. Egy privát AK-fürt létrehozásával beállíthatja, hogy az AK privát vezérlési síkot használjon. További információt a [privát Azure Kubernetes Service-fürt létrehozása](../aks/private-clusters.md)című témakörben talál.

Miután létrehozta a privát AK-fürtöt, [csatolja a fürtöt a virtuális hálózathoz](how-to-create-attach-kubernetes.md) Azure Machine Learninghoz való használathoz.

> [!IMPORTANT]
> Mielőtt egy magánhálózati kapcsolattal rendelkező AK-fürtöt Azure Machine Learning-mel használ, meg kell nyitnia egy támogatási eseményt a funkció engedélyezéséhez. További információ: a [kvóták kezelése és növelése](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

### <a name="internal-aks-load-balancer"></a>Belső AK-Load Balancer

Alapértelmezés szerint az AK-ban üzemelő példányok [nyilvános Load balancert](../aks/load-balancer-standard.md)használnak. Ebből a szakaszból megtudhatja, hogyan konfigurálhatja az AK-t belső terheléselosztó használatára. Egy belső (vagy privát) terheléselosztó akkor használatos, ha csak a magánhálózati IP-címek engedélyezettek a rendszerfelületként. A belső terheléselosztó a virtuális hálózaton belüli forgalom elosztására szolgál.

A privát terheléselosztó úgy van beállítva, hogy az AK-t _belső terheléselosztó_ használatára konfigurálja. 

#### <a name="enable-private-load-balancer"></a>Privát Load Balancer engedélyezése

> [!IMPORTANT]
> Az Azure Kubernetes Service-fürt Azure Machine Learning Studióban való létrehozásakor nem engedélyezhető a magánhálózati IP-cím. Létrehozhat egyet belső terheléselosztó használatával, ha a Python SDK-t vagy az Azure CLI-bővítményt használja a gépi tanuláshoz.

Az alábbi példák bemutatják, hogyan __hozhat létre egy új AK-fürtöt privát IP-/belső terheléselosztó__ használatával az SDK és a parancssori felület segítségével:

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
> A CLI-vel csak belső terheléselosztó használatával hozhat létre egy AK-fürtöt. Nincs az ml parancs egy meglévő fürt belső terheléselosztó használatára való frissítéséhez.

További információ: az [ml computetarget Create AK](/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

---

Amikor __meglévő fürtöt csatol__ a munkaterülethez, meg kell várnia, amíg a csatlakoztatási művelet be nem konfigurálja a terheléselosztó-t. A fürtök csatlakoztatásával kapcsolatos információkért lásd: [meglévő AK-fürt csatolása](how-to-create-attach-kubernetes.md).

A meglévő fürt csatolása után frissítheti a fürtöt belső terheléselosztó/magánhálózati IP-cím használatára:

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

## <a name="enable-azure-container-instances-aci"></a>Azure Container Instances engedélyezése (ACI)

A Azure Container Instances a modell telepítésekor dinamikusan jönnek létre. Annak engedélyezéséhez, hogy a Azure Machine Learning az ACI-t a virtuális hálózaton belül hozza létre, engedélyeznie kell az alhálózati __delegálást__ az üzemelő példány által használt alhálózathoz.

> [!WARNING]
> Ha a virtuális hálózatban Azure Container Instancest használ, a virtuális hálózatnak a következőket kell tennie:
> * Ugyanabban az erőforráscsoporthoz, mint a Azure Machine Learning munkaterülete.
> * Ha a munkaterület __privát végponttal__ rendelkezik, a Azure Container instances használt virtuális hálózatnak meg kell egyeznie a munkaterület privát végpontja által használttal.
>
> A virtuális hálózaton belüli Azure Container Instances használatakor a munkaterület Azure Container Registry (ACR) nem lehet a virtuális hálózaton.

Ha egy virtuális hálózatban szeretné használni az ACI-t a munkaterületére, kövesse az alábbi lépéseket:

1. Ha engedélyezni szeretné az alhálózati delegálást a virtuális hálózaton, használja az [alhálózati delegálás hozzáadása vagy eltávolítása](../virtual-network/manage-subnet-delegation.md) című cikk információit. A delegálást engedélyezheti virtuális hálózat létrehozásakor, vagy hozzáadhatja egy meglévő hálózathoz.

    > [!IMPORTANT]
    > A delegálás engedélyezésekor használja `Microsoft.ContainerInstance/containerGroups` a __meghatalmazott alhálózatot a szolgáltatás__ értékéhez.

2. Telepítse a modellt [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true)használatával, használja a `vnet_name` és a `subnet_name` paramétereket. Állítsa be ezeket a paramétereket a virtuális hálózat nevére és az alhálózatra, ahol engedélyezte a delegálást.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>A virtuális hálózat kimenő kapcsolatainak korlátozása

Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, engedélyeznie kell a Azure Container Registryhoz való hozzáférést. Győződjön meg például arról, hogy a hálózati biztonsági csoportok (NSG) olyan szabályt tartalmaznak, amely lehetővé teszi a __AzureContainerRegistry. RegionName__ szolgáltatáshoz való hozzáférést, ahol a (z) {RegionName} egy Azure-régió neve.

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy négy részből álló virtuális hálózati sorozat harmadik része. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [1. rész: a Virtual Network áttekintése](how-to-network-security-overview.md)
* [2. rész: a munkaterület erőforrásainak védelme](how-to-secure-workspace-vnet.md)
* [3. rész: a képzési környezet biztonságossá tétele](how-to-secure-training-vnet.md)
* [5. rész: a Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)