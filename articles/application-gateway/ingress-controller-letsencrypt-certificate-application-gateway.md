---
title: LetsEncrypt.org-tanúsítványok használata Application Gateway
description: Ez a cikk azt ismerteti, hogyan szerezhet be tanúsítványt a LetsEncrypt.org, és hogyan használhatja azt a Application Gateway AK-fürtökhöz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807039"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>LetsEncrypt.org-t használó tanúsítványok használata a Application Gateway AK-fürtökhöz

Ez a szakasz úgy konfigurálja az AK-t, hogy kihasználja a [LetsEncrypt.org](https://letsencrypt.org/) , és automatikusan beszerezze a tartományhoz tartozó TLS/SSL-tanúsítványt. A tanúsítvány Application Gateway lesz telepítve, amely SSL/TLS-megszakítást hajt végre az AK-fürtön. Az itt ismertetett beállítás a [CERT-Manager](https://github.com/jetstack/cert-manager) Kubernetes bővítményt használja, amely automatizálja a tanúsítványok létrehozását és felügyeletét.

Az alábbi lépéseket követve telepítse a [tanúsítvány-kezelőt](https://docs.cert-manager.io) a meglévő AK-fürtre.

1. Helm-diagram

    Futtassa a következő szkriptet a `cert-manager` Helm diagram telepítéséhez. Ez a következő lesz:

    - hozzon létre egy új `cert-manager` névteret az AK-ban
    - hozza létre a következő CRDs: tanúsítvány, kérdés, ClusterIssuer, kiállító, rendelés
    - a CERT-Manager diagram telepítése (a docs.cert-manager.io-ből [)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer erőforrás

    Hozzon létre egy `ClusterIssuer` erőforrást. A ahhoz a hitelesítésszolgáltatóhoz szükséges, `cert-manager` `Lets Encrypt` amely az aláírt tanúsítványokat fogja beszerezni.

    A nem névteret erőforrás használatával a `ClusterIssuer` CERT-Manager több névtérből is felhasználható tanúsítványokat állít ki. `Let’s Encrypt` az ACME protokoll használatával ellenőrzi, hogy egy adott tartománynevet és a tanúsítvány kiállítását kívánja-e használni. További részletek a tulajdonságok konfigurálásáról `ClusterIssuer` . [](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer` a utasítja `cert-manager` a tanúsítványokat a `Lets Encrypt` teszteléshez használt átmeneti környezet használatával (a főtanúsítvány a böngésző-és ügyfél-megbízhatósági tárolókban nem található).

    Az alábbi YAML az alapértelmezett kérdés típusa a következő: `http01` . Egyéb kihívások dokumentálva vannak a [Letsencrypt.org típusaival](https://letsencrypt.org/docs/challenge-types/) kapcsolatban

    > [!IMPORTANT] 
    > Frissítés az `<YOUR.EMAIL@ADDRESS>` alábbi YAML

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Alkalmazás üzembe helyezése

    Hozzon létre egy bejövő erőforrást az `guestbook` alkalmazásnak a Application Gateway használatával történő elérhetővé tétele érdekében a tanúsítvány titkosításának lehetővé tétele.

    Győződjön meg arról, hogy Application Gateway rendelkezik egy DNS-névvel rendelkező nyilvános előtéri IP-konfigurációval (az alapértelmezett tartománnyal vagy szolgáltatás kiépítésével, `azure.com` `Azure DNS Zone` valamint saját egyéni tartomány hozzárendelésével).
    Jegyezze fel a jegyzetet `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` , amely azt jelzi, hogy a tanúsítvány-kezelő feldolgozza a címkézett bejövő erőforrásokat.

    > [!IMPORTANT] 
    > Frissítse `<PLACEHOLDERS.COM>` az alábbi YAML a saját tartományával (vagy a Application Gateway egy, például "KH-AKS-ingress.westeurope.cloudapp.Azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Néhány másodperc elteltével a `guestbook` szolgáltatás a Application Gateway HTTPS URL-címen keresztül érhető el az automatikusan kiadott **átmeneti** `Lets Encrypt` tanúsítvány használatával.
    Előfordulhat, hogy a böngésző egy érvénytelen hitelesítésszolgáltatótól figyelmeztet. Az átmeneti tanúsítványt a bocsátja ki `CN=Fake LE Intermediate X1` . Ez azt jelzi, hogy a rendszer a vártnak megfelelően működött, és készen áll az éles tanúsítványra.

4. Éles tanúsítvány

    Az előkészítési tanúsítvány sikeres beállítása után átválthat éles ACME-kiszolgálóra:
    1. Cserélje le az előkészítési jegyzetet a bejövő erőforrásra a következővel: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Törölje az `ClusterIssuer` előző lépésben létrehozott meglévő előkészítést, és hozzon létre egy újat az Acme-kiszolgáló a fenti ClusterIssuer-YAML való lecserélésével. `https://acme-v02.api.letsencrypt.org/directory`

5. Tanúsítvány lejárata és megújítása

    A `Lets Encrypt` tanúsítvány lejárata előtt a `cert-manager` automatikusan frissíti a tanúsítványt a Kubernetes titkos tárolóban. Ezen a Application Gateway ponton a bejövő adatforgalom-vezérlő a Application Gateway konfigurálásához az általa használt bejövő erőforrások által hivatkozott frissített titkos kulcsot fogja alkalmazni.
