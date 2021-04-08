---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Maverics Identity Orchestrator SAML-összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Maverics Identity Orchestrator SAML-összekötő között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585094"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Az Azure AD egyszeri bejelentkezés integrálása a Maverics Identity Orchestrator SAML-összekötővel

A rétegek Maverics Identity Orchestrator egyszerű módszert kínál a helyszíni alkalmazások integrálására Azure Active Directory (Azure AD) használatával a hitelesítéshez és a hozzáférés-vezérléshez. A Maverics Orchestrator képes az olyan alkalmazások hitelesítésének és engedélyezésének modernizálására, amelyek jelenleg a fejlécek, a cookie-k és más védett hitelesítési módszerek alapján működnek. A Maverics Orchestrator-példányok a helyszínen vagy a felhőben is üzembe helyezhetők. 

Ez a hibrid hozzáférési oktatóanyag bemutatja, hogyan telepíthet át egy örökölt Web Access Management-termék által védett helyszíni webalkalmazást az Azure AD hitelesítéshez és hozzáférés-vezérléshez való használatához. Az alapszintű lépések a következők:

1. A Maverics Orchestrator beállítása
1. Alkalmazás proxy
1. Vállalati alkalmazás regisztrálása az Azure AD-ben
1. Hitelesítés az Azure-on keresztül és az alkalmazáshoz való hozzáférés engedélyezése
1. Fejlécek hozzáadása a zökkenőmentes alkalmazás-hozzáféréshez
1. Több alkalmazás használata

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egy Maverics Identity Orchestrator SAML-összekötő SSO-kompatibilis előfizetés. A Maverics szoftver beszerzéséhez vegye fel a kapcsolatot a [rétegek értékesítésével](mailto:sales@strata.io).
* Legalább egy olyan alkalmazás, amely fejléc-alapú hitelesítést használ. A példák egy Sonar nevű alkalmazással működnek, amely a (z) helyen található https://app.sonarsystems.com , és egy Connectulum nevű alkalmazás, amely a következő helyen található: https://app.connectulum.com .
* Egy Linux rendszerű gép, amely a Maverics-Orchestrator üzemelteti
  * Operációs rendszer: RHEL 7,7 vagy újabb, CentOS 7 +
  * Lemez: >= 10 GB
  * Memória: >= 4 GB
  * Portok: 22 (SSH/SCP), 443, 7474
  * Rendszergazdai jogosultságok a telepítési/felügyeleti feladatokhoz
  * Hálózati kimenő forgalom a Maverics-identitást futtató kiszolgálóról a védett alkalmazásba Orchestrator

## <a name="step-1-set-up-the-maverics-orchestrator"></a>1. lépés: a Maverics Orchestrator beállítása

### <a name="install-maverics"></a>A Maverics telepítése

1. Szerezze be a legújabb Maverics RPM-t. Másolja a csomagot arra a rendszerre, amelyre telepíteni kívánja a Maverics szoftvert.

1. Telepítse a Maverics csomagot, és cserélje le a fájlnevét a helyére `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   A Maverics telepítése után a szolgáltatás a következőként fog futni: `systemd` . A szolgáltatás futásának ellenőrzéséhez hajtsa végre a következő parancsot:

   `sudo systemctl status maverics`

1. A Orchestrator újraindításához és a naplók követéséhez futtassa a következő parancsot:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

A Maverics telepítése után a rendszer az alapértelmezett `maverics.yaml` fájlt hozza létre a `/etc/maverics` címtárban. Mielőtt szerkeszti a konfigurációt a `appgateways` (z) és a konfigurációban `connectors` , a konfigurációs fájl a következőhöz hasonlóan fog kinézni:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS konfigurálása

A DNS hasznos lesz, így nem kell megemlékeznie a Orchestrator-kiszolgáló IP-címére.

Szerkessze a böngésző számítógépének (laptopjának) hosts fájlját a 12.34.56.78 feltételezett Orchestrator IP-címének használatával. Linux-alapú operációs rendszereken a fájl a következő helyen található: `/etc/hosts` . Windows rendszeren a következő helyen található: `C:\windows\system32\drivers\etc` .

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Annak ellenőrzéséhez, hogy a DNS a várt módon van-e konfigurálva, kérheti a Orchestrator az állapot végpontját. A böngészőből kérjen kérelmet http://sonar.maverics.com:7474/status .

### <a name="configure-tls"></a>TLS konfigurálása

A biztonság fenntartása érdekében fontos, hogy a biztonságos csatornákon keresztül kommunikáljon a Orchestrator. Ennek eléréséhez hozzáadhat egy tanúsítványt/kulcspárt a `tls` szakaszhoz.

Ha önaláírt tanúsítványt és kulcsot szeretne előállítani a Orchestrator-kiszolgálóhoz, futtassa a következő parancsot a `/etc/maverics` címtárból:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Éles környezetekben valószínűleg egy ismert HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványt szeretne használni, hogy elkerülje a figyelmeztetéseket a böngészőben. A [titkosítás](https://letsencrypt.org/) egy jó és ingyenes lehetőség, ha megbízható hitelesítésszolgáltatót keres.

Most használja az újonnan létrehozott tanúsítványt és kulcsot a Orchestrator. A konfigurációs fájlnak most a következő kódot kell tartalmaznia:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Annak ellenőrzéséhez, hogy a TLS a várt módon van-e konfigurálva, indítsa újra a Maverics szolgáltatást, és tegyen egy kérést az állapot végpontjának. A böngészőből kérjen kérelmet https://sonar.maverics.com/status .

## <a name="step-2-proxy-an-application"></a>2. lépés: az alkalmazás proxyja

Ezután konfigurálja az alapszintű proxyt a Orchestrator a használatával `appgateways` . Ez a lépés segít ellenőrizni, hogy a Orchestrator rendelkezik-e a védett alkalmazáshoz szükséges kapcsolattal.

A konfigurációs fájlnak most a következő kódot kell tartalmaznia:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

Annak ellenőrzéséhez, hogy a proxy a várt módon működik-e, indítsa újra a Maverics szolgáltatást, és küldjön egy kérést az alkalmazásnak a Maverics-proxyn keresztül. A böngészőből kérjen kérelmet https://sonar.maverics.com . Igény szerint elvégezheti az adott alkalmazás-erőforrásokra vonatkozó kérést, például a `https://sonar.maverics.com/RESOURCE` `RESOURCE` védett felsőbb rétegbeli alkalmazás érvényes alkalmazásspecifikus erőforrását.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>3. lépés: vállalati alkalmazás regisztrálása az Azure AD-ben

