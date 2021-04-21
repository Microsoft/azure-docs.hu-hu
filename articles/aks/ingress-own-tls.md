---
title: TLS-tanúsítványok használata bejövő forgalomhoz
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepíthet és konfigurálhet olyan NGINX bejövő forgalomvezérlőt, amely saját tanúsítványokat használ egy Azure Kubernetes Service -fürtben.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: d3d554fbbbbb16910b76e7eb56afd13c436f8cf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779938"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>HTTPS bejövőforgalom-vezérlő létrehozása, és saját TLS-tanúsítványok használata az Azure Kubernetes Service-ben (AKS)

A bejövőforgalom-vezérlő olyan szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-leválasztást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásaival konfigurálhatja az egyes Kubernetes-szolgáltatások bejövő szabályait és útvonalait. A bejövőforgalom-vezérlő és a bejövő szabályok használatával egyetlen IP-cím használatával irányítható a forgalom egy Kubernetes-fürt több szolgáltatására.

Ez a cikk bemutatja, hogyan helyezheti üzembe az [NGINX][nginx-ingress] bejövő vezérlőt egy Azure Kubernetes Service (AKS) fürtben. Saját tanúsítványokat hoz létre, és létrehoz egy Kubernetes-titkos secret-et a bejövő útvonalhoz. Végül két alkalmazás fut az AKS-fürtben, amelyek mind egyetlen IP-címen keresztül érhetők el.

További lehetőségek:

- [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózatot és IP-címet használó bejövő vezérlő létrehozása][aks-ingress-internal]
- Hozzon létre egy bejövő vezérlőt, amely a Let's Encrypt használatával automatikusan hoz létre TLS-tanúsítványokat dinamikus nyilvános [IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a [Helm 3 használatával][helm] telepíti az NGINX bejövő forgalomvezérlőt. Győződjön meg arról, hogy a Helm legújabb kiadását használja, és hozzáféréssel rendelkezik a *ingress-nginx* Helm-adattárhoz. A frissítésre vonatkozó utasításokért lásd a [Helm telepítési docs-ját.][helm-install] A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: Alkalmazások telepítése a [Helm használatával az Azure Kubernetes Service (AKS) használatával.][use-helm]

Ehhez a cikkhez az Azure CLI 2.0.64-es vagy újabb verziójára is szükség van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő vezérlő létrehozása

A bejövő vezérlő létrehozásához a használatával `Helm` telepítse az *nginx-ingress vezérlőt.* A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő vezérlő replikái futtatásának teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> Az alábbi példa létrehoz egy Kubernetes-névteret a *ingress-basic nevű bejövő forgalom erőforrásaihoz.* Szükség szerint adjon meg egy névteret a saját környezetéhez. Ha az AKS-fürt nincs engedélyezve a Kubernetes RBAC-n, adja hozzá a parancsot a `--set rbac.create=false` Helm-parancsokhoz.

> [!TIP]
> Ha engedélyezni szeretné [][client-source-ip] az ügyfélforrás IP-címének megőrzését a fürtben található tárolókra irányuló kérések számára, adja hozzá a parancsot a `--set controller.service.externalTrafficPolicy=Local` Helm telepítési parancshoz. Az ügyfél forrás IP-címét a kérés fejlécében, az *X-Forwarded-For alatt tárolja a rendszer.* Ha olyan bejövő forgalomvezérlőt használ, amelynél engedélyezve van az ügyfélforrás IP-megőrzése, a TLS-áthaladás nem fog működni.

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

A telepítés során létrejön egy azure-beli nyilvános IP-cím a bejövő forgalomvezérlőhöz. Ez a nyilvános IP-cím statikus a bejövő forgalomvezérlő élettartama során. Ha törli a bejövő forgalomvezérlőt, a nyilvános IP-cím hozzárendelése elveszik. Ha ezután létrehoz egy további bejövő forgalomvezérlőt, a rendszer egy új nyilvános IP-címet rendel hozzá. Ha meg szeretné őrizni a nyilvános IP-cím használatát, létrehozhat egy bejövő vezérlőt statikus nyilvános [IP-címmel.][aks-ingress-static-tls]

