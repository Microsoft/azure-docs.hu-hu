---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezés (SSO) integrálása a Maverics Identity Orchestrator SAML-összekötővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory És a Maverics Identity Orchestrator SAML-összekötő között.
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
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599026"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Az Azure AD egyszeri bejelentkezés integrálása a Maverics Identity Orchestrator SAML-összekötővel

A Strata Maverics Identity Orchestrator szolgáltatása egyszerű módszert kínál a helyszíni alkalmazások és az Azure Active Directory (Azure AD) integrálására a hitelesítés és a hozzáférés-vezérlés érdekében. A Maverics Orchestrator képes modernizálni a hitelesítést és az engedélyezést az olyan alkalmazások esetében, amelyek jelenleg fejléceket, cookie-kat és más jogvédett hitelesítési módszereket alkalmaznak. A Maverics Orchestrator-példányok a helyszínen vagy a felhőben is üzembe helyezhetők. 

Ez a hibrid hozzáféréssel kapcsolatos oktatóanyag azt mutatja be, hogyan lehet az Azure AD-t hitelesítésre és hozzáférés-vezérlésre használni az örökölt webes hozzáférés-kezelési termék által jelenleg védett helyszíni webalkalmazások áttelepítésére. Az alapvető lépések a következőek:

1. A Maverics Orchestrator beállítása
1. Alkalmazás proxybeállítása
1. Vállalati alkalmazás regisztrálása az Azure AD-ban
1. Hitelesítés az Azure-on keresztül és az alkalmazáshoz való hozzáférés jogosultsága
1. Fejlécek hozzáadása az alkalmazások zökkenőmentes eléréséhez
1. Több alkalmazással való munka

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)
* Maverics Identity Orchestrator SAML Connector SSO-kompatibilis előfizetés. A Maverics szoftver lekértért lépjen kapcsolatba a [Réteg értékesítési csoportával.](mailto:sales@strata.io)
* Legalább egy fejlécalapú hitelesítést használó alkalmazás. A példák egy Connectulum nevű alkalmazással működnek, amely a következőn `https://app.connectulum.com` található: .
* A Maverics Orchestratort tároló Linux rendszerű gép
  * Operációs rendszer: RHEL 7.7 vagy újabb, CentOS 7+
  * Lemez: >= 10 GB
  * Memória: >= 4 GB
  * Portok: 22 (SSH/SCP), 443, 7474
  * Gyökér hozzáférés a telepítési/felügyeleti feladatokhoz
  * Hálózati forgalom a Maverics Identity Orchestratort üzemeltető kiszolgálóról a védett alkalmazásba

## <a name="step-1-set-up-the-maverics-orchestrator"></a>1. lépés: A Maverics Orchestrator beállítása

### <a name="install-maverics"></a>A Maverics telepítése

1. Szerezze be a legújabb Maverics RPM-et. Másolja a csomagot arra a rendszerre, amelyre telepíteni szeretné a Maverics szoftvert.

1. Telepítse a Maverics-csomagot, és a helyére a saját fájlnevét kell `maverics.rpm` behelyettesítenünk.

   `sudo rpm -Uvf maverics.rpm`

   A Maverics telepítése után az szolgáltatásként fog futni a `systemd` alatt. A szolgáltatás futásának ellenőrzéséhez hajtsa végre a következő parancsot:

   `sudo systemctl status maverics`

1. Az Orchestrator újraindításához és a naplók futtatásához futtassa a következő parancsot:

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

A Maverics telepítése után az alapértelmezett fájl `maverics.yaml` létrejön a `/etc/maverics` könyvtárban. Mielőtt szerkeszti a konfigurációt a és a fájlba, a konfigurációs `appgateways` `connectors` fájl a következő z-hez hasonló lesz:

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS konfigurálása

A DNS hasznos lesz, így nem kell megjegyeznie az Orchestrator-kiszolgáló IP-címét.

Szerkessze a böngészőgép (a laptopja) gazdagépfájlját egy feltételezett, 12.34.56.78-as Orchestrator IP-címmel. Linux-alapú operációs rendszereken ez a fájl a következő helyen található: `/etc/hosts` . Windows rendszeren ez a helyen `C:\windows\system32\drivers\etc` található.

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

Annak megerősítéséhez, hogy a DNS a várt módon van konfigurálva, kérelmet kérhet az Orchestrator állapotvégpontjára. A böngészőből kérje a http://sonar.maverics.com:7474/status kérést.

### <a name="configure-tls"></a>TLS konfigurálása

A biztonság fenntartása érdekében elengedhetetlen a biztonságos csatornákon keresztüli kommunikáció az Orchestratorral való kommunikációhoz. Ehhez hozzáadhat egy tanúsítvány-/kulcspárt a `tls` szakaszban.

