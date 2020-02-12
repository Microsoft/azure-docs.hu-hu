---
title: Hitelesítés a Kubernetes-fürtből
description: Megtudhatja, hogyan biztosíthat Kubernetes-fürtöt az Azure Container registryben található rendszerképekhez egy egyszerű szolgáltatásnév használatával
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154893"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Képek lekérése egy Azure Container registryből egy Kubernetes-fürtre

Az Azure Container Registry-t tároló lemezképek forrásaként bármely Kubernetes-fürthöz használhatja, beleértve a "helyi" Kubernetes-fürtöket, például a [minikube](https://minikube.sigs.k8s.io/) -t és a [fajtát](https://kind.sigs.k8s.io/)is. Ez a cikk bemutatja, hogyan hozhat létre egy Kubernetes-lekérési titkot egy Azure Active Directory egyszerű szolgáltatásnév alapján. Ezután a titok használatával lehívhatja a képeket egy Azure Container registryből egy Kubernetes-telepítésben.

> [!TIP]
> Ha a felügyelt [Azure Kubernetes szolgáltatást](../aks/intro-kubernetes.md)használja, akkor a fürtöt a képek lekéréséhez a cél Azure Container Registry szolgáltatással is [integrálhatja](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) . 

Ez a cikk feltételezi, hogy már létrehozott egy privát Azure Container registryt. Szükség van egy Kubernetes-fürt futtatására is, amely a `kubectl` parancssori eszközön keresztül érhető el.

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
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
ahol

| Érték | Leírás |
| :--- | :--- |
| `secret-name` | A képkeresési titok neve, például *ACR-Secret* |
| `namespace` | Kubernetes névtér, amely a titkot a következőre helyezi <br/> Csak akkor szükséges, ha a titkot az alapértelmezett névtértől eltérő névtérben kívánja elhelyezni. |
| `container-registry-name` | Az Azure Container Registry neve |
| `service-principal-ID` | Annak az egyszerű szolgáltatásnak az azonosítója, amelyet a Kubernetes a beállításjegyzék eléréséhez fog használni |
| `service-principal-password` | Egyszerű szolgáltatásnév jelszava |

## <a name="use-the-image-pull-secret"></a>A rendszerkép lekérési titkának használata

Miután létrehozta a rendszerkép lekérésének titkát, használhatja Kubernetes-hüvelyek és-telepítések létrehozásához. Adja meg a titkos kulcs nevét a központi telepítési fájlban `imagePullSecrets` alatt. Például:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

Az előző példában `your-awesome-app:v1` az Azure Container registryből lekérni kívánt rendszerkép neve, és `acr-secret` a beállításjegyzék eléréséhez létrehozott lekérési titok neve. A pod telepítésekor a Kubernetes automatikusan lekéri a rendszerképet a beállításjegyzékből, ha még nem szerepel a fürtön.


## <a name="next-steps"></a>Következő lépések

* Az egyszerű szolgáltatások és a Azure Container Registry használatáról további információt a következő témakörben talál: [Azure Container Registry hitelesítés egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md)
* További információ a képek lekérési titkairól a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset