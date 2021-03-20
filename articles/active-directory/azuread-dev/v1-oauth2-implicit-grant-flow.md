---
title: A OAuth2 implicit engedélyezési folyamatának megértése az Azure AD-ben | Microsoft Docs
description: További információ az OAuth2 implicit engedélyezési folyamatának Azure Active Directory megvalósításáról, valamint attól, hogy az alkalmazása megfelelő-e.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154508"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>A OAuth2 implicit engedélyezési folyamatának megértése Azure Active Directoryban (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A OAuth2 implicit megadása a leghírhedtebb, hogy a OAuth2-specifikációban a leghosszabb számú biztonsági szempontot adja meg. És még ez a ADAL JS által megvalósított megközelítés, amelyet a SPA-alkalmazások írásakor ajánlunk. Mit ad? A kompromisszumok lényege: az implicit támogatás az a legjobb módszer, amellyel a webes API-kat használó alkalmazások böngészőből JavaScripten keresztül is elvégezhető.

## <a name="what-is-the-oauth2-implicit-grant"></a>Mi a OAuth2 implicit megadása?

A tömör [OAuth2 engedélyezési kód megadása](https://tools.ietf.org/html/rfc6749#section-1.3.1) az engedélyezési engedély, amely két különálló végpontot használ. A rendszer az engedélyezési végpontot használja a felhasználói interakciós fázishoz, amely egy engedélyezési kódot eredményez. Az ügyfél ezt követően a jogkivonat-végpontot használja a hozzáférési jogkivonat kódjának cseréjére, és gyakran frissítési jogkivonatra is. A webalkalmazásoknak saját alkalmazásbeli hitelesítő adataikat kell megadniuk a jogkivonat-végponthoz, hogy az engedélyezési kiszolgáló hitelesíteni tudja az ügyfelet.

A [OAuth2 implicit támogatás](https://tools.ietf.org/html/rfc6749#section-1.3.2) más engedélyezési támogatások egy változata. Lehetővé teszi, hogy az ügyfél hozzáférési jogkivonatot szerezzen be (és id_token az [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)használatakor) közvetlenül az engedélyezési végpontról, a jogkivonat-végponttal való kapcsolatfelvétel és az ügyfél hitelesítése nélkül. Ez a változat a webböngészőben futó JavaScript-alapú alkalmazásokhoz lett tervezve: az eredeti OAuth2-specifikációban a tokenek egy URI-töredékben lesznek visszaadva. Így a jogkivonat-bitek elérhetővé válnak az ügyfél JavaScript-kódjában, de garantálják, hogy a rendszer a kiszolgáló felé irányuló átirányítások során nem fog szerepelni. Az OAuth2 implicit megadása esetén az engedélyezési végpont a korábban megadott átirányítási URI használatával kiadja a hozzáférési jogkivonatokat közvetlenül az ügyfélnek. Emellett megvan az előnye, hogy kiküszöböli a kereszthivatkozási hívások követelményeit, amelyek akkor szükségesek, ha a JavaScript-alkalmazásnak a jogkivonat-végponthoz kell kapcsolódnia.

Az OAuth2 implicit támogatás fontos jellemzője, hogy az ilyen folyamatok soha nem adnak vissza frissítési jogkivonatokat az ügyfélnek. A következő szakasz azt mutatja be, hogy ez miért nem szükséges, és valójában biztonsági probléma lenne.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>A OAuth2 implicit engedélyezésének megfelelő forgatókönyvei

A OAuth2-specifikáció kijelenti, hogy az implicit támogatás úgy lett kialakítva, hogy lehetővé tegye a felhasználói ügynök alkalmazásai számára – azaz a böngészőben futtatott JavaScript-alkalmazásokat. Az ilyen alkalmazások meghatározó jellemzője, hogy a JavaScript-kód a kiszolgálói erőforrások (általában a webes API-k) elérésére és ennek megfelelően az alkalmazás felhasználói élményének frissítésére szolgál. Gondoljon olyan alkalmazásokra, mint például a Gmail vagy az Outlook Web Access: Amikor kiválaszt egy üzenetet a Beérkezett üzenetek mappából, csak az üzenet vizualizáció paneljén változik az új kijelölés megjelenítése, míg a lap többi része változatlan marad. Ez a jellemző ellentétben áll a hagyományos átirányítási webalkalmazásokkal, ahol minden felhasználói interakció a teljes oldal visszaküldését és az új kiszolgáló válaszának teljes oldalát jeleníti meg.

Azok az alkalmazások, amelyek a JavaScript-alapú megközelítést használják szélsőségesként, egyoldalas alkalmazásoknak vagy Gyógyfürdőknek nevezzük. Az elképzelés az, hogy ezek az alkalmazások csak a kezdeti HTML-oldalt és a kapcsolódó JavaScriptet szolgálják ki, és a JavaScripten keresztül végrehajtott webes API-hívások által vezérelt minden további interakciót. Azonban a hibrid megközelítések, amelyekben az alkalmazás többnyire visszaküldéssel vezérelt, de alkalmanként JS-hívásokat hajt végre, nem ritka – az implicit flow használatáról szóló vitát is fontos.

Az átirányítási alkalmazások általában cookie-kon keresztül védik a kéréseiket, azonban ez a megközelítés nem működik jól a JavaScript-alkalmazásokhoz. A cookie-k csak a által létrehozott tartományon működnek, míg a JavaScript-hívások más tartományokra is irányíthatók. Valójában ez gyakran előfordul: a Microsoft Graph API-t, az Office API-t, az Azure API-t, a tartományon kívüli, az alkalmazás kiszolgálása helyétől eltérő alkalmazásokat. A JavaScript-alkalmazások egyre növekvő tendenciát mutatnak, ha nincs háttérrendszer, a harmadik féltől származó webes API-kra támaszkodva 100%-ot az üzleti funkció megvalósítására.

Jelenleg a webes API-hívások védelmének előnyben részesített módszere a OAuth2 tulajdonosi jogkivonat megközelítésének használata, amelyben minden hívást egy OAuth2 hozzáférési token kísér. A webes API megvizsgálja a bejövő hozzáférési jogkivonatot, és ha megtalálja a szükséges hatóköröket, akkor hozzáférést biztosít a kért művelethez. Az implicit folyamat kényelmes mechanizmust biztosít a JavaScript-alkalmazásokhoz a webes API-k hozzáférési jogkivonatának beszerzéséhez, ami számos előnyt kínál a cookie-kkal kapcsolatban:

* A tokeneket megbízhatóan lehet megszerezni a több eredetű hívás szükségessége nélkül – az átirányítási URI kötelező regisztrációja, amelynél a tokenek visszaállnak, a tokenek nem helyezhetők el.
* A JavaScript-alkalmazások a szükségesnél több hozzáférési jogkivonatot szerezhetnek be, az adott számú webes API-ra vonatkozóan – a tartományok korlátozása nélkül
* A HTML5-funkciók, például a munkamenet vagy a helyi tárterület teljes hozzáférést biztosítanak a jogkivonat-gyorsítótárazáshoz és az élettartam-kezeléshez, míg a cookie-k kezelése az alkalmazásba
* A hozzáférési jogkivonatok nem érzékenyek a helyek közötti kérelmek hamisítására (CSRF) irányuló támadásokra

Az implicit engedélyezési folyamat nem ad ki frissítési jogkivonatokat, főleg biztonsági okokból. A frissítési token nem a hozzáférési jogkivonatoknak megfelelően van leszűkítve, így sokkal nagyobb hatékonyságot biztosít, így sokkal nagyobb károkat okoz, ha kiszivárgott. Az implicit folyamat során a tokenek az URL-címben lesznek továbbítva, így az elfogás kockázata magasabb, mint az engedélyezési kód megadásakor.

Egy JavaScript-alkalmazás azonban egy másik mechanizmussal rendelkezik a hozzáférési tokenek megújításához, anélkül, hogy ismételten megkéri a felhasználót a hitelesítő adatok megadására. Az alkalmazás a rejtett iframe használatával új jogkivonat-kérelmeket hajthat végre az Azure AD engedélyezési végpontján: Ha a böngésző továbbra is aktív munkamenettel rendelkezik (olvasás: munkamenet-cookie-val rendelkezik) az Azure AD-tartományhoz, a hitelesítési kérelem a felhasználói beavatkozás szükségessége nélkül is sikeresen megtörténhet.

Ez a modell lehetővé teszi, hogy a JavaScript-alkalmazás önállóan megújítsa a hozzáférési jogkivonatokat, és új API-k számára is újat szerezzen (ha a felhasználó korábban beleegyezett hozzájuk). Ezzel elkerülheti a nagy értékű összetevők, például a frissítési token beszerzésének, fenntartásának és védelmének további terheit. A csendes megújulást lehetővé teszi, hogy az Azure AD munkamenet-cookie-t az alkalmazáson kívül felügyeli a rendszer. Ennek a módszernek egy másik előnye, hogy egy felhasználó kijelentkezhet az Azure AD-ből az Azure AD-be bejelentkezett bármely alkalmazás használatával, amely a böngésző lapjain fut. Ez az Azure AD munkamenet-cookie törlését eredményezi, és a JavaScript alkalmazás automatikusan elveszíti a kijelentkezett felhasználó jogkivonatának megújítását.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Az implicit engedélyezési funkció megfelelő az alkalmazáshoz?

Az implicit támogatás nagyobb kockázatot jelent, mint a többi támogatás, és a figyelmet igénylő területeknek megfelelően dokumentálva kell lennie (például a [hozzáférési jogkivonattal való visszaéléssel, hogy megszemélyesítse az erőforrás-tulajdonost az implicit flow-ban][OAuth2-Spec-Implicit-Misuse] , és [OAuth a 2,0-es veszélyforrás-modellt és biztonsági szempontokat][OAuth2-Threat-Model-And-Security-Implications]). A magasabb kockázati profilt azonban nagyrészt az a tény okozza, hogy az aktív kódot végrehajtó alkalmazásokat egy távoli erőforrás szolgáltatja egy böngészőnek. Ha gyógyfürdő-architektúrát tervez, nem rendelkezik háttér-összetevőkkel, vagy webes API-t szeretne meghívni JavaScripten keresztül, a jogkivonat-beszerzéshez használt implicit folyamat használata javasolt.

Ha az alkalmazás egy natív ügyfél, az implicit folyamat nem remekül illeszkedik. Az Azure AD-munkamenet cookie-nak a natív ügyfél kontextusában való hiánya megfosztja az alkalmazást a hosszú élettartamú munkamenet fenntartásának eszközeitől. Ez azt jelenti, hogy az alkalmazás többször is kéri a felhasználót, amikor hozzáférési jogkivonatokat kap az új erőforrásokhoz.

Ha olyan webalkalmazást fejleszt, amely hátteret tartalmaz, és egy API-t használ a háttér-kódjából, az implicit folyamat szintén nem megfelelő. A többi támogatás sokkal nagyobb teljesítményt nyújt. A OAuth2 ügyfél-hitelesítő adatok megadása például lehetővé teszi az alkalmazáshoz hozzárendelt engedélyeket tükröző jogkivonatok beszerzését, a felhasználói delegálásokkal szemben. Ez azt jelenti, hogy az ügyfélnek lehetősége van az erőforrásokhoz való programozott hozzáférés fenntartására, még akkor is, ha a felhasználó nem vesz aktívan részt egy munkamenetben, és így tovább. Nem csak ez, de az ilyen támogatások nagyobb biztonsági garanciákat biztosítanak. Például a hozzáférési tokenek soha nem haladnak át a felhasználói böngészőn keresztül, ezért a böngésző előzményeiben nem kerülnek mentésre, így tovább. Az ügyfélalkalmazás emellett erős hitelesítést is végezhet a jogkivonat kérésekor.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, [Hogyan integrálhat egy alkalmazást az Azure ad-vel][ACOM-How-To-Integrate] az alkalmazás-integrációs folyamat további mélysége érdekében.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