Az Orchestrator-kiszolgáló önaírt tanúsítványának és kulcsának létrehozásához futtassa a következő parancsot a `/etc/maverics` könyvtárból:

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> Éles környezetben valószínűleg egy ismert hitelesítésszolgáltató által aláírt tanúsítványt szeretne használni, hogy elkerülje a figyelmeztetéseket a böngészőben. [A Let's Encrypt](https://letsencrypt.org/) egy jó és ingyenes lehetőség, ha megbízható hitelesítésszolgáltatót keres.

Most használja az Orchestrator újonnan létrehozott tanúsítványát és kulcsát. A konfigurációs fájlnak most a következő kódot kell tartalmaznia:

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

Annak megerősítéséhez, hogy a TLS a várt módon van konfigurálva, indítsa újra a Maverics szolgáltatást, és indítson egy kérést az állapot végpontjára.

## <a name="step-2-proxy-an-application"></a>2. lépés: Alkalmazás proxybeállítása

Ezután konfigurálja az alapszintű proxyzást az Orchestratorban a `appgateways` használatával. Ezzel a lépéssel ellenőrizheti, hogy az Orchestrator rendelkezik-e a szükséges kapcsolattal a védett alkalmazással.

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

Annak megerősítéséhez, hogy a proxyzás a várt módon működik, indítsa újra a Maverics szolgáltatást, és a Maverics-proxyn keresztül indítson kérést az alkalmazásnak. Igény szerint kérést is kérhet adott alkalmazás-erőforrásokhoz.

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>3. lépés: Vállalati alkalmazás regisztrálása az Azure AD-ben

Most hozzon létre egy új vállalati alkalmazást az Azure AD-ban, amely a végfelhasználók hitelesítésére lesz használva.

> [!NOTE]
> Ha Azure AD-funkciókat, például feltételes hozzáférést használ, fontos, hogy helyszíni alkalmazásonként hozzon létre egy vállalati alkalmazást. Ez lehetővé teszi az alkalmazásonkénti feltételes hozzáférést, az alkalmazásonkénti kockázatértékelést, az alkalmazásonként hozzárendelt engedélyeket és így tovább. Az Azure AD-beli nagyvállalati alkalmazások általában egy Azure-összekötőhöz csatlakoznak a Mavericsben.

Vállalati alkalmazás regisztrálása az Azure AD-ban:

1. Az Azure AD-bérlőben válassza a Vállalati **alkalmazások** lehetőséget, majd válassza az **Új alkalmazás lehetőséget.** Az Azure AD-katalógusban keressen rá a **Maverics Identity Orchestrator SAML-összekötő** kifejezésre, majd jelölje ki.

1. A Maverics Identity Orchestrator SAML Connector **Properties** (SAML-összekötő tulajdonságai) panelen állítsa a User **assignment required?** (Felhasználó-hozzárendelésre van szükség? ) **gombra,** hogy az alkalmazás a címtár összes felhasználója számára működjön.

1. A Maverics Identity Orchestrator SAML Connector **Overview** (SAML-összekötő áttekintése) panelen válassza az **Egyszeri** bejelentkezés beállítása lehetőséget, majd válassza az **SAML lehetőséget.**

1. A Maverics Identity Orchestrator **SAML-összekötő SAML-alapú** bejelentkezési panelen szerkessze az SAML-alapkonfigurációt a Szerkesztés **(ceruza** ikon) gombra kattintva. 

   ![Képernyőkép az "SamL-alapkonfiguráció" Szerkesztés gombról.](common/edit-urls.png)

1. Adja meg a **entitásazonosítóját.** `https://sonar.maverics.com` Az entitásazonosítónak egyedinek kell lennie a bérlőben lévő alkalmazások között, és tetszőleges érték is lehet. Ezt az értéket akkor fogja használni, amikor a következő szakaszban meghatározza az `samlEntityID` Azure-összekötő mezőjét.

1. Adja meg a **válasz URL-címét.** `https://sonar.maverics.com/acs` Ezt az értéket akkor fogja használni, amikor a következő szakaszban meghatározza az `samlConsumerServiceURL` Azure-összekötő mezőjét.

1. Adja meg a **bejelentkezési URL-címet.** `https://sonar.maverics.com/` Ezt a mezőt a Maverics nem használja, de az Azure AD-ban szükség van rá, hogy a felhasználók az Azure AD Saját alkalmazások portálján keresztül hozzáférjenek az alkalmazáshoz.

1. Kattintson a **Mentés** gombra.

1. Az **SAML aláíró tanúsítvány** szakaszban  kattintson a Másolás gombra az Alkalmazás-összevonási metaadatok **URL-cím** értékének másoláshoz, majd mentse a számítógépére.

   ![Képernyőkép az SAML aláíró tanúsítvány másolása gombról.](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>4. lépés: Hitelesítés az Azure-on keresztül és az alkalmazáshoz való hozzáférés jogosultsága

Következő lépésként helyezze el az újonnan létrehozott vállalati alkalmazást az Azure-összekötő konfigurálásával a Mavericsben. Ez `connectors` a blokkgal párosított konfiguráció lehetővé `idps` teszi, hogy az Orchestrator hitelesítse a felhasználókat.

A konfigurációs fájlnak most a következő kódot kell tartalmaznia. A helyére írja be az előző lépés `METADATA_URL` alkalmazás-összevonási metaadatainak URL-címét.

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

Annak megerősítéséhez, hogy a hitelesítés a várt módon működik, indítsa újra a Maverics szolgáltatást, és a Maverics-proxyn keresztül indítson kérést egy alkalmazás-erőforrásnak. Az erőforrás elérése előtt a rendszer átirányítja az Azure-ba a hitelesítéshez.

## <a name="step-5-add-headers-for-seamless-application-access"></a>5. lépés: Fejlécek hozzáadása az alkalmazások zökkenőmentes eléréséhez

Még nem küld fejléceket a felfelé irányuló alkalmazásnak. Adjuk hozzá a kérelmet a Maverics-proxyn keresztüli áthaladás során, hogy a felfelé irányuló alkalmazás azonosítani tudja `headers` a felhasználót.

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

Annak megerősítéséhez, hogy a hitelesítés a várt módon működik, a Maverics-proxyn keresztül kérést kell kérnie egy alkalmazás-erőforráshoz. A védett alkalmazásnak mostantól fejléceket kell kapnia a kéréshez. 

Ha az alkalmazás eltérő fejléceket vár, nyugodtan szerkessze a fejléckulcsokat. Az SAML-folyamat részeként az Azure AD-ból származó összes jogcím használható fejlécben. Megadhatja például a egy másik fejlécét, ahol az az összekötő neve, a pedig az Azure AD által visszaadott `secondary_email: azureSonarApp.email` `azureSonarApp` `email` jogcím. 

## <a name="step-6-work-with-multiple-applications"></a>6. lépés: Több alkalmazással való munka

Most vessünk egy pillantást arra, hogy mi szükséges ahhoz, hogy több, különböző gazdagépen telepített alkalmazáshoz proxyt használj. Ennek a lépésnek az eléréséhez konfigurálnia kell egy App Gateway, egy másik vállalati alkalmazást az Azure AD-ban és egy másik összekötőt.

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

Talán észrevette, hogy a kód egy mezőt ad hozzá a `host` App Gateway definícióihoz. A mező lehetővé teszi, hogy a Maverics Orchestrator meg tudja különböztetni, hogy melyik `host` upstream gazdagépnek kell proxyforgalmat generálni.

Annak megerősítéséhez, hogy az újonnan App Gateway az elvárt módon működik, kérést kell kérnie a következőnek: `https://connectulum.maverics.com` .

## <a name="advanced-scenarios"></a>Speciális forgatókönyvek

### <a name="identity-migration"></a>Identitás migrálása

Nem tudja használni a webes hozzáférés-kezelési eszközt, de nincs mód arra, hogy tömeges jelszó-visszaállítás nélkül miminálja a felhasználókat? A Maverics Orchestrator a használatával támogatja az identitások migrálását. `migrationgateways`

### <a name="web-server-gateways"></a>Webkiszolgáló-átjárók

Nem szeretné átdolgozni a hálózati és proxyforgalmat a Maverics Orchestratoron keresztül? Ez nem jelent problémát. A Maverics Orchestrator webkiszolgáló-átjárókkal (modulokkal) párosítható, hogy proxy nélkül kínálják ugyanezeket a megoldásokat.

## <a name="wrap-up"></a>Wrap-up

Ezen a ponton telepítette a Maverics Orchestratort, létrehozott és konfigurált egy vállalati alkalmazást az Azure AD-ban, és úgy konfigurálta az Orchestratort, hogy proxyt állítson be egy védett alkalmazáshoz, miközben hitelesítési és érvényesítési szabályzatra van szükség. Ha többet szeretne megtudni arról, hogyan használható a Maverics Orchestrator az elosztott identitáskezelési esetekhez, lépjen kapcsolatba a [Réteggal.](mailto:sales@strata.io)

## <a name="next-steps"></a>Következő lépések

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)
