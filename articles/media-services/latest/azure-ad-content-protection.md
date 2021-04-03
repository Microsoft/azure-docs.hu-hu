---
title: Teljes körű tartalomvédelem az Azure AD-vel
description: Ez a cikk bemutatja, hogyan védhető meg a tartalma Azure Media Services és Azure Active Directory
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
ms.openlocfilehash: 9415d66c49992bc31f773dec908a861f1126e714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92427207"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>Oktatóanyag: végpontok közötti tartalomvédelem az Azure AD használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ebben az oktatóanyagban és a megadott Player-mintában a Azure Media Services (AMS) és a Azure Active Directory (HRE) teljes körű Media Content Protection-alrendszert állíthat be, hogy az összes AMS támogatott DRM/AES-128, Streaming Protocol, codec és Container formátummal továbbítsa a médiatartalmakat. A minta elég általános ahhoz, hogy biztonságos hozzáférést biztosítson a 2. OAuth által védett REST APIhoz a Code Exchange (PKCE) igazolási kulcsával. (Azure Media Services a licenc kézbesítési szolgáltatása csupán egyike.) Emellett Microsoft Graph API-val vagy bármely, a 2. OAuth-engedélyezési kóddal védett, egyéni fejlesztésű REST API is működik. Ez a [mintakód](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)kísérő dokumentuma.

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
>
> * Tekintse át a hitelesítési követelményeket
> * Az alkalmazás működésének megismerése
> * Háttérbeli erőforrás-alkalmazás regisztrálása
> * Ügyfélalkalmazás regisztrálása
> * A Media Services-fiók tartalmi kulcs-házirendjének és folyamatos átviteli házirendjeinek beállítása
> * A Player alkalmazás beállítása

