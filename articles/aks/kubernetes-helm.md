---
title: Meglévő alkalmazások telepítése a Helmtel az AK-ban
description: Megtudhatja, hogyan helyezhet üzembe tárolókat az Azure Kubernetes Service (ak)-fürtben a Helm Packaging eszköz használatával
services: container-service
author: zr-msft
ms.topic: article
ms.date: 12/07/2020
ms.author: zarhoads
ms.openlocfilehash: f12dffe0b538738a8f6dd00cd3d87d44da828f21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96779167"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Meglévő alkalmazások telepítése a Helm szolgáltatással az Azure Kubernetes Service-ben (ak)

A [Helm][helm] egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez. A Linux-csomagkezelő, például az *apt* és a *yum* hasonlóan a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ez a cikk bemutatja, hogyan konfigurálhatja és használhatja a Helm-t egy Kubernetes-fürtön az AK-ban.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szükség van a Helm CLI telepítésére is, amely a fejlesztői rendszeren futó ügyfél. Lehetővé teszi az alkalmazások indítását, leállítását és kezelését a Helm használatával. Ha a Azure Cloud Shell használja, a Helm CLI már telepítve van. A helyi platformra vonatkozó telepítési utasításokért lásd: a [Helm telepítése][helm-install].

> [!IMPORTANT]
> A Helm Linux-csomópontokon fut. Ha a fürtben Windows Server-csomópontok vannak, akkor győződjön meg arról, hogy a Helm hüvelyek csak Linux-csomópontokon futnak. Emellett biztosítania kell, hogy a telepített Helm-diagramok is a megfelelő csomópontokon fussanak. A cikkben szereplő parancsok [NODE-választói] [k8s-Node-választó] használatával gondoskodnak arról, hogy a hüvelyek a megfelelő csomópontokra legyenek ütemezve, de nem minden Helm-diagramon lehet kijelölni egy csomópont-választót. Érdemes lehet más beállításokat is használni a fürtön, például a [szennyező][taints]elemek használatával.

## <a name="verify-your-version-of-helm"></a>A Helm verziójának ellenőrzése

A `helm version` parancs használatával ellenőrizze, hogy a Helm 3 telepítve van-e:

```console
helm version
```

A következő példa azt mutatja be, hogy a Helm Version 3.0.0 telepítve van:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Alkalmazás telepítése a Helm v3-vel

### <a name="add-helm-repositories"></a>Helm-adattárak hozzáadása

A [Helm repo][helm-repo-add] parancs használatával adja hozzá a *bejövő-Nginx* tárházat.

```console
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Helm-diagramok keresése

A Helm-diagramok használatával az alkalmazások Kubernetes-fürtbe helyezhetők. Az előre létrehozott Helm-diagramok kereséséhez használja a [Helm Search][helm-search] parancsot:

```console
helm search repo ingress-nginx
```

A következő összehasonlított példa kimenete a következőkhöz használható Helm-diagramok némelyikét mutatja be:

```console
$ helm search repo ingress-nginx

NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
ingress-nginx/ingress-nginx     2.12.0          0.34.1          Ingress controller for Kubernetes using NGINX a...
```

A diagramok listájának frissítéséhez használja a [Helm repo Update][helm-repo-update] parancsot.

```console
helm repo update
```

Az alábbi példa egy sikeres adattár-frissítést mutat be:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm-diagramok futtatása

Ha a diagramokat a Helm használatával szeretné telepíteni, használja a [Helm install][helm-install-command] parancsot, és adjon meg egy kiadási nevet és a telepítendő diagram nevét. A Helm-diagramok működés közbeni telepítésének megtekintéséhez telepítsen egy alapszintű Nginx-telepítést egy Helm-diagram használatával.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A következő összehasonlított példa kimenet a Helm diagram által létrehozott Kubernetes-erőforrások telepítési állapotát mutatja:

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

Használja a `kubectl get services` parancsot a szolgáltatás *külső IP-* címének lekéréséhez.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

Az alábbi parancs például a *My-Nginx-* inintegrációs-beáramló-ba-Nginx-Controller szolgáltatás *külső IP-címét* jeleníti meg:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>Kiadások listázása

A fürtön telepített kiadások listájának megtekintéséhez használja az `helm list` parancsot.

```console
helm list
```

Az alábbi példa az előző lépésben üzembe helyezett *My-Nginx-* beléptetési kiadást mutatja be:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ilyen erőforrások például a hüvelyek, az üzembe helyezések és a szolgáltatások. Ezen erőforrások [törléséhez használja a Helm uninstall][helm-cleanup] parancsot, és adja meg a kiadás nevét az előző parancsban található módon `helm list` .

```console
helm uninstall my-nginx-ingress
```

A következő példa a *My-Nginx-inbehatolás* nevű kiadást mutatja:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Következő lépések

További információ a Kubernetes-alkalmazások Helmtel történő kezeléséről: a Helm dokumentációja.

> [!div class="nextstepaction"]
> [Helm-dokumentáció][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
