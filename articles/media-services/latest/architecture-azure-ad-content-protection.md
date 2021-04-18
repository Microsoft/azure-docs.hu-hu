---
title: Végpontok között található tartalomvédelem az Azure AD használatával
description: Ez a cikk bemutatja, hogyan védheti meg a tartalmakat Azure Media Services és Azure Active Directory
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 9c81a9b48ff9fa305385c45266d88deb4047f70f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599485"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Oktatóanyag: Végpontok között tartalomvédelem az Azure AD használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ebben az oktatóanyagban és a biztosított lejátszómintában teljes médiatartalom-védelmi alrendszert állíthat be az Azure Media Services (AMS) és az Azure Active Directory (AAD) szolgáltatásban, hogy médiatartalmakat streamelje az AMS által támogatott DRM/AES-128, streamelési protokollok, kodek és tárolóformátumok használatával. A minta elég általános ahhoz, hogy biztonságos hozzáférést biztosítsunk az OAuth 2 által védett bármely REST API a Kódcsere (PKCE) hitelesítési kódfolyamán keresztül. (Azure Media Services szolgáltatás csak egy ilyen szolgáltatás.) Emellett a Microsoft Graph API-val vagy az OAuth 2 engedélyezési kódfolyamával REST API fejlesztett egyéni szolgáltatásokkal is működik. Ez a mintakód [társdokumentuma.](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> * Vegye figyelembe a hitelesítési követelményeket
> * Az alkalmazás működése
> * Háttérbeli erőforrás-alkalmazás regisztrálása
> * Ügyfélalkalmazás regisztrálása
> * A Media Services-fiók tartalomkulcs-szabályzatának és streamelési szabályzatának beállítása
> * A lejátszóalkalmazás beállítása

Ha nem rendelkezik előfizetéssel Azure Media Services hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) Azure-próbafiókot, majd hozzon létre egy Media Services fiókot.

### <a name="duration"></a>Időtartam
Az oktatóanyag befejezése körülbelül két órát vehet igénybe, miután az előfeltételként szükséges technológia használatra kész.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi technológiai verziókat és fogalmakat használjuk. Javasoljuk, hogy az oktatóanyag megkezdése előtt ismerkedjen meg velük.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó előfeltételek

Az oktatóanyag megkezdése előtt nem kötelező, de ajánlott, hogy tisztában van az alábbi fogalmakkal:

