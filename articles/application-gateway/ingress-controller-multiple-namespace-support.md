---
title: Több névtér használatának engedélyezése Application Gateway beáramló vezérlőhöz
description: Ez a cikk azt ismerteti, hogyan engedélyezhető több névtér támogatása egy Kubernetes-fürtben egy Application Gateway bejövő adatkezelővel.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874593"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Több névtér támogatásának engedélyezése egy AK-fürtön Application Gateway bejövő adatkezelővel

## <a name="motivation"></a>Motiváció
A Kubernetes- [névterek](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) segítségével egy Kubernetes-fürt particionálható és kiosztható egy nagyobb csapat alcsoportjaiba. Ezek az alcsoportok ezután üzembe helyezhetik és kezelhetik az infrastruktúrát az erőforrások, a biztonság, a konfiguráció stb. finomabb szabályozásával. A Kubernetes lehetővé teszi, hogy egy vagy több bejövő erőforrás külön legyen definiálva az egyes névtereken belül.

Az 0,7-es verziótól kezdve az [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) több névtérből is betöltheti az eseményeket, és megfigyelheti azokat. Ha az AK rendszergazdája úgy dönt, hogy az [app Gateway](https://azure.microsoft.com/services/application-gateway/) -t bejövőként használja, az összes névtér ugyanazt a példányt fogja használni Application Gateway. A bejövő hozzáférés-vezérlés egyetlen telepítése figyeli a hozzáférhető névtereket, és konfigurálja a Application Gateway társítva lesz.

A AGIC 0,7-es verziója továbbra is kizárólag a `default` névteret vizsgálja meg, kivéve, ha ezt explicit módon egy vagy több különböző névtérre változtatja a Helm konfigurációjában (lásd az alábbi szakaszt).

## <a name="enable-multiple-namespace-support"></a>Több névtér támogatásának engedélyezése
Több névtér támogatásának engedélyezése:
1. módosítsa a [Helm-config. YAML](#sample-helm-config-file) fájlt az alábbi módszerek egyikével:
   - törölje a `watchNamespace` kulcsot teljes egészében a [Helm-config fájlból. YAML](#sample-helm-config-file) – a AGIC minden névteret figyelembe vesz
   - `watchNamespace`üres karakterláncra van beállítva – a AGIC minden névteret betart
   - több névtér hozzáadása vesszővel ( `watchNamespace: default,secondNamespace` ) – a AGIC csak a következő névtereket veszi figyelembe:
2. a Helm-sablon módosításainak alkalmazása a következővel: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Az üzembe helyezést követően több névteret is megfigyelheti, a AGIC a következőket teszi:
  - az összes elérhető névtér bejövő erőforrásainak listázása
  - Szűrés a bejövő erőforrásokhoz megjegyzésekkel `kubernetes.io/ingress.class: azure/application-gateway`
  - kombinált [Application Gateway konfigurációjának](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) összeállítása
  - a konfiguráció alkalmazása a társított Application Gateway a [ARM](../azure-resource-manager/management/overview.md) használatával

## <a name="conflicting-configurations"></a>Ütköző konfigurációk
Több névteres bemenő [erőforrás](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) is UTASÍTHATJA a AGIC, hogy ütköző konfigurációkat hozzon létre egyetlen Application Gateway számára. (Két ingresses, amely ugyanazt a tartományt igényli a példányhoz.)

A hierarchia- **figyelők** (IP-cím, port és gazdagép) és az **útválasztási szabályok** (a kötési figyelő, a háttér-készlet és a http-beállítások) felső részén több névtér/ingresses is létrehozhatók és megoszthatók.

A többi elérési úton, a háttér-készletek, a HTTP-beállítások és a TLS-tanúsítványok csak egyetlen névtérből hozhatók létre, és a rendszer eltávolítja a duplikált elemeket.

Tegyük fel például, hogy a következő duplikált bejövő erőforrások definiált névtereket `staging` és `production` a `www.contoso.com` :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Annak ellenére, hogy a bejövő erőforrások felé irányuló forgalmat a rendszer `www.contoso.com` a megfelelő Kubernetes-névterekhez irányítja, csak egy háttérrendszer képes a forgalom kiszolgálására. A AGIC létrehoz egy konfigurációt az egyik erőforrásra vonatkozóan az "első alkalommal kiszolgált" alapján. Ha egyszerre két ingresses-erőforrást hoz létre, akkor az ábécében az egyik korábbi is elsőbbséget élvez. A fenti példában csak a bejövő adatokra vonatkozó beállításokat lehet létrehozni `production` . A Application Gateway a következő erőforrásokkal lesz konfigurálva:

  - Hallgató `fl-www.contoso.com-80`
  - Útválasztási szabály: `rr-www.contoso.com-80`
  - Háttér-készlet: `pool-production-contoso-web-service-80-bp-80`
  - HTTP-beállítások: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Állapot mintavétele: `pb-production-contoso-web-service-80-websocket-ingress`

Vegye figyelembe, hogy a *figyelő* és az *útválasztási szabály* kivételével a létrehozott Application Gateway erőforrások közé tartozik annak a névtérnek () a neve, amelyhez létre lettek `production` hozva.

Ha a két bejövő erőforrás be van vezetve az AK-fürtbe különböző időpontokban, valószínű, hogy a AGIC, amikor újrakonfigurálja Application Gateway és átirányítja a forgalmat a rendszerből `namespace-B` `namespace-A` .

Ha például először adta hozzá a `staging` -t, a AGIC úgy konfigurálja Application Gateway, hogy átirányítsa a forgalmat az átmeneti háttérrendszer-készletbe. Egy későbbi fázisban a `production` Bejövő forgalom bevezetése a AGIC újraprogramozását eredményezi Application Gateway, amely a háttér-készletbe irányítja a forgalmat `production` .

## <a name="restrict-access-to-namespaces"></a>Névterek elérésének korlátozása
Alapértelmezés szerint a AGIC a megadott névtéren belüli jegyzett bejövő forgalom alapján konfigurálja Application Gateway. Ha korlátozni szeretné ezt a viselkedést, a következő lehetőségek közül választhat:
  - korlátozza a névtereket, ha explicit módon definiálja a névtereket, a AGIC a `watchNamespace` [Helm-config](#sample-helm-config-file) YAML kulcsán keresztül kell megfigyelnie. YAML
  - a [szerepkörök és RoleBinding](../aks/azure-ad-rbac.md) használata a AGIC meghatározott névterekre való korlátozásához

## <a name="sample-helm-config-file"></a>Példa Helm konfigurációs fájlra

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