---
title: Virtuális csomópontok létrehozása a portál használatával az Azure Kubernetes Servicesben (AKS)
description: Megtudhatja, hogyan használhatja a Azure Portal egy olyan Azure Kubernetes Services- (AKS-) fürt létrehozásához, amely virtuális csomópontokat használ podok futtatásához.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: fad021dc92753013234a3b0831e76e87fa25db10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769300"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure Kubernetes Services- (AKS-) fürt létrehozása és konfigurálása az Azure Portal

Ez a cikk bemutatja, hogyan használhatja a Azure Portal virtuális hálózati erőforrások és egy engedélyezett virtuális csomópontokkal rendelkező AKS-fürt létrehozására és konfigurálára.

> [!NOTE]
> [Ez a](virtual-nodes.md) cikk áttekintést nyújt a virtuális csomópontok használatával elérhető régiókról és korlátozásokról.

## <a name="before-you-begin"></a>Előkészületek

A virtuális csomópontok lehetővé teszik a hálózati kommunikációt a Azure Container Instances (ACI) és az AKS-fürtön futó podok között. A kommunikáció érdekében létrejön egy virtuális hálózat alhálózata, és hozzárendeli a delegált engedélyeket. A virtuális csomópontok csak a speciális  hálózat (Azure CNI) használatával létrehozott AKS-fürtökön működnek. Alapértelmezés szerint az AKS-fürtök *alapszintű* hálózattal (kubenet) vannak létrehozva. Ez a cikk bemutatja, hogyan hozhat létre virtuális hálózatot és alhálózatokat, és hogyan helyezhet üzembe speciális hálózatokat használó AKS-fürtöt.

Ha korábban még nem használta az ACI-t, regisztrálja a szolgáltatót az előfizetésében. Az ACI-szolgáltató regisztrációjának állapotát az [az provider list paranccsal][az-provider-list] ellenőrizheti az alábbi példában látható módon:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft.ContainerInstance szolgáltatónak* Regisztráltként kell jelentenie, ahogyan az alábbi példakimenetben látható: 

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Ha a szolgáltató *NotRegistered (Regisztrált)* ként jelenik meg, regisztrálja a szolgáltatót [az az provider register használatával][az-provider-register] az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az alkalmazás bal felső sarkában válassza Azure Portal **Erőforrás** létrehozása  >  **Kubernetes Service lehetőséget.**

Az **Alapvető beállítások** lapon konfigurálja az alábbiakat:

- *PROJEKT ADATAI*: Válasszon ki egy Azure-előfizetést, majd válasszon ki vagy hozzon létre egy Azure-erőforráscsoportot, például: *myResourceGroup*. Adja meg a **Kubernetes-fürt nevét**, például *myAKSCluster*.
- *FÜRT ADATAI:* Válasszon ki egy régiót és Egy Kubernetes-verziót az AKS-fürthöz.
- *ELSŐDLEGES CSOMÓPONTKÉSZLET:* Válassza ki az AKS-csomópontok virtuálisgép-méretét. A virtuálisgép-méret az AKS-fürt telepítését követően **nem** módosítható.
     - Adja meg a fürtre telepítendő csomópontok számát. Ebben a cikkben állítsa a **Csomópontok száma beállítását** *1-re.* A csomópontok száma a fürt telepítése után is **módosítható**.

Kattintson **a Tovább: Csomópontkészletek elemre.**

A **Csomópontkészletek lapon** válassza a *Virtuális csomópontok engedélyezése lehetőséget.*

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="Egy böngészőben a fürt létrehozását mutatja be, amely a fürtön engedélyezett virtuális csomópontokkal Azure Portal. Az &quot;Enable virtual nodes&quot; (Virtuális csomópontok engedélyezése) lehetőség ki van emelve.":::

Alapértelmezés szerint létrejön egy fürtidentitás. Ez a fürtidentitás a fürtök közötti kommunikációra és más Azure-szolgáltatásokkal való integrációra szolgál. Alapértelmezés szerint ez a fürtidentitás egy felügyelt identitás. További információ: [Felügyelt identitások használata.](use-managed-identity.md) Fürtidentitásként szolgáltatásnév is használható.