* Digital Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* AMS [tartalomkulcs-szabályzatok](drm-content-key-policy-concept.md) az AMS API 3-as, Azure Portal- vagy [Azure Media Services Explorer (AMSE) eszköz használatával](https://github.com/Azure/Azure-Media-Services-Explorer)
* Azure AD v2-végpontok a [Microsoft Identity Platformon](../../active-directory/develop/index.yml)
* Modern felhőalapú hitelesítés, például [OAuth 2.0 és OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Engedélyezési kódfolyam az OAuth 2.0-ban és](../../active-directory/develop/v2-oauth2-auth-code-flow.md) A PKCE-hez miért van szükség
  * [Delegált engedély és alkalmazásengedély](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT-jogkivonat,](../../active-directory/develop/access-tokens.md)jogcímek és aláírókulcs-visszaállítás (a mintában szerepel)

### <a name="prerequisite-code-and-installations"></a>Előfeltételként szükséges kód és telepítések

* A mintakód. Töltse le [a mintakódot.](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)
* A kód Visual Studio telepítése. Töltse le Visual Studio Code-kódot [https://code.visualstudio.com/download](https://code.visualstudio.com/download) itt: .
* A Node.js. A Node.js itt tölthető [https://nodejs.org](https://nodejs.org) le. Az NPM a telepítéssel együtt jár.
* Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
* Egy Azure Media Services (AMS)-fiók.
* @azure/msal-browser 2.0-s verziója, a [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK-család tagjainak egyike a különböző ügyfélplatformok esetében
* Az alkalmazás legújabb [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)(a mintában található).)
* AZ Apple HITELESÍTŐ adatai, ha a FairPlay DRM-et és a CORS által üzemeltetett, ügyféloldali JavaScripten keresztül elérhető alkalmazás-tanúsítványt szeretné tartalmazni.

> [!IMPORTANT]
> Ez az oktatóanyag a .NET-et használja a tartalomkulcs-szabályzat korlátozásának létrehozásához.  Ha Ön nem .NET-fejlesztő, de Node.js szeretne csatlakozni az Azure Media Services-hoz, olvassa el a Connect [to Media Services v3 API - Node.js](configure-connect-nodejs-howto.md)(Csatlakozás Media Services v3 API-hoz – Node.js. Egy új Node.js is elérhető a kulcsváltás automatikus kezeléséhez. Lásd: Node.js [passport-ad modul.](https://github.com/AzureAD/passport-azure-ad)

## <a name="consider-the-authentication-and-authorization-requirements"></a>Vegye figyelembe a hitelesítési és engedélyezési követelményeket

Az alrendszer megtervezésekor néhány kihívással kell szembesülni. Több mozgó részből áll, az ügyfélalkalmazások korlátozásokkal és az Azure AD-kulcsváltással, amely hat hetente történik.

Az Single-Page használt alkalmazás (SPA) figyelembe veszi a hitelesítési követelményekkel kapcsolatos kihívásokat és az alábbi korlátozásokat. A következőket használja:

* Az Azure AD v2-végpontok, mint az Azure AD fejlesztői platform (v1-végpontok) a Microsoft Identity Platformra (v2-végpontok) változnak.
* Engedélyezési kódfolyam, mert az OAuth 2 implicit engedélyezési folyamat elavult.
* Olyan alkalmazás, amelyre a következő korlátozások vonatkoznak:
    * A nyilvános ügyfelek nem rejtik el az ügyfél titkos titkos ját.  Az engedélyezési kódfolyamhoz önmagában szükség van az ügyfél titkos kódának elrejtésére, ezért A PKCE-t engedélyező kódfolyamot kell használni.
    * Böngészőalapú alkalmazás, amely a böngésző biztonsági védőfala (CORS/előzetes korlátozás) hatálya alá esik.
    * Egy modern JavaScriptet használó böngészőalapú alkalmazás, amely a JavaScript biztonsági korlátozásai (egyéni fejléc akadálymentessége, korrelációs azonosító) vonatkozik rá.

## <a name="understand-the-subsystem-design"></a>Az alrendszer kialakításának a leírása

Az alrendszer kialakítása az alábbi ábrán látható.  Három rétegből áll:

* Háttérréteg (fekete színben) a tartalomkulcs-szabályzat konfigurálásához és a tartalom streameléshez való közzétételéhez
* Nyilvános végpontok (kék színben), amelyek a lejátszó/ügyfél felé néző végpontok, amelyek hitelesítést, engedélyezést, DRM-licencet és titkosított tartalmakat szolgáltatásokat license (DRM) szolgáltatásokat nak
* Lejátszóalkalmazás (világoskék színben), amely az összes összetevőt és
    * az Azure AD-n keresztüli felhasználói hitelesítést kezeli.
    * A kezeli access_token Azure AD-től való beszerzését.
    * A jegyzékfájlt és titkosított tartalmat fogad az AMS-től/CDN-től.
    * drm-licencet szerez be a Azure Media Services.
    * A kezeli a tartalom visszafejtése, dekódolása és megjelenítése.

![JWT-jogkivonatok elemzési képernyője](media/aad-ams-content-protection/subsystem.svg)

További [információ az alrendszerről: Több DRM-tartalomvédelmi](./architecture-design-multi-drm-system.md) rendszer tervezése hozzáférés-vezérléssel.

## <a name="understand-the-single-page-app"></a>Az egyoldalas alkalmazás

A lejátszóalkalmazás egy egyoldalas alkalmazás (SPA), amely a Visual Studio kódban a következővel van kifejlesztve:

* Node.js ES 6 JavaScripttel
* @azure/msal-browser 2.0 bétaverzió
* Azure Media Player SDK
* OAuth 2-folyamat az Azure AD v2-végpontokkal (Microsoft Identity Platform)

Az SPA lejátszóalkalmazás a következő műveleteket követi:

* Felhasználók hitelesítése a bérlő natív felhasználói számára, valamint vendégfelhasználók más AAD-bérlőkből vagy MSA-fiókokból. A felhasználók dönthetnek úgy, hogy egy böngésző előugró ablakán vagy átirányításán keresztül jelentkeznek be (olyan böngészők esetén, amelyek nem engedélyezik az előugró ablakokat, például a Safarit).
* Beszerzés engedélyezési `access_token` kódfolyamon keresztül a PKCE-val.
* Megújítása (az AAD által kiadott jogkivonatok 1 órán át `access_token` érvényesek), amelyekhez `refresh_token` szintén be kell szerezni.
* JWT-jogkivonatok (mind a , mind `access_token` `id_token` a ) elemezése teszteléshez/vizsgálathoz.
* DRM-licencek beszerzése mindhárom DRM-hez vagy AES-128 tartalomkulcshoz.
* Tartalmak streamelése a DRM különböző kombinációi és a streamelési protokoll és a tárolóformátum között. A rendszer minden kombinációhoz a megfelelő formátumsringet generálja.
* Visszafejtés, dekódolás és megjelenítés.
* Microsoft Graph API-hívásokat hibaelhárítási célokra. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

A bejelentkezés, a jogkivonat beszerzésének, a jogkivonat megújításának és a jogkivonat megjelenítésének képernyője:

 ![A bejelentkezést, a jogkivonat beszerzését, a jogkivonat megújítását és a jogkivonat megjelenítését megjelenítő képernyő](media/aad-ams-content-protection/token-acquisition.png)

A JWT-jogkivonatok (access_token vagy id_token):

![J W T-jogkivonatok elemzési képernyőképe.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

A DRM/AES és a streamelési protokollok és a tárolóformátum különböző kombinációit tartalmazó védett tartalmak tesztelésére szolgáló képernyő:

![Képernyőkép a D R M vagy A E S különböző kombinációival és a streamelési protokollok és a tárolóformátumok különböző kombinációival tesztelt védett tartalmakról](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Azure AD-bérlő kiválasztása

> [!NOTE]
> Ettől a lépéstől azt feltételezzük, hogy bejelentkezett a Azure Portal és már rendelkezik legalább egy Azure AD-bérlővel.

Válasszon ki egy Azure AD-bérlőt a teljes mintaalkalmazáshoz. Erre két lehetősége van:

* Egy meglévő Azure AD-bérlő. Minden Azure-előfizetésnek egy Azure AD-bérlővel kell lennie, de egy Azure AD-bérlőt több Azure-előfizetés is használhat.
* Egy új Azure AD-bérlő, *amelyet egyik Azure-előfizetés* sem használ. Ha az új bérlőt választja, a Media Service-fióknak és a mintalejátszó alkalmazásnak egy külön Azure AD-bérlőt használó Azure-előfizetésben kell lennie. Ez némi rugalmasságot biztosít. Használhatja például a saját Azure AD-bérlőt, de az ügyfél médiaszolgáltatás-fiókját is az ügyfél Azure-előfizetésében.

## <a name="register-the-backend-resource-app"></a>A háttérerőforrás-alkalmazás regisztrálása

1. Keresse meg a kiválasztott vagy létrehozott Azure AD-bérlőt.
1. Válassza **Azure Active Directory** lehetőséget a menüből.
1. Válassza **Alkalmazásregisztrációk** lehetőséget a menüből.
1. Kattintson **az + Új regisztráció elemre.**
1. Az alkalmazásnak adja a *LicenseDeliveryResource2* nevet (ahol a 2 az AAD v2-végpontokat jelöli).
1. Válassza **a Csak ebben a szervezeti címtárban ([ a bérlő *neve*] csak egyetlen** bérlő) lehetőséget. Ha több bérlő számára is engedélyezni szeretné a hozzáférést, válasszon egyet a többi több-bérlős lehetőségek közül.
1. Az **Átirányítási URI** nem kötelező, és később módosítható.
1. Kattintson a **Regisztrálás** parancsra. Megjelenik Alkalmazásregisztrációk nézet.
1. A menüben válassza a **Jegyzékfájl** lehetőséget. Megjelenik a Jegyzéknézet.
1. Módosítsa a értékét `accessTokenAcceptedVersion` *2-re* (idézőjelek nélkül).
1. Módosítsa a értékét `groupMembershipClaims` *"SecurityGroup"* értékre (idézőjelekkel).
1. Kattintson a **Mentés** gombra.
1. Válassza **az API megjelenítése lehetőséget** a menüben. Megjelenik a Hatókör hozzáadása nézet. (Az Azure biztosít egy alkalmazásazonosító URI-t, de ha módosítani szeretné, az Alkalmazásazonosító URI mezőben szerkesztheti.)
1. Kattintson a **Mentés gombra, és folytassa a gombra.** A nézet megváltozik. Az alábbi táblázat Beállítás oszlopában található beállítások mindegyikéhez adja meg az értéket az Érték oszlopban, majd kattintson a **Hatókör hozzáadása lehetőségre.**

| Beállítás | Érték | Leírás |
| ------- | ----- | ----------- |
| Hatókör neve | *Drm. License.Delivery* | A hatókör megjelenése az API-hoz való hozzáférés kérésekor, illetve a hozzáférési jogkivonatok között, ha a hatókört megadták egy ügyfélalkalmazásnak. Ennek egyedinek kell lennie az alkalmazásban. Ajánlott eljárás a "resource.operation.constraint" minta használata a név létrehozásához. |
| Ki járulhat hozzá? | *Rendszergazdák és felhasználók* | Meghatározza, hogy a felhasználók beleegyeznek-e ebbe a hatókörbe az olyan könyvtárakban, amelyekben engedélyezve van a felhasználói jóváhagyás. |
| Rendszergazdai hozzájárulás megjelenítendő neve | *DRM-licenckézbesítés* | A hatókör neve a hozzájárulási képernyőn, amikor a rendszergazdák hozzájárulnak ehhez a hatókörhöz. |
| Rendszergazdai jóváhagyás leírása** | *DRM-licencküldetés háttérerőforrás-hatóköre* | A hatókör részletes leírása, amely akkor jelenik meg, ha a bérlői rendszergazdák kibontják a hatókört a hozzájárulási képernyőn. |
| Felhasználói jóváhagyás megjelenítendő neve | *Drm. License.Delivery* | A hatókör neve a hozzájárulási képernyőn, amikor a felhasználók hozzájárulnak ehhez a hatókörhöz. |
| Felhasználói jóváhagyás leírása | *DRM-licencküldetés háttérerőforrás-hatóköre* | Ez a hatókör részletes leírása, amely akkor jelenik meg, amikor a felhasználók kibontják a hatókört a hozzájárulási képernyőn. |
| Állapot | *Engedélyezve* | Meghatározza, hogy ez a hatókör elérhető-e az ügyfelek számára a lekéréshez. Állítsa "Letiltva" beállításra az ügyfelek számára nem látható hatókörök esetében. Csak a letiltott hatókörök törölhetők, ezért javasoljuk, hogy a törlés előtt legalább egy hetet várnia kell, hogy az ügyfelek továbbra is használják azt. |

## <a name="register-the-client-app"></a>Az ügyfélalkalmazás regisztrálása

1. Keresse meg a kiválasztott vagy létrehozott Azure AD-bérlőt.
1. Válassza **Azure Active Directory** lehetőséget a menüből.
1. Válassza **Alkalmazásregisztrációk** lehetőséget a menüből.
1. Kattintson **az + Új regisztráció elemre.**
1. Nevezze el az ügyfélalkalmazást, például *AMS AAD-Content Protection.*
1. Válassza **a Csak ebben a szervezeti címtárban ([ a bérlő *neve*] csak egyetlen bérlő) lehetőséget.** Ha több bérlő számára is engedélyezni szeretné a hozzáférést, válasszon egyet a többi több-bérlős lehetőségek közül.
1. Az **Átirányítási URI** nem kötelező, és később módosítható.
1. Kattintson a **Regisztrálás** parancsra.
1. Válassza **az API-engedélyek** lehetőséget a menüben.
1. Kattintson **a + Engedély hozzáadása elemre.** Megnyílik a Request API permissions (API-engedélyek kérése) nézet.
1. Kattintson a **Saját API fülre,** és válassza ki az előző szakaszban létrehozott *LicenseDeliveryResource2* alkalmazást.
1. Kattintson a DRM nyílra, és ellenőrizze a *DRM-et. License.Delivery* engedély.
1. Kattintson **az Engedélyek hozzáadása elemre.** Az Engedélyek hozzáadása nézet bezárul.
1. A menüben válassza a **Jegyzékfájl** lehetőséget. Megjelenik a Jegyzéknézet.
1. Keresse meg és adja hozzá a következő értékpárokat az `replyUrlsWithType` attribútumhoz:

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > Ezen a ponton még nincs meg a lejátszóalkalmazás URL-címe.  Ha az alkalmazást a localhost webkiszolgálóról futtatja, használhatja csak a localhost értékpárt. A lejátszóalkalmazás üzembe helyezése után itt használhatja az üzembe helyezett URL-címet.  Ha elfelejti ezt megtenni, hibaüzenet jelenik meg az Azure AD-bejelentkezésben.

1. Kattintson a **Mentés** gombra.
1. Végül a konfiguráció helyességét a Hitelesítés lehetőséget **választva ellenőrizheti.**  Megjelenik a Hitelesítés nézet. Az ügyfélalkalmazás egyoldalas alkalmazásként (SPA) lesz listázva, az átirányítási URI megjelenik, a engedélyezés típusa pedig Authorization Code Flow with PKCE (Hitelesítési kódfolyam PKCE-val).

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>Az Media Services-fiók tartalomkulcs-szabályzatának és streamelési szabályzatának beállítása

**Ez a szakasz feltételezi, hogy Ön .NET-fejlesztő, és ismeri az AMS v3 API használatát.**

> [!NOTE]
> A jelen cikk írásakor nem használhatja a Azure Portal-t a Media Services-fiókkulcs szabályzatának beállításához, mert az nem támogatja az aszimmetrikus jogkivonat-aláírókulcs openID-Config használatával való használatát.  A beállításnak támogatnia kell az Azure AD-kulcsváltást, mert az Azure AD által kiadott jogkivonatot egy aszimmetrikus kulcs írta alá, és a kulcs hat hetente le lesz állítva. Ezért ez az oktatóanyag a .NET-et és az AMS v3 API-t használja.

A DRM és [](stream-streaming-policy-concept.md) az AES-128 tartalomkulcs-szabályzatának és streamelési szabályzatának konfigurálása érvényes. [](drm-content-key-policy-concept.md)  Módosítsa a `ContentKeyPolicyRestriction` tartalmát a tartalomkulcs-szabályzatban.

Az alábbiakban a tartalomkulcs-szabályzat korlátozásának létrehozására vonatkozó .NET-kódot olvashatja.

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

Módosítsa a `ida_AADOpenIdDiscoveryDocument` fenti kód , és `ida_audience` `ida_issuer` értékeit. A következő elemek értékeinek megkeresása a Azure Portal:

1. Válassza ki a korábban használt AAD-bérlőt, Alkalmazásregisztrációk a menüben, majd kattintson a **Végpontok hivatkozásra.** 
1. Jelölje ki és másolja ki az **OpenIdConnect** metaadat-dokumentummező értékét, és illessze be értékként a `ida_AADOpenIdDiscoveryDocument` kódba.
1. Az `ida_audience` érték a *licenseDeliveryResource2* regisztrált alkalmazás alkalmazás- (ügyfél-) azonosítója.
1. Az `ida_issuer` érték a `https://login.microsoftonline.com/[tenant_id]/v2.0` URL-cím. Cserélje le a [*tenant_id*] helyére a bérlőazonosítóját.

## <a name="set-up-the-sample-player-app"></a>A mintalejátszó alkalmazás beállítása

Ha még nem tette meg, klónozza vagy töltse le az alkalmazást a forrás-fájlból: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

A lejátszóalkalmazást két lehetőség közül választhatja:

* Minimális testreszabás (csak néhány állandó sztringérték, például a client_id, tenant_id és a streamelési URL-cím cseréje), de az Visual Studio Code és a Node.js.
* Ha inkább egy másik IDE-t és webes platformot szeretne használni, például a ASP.NET Core-t, a weblapfájlokat, a JavaScript-fájlokat és a CSS-fájlokat a projektben is használhatja, mivel maga a lejátszóalkalmazás nem használ kiszolgálóoldali kódot.

### <a name="option-1"></a>1\. lehetőség

1. A Visual Studio Code elindítása.
1. A projekt megnyitásához kattintson a File -> Open Folder -> browse to and select the parent folder of thepackage.js *on* file ..
1. Nyissa meg a *public/javascript/constants.js.*
1. Cserélje `OAUTH2_CONST.CLIENT_ID` le a `client_id` helyére az AAD-bérlőben regisztrált ügyfélalkalmazást.  A következőt `client_id` a regisztrált alkalmazás Áttekintés szakaszában találja a Azure Portal. Megjegyzés: ez nem az objektumazonosító, hanem az ügyfél-azonosító.
1. Cserélje `OAUTH2_CONST.TENANT_ID` le a `tenant_id` helyére az Azure AD-bérlője helyére. A elemet a `tenant_id` menüben Azure Active Directory találhatja meg. A tenant_id az Áttekintés szakaszban fog megjelenni.
1. Cserélje `OAUTH2_CONST.SCOPE` le a helyére a regisztrált ügyfélalkalmazásban hozzáadott hatókört. A hatókör megkereséhez navigálhat a regisztrált ügyfélalkalmazáshoz a Alkalmazásregisztrációk **menüből,** majd válassza ki az ügyfélalkalmazást:
    1. Válassza ki az ügyfélalkalmazást, kattintson az **API-engedélyek menüre,** majd válassza ki a *DRM hatókört. License.Delivery a* *LicenseDeliveryResource2 API-engedély alatt.* Az engedély formátumának a következő formátumban kell lennie: *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM. License.Delivery*. **Fontos:** Tartsa a helyet a `offline_access` `OAUTH2_CONST.SCOPE` előtt.
1. Cserélje le a két konstans sztringet `AMS_CONST` az alábbi módon. Az egyik a teszteszköz védett streamelési URL-címe, a másik pedig a TANÚSÍTVÁNY-alkalmazás tanúsítványának URL-címe, ha szeretné szerepelni a FairPlay-tesztesetet. Ellenkező esetben meghagyhatja a következőt: `AMS_CONST.APP_CERT_URL` . Ezután kattintson a **Mentés gombra.**

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

Helyi tesztelés:

1. A Visual Studio Code (VSC) menüben válassza a Nézet **lehetőséget** a főmenüben, majd válassza a **Terminál lehetőséget.**
1. Ha még nem telepítette az npm-et, a parancssorba írja be a következőt: `npm install` .
1. Írja `npm start` be a parancsot a parancssorba. (Ha az npm nem indul el, próbálja meg a könyvtárat a következőre megváltoztatni: a parancssorba `npmweb` `cd npmweb` beírása.)
1. Böngészőben nyissa meg a `http://localhost:3000` webhelyet.

A használt böngészőtől függően válassza ki a DRM/AES vs Streaming Protocol és a Container Format megfelelő kombinációját a bejelentkezés utáni teszteléshez `access_token` (beszerzés). Ha macOS rendszeren teszteli a Safarit, jelölje be az Átirányítási API lehetőséget, mivel a Safari nem engedélyezi az előugró ablakokat. A legtöbb más böngészőben felugró ablakok és átirányítási beállítások is elérhetőek.

### <a name="option-2"></a>2\. lehetőség

Ha egy másik IDE/webplatformot és/vagy egy webkiszolgálót ,például az IIS-t használ a fejlesztői gépen, másolja a következő fájlokat egy új könyvtárba a helyi webkiszolgálón. Az alábbi elérési utakon találja meg őket a letöltött kódban.

* *views/index.ejs* (az utótag módosítása .html formátumra)
* *views/jwt.ejs* (az utótag módosítása .html formátumra)
* *views/info.ejs* (az utótag módosítása html-re)
* *public/** (JavaScript-fájlok, CSS, képek az alább látható módon)

1. Másolja a nézet mappában található *fájlokat* az új könyvtár gyökeréhez.
1. Másolja *a nyilvános* mappában található *mappákat* az új könyvtár gyökerében.
1. Módosítsa a fájlok `.ejs` bővítményét a következőre: `.html` . (A rendszer nem használ kiszolgálóoldali változót, így biztonságosan módosíthatja.)
1. Nyissa *index.html* fájlt a VSC-ban (vagy más kódszerkesztőben), és módosítsa a és az elérési utat úgy, hogy azok tükrözzék a fájlok `<script>` `<link>` helyét.  Ha követte az előző lépéseket, csak az elérési úton kell törölnie a `\` et.  A például `<script type="text/javascript" src="/javascript/constants.js"></script>` a következő lesz: `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. Szabja testre a *javascript/constants.js* állandókat az 1. lehetőségnek megfelelően.

## <a name="common-customer-scenarios"></a>Gyakori ügyfélforgatókönyvek

Most, hogy befejezte az oktatóanyagot, és van egy működő alrendszere, megpróbálhatja módosítani a következő ügyfélforgatókönyvek szerint:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) az Azure AD-csoporttagságon keresztüli licenck kézbesítéshez

A rendszer eddig minden bejelentkező felhasználó számára lehetővé teszi, hogy érvényes licencet kap, és lejátssa a védett tartalmat.

Gyakori ügyfélkövetelmény, hogy a hitelesített felhasználók egy részéhez engedélyezve van a tartalom megtekintése, míg mások nem, például olyan ügyfelek, akik alapszintű és prémium szintű előfizetéseket kínálnak a videótartalmakhoz. Az alapszintű előfizetésért fizető ügyfelek nem figyelik a prémium előfizetést igénylő tartalmakat. Az alábbiakban a követelménynek való megfelelő további lépések olvashatók:

#### <a name="set-up-the-azure-ad-tenant"></a>Az Azure AD-bérlő beállítása

1. Állítson be két fiókot a bérlőben. Ezek neve lehet *premium_user* és *basic_user;*
1. Hozzon létre egy felhasználói csoportot, és hívja *meg PremiumGroup-nak.*
1. Adja hozzá *premium_user* prémium szintű *csoporthoz* tagként, de ne  adja hozzá a basic_user a csoporthoz.
1. Jegyezze fel **a** *PremiumGroup objektumazonosítóját.*

#### <a name="set-up-the-media-services-account"></a>A Media Services beállítása

Módosítsa a (Beállítás a Media Service-fiókban című szakaszban látható módon) egy groups nevű jogcím hozzáadásával, amelyben a `ContentKeyPolicyRestriction`  `ida_EntitledGroupObjectId` *PremiumGroup* objektumazonosítója az értéke:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

A *groups* jogcím egy korlátozott jogcímkészlet [tagja](../../active-directory/develop/reference-claims-mapping-policy-type.md#claim-sets) az Azure AD-ban.

#### <a name="test"></a>Tesztelés

1. Jelentkezzen be az *premium_user* fiókkal. Képesnek kell lennie lejátszani a védett tartalmat.
1. Jelentkezzen be az *basic_user* fiókkal. Hibaüzenetet kell kapnia, amely jelzi, hogy a videó titkosított, de nincs kulcs a visszafejtéséhez. Ha megtekinti az eseményeket, a hibákat és a letöltéseket a lejátszó diagnosztikai átfedésének alján található legördülő menüvel, a hibaüzenetnek azt kell jeleznie, hogy a licenc beszerzése sikertelen volt, mert a csoportok jogcímértékének hiányzik az Azure AD-jogkivonat végpontja által kiadott JWT-beli jogcímben.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Több médiaszolgáltatás-fiók támogatása (több előfizetésben)

Egy ügyfél több médiaszolgáltatás-fiókkal is lehet egy vagy több Azure-előfizetésben. Egy ügyfél például egy Media Service-fiókkal elsődlegesként, egy másikkal másodlagos/biztonsági másolatként, egy másikkal pedig fejlesztési/tesztelési célokat biztosít.

Mindössze meg kell győződni arról, hogy ugyanazt a paraméterkészletet használja, mint amit a (Beállítás a Media Service-fiókban) szakaszban használt az létrehozásához az összes `ContentKeyPolicyRestriction` Media Service-fiókban.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Ügyfél, annak beszállítói és/vagy leányvállalatok támogatása több AAD-bérlőn

A megoldás felhasználóiként az ügyfél leányvállalatai, szállítói/partnerei különböző AAD-bérlőkben lehetnek, például `mycustomer.com` , `mysubsidiary.com` és `myparther.com` . Bár ez a megoldás egyetlen adott AAD-bérlőre épül, például a bérlőre, más bérlők felhasználói számára `mycustomer.com` is lehetővé teszi a megoldás megfelelő megoldását.

Ehhez a megoldáshoz adjon hozzá egy felhasználót `mycustomer.com` vendégfelhasználóként a `mypartner.com` `mycustomer.com` bérlőhöz. Győződjön meg `mypartner.com` arról, hogy a felhasználó aktiválja a vendégfiókot. A vendégfiók egy másik AAD-bérlőből vagy egy fiókból `outlook.com` is lehet.

Figyelje meg, hogy a vendégfelhasználói a -ban való aktiválása után is a saját/eredeti AAD-bérlőjükben hitelesítve vannak, de az a által `mypartner.com` `mycustomer.com` van `mypartner.com` `access_token` kibocsátva. `mycustomer.com`

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Ügyfélbérlő/-előfizetés támogatása egy beállítással az előfizetésben/bérlőben

A beállítással tesztelheti a védett tartalmakat az ügyfél Media Service-fiókjában/-előfizetésében. Ezt egy Azure AD-bérlővel és egy media service-fiókkal kell beállítania ugyanabban az előfizetésben. Az ügyfél Media Service-fiókja a saját Azure AD-bérlővel az Azure-előfizetésében lenne.

1. Adja hozzá az ügyfélfiókot a bérlőhöz vendégfiókként.
1. Az ügyféllel való munka során készítse elő a védett tartalmakat az ügyfél médiaszolgáltatás-fiókjában a Beállítás a Media Service-fiókban szakaszban felsorolt három paraméter megszava segítségével.

Az ügyfél ezután tallózással megkeresheti a beállítást, bejelentkezhet a vendégfiókkal, és tesztelheti a saját védett tartalmát. A saját fiókjával is bejelentkezhet, és tesztelheti az ügyfél tartalmát.

A mintamegoldás egy Microsoft-előfizetéssel vagy Microsoft-előfizetéssel egy egyéni bérlővel is beállítható egy Microsoft-bérlőben. Az Azure Media Service-példány egy másik előfizetésből is lehet a bérlővel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!WARNING]
> Ha nem folytatja az alkalmazás használatát, törölje az oktatóanyag során létrehozott erőforrásokat. Ellenkező esetben díjat számítunk fel értük.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Tartalom titkosítása](drm-encrypt-content-how-to.md)
