---
title: Hitelesítés a Kubernetes-fürtből
description: Megtudhatja, hogyan biztosíthat Kubernetes-fürtöt az Azure Container registryben található rendszerképekhez egy egyszerű szolgáltatásnév használatával
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 05/28/2020
ms.openlocfilehash: fbf5dfd68b823b600b11cad3643e5d4004b85ff5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84309815"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Képek lekérése egy Azure Container registryből egy Kubernetes-fürtre

Az Azure Container Registry-t tároló lemezképek forrásaként bármely Kubernetes-fürthöz használhatja, beleértve a "helyi" Kubernetes-fürtöket, például a [minikube](https://minikube.sigs.k8s.io/) -t és a [fajtát](https://kind.sigs.k8s.io/)is. Ez a cikk bemutatja, hogyan hozhat létre egy Kubernetes-lekérési titkot egy Azure Active Directory egyszerű szolgáltatásnév alapján. Ezután a titok használatával lehívhatja a képeket egy Azure Container registryből egy Kubernetes-telepítésben.

> [!TIP]
> Ha a felügyelt [Azure Kubernetes szolgáltatást](../aks/intro-kubernetes.md)használja, akkor a fürtöt a képek lekéréséhez a cél Azure Container Registry szolgáltatással is [integrálhatja](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) . 

Ez a cikk feltételezi, hogy már létrehozott egy privát Azure Container registryt. Emellett a parancssori eszközön keresztül is futnia kell egy Kubernetes-fürtnek, és elérhetőnek kell lennie `kubectl` .

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

Ha nem menti vagy nem emlékszik az egyszerű szolgáltatásnév jelszavára, az az [ad SP hitelesítőadat-visszaállítási][az-ad-sp-credential-reset] paranccsal állíthatja vissza:

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

Ez a parancs egy új, érvényes jelszót ad vissza az egyszerű szolgáltatásnév számára.

## <a name="create-an-image-pull-secret"></a>Rendszerkép-lekérési titok létrehozása

A Kubernetes egy *rendszerkép-lekérési titok* használatával tárolja a beállításjegyzékben való hitelesítéshez szükséges adatokat. Az Azure Container registryhez tartozó lekérési titok létrehozásához adja meg az egyszerű szolgáltatás AZONOSÍTÓját, jelszavát és a beállításjegyzék URL-címét. 

Hozzon létre egy rendszerképet a következő `kubectl` paranccsal:

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
ahol:

| Érték | Leírás |
| :--- | :--- |
| `secret-name` | A képkeresési titok neve, például *ACR-Secret* |
| `namespace` | Kubernetes névtér, amely a titkot a következőre helyezi <br/> Csak akkor szükséges, ha a titkot az alapértelmezett névtértől eltérő névtérben kívánja elhelyezni. |
| `container-registry-name` | Az Azure Container Registry neve, például *myregistry*<br/><br/>A a `--docker-server` beállításjegyzék bejelentkezési kiszolgálójának teljesen minősített neve  |
| `service-principal-ID` | Annak az egyszerű szolgáltatásnak az azonosítója, amelyet a Kubernetes a beállításjegyzék eléréséhez fog használni |
| `service-principal-password` | Egyszerű szolgáltatásnév jelszava |

## <a name="use-the-image-pull-secret"></a>A rendszerkép lekérési titkának használata

Miután létrehozta a rendszerkép lekérésének titkát, használhatja Kubernetes-hüvelyek és-telepítések létrehozásához. Adja meg a titkos kulcs nevét a `imagePullSecrets` telepítési fájlban. Például:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

Az előző példában a a `my-awesome-app:v1` rendszerkép neve, amelyet az Azure Container registryből kell lekérni, és a  `acr-secret` létrehozott lekérési titok neve a beállításjegyzék eléréséhez. A pod telepítésekor a Kubernetes automatikusan lekéri a rendszerképet a beállításjegyzékből, ha még nem szerepel a fürtön.


## <a name="next-steps"></a>Következő lépések

* Az egyszerű szolgáltatások és a Azure Container Registry használatáról további információt a következő témakörben talál: [Azure Container Registry hitelesítés egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md)
* További információ a képek lekérési titkairól a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset