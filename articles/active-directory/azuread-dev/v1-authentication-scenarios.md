---
title: Azure AD fejlesztők számára (v 1.0) | Azure
description: Ismerje meg a fejlesztők számára készült Azure AD-alapú hitelesítési alapismereteket, például az alkalmazás modelljét, az API-t, a kiépítés és a leggyakoribb hitelesítési forgatókönyveket.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 36b39f3706db615e40ebfadebf36be4d8b29c33e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154729"
---
# <a name="what-is-authentication"></a>Mi a hitelesítés?

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A *hitelesítési* valamely fél érvényes hitelesítő adatainak bekérése, amely egy identitás- és hozzáférés-vezérléshez használható rendszerbiztonsági tag létrehozását eredményezi. Egyszerűbb megfogalmazásban a hitelesítés az a folyamat, amelynek során a felhasználó bebizonyítja, hogy az, akinek vallja magát. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni.

Az *engedélyezés* egy műveletre jogosító engedély megadása egy hitelesített rendszerbiztonsági tag számára. Meghatározza az elérhető adatok körét és az azokon végrehajtható műveleteket. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni.

A fejlesztők számára készült Azure Active Directory (v 1.0) (Azure AD) leegyszerűsíti az alkalmazás-fejlesztők hitelesítését szolgáltatásként, az iparági szabványnak megfelelő protokollok, például a OAuth 2,0 és az OpenID Connect támogatásával, valamint a különböző platformokhoz használható nyílt forráskódú kódtárakat, amelyek segítségével gyorsan elindíthatja a kódolást.

Az Azure AD programozási modellben két alapvető használati eset létezik:

* Az OAuth 2.0 engedélyezési folyamat – amikor az erőforrás tulajdonosa adja meg az engedélyt az ügyfélalkalmazás számára, hogy az elérhesse az erőforrás-tulajdonos erőforrásait.
* Az erőforrások ügyfél általi elérése – ezt az erőforrás-kiszolgáló valósítja meg a hozzáférési jogkivonatokban foglalt jogcímértékek alapján, amelyek a vonatkozó hozzáférés-vezérlési döntések meghozatalát irányítják.

## <a name="authentication-basics-in-azure-ad"></a>A hitelesítés alapjai Az Azure AD-ben

Vegyük a legalapvetőbb forgatókönyvet, amelyben az identitásokat alkalmazni kell: a felhasználónak a böngészőben hitelesítenie kell magát egy webalkalmazásban. Az alábbi ábra ezt a forgatókönyvet mutatja be:

