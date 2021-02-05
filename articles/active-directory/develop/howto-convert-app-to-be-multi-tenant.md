---
title: Azure AD-felhasználókba bejelentkező alkalmazások készítése
titleSuffix: Microsoft identity platform
description: Bemutatja, hogyan hozhat létre egy több-bérlős alkalmazást, amely bármilyen Azure Active Directory bérlőtől bejelentkezhet a felhasználóba.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581720"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Útmutató: Azure Active Directory-felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával

Ha egy szolgáltatott szoftver (SaaS) alkalmazást biztosít számos szervezet számára, beállíthatja, hogy az alkalmazás minden Azure Active Directory (Azure AD-) bérlőtől fogadja a bejelentkezéseket. Ezt a konfigurációt az *alkalmazás több-bérlővé tétele hívja meg*. Az Azure AD-bérlők felhasználói bejelentkezhetnek az alkalmazásba, miután beleegyezett a fiókja alkalmazásával.

Ha olyan meglévő alkalmazással rendelkezik, amely saját fiókkal rendelkezik, vagy más felhőalapú szolgáltatók más típusú bejelentkezéseit is támogatja, az Azure AD-bejelentkezés bármely bérlőből egyszerű. Csak regisztráljon az alkalmazást, adja hozzá a bejelentkezési kódot OAuth2, OpenID Connect vagy SAML használatával, és helyezzen el egy ["Bejelentkezés Microsoft-fiókkal" gombot][AAD-App-Branding] az alkalmazásban.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy már ismeri az Azure AD egybérlős alkalmazásának létrehozását. Ha nem, kezdje a [fejlesztői útmutató kezdőlapján][AAD-Dev-Guide]található rövid útmutatók egyikével.

Az alkalmazás egy Azure AD több-bérlős alkalmazásba való konvertálása négy lépésből áll:

1. [Az alkalmazás regisztrációjának frissítése több-bérlőre](#update-registration-to-be-multi-tenant)
2. [A kód frissítése a/gyakori hibák-végpontra irányuló kérelmek küldéséhez](#update-your-code-to-send-requests-to-common)
3. [A kód frissítése több kiállító érték kezelésére](#update-your-code-to-handle-multiple-issuer-values)
4. [A felhasználók és a rendszergazdák beleegyezésének megértése és a megfelelő kód módosításának elvégzése](#understand-user-and-admin-consent)

Nézzük meg az egyes lépéseket részletesen. Az [Azure ad és az OpenID Connect használatával meghívja a Microsoft Graph egy több-bérlős SaaS-webalkalmazást](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)is készíthet.

## <a name="update-registration-to-be-multi-tenant"></a>Regisztráció frissítése több-bérlőre

Alapértelmezés szerint az Azure AD-ben a webalkalmazás/API-regisztráció egyetlen bérlős. A regisztráció több-bérlőt úgy teheti meg, hogy megkeresi a **támogatott fióktípus** kapcsolót az alkalmazás regisztrációjának **hitelesítés** ablaktábláján a [Azure Portal][AZURE-portal] , és beállítja azt a **szervezeti címtárban lévő fiókokhoz**.

A több-bérlős alkalmazások megkezdése előtt az Azure AD-nek az alkalmazás alkalmazásspecifikus URI azonosítójának globálisan egyedinek kell lennie. Az alkalmazásazonosító URI egy módszer, amellyel az alkalmazás a protokollüzenetekben azonosítható. Egybérlős alkalmazás esetében az alkalmazásazonosító URI-nak csak a bérlőn belül kell egyedinek lennie. Több-bérlős alkalmazás esetében azonban globálisan egyedinek kell lennie, hogy az Azure AD megtalálja az alkalmazást a különböző bérlők közt. A globális egyediség azzal kényszeríthető ki, hogy a rendszer megköveteli, hogy az alkalmazásazonosító URI egy olyan egy gazdanévvel rendelkezzen, amely egyezik az Azure AD-bérlő egy ellenőrzött tartományával.

Alapértelmezés szerint a Azure Portal használatával létrehozott alkalmazások globálisan egyedi alkalmazás-azonosító URI-t hoznak létre az alkalmazás létrehozásakor, de ezt az értéket módosíthatja. Ha például a bérlő neve contoso.onmicrosoft.com volt, akkor egy érvényes alkalmazás-azonosító URI lenne `https://contoso.onmicrosoft.com/myapp` . Ha a bérlő ellenőrizte a tartományát `contoso.com` , akkor egy érvényes alkalmazás-azonosító URI is lesz `https://contoso.com/myapp` . Ha az alkalmazásazonosító URI nem ezt a mintát követi, az alkalmazások több-bérlősként való konfigurálása meghiúsul.

## <a name="update-your-code-to-send-requests-to-common"></a>A kód frissítése a/gyakori hibák érkező kérések küldésére

Egybérlős alkalmazásokban a bejelentkezési kérelmeket a bérlő bejelentkezési végpontjának küldi el a rendszer. A végpont contoso.onmicrosoft.com például a következő lesz: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . A bérlői végpontnak küldött kérések bejelentkezhetnek a bérlőbe az adott bérlő alkalmazásaiba.

Egy több-bérlős alkalmazás esetében az alkalmazás nem tudja, hogy melyik bérlőről származik a felhasználó, így nem küldhet kéréseket a bérlői végpontnak. Ehelyett a rendszer a kérelmeket egy olyan végpontnak küldi, amely az összes Azure AD-bérlőre kiterjed: `https://login.microsoftonline.com/common`

Ha a Microsoft Identity platform kérést kap a/gyakori hibák-végponton, akkor a felhasználó a és a rendszerbe kerül, ezért felfedi, hogy a felhasználó melyik bérlőből származik. Az/gyakori hibák-végpont az Azure AD által támogatott összes hitelesítési protokollal működik: OpenID Connect, OAuth 2,0, SAML 2,0 és WS-Federation.

Az alkalmazásra adott bejelentkezési válasz a felhasználót jelképező jogkivonatot tartalmaz. A jogkivonatban szereplő kiállítói érték azt jelzi, hogy a felhasználó melyik bérlőtől származik. Ha a válasz visszaadja a/gyakori hibák végpontot, a token kiállító értéke a felhasználó bérlője.

> [!IMPORTANT]
> A/gyakori hibák végpont nem bérlő, és nem kiállító, csak egy multiplexer. A/gyakori hibák használatakor az alkalmazásban a jogkivonatok érvényesítéséhez szükséges logikát frissíteni kell, hogy ezt vegye figyelembe.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>A kód frissítése több kiállító érték kezelésére

A webalkalmazások és webes API-k megkapják és érvényesítik a Microsoft Identity platform jogkivonatait.

> [!NOTE]
> Míg a natív ügyfélalkalmazások a Microsoft Identity platformtól kérik és fogadják a jogkivonatokat, így azok az API-khoz küldik őket, ahol a hitelesítésük megtörtént. A natív alkalmazások nem ellenőrzik a hozzáférési jogkivonatokat, és azokat átlátszatlanként kell kezelni.

Nézzük meg, hogyan érvényesíti az alkalmazás a Microsoft Identity platformtól kapott jogkivonatokat. Az egybérlős alkalmazások általában a következőhöz hasonló végponti értéket vesznek igénybe:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... és felhasználja a metaadat-URL-cím (ebben az esetben OpenID Connect) összeállításához, például:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

a jogkivonatok érvényesítésére szolgáló két kritikus információ letöltéséhez: a bérlő aláíró kulcsai és kiállítói értéke.

Mindegyik Azure AD-bérlő egyedi kiállítói értékkel rendelkezik az űrlapon:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... ahol a GUID értéke a bérlő bérlői AZONOSÍTÓjának átnevezéses biztonságos verziója. Ha az előző metaadat-hivatkozást választja `contoso.onmicrosoft.com` , akkor a kiállító értéke a dokumentumban látható.

Ha egy egybérlős alkalmazás érvényesít egy jogkivonatot, ellenőrzi a jogkivonat aláírását az aláíró kulcsokkal a metaadat-dokumentumból. Ez a teszt lehetővé teszi annak biztosítását, hogy a jogkivonatban szereplő kiállító érték egyezik a metaadat-dokumentumban található azonosítóval.

Mivel a/gyakori hibák végpont nem felel meg egy bérlőnek, és nem kiállító, ha a/gyakori hibák metaadataiban megvizsgálja a kibocsátó értékét, akkor a tényleges érték helyett a sablonhoz tartozó URL-cím szerepel:

```http
https://sts.windows.net/{tenantid}/
```

Ezért a több-bérlős alkalmazások nem tudják érvényesíteni a jogkivonatokat úgy, hogy a metaadatokban lévő kiállítói értéket a `issuer` tokenben lévő értékkel egyeztetik. A több-bérlős alkalmazásoknak logikával kell eldönteniük, hogy mely kibocsátói értékek érvényesek, és melyek nem a kibocsátó értékének bérlői azonosító részén alapulnak.

Ha például egy több-bérlős alkalmazás csak olyan bérlők számára engedélyezi a bejelentkezést, akik regisztráltak a szolgáltatásra, akkor ellenőriznie kell a kibocsátó értékét vagy a `tid` jogkivonatban lévő jogcím értékét, hogy megbizonyosodjon róla, hogy a bérlő szerepel az előfizetők listáján. Ha egy több-bérlős alkalmazás csak az egyéni felhasználókra vonatkozik, és nem végez hozzáférési döntéseket a bérlők alapján, akkor az a kibocsátó értékét is figyelmen kívül hagyhatja.

A [több-bérlős mintákban][AAD-Samples-MT]a kibocsátó ellenőrzése le van tiltva, hogy bármely Azure ad-bérlő bejelentkezzen.

## <a name="understand-user-and-admin-consent"></a>A felhasználók és a rendszergazdák beleegyezésének ismertetése

Ahhoz, hogy egy felhasználó bejelentkezzen egy alkalmazásba az Azure AD-ben, az alkalmazásnak szerepelnie kell a felhasználó bérlője számára. Ez lehetővé teszi, hogy a szervezet olyan műveleteket hajtson végre, mint például az egyedi házirendek alkalmazása, amikor a bérlő felhasználói bejelentkeznek az alkalmazásba. Egybérlős alkalmazások esetén ez a regisztráció egyszerűbb; Ez az az alkalmazás, amely akkor történik, amikor regisztrálja az alkalmazást a [Azure Portalban][AZURE-portal].

A több-bérlős alkalmazások esetében az alkalmazás kezdeti regisztrálása a fejlesztő által használt Azure AD-bérlőn történik. Ha egy másik bérlő felhasználója első alkalommal jelentkezik be az alkalmazásba, az Azure AD beleegyezik az alkalmazás által kért engedélyekkel. Ha beleegyezik, a rendszer a felhasználó bérlője számára létrehoz egy *egyszerű szolgáltatásnév* nevű alkalmazást, és a bejelentkezés folytatható. A rendszer a címtárban is létrehoz egy delegálást, amely rögzíti a felhasználó beleegyezett az alkalmazáshoz. Az alkalmazás alkalmazás-és ServicePrincipal objektumairól, valamint arról, hogy azok hogyan kapcsolódnak egymáshoz, lásd: [alkalmazásobjektumok és egyszerű szolgáltatásnév objektumok][AAD-App-SP-Objects].

![Az egyrétegű alkalmazás beleegyezőjét mutatja be][Consent-Single-Tier]

Ezt a beleegyező felhasználói élményt az alkalmazás által kért engedélyek érintik. A Microsoft Identity platform kétféle engedélyt támogat, csak az alkalmazást, és delegált.

* A delegált engedély lehetővé teszi, hogy az alkalmazás bejelentkezett felhasználóként működjön a felhasználó által elvégezhető műveletek egy részhalmaza számára. Megadhat például egy alkalmazást a delegált engedéllyel a bejelentkezett felhasználó naptárának olvasásához.
* Az alkalmazás csak az alkalmazás identitására jogosult. Például engedélyezheti az alkalmazásnak, hogy a bérlőben lévő felhasználók listáját olvassa el, függetlenül attól, hogy ki jelentkezett be az alkalmazásba.

Bizonyos engedélyeket egy normál felhasználó is jóváhagyhat, míg mások a bérlői rendszergazda beleegyezését igénylik.

Ha többet szeretne megtudni a felhasználói és rendszergazdai jogosultságokról, tekintse meg [a rendszergazdai beleegyező munkafolyamat konfigurálása](../manage-apps/configure-admin-consent-workflow.md)című témakört.

### <a name="admin-consent"></a>Rendszergazdai jóváhagyás

Csak az alkalmazásra vonatkozó engedélyek szükségesek a bérlői rendszergazda beleegyezni. Ha az alkalmazás csak alkalmazásra vonatkozó engedélyt kér, és egy felhasználó megpróbál bejelentkezni az alkalmazásba, megjelenik egy hibaüzenet, amely azt jelzi, hogy a felhasználó nem tud beleegyezni.

Bizonyos delegált engedélyekhez bérlői rendszergazda beleegyező engedélye is szükséges. Például az Azure AD-be való visszaírási képességnek, mivel a bejelentkezett felhasználó a bérlői rendszergazda beleegyezik. A csak az alkalmazás engedélyeihez hasonlóan, ha egy általános felhasználó megpróbál bejelentkezni egy olyan alkalmazásba, amely rendszergazdai hozzájárulást igénylő delegált engedélyt kér, az alkalmazás hibaüzenetet kap. Azt határozza meg, hogy egy engedélyhez rendszergazdai hozzájárulásra van-e szükség az erőforrást közzétevő fejlesztőtől, és az erőforrás dokumentációjában található. A [Microsoft Graph API][MSFT-Graph-permission-scopes] -hoz tartozó engedélyek dokumentációja azt jelzi, hogy mely engedélyekre van szükség rendszergazdai jogosultsággal.

Ha az alkalmazás rendszergazdai jogosultságot igénylő engedélyeket használ, olyan kézmozdulattal kell rendelkeznie, mint például egy gomb vagy hivatkozás, ahol a rendszergazda kezdeményezheti a műveletet. A művelethez az alkalmazás által küldött kérelem a szokásos OAuth2/OpenID Connect engedélyezési kérelem, amely tartalmazza a `prompt=admin_consent` lekérdezési karakterlánc paramétert is. Ha a rendszergazda beleegyezett, és az ügyfél bérlője létrehozta a szolgáltatást, a későbbi bejelentkezési kérések nem igénylik a `prompt=admin_consent` paramétert. Mivel a rendszergazda úgy döntött, hogy a kért engedélyek elfogadhatók, a bérlőn kívül más felhasználókat sem kell megadnia az adott időponthoz.

A bérlői rendszergazdák letilthatják, hogy a felhasználók miként tudnak hozzájárulni az alkalmazásokhoz. Ha ez a funkció le van tiltva, a rendszergazda beleegyezik, hogy az alkalmazás a bérlőben legyen használatban. Ha azt szeretné, hogy az alkalmazás a végfelhasználói engedélyekkel legyen letiltva, a [Azure Portal][AZURE-portal] a **vállalati alkalmazások** területen található **[felhasználói beállítások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** szakaszban találhatja meg a konfigurációs kapcsolót.

A `prompt=admin_consent` paramétert olyan alkalmazások is használhatják, amelyek rendszergazdai jogosultságot nem igénylő engedélyeket kérnek. Példa arra, hogy mikor érdemes használni, ha az alkalmazásnak olyan élményre van szüksége, amelyben a bérlői rendszergazda "feliratkozik", és a rendszer nem kér más felhasználókat az adott ponttól való csatlakozásra.

Ha egy alkalmazáshoz rendszergazdai hozzájárulásra van szükség, és a rendszergazda bejelentkezik a `prompt=admin_consent` paraméter küldése nélkül, amikor a rendszergazda sikeresen beleegyezett az alkalmazásba, akkor **csak a felhasználói fiókjához** lesz érvényes. A normál felhasználók továbbra sem tudnak bejelentkezni vagy beleegyezni az alkalmazásba. Ez a funkció akkor hasznos, ha azt szeretné, hogy a bérlői rendszergazda meg tudja vizsgálni az alkalmazást, mielőtt engedélyezné más felhasználók hozzáférését.

### <a name="consent-and-multi-tier-applications"></a>Beleegyezett és többrétegű alkalmazások

Előfordulhat, hogy az alkalmazásnak több rétege is van, amelyek mindegyike az Azure AD-beli saját regisztrációját képviseli. Például egy olyan natív alkalmazás, amely meghívja a webes API-t vagy egy webes API-t meghívó webalkalmazást. Mindkét esetben az ügyfél (a natív alkalmazás vagy a webalkalmazás) engedélyt kér az erőforrás meghívására (webes API). Ahhoz, hogy az ügyfél sikeresen hozzá legyen adva az ügyfél bérlője számára, az összes erőforrást, amelyre az engedélyt kér, már léteznie kell az ügyfél bérlője számára. Ha ez az állapot nem teljesül, az Azure AD olyan hibát ad vissza, amelyet először hozzá kell adni az erőforráshoz.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Több réteg egyetlen bérlőn

Ez akkor lehet probléma, ha a logikai alkalmazás két vagy több alkalmazás-regisztrációból áll, például egy különálló ügyfélről és erőforrásról. Először hogyan szerezheti be az erőforrást az ügyfél bérlője számára? Az Azure AD ezt az esetet mutatja be azáltal, hogy lehetővé teszi az ügyfelek és az erőforrások egyetlen lépésben való hozzájárulását. A felhasználó az ügyfél és az erőforrás által kért engedélyek összegét látja a belefoglalási oldalon. Ennek a viselkedésnek az engedélyezéséhez az erőforrás alkalmazás-regisztrációjának tartalmaznia kell az ügyfél alkalmazás-AZONOSÍTÓját az `knownClientApplications` [alkalmazás jegyzékfájljában][AAD-App-Manifest]. Például:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

A jelen cikk végén a [kapcsolódó tartalom](#related-content) szakasza egy többrétegű, natív ügyfél-HÍVÓ webes API-mintáját mutatja be. A következő ábra áttekintést nyújt az egyetlen bérlőben regisztrált többrétegű alkalmazások beleegyezikéről.

![Bemutatja a többrétegű ismert ügyfélalkalmazás beleegyezikét][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Több réteg több bérlőnél

Hasonló eset történik, ha az alkalmazás különböző szintjei különböző bérlők számára vannak regisztrálva. Vegyünk például egy olyan natív ügyfélalkalmazás létrehozását, amely meghívja az Exchange Online API-t. A natív alkalmazás fejlesztéséhez, valamint a natív alkalmazásnak az ügyfél bérlőben való futtatásához az Exchange Online egyszerű szolgáltatásának jelen kell lennie. Ebben az esetben a fejlesztőnek és az ügyfélnek meg kell vásárolnia az Exchange Online-t az egyszerű szolgáltatásnév létrehozásához a bérlők számára.

Ha egy, a Microsofttól eltérő szervezet által létrehozott API-t használ, az API fejlesztőinek biztosítaniuk kell, hogy az ügyfelek beleegyezett az alkalmazásba az ügyfelek bérlői számára. Az ajánlott kialakítás a harmadik féltől származó fejlesztő számára, hogy az API-t úgy hozza létre, hogy webes ügyfélként is működhet a regisztráció megvalósításához. Ehhez tegye a következőket:

1. Kövesse a korábbi szakaszokat, és győződjön meg arról, hogy az API megvalósítja a több-bérlős alkalmazás regisztrációs/kód követelményeit.
2. Az API hatókörei/szerepköreinek kimutatása mellett ellenőrizze, hogy a regisztráció tartalmazza-e a "bejelentkezés és a felhasználói profil olvasása" engedélyt (alapértelmezés szerint megadva).
3. Hozzon létre egy bejelentkezési/regisztrációs oldalt a webes ügyfélben, és kövesse a [rendszergazdai beleegyező](#admin-consent) útmutatást.
4. Miután a felhasználó beleegyezett az alkalmazásba, az egyszerű szolgáltatásnév és a hozzájárulás delegálására szolgáló hivatkozások a bérlőben jönnek létre, és a natív alkalmazás jogkivonatokat kap az API-hoz.

A következő ábra áttekintést nyújt a különböző bérlők által regisztrált többrétegű alkalmazásokhoz.

![A többrétegű többszintű alkalmazás beleegyezik][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Beleegyezés visszavonása

A felhasználók és a rendszergazdák bármikor visszavonhatják az alkalmazáshoz való beleegyezést:

* A felhasználók visszavonják az egyes alkalmazásokhoz való hozzáférést, ha eltávolítják őket a [hozzáférési panel alkalmazásai][AAD-Access-Panel] listáról.
* A rendszergazdák visszavonják az alkalmazásokhoz való hozzáférést, ha eltávolítják őket a [Azure Portal][AZURE-portal] [vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) szakaszával.

Ha egy rendszergazda a bérlő összes felhasználója számára engedélyez egy alkalmazást, a felhasználók nem vonhatják vissza egyenként a hozzáférést. Csak a rendszergazda vonhatja vissza a hozzáférést, és csak a teljes alkalmazáshoz.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Több-bérlős alkalmazások és gyorsítótárazási hozzáférési tokenek

A több-bérlős alkalmazások hozzáférési tokeneket is kérhetnek az Azure AD által védett API-k meghívásához. Ha a Microsoft Authentication Library (MSAL) egy több-bérlős alkalmazással való használata esetén gyakran előfordul, hogy a/gyakori hibák-t használó felhasználó jogkivonat-tokent kér, választ kap, majd egy későbbi tokent kér ugyanahhoz a felhasználóhoz is a/Common. használatával. Mivel az Azure AD válasza egy bérlőtől származik, és nem/gyakori hibák, a MSAL gyorsítótárazza a jogkivonatot a bérlőtől. A/gyakori hibák a felhasználó hozzáférési jogkivonatának beolvasására irányuló hívása nem éri el a gyorsítótár bejegyzését, és a rendszer felszólítja a felhasználót, hogy jelentkezzen be újra. Ha el szeretné kerülni a gyorsítótár hiányzó számát, győződjön meg arról, hogy a bérlő végpontján már bejelentkezett felhasználóra vonatkozó további hívások történnek.

## <a name="related-content"></a>Kapcsolódó tartalom

* [Több-bérlős alkalmazás mintája](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Védjegyzési útmutató az alkalmazásokhoz][AAD-App-Branding]
* [Alkalmazás-és egyszerű szolgáltatások objektumai][AAD-App-SP-Objects]
* [Alkalmazások integrálása az Azure Active Directoryval][AAD-Integrating-Apps]
* [Az engedélyezési keretrendszer áttekintése][AAD-Consent-Overview]
* [Az API-engedélyek hatókörének Microsoft Graph][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre olyan alkalmazásokat, amelyek bármely Azure AD-bérlőből bejelentkezhetnek a felhasználókba. Miután engedélyezte az egyszeri Sign-On (SSO) használatát az alkalmazás és az Azure AD között, frissítheti az alkalmazást a Microsoft-erőforrások, például a Microsoft 365 számára elérhető API-k eléréséhez. Ez lehetővé teszi, hogy személyre szabott felhasználói élményt nyújtson az alkalmazásban, például a környezetfüggő információkat jelenítse meg a felhasználók számára, például a profil képét vagy a következő naptári időpontot.

Ha többet szeretne megtudni az Azure AD-hez és Microsoft 365 szolgáltatásokhoz, például az Exchange, a SharePoint, a OneDrive, a OneNote és más rendszerekhez, látogasson el [Microsoft Graph API][MSFT-Graph-overview]-ra.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken