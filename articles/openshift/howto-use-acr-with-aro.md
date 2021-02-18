---
title: Azure Container Registry használata az Azure Red Hat OpenShift
description: Megtudhatja, hogyan hívhat le és futtathat egy tárolót Azure Container Registry az Azure Red Hat OpenShift-fürtön.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 01/10/2021
ms.openlocfilehash: 651b73db084e8090f59faeffa9991c2ac468ca08
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634413"
---
# <a name="use-azure-container-registry-with-azure-red-hat-openshift-aro"></a>Azure Container Registry használata az Azure Red Hat OpenShift (ARO) használatával

A Azure Container Registry (ACR) egy felügyelt tárolói beállításjegyzék-szolgáltatás, amellyel a privát Docker-tárolók lemezképeit nagyvállalati funkciókkal, például geo-replikálással is tárolhatja. Ha az ACR-t egy ARO-fürthöz szeretné elérni, a fürt a Docker bejelentkezési hitelesítő adatainak a Kubernetes titkos kódjában történő tárolásával képes hitelesíteni az ACR-t.  Hasonlóképpen, egy ARO-fürt használhat egy imagePullSecret a pod specban, hogy hitelesítse magát a beállításjegyzékben a rendszerkép húzásakor. Ebből a cikkből megtudhatja, hogyan állíthat be egy Azure Container Registryt egy Azure Red Hat OpenShift-fürttel a privát Docker-tárolók rendszerképeinek tárolásához és lekéréséhez.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató feltételezi, hogy rendelkezik egy meglévő Azure Container Registry. Ha nem, a Azure Portal vagy az [Azure CLI utasítás](../container-registry/container-registry-get-started-azure-cli.md) használatával hozzon létre egy tároló-beállításjegyzéket.

A cikk azt is feltételezi, hogy rendelkezik egy meglévő Azure Red Hat OpenShift-fürttel, és `oc` telepítette a CLI-t. Ha nem, kövesse az ARO- [fürt létrehozása oktatóanyag](tutorial-create-cluster.md)utasításait.

## <a name="get-a-pull-secret"></a>Lekéréses titok beolvasása

Szüksége lesz egy lekéréses titokra az ACR-től a beállításjegyzék eléréséhez az ARO-fürtről.

A lekéréses titkos hitelesítő adatok beszerzéséhez használhatja a Azure Portal vagy az Azure CLI-t is.

Ha a Azure Portal használja, navigáljon az ACR-példányhoz, és válassza a **hozzáférési kulcsok** elemet.  Az Ön `docker-username` a tároló-beállításjegyzék neve, vagy a jelszó-vagy password2 használja `docker-password` .

![Hozzáférési kulcsok](./media/acr-access-keys.png)

Ehelyett használhatja az Azure CLI-t a következő hitelesítő adatok beszerzéséhez:
```azurecli
az acr credential show -n <your registry name>
```

## <a name="create-the-kubernetes-secret"></a>A titkos Kubernetes létrehozása

Most ezeket a hitelesítő adatokat fogjuk használni egy titkos Kubernetes létrehozásához. Hajtsa végre a következő parancsot az ACR hitelesítő adataival:

```
oc create secret docker-registry \
    --docker-server=<your registry name>.azurecr.io \
    --docker-username=<your registry name> \
    --docker-password=******** \
    --docker-email=unused \
    acr-secret
```

>[!NOTE]
>Ez a titok a jelenlegi OpenShift-projektben (Kubernetes-névtérben) lesz tárolva, és csak a projektben létrehozott hüvelyekkel hivatkozhat.  Tekintse meg ezt a [dokumentumot](https://docs.openshift.com/container-platform/4.4/openshift_images/managing_images/using-image-pull-secrets.html) , ha további útmutatásra van a fürtre kiterjedő lekéréses titok létrehozásához.

## <a name="create-a-pod-using-a-private-registry-image"></a>Pod létrehozása privát beállításjegyzék-rendszerkép használatával

Most, hogy csatlakoztatta az ARO-fürtöt az ACR-hez, hozzon létre egy képet az ACR-ből egy Pod létrehozásához.

Kezdjen egy podSpec, és határozza meg a imagePullSecret létrehozott titkot:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: <your registry name>.azurecr.io/hello-world:v1
  imagePullSecrets:
  - name: acr-secret
```

Annak ellenőrzéséhez, hogy a pod működik-e, hajtsa végre a parancsot, és várjon, amíg az állapot **fut**:

```bash
$ oc get pods --watch
NAME         READY   STATUS    RESTARTS   AGE
hello-world  1/1     Running   0          30s
```

## <a name="next-steps"></a>Következő lépések

* [Azure Container Registry](../container-registry/container-registry-concepts.md)
* [Rövid útmutató: saját tároló-beállításjegyzék létrehozása az Azure CLI használatával](../container-registry/container-registry-get-started-azure-cli.md)
