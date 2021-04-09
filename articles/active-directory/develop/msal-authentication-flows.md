---
title: MSAL-hitelesítési folyamatok | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg a Microsoft Authentication Library (MSAL) által használt hitelesítési folyamatokkal és támogatásokkal.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 78932e5852453fe996e26a278f8a1859a8ecf546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755011"
---
# <a name="authentication-flows"></a>Hitelesítési folyamatok

A Microsoft Authentication Library (MSAL) számos hitelesítési folyamatot támogat a különböző alkalmazási forgatókönyvekben való használathoz.

| Folyamat | Leírás | Használatban |
|--|--|--|
| [Engedélyezési kód](#authorization-code) | Az eszközre telepített alkalmazásokban használatos a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez. Lehetővé teszi a bejelentkezés és az API-hozzáférés hozzáadását a mobil-és asztali alkalmazásokhoz. | [Asztali alkalmazások](scenario-desktop-overview.md), [Mobile apps](scenario-mobile-overview.md), [Web Apps](scenario-web-app-call-api-overview.md) |
| [Ügyfél-hitelesítő adatok](#client-credentials) | Lehetővé teszi, hogy egy alkalmazás identitásának használatával hozzáférjen a webkiszolgálón lévő erőforrásokhoz. Általában olyan kiszolgálók közötti interakciók esetében használatos, amelyeket a háttérben kell futtatni, anélkül, hogy a felhasználóval való azonnali interakcióra lenne szükség. | [Démonalkalmazások](scenario-daemon-overview.md) |
| [Eszköz kódja](#device-code) | Lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bemeneti korlátozás alá tartozó eszközökre, például egy intelligens TV-re, egy IoT-eszközre vagy egy nyomtatóra. | [Asztali/mobil alkalmazások](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Implicit támogatás](#implicit-grant) | Lehetővé teszi, hogy az alkalmazás jogkivonatokat kapjon a háttér-kiszolgáló hitelesítő adatainak cseréje nélkül. Lehetővé teszi, hogy az alkalmazás bejelentkezzen a felhasználóval, megtartsa a munkamenetet, és más webes API-kra is lekérje a jogkivonatokat, mindezt az ügyfél JavaScript-kódja | [Egyoldalas alkalmazások (SPA)](scenario-spa-overview.md) |
| [Nevében](#on-behalf-of) | Egy alkalmazás meghívja a szolgáltatást vagy a webes API-t, amely viszont egy másik szolgáltatást vagy webes API-t kell meghívnia. Az a cél, hogy a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül propagálja. | [Webes API-k](scenario-web-api-call-api-overview.md) |
| [Felhasználónév/jelszó](#usernamepassword) | Lehetővé teszi, hogy az alkalmazás közvetlenül a jelszavuk kezelését követően jelentkezzen be a felhasználóba. Ez a folyamat nem ajánlott. | [Asztali/mobil alkalmazások](scenario-desktop-acquire-token.md#username-and-password) |
| [Integrált Windows-hitelesítés](#integrated-windows-authentication) | Lehetővé teszi, hogy a tartományon vagy Azure Active Directoryon (Azure AD-ben) lévő alkalmazások a tokenek csendes beszerzéséhez (felhasználói FELÜLETi interakció nélkül) beszerezzék a jogkivonatot. | [Asztali/mobil alkalmazások](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki

Attól függően, hogy az ügyfélalkalmazás hogyan épül fel, a Microsoft Identity platform által támogatott hitelesítési folyamatok közül egy vagy több is használható. Ezek a folyamatok többféle típusú jogkivonatot és engedélyezési kódokat is létrehozhatnak, és a működésük érdekében különböző jogkivonatok szükségesek.

| Folyamat                                                                               | Igényel            | id_token | hozzáférési jogkivonat | jogkivonat frissítése | engedélyezési kód |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Ügyfél-hitelesítő adatok](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (csak alkalmazás) |               |                    |
| [Eszköz kódjának folyamata](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Implicit folyamat](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md)                                | hozzáférési jogkivonat        | x        | x            | x             |                    |
| [Felhasználónév/jelszó](v2-oauth-ropc.md) (ROPC)                                       | Felhasználónév & jelszó | x        | x            | x             |                    |
| [Hibrid OIDC folyamat](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Jogkivonat-beváltások frissítése](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | jogkivonat frissítése       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Interaktív és nem interaktív hitelesítés

Ezen folyamatok közül több is támogatja az interaktív és a nem interaktív jogkivonat-beszerzést.

  - Az **interaktív** beállítás azt jelenti, hogy a felhasználónak meg kell adnia a bevitelt. Tegyük fel például, hogy a felhasználónak be kell jelentkeznie, többtényezős hitelesítést (MFA) kell végrehajtania, vagy további hozzájárulást kell adnia az erőforrásokhoz.
  - **Nem interaktív** vagy *csendes* hitelesítés esetén a hitelesítési kísérlet olyan token beszerzésére, amelyben a bejelentkezési kiszolgáló *nem tud* további információkat kérni a felhasználótól.

A MSAL-alapú alkalmazásnak először is kísérletet kell tennie egy jogkivonat *csendes* beszerzésére, majd interaktív módon, ha a nem interaktív metódus meghiúsul. További információ erről a mintáról: [tokenek beszerzése és gyorsítótárazása a Microsoft Authentication Library (MSAL) használatával](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Engedélyezési kód

A [OAuth 2 engedélyezési kód megadása](v2-oauth2-auth-code-flow.md) az eszközre telepített alkalmazásokban használható a védett erőforrásokhoz, például a webes API-khoz való hozzáféréshez. Ez lehetővé teszi a bejelentkezést és API-hozzáférést a mobil-és asztali alkalmazásokhoz.

Amikor a felhasználók bejelentkeznek a webalkalmazásba (webhelyekre), a webalkalmazás megkapja az engedélyezési kódot. Az engedélyezési kód a webes API-k hívására szolgáló token beszerzéséhez lett beváltva.

![Az engedélyezési kód folyamatának ábrája](media/msal-authentication-flows/authorization-code.png)

Az előző ábrán az alkalmazás:

1. Egy hozzáférési jogkivonat számára beváltott engedélyezési kódot kér.
2. A hozzáférési token használatával hívja meg a webes API-t.

### <a name="considerations"></a>Megfontolandó szempontok

- A jogkivonat beváltásához csak egyszer használhatja az engedélyezési kódot. Ne próbálja meg többször bekérni a tokent ugyanazzal az engedélyezési kóddal, mert azt a protokoll szabványos specifikációja kifejezetten tiltja. Ha többször is beváltja a kódot, akár szándékosan, akár azért, mert nem tudja, hogy egy keretrendszer is elvégzi Önt, a következő hibaüzenet jelenik meg:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Ügyfél-hitelesítő adatok

A [2. OAuth ügyfél-hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md) lehetővé teszi, hogy egy alkalmazás identitásával hozzáférhessen a webkiszolgálók erőforrásaihoz. Ez az engedélytípus általában olyan kiszolgálóközi interakciók esetén használatos, amelyeknek a felhasználóval való azonnali interakció nélkül, a háttérben kell futniuk. Ezeket az alkalmazástípusokat gyakran démonoknak vagy szolgáltatásfiókoknak nevezik.

Az ügyfél hitelesítő adatainak megadása lehetővé teszi a webszolgáltatások (bizalmas ügyfél) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett a hitelesítéshez egy másik webszolgáltatás hívásakor. Ebben az esetben az ügyfél általában egy közepes szintű webszolgáltatás, egy démon-szolgáltatás vagy egy webhely. A Microsoft Identity platform lehetővé teszi, hogy a hívó szolgáltatás hitelesítő adatként használjon tanúsítványokat (közös titok helyett).

> [!NOTE]
> A bizalmas ügyfél folyamata nem érhető el olyan mobil platformokon, mint a UWP, a Xamarin. iOS és a Xamarin. Android, mert csak a nyilvános ügyfélalkalmazások használatát támogatják. A nyilvános ügyfélalkalmazások nem tudják, hogyan igazolják az alkalmazás identitását az identitás-szolgáltatónak. A biztonságos kapcsolat a webalkalmazásban vagy a webes API-ban is elérhető a tanúsítványok telepítésével.

### <a name="application-secrets"></a>Alkalmazás-titkok

![Bizalmas ügyfél jelszavas diagramja](media/msal-authentication-flows/confidential-client-password.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése az alkalmazás titkos kódjának vagy a jelszó hitelesítő adatainak használatával.
2. A jogkivonat használatával teszi elérhetővé az erőforrás kéréseit.

### <a name="certificates"></a>Tanúsítványok

![A bizalmas ügyfél tanúsítványának ábrája](media/msal-authentication-flows/confidential-client-certificate.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése a tanúsítvány hitelesítő adatainak használatával.
2. A jogkivonat használatával teszi elérhetővé az erőforrás kéréseit.

Ezeknek az ügyfél-hitelesítő adatoknak a következőkre van szükségük:

- Regisztrálva van az Azure AD-ben.
- Átadva a bizalmas ügyfélalkalmazás-objektum kódjában történő létrehozásakor.

## <a name="device-code"></a>Eszköz kódja

A [OAuth 2 eszköz kódja](v2-oauth2-device-code.md) lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bemeneti korlátozás alá tartozó eszközökre, például az intelligens televízióra, a IoT-eszközökre és a nyomtatókra. Az Azure AD-vel való interaktív hitelesítéshez webböngésző szükséges. Ha az eszköz vagy az operációs rendszer nem biztosít webböngészőt, az eszköz kódjának folyamata lehetővé teszi, hogy a felhasználó egy másik eszközt, például számítógépet vagy mobiltelefont használjon interaktív bejelentkezéshez.

Az eszköz kódjának használatával az alkalmazás egy kétlépéses folyamaton keresztül szerzi be a jogkivonatokat, amelyeket az eszközökhöz és az operációs rendszerekhez terveztek. Ilyen alkalmazások például a IoT-eszközökön és a parancssori felületi (CLI-) eszközökön futók.

![Az eszköz kódjának folyamatábrája](media/msal-authentication-flows/device-code.png)

A fenti diagram elemei:

1. Ha felhasználói hitelesítésre van szükség, az alkalmazás egy kódot biztosít, és arra kéri a felhasználót, hogy használjon egy másik eszközt, például egy internetkapcsolattal rendelkező okostelefont egy URL-cím megkereséséhez (például `https://microsoft.com/devicelogin` ). A rendszer ezután megkéri a felhasználót, hogy adja meg a kódot, és ha szükséges, folytassa a normál hitelesítési folyamaton, beleértve a hozzájárulási kéréseket és a [többtényezős hitelesítést](../authentication/concept-mfa-howitworks.md).
1. A sikeres hitelesítés után a parancssori alkalmazás egy visszaadott csatornán keresztül fogadja a szükséges jogkivonatokat, és ezeket használja a szükséges webes API-hívások végrehajtásához.

### <a name="constraints"></a>Korlátozások

- Az eszköz kódjának folyamata csak a nyilvános ügyfélalkalmazások esetében érhető el.
- A nyilvános ügyfélalkalmazás összeállításakor átadott hatóságnak a következők egyikének kell lennie:
  - Bérlő, abban az esetben, ha a vagy a bérlő `https://login.microsoftonline.com/{tenant}/,` `{tenant}` azonosítóját vagy a bérlőhöz társított tartománynevet jelképező GUID.
  - Munkahelyi és iskolai fiókokhoz az űrlapon `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Implicit támogatás

A [OAuth 2 implicit engedélyezési](v2-oauth2-implicit-grant-flow.md) folyamat lehetővé teszi, hogy az alkalmazás jogkivonatokat szerezzen be a Microsoft Identity platformról a háttér-kiszolgáló hitelesítő adatainak cseréje nélkül. Ez a folyamat lehetővé teszi, hogy az alkalmazás bejelentkezzen a felhasználóval, tartson fenn egy munkamenetet, és más webes API-k esetében jogkivonatokat kapjon, mindezt az ügyfél JavaScript-kódjában.

![Implicit engedélyezési folyamat ábrája](media/msal-authentication-flows/implicit-grant.svg)

Számos modern webalkalmazás a JavaScriptben vagy egy SPA-keretrendszerben (például szögletes, Vue.js és React.js) készült ügyféloldali, egyoldalas alkalmazásokban (SPA) van kialakítva. Ezek az alkalmazások webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások. A Microsoft Identity platform lehetővé teszi, hogy az egyoldalas alkalmazások bejelentkezzenek a felhasználókba, és jogkivonatokat kérjenek a háttér-szolgáltatások vagy webes API-k elérésére az implicit engedélyezési folyamat használatával. Az implicit folyamat lehetővé teszi, hogy az alkalmazás azonosító jogkivonatokat kapjon a hitelesített felhasználó képviseletére, valamint a védett API-k meghívásához szükséges jogkivonatok elérésére is.

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket (például Electron vagy React-Native) használnak, mivel további képességeket igényelnek a natív platformokkal való interakcióhoz.

Az implicit folyamat mód használatával kiállított tokenek **hossza korlátozott** , mert az URL-cím szerint (ahol `response_mode` a vagy a vagy a) a böngészőbe visszatérnek `query` `fragment` . Néhány böngésző korlátozza az URL-cím hosszát a böngésző sávján, és nem sikerül, ha túl hosszú. Ezért ezek az implicit flow-tokenek nem tartalmaznak `groups` vagy `wids` jogcímeket.

## <a name="on-behalf-of"></a>Nevében

A rendszer a [OAuth 2 hitelesítést](v2-oauth2-on-behalf-of-flow.md) használja, ha egy alkalmazás olyan szolgáltatást vagy webes API-t hív meg, amelyhez egy másik szolgáltatást vagy webes API-t kell meghívni. Az a cél, hogy a delegált felhasználói identitást és engedélyeket a kérési láncon keresztül propagálja. Ahhoz, hogy a középső szintű szolgáltatás hitelesített kéréseket továbbítson az alárendelt szolgáltatásnak, a felhasználó *nevében* védenie kell egy hozzáférési jogkivonatot a Microsoft Identity platformon.

![A folyamaton belüli adatforgalom diagramja](media/msal-authentication-flows/on-behalf-of.png)

A fenti diagram elemei:

1. Az alkalmazás egy hozzáférési jogkivonatot vásárol a webes API-hoz.
2. Egy ügyfél (web, Desktop, mobil vagy egylapos alkalmazás) egy védett webes API-t hív meg, amely a hozzáférési tokent tulajdonosi jogkivonatként adja hozzá a HTTP-kérelem hitelesítési fejlécéhez. A webes API hitelesíti a felhasználót.
3. Amikor az ügyfél meghívja a webes API-t, a webes API egy másik jogkivonatot kér a felhasználó nevében.
4. A védett webes API ezt a tokent használja az alárendelt webes API-k hívására a felhasználó nevében. A webes API később más alsóbb rétegbeli API-k számára is igényelhet jogkivonatokat (de ugyanazon felhasználó nevében továbbra is).

## <a name="usernamepassword"></a>Felhasználónév/jelszó

A [OAuth 2 erőforrás-tulajdonos jelszavas hitelesítő adatok](v2-oauth-ropc.md) (ROPC) megadása lehetővé teszi, hogy az alkalmazás közvetlenül a jelszavának kezelésével jelentkezzen be a felhasználóba. Az asztali alkalmazásban a username/Password folyamat használatával csendesen lehet lekérni a jogkivonatot. Az alkalmazás használatakor nincs szükség felhasználói felületre.

![A Felhasználónév/jelszó folyamat ábrája](media/msal-authentication-flows/username-password.png)

Az előző ábrán az alkalmazás:

1. A rendszer a Felhasználónév és a jelszó megadásával szerzi be a jogkivonatot az identitás-szolgáltatónak.
2. Meghívja a webes API-t a token használatával.

> [!WARNING]
> Ez a folyamat nem ajánlott. Magas fokú megbízhatóságot és hitelesítő adatokat igényelnek. Ezt a folyamatot *csak* akkor érdemes használni, ha nem használhatók több biztonságos folyamat. További információ: [Mi a megoldás a jelszavak egyre növekvő problémájára?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

A jogkivonatok Windows-tartományhoz csatlakoztatott gépeken való csendes beszerzésének előnyben részesített folyamata [integrált Windows-hitelesítés](#integrated-windows-authentication). Más esetekben használja az [eszköz kódjának folyamatát](#device-code).

Bár a Felhasználónév/jelszó folyamata hasznos lehet bizonyos helyzetekben, például a DevOps-ben, kerülje azt, ha olyan interaktív helyzetekben szeretné használni a felhasználónevet és a jelszót, ahol a saját felhasználói felületét adja meg.

Felhasználónév/jelszó használatával:

- A többtényezős hitelesítést elvégző felhasználók nem fognak tudni bejelentkezni, mert nincs interakció.
- A felhasználók nem tudnak egyszeri bejelentkezést végezni.

### <a name="constraints"></a>Korlátozások

Az [integrált Windows-hitelesítési korlátozásokon](#integrated-windows-authentication)kívül a következő korlátozások is érvényesek:

- A Felhasználónév/jelszó folyamat nem kompatibilis a feltételes hozzáféréssel és a többtényezős hitelesítéssel. Ennek következményeként, ha az alkalmazás egy Azure AD-bérlőn fut, ahol a bérlői rendszergazda a többtényezős hitelesítést igényli, nem használhatja ezt a folyamatot. Számos szervezet ezt teszi.
- A ROPC csak munkahelyi és iskolai fiókokhoz használható. A Microsoft-fiókok (MSA) ROPC nem használhatók.
- A folyamat elérhető a .NET Desktopban és a .NET Core-on, de nem Univerzális Windows-platformon.
- Azure AD B2C a ROPC folyamat csak helyi fiókok esetében működik. A MSAL.NET és a Azure AD B2C ROPC kapcsolatos információkért lásd: a [ROPC és a Azure ad B2C használata](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Beépített Windows-hitelesítés

A MSAL támogatja az integrált Windows-hitelesítést (IWA) olyan asztali és mobil alkalmazások esetében, amelyek tartományhoz csatlakoztatott vagy Azure AD-hez csatlakoztatott Windows-számítógépen futnak. Az IWA használatával ezek az alkalmazások csendesen vásárolhatnak tokent anélkül, hogy a felhasználónak felhasználói FELÜLETi interakcióra lenne szükségük.

![Integrált Windows-hitelesítés diagramja](media/msal-authentication-flows/integrated-windows-authentication.png)

Az előző ábrán az alkalmazás:

1. Jogkivonat beszerzése integrált Windows-hitelesítés használatával.
2. A jogkivonat használatával teszi elérhetővé az erőforrás kéréseit.

### <a name="constraints"></a>Korlátozások

Az integrált Windows-hitelesítés (IWA) *csak* az összevont felhasználók számára támogatja – a Active Directoryban létrehozott és az Azure ad által támogatott felhasználók. A közvetlenül az Azure AD-ben létrehozott felhasználók Active Directory biztonsági mentés nélkül (felügyelt felhasználók) nem használhatják ezt a hitelesítési folyamatot. Ez a korlátozás nem befolyásolja a [Felhasználónév/jelszó folyamatát](#usernamepassword).

A IWA a .NET-keretrendszer, a .NET Core és a Univerzális Windows-platform alkalmazások esetében használható.

A IWA nem kerüli el a többtényezős hitelesítést. Ha a többtényezős hitelesítés konfigurálva van, a IWA sikertelen lehet, ha egy multi-Factor Authentication-kihívásra van szükség. A többtényezős hitelesítéshez felhasználói beavatkozás szükséges.

Nem szabályozhatja, hogy az identitás-szolgáltató Mikor kér kétfaktoros hitelesítést. A bérlői rendszergazda nem. A kétfaktoros hitelesítés általában akkor szükséges, ha egy másik országból vagy régióból jelentkezik be, ha nem VPN-kapcsolaton keresztül csatlakozik a vállalati hálózathoz, és néha még akkor is, ha VPN-kapcsolaton keresztül csatlakozik. Az Azure AD a mesterséges intelligencia használatával folyamatosan megtanulja, hogy kétfaktoros hitelesítésre van-e szükség. Ha a IWA sikertelen, akkor vissza kell térnie egy [interaktív felhasználói kérésre](#interactive-and-non-interactive-authentication).

A nyilvános ügyfélalkalmazás létrehozásakor átadott hatóságnak az alábbiak egyikének kell lennie:

- Bérlő, abban az esetben, ha a vagy a bérlő `https://login.microsoftonline.com/{tenant}/,` `{tenant}` azonosítóját vagy a bérlőhöz társított tartománynevet jelképező GUID.
- Bármilyen munkahelyi és iskolai fiókhoz ( `https://login.microsoftonline.com/organizations/` ). A Microsoft Personal accounts (MSA) nem támogatott; nem használhatók `/common` vagy `/consumers` bérlők.

Mivel a IWA egy csendes folyamat, a következők egyikének igaznak kell lennie:

- Az alkalmazás felhasználójának előzőleg el kell juttatnia az alkalmazás használatát.
- A bérlői rendszergazdának előzőleg el kell juttatnia a bérlő összes felhasználója számára az alkalmazás használatát.

Ez azt jelenti, hogy az alábbiak egyike igaz:

- A fejlesztőknek Ön **is kiválasztotta a Azure Portal** saját magának.
- A bérlői rendszergazda kiválasztotta az alkalmazás regisztrációjának **engedélyezése/visszavonása a következőhöz: {bérlő tartomány}** **a** Azure Portalban (lásd a [webes API eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)című témakört).
- Megadta a felhasználók számára az alkalmazáshoz való hozzájárulásukat. Lásd: [egyéni felhasználói engedély kérése](v2-permissions-and-consent.md#requesting-individual-user-consent).
- Megadta a lehetőséget, hogy a bérlői rendszergazda beleegyezett az alkalmazásba; Lásd: [rendszergazdai engedély](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

A IWA folyamat engedélyezve van a .NET Desktop, a .NET Core és a Windows Universal platform alkalmazásaihoz.

További információ a beleegyező adatokról: [v 2.0 engedélyek és beleegyezik](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette a Microsoft Authentication Library (MSAL) által támogatott hitelesítési folyamatokat, megismerheti az ezekben a folyamatokban használt jogkivonatok beszerzését és gyorsítótárazását:

[Tokenek beszerzése és gyorsítótárazása a Microsoft Authentication Library (MSAL) használatával](msal-acquire-cache-tokens.md)
