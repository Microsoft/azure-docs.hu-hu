---
title: A Istio telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Istio a Service Mesh Azure Kubernetes szolgáltatásbeli (ak-) fürtben való létrehozásához
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94683828"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>A Istio telepítése és használata az Azure Kubernetes szolgáltatásban (ak)

A [Istio][istio-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtökön futó összes szolgáltatás egyik kulcsfontosságú készletét biztosítja. Ezek a szolgáltatások közé tartoznak a forgalomirányítás, a szolgáltatások identitása és a biztonság, a házirendek betartatása és a Megfigyelhetőség. A Istio kapcsolatos további információkért tekintse meg a hivatalos [Mi az Istio?][istio-docs-concepts] dokumentációt.

Ez a cikk bemutatja, hogyan telepítheti a Istio. A Istio `istioctl` -ügyfél bináris fájlja telepítve van az ügyfélszámítógépre, és a Istio-összetevők a Kubernetes-fürtön vannak telepítve az AK-ban.

> [!NOTE]
> Az alábbi utasítások a Istio-verzióra hivatkoznak `1.7.3` .
>
> A Istio- `1.7.x` kiadásokat a Istio csapat tesztelte a Kubernetes verziójával `1.16+` . További Istio-verziók a [GitHub-Istio kiadásokban][istio-github-releases], valamint a [Istio-Hírek][istio-release-notes] és a támogatott Kubernetes-verziók információi a [Istio általános gyakori kérdések][istio-faq]című témakörben találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját
> * A Istio telepítése az AK-on
> * A Istio telepítésének ellenőrzése
> * A bővítmények elérése
> * Istio eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.16` és újabb, KUBERNETES RBAC engedélyezve), és létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket.

Győződjön meg arról, hogy elolvasta a [Istio teljesítményére és méretezhetőségére](https://istio.io/docs/concepts/performance-and-scalability/) vonatkozó dokumentációt, és Ismerje meg a Istio futtatásához szükséges további erőforrás-követelményeket az AK-fürtben. Az alapvető és a memóriára vonatkozó követelmények az adott munkaterhelés alapján változhatnak. Válasszon megfelelő számú csomópontot és virtuálisgép-méretet a telepítéshez.

Ez a cikk a Istio telepítési útmutatóját több különálló lépésben elkülöníti. A végeredmény ugyanaz, mint a hivatalos Istio telepítési [útmutatója][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>A Istio operátor telepítése az AK-on

A Istio egy [operátort][istio-install-operator] biztosít a Istio-összetevők telepítésének és frissítéseinek kezeléséhez az AK-fürtön belül. A Istio operátort az ügyfél bináris fájljának használatával fogjuk telepíteni `istioctl` .

```bash
istioctl operator init
```

A következő kimenethez hasonlóan kell megjelennie, hogy meggyőződjön arról, hogy a Istio-kezelő telepítve van.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

A Istio operátor a `istio-operator` névtérbe van telepítve. A névtér lekérdezése.

```bash
kubectl get all -n istio-operator
```

Ekkor a következő összetevőket kell telepíteni.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

További információ az operátori mintázatról, valamint arról, hogy miként automatizálható az összetett feladatok automatizálása a [kubernetes.IO][kubernetes-operator]segítségével.


### <a name="install-istio-components"></a>Istio-összetevők telepítése

Most, hogy sikeresen telepítette a Istio-kezelőt az AK-fürtön, itt az ideje, hogy telepítse a Istio-összetevőket. 

A `default` [Istio konfigurációs profilt][istio-configuration-profiles] kihasználjuk a [Istio operátor specifikációjának][istio-control-plane]létrehozásához.

A következő parancs futtatásával `istioctl` megtekintheti a `default` Istio konfigurációs profil konfigurációját.

```bash
istioctl profile dump default
```

> [!NOTE]
> A Istio jelenleg Linux-csomópontokon történő futtatásra kell ütemezni. Ha a fürtben Windows Server-csomópontok vannak, meg kell győződnie arról, hogy a Istio-hüvelyek csak Linux-csomópontokon futnak. A csomópont- [választókkal][kubernetes-node-selectors] gondoskodunk róla, hogy a hüvelyek a megfelelő csomópontokra legyenek ütemezve.

> [!CAUTION]
> A [ISTIO CNI][istio-feature-cni] Istio-funkciók jelenleg az [Alpha][istio-feature-stages]-ben találhatók, ezért ezeket a lépéseket kell megadnia, mielőtt engedélyezi ezeket. 

Hozzon létre egy nevű fájlt `istio.aks.yaml` az alábbi tartalommal. Ez a fájl a [Istio operátor specifikációját][istio-control-plane] fogja tárolni a Istio konfigurálásához.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

Hozza létre a `istio-system` névteret, és telepítse a Istio operátor SPECT a névtérbe. A Istio operátor figyeli a Istio operátor specifikációját, és a használatával telepíti és konfigurálja a Istio-t az AK-fürtben.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

Ezen a ponton üzembe helyezte a Istio-t az AK-fürtön. A Istio sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a [Istio telepítésének ellenőrzéséhez](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>A Istio telepítésének ellenőrzése

Kérdezze le a `istio-system` névteret, ahol a Istio és a kiegészítő összetevőket a Istio-kezelő telepítette:

```bash
kubectl get all -n istio-system
```

A következő összetevőket kell megjelennie:

- `istio*` – a Istio összetevői
- `jaeger-*`, `tracing` és `zipkin` -nyomkövetési addon
- `prometheus` -mérőszámok addon
- `grafana` -elemzési és monitorozási irányítópult addon
- `kiali` -Service Mesh irányítópult addon

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

További betekintést nyerhet a telepítésbe a Istio-kezelő naplófájljainak megtekintésével.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

Ha a `istio-ingressgateway` külső IP-címet mutat `<pending>` , várjon néhány percet, amíg az Azure-hálózat nem rendel hozzá IP-címet.

Az összes hüvelynek állapota a következő: `Running` . Ha a hüvelye nem rendelkezik ezekkel az állapotokkal, várjon egy percet vagy kettőt, amíg meg nem történik. Ha bármelyik hüvely hibát jelez, használja a [kubectl leírását a pod][kubectl-describe] paranccsal a kimenet és az állapot áttekintéséhez.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

A Istio operátor több bővítményt is telepített, amelyek további funkciókat biztosítanak. A bővítmények webalkalmazásai **nem** jelennek meg nyilvánosan külső IP-címen keresztül. 

A bővítmény felhasználói felületének eléréséhez használja az `istioctl dashboard` parancsot. Ez a parancs [kubectl Port-Forward][kubectl-port-forward] és egy véletlenszerű portot használ, hogy biztonságos kapcsolatot hozzon létre az ügyfélszámítógép és a megfelelő Pod az AK-fürtben. Ezután automatikusan megnyitja a bővítmény webalkalmazást az alapértelmezett böngészőben.

### <a name="grafana"></a>Grafana

A Istio tartozó elemzési és figyelési irányítópultokat a [Grafana][grafana]nyújtja. Ha a rendszer kéri, ne felejtse el használni a Grafana titkos kulcson keresztül létrehozott hitelesítő adatokat. A következőképpen nyissa meg biztonságosan a Grafana irányítópultját:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

A Istio metrikáit a [Prometheus][prometheus]nyújtja. A következő módon nyissa meg biztonságosan a Prometheus-irányítópultot:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

A Istio belüli nyomkövetést a [Jaeger][jaeger]biztosítja. A következő módon nyissa meg biztonságosan a Jaeger-irányítópultot:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

A [Kiali][kiali]által biztosított Service Mesh-megfigyelő irányítópultot. Ha a rendszer kéri, ne felejtse el használni a Kiali titkos kulcson keresztül létrehozott hitelesítő adatokat. A következőképpen nyissa meg biztonságosan a Kiali irányítópultját:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Elérhető a [megbízottat][envoy] proxyk egyszerű felülete. Konfigurációs információkat és mérőszámokat biztosít egy megadott Pod-on futó megbízottat proxyhoz. A következő módon nyissa meg biztonságosan a megbízott felületet:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Istio eltávolítása az AK-ból

> [!WARNING]
> A Istio egy futó rendszerből való törlése a szolgáltatások közötti forgalomhoz vezethet. A folytatás előtt győződjön meg arról, hogy rendelkezik a rendszer megfelelő működéséhez szükséges Istio nélkül.

### <a name="remove-istio"></a>Istio eltávolítása

A Istio az AK-fürtből való eltávolításához törölje a `IstioOperator` korábban hozzáadott nevű erőforrást `istio-control-plane` . A Istio operátor felismeri, hogy a Istio operátor specifikációja el lett távolítva, majd törli az összes társított Istio-összetevőt.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

A következő futtatásával ellenőrizhető, hogy az összes Istio-összetevő törölve lett-e.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Istio-operátor eltávolítása

A Istio sikeres eltávolítása után eltávolíthatja a Istio operátort is.

```bash
istioctl operator remove
```

Végül távolítsa el a `istio-` névtereket.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Következő lépések

A Istio további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos Istio útmutatót:

- [Istio – telepítési útmutatók][istio-installation-guides]

A következő esetekben is elvégezheti a további forgatókönyvek használatát:

- [Istio Bookinfo alkalmazás – példa][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
