---
title: Integráció Azure Container Registry és Azure Kubernetes Service
description: Útmutató az Azure Kubernetes Service (AKS) és Azure Container Registry (ACR) integrálásához
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: ab8065a14aac9e798bfe7d632aa5b33c44706190
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775827"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Container Registryvel történő hitelesítés az Azure Kubernetes Service-ből (AKS)

Ha ACR-t Azure Container Registry (ACR) Azure Kubernetes Service (AKS) használ, hitelesítési mechanizmust kell létrehozni. Ez a művelet a CLI és a portál felhasználói felületének részeként valósul meg, és a szükséges engedélyeket adja meg az ACR-nek. Ez a cikk példákat mutat be a két Azure-szolgáltatás közötti hitelesítés konfigurálásához. 

Az AKS-ről ACR-integrációra néhány egyszerű paranccsal is beállíthatja az Azure CLI-t. Ez az integráció hozzárendeli az AcrPull szerepkört az AKS-fürthöz társított felügyelt identitáshoz.

> [!NOTE]
> Ez a cikk az AKS és az ACR közötti automatikus hitelesítést tartalmazza. Ha egy rendszerképet kell lekért egy privát külső regisztrációs adatbázisból, használjon egy [rendszerkép lekért titkos kulcsát.][Image Pull Secret]

## <a name="before-you-begin"></a>Előkészületek

Ezekhez a példákhoz a következőre van szükség:

* **Tulajdonosi** **vagy Azure-fiók rendszergazdai** szerepköre az **Azure-előfizetésben**
* Az Azure CLI 2.7.0-s vagy újabb verziója

Annak érdekében, hogy ne legyen **szükség tulajdonosi** vagy Azure-fiók-rendszergazdai szerepkörre, manuálisan konfigurálhat egy felügyelt identitást, vagy egy meglévő felügyelt identitással hitelesítheti az ACR-t az AKS-ről.  További információ: Azure-beli felügyelt identitás használata azure-beli tároló-beállításjegyzékben [való hitelesítéshez.](../container-registry/container-registry-authentication-managed-identity.md)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Új AKS-fürt létrehozása ACR-integrációval

Az AKS- és ACR-integrációt az AKS-fürt kezdeti létrehozása során állíthatja be.  Ahhoz, hogy egy AKS-fürt kommunikálhat az ACR-rel, Azure Active Directory **felügyelt identitást** használ. A következő CLI-parancs lehetővé teszi egy meglévő ACR hitelesítését az előfizetésben, és konfigurálja a felügyelt identitáshoz megfelelő **ACRPull-szerepkört.** Az alábbi paraméterekhez érvényes értékeket kell megszabadni.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Másik lehetőségként megadhatja az ACR nevét egy ACR-erőforrás-azonosítóval, amelynek formátuma a következő:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Ha olyan ACR-t használ, amely az AKS-fürttől eltérő előfizetésben található, használja az ACR-erőforrás azonosítóját az AKS-fürt csatolásához vagy leválasztásakor.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ez a lépés több percig is eltarthat.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integráció konfigurálása meglévő AKS-fürtökhöz

Meglévő ACR integrálása meglévő AKS-fürtökbe az **acr-name** vagy az **acr-resource-id** érvényes értékeinek meg kell megszabadulása az alábbiak szerint.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

Vagy

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Az ACR- és AKS-fürtök közötti integrációt a következővel is eltávolíthatja:

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

vagy

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Az ACR & AKS-sel

### <a name="import-an-image-into-your-acr"></a>Rendszerkép importálása az ACR-be

Importálhat egy rendszerképet a Docker Hubból az ACR-be a következő futtatásával:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>A minta rendszerkép üzembe helyezése az ACR-ről az AKS-be

Győződjön meg arról, hogy a megfelelő AKS-adatokat használja

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Hozzon létre egy **acr-nginx.yaml nevű fájlt,** amely a következőket tartalmazza. Helyettesítse be a regisztrációs adatbázis erőforrásnevét **az acr-name névvel.** Például: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Ezután futtassa ezt az üzembe helyezést az AKS-fürtben:

```console
kubectl apply -f acr-nginx.yaml
```

Az üzembe helyezést a következő futtatásával figyelheti:

```console
kubectl get pods
```

Két futó podnak kell lennie.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Hibaelhárítás
* Futtassa [az az aks check-acr parancsot](/cli/azure/aks#az_aks_check_acr) annak ellenőrzéséhez, hogy a beállításjegyzék elérhető-e az AKS-fürtből.
* További információ az [ACR Diagnosticsról](../container-registry/container-registry-diagnostics-audit-logs.md)
* További információ az [ACR Health-ről](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/