A fürt speciális hálózatra is konfigurálva van. A virtuális csomópontok úgy vannak konfigurálva, hogy a saját Azure-beli virtuális hálózatuk alhálózatát használják. Ez az alhálózat delegált engedélyekkel rendelkezik az Azure-erőforrások az AKS-fürt közötti csatlakoztatáshoz. Ha még nem rendelkezik delegált alhálózattal, a Azure Portal létrehozza és konfigurálja az Azure-beli virtuális hálózatot és alhálózatot a virtuális csomópontokkal való használatra.

Válassza az **Áttekintés + létrehozás** lehetőséget. Az érvényesítés befejezése után válassza a **Létrehozás lehetőséget.**

Az AKS-fürt létrehozása és a használatra való előkészítése néhány percet vesz igénybe.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl] eszközt, a Kubernetes parancssori ügyfelét. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

A kód Cloud Shell a kódblokk jobb felső sarkában található Try **it** (Próbálja ki) lehetőséget. A böngészőlapot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálhatja a `kubectl` ügyfelet a Kubernetes-fürthöz való csatlakozásra. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

Az alábbi példakimeneten látható az egyetlen létrehozott virtuálisgép-csomópont, majd a Linux virtuális csomópontja, a *virtual-node-aci-linux:*

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Mintaalkalmazás üzembe helyezése

A Azure Cloud Shell hozzon létre egy nevű fájlt, és másolja be a `virtual-node.yaml` következő YAML-fájlt. A csomóponton a tároló ütemezése egy [nodeSelector][node-selector] és [egy leratómező][toleration] definiálása. Ezekkel a beállításokkal ütemezheti a podot a virtuális csomóponton, és megerősítheti, hogy a szolgáltatás engedélyezve van.

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

Futtassa az alkalmazást a [kubectl apply paranccsal.][kubectl-apply]

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Használja a [kubectl get pods][kubectl-get] parancsot a argumentummal a podok és az ütemezett csomópontok `-o wide` listájának kimeneteként. Figyelje meg, `virtual-node-helloworld` hogy a pod ütemezve van a `virtual-node-linux` csomóponton.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A podhoz egy belső IP-cím van hozzárendelve az Azure-beli virtuális hálózat alhálózatán, amely delegálva van a virtuális csomópontokkal való használatra.

> [!NOTE]
> Ha a tárolóban tárolt rendszerképeket Azure Container Registry, konfigurálja és használja [a Kubernetes titkos adatokat.][acr-aks-secrets] A virtuális csomópontok aktuális korlátja, hogy nem használhat integrált Azure AD-szolgáltatásnév-hitelesítést. Ha nem használ titkos kódokat, a virtuális csomópontokon ütemezett podok nem indulnak el, és hibát `HTTP response status code 400 error code "InaccessibleImage"` jeleznek.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont podjának tesztelése

A virtuális csomóponton futó pod teszteléshez keresse meg a bemutatóalkalmazást egy webes ügyféllel. Mivel a podhoz belső IP-cím van hozzárendelve, ezt a kapcsolatot gyorsan tesztelheti egy másik podról az AKS-fürtön. Hozzon létre egy tesztpodot, és csatoljon hozzá egy terminál-munkamenetet:

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Telepítse `curl` a podot a `apt-get` használatával:

```console
apt-get update && apt-get install -y curl
```

Most az használatával férhet hozzá a pod `curl` címhez, például: *http://10.241.0.4* . Adja meg az előző parancsban látható saját belső `kubectl get pods` IP-címét:

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

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy podot ütemezünk a virtuális csomóponton, és hozzárendelünk egy privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatástelepítést, és egy terheléselosztási vagy bejövőforgalom-vezérlőn keresztül irányíthat forgalmat a podra. További információ: Alapszintű bejövő forgalomvezérlő létrehozása az [AKS-ban.][aks-basic-ingress]

A virtuális csomópontok az AKS méretezési megoldásának egyik összetevője. A megoldások méretezésével kapcsolatos további információkért tekintse meg a következő cikkeket:

- [A Kubernetes automatikus horizontális podméretozó használata][aks-hpa]
- [A Kubernetes-fürt automatikus skálázó használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok automatikus skálázási mintáját][virtual-node-autoscale]
- [További információ a Virtual Kubelet nyílt forráskódú kódtárról][virtual-kubelet-repo]

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
