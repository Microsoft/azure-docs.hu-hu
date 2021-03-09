---
title: Azure Container Registry integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Kubernetes szolgáltatás (ak) a Azure Container Registry (ACR) használatával
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 19ece696dabc81e643e8a904d506d22e40eaa099
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499152"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Container Registryvel történő hitelesítés az Azure Kubernetes Service-ből (AKS)

Ha Azure Container Registryt (ACR) használ az Azure Kubernetes szolgáltatással (ak), akkor hitelesítési mechanizmust kell létrehoznia. Ez a művelet a CLI és a portál felhasználói felületének részeként valósul meg, az ACR számára szükséges engedélyek megadásával. Ez a cikk példákat tartalmaz a két Azure-szolgáltatás közötti hitelesítés konfigurálására. 

Beállíthatja az AK-t az ACR-integrációra néhány egyszerű parancsban az Azure CLI-vel. Ez az integráció a AcrPull-szerepkört az AK-fürthöz társított felügyelt identitáshoz rendeli.

> [!NOTE]
> Ez a cikk az AK és az ACR közötti automatikus hitelesítést ismerteti. Ha privát külső beállításjegyzékből kell lekérnie a képet, használjon [képet lekéréses titkos kulcsot][Image Pull Secret].

## <a name="before-you-begin"></a>Előkészületek

A példákhoz a következők szükségesek:

* Az **Azure-előfizetéshez** tartozó **tulajdonosi** vagy **Azure-fiók rendszergazdai** szerepköre
* Azure CLI-verzió 2.7.0 vagy újabb verziója

Ha el szeretné kerülni a **tulajdonosi** vagy az **Azure-fiók rendszergazdai** szerepkörét, manuálisan konfigurálhatja a felügyelt identitást, vagy egy meglévő felügyelt identitás használatával hitelesítheti az ACR-t az AK-ból. További információ: [Azure-beli felügyelt identitás használata az Azure Container registryben való hitelesítéshez](../container-registry/container-registry-authentication-managed-identity.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Új AK-fürt létrehozása ACR-integrációval

Itt állíthatja be az AK-t és az ACR-integrációt az AK-fürt kezdeti létrehozása során.  Annak engedélyezéséhez, hogy egy AK-fürt együttműködjön az ACR-szel, Azure Active Directory **felügyelt identitást** kell használni. A következő CLI-parancs lehetővé teszi egy meglévő ACR engedélyezését az előfizetésében, és konfigurálja a megfelelő **ACRPull** szerepkört a felügyelt identitáshoz. Adja meg az alábbi paraméterek érvényes értékeit.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Alternatív megoldásként megadhatja az ACR nevét egy ACR erőforrás-AZONOSÍTÓval, amelynek formátuma a következő:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Ha olyan ACR-t használ, amely egy másik előfizetésben található az AK-fürttől, használja az ACR erőforrás-AZONOSÍTÓját, amikor egy AK-fürthöz csatolja vagy leválasztja őket.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Ez a lépés több percet is igénybe vehet.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integráció konfigurálása meglévő AK-fürtökhöz

Az alábbihoz tartozó **ACR-Name** vagy **ACR-Resource-id** érvényes értékek megadásával integrálhat egy meglévő, meglévő "ACR" típusú fürtöket.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

vagy

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Az ACR és az AK-fürt közötti integrációt is eltávolíthatja a következőkkel

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

vagy

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>ACR-& AK használata

### <a name="import-an-image-into-your-acr"></a>Rendszerkép importálása az ACR-be

Importáljon egy rendszerképet a Docker hub-ből az ACR-be a következő parancs futtatásával:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>A minta rendszerképének üzembe helyezése ACR-ből AK-ra

Győződjön meg arról, hogy megfelelő AK-beli hitelesítő adatokkal rendelkezik

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Hozzon létre egy **ACR-Nginx. YAML** nevű fájlt, amely a következőt tartalmazza. Helyettesítse be a beállításjegyzék erőforrásának nevét az **ACR-Name** kifejezésre. Példa: *myContainerRegistry*.

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

Ezután futtassa ezt az üzembe helyezést az AK-fürtben:

```console
kubectl apply -f acr-nginx.yaml
```

A központi telepítést a futtatásával figyelheti:

```console
kubectl get pods
```

Két futó hüvelynek kell lennie.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Hibaelhárítás
* Az az [AK-beli ellenőrzés-ACR](/cli/azure/aks#az_aks_check_acr) parancs futtatásával ellenőrizze, hogy a beállításjegyzék elérhető-e az AK-fürtről.
* További információ az [ACR Diagnostics](../container-registry/container-registry-diagnostics-audit-logs.md) szolgáltatásról
* További információ az [ACR Health](../container-registry/container-registry-check-health.md) szolgáltatásról

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/