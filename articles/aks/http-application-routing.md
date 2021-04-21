---
title: HTTP-alkalmazás-útválasztási bővítmény Azure Kubernetes Service (AKS)
description: A HTTP-alkalmazásútválasztási bővítmény használatával hozzáférhet az Azure Kubernetes Service (AKS) üzembe helyezett alkalmazásokhoz.
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 95767e94a120b7f4595744beabc72fcd954e6e2f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783106"
---
# <a name="http-application-routing"></a>HTTP-alkalmazások útválasztása

A HTTP-alapú alkalmazás-útválasztási megoldással könnyedén elérheti a saját fürtjére (AKS) Azure Kubernetes Service telepített alkalmazásokat. Ha a megoldás engedélyezve van, konfigurál egy bejövő forgalomvezérlőt [az](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) AKS-fürtben. Az alkalmazások üzembe helyezésekor a megoldás nyilvánosan elérhető DNS-neveket is létrehoz az alkalmazásvégponthoz.

Ha a bővítmény engedélyezve van, egy DNS-zónát hoz létre az előfizetésben. További információ a DNS-költségekről: [DNS-díjszabás.][dns-pricing]

> [!CAUTION]
> A HTTP-alkalmazásútválasztási bővítmény segítségével gyorsan létrehozhat egy bejövő vezérlőt, és elérheti az alkalmazásokat. Ez a bővítmény jelenleg nem éles környezetben való használatra készült, és nem ajánlott éles környezetben való használatra. A több replikát és TLS-támogatást is magában foglaló, éles használatra kész bejövő forgalom üzembe helyezéséhez lásd: HTTPS bejövő [forgalomvezérlő létrehozása.](./ingress-tls.md)

## <a name="http-routing-solution-overview"></a>HTTP-útválasztási megoldás áttekintése

A bővítmény két összetevőt helyez üzembe: egy [Kubernetes bejövő][ingress] forgalomvezérlőt és egy [External-DNS-vezérlőt.][external-dns]

- **Bejövő forgalom vezérlője:** A bejövő forgalom vezérlője egy LoadBalancer típusú Kubernetes-szolgáltatással van elérhetővé téve az interneten. A bejövő forgalom vezérlője a [Kubernetes][ingress-resource]bejövő erőforrásokat figyeli és valósítja meg, ami útvonalakat hoz létre az alkalmazásvégponthoz.
- **Külső DNS-vezérlő:** A Kubernetes bejövő erőforrásait figyeli, és DNS A rekordokat hoz létre a fürtspecifikus DNS-zónában.

## <a name="deploy-http-routing-cli"></a>HTTP-útválasztás üzembe helyezése: CLI

A HTTP-alkalmazás-útválasztási bővítmény engedélyezhető az Azure CLI-val az AKS-fürt üzembe helyezésekor. Ezt az az [aks create][az-aks-create] paranccsal és a argumentummal `--enable-addons` használhatja.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Ha több bővítményt is engedélyezni szeretne, adja meg őket vesszővel elválasztott listaként. A HTTP-alkalmazások útválasztásának és monitorozásának engedélyezéséhez például használja a következő formátumot: `--enable-addons http_application_routing,monitoring` .

A HTTP-útválasztást meglévő AKS-fürtön is engedélyezheti [az az aks enable-addons paranccsal.][az-aks-enable-addons] A HTTP-útválasztás meglévő fürtön való engedélyezéséhez adja hozzá a paramétert, http_application_routing a következő `--addons` példában látható  módon:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

A fürt üzembe helyezése vagy frissítése után az [az aks show][az-aks-show] paranccsal lekérheti a DNS-zóna nevét.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Ez a név szükséges az alkalmazások AKS-fürtön való üzembe helyezéséhez, és az alábbi példakimenetben látható:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>HTTP-útválasztás üzembe helyezése: Portál

A HTTP-alkalmazásútválasztási bővítmény az AKS-fürt Azure Portal a fürtön keresztül engedélyezhető.

![A HTTP-útválasztási szolgáltatás engedélyezése](media/http-routing/create.png)

