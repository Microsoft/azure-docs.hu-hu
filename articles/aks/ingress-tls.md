---
title: Bejövő forgalom létrehozása automatikus TLS-sel
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan telepíthet és konfigurálható egy NGINX bejövő forgalomvezérlő, amely a Let's Encryptet használja a TLS-tanúsítvány automatikus generációjakor egy Azure Kubernetes Service-fürtben.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3f78c0777d1377c8afdc5b43b457873de395cd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779794"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>HTTPS bejövő forgalomvezérlő létrehozása Azure Kubernetes Service (AKS)

A bejövőforgalom-vezérlő olyan szoftver, amely fordított proxyt, konfigurálható forgalom-útválasztást és TLS-leválasztást biztosít a Kubernetes-szolgáltatásokhoz. A Kubernetes bejövő erőforrásaival konfigurálhatja az egyes Kubernetes-szolgáltatások bejövő szabályait és útvonalait. A bejövőforgalom-vezérlő és a bejövő szabályok használatával egyetlen IP-cím használatával irányítható a forgalom egy Kubernetes-fürt több szolgáltatására.

Ez a cikk bemutatja, hogyan helyezheti üzembe az [NGINX][nginx-ingress] bejövő vezérlőt egy Azure Kubernetes Service (AKS) fürtben. A [tanúsítványkezelő projekt][cert-manager] a [Let's Encrypt-tanúsítványok][lets-encrypt] automatikus előállítására és konfigurálására használható. Végül két alkalmazás fut az AKS-fürtben, amelyek mind egyetlen IP-címen keresztül érhetők el.

További lehetőségek:

- [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózatot és IP-címet használó bejövő vezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő forgalomvezérlő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy bejövő vezérlőt, amely a Let's Encrypt használatával automatikusan hoz létre TLS-tanúsítványokat statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Ez a cikk azt [][custom-domain] is feltételezi, hogy az AKS-fürt erőforráscsoportjában van egy [DNS-zónával][dns-zone] bíró egyéni tartomány.

Ez a cikk a [Helm 3 használatával][helm] telepíti az NGINX bejövő forgalomvezérlőt és a tanúsítványkezelőt. Győződjön meg arról, hogy a Helm legújabb kiadását használja, és hozzáféréssel rendelkezik a *ingress-nginx* és *a jetstack* Helm-adattárakhoz. A frissítésre vonatkozó utasításokért lásd a [Helm telepítési docs-ját.][helm-install] A Helm konfigurálásával és használatával kapcsolatos további információkért lásd: Alkalmazások telepítése a [Helm használatával az Azure Kubernetes Service (AKS) használatával.][use-helm]

Ehhez a cikkhez az Azure CLI 2.0.64-es vagy újabb verziójára is szükség van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Bejövő vezérlő létrehozása

A bejövő vezérlő létrehozásához használja az parancsot az `helm` *nginx-ingress telepítéséhez.* A magasabb szintű redundancia érdekében az NGINX bejövő forgalmi vezérlő két replikája van telepítve a `--set controller.replicaCount` paraméterrel. A bejövő forgalomvezérlő replikái futtatásának teljes körű kihasználása érdekében győződjön meg arról, hogy az AKS-fürtben egynél több csomópont található.

A bejövő forgalmi vezérlőt egy Linux-csomóponton is ütemezni kell. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A csomópont-választó `--set nodeSelector` paraméterrel történő meghatározása arra utasítja a Kubernetes ütemezőt, hogy az NGINX bejövő vezérlőt Linux-alapú csomóponton futtassa.

> [!TIP]
> Az alábbi példa létrehoz egy Kubernetes-névteret a *ingress-basic nevű bejövő forgalom erőforrásaihoz.* Szükség szerint adjon meg egy névteret a saját környezetéhez.

> [!TIP]
> Ha engedélyezni szeretné [][client-source-ip] az ügyfélforrás IP-címének megőrzését a fürtben található tárolókra irányuló kérések számára, adja hozzá a parancsot a `--set controller.service.externalTrafficPolicy=Local` Helm telepítési parancshoz. Az ügyfél forrás IP-címét a kérés fejlécében, az *X-Forwarded-For* alatt tárolja a rendszer. Ha olyan bejövő forgalomvezérlőt használ, amelynél engedélyezve van az ügyfélforrás IP-megőrzése, a TLS-áteredő nem fog működni.

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

A telepítés során létrejön egy nyilvános Azure IP-cím a bejövő forgalomvezérlőhöz. Ez a nyilvános IP-cím statikus a bejövő forgalomvezérlő élettartama során. Ha törli a bejövő forgalomvezérlőt, a nyilvános IP-cím hozzárendelése elveszik. Ha ezután létrehoz egy további bejövő forgalomvezérlőt, a rendszer egy új nyilvános IP-címet rendel hozzá. Ha meg szeretné őrizni a nyilvános IP-cím használatát, létrehozhat egy statikus nyilvános IP-címmel egy bejövő [vezérlőt.][aks-ingress-static-tls]

A nyilvános IP-cím lekért használhatja az `kubectl get service` parancsot. Az IP-cím szolgáltatáshoz való hozzárendelése néhány percet vesz igénybe.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Még nem jött létre bejövő forgalomra vonatkozó szabály. Ha a nyilvános IP-címre keres rá, megjelenik az NGINX bejövő forgalomvezérlő alapértelmezett 404-es oldala.

## <a name="add-an-a-record-to-your-dns-zone"></a>A rekord hozzáadása a DNS-zónához

Adjon hozzá *egy A* rekordot a DNS-zónához az NGINX szolgáltatás külső [IP-címével az az network dns record-set a add-record használatával.][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Ha szükséges, egyéni tartomány helyett teljes tartománynevet is konfigurálhat a bejövő forgalomvezérlő IP-címéhez. Vegye figyelembe, hogy ez a minta Bash-rendszerhéjhoz való.
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

Az NGINX bejövőforgalom-vezérlő támogatja a TLS-megszakítást. A HTTPS-tanúsítványok lekérésének és konfigurálásának számos módja van. Ez a cikk bemutatja a [tanúsítványkezelőt,][cert-manager]amely automatikus Lets [Encrypt][lets-encrypt] tanúsítvány-generálás és felügyeleti funkciókat biztosít.

A tanúsítványkezelő vezérlő telepítése:

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

A tanúsítványkezelő konfigurálásról a [cert-manager][cert-manager]projektben található további információ.

## <a name="create-a-ca-cluster-issuer"></a>Hitelesítésszolgáltatói fürtkiállító létrehozása

A tanúsítványok kiadása előtt a tanúsítványkezelőnek kiállító vagy [ClusterIssuer erőforrásra van][cert-manager-cluster-issuer] szüksége. [][cert-manager-issuer] Ezek a Kubernetes-erőforrások azonosak a funkciókban, azonban egyetlen névtérben működnek, és az `Issuer` `ClusterIssuer` összes névtérben működnek. További információért tekintse meg [a tanúsítványkezelő kiállítójának dokumentációját.][cert-manager-issuer]

Hozzon létre egy fürtkiállítót, például a következő `cluster-issuer.yaml` példajegyzéket használva. Frissítse az e-mail-címet a szervezet érvényes címével:

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

Konfigurálva van egy bejövő forgalomvezérlő és egy tanúsítványkezelő megoldás. Most futtasunk két bemutatóalkalmazást az AKS-fürtben. Ebben a példában a Helm egy egyszerű Hello World alkalmazás két példányának üzembe *helyezésére* használható.

A bejövő forgalom vezérlőinek a művelet közbeni futtatásához futtason két bemutatóalkalmazást az AKS-fürtben. Ebben a példában a használatával egy egyszerű Hello World alkalmazás két `kubectl apply` *példányát helyezheti üzembe.*

Hozzon létre *egy aks-helloworld-one.yaml fájlt,* és másolja be az alábbi YAML-példát:

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

Hozzon létre *egy aks-helloworld-two.yaml fájlt,* és másolja be az alábbi YAML-példát:

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

Futtassa a két bemutatóalkalmazást a `kubectl apply` használatával:

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Bejövő útvonal létrehozása

Most már mindkét alkalmazás fut a Kubernetes-fürtön. Azonban egy típusú szolgáltatással vannak konfigurálva, és nem érhetők el `ClusterIP` az internetről. Ahhoz, hogy nyilvánosan elérhetővé tegye őket, hozzon létre egy Kubernetes bejövő forgalom erőforrást. A bejövőforgalom-erőforrás konfigurálja a szabályokat, amelyek a forgalmat a két alkalmazás egyikéhez irányítják.

A következő példában a *hello-world-ingress címre való forgalom. MY_CUSTOM_DOMAIN* az *aks-helloworld-one szolgáltatáshoz lesz irányítva.* Forgalom a *hello-world-ingress címre. MY_CUSTOM_DOMAIN/hello-world-two* az *aks-helloworld-two szolgáltatáshoz van irányítva.* Forgalom *a hello-world-ingress forgalomra. MY_CUSTOM_DOMAIN/statikus* fájl a statikus eszközök számára az *aks-helloworld-one* nevű szolgáltatáshoz lesz irányítva.

> [!NOTE]
> Ha egyéni tartomány helyett teljes tartománynevet konfigurált a bejövő forgalomvezérlő IP-címéhez, használja a teljes tartománynevet a *hello-world-ingress helyett. MY_CUSTOM_DOMAIN:*. Ha például a teljes tartomány demo-aks-ingress.eastus.cloudapp.azure.com *,* cserélje le *a hello-world-ingress helyére. MY_CUSTOM_DOMAIN* és *demo-aks-ingress.eastus.cloudapp.azure.com* a következőben: `hello-world-ingress.yaml` .

Hozzon létre egy nevű fájlt `hello-world-ingress.yaml` az alábbi YAML-példa használatával. Frissítse a *gazdagépeket* *és* gazdagépeket az előző lépésben létrehozott DNS-névre.

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

Hozza létre a bejövő forgalom erőforrását az `kubectl apply` paranccsal.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Tanúsítványobjektum létrejöttének ellenőrzése

Ezután létre kell hoznunk egy tanúsítvány-erőforrást. A tanúsítványerőforrás határozza meg a kívánt X.509-tanúsítványt. További információ: [tanúsítványkezelői tanúsítványok.][cert-manager-certificates] A tanúsítványkezelő automatikusan létrehozott egy tanúsítványobjektumot a bejövő forgalomhoz és az eszközhöz, amelyet a rendszer a 0.2.2-es óta automatikusan telepít a tanúsítványkezelővel. További információkért tekintse meg a [bejövő forgalomról és a kis- és nagy forgalomról tájékoztató dokumentációt.][ingress-shim]

A tanúsítvány sikeres létrejöttének ellenőrzéséhez használja a parancsot, és ellenőrizze, hogy a READY igaz-e, `kubectl get certificate --namespace ingress-basic` ami több percig is eltarthat.  

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>A bejövő forgalom konfigurációjának tesztelése

Nyisson meg egy webböngészőt a *hello-world-ingress megnyitásához. MY_CUSTOM_DOMAIN* Kubernetes bejövő vezérlője. Figyelje meg, hogy a rendszer átirányítja a HTTPS használatára, és a tanúsítvány megbízható, és a bemutató alkalmazás megjelenik a webböngészőben. Adja hozzá *a /hello-world-two* útvonalat, és figyelje meg, hogy megjelenik a második bemutató alkalmazás az egyéni címmel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a cikk a Helm segítségével telepítette a bejövő forgalom összetevőit, tanúsítványait és mintaalkalmazásait. Helm-diagram üzembe helyezésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podokat, üzemelő példányokat és szolgáltatásokat tartalmaznak. Az erőforrások törléséhez törölheti a teljes névteret vagy az egyes erőforrásokat.

### <a name="delete-the-sample-namespace-and-all-resources"></a>A minta névtér és az összes erőforrás törlése

A teljes minta névtér törléséhez használja az parancsot, `kubectl delete` és adja meg a névtér nevét. A névtér összes erőforrása törölve lesz.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Erőforrások törlése egyenként

Másik lehetőségként részletesebben is törölheti a létrehozott egyes erőforrásokat. Először távolítsa el a fürtkiállító erőforrásait:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Listsa ki a Helm-kiadásokat az `helm list` paranccsal. Keresse meg az *nginx és* *a cert-manager* nevű diagramokat az alábbi kimenetben látható módon:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Távolítsa el a kiadásokat az `helm uninstall` paranccsal. Az alábbi példa eltávolítja az NGINX bejövő és tanúsítványkezelő üzemelő példányokat.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Ezután távolítsa el a két mintaalkalmazást:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Távolítsa el a forgalmat a mintaalkalmazások felé irányított bejövő útvonalról:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Végül törölheti magát a névteret. Használja a `kubectl delete` parancsot, és adja meg a névtér nevét:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk az AKS néhány külső összetevőt is tartalmazott. Az összetevőkkel kapcsolatos további információkért tekintse meg a következő projektoldalakat:

- [Helm CLI][helm-cli]
- [NGINX bejövő forgalomvezérlő][nginx-ingress]
- [Tanúsítványkezelő][cert-manager]

További lehetőségek:

- [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózatot és IP-címet használó bejövő forgalomvezérlő létrehozása][aks-ingress-internal]
- [Saját TLS-tanúsítványokat használó bejövő vezérlő létrehozása][aks-ingress-own-tls]
- [Hozzon létre egy bejövő vezérlőt, amely a Let's Encrypt használatával automatikusan hoz létre TLS-tanúsítványokat statikus nyilvános IP-címmel][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
