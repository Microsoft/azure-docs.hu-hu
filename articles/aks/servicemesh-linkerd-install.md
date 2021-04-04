---
title: A Linkerd telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Linkerd a Service Mesh Azure Kubernetes szolgáltatásbeli (ak-) fürtben való létrehozásához
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5b2ec220bbf14a79247ed2c78a0e84f4717fae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94684236"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>A Linkerd telepítése az Azure Kubernetes szolgáltatásban (ak)

A [Linkerd][linkerd-github] egy nyílt forráskódú Service Mesh-és [CNCF-inkubációs projekt][linkerd-cncf]. A Linkerd egy ultrakönnyű szolgáltatás hálója, amely a forgalomirányítás, a szolgáltatás identitása, a biztonság, a megbízhatóság és a Megfigyelhetőség terén nyújt szolgáltatásokat. A Linkerd kapcsolatos további információkért tekintse meg a hivatalos [Linkerd gyakori kérdések][linkerd-faq] és a [Linkerd architektúrájának][linkerd-architecture] dokumentációját.

Ez a cikk bemutatja, hogyan telepítheti a Linkerd. A Linkerd `linkerd` -ügyfél bináris fájlja telepítve van az ügyfélszámítógépre, és a Linkerd-összetevők a Kubernetes-fürtön vannak telepítve az AK-ban.

> [!NOTE]
> Ezek az utasítások a Linkerd-verzióra hivatkoznak `stable-2.6.0` .
>
> A Linkerd `stable-2.6.x` Kubernetes-verziókkal is futtatható `1.13+` . További stabil és peremhálózati Linkerd-verziók a [GitHub-Linkerd kiadásokban][linkerd-github-releases]találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Töltse le és telepítse a Linkerd Linkerd-ügyfél bináris fájlját
> * A Linkerd telepítése az AK-on
> * A Linkerd telepítésének ellenőrzése
> * Az irányítópult elérése
> * Linkerd eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.13` és újabb, KUBERNETES RBAC engedélyezve), és létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket.

Az összes Linkerd-hüvelyt Linux-csomópontokon kell futtatni – ez a beállítás az alapértelmezett telepítési módszer az alábbiakban részletezett módon, és nem igényel további konfigurálást.

Ez a cikk a Linkerd telepítési útmutatóját több különálló lépésben elkülöníti. Az eredmény ugyanaz, mint a hivatalos Linkerd az első lépéseket ismertető [útmutatóban][linkerd-getting-started].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>A Linkerd telepítése az AK-on

A Linkerd telepítése előtt a telepítés előtti ellenőrzéseket futtatjuk annak megállapításához, hogy a vezérlő síkja telepíthető-e az AK-fürtön:

```console
linkerd check --pre
```

A következőhöz hasonlóan kell megjelennie, hogy az AK-fürt érvényes telepítési cél a Linkerd számára:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Most itt az ideje, hogy telepítse a Linkerd-összetevőket. A `linkerd` és a `kubectl` bináris fájlok használatával telepítse a Linkerd összetevőket az AK-fürtbe. A `linkerd` rendszer automatikusan létrehozza a névteret, és az összetevőket a rendszer ebbe a névtérbe telepíti.

```console
linkerd install | kubectl apply -f -
```

A Linkerd számos objektumot telepít. A lista a fenti parancs kimenetében jelenik meg `linkerd install` . A Linkerd-összetevők üzembe helyezéséhez a fürt környezetéből függően 1 percet kell elvégeznie.

Ezen a ponton üzembe helyezte a Linkerd-t az AK-fürtön. A Linkerd sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a [Linkerd telepítésének ellenőrzéséhez](#validate-the-linkerd-installation).

## <a name="validate-the-linkerd-installation"></a>A Linkerd telepítésének ellenőrzése

Győződjön meg arról, hogy az erőforrások létrehozása sikeresen megtörtént. A [kubectl Get SVC][kubectl-get] és a [kubectl Get Pod][kubectl-get] parancsok használatával kérdezheti le a `linkerd` névteret, ahol a Linkerd-összetevőket a parancs telepítette `linkerd install` :

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

A következő példa kimenetében láthatók a szolgáltatások és a hüvelyek (a Linux-csomópontokon ütemezettek), amelyeknek mostantól futniuk kell:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

A Linkerd az `linkerd` ügyfél bináris fájlján keresztül biztosít egy parancsot annak ellenőrzéséhez, hogy a Linkerd vezérlő síkja sikeresen telepítve és konfigurálva van-e.

```console
linkerd check
```

A telepítés sikerességét az alábbihoz hasonló módon kell megjelennie:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Az irányítópult elérése

A Linkerd tartalmaz egy irányítópultot, amely betekintést nyújt a szolgáltatás hálóba és munkaterhelésbe. Az irányítópult eléréséhez használja az `linkerd dashboard` parancsot. Ez a parancs a [kubectl-portot][kubectl-port-forward] használja fel, hogy biztonságos kapcsolatot hozzon létre az ügyfélszámítógép és az AK-fürt megfelelő hüvelyei között. Ekkor a rendszer automatikusan megnyitja az irányítópultot az alapértelmezett böngészőben.

```console
linkerd dashboard
```

A parancs egy portot is létrehoz, és egy hivatkozást ad vissza a Grafana-irányítópultokhoz.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>Linkerd eltávolítása az AK-ból

> [!WARNING]
> A Linkerd egy futó rendszerből való törlése a szolgáltatások közötti forgalomhoz vezethet. A folytatás előtt győződjön meg arról, hogy rendelkezik a rendszer megfelelő működéséhez szükséges Linkerd nélkül.

Először el kell távolítania az adatsík-proxykat. Távolítsa el az automatikus proxy-injektálási [megjegyzéseket][linkerd-automatic-proxy-injection] a számítási feladatok névterei közül, és állítsa be a számítási feladatok központi telepítését. A munkaterhelések nem rendelkezhetnek többé társított adatsík-összetevőkkel.

Végül távolítsa el a vezérlő síkot a következőképpen:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Következő lépések

A Linkerd további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos Linkerd útmutatót:

- [Linkerd-Helm telepítés][linkerd-install-with-helm]
- [Linkerd – többfázisú telepítés a szerepkör-jogosultságok ellátásához][linkerd-multi-stage-installation]

A következő esetekben is elvégezheti a további forgatókönyvek használatát:

- [Linkerd emojivoto bemutató][linkerd-demo-emojivoto]
- [Linkerd könyvek bemutatója][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
