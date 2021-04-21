---
title: Fejlesztés Azure Kubernetes Service (AKS) a Helmmal
description: A Helm és az AKS Azure Container Registry alkalmazástárolók fürtben való csomagolásához és futtatásához.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: e293d0c58f265b25f3df0a218f84888467468f59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767492"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Rövid útmutató: Fejlesztés Azure Kubernetes Service (AKS) a Helm segítségével

[A Helm][helm] egy nyílt forráskódú csomagolási eszköz, amely a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez nyújt segítséget. A Linux-csomagkezelők, például az *APT* és a *Yum* esetében a Helm a Kubernetes-diagramokat kezeli, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ebben a rövid útmutatóban a Helm használatával becsomagol és futtat egy alkalmazást az AKS-on. A meglévő alkalmazások Helm használatával való telepítésével kapcsolatos további információkért tekintse meg a Meglévő alkalmazások telepítése [a Helm használatával az AKS-ban][helm-existing] útmutatót.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Telepítette az Azure CLI-t.](/cli/azure/install-azure-cli)
* [A Helm v3 telepítve van.][helm-install]

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
A tárolólemezképeket egy Azure Container Registry (ACR) tárolóban kell tárolnia, hogy az alkalmazást az AKS-fürtben futtassa a Helm használatával. Adja meg a regisztrációs adatbázis egyedi nevét az Azure-ban, amely 5–50 alfanumerikus karaktert tartalmaz. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

Az alábbi példa [az az acr create][az-acr-create] segítségével hoz létre egy *MyHelmACR* nevű ACR-t a *MyResourceGroup* csoportban az *alapszintű* termékváltozattal.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

A kimenet az alábbi példához hasonló lesz. Jegyezze fel az ACR *loginServer* értékét, mivel egy későbbi lépésben használni fogja. Az alábbi példában *a myhelmacr.azurecr.io* a *MyHelmACR bejelentkezési kiszolgálója.* 

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

Az új AKS-fürtnek hozzá kell férni az ACR-hez, hogy leküldi és futtassa a tárolólemezképeket. Az alábbi paranccsal végezze el a következőket:
* Hozzon létre egy *MyAKS nevű AKS-fürtöt,* és csatolja a *MyHelmACR fájlt.*
* Adjon hozzáférést *a MyAKS-fürtnek* a *MyHelmACR* ACR-hez.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Csatlakozás az AKS-fürthöz

Kubernetes-fürt helyi csatlakoztatáshoz használja a Kubernetes [kubectl parancssori ügyfelét.][kubectl] `kubectl` A már telepítve van, ha a Azure Cloud Shell. 

1. Telepítse `kubectl` helyileg az `az aks install-cli` paranccsal:

    ```azurecli
    az aks install-cli
    ```

2. Konfigurálja úgy a parancsot, hogy az paranccsal `kubectl` csatlakozzon a Kubernetes-fürthöz. `az aks get-credentials` A következő példaparancs lekért hitelesítő adatokat a MyResourceGroup *myAKS* nevű AKS-fürthöz:   

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ez a rövid útmutató egy [példaalkalmazást Node.js az Azure Dev Spaces mintaadattárából.][example-nodejs] Klónozza az alkalmazást a GitHubról, és lépjen a `dev-spaces/samples/nodejs/getting-started/webfrontend` könyvtárba.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Dockerfile létrehozása

Hozzon létre egy *új Dockerfile-fájlt* a következő parancsokkal:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>A mintaalkalmazás összeállítása és leküldése az ACR-be

Az előző Docker-fájl használatával futtassa [az az acr build][az-acr-build] parancsot egy rendszerkép felépítéséhez és a regisztrációs adatbázisba való leküldéséhez. A parancs végén található beállítja a Docker-fájl helyét (ebben az esetben az `.` aktuális könyvtárat).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Helm-diagram létrehozása

Hozza létre a Helm-diagramot a `helm create` paranccsal.

```console
helm create webfrontend
```

A *webfrontend/values.yaml frissítése:*
* Cserélje le a regisztrációs adatbázis korábbi lépésben feljegyzett loginServer kiszolgálóját, például: *myhelmacr.azurecr.io.*
* Váltsa át `image.repository` a et a `<loginServer>/webfrontend`
* Váltsa át `service.type` a et a `LoadBalancer`

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

Frissítsen `appVersion` a `v1` következőre *a webfrontend/Chart.yaml fájlban:*. Példa:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>A Helm-diagram futtatása

Telepítse az alkalmazást a Helm-diagram használatával az `helm install` paranccsal.

```console
helm install webfrontend webfrontend/
```

Néhány percet vesz igénybe, hogy a szolgáltatás nyilvános IP-címet ad vissza. A folyamat előrehaladásának figyelése `kubectl get service` az paranccsal és a `--watch` argumentummal.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Nyissa meg az alkalmazás terheléseltöltőjét egy böngészőben a használatával, `<EXTERNAL-IP>` és tekintse meg a mintaalkalmazást.

## <a name="delete-the-cluster"></a>A fürt törlése

Az [az group delete paranccsal][az-group-delete] eltávolítható az erőforráscsoport, az AKS-fürt, a tároló-beállításjegyzék, az ACR-ban tárolt tárolólemezképek és az összes kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.
> 
> Ha felügyelt identitást használt, az identitást a platform kezeli, és nem igényel eltávolítást.

## <a name="next-steps"></a>További lépések

A Helm használatával kapcsolatos további információkért tekintse meg a Helm dokumentációját.

> [!div class="nextstepaction"]
> [Helm-dokumentáció][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
