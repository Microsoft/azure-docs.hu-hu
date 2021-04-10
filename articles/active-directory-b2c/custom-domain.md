---
title: Azure AD B2C egyéni tartományok engedélyezése
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan engedélyezheti az egyéni tartományokat a Azure Active Directory B2C átirányítási URL-címeiben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580164"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Egyéni tartományok engedélyezése Azure Active Directory B2Choz

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Ez a cikk bemutatja, hogyan engedélyezheti az egyéni tartományokat a Azure Active Directory B2C átirányítási URL-címeiben (Azure AD B2C). Ha egyéni tartományt használ az alkalmazással, zökkenőmentesebb felhasználói élményt nyújt. A felhasználó szemszögéből a bejelentkezési folyamat során továbbra is a tartományon belül maradnak, és nem kell átirányítani a Azure AD B2C alapértelmezett tartományra *<bérlő-neve>. b2clogin.com*.

![Egyéni tartomány felhasználói felülete](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Egyéni tartomány áttekintése

Az [Azure bejárati ajtó](https://azure.microsoft.com/services/frontdoor/)használatával engedélyezheti Azure ad B2C egyéni tartományait. Az Azure bejárati ajtó egy globális, méretezhető belépési pont, amely a Microsoft globális peremhálózati hálózatát használja gyors, biztonságos és széles körben méretezhető webalkalmazások létrehozásához. Az Azure bejárati ajtó mögött is megjelenítheti Azure AD B2C tartalmakat, majd beállíthat egy beállítást az Azure bejárati ajtaján, hogy az alkalmazás URL-címében egy egyéni tartományon keresztül továbbítsa a tartalmat.

Az alábbi ábra az Azure bejárati ajtó integrációját mutatja be:

1. Egy alkalmazásból a felhasználó a Bejelentkezés gombra kattint, amely a Azure AD B2C bejelentkezési oldalára kerül. Ez az oldal egyéni tartománynevet határoz meg.
1. A webböngésző feloldja az egyéni tartománynevet az Azure bejárati ajtó IP-címére. A DNS-feloldás során egy egyéni tartománynévvel rendelkező kanonikus név (CNAME) rekord az előtér alapértelmezett előtér-gazdagépére mutat (például: `contoso.azurefd.net` ). 
1. Az egyéni tartományhoz (például) tartozó forgalmat `login.contoso.com` a rendszer a megadott előtér-alapértelmezett előtér-gazdagépre () irányítja `contoso.azurefd.net` .
1. Az Azure bejárati ajtó meghívja Azure AD B2C tartalmat a Azure AD B2C `<tenant-name>.b2clogin.com` alapértelmezett tartomány használatával. Az Azure AD B2C végpontra irányuló kérelem tartalmaz egy egyéni HTTP-fejlécet, amely tartalmazza az eredeti egyéni tartománynevet.
1. Azure AD B2C válaszol a kérelemre a megfelelő tartalom és az eredeti egyéni tartomány megjelenítésével.

![Egyéni tartományi hálózatkezelési diagram](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> A böngészőből az Azure-ba való kapcsolódáskor az IPv6 helyett mindig IPv4-t kell használnia.

Egyéni tartományok használatakor vegye figyelembe a következőket:

- Több egyéni tartományt is beállíthat. A támogatott egyéni tartományok maximális száma: az Azure [ad szolgáltatás korlátai és korlátozásai](../active-directory/enterprise-users/directory-service-limits-restrictions.md) a Azure ad B2C és az [Azure-előfizetések és a szolgáltatási korlátok, kvóták és](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) korlátozások az Azure bejárati ajtón.
- Az Azure bejárati ajtó egy különálló Azure-szolgáltatás, így további költségek is felmerülnek. További információ: a [bejárati ajtó díjszabása](https://azure.microsoft.com/pricing/details/frontdoor).
- Az Azure-beli elülső [webalkalmazási tűzfal](../web-application-firewall/afds/afds-overview.md)használatához meg kell erősítenie, hogy a tűzfal konfigurációja és a szabályok megfelelően működnek-e a Azure ad B2C felhasználói folyamataival.
- Az egyéni tartományok konfigurálása után a felhasználók továbbra is hozzáférhetnek a Azure AD B2C alapértelmezett tartománynevéhez *<bérlő neve>. b2clogin.com* (kivéve, ha egyéni házirendet használ, és letiltja a [hozzáférést](#block-access-to-the-default-domain-name).
- Ha több alkalmazással is rendelkezik, telepítse át őket az egyéni tartományba, mert a böngésző a jelenleg használt tartománynév alatt tárolja a Azure AD B2C munkamenetet.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Egyéni tartománynév hozzáadása a bérlőhöz

Kövesse az [egyéni tartomány Azure ad-ben való hozzáadására és érvényesítésére](../active-directory/fundamentals/add-custom-domain.md)vonatkozó útmutatást. A tartomány ellenőrzése után törölje a létrehozott DNS TXT-rekordot.

> [!IMPORTANT]
> Ezeket a lépéseket mindenképpen jelentkezzen be **Azure ad B2C** -bérlőbe, és válassza ki a **Azure Active Directory** szolgáltatást.

Ellenőrizze a használni kívánt altartományokat. Csak a legfelső szintű tartomány ellenőrzése nem elegendő. Ha például a *login.contoso.com* és a *Account.contoso.com* használatával szeretne bejelentkezni, ellenőriznie kell mindkét altartományt, és nem csak a legfelső szintű tartományi *contoso.com*.  

## <a name="create-a-new-azure-front-door-instance"></a>Új Azure-beli előtérben lévő példány létrehozása

Kövesse az [alkalmazáshoz tartozó bejárati ajtó létrehozásához](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) szükséges lépéseket az előtér-gazdagép és az útválasztási szabályok alapértelmezett beállításainak használatával. 

> [!IMPORTANT]
> Az alábbi lépések elvégzése után jelentkezzen be az 1. lépésben szereplő Azure Portalra, válassza a **könyvtár + előfizetés** lehetőséget, és válassza ki azt a könyvtárat, amely az Azure-előfizetéshez használni kívánt Azure-előfizetést tartalmazza. Ez *nem* lehet a Azure ad B2C bérlőt tartalmazó könyvtár. 

Az **Add a backend (háttér hozzáadása**) lépésben használja az alábbi beállításokat:

* A **háttérbeli gazdagép típusa** beállításnál válassza az **Egyéni gazdagép** lehetőséget.  
* A **háttérbeli gazdagép neve** beállításnál válassza ki a Azure ad B2C végpontjának állomásnevét, <bérlő neve>. b2clogin.com. Például: contoso.b2clogin.com. 
* A **háttérbeli gazdagép fejlécéhez** válassza ki ugyanazt az értéket, amelyet a **háttérbeli gazdagép neveként** kiválasztott.

![Háttér hozzáadása](./media/custom-domain/add-a-backend.png)

Miután hozzáadta a **hátteret** a **háttér-készlethez**, tiltsa le az **állapot**-mintavételt.

![Háttérkészlet hozzáadása](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Egyéni tartomány beállítása az Azure-beli bejárati ajtón

A lépéseket követve [adhat hozzá egyéni tartományt az előtérben](../frontdoor/front-door-custom-domain.md). Az `CNAME` Egyéni tartományhoz tartozó rekord létrehozásakor használja az [Egyéni tartománynév hozzáadása az Azure ad](#add-a-custom-domain-name-to-your-tenant) -lépéshez korábban ellenőrzött egyéni tartománynevet. 

Az Egyéni tartománynév ellenőrzése után válassza az **Egyéni tartománynév HTTPS** lehetőséget. Ezután a **tanúsítványkezelő típusa** területen válassza a [bejárati ajtó felügyelete](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)lehetőséget, vagy [használja a saját tanúsítványát](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

Az alábbi képernyőfelvétel bemutatja, hogyan adhat hozzá egyéni tartományt, és hogyan engedélyezheti a HTTPS-t egy Azure-beli bejárati tanúsítvány használatával.

![Az Azure bejárati ajtó egyéni tartományának beállítása](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>A CORS konfigurálása

Ha [a Azure ad B2C felhasználói FELÜLETET](customize-ui-with-html.md) HTML-sablonnal testreszabja, a CORS-t az egyéni tartománnyal kell [konfigurálnia](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) .

Az Azure Blob Storage-t az alábbi lépésekkel konfigurálhatja a több eredetű erőforrás-megosztáshoz:

1. Az [Azure Portalon](https://portal.azure.com) lépjen a tárfiókra.
1. A menüben válassza a **CORS** lehetőséget.
1. Az **engedélyezett eredetek** mezőben adja meg a következőt: `https://your-domain-name` . Cserélje le a `your-domain-name` nevet a tartománynevére. Például: `https://login.contoso.com`. A bérlő nevének megadásakor használja az összes kisbetűt.
1. Az **engedélyezett módszerek** esetében válassza a mindkettő és a lehetőséget `GET` `OPTIONS` .
1. Az **engedélyezett fejlécek** mezőbe írjon be egy csillagot (*).
1. A közzétett **fejlécek** esetében írjon be egy csillagot (*).
1. A **Max Age** értéknél adja meg a 200 értéket.
1. Kattintson a **Mentés** gombra.

## <a name="configure-your-identity-provider"></a>Az identitás-szolgáltató konfigurálása

Amikor egy felhasználó úgy dönt, hogy bejelentkezik egy közösségi identitás-szolgáltatóval, Azure AD B2C kezdeményez egy engedélyezési kérést, és a felhasználót a kiválasztott identitás-szolgáltatóhoz viszi, hogy elvégezze a bejelentkezési folyamatot. Az engedélyezési kérelem a `redirect_uri` Azure ad B2C alapértelmezett tartománynevét adja meg: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Ha úgy állította be a házirendet, hogy engedélyezze a bejelentkezést egy külső identitás-szolgáltatóval, frissítse a OAuth átirányítási URI azonosítókat az egyéni tartománnyal. A legtöbb identitás-szolgáltató lehetővé teszi, hogy több átirányítási URI-t regisztráljon. Javasoljuk, hogy az átirányítási URI-ket ne cserélje le ahelyett, hogy tesztelje az egyéni házirendet anélkül, hogy befolyásolná az Azure AD B2C alapértelmezett tartománynevet használó alkalmazásokat. 

A következő átirányítási URI-n:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Cserélje le **<Custom-domain-name>** az egyéni tartománynevére.
- Cserélje le **<bérlő nevét>** a bérlő nevére vagy a BÉRLŐi azonosítóra.

Az alábbi példa egy érvényes OAuth átirányítási URI-t mutat be:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Ha a [bérlői azonosító](#optional-use-tenant-id)használatát választja, egy érvényes OAuth átirányítási URI-ja a következőhöz hasonlóan fog kinézni:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Az [SAML-azonosítók](saml-identity-provider-technical-profile.md) metaadatai a következőképpen néznek ki:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Egyéni tartomány tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C** lehetőséget.
1. A **házirendek** területen válassza a **felhasználói folyamatok (házirendek)** lehetőséget.
1. Válasszon ki egy felhasználói folyamatot, majd válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *webapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson **a másolás a vágólapra** elemre.

    ![Az engedélyezési kérelem URI-azonosítójának másolása](./media/custom-domain/user-flow-run-now.png)

1. A **felhasználói folyamat végpontjának** URL-címe alatt cserélje le a Azure ad B2C tartományt (<bérlő-neve>. b2clogin.com) az egyéni tartományba.  
    Például a következő helyett:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    használja

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. Válassza a **felhasználói folyamat futtatása** lehetőséget. A Azure AD B2C szabályzatnak be kell töltenie.
1. Bejelentkezés helyi és közösségi fiókkal egyaránt.
1. Ismételje meg a tesztet a többi házirenddel.

## <a name="configure-your-application"></a>Az alkalmazás konfigurálása 

Az egyéni tartomány konfigurálását és tesztelését követően frissítheti az alkalmazásait, és betöltheti az egyéni tartományt a Azure AD B2C tartomány helyett állomásnévként megadó URL-címet. 

Az egyéni tartományi integráció az Azure AD B2C házirendeket (felhasználói folyamatokat vagy egyéni házirendeket) használó hitelesítési végpontokra vonatkozik a felhasználók hitelesítéséhez. Ezek a végpontok a következőhöz hasonlóak lehetnek:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Cserélje le ezt:
- **Egyéni** tartomány egyéni tartománnyal
- **bérlő neve** a bérlő nevével vagy a bérlő azonosítójával
- **házirend** neve a szabályzat nevével. [További információ a Azure ad B2C szabályzatokról](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Az [SAML-szolgáltató](./saml-service-provider.md) metaadatai a következőhöz hasonlóak lehetnek: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Választható Bérlői azonosító használata

A B2C-bérlő nevét lecserélheti az URL-címre a bérlői azonosító GUID azonosítóval, hogy az URL-címben a "B2C" kifejezésre mutató összes hivatkozást el lehessen távolítani. A bérlői azonosító GUID azonosítóját az Azure Portal B2C Áttekintés oldalán találja.
Írja be például, hogy `https://account.contosobank.co.uk/<tenant ID GUID>/` a `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` helyett

Ha bérlői név helyett a bérlői azonosítót szeretné használni, akkor ügyeljen arra, hogy az **OAuth átirányítási URI** -azonosítókat ennek megfelelően frissítse. További információ: [az identitás-szolgáltató konfigurálása](#configure-your-identity-provider).

### <a name="token-issuance"></a>Jogkivonat kiállítása

A jogkivonat-kiállító neve (ISS) a használatban lévő egyéni tartomány alapján módosítja a jogcímeket. Például:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Az alapértelmezett tartománynév elérésének letiltása

Az egyéni tartomány hozzáadása és az alkalmazás konfigurálása után a felhasználók továbbra is hozzáférhetnek a <bérlő-neve>. b2clogin.com tartományhoz. A hozzáférés megakadályozása érdekében beállíthatja, hogy a házirend az "állomásnév" engedélyezési kérelmét az engedélyezett tartományok listáján tekintse meg. Az állomásnév az URL-címben megjelenő tartománynév. Az állomásnév a `{Context:HostName}` [jogcím-feloldók](claim-resolver-overview.md)használatával érhető el. Ezt követően egyéni hibaüzenetet is beállíthat. 

1. Példa egy feltételes hozzáférési szabályzatra, amely ellenőrzi az állomásnévt a [githubról](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name).
1. Minden fájlban cserélje le a karakterláncot a `yourtenant` Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz `contosob2c.onmicrosoft.com` .
1. Töltse fel a házirend-fájlokat a következő sorrendben: `B2C_1A_TrustFrameworkExtensions_HostName.xml` és `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C egy nem talált lapot ad vissza.

- **Tünet** – az egyéni tartomány konfigurálása után, amikor megpróbál bejelentkezni az egyéni tartományba, a rendszer http 404 hibaüzenetet kap.
- **Lehetséges okok** – ez a probléma kapcsolódhat a DNS-konfigurációhoz vagy az Azure bejárati ajtójának háttér-konfigurációjához. 
- **Megoldás**:  
    1. Győződjön meg arról, hogy az egyéni tartomány [regisztrálva van, és sikeresen ellenőrizve](#add-a-custom-domain-name-to-your-tenant) lett a Azure ad B2C-bérlőben.
    1. Győződjön meg arról, hogy az [egyéni tartomány](../frontdoor/front-door-custom-domain.md) megfelelően van konfigurálva. Az `CNAME` Egyéni tartományhoz tartozó rekordnak az Azure Front Door alapértelmezett előtér-gazdagépére kell mutatnia (például contoso.azurefd.net).
    1. Győződjön meg arról, hogy az [Azure bejárati ajtó háttér-készlete](#set-up-your-custom-domain-on-azure-front-door) a bérlőre mutat, ahol az egyéni tartománynevet állítja be, valamint a felhasználói folyamat vagy az egyéni házirendek tárolási helyét.

### <a name="identify-provider-returns-an-error"></a>A szolgáltató azonosítása hibát ad vissza.

- **Tünet** – az egyéni tartomány konfigurálása után helyi fiókkal is bejelentkezhet. Ha azonban a külső [közösségi vagy vállalati identitás-szolgáltatók](add-identity-provider.md)hitelesítő adataival jelentkezik be, az identitás-szolgáltatók hibaüzenetet küldenek.
- **Lehetséges okok** – ha Azure ad B2C bejelentkezik a felhasználó egy összevont identitás-szolgáltatóval, az átirányítási URI-t adja meg. Az átirányítási URI a végpont, ahová az Identitáskezelő visszaadja a tokent. Az átirányítási URI ugyanaz a tartomány, amelyet az alkalmazás az engedélyezési kérelemmel használ. Ha az átirányítási URI még nincs regisztrálva az identitás-szolgáltatóban, akkor előfordulhat, hogy nem bízik meg az új átirányítási URI-n, ami hibaüzenetet eredményez. 
- **Megoldás** – kövesse az identitás- [szolgáltató konfigurálása](#configure-your-identity-provider) az új átirányítási URI hozzáadásához című szakasz lépéseit. 


## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Használhatom az Azure bejárati ajtó speciális konfigurációját, például a *webalkalmazási tűzfalszabályok szabályait*? 
  
Míg az Azure-beli előtérben speciális konfigurációs beállítások nem támogatottak, a saját felelősségére használhatja őket. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Ha a Futtatás most lehetőséggel próbálkozom a szabályzat futtatásával, miért nem látom az egyéni tartományt?

Másolja az URL-címet, módosítsa a tartománynevet manuálisan, majd illessze be újra a böngészőjébe.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Melyik IP-címet mutatja be a Azure AD B2C? A felhasználó IP-címe vagy az Azure bejárati ajtó IP-címe?

Az Azure bejárati ajtó továbbítja a felhasználó eredeti IP-címét. Ezt az IP-címet fogja látni a naplózási jelentésben vagy az egyéni házirendben.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Használhatok harmadik féltől származó webalkalmazási tűzfalat (WAF) a B2C-vel?

Ha saját webalkalmazási tűzfalat szeretne használni az Azure-beli bejárati ajtó előtt, konfigurálnia és ellenőriznie kell, hogy minden megfelelően működik-e a Azure AD B2C felhasználói folyamataival.

## <a name="next-steps"></a>Következő lépések

További információ a [OAuth-engedélyezési kérelmekről](protocols-overview.md).