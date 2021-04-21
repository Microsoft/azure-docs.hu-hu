---
title: Virtuális csomópontok létrehozása az Azure CLI használatával
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre az Azure CLI használatával olyan Azure Kubernetes Services- (AKS-) fürtöt, amely virtuális csomópontokat használ podok futtatásához.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 86f1d8923eea961471883c44168c4fa848ac12d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769282"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Azure Kubernetes Services- (AKS-) fürt létrehozása és konfigurálása virtuális csomópontok használatára az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozhatja létre és konfigurálhatja a virtuális hálózati erőforrásokat és az AKS-fürtöt az Azure CLI használatával, majd hogyan engedélyezheti a virtuális csomópontokat.


## <a name="before-you-begin"></a>Előkészületek

A virtuális csomópontok lehetővé teszik a hálózati kommunikációt a Azure Container Instances (ACI) és az AKS-fürtben futó podok között. A kommunikáció érdekében létrejön egy virtuális hálózat alhálózata, és hozzárendeli a delegált engedélyeket. A virtuális csomópontok csak a speciális  hálózat használatával létrehozott AKS-fürtökön működnek (Azure CNI). Alapértelmezés szerint az AKS-fürtök *alapszintű* hálózattal (kubenet) vannak létrehozva. Ez a cikk bemutatja, hogyan hozhat létre virtuális hálózatot és alhálózatokat, és hogyan helyezhet üzembe speciális hálózatokat használó AKS-fürtöt.

> [!IMPORTANT]
> Mielőtt virtuális csomópontokat használ az AKS-sel, tekintse át az [AKS][virtual-nodes-aks] virtuális csomópontokra és az ACI virtuális hálózatra vonatkozó [korlátozását.][virtual-nodes-networking-aci] Ezek a korlátozások az AKS-fürt és a virtuális csomópontok helyét, hálózati konfigurációját és egyéb konfigurációs adatait is érintik.

Ha korábban még nem használta az ACI-t, regisztrálja a szolgáltatót az előfizetésében. Az ACI-szolgáltató regisztrációjának állapotát az [az provider list][az-provider-list] paranccsal ellenőrizheti az alábbi példában látható módon:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft.ContainerInstance szolgáltatónak* Regisztráltként kell jelentenie, ahogyan az alábbi példakimenetben látható: 

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Ha a szolgáltató *NotRegistered*(Regisztrált) ként jelenik meg, regisztrálja a szolgáltatót [az az provider register használatával][az-provider-register] az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A kód Cloud Shell a kódblokk jobb felső sarkában található Try **it** (Próbálja ki) lehetőséget. A böngészőlapot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, ehhez a cikkhez az Azure CLI 2.0.49-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot [az az network vnet create paranccsal.][az-network-vnet-create] Az alábbi példa egy *myVnet* nevű virtuális hálózatot hoz létre *a 10.0.0.0/8* címelőtaggal és egy *myAKSSubnet* nevű alhálózattal. Az alhálózat címelőtagja alapértelmezés szerint *a 10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Most hozzon létre egy további alhálózatot a virtuális csomópontokhoz [az az network vnet subnet create paranccsal.][az-network-vnet-subnet-create] A következő példa létrehoz egy *myVirtualNodeSubnet* nevű alhálózatot *a 10.241.0.0/16 címelőtaggal.*

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Szolgáltatásnév létrehozása vagy felügyelt identitás használata

Ahhoz, hogy egy AKS-fürt kommunikálhat más Azure-erőforrásokkal, fürtidentitást használ. Ezt a fürtidentitást automatikusan létrehozhatja az Azure CLI vagy a portál, vagy létrehozhat egyet előre, és további engedélyeket rendelhet hozzá. Alapértelmezés szerint ez a fürtidentitás egy felügyelt identitás. További információ: [Felügyelt identitások használata.](use-managed-identity.md) Fürtidentitásként szolgáltatásnév is használható. A következő lépések azt mutatják be, hogyan hozhatja létre és rendelheti hozzá a szolgáltatásnév manuálisan a fürthöz.

Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="assign-permissions-to-the-virtual-network"></a>Engedélyek hozzárendelése a virtuális hálózathoz

Ha engedélyeznie kell a fürt számára a virtuális hálózat használatát és kezelését, meg kell adnunk az AKS-szolgáltatásnévnek a megfelelő jogosultságokat a hálózati erőforrások használatára.