Most hozzon létre egy új vállalati alkalmazást az Azure AD-ben, amelyet a rendszer a végfelhasználók hitelesítéséhez fog használni.

> [!NOTE]
> Az Azure AD-szolgáltatások, például a feltételes hozzáférés használata esetén fontos, hogy helyszíni alkalmazásként hozzon létre egy vállalati alkalmazást. Ez lehetővé teszi az alkalmazáson belüli feltételes hozzáférést, az alkalmazáson belüli kockázat kiértékelését, az alkalmazáshoz hozzárendelt engedélyeket és így tovább. Az Azure AD-ben a vállalati alkalmazások általában egy Azure-összekötőre mutatnak a Maverics-ben.

Vállalati alkalmazás regisztrálása az Azure AD-ben:

1. Az Azure AD-bérlőben lépjen a **vállalati alkalmazások** elemre, majd válassza az **új alkalmazás** lehetőséget. Az Azure AD-katalógusban keresse meg a **Maverics Identity Orchestrator SAML-összekötőt**, majd jelölje ki.

1. A Maverics Identity Orchestrator SAML-összekötő **tulajdonságai** ablaktáblán adja meg a **felhasználó-hozzárendelés szükséges lehetőséget?** a **nem** értékkel engedélyezheti, hogy az alkalmazás működjön a címtárban lévő összes felhasználó számára.

1. Az Maverics Identity Orchestrator SAML-összekötő **Áttekintés** paneljén válassza az **egyszeri bejelentkezés beállítása** lehetőséget, majd válassza az **SAML** lehetőséget.

1. A Maverics Identity Orchestrator SAML-összekötő **SAML-alapú bejelentkezési** paneljén szerkessze az **alapszintű SAML-konfigurációt** a **Szerkesztés** (ceruza ikon) gomb kiválasztásával.

   ![Képernyőkép az "alapszintű SAML-konfiguráció" szerkesztés gombról.](common/edit-urls.png)

1. Adja meg a **entitás azonosítóját** `https://sonar.maverics.com` . Az entitás AZONOSÍTÓjának egyedinek kell lennie a bérlő alkalmazásai között, és tetszőleges érték lehet. Ezt az értéket fogja használni, amikor a `samlEntityID` következő szakaszban meghatározza az Azure-összekötő mezőjét.

1. Adja meg a **Válasz URL-címét** `https://sonar.maverics.com/acs` . Ezt az értéket fogja használni, amikor a `samlConsumerServiceURL` következő szakaszban meghatározza az Azure-összekötő mezőjét.

1. Adja meg a **bejelentkezési URL-címét** `https://sonar.maverics.com/` . A Maverics nem használja ezt a mezőt, de az Azure AD-ben engedélyezni kell, hogy a felhasználók hozzáférhessenek az alkalmazáshoz az Azure AD saját alkalmazások portálján.

1. Kattintson a **Mentés** gombra.

