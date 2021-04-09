---
title: Bejövő vezérlő használata statikus IP-címmel
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy, az Azure Kubernetes Service (ak) fürtben statikus nyilvános IP-címmel rendelkező NGINX bejövő adatforgalom-vezérlőt.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: fa6572ddc694cb892f48cb3e618c176f087524f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506565"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az NGINX beléptetési [vezérlőt][nginx-ingress] egy Azure Kubernetes-szolgáltatási (ak-) fürtben. A bejövő vezérlő statikus nyilvános IP-címmel van konfigurálva. A Certificate [-Manager][cert-manager] projekttel automatikusan hozhatja meg és állíthatja be a tanúsítványok [titkosítását][lets-encrypt] . Végül két alkalmazás fut az AK-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy adatforgalom-vezérlőt, amely a let titkosítást használja a dinamikus nyilvános IP-címmel rendelkező TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ez a cikk a [Helm 3][helm] használatával telepíti az NGINX beáramló vezérlőt és a tanúsítvány-kezelőt. Győződjön meg arról, hogy a Helm legújabb kiadását használja, és hozzáfér a *bejövő-Nginx* és a *jetstack* Helm-adattárakhoz. A frissítési utasításokért tekintse meg a [Helm install docs][helm-install]című témakört. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

Alapértelmezés szerint egy új nyilvános IP-cím-hozzárendeléssel jön létre az NGINX bejövő adatkezelője. Ez a nyilvános IP-cím csak a beáramló vezérlő élettartamára statikus, és a vezérlő törlése és újbóli létrehozása esetén elvész. Gyakori konfigurációs követelmény, hogy az NGINX bejövő vezérlő egy meglévő statikus nyilvános IP-címet adjon meg. A statikus nyilvános IP-cím akkor is megmarad, ha a bejövő vezérlőt törölték. Ez a módszer lehetővé teszi a meglévő DNS-rekordok és hálózati konfigurációk egységes módon történő használatát az alkalmazások életciklusa során.

Ha létre kell hoznia egy statikus nyilvános IP-címet, először le kell kérnie az AK-fürt erőforráscsoport-nevét az az [AK show][az-aks-show] paranccsal:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ezután hozzon létre egy nyilvános IP-címet a *statikus* kiosztási módszerrel az az [Network Public-IP Create][az-network-public-ip-create] parancs használatával. A következő példa egy *myAKSPublicIP* nevű nyilvános IP-címet hoz létre az alábbi lépésben beszerzett AK fürterőforrás-csoportban:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> A fenti parancsok olyan IP-címet hoznak létre, amely törölve lesz, ha törli az AK-fürtöt. Másik lehetőségként létrehozhat egy olyan IP-címet is egy másik erőforráscsoporthoz, amelyet az AK-fürttől függetlenül kezelhet. Ha egy másik erőforráscsoporthoz hoz létre IP-címet, győződjön meg arról, hogy az AK-fürt által használt fürt identitása delegált engedélyekkel rendelkezik a másik erőforráscsoporthoz, például a *hálózati közreműködőhöz*. További információ: [statikus nyilvános IP-cím és DNS-címke használata az AK-Load Balancer használatával][aks-static-ip].

Most telepítse az *Nginx-* beléptetési diagramot a Helm szolgáltatással. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

Két további paramétert kell átadnia a Helm-kiadáshoz, hogy a bejövő forgalom vezérlője a terheléselosztó statikus IP-címét és a beáramló vezérlő szolgáltatáshoz való hozzárendelését, valamint a nyilvános IP-cím erőforrásra alkalmazott DNS-név címkéjét is fel tudja hívni. Ahhoz, hogy a HTTPS-tanúsítványok megfelelően működjenek, a DNS-név címkével kell konfigurálni egy teljes tartománynevet a bejövő vezérlő IP-címéhez.

1. Adja hozzá a `--set controller.service.loadBalancerIP` paramétert. Adja meg saját nyilvános IP-címét, amelyet az előző lépésben hozott létre.
1. Adja hozzá a `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` paramétert. Adja meg az előző lépésben létrehozott nyilvános IP-címhez alkalmazni kívánt DNS-név címkéjét.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *– alapszintű* forgalomhoz. Szükség szerint adja meg a saját környezetének névterét. Ha az AK-fürt nincs engedélyezve a Kubernetes RBAC, adja hozzá `--set rbac.create=false` a parancsot a Helm parancshoz.