A nyilvános IP-cím lekért használhatja az `kubectl get service` parancsot.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Az IP-cím szolgáltatáshoz való hozzárendelése néhány percet vesz igénybe.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Jegyezze fel ezt a nyilvános IP-címet, mivel az utolsó lépésben az üzembe helyezés teszteléséhez használt.

Még nem hoztak létre bejövő szabályokat. Ha a nyilvános IP-címre keres, az NGINX bejövő forgalomvezérlő alapértelmezett 404-es oldala jelenik meg.

## <a name="generate-tls-certificates"></a>TLS-tanúsítványok létrehozása

Ehhez a cikkhez hozzunk létre egy önaírt tanúsítványt a `openssl` következővel: . Éles környezetben megbízható, aláírt tanúsítványt kell igényelni egy szolgáltatón vagy a saját hitelesítésszolgáltatóján (CA) keresztül. A következő lépésben létrehoz egy Kubernetes *Titkos* kulcsot az OpenSSL által létrehozott TLS-tanúsítvány és titkos kulcs használatával.

Az alábbi példa egy 2048 bites RSA X509-tanúsítványt hoz létre, amely 365 napig érvényes *aks-ingress-tls.crt névvel.* A titkos kulcsfájl neve *aks-ingress-tls.key.* A Kubernetes TLS titkos adatokat mindkét fájlhoz meg kell követelni.

Ez a cikk a demo.azure.com *általános* nevével működik, és nem szükséges módosítani. Éles környezetben adja meg a saját szervezeti értékeit a `-subj` paraméterhez:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes titkos kódjának létrehozása a TLS-tanúsítványhoz

Ahhoz, hogy a Kubernetes használni tudja a TLS-tanúsítványt és a titkos kulcsot a bejövő forgalomvezérlőhöz, létre kell hoznia és használnia kell egy titkos kulcsot. A titkos kulcs egyszer van definiálva, és az előző lépésben létrehozott tanúsítványt és kulcsfájlt használja. Ezután hivatkozhat erre a titkos referenciára a bejövő útvonalak meghatározásakor.

Az alábbi példa létrehoz egy *aks-ingress-tls nevű titkos adatokat:*

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Konfigurálva van egy bejövő vezérlő és egy titkos tanúsítvány a tanúsítvánnyal. Most futtasunk két bemutatóalkalmazást az AKS-fürtben. Ebben a példában a Helm egy egyszerű "Hello world" alkalmazás két példányának üzembe helyezésére használható.

A bejövő forgalom vezérlőinek a művelet közbeni futtatásához futtason két bemutatóalkalmazást az AKS-fürtben. Ebben a példában a használatával egy egyszerű Hello World alkalmazás két `kubectl apply` *példányát helyezheti üzembe.*

Hozzon létre *egy aks-helloworld.yaml fájlt,* és másolja be az alábbi YAML-példát:

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

Hozzon létre *egy ingress-demo.yaml fájlt,* és másolja be az alábbi YAML-példát:

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

Futtassa a két bemutatóalkalmazást a `kubectl apply` használatával:

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Bejövő útvonal létrehozása

Most már mindkét alkalmazás a Kubernetes-fürtön fut, de egy típusú szolgáltatással vannak `ClusterIP` konfigurálva. Ezért az alkalmazások nem érhetők el az internetről. Ahhoz, hogy nyilvánosan elérhetővé tegye őket, hozzon létre egy Kubernetes bejövő forgalomra vonatkozó erőforrást. A bejövőforgalom-erőforrás konfigurálja a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a cím felé forgalmat a nevű `https://demo.azure.com/` szolgáltatáshoz `aks-helloworld` irányítjuk. A címre való `https://demo.azure.com/hello-world-two` forgalom a szolgáltatáshoz `ingress-demo` lesz irányítva. Ebben a cikkben nem kell módosítania a bemutató állomásneveket. Éles környezetben adja meg a tanúsítványkérelem és a generálási folyamat részeként megadott neveket.

