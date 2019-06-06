---
title: Hozzon létre egy alapszintű bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan telepítése és konfigurálása egy alapszintű NGINX bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)-fürtben.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: b0dfe69a77d236e7a811ca5c7407428327c62ff3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430996"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Hozzon létre egy bejövőforgalom-vezérlőt az Azure Kubernetes Service (AKS)

Bejövőforgalom-vezérlőjéhez olyan szoftver, amely biztosítja a fordított proxy, konfigurálható forgalom-útválasztást és a TLS-lezárást biztosít Kubernetes-szolgáltatás. Kubernetes bejövő erőforrások segítségével konfigurálhatja a bejövő szabályok és útvonalak a Kubernetes-szolgáltatás. A bejövőforgalom-vezérlőt, és a bejövő szabályok használatával az egyetlen IP-cím irányíthatja a forgalmat több szolgáltatást a Kubernetes-fürtben használható.

Ez a cikk bemutatja, hogyan helyezhet üzembe a [NGINX bejövőforgalom-vezérlőjéhez] [ nginx-ingress] Azure Kubernetes Service (AKS)-fürtben. Két alkalmazás futtatása a az AKS-fürtöt, amelyek mindegyike érhető el az egyetlen IP-címen keresztül.

További lehetőségek:

- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- [Hozzon létre egy saját TLS-tanúsítványokat használ a bejövőforgalom-vezérlőt][aks-ingress-own-tls]
- Hozzon létre egy bejövőforgalom-vezérlőt használó hozzunk titkosítása automatikusan létrehozni a TLS-tanúsítványok [dinamikus nyilvános IP-címmel rendelkező] [ aks-ingress-tls] vagy [egy statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a Helm használatával az NGINX bejövőforgalom-vezérlőt, a tanúsítvány-kezelő és a egy mintául szolgáló webalkalmazás telepítése. Szüksége lesz a Helm belül az AKS-fürt inicializálva, és a tiller valóban szolgáltatásfiók használatával. Konfigurálása és a Helm használatával további információkért lásd: [telepíthet alkalmazásokat a Helm használatával az Azure Kubernetes Service (AKS)][use-helm].

Ez a cikk is szükséges, hogy futnak-e az Azure CLI 2.0.64 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Hozzon létre egy bejövőforgalom-vezérlőt

A bejövőforgalom-vezérlőjéhez létrehozásához használja `Helm` telepítéséhez *nginx-belépő*. Hozzáadott redundancia céljából két replika az NGINX bejövő vezérlők telepítik a `--set controller.replicaCount` paraméter. Teljes körűen kihasználhatják a bejövőforgalom-vezérlőjéhez replikáin fut, ellenőrizze, egynél több csomópont szerepel az AKS-fürt.

A bejövőforgalom-vezérlőt is kell ütemezni egy Linux-csomóponton. A Windows Server-csomópontok (jelenleg előzetes verzióban érhető el az aks-ben) ne futtassa a bejövőforgalom-vezérlőt. Egy csomópont-választó használatával van megadva a `--set nodeSelector` állapítható meg, hogy a Kubernetes-ütemezőt az NGINX bejövőforgalom-vezérlőt a Linux-alapú csomóponton futó paraméter.

> [!TIP]
> Az alábbi példa létrehoz egy Kubernetes-névtér nevű bejövő erőforrások *bejövő – alapszintű*. Adjon meg egy névteret a saját környezetben, igény szerint. Ha az AKS-fürt nem RBAC engedélyezve, vegye fel `--set rbac.create=false` a Helm parancsokhoz.

> [!TIP]
> Ha engedélyezni szeretné [ügyfél forrás IP-megőrzését] [ client-source-ip] a fürtben lévő tárolók kérelmeket, vegye fel az `--set controller.service.externalTrafficPolicy=Local` parancs a Helm telepítse. Az ügyfél forrásának IP tárolja a kérelem fejléce alatt *X – továbbított – a*. Az ügyfél forrás IP megőrzését engedélyezve bejövőforgalom-vezérlőjéhez használatakor SSL átmenő nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A terheléselosztó Kubernetes szolgáltatás az NGINX bejövőforgalom-vezérlőjéhez hoz létre, amikor a dinamikus nyilvános IP-cím van hozzárendelve, az alábbi példa kimenetében látható módon:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Bejövő szabályok már hozott létre, így az NGINX bejövőforgalom-vezérlőjéhez tartozó alapértelmezett 404-es lap is megjelenik, ha a belső IP-címe. Bejövő szabályok a következő lépések vannak konfigurálva.

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Szeretné látni működés közben bejövőforgalom-vezérlőt, futtassunk két bemutató alkalmazás az AKS-fürt található. Ebben a példában a Helm szolgál egy egyszerű "Hello world" alkalmazás két példány üzembe helyezéséhez.

A minta Helm-diagramok telepítése előtt vegye fel az Azure-minták tárhelyet a Helm-környezetben a következő:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Az első bemutató alkalmazás létrehozása egy Helm-diagramot a következő paranccsal:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Egy második példányt a bemutató alkalmazás telepítése. A második példány, adja meg az új címet, hogy a két alkalmazás vizuálisan elkülönülnek. Is adjon meg egy egyedi szolgáltatásnevet:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Bejövő útvonal létrehozása

Mindkét alkalmazás most már a Kubernetes-fürtöt futtat. Irányíthatja a forgalmat mindegyik alkalmazás, hozzon létre egy Kubernetes bejövő erőforrást. A bejövő forgalom erőforrás konfigurálja a szabályokat, amelyek a két alkalmazás egyik irányíthatja a forgalmat.

A következő példában a forgalmat a címre `http://40.117.74.8/` irányítja a rendszer a szolgáltatás nevű `aks-helloworld`. A cím forgalmát `http://40.117.74.8/hello-world-two` irányítja a rendszer a `ingress-demo` szolgáltatás.

Hozzon létre egy fájlt `hello-world-ingress.yaml` , és másolja az alábbi példában YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Létrehozhatja a bejövő forgalom erőforrás a `kubectl apply -f hello-world-ingress.yaml` parancsot.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>A bejövőforgalom-vezérlőjéhez tesztelése

Tesztelheti a útvonalait a bejövőforgalom-vezérlőt, keresse meg a két alkalmazás. Például az NGINX bejövőforgalom-vezérlőt, IP-címét egy webböngészőben nyissa meg *http://40.117.74.8* . Az első bemutató alkalmazás megjelenik a webböngészőben az alábbi példában látható módon:

![Első alkalmazását a bejövőforgalom-vezérlőjéhez mögött fut.](media/ingress-basic/app-one.png)

Most adja hozzá a */hello-world-two* elérési útját az IP-cím, például *http://40.117.74.8/hello-world-two* . A második bemutató alkalmazás és az egyéni cím jelenik meg:

![A bejövőforgalom-vezérlőjéhez mögött futó második alkalmazás](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a bejövő forgalom összetevők és a mintaalkalmazások telepítése Helm használja. Amikor telepít egy Helm-diagram, egy Kubernetes-erőforrások száma jönnek létre. Ezeket az erőforrásokat podok, központi telepítések és szolgáltatásokat tartalmazza. Ezek az erőforrások törléséhez törölheti vagy a teljes minta névteret, vagy egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta-névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja a `kubectl delete` parancsot, majd írja be a névtér nevét. A névtér összes erőforrása törlődik.

```console
kubectl delete namespace ingress-basic
```

Ezt követően távolítsa el az AKS hello world alkalmazás a Helm-adattárat:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Külön-külön törölje az erőforrást

Azt is megteheti a részletesebb megközelítést, ami törli az egyes létrehozott erőforrásokat. A Helm-kiadások listája a `helm list` parancsot. Keresse meg a diagramok nevű *nginx-bejövő* és *aks-helloworld*, ahogy az alábbi példa kimenetében látható:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

A kiadások, és törölje a `helm delete` parancsot. Az alábbi példában az NGINX bejövő üzembe helyezés és a két minta AKS hello world alkalmazás törlése.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
```

Ezután távolítsa el az AKS hello world alkalmazás a Helm-adattárat:

```console
helm repo remove azure-samples
```

A bejövő útvonal, amely átirányítja a forgalmat a mintaalkalmazások eltávolítása:

```console
kubectl delete -f hello-world-ingress.yaml
```

Végül törölheti a maga névtér. Használja a `kubectl delete` parancsot, majd írja be a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>További lépések

Ez a cikk tartalmaz néhány külső összetevők az aks-ben. Ezek az összetevők kapcsolatos további információkért tekintse meg a következő projekt oldalakat:

- [Helm CLI][helm-cli]
- [Az NGINX bejövőforgalom-vezérlőt][nginx-ingress]

További lehetőségek:

- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- [Hozzon létre egy saját TLS-tanúsítványokat használ a bejövőforgalom-vezérlőt][aks-ingress-own-tls]
- Hozzon létre egy bejövőforgalom-vezérlőt használó hozzunk titkosítása automatikusan létrehozni a TLS-tanúsítványok [dinamikus nyilvános IP-címmel rendelkező] [ aks-ingress-tls] vagy [egy statikus nyilvános IP-címmel][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