Először szerezze be a virtuális hálózat erőforrás-azonosítóját [az az network vnet show használatával:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Ahhoz, hogy megfelelő hozzáférést adjon az AKS-fürtnek a virtuális hálózat használatára, hozzon létre egy szerepkör-hozzárendelést [az az role assignment create paranccsal.][az-role-assignment-create] Cserélje le az `<appId`> és `<vnetId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Üzembe helyez egy AKS-fürtöt az előző lépésben létrehozott AKS-alhálózaton. Az az network vnet subnet show használatával szerezze be [az alhálózat azonosítóját:][az-network-vnet-subnet-show]

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Cserélje le a helyére az előző lépésben lekért azonosítót, majd a helyére az előző szakaszban `<subnetId>` `<appId>` összegyűjtött `<password>` értékeket.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Néhány perc múlva befejeződik a parancs végrehajtása, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban.

## <a name="enable-virtual-nodes-addon"></a>Virtuális csomópontok bővítményének engedélyezése

A virtuális csomópontok engedélyezéséhez használja az [az aks enable-addons parancsot.][az-aks-enable-addons] Az alábbi példa az előző lépésben létrehozott *myVirtualNodeSubnet* nevű alhálózatot használja:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ebben a lépésben a rendszer hitelesítő adatokat tölt le, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

Az alábbi példakimeneten látható az egyetlen létrehozott virtuálisgép-csomópont, majd a Linux virtuális csomópontja, a *virtual-node-aci-linux:*

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Mintaalkalmazás üzembe helyezése

Hozzon létre egy nevű `virtual-node.yaml` fájlt, és másolja be a következő YAML-fájlt. A csomóponton a tároló ütemezése egy [nodeSelector][node-selector] és [egy leratómező][toleration] definiálása.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Futtassa az alkalmazást a [kubectl apply paranccsal.][kubectl-apply]

```console
kubectl apply -f virtual-node.yaml
```

Használja a [kubectl get pods][kubectl-get] parancsot a argumentummal a podok listájának és az ütemezett `-o wide` csomópont kimenetének kimeneteként. Figyelje meg, `aci-helloworld` hogy a pod ütemezve van a `virtual-node-aci-linux` csomóponton.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A podhoz egy belső IP-cím van hozzárendelve az Azure-beli virtuális hálózat alhálózatán, amely a virtuális csomópontokkal való használatra van delegálva.

> [!NOTE]
> Ha a tárolóban tárolt rendszerképeket Azure Container Registry, konfigurálja [és használja a Kubernetes titkos adatokat.][acr-aks-secrets] A virtuális csomópontok aktuális korlátja, hogy nem használhat integrált Azure AD-szolgáltatásnév-hitelesítést. Ha nem használ titkos kódokat, a virtuális csomópontokon ütemezett podok nem indulnak el, és hibát `HTTP response status code 400 error code "InaccessibleImage"` jeleznek.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont podjának tesztelése

A virtuális csomóponton futó pod tesztelésére keresse meg a bemutató alkalmazást egy webes ügyféllel. Mivel a podhoz belső IP-cím van hozzárendelve, ezt a kapcsolatot gyorsan tesztelheti az AKS-fürt egy másik podján. Hozzon létre egy tesztpodot, és csatoljon hozzá egy terminál-munkamenetet:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Telepítse `curl` a podot a `apt-get` használatával:

```console
apt-get update && apt-get install -y curl
```

Most a használatával férhet hozzá a pod `curl` címhez, *http://10.241.0.4* például: . Adja meg az előző parancsban látható saját belső `kubectl get pods` IP-címét:

```console
curl -L http://10.241.0.4
```

Megjelenik a bemutató alkalmazás, ahogyan az a következő rövid példakimenetben is látható:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zárja be a tesztpod terminál-munkamenetét a `exit` következővel: . A munkamenet befejezésekor a pod lesz törölve.

## <a name="remove-virtual-nodes"></a>Virtuális csomópontok eltávolítása

Ha már nem szeretne virtuális csomópontokat használni, letilthatja őket [az az aks disable-addons paranccsal.][az aks disable-addons] 

Ha szükséges, nyissa meg a [https://shell.azure.com](https://shell.azure.com) Azure Cloud Shell a böngészőben.

Először törölje a `aci-helloworld` virtuális csomóponton futó podot:

```console
kubectl delete -f virtual-node.yaml
```

Az alábbi példaparancs letiltja a Linux rendszerű virtuális csomópontokat:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Most távolítsa el a virtuális hálózati erőforrásokat és az erőforráscsoportot:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy podot ütemezünk a virtuális csomóponton, és hozzárendelünk egy privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatástelepítést, és egy terheléselosztási vagy bejövőforgalom-vezérlőn keresztül irányíthat forgalmat a podra. További információ: Alapszintű bejövő forgalomvezérlő létrehozása az [AKS-ban.][aks-basic-ingress]

A virtuális csomópontok gyakran az AKS méretezési megoldásának egyik összetevője. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes automatikus horizontális podméretozó használata][aks-hpa]
- [A Kubernetes-fürt automatikus skálázó használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok automatikus skálázási mintáját][virtual-node-autoscale]
- [További információ a Virtual Kubelet nyílt forráskódú kódtárról][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks disable-addons]: /cli/azure/aks#az_aks_disable_addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md