A fürt üzembe helyezése után keresse meg az automatikusan létrehozott AKS-erőforráscsoportot, és válassza ki a DNS-zónát. Jegyezze fel a DNS-zóna nevét. Ez a név szükséges az alkalmazások AKS-fürtön való üzembe helyezéséhez.

![A DNS-zóna nevének lekért neve](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Csatlakozás az AKS-fürthöz

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

Az [az aks get-credentials][] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Az alábbi példa lekért hitelesítő adatokat kap a *MyResourceGroup* *myAKSCluster* nevű AKS-fürthöz:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>HTTP-útválasztás használata

Előfordulhat, hogy a HTTP-alkalmazásútválasztási megoldás csak olyan bejövő erőforrásokon aktiválódik, amelyek a következőképpen vannak feljegyzetve:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Hozzon létre egy **samples-http-application-routing.yaml nevű** fájlt, és másolja be a következő YAML-fájlt. A 43. sorban frissítsen a cikk előző lépésében összegyűjtött `<CLUSTER_SPECIFIC_DNS_ZONE>` DNS-zónanévvel.

```yaml
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
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

Az [erőforrások létrehozásához használja a kubectl apply][kubectl-apply] parancsot.

```bash
kubectl apply -f samples-http-application-routing.yaml
```

Az alábbi példa a létrehozott erőforrásokat mutatja be:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Nyisson meg egy webböngészőt *az \<CLUSTER_SPECIFIC_DNS_ZONE\> aks-helloworld.*, például a aks-helloworld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io, és ellenőrizze, hogy látja-e a bemutatóalkalmazást.  Az alkalmazás megjelenése eltarthat néhány percig.

## <a name="remove-http-routing"></a>HTTP-útválasztás eltávolítása

A HTTP-útválasztási megoldás az Azure CLI használatával távolítható el. Futtassa a következő parancsot az AKS-fürt és az erőforráscsoport nevének behelyettesítésével.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Ha a HTTP-alkalmazás-útválasztási bővítmény le van tiltva, előfordulhat, hogy egyes Kubernetes-erőforrások a fürtben maradnak. Ezek az erőforrások *a configMaps* és *a titkos* kulcsok, és a *kube-system névtérben* vannak létrehozva. Ha tiszta fürtöt szeretne fenntartani, előfordulhat, hogy el szeretné távolítani ezeket az erőforrásokat.

Keresse meg *az addon-http-application-routing erőforrásokat* a következő [kubectl get parancsokkal:][kubectl-get]

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Az alábbi példakimenet a törölnie kell a configMaps adatokat jeleníti meg:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Az erőforrások törléséhez használja a [kubectl delete][kubectl-delete] parancsot. Adja meg az erőforrás típusát, az erőforrás nevét és a névteret. Az alábbi példa törli az egyik korábbi konfigurációs leképezőt:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Ismételje meg az előző lépést a fürtben maradt `kubectl delete` összes *addon-http-application-routing* erőforrással.

## <a name="troubleshoot"></a>Hibaelhárítás

A [kubectl logs paranccsal][kubectl-logs] megtekintheti az External-DNS alkalmazás naplóit. A naplóknak meg kell erősítenie, hogy az A és TXT DNS-rekord sikeresen létrejött.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Ezek a rekordok a dns-zóna erőforrásán is láthatók a Azure Portal.

![A DNS-rekordok lekért száma](media/http-routing/clippy.png)

A [kubectl logs paranccsal][kubectl-logs] megtekintheti az Nginx bejövő forgalomvezérlő alkalmazásnaplóit. A naplóknak meg kell erősítenie a bejövőforgalom-erőforrás és a vezérlő `CREATE` újratöltését. A rendszer minden HTTP-tevékenységet naplóz.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Távolítsa el a cikkben létrehozott társított Kubernetes-objektumokat a `kubectl delete` használatával.

```bash
kubectl delete -f samples-http-application-routing.yaml
```

A példakimenet azt mutatja, hogy a Kubernetes-objektumok el vannak távolítva.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Következő lépések

A HTTPS által védett bejövő forgalomvezérlő AKS-ben való telepítéséről a HTTPS bejövő forgalom a Azure Kubernetes Service [(AKS)][ingress-https]oldalon található.

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
