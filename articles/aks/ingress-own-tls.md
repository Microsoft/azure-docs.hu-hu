---
title: TLS-tanúsítványok használata a bejövő forgalomhoz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan telepíthet és konfigurálhat egy olyan NGINX bejövő adatkezelőt, amely saját tanúsítványokat használ egy Azure Kubernetes Service-(ak-) fürtben.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: ee196bd749ad5821a1855d4549b22698c724b3f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104926"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>HTTPS bejövőforgalom-vezérlő létrehozása, és saját TLS-tanúsítványok használata az Azure Kubernetes Service-ben (AKS)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az NGINX beléptetési [vezérlőt][nginx-ingress] egy Azure Kubernetes-szolgáltatási (ak-) fürtben. Saját tanúsítványokat hoz létre, és létrehoz egy Kubernetes titkot a bejövő útvonalakkal való használatra. Végül két alkalmazás fut az AK-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a [Helm 3][helm] használatával telepíti az NGINX beáramló vezérlőt. Győződjön meg arról, hogy a Helm legújabb kiadását használja, és hozzáférhet a *bejövő-Nginx Helm-* tárházhoz. A frissítési utasításokért tekintse meg a [Helm install docs][helm-install]című témakört. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

A bejövő adatkezelő létrehozásához használja az `Helm` *Nginx-behatolások* telepítését. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *– alapszintű* forgalomhoz. Szükség szerint adja meg a saját környezetének névterét. Ha az AK-fürt nincs engedélyezve a Kubernetes RBAC, adja hozzá `--set rbac.create=false` a parancsot a Helm parancshoz.

> [!TIP]
> Ha engedélyezni szeretné az [ügyfél forrásának IP-megőrzését][client-source-ip] a fürtben lévő tárolók kéréseire, adja hozzá `--set controller.service.externalTrafficPolicy=Local` a parancsot a Helm install parancshoz. Az ügyfél forrásának IP-címét a kérelem fejlécében tárolja a rendszer az *X által továbbított – esetében*. Ha az ügyfél-forrás IP-megtartást engedélyező bejövő vezérlőt használ, a TLS-áteresztő nem fog működni.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A telepítés során létrejön egy Azure nyilvános IP-cím a beléptetési vezérlőhöz. Ez a nyilvános IP-cím statikus a bejövő vezérlő élettartama szempontjából. Ha törli a beáramló vezérlőt, a nyilvános IP-cím hozzárendelés elvész. Ha ezután létrehoz egy további bejövő vezérlőt, a rendszer egy új nyilvános IP-címet rendel hozzá. Ha meg szeretné őrizni a nyilvános IP-cím használatát, helyette [létrehozhat egy statikus nyilvános IP-címmel rendelkező bejövő vezérlőt][aks-ingress-static-tls]is.

A nyilvános IP-cím lekéréséhez használja az `kubectl get service` parancsot.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Néhány percet vesz igénybe, hogy az IP-cím hozzá legyen rendelve a szolgáltatáshoz.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Jegyezze fel ezt a nyilvános IP-címet, mert az utolsó lépésben az üzemelő példány tesztelésére szolgál.

Még nem jöttek létre Bejövő szabályok. Ha megkeresi a nyilvános IP-címet, megjelenik az NGINX bejövő vezérlő alapértelmezett 404 lapja.

## <a name="generate-tls-certificates"></a>TLS-tanúsítványok előállítása

Ehhez a cikkhez hozzon ki egy önaláírt tanúsítványt a következővel: `openssl` . Éles használatra egy megbízható, aláírt tanúsítványt kell igényelnie a szolgáltatón vagy a saját hitelesítésszolgáltatóján keresztül. A következő lépésben egy Kubernetes- *titkot* hoz létre a TLS-tanúsítvány és az OpenSSL által generált titkos kulcs használatával.

Az alábbi példa egy 2048 bites RSA X509-tanúsítványt hoz létre, amely érvényes a *AKS-ingress-TLS. CRT* nevű 365 napig. A titkos kulcs fájljának neve *AKS-ingress-TLS. Key*. A Kubernetes TLS-titokhoz mindkét fájlnak szüksége van.

Ez a cikk a *demo.Azure.com* köznapi nevével működik együtt, ezért nem szükséges módosítani. Éles használatra a paraméterhez adja meg a saját szervezeti értékeit `-subj` :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes titkos kódjának létrehozása a TLS-tanúsítványhoz

Ha engedélyezni szeretné, hogy a Kubernetes a bejövő vezérlőhöz tartozó TLS-tanúsítványt és titkos kulcsot használja, hozzon létre és használjon titkos kódot. A titkos kulcs egyszer van definiálva, és az előző lépésben létrehozott tanúsítványt és kulcsot használja. Ezt a titkot a bejövő útvonalak meghatározásakor kell megadnia.

A következő példa létrehoz egy titkos nevet a (z)- *beáramló-TLS* számára:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Be van állítva egy bejövő vezérlő és egy titkos tanúsítvány a tanúsítvánnyal. Most futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában a Helm egy egyszerű "Hello World" alkalmazás két példányának üzembe helyezésére szolgál.

