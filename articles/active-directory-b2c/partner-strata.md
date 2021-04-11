---
title: Oktatóanyag a Azure Active Directory B2C és a rétegek konfigurálásához
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálható Azure AD B2C hitelesítés a whoIam használatával a felhasználók ellenőrzéséhez
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 49def79c467aa8f2b3fda9688dc77fcb003a34a5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256565"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Oktatóanyag a helyi alkalmazások a rétegek használatával történő védelemének Azure AD B2C bővítéséhez

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure Active Directory (AD) B2C-t a rétegek [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/).
A Maverics Identity Orchestrator kiterjeszti Azure AD B2C a helyszíni alkalmazások védelmére. Bármely Identity rendszerhez kapcsolódik, átlátható módon áttelepíti a felhasználókat és a hitelesítő adatokat, szinkronizálja a házirendeket és a konfigurációkat, valamint absztrakt hitelesítéssel és munkamenet-felügyelettel rendelkezik. A többrétegű vállalatok segítségével az alkalmazások újraírása nélkül lehet gyorsan áttérni az örökölt rendszerről Azure AD B2Cra. A megoldás a következő előnyökkel jár:

- **Ügyfél egyszeri Sign-On (SSO) a helyszíni hibrid alkalmazásokhoz**: Azure ad B2C támogatja az ügyfél egyszeri bejelentkezését a Maverics Identity Orchestrator. A felhasználók a Azure AD B2C vagy közösségi identitás-szolgáltatóban (identitásszolgáltató) üzemeltetett fiókokkal jelentkeznek be. A Maverics olyan alkalmazásokra is kiterjeszti az SSO-t, amelyeket az örökölt identitás-rendszerek (például a Symantec SiteMinder) már korábban védett.

- A **szabványokon alapuló egyszeri bejelentkezések kiterjesztése az alkalmazásokba az újraírás nélkül**: az Azure ad B2C segítségével kezelheti a felhasználói hozzáférést, és engedélyezheti az egyszeri bejelentkezést a Maverics Identity Orchestrator SAML vagy OIDC összekötők használatával.

- **Egyszerű konfiguráció**: Azure ad B2C egy egyszerű, lépésről lépésre haladó felhasználói felületet biztosít a Maverics Identity Orchestrator SAML-vagy OIDC-összekötők Azure ad B2Choz való csatlakoztatásához.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- Egy [Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) egy példánya, amely a Maverics Identity Orchestrator által használt titkokat tárolja. A szolgáltatás Azure AD B2C vagy más attribútum-szolgáltatóhoz, például egy Lightweight Directory Access Protocol-(LDAP-) címtárhoz vagy adatbázishoz való kapcsolódásra szolgál.