> [!TIP]
> Ha engedélyezni szeretné az [ügyfél forrásának IP-megőrzését][client-source-ip] a fürtben lévő tárolók kéréseire, adja hozzá `--set controller.service.externalTrafficPolicy=Local` a parancsot a Helm install parancshoz. Az ügyfél forrásának IP-címét a kérelem fejlécében tárolja a rendszer az *X által továbbított – esetében*. Ha az ügyfél-forrás IP-megtartást engedélyező bejövő vezérlőt használ, a TLS-áteresztő nem fog működni.

Frissítse a következő parancsfájlt a bejövő vezérlő **IP-címével** és egy **egyedi névvel** , amelyet a teljes tartománynév előtaghoz használni szeretne.

> [!IMPORTANT]
> A parancs futtatásakor frissítenie kell a *STATIC_IP* és az *DNS_LABELt* a saját IP-címével és egyedi nevével.

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

Ha a Kubernetes terheléselosztó szolgáltatás létrejön az NGINX beáramló vezérlőhöz, a statikus IP-cím hozzá van rendelve, ahogy az a következő példában látható:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Még nem jöttek létre Bejövő szabályok, így az NGINX bejövő vezérlő alapértelmezett 404 lapja jelenik meg, ha megkeresi a nyilvános IP-címet. A bejövő szabályok a következő lépésekben vannak konfigurálva.

A DNS-név címkét úgy ellenőrizheti, hogy a nyilvános IP-címen lévő teljes tartománynevet a következőképpen kérdezi le:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

A bejövő vezérlő mostantól az IP-címen vagy a teljes tartománynéven keresztül érhető el.

## <a name="install-cert-manager"></a>Tanúsítványkezelő telepítése

Az NGINX bejövőforgalom-vezérlő támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk a [CERT-Manager][cert-manager]használatát mutatja be, amely lehetővé teszi az automatikus titkosítást a tanúsítvány-létrehozási és-kezelési funkciók [titkosításához][lets-encrypt] .

> [!NOTE]
> Ez a cikk a `staging` környezetet használja a titkosításhoz. Éles környezetben az `letsencrypt-prod` `https://acme-v02.api.letsencrypt.org/directory` erőforrás-definíciókban és a Helm-diagram telepítésekor használja a és a-t.

Ha a tanúsítvány-kezelő vezérlőt egy Kubernetes RBAC-kompatibilis fürtön szeretné telepíteni, használja a következő `helm install` parancsot:

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

