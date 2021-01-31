---
title: Ajánlott eljárások a Microsoft Identity platformhoz | Azure
description: Ismerje meg az ajánlott eljárásokat, ajánlásokat és általános áttekintéseket a Microsoft Identity platformmal való integráció során.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: a6a7bf24571660d8e728c1acba29af2504539a18
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219960"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft Identity platform – ajánlott eljárások és javaslatok

Ez a cikk az ajánlott eljárásokat, javaslatokat és általános áttekintéseket ismerteti a Microsoft Identity platformmal való integráció során.  Ez az ellenőrzőlista kiváló minőségű és biztonságos integrációt biztosít. Rendszeresen tekintse át a listát, és győződjön meg róla, hogy az alkalmazás az Identity platformmal való integrációjának minőségét és biztonságát tartja karban. Az ellenőrzőlista nem alkalmas a teljes alkalmazás áttekintésére. Az ellenőrzőlista tartalma változhat, ahogy javítunk a platformon.

Ha most ismerkedik az első lépésekkel, tekintse meg a [Microsoft Identity platform dokumentációját](index.yml) , ahol megismerheti a hitelesítési alapismereteket, az alkalmazás forgatókönyveit a Microsoft Identity platformon, és így tovább.

Az alábbi ellenőrzőlista használatával biztosíthatja, hogy az alkalmazás hatékonyan legyen integrálva a [Microsoft Identity platformmal](./index.yml).

> [!TIP]
> A Azure Portal *integrációs asszisztense* segíthet az ajánlott eljárások és javaslatok alkalmazásában. Válassza ki a Azure Portal valamelyik [alkalmazás-regisztrációját](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) , majd válassza az **integrációs asszisztens** menüpontot a Segéd megkezdéséhez.

## <a name="basics"></a>Alapbeállítások

![jelölőnégyzet ](./media/active-directory-integration-checklist/checkbox-two.svg) beolvasása és a [Microsoft-Platform házirendjeinek](/legal/microsoft-identity-platform/terms-of-use)megismerése. Győződjön meg arról, hogy az alkalmazás megfelel a felhasználók és a platform elleni védelemhez tervezett feltételeknek.

## <a name="ownership"></a>Tulajdonjog

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Győződjön meg arról, hogy az alkalmazások regisztrálásához és kezeléséhez használt fiókhoz tartozó információk naprakészek.

## <a name="branding"></a>Arculat

![a jelölőnégyzet be van jelölve ](./media/active-directory-integration-checklist/checkbox-two.svg) az [alkalmazások védjegyezési irányelveinek](howto-add-branding-in-azure-ad-apps.md).

![Jelölje be a jelölőnégyzetet az ](./media/active-directory-integration-checklist/checkbox-two.svg) alkalmazáshoz tartozó név és embléma megadásával. Ezek az információk az [alkalmazás jóváhagyására vonatkozó kérdésben](application-consent-experience.md)jelennek meg. Győződjön meg arról, hogy a név és az embléma megfelel a vállalatának vagy termékének, hogy a felhasználók tájékozott döntéseket hozhassanak. Győződjön meg arról, hogy nem sérti a védjegyeket.

## <a name="privacy"></a>Adatvédelem

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Adja meg az alkalmazás használati feltételeire és adatvédelmi nyilatkozatára mutató hivatkozásokat.

## <a name="security"></a>Biztonság

