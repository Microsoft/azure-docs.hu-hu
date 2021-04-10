---
title: Virtuális csomópontok létrehozása az Azure CLI-vel
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használhatja az Azure CLI-t olyan Azure Kubernetes Services-(ak-) fürt létrehozásához, amely virtuális csomópontokat használ a hüvelyek futtatásához.
services: container-service
ms.topic: conceptual
ms.date: 03/16/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 1c673cae41fcbd3d54aa9b4062dd030ace9f0767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577801"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Azure Kubernetes Services (ak) fürt létrehozása és konfigurálása virtuális csomópontok használatára az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozhatja létre és konfigurálhatja a virtuális hálózati erőforrásokat és az AK-fürtöt az Azure CLI használatával, majd engedélyezheti a virtuális csomópontokat.


## <a name="before-you-begin"></a>Előkészületek

A virtuális csomópontok hálózati kommunikációt tesznek lehetővé Azure Container Instances (ACI) és az AK-fürtön futó hüvelyek között. A kommunikáció biztosításához létre kell hozni egy virtuális hálózati alhálózatot, és hozzá kell rendelni a delegált engedélyeket. A virtuális csomópontok csak a *speciális* hálózatkezelés (Azure CNI) használatával létrehozott AK-fürtökkel működnek. Alapértelmezés szerint az AK-fürtök *alapszintű* hálózatkezeléssel (kubenet) jönnek létre. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális hálózatot és alhálózatokat, majd helyezzen üzembe egy speciális hálózatkezelést használó AK-fürtöt.

> [!IMPORTANT]
> Mielőtt virtuális csomópontokat használ az AK-val, tekintse át az [AK virtuális csomópontok korlátozásait][virtual-nodes-aks] és az [ACI virtuális hálózati korlátozásait][virtual-nodes-networking-aci]. Ezek a korlátozások az AK-fürt és a virtuális csomópontok helyét, hálózati konfigurációját és egyéb konfigurációs adatait érintik.

