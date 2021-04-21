---
title: Windows-tárolók használata
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Megtudhatja, hogyan futtathat Azure Dev Spacest windowsos tárolókat tartalmazó meglévő fürtön
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, tárolók, Windows-tárolók
ms.openlocfilehash: bbef5eafe44e38691327714c14c6a6026d45a3c7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777436"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Windows-tárolók használata az Azure Dev Spaces használatával

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Az Azure Dev Spaces az új és a meglévő Kubernetes-névterekben is engedélyezhető. Az Azure Dev Spaces Linux-tárolókon futó és eszközszolgáltatásokat fog futtatni. Ezek a szolgáltatások az azonos névtérben található Windows-tárolókon futó alkalmazásokkal is interakcióba léphetnek. Ez a cikk bemutatja, hogyan futtathat szolgáltatásokat meglévő Windows-tárolókat tartalmazó névtérben a Dev Spaces használatával. Az Azure Dev Spaces használatával jelenleg nem lehet hibakeresést végezni vagy windowsos tárolókat csatolni.

## <a name="set-up-your-cluster"></a>A fürt beállítása

Ez a cikk feltételezi, hogy már rendelkezik Linux- és Windows-csomópontkészletekkel is. Ha Linux- és Windows-csomópontkészletekkel kell fürtöt létrehoznia, kövesse az itt található [utasításokat.][windows-container-cli]

Csatlakozzon a fürthöz a [kubectl][kubectl], a Kubernetes parancssori ügyfél használatával. Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

Az alábbi példakimenet egy Windows és Linux rendszerű fürtöt mutat be. Mielőtt továbblépne, győződjön meg arról, hogy az állapot *Készen* áll az egyes csomópontok számára.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Fertőzöttság [alkalmazása a][using-taints] Windows-csomópontokra. A Windows-csomópontok fertőzött volta megakadályozza, hogy a Dev Spaces Linux-tárolókat ütemezsen a Windows-csomópontokon való futtatásra. Az alábbi példaparancs egy fertőzöttet alkalmaz az előző példában található *aksnpwin987654* Windows-csomópontra.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Amikor fertőzöttségeket alkalmaz egy csomópontra, konfigurálnia kell egy megfelelő tűrést a szolgáltatás üzembehelyezési sablonján, hogy a szolgáltatás ezen a csomóponton fusson. A mintaalkalmazás már konfigurálva van [az][sample-application-toleration-example] előző parancsban konfigurált fertőzöttségnek megfelelő tűrővel.

## <a name="run-your-windows-service"></a>A Windows-szolgáltatás futtatása

Futtassa a Windows-szolgáltatást az AKS-fürtön, és ellenőrizze, hogy *Fut* állapotban van-e. Ez a cikk egy [mintaalkalmazást használ][sample-application] a fürtön futó Windows- és Linux-szolgáltatás szemléltetására.

Klónozza a mintaalkalmazást a GitHubról, és lépjen a `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` könyvtárba:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A mintaalkalmazás [a Helm 3-as verzióval][helm-installed] futtatja a Windows-szolgáltatást a fürtön. Lépjen a `charts` könyvtárba, és a Helm használatával futtassa a Windows-szolgáltatást:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

A fenti parancs a Helm használatával futtatja a Windows-szolgáltatást a *dev* névtérben. Ha nem hoz létre dev nevű névteret, az létrejön.

Az `kubectl get pods` paranccsal ellenőrizze, hogy a Windows-szolgáltatás fut-e a fürtön. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Az Azure Dev Spaces engedélyezése

Engedélyezze a Dev Spacest ugyanabban a névtérben, mint a Windows-szolgáltatás futtatásához. A következő parancs engedélyezi a Dev Spacest a *dev* névtérben:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Windows-szolgáltatás frissítése a Dev Spaceshez

Ha olyan meglévő névtéren engedélyezi a Dev Spacest, amely már fut, alapértelmezés szerint a Dev Spaces megpróbálja az adott névtérben futó összes új tárolót kiszeretni. A Dev Spaces a névtérben már futó szolgáltatáshoz létrehozott új tárolókat is megpróbálja kiszeretni. Ha meg szeretné akadályozni, hogy a Dev Spaces bevezessen egy, a névtérben futó tárolót, adja hozzá a *no-proxy fejlécet* a fájlhoz. `deployment.yaml`

Adja `azds.io/no-proxy: "true"` hozzá a következőt a `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` fájlhoz:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

A `helm list` windowsos szolgáltatás üzemelő példányának felsorolásához használja a következőt:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

A fenti példában az üzemelő példány neve *windows-service.* Frissítse a Windows-szolgáltatást az új konfigurációval a `helm upgrade` használatával:

```cmd
helm upgrade windows-service . --namespace dev
```

Mivel frissítette a `deployment.yaml` -et, a Dev Spaces nem próbálja meg beszerkesni a szolgáltatást.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Linux-alkalmazás futtatása az Azure Dev Spaces használatával

Lépjen a könyvtárba, és a és a paranccsal futtassa `webfrontend` `azds prep` a `azds up` Linux-alkalmazást a fürtön.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

A `azds prep --enable-ingress` parancs létrehozza a Helm-diagramot és a Docker-fájlokat az alkalmazáshoz.

> [!TIP]
> Az Azure Dev Spaces a projekt [Dockerfile-](../how-dev-spaces-works-prep.md#prepare-your-code) és Helm-diagramját használja a kód felépítéséhez és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítését és futtatását.

A `azds up` parancs a névtérben futtatja a szolgáltatást.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

A futó szolgáltatást a nyilvános URL-cím megnyitásával láthatja, amely az azds up parancs kimenetében jelenik meg. Ebben a példában a nyilvános URL-cím `http://dev.webfrontend.abcdef0123.eus.azds.io/` a következő: . Nyissa meg a szolgáltatást egy böngészőben, és kattintson a lap tetején található *About (About)* elemre. Ellenőrizze, hogy megjelenik-e üzenet a *mywebapi szolgáltatástól,* amely a tároló által használt Windows-verziót tartalmazza.

![Mintaalkalmazás a mywebapi Windows-verziójával](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Következő lépések

További információ az Azure Dev Spacesről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
