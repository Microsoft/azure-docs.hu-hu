---
title: Windows-tárolók használata
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Ismerje meg, hogyan futtathat Azure dev-helyeket meglévő fürtön Windows-tárolókkal
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AK, Azure Kubernetes szolgáltatás, tárolók, Windows-tárolók
ms.openlocfilehash: 682af37b79083fb360a17f87624f69239c03549d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198183"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Windows-tárolók használata az Azure dev Spaces használatával

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Az Azure dev Spaces szolgáltatást az új és a meglévő Kubernetes-névtereken is engedélyezheti. Az Azure dev Spaces szolgáltatás a Linux-tárolókban futó eszközökön fut. Ezek a szolgáltatások a Windows-tárolókban futó alkalmazásokat is kezelhetik ugyanabban a névtérben. Ebből a cikkből megtudhatja, hogyan használhatja a dev Spaces szolgáltatást a meglévő Windows-tárolókkal rendelkező névtérben lévő szolgáltatások futtatására. Jelenleg nem tud hibakeresést végezni a Windows-tárolók és az Azure dev Spaces összekapcsolásával.

## <a name="set-up-your-cluster"></a>A fürt beállítása

Ez a cikk azt feltételezi, hogy már rendelkezik egy, a Linux-és Windows-csomópont-készlettel rendelkező fürttel. Ha Linux-és Windows-csomópont-készletekkel rendelkező fürtöt kell létrehoznia, kövesse az [itt][windows-container-cli]található utasításokat.

Kapcsolódjon a fürthöz a [kubectl][kubectl]és a Kubernetes parancssori ügyfél használatával. Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példában a kimenet egy Windows-és Linux-csomóponttal rendelkező fürtöt mutat be. A folytatás előtt győződjön meg arról, hogy az állapot *készen áll* az egyes csomópontokra.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Alkalmazzon egy [Taint][using-taints] a Windows-csomópontjaira. A Windows-csomópontok megromlása megakadályozza, hogy a fejlesztői helyek a Linux-tárolókat ütemezve futtassák a Windows-csomópontokon. Az alábbi parancs-példában szereplő parancs egy szennyező elemet alkalmaz a *aksnpwin987654* Windows-csomópontra az előző példából.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Ha egy csomópontra alkalmazza a Taint, a szolgáltatás ezen a csomóponton való futtatásához a szolgáltatás központi telepítési sablonjában meg kell adnia a megfelelő tolerancia beállítását. A minta alkalmazás már konfigurálva van az előző parancsban konfigurált adatszennyező értékkel [egyezően][sample-application-toleration-example] .

## <a name="run-your-windows-service"></a>Windows-szolgáltatás futtatása

Futtassa a Windows-szolgáltatást az AK-fürtön, és ellenőrizze, hogy *futó* állapotban van-e. Ez a cikk egy [minta alkalmazást][sample-application] használ a fürtön futó Windows-és Linux-szolgáltatások bemutatására.

A minta alkalmazás klónozása a GitHubról, majd a `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` címtárba való belépés:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

A minta alkalmazás a [Helm 3][helm-installed] használatával futtatja a Windows-szolgáltatást a fürtön. Navigáljon a `charts` címtárhoz, és használja a Windows-szolgáltatás futtatását:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

A fenti parancs a Helm használatával futtatja a Windows-szolgáltatást a *fejlesztői* névtérben. Ha nem rendelkezik *dev* nevű névtérrel, a rendszer létrehozza.

A `kubectl get pods` parancs használatával ellenőrizheti, hogy a Windows-szolgáltatás fut-e a fürtben. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Az Azure dev Spaces használatának engedélyezése

Engedélyezze a fejlesztői helyeket a Windows-szolgáltatás futtatásához használt névtérben. A következő parancs engedélyezi a dev Spaces használatát a *fejlesztői* névtérben:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Windows-szolgáltatás frissítése a fejlesztői tárhelyekhez

Ha egy már futó tárolóval rendelkező meglévő névtérben engedélyezi a dev Spaces szolgáltatást, a dev Spaces szolgáltatás alapértelmezés szerint megkísérli a névtérben futó új tárolók kiválasztását. A dev Spaces emellett a névtérben már futó, szolgáltatáshoz létrehozott új tárolókat is megpróbálja kipróbálni. Ha meg szeretné akadályozni, hogy a fejlesztői területek a névtérben futó tárolót használjanak, adja hozzá a *No-proxy* fejlécet a következőhöz: `deployment.yaml` .

Hozzáadás `azds.io/no-proxy: "true"` a `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` fájlhoz:

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

A használatával `helm list` listázhatja a Windows-szolgáltatás központi telepítését:

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

A fenti példában az *üzemelő példány neve Windows-Service*. Frissítse Windows-szolgáltatását az új konfigurációval a használatával `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

A frissítése óta a `deployment.yaml` fejlesztői tárhelyek nem fogják kipróbálni és kiépíteni a szolgáltatást.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Linux-alkalmazás futtatása az Azure dev Spaces-szel

Navigáljon a `webfrontend` címtárhoz, és a `azds prep` és a parancsok használatával futtassa a Linux-alkalmazást a `azds up` fürtön.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

A `azds prep --enable-ingress` parancs létrehozza a Helm-diagramot és a Dockerfiles az alkalmazáshoz.

> [!TIP]
> A projekthez tartozó [Docker és Helm diagramot](../how-dev-spaces-works-prep.md#prepare-your-code) az Azure dev Spaces használja a kód összeállításához és futtatásához, de módosíthatja ezeket a fájlokat, ha módosítani szeretné a projekt felépítésének és futtatásának módját.

A `azds up` parancs futtatja a szolgáltatást a névtérben.

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

A szolgáltatás futtatásához nyissa meg a nyilvános URL-címet, amely a azds up parancs kimenetében jelenik meg. Ebben a példában a nyilvános URL-cím a következő: `http://dev.webfrontend.abcdef0123.eus.azds.io/` . Navigáljon a szolgáltatáshoz egy böngészőben, és kattintson a felül található *Névjegy* elemre. Ellenőrizze, hogy megjelenik-e a *mywebapi* szolgáltatás azon üzenete, amely a tároló által használt Windows-verziót tartalmazza.

![Minta alkalmazás, amely a Windows-verziót mutatja a mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Következő lépések

További információ az Azure dev Spaces működéséről.

> [!div class="nextstepaction"]
> [Az Azure Dev Spaces működése](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