Ha nem rendelkezik Azure Media Services-előfizetéssel, hozzon létre egy ingyenes Azure- [próbaverziós fiókot](https://azure.microsoft.com/free/) , majd hozzon létre egy Media Services fiókot.

### <a name="duration"></a>Időtartam
Az oktatóanyagnak körülbelül két órát kell elvégeznie ahhoz, hogy az előfeltételként szükséges technológia azonnal elérhető legyen.

## <a name="prerequisites"></a>Előfeltételek

A rendszer a következő legújabb technológiai verziókat és fogalmakat használja. Javasoljuk, hogy ismerkedjen meg velük az oktatóanyag megkezdése előtt.

### <a name="prerequisite-knowledge"></a>Ismeretekre vonatkozó előfeltételek

Nem kötelező, de javasoljuk, hogy Ismerje meg a következő fogalmakat az oktatóanyag megkezdése előtt:

* Digitális Rights Management (DRM)
* [Azure Media Services (AMS) v3](./media-services-overview.md)
* A [tartalmi kulcsokra vonatkozó HÁZIRENDEK](content-key-policy-concept.md) AMS API v3, Azure Portal vagy a [Azure Media Services Explorer (AMSE) eszköz](https://github.com/Azure/Azure-Media-Services-Explorer) használatával
* Azure AD v2-végpontok a [Microsoft Identity platformon](../../active-directory/develop/index.yml)
* Modern felhőalapú hitelesítés, például [OAuth 2,0 és OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [Engedélyezési kód flow a OAuth 2,0-ben](../../active-directory/develop/v2-oauth2-auth-code-flow.md) és miért szükséges a PKCE
  * [Delegált engedély vs alkalmazás engedélye](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT-jogkivonat](../../active-directory/develop/access-tokens.md), jogcímek és az aláíró kulcsok átállítása (a mintában szerepel)

### <a name="prerequisite-code-and-installations"></a>Előfeltételként szükséges kód és telepítések

* A mintakód. A [mintakód](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)letöltése.
* A Visual Studio Code telepítése. Töltse le itt a Visual Studio Code-ot [https://code.visualstudio.com/download](https://code.visualstudio.com/download) .
* Node.js telepítése. Itt töltheti le Node.js [https://nodejs.org](https://nodejs.org) . A NPM a telepítéshez tartozik.
* Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
* Egy Azure Media Services-(AMS-) fiók.
* @azure/msal-browser v 2.0, a [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md) SDK termékcsalád egyik tagja a különböző ügyféloldali platformokhoz
* A [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)legújabb verziója (a minta része)
* FPS hitelesítő adatok az Apple-től, ha a FairPlay DRM-t és az CORS-ben üzemeltetett alkalmazás-tanúsítványt is szeretné használni az ügyféloldali JavaScript használatával.

> [!IMPORTANT]
> Ez az oktatóanyag a .NET-et használja a tartalmi kulcsokra vonatkozó házirend korlátozásának létrehozásához.  Ha Ön nem .NET-fejlesztő, de szeretné kipróbálni Node.js Azure Media Serviceshoz való kapcsolódáshoz, olvassa el [a kapcsolódás Media Services V3 API-Node.js](configure-connect-nodejs-howto.md). Egy Node.js modul is elérhető a kulcsok automatikus átváltásához, lásd: Node.js [Passport-ad modul](https://github.com/AzureAD/passport-azure-ad).

## <a name="consider-the-authentication-and-authorization-requirements"></a>A hitelesítési és engedélyezési követelmények megfontolása

Néhány kihívás az alrendszer megtervezése során jelenik meg. Több mozgó részből áll, az ügyfélalkalmazások megkötései, valamint az Azure AD-kulcs rolloverje hat hetente történik.

Az oktatóanyagban használt Single-Page alkalmazás (SPA) figyelembe veszi a hitelesítési követelményekkel és az azt követő korlátozásokkal kapcsolatos kihívásokat. A következőket használja:

* Az Azure ad v2-végpontok, mint az Azure AD fejlesztői platform (v1-végpontok) a Microsoft Identity platformra (v2-végpontokra) változnak.
* Az engedélyezési kód folyamata, mert a OAuth 2 implicit engedélyezési folyamat elavult.
* A következő korlátozások alá eső alkalmazások:
    * Egy nyilvános ügyfél nem tudja elrejteni az ügyfél titkos kulcsát.  Az engedélyezési kód csak az ügyfél titkos kulcsának elrejtését igényli, így az engedélyezési kód a PKCE-sel van használatban.
    * Böngésző-alapú alkalmazás, amely a böngésző biztonsági Homokozójában (CORS/elővizsgálati korlátozás) van kitéve.
    * Egy böngészőalapú alkalmazás, amely a JavaScript biztonsági korlátozásokkal (Egyéni fejléc kisegítő lehetőségek, korrelációs azonosító) függ.

## <a name="understand-the-subsystem-design"></a>Az alrendszer kialakításának megismerése

Az alrendszer kialakítása az alábbi ábrán látható.  Három rétegből áll:

* Back-Office réteg (fekete) a tartalmi kulcs házirendjének konfigurálásához és tartalom közzétételéhez a streaminghez
* Nyilvános végpontok (kék színnel), amelyek a hitelesítéssel, engedélyezéssel, DRM-licenccel és titkosított tartalommal rendelkező lejátszó/ügyfél felé irányuló végpontok
* Az összes összetevőt integráló lejátszó alkalmazás (világoskék)
    * kezeli a felhasználói hitelesítést az Azure AD-n keresztül.
    * kezeli access_token beszerzését az Azure AD-ből.
    * a rendszer az AMS/CDN által küldött jegyzékfájlokat és titkosított tartalmakat fogadja.
    * DRM-licencet vásárol a Azure Media Servicesból.
    * kezeli a tartalom visszafejtését, dekódolását és megjelenítését.

![JWT-tokenek elemzésére szolgáló képernyő](media/aad-ams-content-protection/subsystem.svg)

Az alrendszersel kapcsolatos további részletekért olvassa el a [multi-DRM tartalomkezelő rendszer kialakítását az Access Control](./design-multi-drm-system-with-access-control.md) használatával.

## <a name="understand-the-single-page-app"></a>Az egyoldalas alkalmazás megismerése

A Player alkalmazás egy egyoldalas alkalmazás (SPA), amelyet a Visual Studio Code-ban fejlesztettünk ki a következő használatával:

* Node.js with ES 6 JavaScript
* @azure/msal-browser 2,0 Beta
* Azure Media Player SDK
* OAuth 2 folyamat az Azure AD v2-végpontokon (Microsoft Identity platform)

A SPA Player alkalmazás a következő műveleteket hajtja végre:

* Felhasználói hitelesítés a bérlőhöz natív felhasználók számára, valamint más HRE-bérlők vagy MSA-fiókok vendégeit használó felhasználók számára. A felhasználók dönthetnek úgy, hogy bejelentkeznek a böngésző felugró ablakával vagy átirányításával (olyan böngészőknél, amelyek nem engedélyezik a Safarit).
* `access_token`Engedélyezési programkódon keresztüli adatforgalom beszerzése a PKCE-mel.
* A `access_token` (HRE által kiállított jogkivonatok 1 órára érvényesek), amely esetében az `refresh_token` is beszerzésre kerül.
* JWT-tokenek (mindkettő `access_token` és `id_token` ) elemzése teszteléshez/vizsgálathoz.
* DRM-licencek beszerzése mindhárom DRMs vagy AES-128 tartalmi kulcshoz.
* Tartalom folyamatos átvitele a DRM vs Streaming Protocol vs Container Format különböző kombinációi alapján. Minden kombinációhoz a megfelelő formázó karakterlánc jön létre.
* Visszafejtés, dekódolás és megjelenítés.
* Microsoft Graph API-hívások hibaelhárítási célból. <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

A bejelentkezés, a jogkivonat beszerzése, a jogkivonat-megújítás és a jogkivonat-megjelenítés képernyője:

 ![Képernyő a bejelentkezéshez, a jogkivonat-beszerzéshez, a jogkivonat-megújításhoz és a jogkivonat-megjelenítéshez](media/aad-ams-content-protection/token-acquisition.png)

A JWT-tokenek elemzésére szolgáló képernyő (access_token vagy id_token):

![A J W T-tokenek elemzését bemutató képernyőkép.](media/aad-ams-content-protection/parsing-jwt-tokens.png)

A védett tartalom tesztelésére szolgáló képernyő a DRM/AES vs streaming protokollok és a tároló formátumának különböző kombinációjával:

![Képernyőkép a védett tartalom teszteléséről, amely a D R M különböző kombinációit vagy az E-k és A tárolók közötti adatátviteli protokollokat](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>Azure AD-bérlő kiválasztása

> [!NOTE]
> Innentől kezdve feltételezzük, hogy bejelentkezett a Azure Portalba, és legalább egy Azure AD-Bérlővel rendelkezik.

Válasszon ki egy Azure AD-bérlőt, amelyet teljes mintaként használhat. Erre két lehetősége van:

* Egy meglévő Azure AD-bérlő. Minden Azure-előfizetéshez egy Azure AD-Bérlőnek kell tartoznia, de az Azure AD-bérlőt több Azure-előfizetés is használhatja.
* Új Azure AD-bérlő, amelyet egyetlen Azure-előfizetés *sem* használ. Ha az új bérlő lehetőséget választja, a Media Service-fióknak és a minta-lejátszó alkalmazásnak olyan Azure-előfizetésben kell lennie, amely külön Azure AD-bérlőt használ. Ez némi rugalmasságot biztosít. Használhatja például saját Azure AD-bérlőjét, de az ügyfél Media Service-fiókját is az ügyfél Azure-előfizetésében.

## <a name="register-the-backend-resource-app"></a>A háttérbeli erőforrás alkalmazás regisztrálása

1. Navigáljon a kiválasztott vagy létrehozott Azure AD-bérlőhöz.
1. A menüből válassza a **Azure Active Directory** lehetőséget.
1. A menüből válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Kattintson az **+ új regisztráció** elemre.
1. Nevezze el az alkalmazás *LicenseDeliveryResource2* (ahol a 2 a HRE v2 végpontot jelzi).
1. **Csak a szervezeti címtárban válassza a fiókok lehetőséget ([*a bérlő neve*] csak egyetlen bérlő)**. Ha több bérlő számára is engedélyezni szeretné a hozzáférést, válassza a további több-bérlős lehetőségek egyikét.
1. Az **átirányítási URI** nem kötelező, és később is módosítható.
1. Kattintson a **Regisztrálás** parancsra. Ekkor megjelenik a Alkalmazásregisztrációk nézet.
1. Válassza ki a **jegyzékfájlt** a menüből. Ekkor megjelenik a jegyzékfájl nézet.
1. Módosítsa a értékét a `accessTokenAcceptedVersion` *2* értékre (idézőjelek nélkül).
1. Módosítsa a értékét `groupMembershipClaims` *"SecurityGroup"* értékre (idézőjelekkel).
1. Kattintson a **Mentés** gombra.
1. Válassza ki az **API közzététele** lehetőséget a menüből. Ekkor megjelenik a hatókör hozzáadása nézet. (Az Azure egy alkalmazás-azonosító URI-t biztosít, de ha módosítani szeretné, szerkesztheti az alkalmazás-azonosító URI-ja mezőben.)
1. Kattintson **a Mentés és folytatás** gombra. A nézet meg fog változni. Az alábbi táblázat Setting (beállítás) oszlopában az érték oszlopban adja meg az értéket, majd kattintson a **hatókör hozzáadása** elemre.

| Beállítás | Érték | Leírás |
| ------- | ----- | ----------- |
| Hatókör neve | *DRM. Licenc. Delivery* | A hatókör megjelenése abban az esetben, ha az API-hoz való hozzáférést kérik, illetve a hozzáférési jogkivonatokban, ha a hatókört megadták egy ügyfélalkalmazás számára. Ennek az alkalmazáson belül egyedinek kell lennie. Az ajánlott eljárás az "erőforrás. Operation. megkötés" használata mintaként a név létrehozásához. |
| Ki tud hozzájárulni? | *Rendszergazdák és felhasználók* | Meghatározza, hogy a felhasználók beleegyeznek-e a hatókörbe olyan címtárakban, amelyeken engedélyezve van a felhasználói engedély. |
| Rendszergazdai hozzájárulás megjelenítendő neve | *DRM-licenckézbesítés* | A hatókör a beleegyezési képernyőn lesz meghívva, amikor a rendszergazdák beleegyeznek ehhez a hatókörhöz. |
| Rendszergazdai engedély leírása * * | *DRM-licenc kézbesítési háttér erőforrás-hatóköre* | A hatókör részletes leírása, amely akkor jelenik meg, ha a bérlői rendszergazdák kibővítik a hatókört a beleegyezési képernyőn. |
| Felhasználói jóváhagyás megjelenítendő neve | *DRM. Licenc. Delivery* | A hatókör a beleegyezési képernyőn lesz meghívva, amikor a felhasználók beleegyeznek ehhez a hatókörhöz. |
| Felhasználói jóváhagyás leírása | *DRM-licenc kézbesítési háttér erőforrás-hatóköre* | Ez a hatókör részletes leírása, amely akkor jelenik meg, amikor a felhasználók kibontanak egy hatókört a beleegyezési képernyőn. |
| Állapot | *Engedélyezve* | Meghatározza, hogy a hatókör elérhető legyen-e az ügyfelek számára. Állítsa "Letiltva" értékre azon hatókörök esetében, amelyeket nem szeretne látni az ügyfelek számára. A rendszer csak a letiltott hatóköröket törölheti, és azt javasoljuk, hogy legalább egy hetet várjon a hatókör letiltását követően, hogy az ügyfelek ne használják tovább. |

## <a name="register-the-client-app"></a>Az ügyfélalkalmazás regisztrálása

1. Navigáljon a kiválasztott vagy létrehozott Azure AD-bérlőhöz.
1. A menüből válassza a **Azure Active Directory** lehetőséget.
1. A menüből válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Kattintson az **+ új regisztráció** elemre.
1. Adja meg az ügyfélalkalmazás nevét, például: *AMS hre Content Protection*.
1. **Csak a szervezeti címtárban válassza a fiókok lehetőséget ([*a bérlő neve*] csak egyetlen bérlő)**. Ha több bérlő számára is engedélyezni szeretné a hozzáférést, válassza a további több-bérlős lehetőségek egyikét.
1. Az **átirányítási URI** nem kötelező, és később is módosítható.
1. Kattintson a **Regisztrálás** parancsra.
1. Válassza ki az **API-engedélyeket** a menüből.
1. Kattintson **az + engedély hozzáadása** lehetőségre. Ekkor megnyílik a kérelem API-engedélyeinek nézete.
1. Kattintson a **saját API** fülre, és válassza ki az utolsó szakaszban létrehozott *LicenseDeliveryResource2* alkalmazást.
1. Kattintson a DRM nyílra, és jelölje be a DRM-t *. Licenc. Delivery* engedély.
1. Kattintson az **engedélyek hozzáadása** lehetőségre. Az engedélyek hozzáadása nézet be lesz zárva.
1. Válassza ki a **jegyzékfájlt** a menüből. Ekkor megjelenik a jegyzékfájl nézet.
1. Keresse meg és adja hozzá a következő érték párokat az `replyUrlsWithType` attribútumhoz:

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
    > Ezen a ponton még nem rendelkezik a Player-alkalmazás URL-címével.  Ha az alkalmazást a localhost webkiszolgálóról futtatja, akkor csak a localhost érték párokat használhatja. A Player alkalmazás üzembe helyezése után itt adhatja hozzá a bejegyzést a központilag telepített URL-címhez.  Ha elfelejti, hibaüzenet jelenik meg az Azure AD-bejelentkezésben.

1. Kattintson a **Mentés** gombra.
1. Végül ellenőrizze, hogy a konfiguráció megfelelő-e, majd válassza a **hitelesítés** lehetőséget.  Ekkor megjelenik a hitelesítési nézet. Az ügyfélalkalmazás egy egyoldalas alkalmazásként (SPA) jelenik meg, az átirányítási URI a listáról lesz listázva, a Grant típus pedig a PKCE-vel való engedélyezési kód lesz.

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>A Media Services fiók tartalmi kulcsára vonatkozó házirend és folyamatos átviteli szabályzatok beállítása

**Ez a szakasz azt feltételezi, hogy Ön .NET-fejlesztő, és ismeri az AMS V3 API használatát.**

> [!NOTE]
> A jelen cikk írásakor nem használhatja a Media Services-fiókhoz tartozó kulcs házirend-beállításának Azure Portal, mert nem támogatja az OpenID-config-val rendelkező aszimmetrikus jogkivonat-aláíró kulcs használatát.  A telepítőnek támogatnia kell az Azure AD-kulcs átváltását, mert az Azure AD által kibocsátott jogkivonatot egy aszimmetrikus kulcs aláírja, és a kulcs 6 hetente összesíti. Ezért ez az oktatóanyag a .NET-et és az AMS V3 API-t használja.

A [tartalom kulcsára vonatkozó házirend](content-key-policy-concept.md) és a DRM és AES-128 [streaming-szabályzatok](streaming-policy-concept.md) konfigurálása érvényes.  Módosítsa a `ContentKeyPolicyRestriction` tartalmat a tartalmi kulcs házirendjében.

Alább látható a .NET-kód a tartalmi kulcs házirend-korlátozásának létrehozásához.

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

Módosítsa a `ida_AADOpenIdDiscoveryDocument` , a `ida_audience` és a `ida_issuer` értékeket a fenti kódban. Ezen elemek értékeinek megkereséséhez a Azure Portalban:

1. Válassza ki a korábban használt HRE-bérlőt, kattintson **Alkalmazásregisztrációk** gombra a menüben, majd kattintson a **végpontok** hivatkozásra.
1. Válassza ki és másolja ki a **OpenIdConnect metaadat-dokumentum** mezőjének értékét, és illessze be a kódot az `ida_AADOpenIdDiscoveryDocument` Érték mezőbe.
1. Az `ida_audience` érték a regisztrált alkalmazás *LicenseDeliveryResource2*(ügyfél) azonosítója.
1. Az `ida_issuer` érték az URL-cím `https://login.microsoftonline.com/[tenant_id]/v2.0` . Cserélje le a [*tenant_id*]-t a BÉRLŐi azonosítóra.

## <a name="set-up-the-sample-player-app"></a>A minta lejátszó alkalmazás beállítása

Ha még nem tette meg, akkor az alkalmazást a forrás-tárházból is megnyithatja vagy letöltheti: [https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad) .

A Player alkalmazás beállítása két lehetőség közül választhat:

* Minimális testreszabás (csak bizonyos konstans karakterlánc-értékek, például client_id, tenant_id és streaming URL-cím helyett), de a Visual Studio Code és a Node.jst kell használnia.
* Ha inkább egy másik IDE-és webplatformot szeretne használni, például a ASP.NET Coret, akkor a weblapok fájljait, a JavaScript-fájlokat és a CSS-fájlt a projektbe helyezheti, mivel maga a Player alkalmazás nem használ kiszolgálóoldali kódokat.

### <a name="option-1"></a>1\. lehetőség

1. A Visual Studio Code elindítása.
1. A projekt megnyitásához kattintson a fájl-> mappa megnyitása lehetőségre, > tallózással keresse meg és válassza ki a fájl *package.jsjának* szülő mappáját.
1. Nyissa meg a JavaScript *-fájl nyilvános/JavaScript/constants.js*.
1. Cserélje le `OAUTH2_CONST.CLIENT_ID` a `client_id` regisztrált ÜGYFÉLALKALMAZÁS a HRE-bérlőben.  A `client_id` Azure Portal található regisztrált alkalmazás Áttekintés szakaszában talál. Megjegyzés: ez az ügyfél-azonosító, nem objektumazonosító.
1. Cserélje le `OAUTH2_CONST.TENANT_ID` az `tenant_id` Azure ad-bérlőre. A `tenant_id` Azure Active Directory menüre kattintva érheti el. A tenant_id megjelenik az Áttekintés szakaszban.
1. Helyettesítse `OAUTH2_CONST.SCOPE` be a regisztrált ügyfélalkalmazás által hozzáadott hatókört. A hatókört úgy tekintheti meg, hogy a **Alkalmazásregisztrációk** menüből navigál a regisztrált ügyfélalkalmazás számára, majd kiválasztja az ügyfélalkalmazás:
    1. Válassza ki az ügyfélalkalmazás, kattintson az **API-engedélyek** menüre, majd válassza ki a hatókört a DRM-hez *. Licenc. Delivery* az API engedély *LicenseDeliveryResource2*. Az engedélynek a következő formátumúnak kell lennie: *API://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM. Licenc. Delivery*. **Fontos**: tartsa meg a helyet a ( `offline_access` z `OAUTH2_CONST.SCOPE` ) előtt.
1. Cserélje le a két konstans karakterláncot az `AMS_CONST` alább látható módon. Az egyik a tesztelési eszköz védett adatfolyam-továbbítási URL-címe, a másik az FPS-alkalmazás tanúsítványának URL-címe, ha meg szeretné adni a FairPlay tesztelési esetét is. Ellenkező esetben hagyja a következőt: `AMS_CONST.APP_CERT_URL` . Ezután kattintson a **Mentés** gombra.

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

1. A Visual Studio Code (VSC) eszközben válassza a **nézet** lehetőséget a főmenüben, majd a **terminál** elemet.
1. Ha még nem telepítette a NPM-et, a parancssorba írja be a parancsot `npm install` .
1. Írja be `npm start` a parancsot a parancssorba. (Ha a NPM nem indul el, próbálja meg módosítani a könyvtárat a `npmweb` parancssorba való beírásával `cd npmweb` .)
1. Böngésző használatával keresse meg a következőt: `http://localhost:3000` .

A használt böngészőtől függően válassza ki a DRM/AES vs streaming protokoll és a tároló formátumának megfelelő kombinációját a bejelentkezés ( `access_token` Beszerzés) utáni teszteléshez. Ha macOS rendszeren végez tesztelést a Safariban, tekintse át az átirányítási API lehetőséget, mivel a Safari nem engedélyezi a felugró ablakokat. A legtöbb böngésző a felugró ablakokat és az átirányítási lehetőségeket is lehetővé teszi.

### <a name="option-2"></a>2\. lehetőség

Ha egy másik IDE/webplatformot és/vagy egy webkiszolgálót, például a fejlesztői gépen futó IIS-t szeretne használni, másolja a következő fájlokat egy új könyvtárba a helyi webkiszolgálón. Az alábbi elérési utak a letöltött kódban találhatók.

* *views/index. EJS* (utótag módosítása. html)
* *views/JWT. EJS* (az utótag módosítása. html)
* *views/info. EJS* (az utótag módosítása HTML-re)
* *nyilvános/** (JavaScript-fájlok, CSS, képek az alább láthatók szerint)

1. Másolja a *View (nézet* ) mappában található fájlokat az új könyvtár gyökerébe.
1. Másolja a *nyilvános* mappában található *mappákat* az új könyvtár gyökerébe.
1. Módosítsa a fájlok kiterjesztéseit a következőre: `.ejs` `.html` . (A rendszer nem használja a kiszolgálóoldali változót, így biztonságosan módosíthatja azt.)
1. Nyissa meg *index.html* -t a VSC-ben (vagy más szerkesztőprogramban), és módosítsa a `<script>` és az `<link>` elérési utakat úgy, hogy azok tükrözzék a fájlok helyét.  Ha követte az előző lépéseket, csak az `\` elérési úton kell törölnie.  Például a következő `<script type="text/javascript" src="/javascript/constants.js"></script>` lesz: `<script type="text/javascript" src="javascript/constants.js"></script>` .
1. Szabja testre az állandókat a *JavaScript/constants.js* fájlban az 1. lehetőségnek megfelelően.

## <a name="common-customer-scenarios"></a>Gyakori felhasználói forgatókönyvek

Most, hogy elvégezte az oktatóanyagot, és rendelkezik egy működő alrendszerrel, a következő felhasználói forgatókönyvekhez is kipróbálhatja a módosítást:

### <a name="azure-role-based-access-control-azure-rbac-for-license-delivery-via-azure-ad-group-membership"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) az Azure AD-csoporttagság használatával történő licenc-továbbításhoz

Eddig a rendszer lehetővé teszi minden olyan felhasználó számára, aki bejelentkezhet egy érvényes licenc beszerzéséhez és a védett tartalom lejátszásához.

Ez egy gyakori ügyfél-követelmény, hogy a hitelesített felhasználók egy részhalmaza számára engedélyezett a tartalom megtekintése, míg mások nem, például olyan ügyfelek, akik alapszintű és prémium előfizetéseket biztosítanak a videó tartalmához. Az alapszintű előfizetés után fizetett ügyfelek nem tekinthetik meg a prémium szintű előfizetést igénylő tartalmakat. A követelmények teljesítéséhez szükséges további lépések az alábbiak:

#### <a name="set-up-the-azure-ad-tenant"></a>Az Azure AD-bérlő beállítása

1. Két fiók beállítása a bérlőben. Ezek neve *premium_user* és *basic_user*;
1. Hozzon létre egy felhasználói csoportot, és hívja meg a *PremiumGroup*.
1. Adja hozzá a *premium_usert* a *PremiumGroup* tagként, de ne adja hozzá a *basic_usert* a csoporthoz.
1. Jegyezze fel a *PremiumGroup* **objektum-azonosítóját** .

#### <a name="set-up-the-media-services-account"></a>A Media Services fiók beállítása

Módosítsa `ContentKeyPolicyRestriction` (ahogy az a fenti részben látható a Media Service-fiókban), adjon hozzá egy *csoportok* nevű jogcímet, ahol `ida_EntitledGroupObjectId` a *PremiumGroup* objektumazonosító értéke:

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

A *groups* jogcím egy [korlátozott jogcímek készletének](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets) tagja az Azure ad-ben.

#### <a name="test"></a>Tesztelés

1. Jelentkezzen be a *premium_user* -fiókkal. Képesnek kell lennie a védett tartalom lejátszására.
1. Jelentkezzen be a *basic_user* -fiókkal. Olyan hibaüzenetet kap, amely jelzi, hogy a videó titkosított, de nincs kulcs a visszafejtéshez. Ha megtekinti az eseményeket, a hibákat és a letöltéseket a lejátszó diagnosztikai átfedésének alján lévő legördülő listáról, akkor a hibaüzenetnek jeleznie kell, hogy az Azure AD-jogkivonat végpontja által kiállított JWT esetében a hiányzó jogcím értéke miatt sikertelen volt a licenc.

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>Több Media Service-fiók támogatása (több előfizetés között)

Egy ügyfél több Media Service-fiókkal is rendelkezhet egy vagy több Azure-előfizetésen belül. Előfordulhat például, hogy egy ügyfél rendelkezik egy Media Service-fiókkal elsődlegesként, egy másikat másodlagosként/biztonsági mentésként, egy másikat pedig fejlesztési és tesztelési célokra.

Mindössze annyit kell tennie, hogy ugyanazt a paramétert használja, mint amelyet a (a Media Service-fiók beállítása) szakaszban használt, az `ContentKeyPolicyRestriction` összes Media Service-fiók létrehozásához.

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>Ügyfelek, szállítóik és/vagy leányvállalatok támogatása több HRE-bérlőn keresztül

A megoldás felhasználói, az ügyfél leányvállalatai, szállítói/partnerei pedig különböző HRE-bérlők, például,, `mycustomer.com` `mysubsidiary.com` és `myparther.com` . Habár ez a megoldás egyetlen konkrét HRE-bérlőre épül, például a `mycustomer.com` -ben, más bérlők felhasználói számára is dolgozhat.

`mycustomer.com`Ha ezt a megoldást használja, vegyen fel egy felhasználót a `mypartner.com` vendég felhasználóként a `mycustomer.com` bérlőbe. Győződjön meg arról, hogy a `mypartner.com` felhasználó aktiválja a vendég fiókot. A vendég fiók lehet egy másik HRE-bérlő vagy egy `outlook.com` fiók.

Figyelje meg, hogy a felhasználótól a rendszerbe való `mypartner.com` aktiválást követően a `mycustomer.com` rendszer továbbra is a saját/eredeti HRE-bérlőn keresztül hitelesíti magát, `mypartner.com` de a `access_token` kiállítója `mycustomer.com` .

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>Ügyfél-bérlő/előfizetés támogatása az előfizetésben/bérlőben lévő beállításokkal

A telepítő segítségével tesztelheti a védett tartalmakat az ügyfél Media Service-fiókjában/előfizetésében. Egy Azure AD-Bérlővel és egy, az előfizetéshez tartozó Media Service-fiókkal kell beállítania. Az ügyfél Media Service-fiókja az Azure-előfizetésében saját Azure AD-Bérlővel lenne.

1. Adja hozzá az ügyfél fiókját a bérlőhöz vendég fiókként.
1. Az ügyféllel együttműködve előkészítheti a védett tartalmakat az ügyfél Media Service-fiókjába úgy, hogy a következő három paramétert adja meg a Media Service-fiók beállítása szakaszban felsoroltak szerint.

Az ügyfél ezután megkeresheti a beállításokat, bejelentkezhet a vendég fiókkal, és tesztelheti saját védett tartalmát. Saját fiókjával is bejelentkezhet, és tesztelheti az ügyfél tartalmát.

Előfordulhat, hogy a Microsoft-előfizetéssel rendelkező Microsoft-bérlőben vagy a Microsoft-előfizetéssel rendelkező egyéni bérlőn belül a mintaoldat is be van állítva. Az Azure Media Service-példány a Bérlővel egy másik előfizetésből is származhat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!WARNING]
> Ha nem folytatja az alkalmazás használatát, törölje az oktatóanyag követése során létrehozott erőforrásokat. Ellenkező esetben fizetni kell rájuk.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: tartalom titkosítása](encrypt-content-quickstart.md)