Ha korábban még nem használta az ACI-t, regisztrálja a szolgáltatót az előfizetésében. Az ACI-szolgáltató regisztrációjának állapotát az az [Provider List][az-provider-list] parancs használatával tekintheti meg, ahogy az az alábbi példában is látható:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft. ContainerInstance* szolgáltatónak *regisztráltként* kell jelentenie, ahogy az alábbi példában is látható:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Ha a szolgáltató *NotRegistered*-ként jelenik meg, regisztrálja a szolgáltatót az az [Provider Register][az-provider-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **kipróbálás** elemet a kód jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verziójára van szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az az [Network vnet Create][az-network-vnet-create] paranccsal. A következő példában létrehozunk egy *myVnet* nevű virtuális hálózatot a *10.0.0.0/8*, valamint egy *myAKSSubnet* nevű alhálózatot. Ennek az alhálózatnak az alapértelmezett *10.240.0.0/16* előtagja:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Most hozzon létre egy további alhálózatot a virtuális csomópontok számára az az [Network vnet subnet Create][az-network-vnet-subnet-create] paranccsal. A következő példa létrehoz egy *myVirtualNodeSubnet* nevű alhálózatot a *10.241.0.0/16* előtaggal.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Egyszerű szolgáltatásnév létrehozása vagy felügyelt identitás használata

Annak engedélyezéséhez, hogy egy AK-fürt más Azure-erőforrásokkal is működjön, a rendszer a fürt identitását használja. Ez a fürtkonfiguráció automatikusan létrehozható az Azure CLI vagy a portál használatával, vagy előre létrehozhat egyet, és további engedélyeket is hozzárendelhet. Alapértelmezés szerint a fürt identitása felügyelt identitás. További információ: [felügyelt identitások használata](use-managed-identity.md). A fürt identitását is használhatja egyszerű szolgáltatásnévként. A következő lépések bemutatják, hogyan lehet manuálisan létrehozni és hozzárendelni az egyszerű szolgáltatást a fürthöz.

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

## <a name="assign-permissions-to-the-virtual-network"></a>Engedélyek kiosztása a virtuális hálózathoz

Annak engedélyezéséhez, hogy a fürt használhassa és felügyelje a virtuális hálózatot, meg kell adnia a megfelelő jogosultságokat a hálózati erőforrások használatához.

Először szerezze be a virtuális hálózati erőforrás AZONOSÍTÓját az [az Network vnet show][az-network-vnet-show]paranccsal:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Ahhoz, hogy a virtuális hálózat használatára megfelelő hozzáférést biztosítson az AK-fürt számára, hozzon létre egy szerepkör-hozzárendelést az az [role hozzárendelés Create][az-role-assignment-create] paranccsal. Cserélje le az `<appId`> és `<vnetId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Egy AK-fürtöt az előző lépésben létrehozott AK alhálózatba helyez üzembe. Az alhálózat AZONOSÍTÓjának lekérése az [az Network vnet subnet show][az-network-vnet-subnet-show]paranccsal:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Cserélje le az `<subnetId>` értéket az előző lépésben beszerzett azonosítóra, majd az `<appId>` `<password>` előző szakaszban összegyűjtött értékekre.

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

## <a name="enable-virtual-nodes-addon"></a>A virtuális csomópontok addon engedélyezése

A virtuális csomópontok engedélyezéséhez használja az az [AK Enable-addons][az-aks-enable-addons] parancsot. A következő példa a *myVirtualNodeSubnet* nevű alhálózatot használja az előző lépésben:

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

A következő példa kimenete az egyetlen virtuálisgép-csomópontot mutatja, majd a Linux, *Virtual-Node-ACI-Linux* virtuális csomópontot:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Minta alkalmazás üzembe helyezése

Hozzon létre egy nevű fájlt `virtual-node.yaml` , és másolja a következő YAML. A tárolónak a csomóponton való megadásához meg kell adni a [nodeSelector][node-selector] és a [tolerancia][toleration] értéket.

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

Futtassa az alkalmazást az [kubectl Apply][kubectl-apply] paranccsal.

```console
kubectl apply -f virtual-node.yaml
```

A [kubectl Get hüvely][kubectl-get] parancs használatával adja `-o wide` meg az argumentumot a hüvelyek és az ütemezett csomópontok listájának kimenetéhez. Figyelje meg, hogy a `aci-helloworld` Pod ütemezve van a `virtual-node-aci-linux` csomóponton.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod a virtuális csomópontokkal való használatra delegált Azure virtuális hálózati alhálózatból származó belső IP-címet kap.

> [!NOTE]
> Ha Azure Container Registryban tárolt rendszerképeket használ, [konfigurálja és használja a Kubernetes titkos kulcsát][acr-aks-secrets]. A virtuális csomópontok jelenlegi korlátozása az, hogy nem használhatja az integrált Azure AD szolgáltatás egyszerű hitelesítését. Ha nem használ titkos kódot, a virtuális csomópontokon ütemezett hüvelyek nem indulnak el, és nem jelentik a hibát `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont-Pod tesztelése

A virtuális csomóponton futó Pod teszteléséhez keresse meg a bemutató alkalmazást egy webes ügyféllel. Mivel a pod belső IP-cím van hozzárendelve, gyorsan tesztelheti ezt a kapcsolatot egy másik Pod-on az AK-fürtön. Hozzon létre egy teszt Pod-t, és csatoljon hozzá egy terminál-munkamenetet:

```console
kubectl run -it --rm testvk --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Telepítés `curl` a pod használatával `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Most nyissa meg a pod `curl` -t a használatával, például: *http://10.241.0.4* . Az előző parancsban megjelenő saját belső IP-cím megadása `kubectl get pods` :

```console
curl -L http://10.241.0.4
```

Megjelenik a bemutató alkalmazás, ahogy az a következő tömörített példában látható:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zárjuk be a terminál-munkamenetet a test Pod-be a használatával `exit` . Ha a munkamenet véget ér, a rendszer törli a pod-t.

## <a name="remove-virtual-nodes"></a>Virtuális csomópontok eltávolítása

Ha már nem szeretné használni a virtuális csomópontokat, letilthatja azokat az az [AK disable-addons][az aks disable-addons] parancs használatával. 

Ha szükséges, [https://shell.azure.com](https://shell.azure.com) nyissa meg a Azure Cloud shellt a böngészőben.

Először törölje a `aci-helloworld` virtuális csomóponton futó Pod-t:

```console
kubectl delete -f virtual-node.yaml
```

A következő példa parancs letiltja a linuxos virtuális csomópontokat:

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

Ebben a cikkben egy Pod-t ütemeztek a virtuális csomóponton, és egy privát, belső IP-címet rendeltek hozzá. Ehelyett hozzon létre egy szolgáltatás központi telepítését, és irányítsa a forgalmat a pod-ra egy terheléselosztó vagy egy bejövő vezérlő használatával. További információkért lásd: [alapszintű bejövő vezérlő létrehozása az AK-ban][aks-basic-ingress].

A virtuális csomópontok gyakran az AK-ban lévő skálázási megoldás egyik összetevője. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes vízszintes Pod automéretező használata][aks-hpa]
- [A Kubernetes-fürt automéretező használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok méretezési mintáját][virtual-node-autoscale]
- [További információ a virtuális Kubelet nyílt forráskódú könyvtáráról][virtual-kubelet-repo]

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
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[virtual-nodes-aks]: virtual-nodes.md
[virtual-nodes-networking-aci]: ../container-instances/container-instances-virtual-network-concepts.md