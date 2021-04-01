---
title: Bejövő Application Gateway vezérlő megjegyzései
description: Ez a cikk a Application Gateway beáramló vezérlőre vonatkozó megjegyzésekkel kapcsolatos dokumentációt tartalmaz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397450"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Jegyzetek Application Gateway bejövő adatkezelőhöz 

## <a name="introductions"></a>Betelepítések

A Kubernetes bejövő erőforrások tetszőleges kulcs/érték párokkal is szerepelhetnek. A AGIC a megjegyzésekre támaszkodik a program Application Gateway szolgáltatásaira, amelyek nem konfigurálhatók a bejövő YAML keresztül. A bejövő jegyzetek a bejövő erőforrásokból származó összes HTTP-beállításra, háttér-készletre és figyelőre vonatkoznak.

## <a name="list-of-supported-annotations"></a>Támogatott jegyzetek listája

A AGIC által megfigyelt bejövő erőforrásokhoz a következővel  kell szerepelnie: `kubernetes.io/ingress.class: azure/application-gateway` . Ezt követően a AGIC a kérdéses bejövő erőforrással fog működni.

| Jegyzet kulcsa | Érték típusa | Alapértelmezett érték | Megengedett értékek
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` másodperc | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` másodperc | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Háttérbeli elérési út előtagja

Ez a jegyzet lehetővé teszi egy bejövő erőforrásban megadott háttérbeli elérési út újraírását az ebben a jegyzetben megadott előtaggal. Ez lehetővé teszi, hogy a felhasználók olyan szolgáltatásokat tegyenek elérhetővé, amelyek végpontja eltér a szolgáltatás bejövő erőforrásokban való megjelenítéséhez használt végpontok neveitől.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
A fenti példában egy megjegyzéssel ellátott bejövő erőforrás van definiálva `go-server-ingress-bkprefix` `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` . A jegyzet azt jelzi, hogy az Application Gateway létrehoz egy HTTP-beállítást, amelynek elérési útja felülbírálja az elérési utat `/hello` `/test/` .

> [!NOTE] 
> A fenti példában csak egy szabály van definiálva. A jegyzetek azonban a teljes beléptetési erőforrásra alkalmazhatók, így ha egy felhasználó több szabályt is definiált, a háttérbeli elérési út előtagját a megadott elérési utakhoz kell beállítani. Így ha egy felhasználó eltérő elérési úttal rendelkező szabályokat szeretne (még ugyanahhoz a szolgáltatáshoz is), különböző bejövő erőforrásokat kell megadnia.

## <a name="tls-redirect"></a>TLS-átirányítás

A [Application Gateway](./redirect-overview.md) konfigurálható a http-URL-címek automatikus átirányítására a https-partnerek számára. Ha ez a jegyzet jelen van, és a TLS megfelelően van konfigurálva, a Kubernetes bejövő vezérlő egy [útválasztási szabályt hoz létre egy átirányítási konfigurációval](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) , és alkalmazza a módosításokat a Application Gateway. A létrehozott átirányítás HTTP lesz `301 Moved Permanently` .

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Kapcsolatok kiürítése

`connection-draining`: Ez a jegyzet lehetővé teszi a felhasználók számára, hogy megadják, hogy engedélyezik-e a kapcsolatok kiürítését.
`connection-draining-timeout`: Ez a jegyzet lehetővé teszi a felhasználóknak az időtúllépés megadását, amely után Application Gateway leállítja a kérelmeket a kiürítési háttér végpontjának.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Cookie-alapú affinitás

Ez a jegyzet lehetővé teszi annak megadását, hogy engedélyezi-e a cookie-alapú affinitást.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Kérelem időtúllépése

Ez a jegyzet lehetővé teszi a kérés időkorlátjának megadását másodpercben, amely után Application Gateway sikertelen lesz a kérés, ha a válasz nem érkezik meg.

### <a name="usage"></a>Használat

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Példa

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Magánhálózati IP-cím használata

Ez a jegyzet lehetővé teszi számunkra, hogy a végpontot a Application Gateway magánhálózati IP-címén tegye elérhetővé.

> [!NOTE]
> * Application Gateway nem támogatja több IP-cím használatát ugyanazon a porton (például: 80/443). Ha a (z) és a (z) AGIC bemenően beáramlik, `appgw.ingress.kubernetes.io/use-private-ip: "false"` `appgw.ingress.kubernetes.io/use-private-ip: "true"` akkor a `HTTP` Application Gateway frissítése sikertelen lesz.
> * A magánhálózati IP-címmel nem rendelkező Application Gateway `appgw.ingress.kubernetes.io/use-private-ip: "true"` figyelmen kívül lesz hagyva a Ingresses. Ez a vezérlő naplóiban jelenik meg, és a ingresses figyelmeztetéssel beáramlik az eseményekbe `NoPrivateIP` .


### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Példa
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Háttérbeli protokoll

Ez a jegyzet lehetővé teszi, hogy megadjuk azt a protokollt, amelyet Application Gateway a hüvelyekkel való kommunikáció során használnia kell. Támogatott protokollok: `http` , `https`

> [!NOTE]
> * Míg az önaláírt tanúsítványokat a Application Gateway támogatja, a AGIC jelenleg csak akkor támogatja, `https` Ha a hüvelyek egy jól ismert hitelesítésszolgáltató által aláírt tanúsítványt használnak.
> * Ügyeljen arra, hogy ne használja a 80-es portot a HTTPS és a 443-es porton a Hüvelyeken HTTP használatával.

### <a name="usage"></a>Használat
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Példa
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```