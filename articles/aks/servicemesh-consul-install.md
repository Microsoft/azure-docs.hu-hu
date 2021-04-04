---
title: A konzul telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Konzulot egy Service Mesh Azure Kubernetes-szolgáltatásbeli (ak-) fürtön való létrehozásához
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683919"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>A konzul telepítése és használata az Azure Kubernetes szolgáltatásban (ak)

A [konzul][consul-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtben található összes szolgáltatásra kiterjedő funkciókat biztosít. Ezek a szolgáltatások közé tartoznak a szolgáltatások felderítése, az állapot-ellenőrzés, a szolgáltatások szegmentálása és a megfigyelés. További információ a Konzulról: a [konzul?][consul-docs-concepts] dokumentációja.

Ez a cikk bemutatja, hogyan telepítheti a konzult. A konzuli összetevők egy Kubernetes-fürtre vannak telepítve az AK-ban.

> [!NOTE]
> Ezek az utasítások a konzul verzióját `1.6.0` használják, és legalább Helm-verziót használnak `2.14.2` .
>
> A konzuli `1.6.x` kiadásokat Kubernetes-verziókon lehet futtatni `1.13+` . További konzuli verziók a [GitHub-konzuli][consul-github-releases] kiadásokban és az egyes kiadásokra vonatkozó információk a [Consul-kibocsátási megjegyzésekben][consul-release-notes]című témakörben találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A konzuli összetevők telepítése az AK-on
> * A konzul telepítésének ellenőrzése
> * Konzul eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.13` és újabb, KUBERNETES RBAC engedélyezve), és létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket. Győződjön meg arról, hogy a fürt legalább 3 csomóponttal rendelkezik a Linux-csomópontok készletében.

Az utasítások követéséhez és a konzul telepítéséhez a [Helm][helm] szükséges. Javasoljuk, hogy a legújabb stabil verziót megfelelően telepítse és konfigurálja a fürtben. Ha segítségre van szüksége a Helm telepítéséhez, tekintse meg az [AK Helm telepítési útmutatóját][helm-install]. Az összes konzuli hüvelyt is be kell ütemezni a Linux-csomópontokon történő futtatásra.

Ez a cikk a konzuli telepítési útmutatót több különálló lépésben választja el. A végeredmény ugyanaz, mint a hivatalos konzul telepítési [útmutatója][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>A konzuli összetevők telepítése az AK-on

Kezdjük `v0.10.0` a Consul Helm diagram verziójának letöltésével. A diagram ezen verziója tartalmazza a konzul verzióját `1.6.0` .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

A Helm és a letöltött `consul-helm` diagram használatával telepítse a konzuli összetevőket a `consul` névtérbe az AK-fürtben. 

> [!NOTE]
> **Telepítési lehetőségek**
> 
> A telepítés részeként a következő beállításokat használjuk:
> - `connectInject.enabled=true` – engedélyezi, hogy a proxyk a hüvelybe legyenek injektálva
> - `client.enabled=true` – a konzuli ügyfelek futtatásának engedélyezése minden csomóponton
> - `client.grpc=true` – gRPC-figyelő engedélyezése a connectInject
> - `syncCatalog.enabled=true` – a Kubernetes és a konzuli szolgáltatások szinkronizálása
>
> **Csomópont-választók**
>
> A konzulnak jelenleg Linux-csomópontokon kell futnia. Ha a fürtben Windows Server-csomópontok vannak, akkor győződjön meg arról, hogy a konzuli hüvelyek csak Linux-csomópontokon futnak. A csomópont- [választókkal][kubernetes-node-selectors] gondoskodunk róla, hogy a hüvelyek a megfelelő csomópontokra legyenek ütemezve.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

A `Consul` Helm diagram több objektumot helyez üzembe. A listát a fenti parancs kimenetében tekintheti meg `helm install` . A konzuli összetevők üzembe helyezése a fürt-környezettől függően 3 percet is igénybe vehet.

Ekkor központilag telepítette a konzult az AK-fürtbe. A konzul sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a konzul telepítésének ellenőrzéséhez.

## <a name="validate-the-consul-installation"></a>A konzul telepítésének ellenőrzése

Győződjön meg arról, hogy az erőforrások létrehozása sikeresen megtörtént. A [kubectl Get SVC][kubectl-get] és a [kubectl Get Pod][kubectl-get] parancsok használatával kérdezheti le a `consul` névteret, ahol a konzuli összetevőket a parancs telepítette `helm install` :

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

A következő példa kimenetében láthatók a szolgáltatások és a hüvelyek (a Linux-csomópontokon ütemezettek), amelyeknek mostantól futniuk kell:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Az összes hüvelynek állapota a következő: `Running` . Ha a hüvelye nem rendelkezik ezekkel az állapotokkal, várjon egy percet vagy kettőt, amíg meg nem történik. Ha bármelyik hüvely hibát jelez, használja a [kubectl leírását a pod][kubectl-describe] paranccsal a kimenet és az állapot áttekintéséhez.

## <a name="accessing-the-consul-ui"></a>A konzul felhasználói felületének elérése

A konzul felhasználói felülete telepítve lett a fenti beállításokban, és felhasználói felületen alapuló konfigurációt biztosít a konzul számára. A konzul felhasználói felülete nem nyilvánosan elérhető külső IP-címen keresztül. A konzul felhasználói felületének eléréséhez használja a [kubectl Port-Forward][kubectl-port-forward] parancsot. Ez a parancs biztonságos kapcsolatot hoz létre az ügyfélszámítógép és a megfelelő Pod-kapcsolat között az AK-fürtben.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Most megnyithat egy böngészőt, és rámutathat a `http://localhost:8080/ui` konzul felhasználói felületének megnyitására. A felhasználói felület megnyitásakor a következőnek kell megjelennie:

![Konzul felhasználói felülete](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Konzul eltávolítása az AK-ból

> [!WARNING]
> Ha egy futó rendszerből törli a konzult, akkor a szolgáltatások közötti forgalomhoz kapcsolódó problémák léphetnek fel. A folytatás előtt győződjön meg arról, hogy a rendszernek megfelelő jogosultságokkal rendelkezik, hogy konzul nélkül is működjön.

### <a name="remove-consul-components-and-namespace"></a>A konzul összetevőinek és névterének eltávolítása

Ha el szeretné távolítani a konzult az AK-fürtből, használja a következő parancsokat. A `helm delete` parancsok el fogják távolítani a `consul` diagramot, és a `kubectl delete namespace` parancs eltávolítja a `consul` névteret.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Következő lépések

A konzul további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos konzuli cikkeket:

- [A Consul-Helm telepítési útmutatója][consul-install-k8]
- [A konzul-Helm telepítési lehetőségei][consul-install-helm-options]

A következő esetekben is elvégezheti a további forgatókönyvek használatát:

- [Konzuli példa alkalmazása][consul-app-example]
- [Konzuli Kubernetes hivatkozási architektúrája][consul-reference]
- [Konzuli háló átjárók][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