1. Az **SAML aláíró tanúsítvány** szakaszban válassza a **Másolás** gombot az **alkalmazás-összevonási metaadatok URL-címének** másolásához, majd mentse a számítógépre.

   ![Képernyőkép az "SAML aláíró tanúsítvány" másolási gombról.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>4. lépés: hitelesítés az Azure-on keresztül és az alkalmazáshoz való hozzáférés engedélyezése

Ezután helyezze el az imént létrehozott vállalati alkalmazást az Azure Connector Maverics-ben való konfigurálásával. Ez `connectors` a konfiguráció a `idps` blokkmal párosítva lehetővé teszi, hogy a Orchestrator hitelesítse a felhasználókat.

A konfigurációs fájlnak most a következő kódot kell tartalmaznia. Ügyeljen arra, hogy az `METADATA_URL` előző lépésben az alkalmazás-összevonási metaadatok URL-címének értékét cserélje le.

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Annak ellenőrzéséhez, hogy a hitelesítés a várt módon működik-e, indítsa újra a Maverics szolgáltatást, és tegyen kérelmet az alkalmazás-erőforráshoz a Maverics-proxyn keresztül. Az erőforráshoz való hozzáférés előtt át kell irányítani az Azure-ba a hitelesítéshez.

## <a name="step-5-add-headers-for-seamless-application-access"></a>5. lépés: fejlécek hozzáadása a zökkenőmentes alkalmazás-hozzáféréshez

Még nem küld fejléceket a felsőbb szintű alkalmazásnak. Vegyük fel `headers` a kérést, ahogy az a Maverics-proxyn keresztül halad, hogy a felsőbb szintű alkalmazás azonosítsa a felhasználót.

A konfigurációs fájlnak most a következő kódot kell tartalmaznia:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

Annak ellenőrzéséhez, hogy a hitelesítés a várt módon működik-e, kérjen egy kérelmet az alkalmazás-erőforráshoz a Maverics-proxyn keresztül. A védett alkalmazásnak most el kell fogadnia a fejléceket a kérelemben. 

Ha az alkalmazás különböző fejléceket vár, szerkessze a fejléc kulcsait. Az Azure AD-ből az SAML-folyamat részeként visszaérkező jogcímek a fejlécekben használhatók. Megadhat például egy másik fejlécet `secondary_email: azureSonarApp.email` , ahol az az `azureSonarApp` összekötő neve, és `email` Az Azure ad által visszaadott jogcím. 

## <a name="step-6-work-with-multiple-applications"></a>6. lépés: több alkalmazás használata

Most vessünk egy pillantást arra, hogy mi szükséges a különböző gazdagépeken található több alkalmazáshoz való proxyhoz. Ennek a lépésnek a megvalósításához konfiguráljon egy másik app Gatewayt, egy másik vállalati alkalmazást az Azure AD-ben és egy másik összekötőt.

A konfigurációs fájlnak most a következő kódot kell tartalmaznia:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

Előfordulhat, hogy észrevette, hogy a kód felvette a `host` mezőt az App Gateway-definícióba. A `host` mező lehetővé teszi, hogy a Maverics-Orchestrator megkülönböztetni, hogy a felsőbb rétegbeli gazdagép hogyan irányítsa át a forgalmat.

Annak ellenőrzéséhez, hogy az újonnan hozzáadott alkalmazás-átjáró a várt módon működik-e, kérjen meg egy kérést https://connectulum.maverics.com .

## <a name="advanced-scenarios"></a>Speciális forgatókönyvek

### <a name="identity-migration"></a>Identitás-áttelepítés

Nem lehet a teljes körű Web Access Management eszköz, de nem tudja áttelepíteni a felhasználókat a tömeges jelszó-visszaállítások nélkül? A Maverics Orchestrator a használatával támogatja az identitás-áttelepítést `migrationgateways` .

### <a name="web-server-gateways"></a>Webkiszolgáló-átjárók

Nem szeretné újradolgozni a hálózat és a proxy forgalmát a Maverics-Orchestrator? Nem probléma. A Maverics-Orchestrator a webkiszolgáló-átjárókkal (modulok) párosítható, és proxy nélkül is megegyező megoldásokat biztosíthatnak.

## <a name="wrap-up"></a>Becsomagolás

Ekkor telepítette a Maverics-Orchestrator, létrehozott és konfigurált egy vállalati alkalmazást az Azure AD-ben, és konfigurálta a Orchestrator-t egy védett alkalmazásra, miközben hitelesítésre és kényszerítésre vonatkozó házirendet igényel. Ha többet szeretne megtudni arról, hogyan használhatók az Maverics-Orchestrator elosztott Identitáskezelés használatára, [vegye fel a kapcsolatot a rétegek](mailto:sales@strata.io)lehetőséggel.

## <a name="next-steps"></a>Következő lépések

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)