![Webalkalmazásba való bejelentkezés áttekintése](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

A diagramon látható különböző összetevőkkel kapcsolatban a következőket kell tudnia:

* Az Azure AD az identitásszolgáltató. Az Identitáskezelő feladata a szervezet címtárában lévő felhasználók és alkalmazások identitásának ellenőrzése, valamint a felhasználók és alkalmazások sikeres hitelesítése esetén a biztonsági jogkivonatok kibocsátása.
* Azure Active Directory (Azure AD) regisztrálni kell egy olyan alkalmazást, amely kiszervezi az Azure AD-hitelesítést. Az Azure AD regisztrálja és egyedileg azonosítja az alkalmazást a címtárban.
* A fejlesztők az Azure AD nyílt forrású hitelesítési kódtáraival egyszerűen valósíthatják meg a hitelesítést, mivel a protokolladatok kezelését az Azure AD végzi. További információ: Microsoft Identity platform 2.0-s [hitelesítési kódtárak](../develop/reference-v2-libraries.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) és 1.0-s verziójú [hitelesítési kódtárak](active-directory-authentication-libraries.md).
* Miután a felhasználó hitelesítése megtörtént, az alkalmazásnak ellenőriznie kell a felhasználó biztonsági jogkivonatát, hogy a hitelesítés sikeres legyen. A rövid útmutatók, oktatóanyagok és a különböző nyelvekhez és keretrendszerekhez elérhető kódminták mutatják be, hogy az alkalmazásnak mit kell tennie.
  * Az alkalmazások gyors felépítésével és a jogkivonatok beszerzését és frissítését, a felhasználók bejelentkeztetését és a felhasználóadatok megjelenítését végző funkcionalitás hozzáadásával kapcsolatban lásd a dokumentáció **Rövid útmutatók** szakaszát.
  * A hitelesítéssel kapcsolatos legfontosabb fejlesztői feladatok elvégzéséhez, például a hozzáférési jogkivonatok beszerzéséhez és azok Microsoft Graph API és egyéb API-k hívásaiban való használatához, a hagyományos böngészőalapú alkalmazásokon belüli, OpenID Connect használatával végzett Microsoft-bejelentkezések megvalósításához, valamint hasonló feladatokhoz használható részletes, forgatókönyv-alapú eljárásokat a dokumentáció **Oktatóanyagok** szakaszában találja.
  * A kódminták letöltéséhez látogasson el a [GitHubra](https://github.com/Azure-Samples?q=active-directory).
* A hitelesítési folyamathoz használt kérés-válasz folyamatot az alkalmazott hitelesítési protokoll határozza meg, például az OAuth 2.0, az OpenID Connect, a WS-Federation vagy az SAML 2.0. A protokollokkal kapcsolatos további információkért tekintse meg a dokumentációjának **fogalmak > hitelesítési protokoll** című szakaszát.

A fenti példaforgatókönyvben az alkalmazásokat az alábbi két szerepkör szerint osztályozhatjuk:

* Olyan alkalmazások, amelyeknek biztonságosan kell hozzáférniük az erőforrásokhoz
* Olyan alkalmazások, amelyek az erőforrások szerepét töltik be

### <a name="how-each-flow-emits-tokens-and-codes"></a>Az egyes folyamatok tokeneket és kódokat bocsátanak ki

Az ügyfél felépítésének módjától függően az Azure AD által támogatott hitelesítési folyamatok közül egy (vagy több) is használható. Ezek a folyamatok különféle tokeneket (id_tokens, frissítési tokeneket, hozzáférési tokeneket) és engedélyezési kódokat hozhatnak létre, és különböző jogkivonatokat igényelnek a működésük érdekében. Ez a diagram áttekintést nyújt:

|Folyamat | Igényel | id_token | hozzáférési jogkivonat | jogkivonat frissítése | engedélyezési kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Engedélyezési kód folyamatábrája](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Implicit folyamat](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hibrid OIDC folyamat](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Jogkivonat-beváltások frissítése](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | jogkivonat frissítése | x | x | x| |
|[Meghatalmazásos folyamat](v1-oauth2-on-behalf-of-flow.md) | hozzáférési jogkivonat| x| x| x| |
|[Ügyfél-hitelesítő adatok](v1-oauth2-client-creds-grant-flow.md) | | | x (csak alkalmazás)| | |

Az implicit módban kiállított tokenek hossza korlátozott, mert az URL-cím (ahol a vagy a) a böngészőbe kerül vissza `response_mode` `query` `fragment` .  Egyes böngészőkben korlátozva van a böngészőablakban elhelyezhető URL-cím mérete, és a művelet nem hajtható végre, ha túl hosszú.  Így ezek a jogkivonatok nem rendelkeznek `groups` vagy `wids` jogcímek. 

Most, hogy az alapokat áttekintettük, a továbbiakban megismerkedhet az identitáskezelési alkalmazásmodellel és API-val, valamint az Azure AD-beli kiépítés működésével, továbbá az Azure AD által támogatott gyakori forgatókönyvekre mutató hivatkozásokat találhat.

## <a name="application-model"></a>Alkalmazásmodell

Az Azure AD az alkalmazásokat egy specifikus modell szerint képezi le, amely két fő funkció kiszolgálására lett kialakítva:

* **Az alkalmazás azonosítása a támogatott hitelesítési protokollnak megfelelően** – Ez az összes azonosító, URL-cím, titkos kulcs és a hitelesítés alkalmával szükséges összes vonatkozó információ számba vételét jelenti. Az Azure AD ebben az esetben:

    * Tárolja a futásidejű hitelesítés támogatásához szükséges összes adatot.
    * Tárolja a szükséges adatokat annak eldöntéséhez, hogy az alkalmazásoknak milyen erőforrásokat kellhet elérniük, és az adott kéréseket teljesíteni kell-e, illetve milyen körülmények között kell teljesíteni.
    * Biztosítja az infrastruktúrát az alkalmazásregisztráció megvalósításához az alkalmazásfejlesztő bérlőjén és a többi Azure AD-bérlőn.

* **Felhasználói hozzájárulás kezelése a jogkivonat-igénylés alkalmával és az alkalmazások dinamikus kiépítésének elősegítése a bérlők közt** – Az Azure AD ebben az esetben:

    * Lehetővé teszi, hogy a felhasználó vagy a rendszergazda dinamikusan megadhassa vagy megtagadhassa a hozzájárulást, hogy az alkalmazás a nevében elérhesse az erőforrásokat.
    * Lehetővé teszi, hogy a rendszergazda alapvetően meghatározhassa, hogy az alkalmazások mely tevékenységei engedélyezettek, mely felhasználók mely alkalmazásokat használhatják, és hogy a címtárerőforrások hogyan érhetők el.

Az Azure AD az alkalmazásokat absztrakt entitásokként, **alkalmazásobjektumokkal** írja le. A fejlesztők alkalmazásokkal dolgoznak. Az üzembe helyezés során az Azure AD az adott alkalmazásobjektumot tervként használva létrehoz egy **szolgáltatásnevet**, amely az alkalmazás konkrét példányát jelöli egy adott címtárban vagy bérlőn. Ez a szolgáltatásnév határozza meg, hogy az alkalmazás ténylegesen milyen tevékenységeket hajthat végre az adott célcímtárban, mely erőforrásokat érheti el, és így tovább. Az Azure AD a szolgáltatásneveket az alkalmazásobjektumokból **hozzájárulás** alapján hozza létre.

Az alábbi ábrán az Azure AD hozzájárulás-alapú kiépítési folyamatának egyszerűsített vázát mutatja.  Ebben az esetben két bérlő létezik (A és B), ahol a bérlő az alkalmazás tulajdonosa, a B bérlő pedig az alkalmazást egy egyszerű szolgáltatásnév használatával hozza létre.  

![Hozzájárulás-alapú egyszerűsített kiépítési folyamat](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

A kiépítési folyamat:

1. A B bérlő felhasználója megpróbál bejelentkezni az alkalmazásba, az engedélyezési végpont jogkivonatot kér az alkalmazáshoz.
1. A felhasználói hitelesítő adatok beszerzése és ellenőrzése hitelesítéssel történik.
1. A rendszer megkéri a felhasználót, hogy adjon hozzáférést az alkalmazásnak a B bérlőhöz való hozzáféréshez.
1. Az Azure AD az A bérlő Application objektumát használja tervként egy egyszerű szolgáltatásnév létrehozásához a B bérlőben
1. A felhasználó megkapja az igényelt jogkivonatot

A folyamat tetszőleges mennyiségben ismételhető a többi bérlőre (C, D és így tovább) vonatkozóan. Az A bérlő megőrzi az alkalmazás tervét (Application Object). A többi bérlőn, amelyekre az alkalmazás hozzájárulást kapott, továbbra is a felhasználók és a rendszergazdák határozhatják meg az egyedi bérlőkön lévő megfelelő szolgáltatásnév-objektumokon keresztül, hogy az alkalmazás milyen tevékenységeket hajthat végre. További információ: [alkalmazás-és szolgáltatásnév-objektumok a Microsoft Identity platformon](../develop/app-objects-and-service-principals.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="claims-in-azure-ad-security-tokens"></a>Jogcímek az Azure AD biztonsági jogkivonataiban

Az Azure AD által kiadott biztonsági jogkivonatok (hozzáférési és azonosító jogkivonatok) jogcímeket, azaz a hitelesített alanyra vonatkozó megerősített információkat tartalmaznak. Az alkalmazások a jogcímeket különféle műveletekhez használhatják:

* A jogkivonat érvényesítése
* Az alany címtárbérlőjének azonosítása
* A felhasználó adatainak megjelenítése
* Az alany engedélyezésének meghatározása

Az egyes biztonsági jogkivonatokban lévő jogcímek a jogkivonat típusától, a felhasználó hitelesítéséhez használt hitelesítő adatoktól és az alkalmazás konfigurációjától függenek.

Az Azure AD által kibocsátott egyes jogcímek rövid leírását az alábbi táblázat tartalmazza. Részletesebb információkért tekintse meg az Azure AD által kiadott [hozzáférési jogkivonatokat](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) és [azonosító jogkivonatokat](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) .

| Jogcím | Description |
| --- | --- |
| Alkalmazásazonosító | A jogkivonatot használó alkalmazást azonosítja. |
| Célközönség | A fogadó erőforrást azonosítja, amelynek a jogkivonat szól. |
| Alkalmazáshitelesítési környezet osztályhivatkozása | Az ügyfél hitelesítési módját adja meg (nyilvános vagy bizalmas ügyfél). |
| Hitelesítési időpont | A hitelesítés megtörténtének dátumát és időpontját rögzíti. |
| Hitelesítési módszer | A jogkivonatalany hitelesítésének módját adja meg (jelszó, tanúsítvány stb.). |
| Utónév | A felhasználó Azure AD-ben beállított utónevét adja meg. |
| Csoportok | Az olyan Azure AD-csoportok objektumazonosítóit adja meg, amelyeknek az adott felhasználó tagja. |
| Identitásszolgáltató | A jogkivonat alanyát hitelesítő identitásszolgáltatót adja meg. |
| Kibocsátás időpontja | A jogkivonat kibocsátásának időpontját rögzíti, amely alapján általában a jogkivonat frissességét szokás elbírálni. |
| Kiállító | A jogkivonatot kiadó biztonsági jegykiadó szolgáltatást, valamint az Azure AD-bérlőt azonosítja. |
| Vezetéknév | A felhasználó Azure AD-ben beállított vezetéknevét adja meg. |
| Name | A jogkivonat alanyát azonosító, ember által olvasható értéket ad meg. |
| Objektumazonosító | Az alany Azure AD-beli megváltoztathatatlan egyedi azonosítóját adja meg. |
| Szerepkörök | A felhasználónak kiosztott Azure AD-beli alkalmazás-szerepkörök rövid neveit adja meg. |
| Hatókör | Az ügyfélalkalmazás számára megadott engedélyeket jelzi. |
| Tárgy | Azt a szolgáltatásnevet adja meg, amelynek az adatait a jogkivonat hitelesíti. |
| Bérlőazonosító | A jogkivonatot kibocsátó címtárbérlő megváltoztathatatlan egyedi azonosítóját adja meg. |
| Jogkivonat élettartama | A jogkivonat érvényességi idejét határozza meg. |
| Felhasználó egyszerű neve | Az alany egyszerű felhasználónevét adja meg. |
| Verzió | A jogkivonat verziószámát adja meg. |

## <a name="next-steps"></a>Következő lépések

* Tudnivalók a [Microsoft Identity platform által támogatott alkalmazások típusairól és forgatókönyvekről](app-types.md)