![Jelölje ](./media/active-directory-integration-checklist/checkbox-two.svg) be az átirányítási URI-k kezelése jelölőnégyzetet: <ul><li>Őrizze meg az összes átirányítási URI tulajdonosát, és tartsa naprakészen a DNS-rekordokat.</li><li>Ne használjon helyettesítő karaktereket (*) az URI-k között.</li><li>Webalkalmazások esetén győződjön meg arról, hogy az összes URI biztonságos és titkosított (például https-sémák használatával).</li><li>Nyilvános ügyfelek esetén használjon platform-specifikus átirányítási URI-ket, ha vannak ilyenek (főleg az iOS és az Android esetében). Ellenkező esetben használja a nagy mennyiségű véletlenszerű átirányítási URI-t, hogy elkerülje az ütközéseket, amikor visszahívja az alkalmazást.</li><li>Ha az alkalmazást egy elkülönített webügynök használja, akkor használhatja a következőt: `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Tekintse át és vágja fel az összes fel nem használt vagy szükségtelen átirányítási URI-t a szokásos módon.</li></ul>

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Ha az alkalmazás regisztrálva van egy címtárban, csökkentse és manuálisan figyelje az alkalmazás-regisztrációs tulajdonosok listáját.

![](./media/active-directory-integration-checklist/checkbox-two.svg)a jelölőnégyzet nem engedélyezi a [OAuth2 implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) támogatását, kivéve, ha explicit módon szükséges. [Itt](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)tájékozódhat az érvényes forgatókönyvről.

![Jelölje be a ](./media/active-directory-integration-checklist/checkbox-two.svg) Felhasználónév/jelszó fölé. Ne használja az [erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC)](v2-oauth-ropc.md), amely közvetlenül kezeli a felhasználók jelszavát. Ennek a folyamatnak magas fokú megbízhatóságra és felhasználói expozícióra van szüksége, és csak akkor használható, ha más, biztonságosabb, a folyamatok nem használhatók. Erre a folyamatra továbbra is szükség van bizonyos helyzetekben (például DevOps), de ügyeljen arra, hogy a használatával korlátozásokat fog alkalmazni az alkalmazására.  A modernebb megközelítésekhez olvassa el a [hitelesítési folyamatokat és az alkalmazás forgatókönyveit](authentication-flows-app-scenarios.md).

![jelölőnégyzet ](./media/active-directory-integration-checklist/checkbox-two.svg) : a webalkalmazások, a webes API-k és a Daemon-alkalmazások bizalmas alkalmazásbeli hitelesítő adatainak védelme és kezelése. [Tanúsítvány hitelesítő adatainak](active-directory-certificate-credentials.md)használata, nem a jelszó hitelesítő adatai (az ügyfél titkos kulcsa). Ha jelszó-hitelesítő adatokat kell használnia, ne állítsa be manuálisan. Ne tárolja a hitelesítő adatokat a kódban vagy a konfigurációban, és soha ne engedélyezze őket emberek általi kezelésre. Ha lehetséges, használjon [felügyelt identitásokat az Azure-erőforrásokhoz](../managed-identities-azure-resources/overview.md) vagy [Azure Key Vault](../../key-vault/general/basic-concepts.md) a hitelesítő adatok tárolásához és rendszeres elforgatásához.

![Jelölje be a jelölőnégyzetet, és ](./media/active-directory-integration-checklist/checkbox-two.svg) Győződjön meg arról, hogy az alkalmazás a legkevésbé megfelelő jogosultságokat kéri Csak olyan engedélyeket kérjen, amelyeket az alkalmazásnak teljesen szüksége van, és csak akkor, amikor szüksége van rájuk. Ismerje meg a különböző [típusú engedélyeket](v2-permissions-and-consent.md#permission-types). Csak szükség esetén használja az alkalmazás engedélyeit; Ha lehetséges, használjon delegált engedélyeket. Az Microsoft Graph engedélyek teljes listájáért tekintse meg ezt az [engedélyeket](/graph/permissions-reference)ismertető témakört.

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Ha a Microsoft Identity platform használatával biztosít API-t, alaposan gondolja át, hogy milyen engedélyekkel kell rendelkeznie. Gondolja át, hogy mi a megfelelő részletesség a megoldáshoz, és hogy a jogosultság (ok) rendszergazdai hozzájárulást igényel. Az engedélyezési döntések meghozatala előtt keresse meg a várt engedélyeket a bejövő jogkivonatokban.

## <a name="implementation"></a>Implementálás

![](./media/active-directory-integration-checklist/checkbox-two.svg)a jelölőnégyzet modern hitelesítési megoldásokat használ (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) a felhasználók biztonságos bejelentkezéséhez.

![](./media/active-directory-integration-checklist/checkbox-two.svg)a jelölőnégyzet nem közvetlenül olyan protokollok ellen, mint a OAuth 2,0 és az Open id. Ehelyett használja a [Microsoft hitelesítési függvénytárát (MSAL)](msal-overview.md). A MSAL-kódtárak biztonságos módon csomagolják a biztonsági protokollokat egy könnyen használható könyvtárban, és beépített támogatást biztosít a [feltételes hozzáférési](../conditional-access/overview.md) forgatókönyvek, az eszközre érvényes [egyszeri bejelentkezés (SSO)](../manage-apps/what-is-single-sign-on.md)és a beépített jogkivonat-gyorsítótárazás támogatásához. További információ: a Microsoft által támogatott [ügyféloldali kódtárak](reference-v2-libraries.md)listája. Ha meg kell adnia a hitelesítési protokollok kódját, kövesse a [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx) vagy más hasonló fejlesztési módszertant. Ügyeljen arra, hogy az egyes protokollokra vonatkozó szabványok részletes leírásában a biztonsági szempontokat kell figyelembe venni.

![Jelölje ](./media/active-directory-integration-checklist/checkbox-two.svg) be a meglévő alkalmazások migrálása [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) szolgáltatásból a [Microsoft hitelesítési könyvtárra](msal-overview.md)jelölőnégyzetet. A MSAL a Microsoft legújabb Identity platform-megoldása, és a ADAL előnyben részesített. A szolgáltatás .NET, JavaScript, Android, iOS, macOS rendszeren érhető el, és a Python és a Java nyilvános előzetes verziójában is elérhető. További információ a [ADAL.net](msal-net-migration.md), a [ADAL.js](msal-compare-msal-js-and-adal-js.md)és a [ADAL.net és az iOS Broker-](msal-net-migration-ios-broker.md) alkalmazások áttelepítéséről.

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) A Mobile apps esetében minden platformot az alkalmazás regisztrációs felületének használatával konfigurálhat. Ahhoz, hogy az alkalmazás kihasználja az egyszeri bejelentkezés Microsoft Authenticator vagy a Microsoft céges portál előnyeit, az alkalmazásnak "közvetítő átirányítási URI-t" kell beállítania. Ez lehetővé teszi a Microsoft számára, hogy a hitelesítés után visszaadja a vezérlést az alkalmazásnak. Az egyes platformok konfigurálásakor az alkalmazás regisztrációs felülete végigvezeti Önt a folyamaton. Egy működő példa letöltéséhez használja a rövid útmutatót. IOS rendszeren használja a Brokers és a System Webview lehetőséget, amikor csak lehetséges.

![jelölőnégyzet ](./media/active-directory-integration-checklist/checkbox-two.svg) a Web Apps vagy a webes API-k esetében a fiókon belül egy jogkivonat-gyorsítótárat kell megőrizni.  A webalkalmazások esetében a jogkivonat-gyorsítótárat a fiók AZONOSÍTÓjának kell megadnia.  Webes API-k esetében a fióknak az API meghívásához használt jogkivonat kivonatával kell megjelennie. A MSAL.NET egyéni jogkivonat-gyorsítótárazási szerializálást biztosít a .NET-keretrendszer és a .NET Core alplatformok számára. A biztonság és a teljesítmény érdekében javasoljuk, hogy felhasználónként egy gyorsítótárat szerializáljon. További információ: a jogkivonat- [gyorsítótár szerializálásának](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)áttekintése.

![Jelölje ](./media/active-directory-integration-checklist/checkbox-two.svg) be a jelölőnégyzetet, ha az alkalmazás által igényelt [adatMicrosoft Graphon](https://developer.microsoft.com/graph)keresztül érhető el, és az egyéni API helyett a Microsoft Graph végponton keresztül kérjen engedélyeket ezekre az adatvédelemre.

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Ne tekintse meg a hozzáférési jogkivonat értékét, vagy próbálja meg ügyfélként elemezni.  Megváltoztathatják az értékeket, a formátumokat, vagy akár a figyelmeztetés nélkül is titkosítva lesznek – mindig a id_token használja, ha az ügyfélnek valamit kell megtanulnia a felhasználóval kapcsolatban, vagy a Microsoft Graph meghívását.  Csak a webes API-knak kell elemezni a hozzáférési jogkivonatokat (mivel ezek a formátum és a titkosítási kulcsok beállítása).

## <a name="end-user-experience"></a>Végfelhasználói élmény

![jelölőnégyzet bejelölésével ](./media/active-directory-integration-checklist/checkbox-two.svg) [megismerheti a hozzájárulási élményt](application-consent-experience.md) , és konfigurálhatja az alkalmazás beleegyezési kérésének részleteit, hogy a végfelhasználók és a rendszergazdák elegendő információval rendelkezzenek az alkalmazás megbízhatóságának megállapításához.

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Csökkentse azt az időtartamot, ameddig a felhasználónak be kell írnia a bejelentkezési hitelesítő adatokat, miközben az alkalmazás használata közben csendes hitelesítést (csendes jogkivonat-beszerzést) kell megadnia az interaktív folyamatok előtt.

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Minden bejelentkezéshez ne használja a "prompt = beleegyezés" kifejezést. Csak a prompt = beleegyezés használata, ha úgy döntött, hogy további engedélyek megadását kell kérnie (például ha módosította az alkalmazás szükséges engedélyeit).

![Jelölje ](./media/active-directory-integration-checklist/checkbox-two.svg) be a megfelelő jelölőnégyzetet, és bővítse az alkalmazást felhasználói adataival. A [Microsoft Graph API](https://developer.microsoft.com/graph) használatával egyszerűen elvégezhető a megoldás. A [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) eszköz, amely segítséget nyújt az első lépésekhez.

![Jelölje be ](./media/active-directory-integration-checklist/checkbox-two.svg) az alkalmazás által igényelt összes engedély teljes készletét, hogy a rendszergazdák könnyen megadhatják a hozzájárulásukat a bérlőik számára. Futtassa a [növekményes beleegyezést](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) a futtatáskor, hogy a felhasználók tisztában legyenek azzal, hogy az alkalmazás Miért kér olyan engedélyeket, amelyek az első indításkor érinthetik vagy megzavarják a felhasználókat.

![jelölőnégyzet ](./media/active-directory-integration-checklist/checkbox-two.svg) a [tiszta egyszeri kijelentkezési élmény](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)megvalósítása. Ez egy adatvédelmi és biztonsági követelmény, és jó felhasználói élményt tesz lehetővé.

## <a name="testing"></a>Tesztelés

![jelölőnégyzetes ](./media/active-directory-integration-checklist/checkbox-two.svg) teszt olyan [feltételes hozzáférési házirendekhez](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , amelyek befolyásolhatják a felhasználók által az alkalmazás használatát.

![jelölőnégyzet](./media/active-directory-integration-checklist/checkbox-two.svg) Tesztelje alkalmazását az összes olyan lehetséges fiókkal, amelyet támogatni kíván (például munkahelyi vagy iskolai fiókok, személyes Microsoft-fiókok, alárendelt fiókok és szuverén fiókok).

## <a name="additional-resources"></a>További források

Részletes információk a 2.0-s verzióról:

* [Microsoft Identity platform (áttekintés)](v2-overview.md)
* [Microsoft Identity platform-protokollok – dokumentáció](active-directory-v2-protocols.md)
* [Hozzáférési jogkivonatok referenciája](access-tokens.md)
* [Azonosító jogkivonatok referenciája](id-tokens.md)
* [A hitelesítési kódtárak referenciája](reference-v2-libraries.md)
* [Engedélyek és beleegyezett a Microsoft Identity platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
