---
title: Bejövő adatforgalom létrehozása automatikus TLS-vel
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepíthet és konfigurálhat egy olyan NGINX beáramlási vezérlőt, amely egy Azure Kubernetes-szolgáltatási (ak-) fürtön használja az automatikus TLS-tanúsítvány létrehozását.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: aa03558196527e320ecba249dd22af19fec7a90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592336"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>HTTPS bejövő adatkezelő létrehozása az Azure Kubernetes szolgáltatásban (ak)

A bejövő vezérlő egy olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és TLS-megszakítást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásai az egyes Kubernetes-szolgáltatások bejövő szabályainak és útvonalának konfigurálására szolgálnak. A bejövő és a bejövő forgalomra vonatkozó szabályok használatával egyetlen IP-cím segítségével átirányíthatja a forgalmat több szolgáltatásba egy Kubernetes-fürtben.

Ez a cikk bemutatja, hogyan helyezheti üzembe az NGINX beléptetési [vezérlőt][nginx-ingress] egy Azure Kubernetes-szolgáltatási (ak-) fürtben. A Certificate [-Manager][cert-manager] projekttel automatikusan hozhatja meg és állíthatja be a tanúsítványok [titkosítását][lets-encrypt] . Végül két alkalmazás fut az AK-fürtben, amelyek mindegyike egyetlen IP-címen érhető el.

További lehetőségek:

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy olyan bejövő adatkezelőt, amely a let titkosítást használja a statikus nyilvános IP-címmel rendelkező TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ez a cikk azt is feltételezi, hogy van [egy egyéni tartománya][custom-domain] egy olyan [DNS-zónával][dns-zone] , amely ugyanabban az erőforráscsoporthoz tartozik, mint az AK-fürt.

Ez a cikk a [Helm 3][helm] használatával telepíti az NGINX beáramló vezérlőt és a tanúsítvány-kezelőt. Győződjön meg arról, hogy a Helm legújabb kiadását használja, és hozzáfér a *bejövő-Nginx* és a *jetstack* Helm-adattárakhoz. A frissítési utasításokért tekintse meg a [Helm install docs][helm-install]című témakört. A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: [alkalmazások telepítése az Azure Kubernetes szolgáltatásban (ak)][use-helm].

Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verzióját is futtatnia kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő adatkezelő létrehozása

A beáramló vezérlő létrehozásához használja az `helm` parancsot az *Nginx-behatolások* telepítéséhez. A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. Ahhoz, hogy teljes mértékben élvezhesse a bejövő vezérlő replikáit, győződjön meg arról, hogy az AK-fürt több csomópontja van.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> A következő példa egy Kubernetes névteret hoz létre a bejövő erőforrások *– alapszintű* forgalomhoz. Szükség szerint adja meg a saját környezetének névterét.

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

A nyilvános IP-cím lekéréséhez használja az `kubectl get service` parancsot. Néhány percet vesz igénybe, hogy az IP-cím hozzá legyen rendelve a szolgáltatáshoz.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Még nem jöttek létre Bejövő szabályok. Ha megkeresi a nyilvános IP-címet, megjelenik az NGINX bejövő vezérlő alapértelmezett 404 lapja.

## <a name="add-an-a-record-to-your-dns-zone"></a>Rekord hozzáadása a DNS-zónához

Adjon hozzá *egy* rekordot a DNS-zónához az NGINX szolgáltatás külső IP-címével az [az Network DNS Record-set A Add-Record][az-network-dns-record-set-a-add-record]paranccsal.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Az egyéni tartomány helyett a bejövő vezérlő IP-címéhez is beállíthat teljes tartománynevet. Vegye figyelembe, hogy ez a minta egy bash-rendszerhéjhoz készült.
> 
> ```bash
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Tanúsítványkezelő telepítése

Az NGINX bejövőforgalom-vezérlő támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk a [CERT-Manager][cert-manager]használatát mutatja be, amely lehetővé teszi az automatikus titkosítást a tanúsítvány-létrehozási és-kezelési funkciók [titkosításához][lets-encrypt] .

A tanúsítvány-kezelő vezérlő telepítése:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

A tanúsítvány-kezelő konfigurálásával kapcsolatos további információkért tekintse meg a [CERT-Manager projektet][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>HITELESÍTÉSSZOLGÁLTATÓI fürt kiállítójának létrehozása

A tanúsítványok kiállítása előtt a tanúsítvány-kezelőnek [kiállítói][cert-manager-issuer] vagy [ClusterIssuer][cert-manager-cluster-issuer] erőforrásra van szüksége. Ezek a Kubernetes-erőforrások azonosak a funkcionalitással, azonban `Issuer` egyetlen névtérben működnek, és az `ClusterIssuer` összes névtérben működnek. További információt a [tanúsítvány-kezelő kiállítói][cert-manager-issuer] dokumentációjában talál.

Hozzon létre egy fürt kiállítóját, például: `cluster-issuer.yaml` , a következő példa jegyzékfájl használatával. Frissítse az e-mail-címet a szervezete érvényes címével:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
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

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Bemutató alkalmazások futtatása

Be van állítva egy bejövő vezérlő és egy tanúsítványkezelő megoldás. Most futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában a Helm egy egyszerű *Hello World* -alkalmazás két példányának üzembe helyezésére szolgál.

A beáramló vezérlő működés közbeni megtekintéséhez futtasson két bemutató alkalmazást az AK-fürtben. Ebben a példában `kubectl apply` egy egyszerű *Helló World* -alkalmazás két példányának üzembe helyezésére használható.

Hozzon létre egy *AK-HelloWorld-One. YAML* fájlt, és másolja a következő PÉLDÁBAN szereplő YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Hozzon létre egy *AK-HelloWorld-Two. YAML* fájlt, és másolja a következő PÉLDÁBAN szereplő YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Futtassa a két bemutató alkalmazást a használatával `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Bejövő forgalom útvonalának létrehozása