A tanúsítvány-kezelő konfigurálásával kapcsolatos további információkért tekintse meg a [CERT-Manager projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>HITELESÍTÉSSZOLGÁLTATÓI fürt kiállítójának létrehozása

A tanúsítványok kiállítása előtt a tanúsítvány-kezelőnek [kiállítói][cert-manager-issuer] vagy [ClusterIssuer][cert-manager-cluster-issuer] erőforrásra van szüksége. Ezek a Kubernetes-erőforrások azonosak a funkcionalitással, azonban `Issuer` egyetlen névtérben működnek, és az `ClusterIssuer` összes névtérben működnek. További információt a [tanúsítvány-kezelő kiállítói][cert-manager-issuer] dokumentációjában talál.

Hozzon létre egy fürt kiállítóját, például: `cluster-issuer.yaml` , a következő példa jegyzékfájl használatával. Frissítse az e-mail-címet a szervezete érvényes címével:

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

A kimenetnek ehhez a példához hasonlónak kell lennie:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Be van állítva egy bejövő vezérlő és egy tanúsítványkezelő megoldás. Most futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában a Helm egy egyszerű "Hello World" alkalmazás két példányának üzembe helyezésére szolgál.

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

A következő példában a címnek való adatforgalom `https://demo-aks-ingress.eastus.cloudapp.azure.com/` a nevű szolgáltatáshoz lesz irányítva `aks-helloworld` . A `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` rendszer átirányítja a címnek a szolgáltatás felé irányuló forgalmat `ingress-demo` . Frissítse a *gazdagépeket és a* *gazdagépet* az előző lépésben létrehozott DNS-névre.

Hozzon létre egy nevű fájlt `hello-world-ingress.yaml` , és másolja a következő PÉLDÁBAN YAML.

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

Hozza létre a bejövő erőforrásokat a `kubectl apply` parancs használatával.

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

A kimenetnek ehhez a példához hasonlónak kell lennie:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tanúsítvány-objektum létrehozása

Ezután létre kell hozni egy tanúsítvány-erőforrást. A tanúsítvány erőforrása határozza meg a kívánt X. 509 tanúsítványt. További információ: [CERT-Manager-tanúsítványok][cert-manager-certificates].

A CERT-Manager valószínűleg automatikusan létrehozott egy tanúsítvány-objektumot a bejövő forgalom-alátét használatával, amelyet a rendszer automatikusan telepít a CERT-Manager szolgáltatással a v 0.2.2 óta. További információkért lásd a bejövő adatokról szóló [dokumentációt][ingress-shim].

A tanúsítvány sikeres létrehozásának ellenőrzéséhez használja az `kubectl describe certificate tls-secret --namespace ingress-basic` parancsot.

Ha a tanúsítvány ki lett állítva, a következőhöz hasonló kimenet jelenik meg:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Ha további tanúsítvány-erőforrást kell létrehoznia, ezt az alábbi példával teheti meg. Frissítse a *dnsNames* és a *tartományokat* az előző lépésben létrehozott DNS-névre. Ha csak belső bejövő vezérlőt használ, adja meg a szolgáltatás belső DNS-nevét.

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

## <a name="test-the-ingress-configuration"></a>A behatolási konfiguráció tesztelése

Nyisson meg egy webböngészőt a Kubernetes bejövő adatkezelője teljes tartománynevére, például: *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

A példákban `letsencrypt-staging` a kiállított TLS/SSL-tanúsítványt nem megbízhatónak tekinti a böngésző. Fogadja el az alkalmazás folytatásához szükséges figyelmeztető üzenetet. A tanúsítvány adatai azt mutatják be, hogy ezt a *hamis le közbenső X1* -tanúsítványt a titkosítva állítja ki. Ez a hamis tanúsítvány azt jelzi `cert-manager` , hogy megfelelően feldolgozta a kérést, és a szolgáltatótól kapott tanúsítványt:

![Az átmeneti tanúsítvány titkosítása](media/ingress/staging-certificate.png)

Ha úgy módosítja a titkosítást, hogy `prod` a rendszer helyett a titkosítást használja, az `staging` alábbi példában látható módon a titkosított tanúsítvány is használatos.

![A tanúsítvány titkosítása](media/ingress/certificate.png)

A bemutató alkalmazás a böngészőben jelenik meg:

![Példa egy alkalmazásra](media/ingress/app-one.png)

Most adja hozzá a */Hello-World-Two* elérési útját a teljes tartománynévhez, például: *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Az egyéni címmel rendelkező második bemutató alkalmazás a következőképpen jelenik meg:

![Példa két alkalmazásra](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a beáramlási összetevők, a tanúsítványok és a minta alkalmazások telepítéséhez használható. Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ezek az erőforrások a hüvelyek, a központi telepítések és a szolgáltatások részét képezik. Ezen erőforrások törléséhez törölheti a teljes minta névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja a `kubectl delete` parancsot, és adja meg a névtér nevét. A rendszer törli a névtérben lévő összes erőforrást.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások egyenkénti törlése

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Először távolítsa el a tanúsítvány erőforrásait:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Most sorolja fel a Helm kiadásait a `helm list` paranccsal. Keresse meg az *Nginx-beáramló* és a *CERT-Manager* nevű diagramot az alábbi példában látható módon:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Távolítsa el a kiadásokat a `helm uninstall` paranccsal. Az alábbi példa eltávolítja az NGINX beáramló üzembe helyezését és a Tanúsítványkezelő üzemelő példányait.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Ezután távolítsa el a két minta alkalmazást:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Törölje a saját maga névterét. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

Végezetül távolítsa el a bejövő vezérlőhöz létrehozott statikus nyilvános IP-címet. Adja meg *MC_* fürterőforrás-csoportjának nevét a cikk első lépéseként, például *MC_myResourceGroup_myAKSCluster_eastus*:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk néhány külső összetevőt tartalmaz az ak-nak. Ha többet szeretne megtudni ezekről az összetevőkről, tekintse meg a következő Project-lapokat:

- [Helm parancssori felület][helm-cli]
- [NGINX bejövő adatkezelő][nginx-ingress]
- [Tanúsítványkezelő][cert-manager]

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Dinamikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]

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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
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
