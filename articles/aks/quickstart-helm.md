---
title: Fejlesztés az Azure Kubernetes szolgáltatásban (ak) a Helmtal
description: Az alkalmazás-tárolók fürtben való kicsomagolásához és futtatásához használja az AK-t és a Azure Container Registry-t.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493077"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Gyors útmutató: fejlesztés az Azure Kubernetes Service-ben (ak) a Helmtal

A [Helm][helm] egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez. A Linux-csomagkezelő, például az *apt* és a *yum* hasonlóan a Helm kezeli a Kubernetes-diagramokat, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ebben a rövid útmutatóban a Helm használatával csomagolja ki és futtatja az alkalmazást az AK-on. Egy meglévő alkalmazás Helm használatával történő telepítésével kapcsolatos további információkért tekintse meg a [meglévő alkalmazások telepítése az AK-ban][helm-existing] című témakört, útmutató.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* Az [Azure CLI telepítve van](/cli/azure/install-azure-cli).
* A [Helm v3 telepítve van][helm-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
A tároló rendszerképeit egy Azure Container Registry (ACR) tárolóban kell tárolnia, hogy az alkalmazás az AK-fürtben fusson a Helm használatával. Az Azure-ban egyedi, 5-50 alfanumerikus karaktereket tartalmazó beállításjegyzékbeli nevet adjon meg. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

Az alábbi példa az [az ACR Create][az-acr-create] paranccsal hoz létre egy *MyHelmACR* nevű ACR-t a *MyResourceGroup* -ben az *alapszintű* SKU használatával.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

A kimenet az alábbi példához hasonló lesz. Jegyezze fel az ACR *lekéréséhez* értékét, mivel ezt egy későbbi lépésben fogja használni. Az alábbi példában a *myhelmacr.azurecr.IO* a *myhelmacr* *lekéréséhez* .

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az új AK-fürtnek hozzá kell férnie az ACR-hez a tároló lemezképének lekéréséhez és futtatásához. Az alábbi paranccsal végezze el a következőket:
* Hozzon létre egy *MyAKS* nevű AK-fürtöt, és csatolja a *MyHelmACR*.
* Adja meg a *MyAKS* -fürt hozzáférését a *MyHelmACR* ACR-hez.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Kapcsolódás az AK-fürthöz

Egy Kubernetes-fürt helyi összekapcsolásához használja a [Kubectl][kubectl]Kubernetes parancssori ügyfelet. `kubectl` már telepítve van a Azure Cloud Shell használata esetén. 

1. Telepítse `kubectl` helyileg a `az aks install-cli` parancsot a parancs használatával:

    ```azurecli
    az aks install-cli
    ```

2. Konfigurálja a `kubectl` parancsot a Kubernetes-fürthöz való kapcsolódáshoz a `az aks get-credentials` parancs használatával. A következő parancs például a *MyAKS* nevű AK-fürt hitelesítő adatait kéri le a *MyResourceGroup*:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ez a rövid útmutató [egy példát Node.js alkalmazást használ az Azure dev Spaces minta adattárból][example-nodejs]. Klónozott az alkalmazást a GitHubról, és navigáljon a `dev-spaces/samples/nodejs/getting-started/webfrontend` címtárhoz.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Dockerfile létrehozása

Hozzon létre egy új *Docker* -fájlt a következő parancsok használatával:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>A minta alkalmazás létrehozása és leküldése az ACR-be

Az előző Docker használatával futtassa az az [ACR Build][az-acr-build] parancsot egy rendszerkép létrehozásához és a beállításjegyzékbe való leküldéséhez. A `.` parancs végén a Docker helye (ebben az esetben az aktuális könyvtár) van beállítva.

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>A Helm-diagram létrehozása

A Helm-diagramot a parancs használatával hozhatja ki `helm create` .

```console
helm create webfrontend
```

*Webfrontend/Values. YAML* frissítése:
* Cserélje le a beállításjegyzék egy korábbi lépésben feljegyzett lekéréséhez, például *myhelmacr.azurecr.IO*.
* Módosítás `image.repository` ide `<loginServer>/webfrontend`
* Módosítás `service.type` ide `LoadBalancer`

Például:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Frissítés `appVersion` a `v1` következőhöz: *webfrontend/chart. YAML*. Példa:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>A Helm-diagram futtatása

Telepítse az alkalmazást a Helm-diagram használatával a `helm install` paranccsal.

```console
helm install webfrontend webfrontend/
```

Néhány percet vesz igénybe, hogy a szolgáltatás egy nyilvános IP-címet ad vissza. Figyelje a folyamatot az `kubectl get service` argumentummal a parancs használatával `--watch` .

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Nyissa meg az alkalmazás Load balancerét egy böngészőben a használatával a `<EXTERNAL-IP>` alkalmazásban.

## <a name="delete-the-cluster"></a>A fürt törlése

Az az [Group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, az AK-fürtöt, a tároló-beállításjegyzéket, az ACR-ben tárolt tároló-lemezképeket és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.
> 
> Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="next-steps"></a>További lépések

A Helm használatával kapcsolatos további információkért tekintse meg a Helm dokumentációját.

> [!div class="nextstepaction"]
> [Helm-dokumentáció][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