- A [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) egy példánya, amely egy Azure-beli virtuális gépen vagy az Ön által választott helyszíni kiszolgálón van telepítve és futtatva. További információ a szoftver beszerzéséről és a telepítési és konfigurációs dokumentációhoz való hozzáférésről: a [rétegek](https://www.strata.io/contact/)

- Egy helyi alkalmazás, amelyet örökölt identitási rendszerről Azure AD B2Cre szeretne áttérni.

## <a name="scenario-description"></a>Forgatókönyv leírása

A rétegek Maverics-integrációja a következő összetevőket tartalmazza:

- **Azure ad B2C**: a felhasználó hitelesítő adatainak ellenőrzéséhez felelős engedélyezési kiszolgáló. A hitelesített felhasználók a Azure AD B2C könyvtárban tárolt helyi fiók használatával férhetnek hozzá a helyszíni alkalmazásokhoz.

- **Külső közösségi vagy vállalati identitásszolgáltató**: bármilyen OpenID Connect-szolgáltató, Facebook, Google vagy GitHub lehet. További információ a [külső idp](./technical-overview.md#external-identity-providers) Azure ad B2C használatával történő használatáról.  

- A **rétegek Maverics Identity Orchestrator**: az a szolgáltatás, amely összehangolja a felhasználói bejelentkezést, és transzparens módon továbbítja az alkalmazásokat a http-fejléceken keresztül.

A következő architektúra-diagram a megvalósítást mutatja be.

![A képen egy Azure AD B2C integráció architektúrája látható a rétegek Maverics, hogy lehetővé váljon a hibrid alkalmazásokhoz való hozzáférés](./media/partner-strata/strata-architecture-diagram.png)

| Lépések | Description |
|:-------|:---------------|
| 1. | A felhasználó a helyszíni üzemeltetett alkalmazás elérésére vonatkozó kérést tesz elérhetővé. A Maverics Identity Orchestrator a felhasználó által az alkalmazásnak benyújtott kérelmet.|
| 2. | A Orchestrator ellenőrzi a felhasználó hitelesítési állapotát. Ha nem kap munkamenet-jogkivonatot, vagy a megadott munkamenet-jogkivonat érvénytelen, a rendszer elküldi a felhasználónak, hogy Azure AD B2C a hitelesítéshez.|
| 3. | Azure AD B2C elküldi a hitelesítési kérést a konfigurált közösségi identitásszolgáltató.|
| 4. | A identitásszolgáltató megválaszolja a felhasználót a hitelesítő adatokhoz. A identitásszolgáltató függően előfordulhat, hogy a felhasználó a többtényezős hitelesítés (MFA) használatát igényli.|
| 5. | A identitásszolgáltató visszaküldi a hitelesítési választ Azure AD B2Cra. Szükség esetén a felhasználó létrehozhat egy helyi fiókot a Azure AD B2C könyvtárban ebben a lépésben.|
| 6. | Azure AD B2C a Orchestrator alkalmazás regisztrációja során megadott végpontra küldi a felhasználói kérést a Azure AD B2C bérlőben.|
| 7. | A Orchestrator kiértékeli a hozzáférési házirendeket, és kiszámítja az alkalmazásnak továbbított HTTP-fejlécekben szerepeltetni kívánt attribútum-értékeket. Ebben a lépésben a Orchestrator további attribútum-szolgáltatóknak is meghívhatják a fejléc értékeinek megfelelő beállításához szükséges információk beolvasását. A Orchestrator beállítja a fejléc értékeit, és elküldi a kérést az alkalmazásnak.|
| 8. | A felhasználó most már hitelesítve van, és hozzáfér az alkalmazáshoz.|

## <a name="get-maverics-identity-orchestrator"></a>Maverics-identitás Orchestrator beolvasása
Ahhoz, hogy a szoftvert használni lehessen a régi helyszíni alkalmazás Azure AD B2Csal való integrálásához, vegye fel a kapcsolatot a [rétegek](https://www.strata.io/contact/)használatába. A szoftver beszerzése után kövesse az alábbi lépéseket a Orchestrator-specifikus előfeltételek meghatározásához, valamint a szükséges telepítési és konfigurációs lépések végrehajtásához.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő konfigurálása

1. **Az alkalmazás regisztrálása**

   a. [Regisztrálja a Orchestrator alkalmazást](./tutorial-register-applications.md?tabs=app-reg-ga) Azure ad B2C bérlőben.
   >[!Note]
   >A Orchestrator-példány konfigurálásakor a bérlő nevét és azonosítóját, az ügyfél-azonosítót, az ügyfél titkos kulcsát, a konfigurált jogcímeket és az átirányítási URI-t kell később megadnia.

   b. Adja meg a Microsoft MS Graph API engedélyeit az alkalmazásaihoz. Az alkalmazásnak a következő engedélyekre lesz szüksége: `offline_access` , `openid` .

   c. Adjon hozzá egy átirányítási URI-t az alkalmazáshoz. Ez az URI megfelel a `oauthRedirectURL` Orchestrator Azure ad B2C-összekötő konfigurációjának paraméterének, például: `https://example.com/oidc-endpoint` .

2. **Felhasználói folyamat létrehozása**: hozzon létre egy [regisztrációs és bejelentkezési felhasználói folyamatot](./tutorial-create-user-flows.md).

3. **Identitásszolgáltató hozzáadása**: dönthet úgy, hogy helyi fiókkal vagy közösségi vagy vállalati [identitásszolgáltató](./add-identity-provider.md)kíván bejelentkezni a felhasználóba.

4. **Felhasználói attribútumok definiálása**: a regisztráció során gyűjteni kívánt attribútumok meghatározása.

5. **Adja meg az alkalmazás jogcímeit**: adja meg az alkalmazásnak az Orchestrator-példányon keresztül visszaadott attribútumokat. A Orchestrator a Azure AD B2C által visszaadott jogcímek attribútumait használja fel, és további attribútumokat kérhet le más csatlakoztatott identitási rendszerekből, például az LDAP-címtárakból és-adatbázisokból. Ezek az attribútumok a HTTP-fejlécekben vannak beállítva, és a felsőbb rétegbeli helyszíni alkalmazásba lesznek küldve.

## <a name="configure-maverics-identity-orchestrator"></a>Maverics-identitás Orchestrator konfigurálása

A következő szakaszokban végigvezeti a Orchestrator-példány konfigurálásához szükséges lépéseken. További támogatás és dokumentációért forduljon a [rétegekhöz](https://www.strata.io/contact/).

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Identity Orchestrator-kiszolgálói követelmények

A Orchestrator-példányt bármely kiszolgálón futtathatja, akár helyszíni, akár nyilvános Felhőbeli infrastruktúrában, például az Azure-ban, az AWS-ben vagy a GCP-ben.

- Operációs rendszer: REHL 7,7 vagy újabb, CentOS 7 +

- Lemez: 10 GB (kicsi)

- Memória: 16 GB

- Portok: 22 (SSH/SCP), 443, 80

- Rendszergazdai jogosultságok a telepítési/felügyeleti feladatokhoz

- A Maverics Identity Orchestrator `maverics` futtató felhasználó `systemd`

- A hálózatról a Maverics Identity Orchestrator üzemeltető kiszolgálóról elérheti az Azure AD-bérlőt.

### <a name="install-maverics-identity-orchestrator"></a>A Maverics Identity Orchestrator telepítése

1. Szerezze be a legújabb Maverics RPM-csomagot. Helyezze a csomagot arra a rendszerre, amelyre telepíteni szeretné a Maverics. Ha a fájlt egy távoli gazdagépre másolja, az [SCP](https://www.ssh.com/ssh/scp/) egy hasznos eszköz.

2. A Maverics csomag telepítéséhez futtassa az alábbi parancsot a fájlnév helyett a következő helyett: `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Alapértelmezés szerint a Maverics telepítve van a `/usr/local/bin` címtárban.

3. A Maverics telepítése után a szolgáltatás a következőként fog futni: `systemd` .  A Maverics szolgáltatás működésének ellenőrzéséhez futtassa a következő parancsot:

   `sudo service maverics status`

  Ha a Orchestrator telepítése sikeres volt, a következőhöz hasonló üzenetnek kell megjelennie:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Ha a Maverics szolgáltatás nem indul el, hajtsa végre a következő parancsot a probléma kivizsgálásához:

   `journalctl --unit=maverics.service --reverse`

   A legutóbbi naplóbejegyzés a kimenet elején fog megjelenni.

A Maverics telepítése után az alapértelmezett `maverics.yaml` fájl jön létre a `/etc/maverics` könyvtárban.

Konfigurálja úgy a Orchestrator, hogy megvédje az alkalmazást. Integrálhatja Azure AD B2C, tárolhat és beolvashatja a titkokat [Azure Key Vaultból](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9). Adja meg azt a helyet, ahol a Orchestrator el kell olvasnia a konfigurációját.

### <a name="supply-configuration-using-environment-variables"></a>Konfiguráció megadása környezeti változók használatával

Adja meg a Orchestrator-példányok konfigurációját környezeti változók használatával.

`MAVERICS_CONFIG`

Ez a környezeti változó megadja a Orchestrator-példányt, amely YAML a konfigurációs fájlokat, és hogy hol találhatók az Indítás vagy az újraindítás során. Állítsa be a környezeti változót a alkalmazásban `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>A Orchestrator TLS-konfigurációjának létrehozása

Az a `tls` mező, amely a `maverics.yaml` Orchestrator-példány által használt Transport Layer biztonsági konfigurációkat deklarálja. Az összekötők a TLS-objektumokat és a Orchestrator-kiszolgálót is használhatják.

A `maverics` kulcs a Orchestrator-kiszolgáló számára van fenntartva. Minden más kulcs elérhető, és használható egy TLS-objektum egy adott összekötőbe való beadásához.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Az Azure AD B2C-összekötő konfigurálása

A rendszerszervezők összekötőket használnak a hitelesítési és az attribútum-szolgáltatókkal való integrációhoz. Ebben az esetben az App Gateway a Azure AD B2C-összekötőt használja a hitelesítéshez és az attribútum-szolgáltatóhoz. A Azure AD B2C a közösségi identitásszolgáltató használja hitelesítésre, majd attribútum-szolgáltatóként viselkedik a Orchestrator, és a HTTP-fejlécekben beállított jogcímeket továbbítja az attribútumoknak.  

Ez az összekötő konfigurációja megfelel a Azure AD B2C bérlőben regisztrált alkalmazásnak.

1. Másolja az ügyfél-azonosítót, a titkos kulcsot és az átirányítási URI-t az alkalmazás konfigurációjában a bérlőben.

2. Adja meg az összekötő nevét, amely itt látható `azureADB2C` , és állítsa be az összekötőt a következőre: `type` `azure` . Jegyezze fel az összekötő nevét, mivel ezt az értéket használja az alábbi egyéb konfigurációs paraméterekben.

3. Ehhez az integrációhoz a értékének a következőnek kell `authType` lennie: `oidc` .

4. Állítsa be az 1. lépésben átmásolt ügyfél-azonosítót a paraméter értékeként `oauthClientID` .

5. Állítsa be az 1. lépésben átmásolt ügyfél-titkos kulcsot a `oauthClientSecret` paraméter értékeként.

6. Állítsa be az 1. lépésben másolt átirányítási URI-t a `oauthRedirectURL` paraméter értékeként.

7. A Azure AD B2C OIDC-összekötő a jól ismert OIDC végpontot használja a metaadatok felderítésére, beleértve az URL-címeket és az aláíró kulcsokat. Adja meg a `oidcWellKnownURL` bérlő végpontjának értékét.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Azure AD B2C meghatározása hitelesítési szolgáltatóként

A hitelesítési szolgáltató határozza meg, hogyan végezheti el a hitelesítést olyan felhasználók számára, akik nem mutatták be az érvényes munkamenetet az alkalmazás-erőforrásra vonatkozó kérelem részeként. A Azure AD B2C-bérlőben lévő konfiguráció meghatározza, hogyan kell megtámadni egy felhasználót a hitelesítő adatokhoz, és további hitelesítési házirendeket alkalmazni. Ha például egy második tényezőt kell megkövetelni a hitelesítési folyamat befejezéséhez, és el kell döntenie, hogy mely jogcímeket kell visszaadnia a Orchestrator alkalmazás-átjárónak a hitelesítés sikeres végrehajtása után.

A értékének meg `authProvider` kell egyeznie az összekötő `name` értékével.

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>A helyszíni alkalmazás Orchestrator-átjáróval való ellátása

A Orchestrator alkalmazás-átjárójának konfigurációja kijelenti, hogy a Azure AD B2C hogyan védik az alkalmazást, és hogy a felhasználók hogyan férhetnek hozzá az alkalmazáshoz.

1. Hozzon létre egy nevet az App Gateway számára. Az alkalmazás azonosítójának felhasználóbarát nevet vagy teljes tartománynevet is használhat.

2. Állítsa be a t `location` . Az itt látható példa az alkalmazás gyökerét használja `/` , azonban az alkalmazás bármely URL-útvonala lehet.

3. Adja meg a védett alkalmazást `upstream` a gazdagépen: Port Convention: `https://example.com:8080` .

4. Állítsa be a hiba és a jogosulatlan lapok értékeit.

5. Adja meg azokat a HTTP-fejléceket és attribútumokat, amelyeket az alkalmazásnak meg kell adni a hitelesítés létrehozásához és az alkalmazáshoz való hozzáférés szabályozásához. A fejlécek nevei tetszőlegesek, és általában az alkalmazás konfigurációjának felelnek meg. Az attribútumok névterét az összekötő adja meg, amely megadja azokat. Az alábbi példában a Azure AD B2C visszaadott értékek az összekötő nevével vannak ellátva, `azureADB2C` ahol az utótag a szükséges értéket tartalmazó attribútum neve, például: `given_name` .

6. Állítsa be a kiértékelni és érvényesíteni kívánt házirendeket. Három művelet van definiálva: `allowUnauthenticated` , `allowAnyAuthenticated` és `allowIfAny` . Minden művelet társítva van, és a szabályzatot a rendszer `resource` kiértékeli `resource` .

>[!NOTE]
>Mind `headers` `policies` a JavaScript, mind a GoLang Service Extensions használatával tetszőleges logikát valósíthat meg, amely jelentősen javítja az alapértelmezett képességeket.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Azure Key Vault használata titkok szolgáltatóként

Fontos, hogy biztosítsa a Orchestrator által a Azure AD B2C és bármely más Identity rendszerhez való kapcsolódáshoz használt titkok védelmét. A Maverics alapértelmezés szerint egyszerű szövegként tölti be a titkos kulcsokat `maverics.yaml` , azonban ebben az oktatóanyagban a Azure Key Vault a Secrets providerként fogja használni.

Az utasításokat követve [hozzon létre egy új Key Vault](../key-vault/secrets/quick-create-portal.md) , amelyet a Orchestrator-példánya titkos szolgáltatóként fog használni. Adja hozzá a titkot a tárolóhoz, és jegyezze fel az `SECRET NAME` adott titkos kulcsot. Például: `AzureADB2CClientSecret`.

Ha egy konfigurációs fájlban titkos kulcsot szeretne deklarálni `maverics.yaml` , zárja be a titkos kódot a szögletes zárójelekkel:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

A szögletes zárójelben megadott értéknek meg kell felelnie a `SECRET NAME` Azure Key Vaultban megadott titkos kulcsnak.

A titkok Azure Key Vaultból való betöltéséhez állítsa be a környezeti változót `MAVERICS_SECRET_PROVIDER` a fájlban a fájlban `/etc/maverics/maverics.env` azure-credentials.jstalálható hitelesítő adatokkal a következő minta használatával:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Minden együtt

Itt látható, hogyan jelenik meg a Orchestrator konfigurációja, amikor befejezi a fent vázolt konfigurációkat.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>A folyamat tesztelése

1. Navigáljon a helyszíni alkalmazás URL-címéhez `https://example.com/sonar/dashboard` .

2. A Orchestrator át kell irányítani a felhasználói folyamat során konfigurált lapra.

3. Válassza ki a identitásszolgáltató a lapon lévő listából.

4. Ha átirányítja a identitásszolgáltató, adja meg a kért hitelesítő adatokat, beleértve az adott identitásszolgáltató által igényelt MFA-tokent is.

5. A sikeres hitelesítés után a rendszer átirányítja Azure AD B2Cra, amely továbbítja az alkalmazás kérését a Orchestrator átirányítási URI-nak.

6. A Orchestrator kiértékeli a házirendeket, kiszámítja a fejléceket, és elküldi a felhasználót a felsőbb rétegbeli alkalmazásnak.  

7. Ekkor meg kell jelennie a kért alkalmazásnak.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