Mindkét alkalmazás már fut a Kubernetes-fürtön. Azonban olyan típusú szolgáltatással vannak konfigurálva, amely `ClusterIP` nem érhető el az internetről. A nyilvánosan elérhetővé tételéhez hozzon létre egy Kubernetes-bejövő erőforrást. A bejövő erőforrás konfigurálja azokat a szabályokat, amelyek átirányítják a forgalmat a két alkalmazás egyikére.

A következő példában a *Hello-World-beáramló címekre irányuló forgalom. MY_CUSTOM_DOMAIN* az *AK-HelloWorld-One* szolgáltatáshoz van irányítva. A *Hello-World-beáramló címekre irányuló forgalom. MY_CUSTOM_DOMAIN/Hello-World-Two* átirányítva az *AK-HelloWorld-Two* szolgáltatáshoz. Forgalom a *Hello-World-inbehatolással. MY_CUSTOM_DOMAIN/statikus* a (z) *HelloWorld-One* nevű szolgáltatáshoz van irányítva a statikus eszközökhöz.

> [!NOTE]
> Ha az egyéni tartomány helyett a bejövő vezérlő IP-címéhez teljes tartománynevet állított be, akkor a *Hello-World-beáramló helyett használja a teljes tartománynevet. MY_CUSTOM_DOMAIN*. Például ha a teljes tartománynév *demo-AKS-ingress.eastus.cloudapp.Azure.com*, cserélje le a *Hello-World-inbehatolást. MY_CUSTOM_DOMAIN* a *demo-AKS-ingress.eastus.cloudapp.Azure.com* a alkalmazásban `hello-world-ingress.yaml` .

Hozzon létre egy nevű fájlt `hello-world-ingress.yaml` az alábbi példa YAML használatával. Frissítse a *gazdagépeket és a* *gazdagépet* az előző lépésben létrehozott DNS-névre.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Hozza létre a bejövő erőforrásokat a `kubectl apply` parancs használatával.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Tanúsítvány-objektum létrehozásának ellenőrzése

Ezután létre kell hozni egy tanúsítvány-erőforrást. A tanúsítvány erőforrása határozza meg a kívánt X. 509 tanúsítványt. További információ: [CERT-Manager-tanúsítványok][cert-manager-certificates]. A CERT-Manager automatikusan létrehozott egy tanúsítvány-objektumot a bejövő forgalom használatával, amelyet a rendszer automatikusan üzembe helyez a CERT-Managerrel a v 0.2.2 óta. További információkért lásd a bejövő adatokról szóló [dokumentációt][ingress-shim].

A tanúsítvány sikeres létrehozásának ellenőrzéséhez használja a parancsot, és ellenőrizze, hogy a `kubectl get certificate --namespace ingress-basic` *kész* érték *igaz*-e, ami több percet is igénybe vehet.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>A behatolási konfiguráció tesztelése

Nyisson meg egy webböngészőt a *Hello-World-inbehatolás szolgáltatásban. MY_CUSTOM_DOMAIN* a Kubernetes bejövő adatkezelője. Figyelje meg, hogy a HTTPS használatára van átirányítva, és a tanúsítvány megbízható, és a bemutató alkalmazás megjelenik a böngészőben. Adja hozzá a */Hello-World-Two* elérési útját, és figyelje meg, hogy a második bemutató alkalmazás az egyéni címmel jelenik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a beáramlási összetevők, a tanúsítványok és a minta alkalmazások telepítéséhez használható. Amikor központilag telepít egy Helm-diagramot, a rendszer számos Kubernetes-erőforrást hoz létre. Ezek az erőforrások a hüvelyek, a központi telepítések és a szolgáltatások részét képezik. Ezen erőforrások törléséhez törölheti a teljes minta névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja a `kubectl delete` parancsot, és adja meg a névtér nevét. A rendszer törli a névtérben lévő összes erőforrást.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások egyenkénti törlése

Azt is megteheti, hogy egy részletesebb megközelítéssel törli a létrehozott egyéni erőforrásokat. Először távolítsa el a fürt kiállítói erőforrásait:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Sorolja fel az paranccsal a Helm kiadásait `helm list` . Keresse meg az *Nginx* és a *CERT-Manager* nevű diagramot az alábbi példában látható módon:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Távolítsa el a kiadásokat a `helm uninstall` paranccsal. Az alábbi példa eltávolítja az NGINX bejövő és a tanúsítvány-kezelő üzemelő példányait.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ezután távolítsa el a két minta alkalmazást:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Távolítsa el a bejövő forgalom útvonalát, amely irányítja a forgalmat a mintául szolgáló alkalmazásokba:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Végezetül törölheti saját maga is a névteret. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
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
- [Hozzon létre egy olyan bejövő adatkezelőt, amely a let titkosítást használja a statikus nyilvános IP-címmel rendelkező TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
