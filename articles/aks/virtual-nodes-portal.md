---
title: Virtuális csomópontok létrehozása a portál használatával az Azure Kubernetes Servicesben (ak)
description: Megtudhatja, hogyan használhatja a Azure Portalt olyan Azure Kubernetes Services-(ak-) fürt létrehozásához, amely virtuális csomópontokat használ a hüvelyek futtatásához.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: c1ecaa88dd5329d86818565983a6ba891a6d8424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577825"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure Kubernetes Services (ak) fürt létrehozása és konfigurálása virtuális csomópontok használatára a Azure Portalban

Ez a cikk bemutatja, hogyan hozhatja létre és konfigurálhatja a virtuális hálózati erőforrásokat és egy AK-fürtöt a virtuális csomópontok engedélyezésével a Azure Portal használatával.

> [!NOTE]
> [Ez a cikk](virtual-nodes.md) áttekintést nyújt a régió rendelkezésre állásáról és a virtuális csomópontok használatával kapcsolatos korlátozásokról.

## <a name="before-you-begin"></a>Előkészületek

A virtuális csomópontok hálózati kommunikációt tesznek lehetővé Azure Container Instances (ACI) és az AK-fürtön futó hüvelyek között. A kommunikáció biztosításához létre kell hozni egy virtuális hálózati alhálózatot, és hozzá kell rendelni a delegált engedélyeket. A virtuális csomópontok csak a *speciális* hálózatkezelés (Azure CNI) használatával létrehozott AK-fürtökkel működnek. Alapértelmezés szerint az AK-fürtök *alapszintű* hálózatkezeléssel (kubenet) jönnek létre. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális hálózatot és alhálózatokat, majd helyezzen üzembe egy speciális hálózatkezelést használó AK-fürtöt.

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

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

A Azure Portal bal felső sarkában válassza az **erőforrás létrehozása**  >  **Kubernetes-szolgáltatás** elemet.

Az **Alapvető beállítások** lapon konfigurálja az alábbiakat:

- *PROJEKT ADATAI*: Válasszon ki egy Azure-előfizetést, majd válasszon ki vagy hozzon létre egy Azure-erőforráscsoportot, például: *myResourceGroup*. Adja meg a **Kubernetes-fürt nevét**, például *myAKSCluster*.
- *Fürt adatai*: válasszon régiót és Kubernetes-verziót az AK-fürthöz.
- *Elsődleges csomóponti készlet*: válasszon ki egy virtuálisgép-méretet az AK-csomópontok számára. A virtuálisgép-méret az AKS-fürt telepítését követően **nem** módosítható.
     - Adja meg a fürtre telepítendő csomópontok számát. Ehhez a cikkhez állítsa a **csomópontok száma** *1* értékre. A csomópontok száma a fürt telepítése után is **módosítható**.

Kattintson a **Tovább: csomópont-készletek** elemre.

A **csomópont-készletek** lapon válassza a *virtuális csomópontok engedélyezése* lehetőséget.

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="A böngészőben egy olyan fürt létrehozása látható, amelyen engedélyezve vannak a virtuális csomópontok a Azure Portal. A &quot;virtuális csomópontok engedélyezése&quot; beállítás ki van emelve.":::

Alapértelmezés szerint a rendszer létrehozza a fürt identitását. Ez a fürt-identitás a fürtök közötti kommunikációhoz és más Azure-szolgáltatásokkal való integrációhoz használatos. Alapértelmezés szerint a fürt identitása felügyelt identitás. További információ: [felügyelt identitások használata](use-managed-identity.md). A fürt identitását is használhatja egyszerű szolgáltatásnévként.

A fürt speciális hálózatkezelésre is konfigurálva van. A virtuális csomópontok saját Azure-beli virtuális hálózati alhálózatának használatára vannak konfigurálva. Ez az alhálózat delegált engedélyekkel rendelkezik az Azure-erőforrások az AK-fürthöz való összekapcsolásához. Ha még nem rendelkezik delegált alhálózattal, a Azure Portal létrehozza és konfigurálja az Azure-beli virtuális hálózatot és az alhálózatot a virtuális csomópontokkal való használatra.

Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejezése után válassza a **Létrehozás** lehetőséget.

Az AKS-fürt létrehozása és a használatra való előkészítése néhány percet vesz igénybe.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl] eszközt, a Kubernetes parancssori ügyfelét. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

A Cloud Shell megnyitásához válassza a **kipróbálás** elemet a kód jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálhatja a `kubectl` ügyfelet a Kubernetes-fürthöz való csatlakozásra. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimenete az egyetlen virtuálisgép-csomópontot mutatja, majd a Linux, *Virtual-Node-ACI-Linux* virtuális csomópontot:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Minta alkalmazás üzembe helyezése

A Azure Cloud Shell hozzon létre egy nevű fájlt, `virtual-node.yaml` és másolja a következő YAML. A tárolónak a csomóponton való megadásához meg kell adni a [nodeSelector][node-selector] és a [tolerancia][toleration] értéket. Ezek a beállítások lehetővé teszik, hogy a pod legyen ütemezve a virtuális csomóponton, és ellenőrizze, hogy a szolgáltatás sikeresen engedélyezve van-e.

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
```

Futtassa az alkalmazást az [kubectl Apply][kubectl-apply] paranccsal.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

A [kubectl Get hüvely][kubectl-get] parancs használatával adja `-o wide` meg az argumentumot a hüvelyek és az ütemezett csomópontok listájának kimenetéhez. Figyelje meg, hogy a `virtual-node-helloworld` Pod ütemezve van a `virtual-node-linux` csomóponton.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod a virtuális csomópontokkal való használatra delegált Azure virtuális hálózati alhálózatból származó belső IP-címet kap.

> [!NOTE]
> Ha Azure Container Registryban tárolt rendszerképeket használ, [konfigurálja és használja a Kubernetes titkos kulcsát][acr-aks-secrets]. A virtuális csomópontok jelenlegi korlátozása az, hogy nem használhatja az integrált Azure AD szolgáltatás egyszerű hitelesítését. Ha nem használ titkos kódot, a virtuális csomópontokon ütemezett hüvelyek nem indulnak el, és nem jelentik a hibát `HTTP response status code 400 error code "InaccessibleImage"` .

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont-Pod tesztelése

A virtuális csomóponton futó Pod teszteléséhez keresse meg a bemutató alkalmazást egy webes ügyféllel. Mivel a pod belső IP-cím van hozzárendelve, gyorsan tesztelheti ezt a kapcsolatot egy másik Pod-on az AK-fürtön. Hozzon létre egy teszt Pod-t, és csatoljon hozzá egy terminál-munkamenetet:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
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

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Pod-t ütemeztek a virtuális csomóponton, és egy privát, belső IP-címet rendeltek hozzá. Ehelyett hozzon létre egy szolgáltatás központi telepítését, és irányítsa a forgalmat a pod-ra egy terheléselosztó vagy egy bejövő vezérlő használatával. További információkért lásd: [alapszintű bejövő vezérlő létrehozása az AK-ban][aks-basic-ingress].

A virtuális csomópontok egy méretezési megoldás egyik összetevője az AK-ban. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes vízszintes Pod automéretező használata][aks-hpa]
- [A Kubernetes-fürt automéretező használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok méretezési mintáját][virtual-node-autoscale]
- [További információ a virtuális Kubelet nyílt forráskódú könyvtáráról][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
