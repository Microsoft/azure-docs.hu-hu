---
title: Az intelligens nyomkövetési védelem (ITP) kezelése a Safariban | Azure
titleSuffix: Microsoft identity platform
description: Egyoldalas alkalmazás (SPA) hitelesítés, ha a harmadik féltől származó cookie-k már nem engedélyezettek.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753967"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Az ITP kezelése a Safariban és más böngészőkben, ahol a harmadik féltől származó cookie-k blokkolva vannak

Manapság sok böngésző blokkolja a harmadik féltől származó cookie-kat – a cookie-k olyan tartományokra irányuló kérések, amelyek nem egyeznek meg a böngésző sávjában láthatóval. Ez megszakítja az implicit folyamatot, és új hitelesítési mintákat igényel a felhasználók sikeres bejelentkezéséhez. A Microsoft Identity platformon az engedélyezési folyamat a PKCE és a tokenek frissítése segítségével biztosítható, hogy a felhasználók a harmadik féltől származó cookie-k blokkolásakor bejelentkezve maradjanak.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Mi az intelligens nyomkövetési védelem (ITP)?

Az Apple Safari alapértelmezett adatvédelmi védelmi funkciója az [intelligens nyomkövetés védelme](https://webkit.org/tracking-prevention-policy/)vagy a *itp*. ITP blokkolja a "harmadik féltől származó" cookie-kat, a tartományok közötti kérések cookie-jait.

A felhasználók nyomon követésének gyakori formája, ha az IFRAME-t egy külső webhelyre tölti be a háttérben, és cookie-kat használ a felhasználó interneten keresztüli összekapcsolásához. Ez a minta sajnos az egyoldalas alkalmazásokban (gyógyfürdőben) az [implicit folyamat](v2-oauth2-implicit-grant-flow.md) megvalósításának szabványos módja is. Ha egy böngésző blokkolja a harmadik féltől származó cookie-kat a felhasználói követés megakadályozása érdekében, a fürdők is megszakadnak.

A Safari nem egyedül blokkolja a harmadik féltől származó cookie-kat a felhasználók adatvédelme érdekében. A bátor alapértelmezés szerint letiltotta a harmadik féltől származó cookie-kat, és a Chromium (a Google Chrome és a Microsoft Edge mögötti platform) bejelentette, hogy a továbbiakban is leállítja a harmadik féltől származó cookie-k támogatását.

A cikkben ismertetett megoldás az összes böngészőben működik, vagy a harmadik féltől származó cookie-k blokkolva vannak.

## <a name="overview-of-the-solution"></a>A megoldás áttekintése

A gyógyfürdőben lévő felhasználók hitelesítésének folytatásához az alkalmazás-fejlesztőknek az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md)kell használniuk. Az Auth Code folyamatában az identitás-szolgáltató kiad egy kódot, és a SPA beváltja a kódot egy hozzáférési jogkivonat és egy frissítési jogkivonat számára. Ha az alkalmazás további jogkivonatokat igényel, akkor a [token frissítése folyamat](v2-oauth2-auth-code-flow.md#refresh-the-access-token) használatával új jogkivonatokat kaphat. MSAL.js 2,0, a Microsoft Identity platform Library for SPAs, implementálja a gyógyfürdők engedélyezési kódjának folyamatát, és kisebb frissítésekkel helyettesíti a MSAL.js 1. x.

A Microsoft Identity platformhoz a fürdők és a natív ügyfelek a hasonló protokoll-útmutatást követik:

* [PKCE-kód használatának kihívása](https://tools.ietf.org/html/rfc7636)
    * A PKCE a Microsoft Identity platform gyógyfürdői esetében *szükséges* . A PKCE natív és bizalmas ügyfelek esetén *ajánlott* .
* Nem használható az ügyfél titkos kulcsa

A SPAs két további korlátozást tartalmaz:

* [Az átirányítási URI-t típusként `spa` kell megjelölni](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) . a CORS engedélyezése a bejelentkezési végpontokon.
* Az engedélyezési kód által az `spa` URI-k átirányításához kiadott tokenek frissítése 24 órás élettartammal, nem pedig 90 napos élettartammal történik.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="A OAuth 2 engedélyezési kód egy egyoldalas alkalmazás és a biztonsági jogkivonat szolgáltatás végpontja közötti folyamatát ábrázoló diagram." border="false":::

## <a name="performance-and-ux-implications"></a>Teljesítmény-és UX-következmények

Egyes alkalmazások, amelyek az implicit flow kísérlet nélküli bejelentkezést végeznek, nem kell átirányítani a bejelentkezési iframe-t a használatával `prompt=none` . A legtöbb böngészőben ez a kérelem az aktuálisan bejelentkezett felhasználó jogkivonatait fogja válaszolni (feltéve, hogy a hozzájárulás már meg van adva). Ez a minta azt jelentette, hogy az alkalmazásoknak nem volt szükségük a teljes oldal átirányítására a felhasználó aláírásához, a teljesítmény és a felhasználói élmény fokozása érdekében – a felhasználó meglátogatja a weblapot, és már bejelentkezett. Mivel az `prompt=none` iframe-ben már nem érhető el a harmadik féltől származó cookie-k letiltása, az alkalmazásoknak meg kell keresniük a bejelentkezési oldalt egy legfelső szintű keretben, hogy a rendszer kiállítson egy engedélyezési kódot.

A bejelentkezés két módon hajtható végre:

* **Teljes oldal átirányítása**
    * A SPA első betöltéséhez irányítsa át a felhasználót a bejelentkezési oldalra, ha már nincs munkamenet (vagy ha a munkamenet lejárt). A felhasználó böngészőjében megjelenik a bejelentkezési oldal, amely a felhasználói munkamenetet tartalmazó cookie-kat tartalmazza, majd visszairányítja az alkalmazást egy töredékben lévő kóddal és tokenekkel.
    * Az átirányítás azt eredményezi, hogy a fürdő kétszer töltődik be. Kövesse az ajánlott eljárásokat a gyógyfürdők gyorsítótárazásához, hogy az alkalmazás ne legyen teljes, kétszer is letöltve.
    * Érdemes lehet olyan előre betöltési sorozatot kötni az alkalmazásban, amely ellenőrzi a bejelentkezési munkamenetet, és átirányítja a bejelentkezési oldalra, mielőtt az alkalmazás teljes mértékben kicsomagolja és végrehajtja a JavaScript-adattartalmat.
* **Előugró ablakok**
    * Ha a teljes oldal átirányításának felhasználói élménye (UX) nem működik az alkalmazás esetében, érdemes lehet felugró ablakokat használni a hitelesítés kezelésére.
    * Ha az előugró ablak hitelesítés után átirányítja az alkalmazást az alkalmazásba, a kód az átirányítási kezelőben a helyi tárolóban tárolja a kódot és a jogkivonatokat, amelyeket az alkalmazás használni fog. A MSAL.js támogatja az előugró ablakokat a hitelesítéshez, akárcsak a legtöbb kódtárat.
    * A böngészők csökkentik a felugró ablakok támogatását, így azok nem a legmegbízhatóbb megoldás. Az előugró ablak létrehozása előtt a felhasználói interakcióra lehet szükség a böngésző követelményeinek kielégítése érdekében.

>[!NOTE]
> Az Apple az [előugró metódust](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) ideiglenes kompatibilitási megoldásként írja le, hogy az eredeti ablak hozzáférjen harmadik féltől származó cookie-khoz. Amíg az Apple a jövőben is eltávolíthatja ezt az engedélyeket, az itt ismertetett útmutatást nem érinti. Ebben az esetben a rendszer az előugró ablakokat használja a bejelentkezési oldalon, így megtalálhatja a munkamenetet, és egy hitelesítési kódot is megadhat. Ennek a jövőben is működnie kell.

### <a name="a-note-on-iframe-apps"></a>Az IFRAME-alkalmazásokra vonatkozó Megjegyzés

A webalkalmazások közös mintája, hogy iframe-t használjon egy alkalmazás beágyazásához egy másikon belül. A legfelső szintű keret kezeli a felhasználó hitelesítését, és az IFRAME-ben üzemeltetett alkalmazás megbízik abban, hogy a felhasználó bejelentkezett, és a jogkivonatokat csendesen beolvassa az implicit folyamat használatával. A csendes jogkivonat beszerzése már nem működik, ha a harmadik féltől származó cookie-k le vannak tiltva – az IFRAME-ben beágyazott alkalmazásnak a felugró ablak használatával kell váltania a felhasználói munkamenet eléréséhez, mivel nem tud a bejelentkezési oldalra lépni.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>A böngészőben a frissítési tokenek biztonsági következményei

A frissítési jogkivonatok böngészőbe való kiállítása biztonsági problémanak minősül. A helyek közötti parancsfájlkezelés (XSS) vagy a feltört JS-csomagok ellopják a frissítési jogkivonatot, és távolról, a lejárat vagy a visszavonás után használhatják azt. Az ellopott frissítési tokenek kockázatának csökkentése érdekében a gyógyfürdők csak 24 órán át érvényes jogkivonatokat kapnak. 24 óra elteltével az alkalmazásnak meg kell adnia egy új engedélyezési kódot a bejelentkezési oldal legfelső szintű keretén keresztül.

Ez a korlátozott élettartamú frissítési jogkivonat-minta a biztonság és a csökkentett teljesítményű UX rendszer közötti egyensúlyként lett kiválasztva. A frissítési tokenek vagy a harmadik féltől származó cookie-k nélkül az engedélyezési kód folyamata (ahogy azt a [OAuth Security aktuális gyakorlatainak tervezete](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)javasolja) megterheli, ha új vagy további tokenekre van szükség. Minden egyes tokenhez teljes oldal-átirányítás vagy előugró elem szükséges, minden alkalommal, amikor egy jogkivonat lejár (óránként általában a Microsoft Identity platform tokenek esetében).

## <a name="next-steps"></a>Következő lépések

További információ az [engedélyezési kód folyamatáról](v2-oauth2-auth-code-flow.md).

Próbálja ki az engedélyezési kód folyamatát a [MSAL.js 2,0](quickstart-v2-javascript-auth-code.md)gyors útmutatóval.
