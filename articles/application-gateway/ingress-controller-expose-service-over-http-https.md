---
title: AK-szolgáltatás közzététele HTTP-n vagy HTTPS-en keresztül Application Gateway
description: Ez a cikk azt ismerteti, hogyan tehet elérhetővé egy AK-szolgáltatást HTTP-n vagy HTTPS-en keresztül a Application Gateway használatával.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850352"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>AK-szolgáltatás közzététele HTTP-n vagy HTTPS-en keresztül Application Gateway 

Ezek az oktatóanyagok segítenek megmutatni a Kubernetes beáramlási [erőforrásainak](https://kubernetes.io/docs/concepts/services-networking/ingress/) használatát, hogy egy példaként elérhető Kubernetes szolgáltatást tegyenek elérhetővé az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) keresztül http vagy https használatával.

## <a name="prerequisites"></a>Előfeltételek

- Telepített `ingress-azure` Helm-diagram.
  - [**Zöldmezős üzembe helyezés**](ingress-controller-install-new.md): Ha nem teljesen új forrásból származik, tekintse meg a következő telepítési útmutatót, amely az AK-fürt üzembe helyezésének lépéseit ismerteti Application Gateway és telepítse az Application Gateway beléptetési vezérlőt az AK-fürtön.
  - [**Rozsdaövezetek rehabilitálása üzembe helyezése**](ingress-controller-install-existing.md): Ha rendelkezik meglévő AK-fürttel és Application Gatewaytel, tekintse meg ezeket az utasításokat az Application Gateway beáramlási vezérlőjének az AK-fürtön való telepítéséhez.
- Ha az alkalmazásban HTTPS-t szeretne használni, szüksége lesz egy x509 tanúsítványra és annak titkos kulcsára.

## <a name="deploy-guestbook-application"></a>Alkalmazás üzembe helyezése `guestbook`

A Vendégkönyv alkalmazás egy webes felhasználói felület, egy háttérrendszer és egy Redis-adatbázis összeállítására szolgáló kanonikus Kubernetes-alkalmazás. Alapértelmezés szerint a `guestbook` porton a névvel ellátott szolgáltatáson keresztül teszi elérhetővé az alkalmazást `frontend` `80` . A Kubernetes bejövő erőforrás nélkül a szolgáltatás nem érhető el az AK-fürtön kívülről. Az alkalmazást és a telepítőt a bejövő erőforrásokkal fogjuk használni az alkalmazás HTTP és HTTPS protokollon keresztüli eléréséhez.

A Vendégkönyv alkalmazás üzembe helyezéséhez kövesse az alábbi utasításokat.

1. Letöltés `guestbook-all-in-one.yaml` innen [](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Üzembe helyezés `guestbook-all-in-one.yaml` az AK-fürtön a futtatásával

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Az alkalmazás már `guestbook` telepítve van.

## <a name="expose-services-over-http"></a>Szolgáltatások közzététele HTTP-n keresztül

A Vendégkönyv-alkalmazás elérhetővé tétele érdekében a következő bejövő erőforrást fogjuk használni:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Ez a bejövő `frontend` forgalom a `guestbook-all-in-one` Application Gateway alapértelmezett háttereként teszi elérhetővé az üzemelő példány szolgáltatását.

Mentse a fenti bejövő erőforrást a-ként `ing-guestbook.yaml` .

1. Üzembe helyezés `ing-guestbook.yaml` a futtatásával:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Keresse meg a bejövő adatkezelő vezérlő naplóját a telepítési állapothoz.

Most az `guestbook` alkalmazásnak elérhetőnek kell lennie. Ezt úgy is megtekintheti, ha felkeresi a Application Gateway nyilvános címe.

## <a name="expose-services-over-https"></a>Szolgáltatások közzététele HTTPS-kapcsolaton keresztül

### <a name="without-specified-hostname"></a>Megadott állomásnév nélkül

Az állomásnév meghatározása nélkül a Vendégkönyv szolgáltatás az Application gatewayre mutató összes gazdagépen elérhető lesz.

1. A bejövő forgalom üzembe helyezése előtt létre kell hoznia egy titkos kubernetes a tanúsítvány és a titkos kulcs üzemeltetéséhez. Kubernetes-titkos kulcsot a futtatásával hozhat létre

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Adja meg a következő bejövő forgalmat. A bejövő forgalom mezőben adja meg a titok nevét a (z `secretName` ) szakaszban.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Cserélje le a `<guestbook-secret-name>` fenti bejövő erőforrást a titkos kulcs nevére. A fenti bejövő erőforrások tárolása a fájlnévben `ing-guestbook-tls.yaml` .

1. Üzembe helyezés – Vendégkönyv-TLS. YAML futtatásával

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Keresse meg a bejövő adatkezelő vezérlő naplóját a telepítési állapothoz.

Az alkalmazás mostantól a `guestbook` http és a HTTPS protokollon is elérhető lesz.

### <a name="with-specified-hostname"></a>Megadott állomásnévvel

Az állomásnév a bejövő forgalomban is megadható a multiplex TLS-konfigurációk és-szolgáltatások számára.
Az állomásnév megadásával a Vendégkönyv szolgáltatás csak a megadott gazdagépen lesz elérhető.

1. Adja meg a következő bejövő forgalmat.
    A bejövő forgalom mezőben adja meg a titok nevét a `secretName` szakaszban, és cserélje le az állomásnévt a `hosts` szakaszba.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Üzembe helyezés `ing-guestbook-tls-sni.yaml` futtatásával

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Keresse meg a bejövő adatkezelő vezérlő naplóját a telepítési állapothoz.

Mostantól az `guestbook` alkalmazás csak a megadott gazdagépen, a HTTP-n és a HTTPS-en lesz elérhető ( `<guestbook.contoso.com>` ebben a példában).

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

A következő bejövő forgalom lehetővé teszi további elérési utak hozzáadását ebbe a bejövő forgalomba, és átirányítani ezeket az útvonalakat más szolgáltatásokra:

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