> [!TIP]
> Ha a tanúsítványkérelem folyamata során megadott állomásnév, a CN-név nem egyezik a bejövő forgalom útvonalán meghatározott gazdagép nevével, a bejövő forgalom vezérlője megjelenít egy hamis *Kubernetes bejövő* forgalomvezérlő-figyelmeztetést. Győződjön meg arról, hogy a tanúsítvány és a bejövő forgalom állomásnevének neve megegyezik.

A *tls szakasz* arra utasítja a bejövő útvonalat, hogy az *aks-ingress-tls* nevű titkos secretet használja a gazdagép *demo.azure.com.* Éles környezetben is adja meg a saját állomáscímét.

Hozzon létre egy nevű `hello-world-ingress.yaml` fájlt, és másolja be az alábbi YAML-példát.

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

Hozza létre a bejövő forgalom erőforrását az `kubectl apply -f hello-world-ingress.yaml` paranccsal.

```console
kubectl apply -f hello-world-ingress.yaml
```

A példakimenet azt mutatja, hogy a bejövő erőforrás létrejött.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>A bejövő forgalom konfigurációjának tesztelése

Ha a tanúsítványokat  a hamis demo.azure.com gazdagépen tesztelni, használja a `curl` *--resolve* paramétert, és adja meg. Ezzel a paraméterrel leképezheti *a demo.azure.com* nevét a bejövő vezérlő nyilvános IP-címére. Adja meg a saját bejövő vezérlő nyilvános IP-címét az alábbi példában látható módon:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

A cím nem adott meg további elérési utat, így a bejövő forgalom vezérlője alapértelmezés szerint az útvonalat */* használja. A rendszer visszaadja az első bemutatóalkalmazást, ahogyan az a következő rövid példakimenetben látható:

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

A *parancs -v* paramétere részletes információkat ad `curl` vissza, beleértve a kapott TLS-tanúsítványt is. A curl kimenetének félúton ellenőrizheti, hogy a saját TLS-tanúsítványát használta-e. A *-k* paraméter továbbra is betölti az oldalt annak ellenére, hogy önaírt tanúsítványt használ. Az alábbi példa azt mutatja, hogy a *kiállító: CN=demo.azure.com; O=aks-ingress-tls* tanúsítványt használtunk:

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

Most adja *hozzá a /hello-world-two útvonalat* a címhez, `https://demo.azure.com/hello-world-two` például: . A rendszer az egyéni címmel egy második bemutatóalkalmazást ad vissza, ahogyan az a következő rövid példakimenetben látható:

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

Ez a cikk a Helm segítségével telepítette a bejövő forgalom összetevőit és a mintaalkalmazásokat. Helm-diagram üzembe helyezésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások lehetnek podok, üzemelő példányok és szolgáltatások. Az erőforrások törléséhez törölheti a teljes névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja az `kubectl delete` parancsot, és adja meg a névtér nevét. A névtér összes erőforrása törölve lesz.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások törlése egyenként

Másik lehetőségként részletesebben is törölheti a létrehozott egyes erőforrásokat. Listába sorolja a Helm-kiadásokat az `helm list` paranccsal. 

```console
helm list --namespace ingress-basic
```

Keresse meg az *nginx-ingress nevű* diagramot az alábbi kimenetben látható módon:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Távolítsa el a kiadásokat az `helm uninstall` paranccsal. 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Az alábbi példa eltávolítja az NGINX bejövő forgalom üzembe helyezését.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Ezután távolítsa el a két mintaalkalmazást:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Távolítsa el azt a bejövő forgalmi útvonalat, amely a mintaalkalmazások felé irányította a forgalmat:

```console
kubectl delete -f hello-world-ingress.yaml
```

Törölje a titkos tanúsítványt:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Végül törölheti magát a névteret. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AKS néhány külső összetevőt is tartalmazott. Az összetevőkkel kapcsolatos további információkért tekintse meg a következő projektoldalakat:

- [Helm CLI][helm-cli]
- [NGINX bejövő forgalomvezérlő][nginx-ingress]

További lehetőségek:

- [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózatot és IP-címet használó bejövő vezérlő létrehozása][aks-ingress-internal]
- Hozzon létre egy bejövő vezérlőt, amely a Let's Encrypt használatával automatikusan hoz létre TLS-tanúsítványokat dinamikus nyilvános [IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
