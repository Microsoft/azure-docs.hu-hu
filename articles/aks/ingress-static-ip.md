---
title: Bejövő forgalomvezérlő használata statikus IP-címmel
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepíthet és konfigurálhatja az NGINX bejövő forgalomvezérlőt statikus nyilvános IP-címmel egy Azure Kubernetes Service (AKS-) fürtben.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779830"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-címmel rendelkező bejövőforgalom-vezérlő létrehozása az Azure Kubernetes Service-ben (AKS)

A bejövőforgalom-vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-leválasztást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályának és útvonalának konfigurálására használhatók. A bejövőforgalom-vezérlő és a bejövő szabályok használatával egyetlen IP-cím használatával irányítható a forgalom egy Kubernetes-fürt több szolgáltatására.

Ez a cikk bemutatja, hogyan helyezheti üzembe az [NGINX][nginx-ingress] bejövő forgalomvezérlőt egy Azure Kubernetes Service (AKS) fürtben. A bejövő forgalom vezérlője statikus nyilvános IP-címmel van konfigurálva. A [tanúsítványkezelő projekt][cert-manager] a [Let's Encrypt-tanúsítványok][lets-encrypt] automatikus létrehozásához és konfigurálásához használható. Végül két alkalmazás fut az AKS-fürtön, amelyek mind egyetlen IP-címen érhetők el.

További lehetőségek:

- [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Saját TLS-tanúsítványokat használó bejövő vezérlő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy bejövő vezérlőt, amely a Let's Encrypt használatával automatikusan hoz létre TLS-tanúsítványokat dinamikus nyilvános IP-címmel][aks-ingress-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van egy meglévő AKS-fürt. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Ez a cikk [a Helm 3 használatával][helm] telepíti az NGINX bejövő forgalomvezérlőt és a tanúsítványkezelőt. Győződjön meg arról, hogy a Helm legújabb kiadását használja, és hozzáféréssel rendelkezik a *ingress-nginx* és *a jetstack* Helm-adattárakhoz. A frissítésre vonatkozó utasításokért tekintse meg a [Helm telepítési docs-ját.][helm-install] A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: Alkalmazások telepítése a [Helm használatával a Azure Kubernetes Service (AKS) használatával.][use-helm]

Ehhez a cikkhez az Azure CLI 2.0.64-es vagy újabb verziójára is szükség van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő vezérlő létrehozása

Alapértelmezés szerint egy NGINX bejövő forgalomvezérlő jön létre egy új nyilvános IP-cím-hozzárendeléssel. Ez a nyilvános IP-cím csak a bejövő forgalomvezérlő élettartama alatt statikus, és a vezérlő törlésekor és újra létrehozásakor elveszik. Gyakori konfigurációs követelmény, hogy az NGINX bejövő forgalomvezérlő egy meglévő statikus nyilvános IP-címet biztosítson. A statikus nyilvános IP-cím megmarad, ha a bejövő forgalomvezérlőt törlik. Ezzel a megközelítéssel a meglévő DNS-rekordokat és hálózati konfigurációkat konzisztens módon használhatja az alkalmazások teljes életciklusa során.

Ha statikus nyilvános IP-címet kell létrehoznia, először az [az aks show][az-aks-show] paranccsal szerezze be az AKS-fürt erőforráscsoport-nevét:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ezután hozzon létre egy nyilvános IP-címet a *statikus* kiosztási módszerrel az az [network public-ip create paranccsal.][az-network-public-ip-create] A következő példa létrehoz egy *myAKSPublicIP* nevű nyilvános IP-címet az AKS-fürt erőforráscsoportban, amelyet az előző lépésben szerzett be:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> A fenti parancsok létrehoznak egy IP-címet, amely az AKS-fürt törlésekor törlődik. Másik lehetőségként létrehozhat egy IP-címet egy másik erőforráscsoportban, amely az AKS-fürttől függetlenül kezelhető. Ha egy másik erőforráscsoportban hoz létre IP-címet, győződjön meg arról, hogy az AKS-fürt által használt fürtidentitás delegált engedélyekkel rendelkezik a másik erőforráscsoporthoz, például a Hálózati *közreműködőhöz.* További információ: Statikus nyilvános IP-cím és [DNS-címke használata az AKS-terheléselosztással.][aks-static-ip]

Most telepítse az *nginx-ingress diagramot* a Helm segítségével. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő forgalomvezérlő replikái futtatásának teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

Két további paramétert kell átadnia a Helm-kiadásnak, hogy a bejövő forgalom vezérlője értesülni fog mind a bejövő forgalomvezérlő szolgáltatáshoz lefoglalandó terheléselosztás statikus IP-címével, mind pedig a nyilvános IP-cím erőforrásra alkalmazott DNS-névcímkével. Ahhoz, hogy a HTTPS-tanúsítványok megfelelően működjön, a rendszer egy DNS-névcímkével konfigurálja a teljes tartománynevet a bejövő forgalomvezérlő IP-címére.

1. Adja hozzá a `--set controller.service.loadBalancerIP` paramétert. Adja meg az előző lépésben létrehozott saját nyilvános IP-címét.
1. Adja hozzá a `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` paramétert. Adjon meg egy DNS-névcímkét, amely az előző lépésben létrehozott nyilvános IP-címre lesz alkalmazva.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> Az alábbi példa létrehoz egy Kubernetes-névteret a *ingress-basic nevű bejövő forgalom erőforrásaihoz.* Szükség szerint adjon meg egy névteret a saját környezetéhez. Ha az AKS-fürt nincs engedélyezve a Kubernetes RBAC-n, adja hozzá a parancsot a `--set rbac.create=false` Helm-parancsokhoz.

> [!TIP]
> Ha engedélyezni szeretné [][client-source-ip] az ügyfélforrás IP-címének megőrzését a fürtben található tárolókra irányuló kérések számára, adja hozzá a parancsot a `--set controller.service.externalTrafficPolicy=Local` Helm telepítési parancshoz. Az ügyfél forrás IP-címét a kérés fejlécében, az *X-Forwarded-For alatt tárolja a rendszer.* Ha olyan bejövő forgalomvezérlőt használ, amelynél engedélyezve van az ügyfélforrás IP-megőrzése, a TLS-áthaladás nem fog működni.

Frissítse a következő szkriptet **a** bejövő vezérlő **IP-címével** és egy egyedi névvel, amit a teljes tartománynév előtaghoz szeretne használni.

> [!IMPORTANT]
> A parancs *futtatásakor* frissítenie STATIC_IP és *DNS_LABEL* a saját IP-címére és egyedi nevére.

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
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Amikor létrejön a Kubernetes terheléselosztási szolgáltatása az NGINX bejövő forgalomvezérlőhöz, a rendszer hozzárendeli a statikus IP-címet, ahogyan az alábbi kimenetben látható:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Még nem hoztak létre bejövő szabályokat, így a nyilvános IP-cím megkeresését az NGINX bejövő vezérlő alapértelmezett 404-es oldala jeleníti meg. A bejövő forgalomra vonatkozó szabályokat a következő lépésekben konfiguráljuk.

A DNS-névcímke alkalmazását a nyilvános IP-cím teljes tartománynevének lekérdezésével ellenőrizheti a következőképpen:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

A bejövő forgalomvezérlő most már elérhető az IP-címen vagy a teljes tartományon keresztül.

## <a name="install-cert-manager"></a>Tanúsítványkezelő telepítése

Az NGINX bejövőforgalom-vezérlő támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk bemutatja a [tanúsítványkezelőt,][cert-manager]amely automatikus Lets [Encrypt][lets-encrypt] tanúsítvány-generálás és felügyeleti funkciókat biztosít.

> [!NOTE]
> Ez a cikk a `staging` Let's Encrypt környezetét használja. Éles környezetben használja a és a et az `letsencrypt-prod` `https://acme-v02.api.letsencrypt.org/directory` erőforrás-definíciókban, valamint a Helm-diagram telepítésekor.

A tanúsítványkezelő vezérlő Kubernetes RBAC-kompatibilis fürtben való telepítéséhez használja a következő `helm install` parancsot:

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

A tanúsítványkezelő konfigurálásról a [cert-manager][cert-manager]projektben található további információ.

## <a name="create-a-ca-cluster-issuer"></a>Hitelesítésszolgáltatói fürtkiállító létrehozása

A tanúsítványok kiadása előtt a tanúsítványkezelőnek kiállító vagy [ClusterIssuer erőforrásra van][cert-manager-cluster-issuer] szüksége. [][cert-manager-issuer] Ezek a Kubernetes-erőforrások azonosak a funkciókban, azonban egyetlen névtérben működnek, és az `Issuer` `ClusterIssuer` összes névtérben működnek. További információért tekintse meg [a tanúsítványkezelő kiállítójának dokumentációját.][cert-manager-issuer]

Hozzon létre egy fürtkiállítót, például a következő `cluster-issuer.yaml` példajegyzéket használva. Frissítse az e-mail-címet a szervezet érvényes címével:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

A kiállító létrehozásához használja az `kubectl apply` parancsot.

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

A kimenetnek az alábbi példához hasonlónak kell lennie:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Konfigurálva van egy bejövő forgalomvezérlő és egy tanúsítványkezelő megoldás. Most futtasunk két bemutatóalkalmazást az AKS-fürtben. Ebben a példában a Helm egy egyszerű "Hello world" alkalmazás két példányának üzembe helyezésére használható.

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

A következő példában a cím felé forgalmat a nevű `https://demo-aks-ingress.eastus.cloudapp.azure.com/` szolgáltatáshoz `aks-helloworld` irányítjuk. A címre való `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` forgalom a szolgáltatáshoz `ingress-demo` lesz irányítva. Frissítse a *gazdagépeket* *és gazdagépeket* az előző lépésben létrehozott DNS-névre.

Hozzon létre egy nevű `hello-world-ingress.yaml` fájlt, és másolja be az alábbi YAML-példát.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Hozza létre a bejövő forgalom erőforrását az `kubectl apply` paranccsal.

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

A kimenetnek az alábbi példához hasonlónak kell lennie:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tanúsítványobjektum létrehozása

Ezután létre kell hoznunk egy tanúsítvány-erőforrást. A tanúsítványerőforrás határozza meg a kívánt X.509-tanúsítványt. További információ: [tanúsítványkezelői tanúsítványok.][cert-manager-certificates]

A tanúsítványkezelő valószínűleg automatikusan létrehozott egy tanúsítványobjektumot a bejövő forgalom-aláfok használatával, amelyet a rendszer a 0.2.2-es óta automatikusan telepít a tanúsítványkezelővel. További információért tekintse meg a bejövő forgalomról és a kis- és nagy [forgalomról tájékoztató dokumentációt.][ingress-shim]

A tanúsítvány sikeres létrejöttének ellenőrzéséhez használja az `kubectl describe certificate tls-secret --namespace ingress-basic` parancsot.

Ha a tanúsítványt kiállították, az alábbihoz hasonló kimenetet fog látni:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Ha további tanúsítvány-erőforrást kell létrehoznia, ezt az alábbi példajegyzékben adhatja meg. Frissítse a *dnsNames és* *a domains* paramétert az előző lépésben létrehozott DNS-névre. Ha csak belső bejövő forgalomvezérlőt használ, adja meg a szolgáltatás belső DNS-nevét.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

A tanúsítvány-erőforrás létrehozásához használja az `kubectl apply` parancsot.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>A bejövő forgalom konfigurációjának tesztelése

Nyisson meg egy webböngészőt a Kubernetes bejövő vezérlő FQDN-jének (például *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* ).

Mivel ezek a példák a t használják, a kibocsátott TLS-/SSL-tanúsítványt a böngésző `letsencrypt-staging` nem megbízhatónak. Fogadja el a figyelmeztető üzenetet az alkalmazáshoz való folytatáshoz. A tanúsítvány adatai azt mutatják, hogy a Hamis *LE köztes X1* tanúsítványt a Let's Encrypt bocsátotta ki. Ez a hamis tanúsítvány azt jelzi, hogy helyesen feldolgozta `cert-manager` a kérést, és kapott egy tanúsítványt a szolgáltatótól:

![Let's Encrypt előkészítési tanúsítvány](media/ingress/staging-certificate.png)

Ha a Let's Encryptet a helyett a használatára módosítja, a Rendszer a Let's Encrypt által kibocsátott megbízható tanúsítványt használja, ahogy az az alábbi `prod` `staging` példában látható:

![Let's Encrypt certificate](media/ingress/certificate.png)

A bemutató alkalmazás megjelenik a webböngészőben:

![Példa alkalmazásra](media/ingress/app-one.png)

Most adja hozzá *a /hello-world-two útvonalat* az FQDN-hez, például: *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Megjelenik a második bemutatóalkalmazás az egyéni címmel:

![Példa az alkalmazásra – második](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a Helm segítségével telepítette a bejövő forgalom összetevőit, tanúsítványait és mintaalkalmazásait. Helm-diagram üzembe helyezésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podokat, üzemelő példányokat és szolgáltatásokat tartalmaznak. Az erőforrások törléséhez törölheti a teljes névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja az `kubectl delete` parancsot, és adja meg a névtér nevét. A névtér összes erőforrása törölve lesz.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások törlése egyenként

Másik lehetőségként részletesebben is törölheti a létrehozott egyes erőforrásokat. Először távolítsa el a tanúsítvány-erőforrásokat:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Most listába kell sorolni a Helm-kiadásokat az `helm list` paranccsal. Keresse meg az *nginx-ingress és* *a cert-manager* nevű diagramokat az alábbi kimenetben látható módon:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Távolítsa el a kiadásokat az `helm uninstall` paranccsal. Az alábbi példa eltávolítja az NGINX bejövő forgalom és a tanúsítványkezelő központi telepítését.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Ezután távolítsa el a két mintaalkalmazást:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Törölje a saját névterét. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

Végül távolítsa el a bejövő vezérlőhöz létrehozott statikus nyilvános IP-címet. Adja meg *MC_* a cikk első lépésében lekért erőforráscsoport nevét, például: *MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AKS néhány külső összetevőt is tartalmazott. Az összetevőkkel kapcsolatos további információkért tekintse meg a következő projektoldalakat:

- [Helm CLI][helm-cli]
- [NGINX bejövő forgalomvezérlő][nginx-ingress]
- [Tanúsítványkezelő][cert-manager]

További lehetőségek:

- [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózatot és IP-címet használó bejövő vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő forgalomvezérlő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy bejövő forgalomvezérlőt dinamikus nyilvános IP-címmel, és konfigurálja a Let's Encryptet TLS-tanúsítványok automatikus létrehozására][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