A beáramló vezérlő működés közbeni megtekintéséhez futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában `kubectl apply` egy egyszerű *Helló World* -alkalmazás két példányának üzembe helyezésére használható.

Hozzon létre egy *AK-HelloWorld. YAML* fájlt, és másolja a következő PÉLDÁBAN szereplő YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Hozzon létre egy *beáramló-bemutató. YAML* fájlt, és másolja a következő PÉLDÁBAN szereplő YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Futtassa a két bemutató alkalmazást a használatával `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Bejövő forgalom útvonalának létrehozása

Mindkét alkalmazás már fut a Kubernetes-fürtön, azonban egy típusú szolgáltatással vannak konfigurálva `ClusterIP` . Így az alkalmazások nem érhetők el az internetről. A nyilvánosan elérhetővé tételéhez hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a címnek való adatforgalom `https://demo.azure.com/` a nevű szolgáltatáshoz lesz irányítva `aks-helloworld` . A `https://demo.azure.com/hello-world-two` rendszer átirányítja a címnek a szolgáltatás felé irányuló forgalmat `ingress-demo` . Ehhez a cikkhez nem kell módosítania ezeket a bemutató-állomásnevek nevét. Éles használatra a tanúsítványkérelem és a létrehozási folyamat részeként megadott neveket adja meg.

> [!TIP]
> Ha a tanúsítványkérelem folyamata során megadott állomásnév, a CN neve nem egyezik a bejövő forgalom útvonalán megadott gazdagéptel, akkor a bejövő vezérlő a Kubernetes beléptetési *vezérlő hamis tanúsítványára* vonatkozó figyelmeztetést jelenít meg. Győződjön meg arról, hogy a tanúsítvány és a bejövő útvonal-gazdagép neve egyezik.

A *TLS* szakasz azt mutatja be, hogy a bejövő forgalom útvonala a gazdagép *demo.Azure.com* tartozó, a (z) *-* ba tartozó titkos kulcs neve. Az éles használat érdekében adja meg a saját gazdagépének a címeit.

Hozzon létre egy nevű fájlt `hello-world-ingress.yaml` , és másolja a következő PÉLDÁBAN YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Hozza létre a bejövő erőforrásokat a `kubectl apply -f hello-world-ingress.yaml` parancs használatával.

```console
kubectl apply -f hello-world-ingress.yaml
```

A példa kimenete a bejövő erőforrások létrejöttét mutatja.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>A behatolási konfiguráció tesztelése

A tanúsítványoknak a hamis *demo.Azure.com* -gazdagépen való teszteléséhez használja `curl` a és a *--feloldás* paramétert. Ezzel a paraméterrel leképezheti a *demo.Azure.com* nevét a bejövő adatkezelő nyilvános IP-címére. Adja meg saját bejövő adatkezelője nyilvános IP-címét, az alábbi példában látható módon:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Nincs további elérési út megadva a címnek, így a bejövő vezérlő alapértelmezett értéke az */* útvonal. A rendszer az első bemutató alkalmazást adja vissza, ahogy az a következő tömörített példában is látható:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

A parancs kimenetében található *-v* paraméter `curl` részletes információkat jelenít meg, beleértve a kapott TLS-tanúsítványt is. A curl-kimenet felé fordítva ellenőrizheti, hogy a saját TLS-tanúsítványát használták-e. A *-k* paraméter folytatja a lap betöltését annak ellenére, hogy önaláírt tanúsítványt használunk. Az alábbi példa azt mutatja, hogy a *kiállító: CN = demo. Azure. com; O = AK – bejövő forgalom – a TLS* -tanúsítványt használták:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Most adja hozzá a */Hello-World-Two* elérési útját a címnek, például: `https://demo.azure.com/hello-world-two` . A rendszer az egyéni címmel rendelkező második bemutató alkalmazást adja vissza, ahogy az az alábbi tömörített példában is látható:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a beáramló összetevők és a minta alkalmazások telepítésére szolgál. Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ilyen erőforrások például a hüvelyek, az üzembe helyezések és a szolgáltatások. Ezen erőforrások törléséhez törölheti a teljes minta névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja a `kubectl delete` parancsot, és adja meg a névtér nevét. A rendszer törli a névtérben lévő összes erőforrást.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások egyenkénti törlése

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Sorolja fel az paranccsal a Helm kiadásait `helm list` . 

```console
helm list --namespace ingress-basic
```

Keresse meg az *Nginx-behatolás* nevű diagramot az alábbi példában látható módon:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Távolítsa el a kiadásokat a `helm uninstall` paranccsal. 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Az alábbi példa eltávolítja az NGINX bejövő forgalom üzembe helyezését.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Ezután távolítsa el a két minta alkalmazást:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Távolítsa el a bejövő forgalom útvonalát, amely irányítja a forgalmat a mintául szolgáló alkalmazásokba:

```console
kubectl delete -f hello-world-ingress.yaml
```

A tanúsítvány titkos kulcsának törlése:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Végezetül törölheti saját maga is a névteret. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk néhány külső összetevőt tartalmaz az ak-nak. Ha többet szeretne megtudni ezekről az összetevőkről, tekintse meg a következő Project-lapokat:

- [Helm parancssori felület][helm-cli]
- [NGINX bejövő adatkezelő][nginx-ingress]

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
