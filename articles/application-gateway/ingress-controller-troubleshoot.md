---
title: Bejövő Application Gateway-vezérlő hibaelhárítása
description: Ez a cikk a Application Gateway beáramlási vezérlővel kapcsolatos gyakori kérdések és/vagy problémák elhárításával kapcsolatos dokumentációt tartalmaz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874610"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>A bejövő adatkezelővel kapcsolatos gyakori kérdések és problémák elhárítása

A [Azure Cloud Shell](https://shell.azure.com/) a legkényelmesebb módszer az AK-val és a AGIC-telepítéssel kapcsolatos problémák elhárítására. Indítsa el a rendszerhéjt a [shell.Azure.com](https://shell.azure.com/) , vagy kattintson a hivatkozásra:

[![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell elindítása")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Tesztelés egyszerű Kubernetes alkalmazással

Az alábbi lépések feltételezik:
  - Van egy AK-fürtje, amelyen engedélyezve van a speciális hálózatkezelés
  - A AGIC telepítve van az AK-fürtön
  - Már rendelkezik egy Application Gateway az AK-fürttel megosztott VNET

Annak ellenőrzéséhez, hogy a Application Gateway + AK + AGIC telepítése helyesen van-e beállítva, telepítse a legegyszerűbb lehetséges alkalmazást:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Másolja és illessze be az összes sort a fenti szkriptből egy [Azure Cloud Shellba](https://shell.azure.com/). Győződjön meg arról, hogy a teljes parancs másolása megtörtént – a rendszertől kezdve `cat` , és az utolsót is beleértve `EOF` .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Miután az AK-fürt felett sikeresen telepítette az alkalmazást, egy új Pod, szolgáltatás és bejövő forgalom lesz.

A hüvelyek listájának lekérése a [Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide` .
Elvárjuk, hogy létrehozták a "test-agic-app-Pod" nevű Pod-t. IP-címmel fog rendelkezni. Ennek a címnek a Application Gateway VNET belül kell lennie, amely az AK-val együtt használható.

![Képernyőkép az Azure Cloud Shell bash ablakáról, amely a listában a test-agic-app-Pod-t tartalmazó hüvelyek listáját jeleníti meg.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

A szolgáltatások listájának lekérése: `kubectl get services -o wide` . A rendszer a "test-agic-app-Service" nevű szolgáltatást várta.

![Képernyőkép az Azure Cloud Shell bash ablakáról, amely a listában a test-agic-app-Pod-t tartalmazó szolgáltatások listáját jeleníti meg.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

A ingresses listájának beolvasása: `kubectl get ingress` . Elvárjuk, hogy létrejött a "test-agic-app-beáramló" nevű bejövő erőforrás. Az erőforrás "test.agic.contoso.com" állomásnévvel fog rendelkezni.

![Képernyőkép az Azure Cloud Shell bash ablakáról, amely megjeleníti a ingresses listáját, amely tartalmazza a test-agic-app-behatolást a listában.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Az egyik hüvely AGIC lesz. `kubectl get pods` Megjeleníti a hüvelyek listáját, amelyek közül az egyik a "beáramló – Azure" lesz. A pod összes naplójának lekérésével `kubectl logs <name-of-ingress-controller-pod>` ellenőrizheti, hogy sikeres volt-e az üzembe helyezés. A sikeres telepítés a következő sorokat adta hozzá a naplóhoz:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Azt is megteheti, hogy [Cloud Shell](https://shell.azure.com/) csak azokat a sorokat kéri le, amelyeknek a sikeres Application Gateway-konfigurációját jelöli `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` , ahol `<ingress-azure....>` a AGIC Pod pontos nevét kell megadni.

Application Gateway a következő konfigurációt fogja alkalmazni:

- Figyelő: ![ figyelő](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Útválasztási szabály: ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Háttér-készlet:
  - A háttér-címkészlet egyetlen IP-címmel rendelkezik, és a rendszer a korábban megfigyelt Pod IP-címével egyezik meg `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Végül a `cURL` parancsot a [Cloud Shellon](https://shell.azure.com/) belül is használhatja az újonnan telepített ALKALMAZÁShoz való http-kapcsolat létesítéséhez:

1. `kubectl get ingress`A Application Gateway nyilvános IP-címének lekéréséhez használja
2. A `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` használata

![Képernyőkép a bash-ablakról Azure Cloud Shell a cURL parancs sikeresen létrehozta a HTTP-kapcsolatokat a tesztelési alkalmazáshoz.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Az eredmény `HTTP/1.1 200 OK` azt jelzi, hogy a Application Gateway + AK + AGIC rendszer a várt módon működik.


## <a name="inspect-kubernetes-installation"></a>Kubernetes telepítésének vizsgálata

### <a name="pods-services-ingress"></a>Hüvelyek, szolgáltatások, bejövő forgalom
A Application Gateway beáramló vezérlő (AGIC) folyamatosan figyeli a következő Kubernetes-erőforrásokat: [üzembe helyezés](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) vagy [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), szolgáltatás, [bejövő](https://kubernetes.io/docs/concepts/services-networking/ingress/) [forgalom](https://kubernetes.io/docs/concepts/services-networking/service/)


A következőnek kell megfelelnie ahhoz, hogy a AGIC a várt módon működjön:
  1. Az ak-nak egy vagy több kifogástalan állapotú **hüvelyrel** kell rendelkeznie.
     Győződjön meg róla, hogy [Cloud Shell](https://shell.azure.com/) `kubectl get pods -o wide --show-labels` , ha rendelkezik egy Pod `apsnetapp` -val, a kimenet a következőhöz hasonló lehet:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Egy vagy több **szolgáltatás**, amely a fenti hüvelyre hivatkozik a megfelelő `selector` címkék használatával.
     [Cloud Shell](https://shell.azure.com/) ellenőrzése a következővel:`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Bejövő**, jegyzetekkel ellátott `kubernetes.io/ingress.class: azure/application-gateway` , a fenti szolgáltatásra hivatkozó hivatkozás a [Cloud Shell](https://shell.azure.com/) a következővel: `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Tekintse meg a fenti bejövő forgalomhoz tartozó jegyzeteket: `kubectl get ingress aspnetapp -o yaml` (helyettesítse `aspnetapp` be a bejövő forgalom nevét)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     A bejövő erőforrásnak megjegyzéssel kell rendelkeznie `kubernetes.io/ingress.class: azure/application-gateway` .
 

### <a name="verify-observed-namespace"></a>Megfigyelt névtér ellenőrzése

* Szerezze be a meglévő névtereket a Kubernetes-fürtben. Milyen névtérben fut az alkalmazás? A AGIC figyeli ezt a névteret? A megfigyelt névterek megfelelő konfigurálásával kapcsolatban tekintse meg a [több névtér támogatási](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) dokumentációját.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* A AGIC-Pod a névtérben kell lennie `default` (lásd: oszlop `NAMESPACE` ). Egy kifogástalan Pod lenne `Running` az `STATUS` oszlopban. Legalább egy AGIC Pod-nek kell lennie.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Ha a AGIC pod nem kifogástalan állapotú (a `STATUS` fenti parancs oszlopa nem `Running` ):
  - a naplókból megismerheti a következő okokat: `kubectl logs <pod-name>`
  - a pod előző példánya esetében: `kubectl logs <pod-name> --previous`
  - adja meg a pod-t, hogy minél több kontextust kapjon: `kubectl describe pod <pod-name>`


* Rendelkezik Kubernetes- [szolgáltatással](https://kubernetes.io/docs/concepts/services-networking/service/) és a [bejövő](https://kubernetes.io/docs/concepts/services-networking/ingress/) erőforrásokkal?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* A Beérkezők [megjegyzése](https://kubernetes.io/docs/concepts/services-networking/ingress/) : `kubernetes.io/ingress.class: azure/application-gateway` ? A AGIC csak a Kubernetes bejövő erőforrásait figyeli.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* A AGIC bizonyos kritikus hibák esetén Kubernetes-eseményeket bocsát ki. A következőket tekintheti meg:
  - a terminálon keresztül `kubectl get events --sort-by=.metadata.creationTimestamp`
  - a böngészőben a [Kubernetes webes felülete (irányítópult)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) használatával


## <a name="logging-levels"></a>Naplózási szintek

A AGIC 3 naplózási szinttel rendelkezik. Az 1. szint az alapértelmezett érték, amely a naplózási sorok minimális számát mutatja.
Az 5. szint viszont az összes naplót megjeleníti, beleértve az ARM-re alkalmazott konfiguráció fertőtlenített tartalmát is.

A Kubernetes-Közösség 9 naplózási szintet létesített a [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) eszközhöz. Ebben a tárházban ezek közül hármat használunk, hasonló szemantikai megoldásokkal:


| Részletesség | Leírás |
|-----------|-------------|
|  1        | Alapértelmezett naplózási szint; indítási részletek, figyelmeztetések és hibák megjelenítése |
|  3        | Részletes információk az eseményekről és a változásokról; létrehozott objektumok listája |
|  5        | Átadott objektumok naplója; az ARM-re alkalmazott megtisztított JSON-konfiguráció megjelenítése |


A részletességi szintek a `verbosityLevel` [Helm-config. YAML](#sample-helm-config-file) fájlban lévő változón keresztül állíthatók be. Növelje a részletességi szintet, hogy `5` lekérje a JSON-konfigurációt az [ARM](../azure-resource-manager/management/overview.md)-nek:
  - Vegyen fel `verbosityLevel: 5` egy sorba saját maga is a [Helm-config. YAML](#sample-helm-config-file) és telepítse újra
  - naplók beolvasása a `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Példa Helm konfigurációs fájlra
```yaml
# This file contains the essential configs for the ingress controller helm chart

# Verbosity level of the App Gateway Ingress Controller
verbosityLevel: 3

################################################################################
# Specify which application gateway the ingress controller will manage
#
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>

    # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
    # This prohibits AGIC from applying config for any host/path.
    # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
    shared: false

################################################################################
# Specify which kubernetes namespace the ingress controller will watch
# Default value is "default"
# Leaving this variable out or setting it to blank or empty string would
# result in Ingress Controller observing all acessible namespaces.
#
# kubernetes:
#   watchNamespace: <namespace>

################################################################################
# Specify the authentication with Azure Resource Manager
#
# Two authentication methods are available:
# - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
armAuth:
    type: aadPodIdentity
    identityResourceID: <identityResourceId>
    identityClientID:  <identityClientId>

## Alternatively you can use Service Principal credentials
# armAuth:
#    type: servicePrincipal
#    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>

################################################################################
# Specify if the cluster is Kubernetes RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```