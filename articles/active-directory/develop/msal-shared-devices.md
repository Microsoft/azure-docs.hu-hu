---
title: Megosztott eszközök üzemmódjának áttekintése
titleSuffix: Microsoft identity platform | Azure
description: Ismerje meg a megosztott eszköz üzemmódot, amely lehetővé teszi az eszközök megosztását az élvonalbeli feldolgozók számára.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf8869002fb3e0170331709af3da5b971a098740
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612398"
---
# <a name="overview-of-shared-device-mode"></a>Megosztott eszköz üzemmódjának áttekintése

A megosztott eszköz mód a Azure Active Directory szolgáltatása, amely lehetővé teszi olyan alkalmazások létrehozását, amelyek támogatják az élvonalbeli munkavégzőket, és lehetővé teszik a megosztott eszköz üzemmód használatát a rájuk telepített eszközökön.

>[!IMPORTANT]
> Megosztott eszköz mód iOS rendszerhez [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>Mik azok az élvonalbeli feldolgozók?

Az élvonalbeli dolgozók a lakossági alkalmazottak, a karbantartási és a helyszíni ügynökök, az orvosi személyzet és más olyan felhasználók, akik nem a számítógép előtt ülnek, vagy a vállalati e-maileket használják az együttműködésre. A következő részekben bemutatjuk az élvonalbeli dolgozók támogatásával kapcsolatos szempontokat és kihívásokat, valamint a Microsoft által biztosított funkciók bevezetését, amelyek lehetővé teszik az alkalmazás számára, hogy egy szervezet Frontline-feldolgozói használhassanak.

### <a name="challenges-of-supporting-frontline-workers"></a>Az élvonalbeli dolgozók támogatásával kapcsolatos kihívások

Az élvonalbeli munkafolyamatok engedélyezése olyan kihívásokat is tartalmaz, amelyeket általában nem a tipikus információkkal dolgozó szakemberek ismertetnek. Ilyen kihívások lehetnek a nagy forgalmú és kevésbé jártas vállalatok alapvető hatékonyságnövelő eszközei. Az élvonalbeli feldolgozók felhatalmazása érdekében a szervezetek különböző stratégiákat fogadnak el. Egyesek olyan saját eszköz (BYOD) stratégiát fogadnak, amelyben alkalmazottaik üzleti alkalmazásokat használnak a személyes telefonján, míg mások a közös eszközökkel, például Ipadekkel vagy Android-tablettákkal biztosítják alkalmazottaikat.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Több felhasználó támogatása egy felhasználó számára tervezett eszközökön

Mivel az iOS vagy az Android rendszerű mobileszközök egyetlen felhasználóhoz lettek tervezve, a legtöbb alkalmazás optimalizálja a saját felhasználói élményét. Az optimalizált élmény része az alkalmazások egyszeri bejelentkezésének és az eszközön bejelentkezett felhasználók megőrzésének engedélyezése. Amikor egy felhasználó eltávolítja a fiókját egy alkalmazásból, az alkalmazás általában nem veszi figyelembe a biztonsággal kapcsolatos eseményt. Számos alkalmazás a gyors bejelentkezéshez is megtartja a felhasználó hitelesítő adatait. Ezt akkor is megtapasztalta, ha törölt egy alkalmazást a mobileszközön, majd újratelepítette, csak a felderítéshez, amely még be van jelentkezve.

### <a name="global-sign-in-and-sign-out-sso"></a>Globális bejelentkezés és kijelentkezés (SSO)

Annak engedélyezéséhez, hogy a szervezet alkalmazottai az alkalmazásokat az alkalmazottak által megosztott eszközökön használják, a fejlesztőknek engedélyeznie kell az ellenkező élményt. Az alkalmazottaknak képesnek kell lenniük egy eszköz kiválasztására a készletből, és egyetlen kézmozdulatot kell elvégezniük a váltás időtartamára. A váltás végén egy másik kézmozdulatot is el kell végeznie a globális kijelentkezéshez az eszközön, és az összes személyes és céges adata el lett távolítva, hogy vissza lehessen őket adni az eszköz készletének. Továbbá, ha egy alkalmazott elfelejti a kijelentkezést, az eszközt a váltás végén és/vagy inaktivitás után automatikusan ki kell jelentkeznie.

Azure Active Directory engedélyezi ezeket a forgatókönyveket egy **megosztott eszköz üzemmód** nevű szolgáltatással.

## <a name="introducing-shared-device-mode"></a>Megosztott eszköz üzemmód bemutatása

Ahogy említettük, a megosztott eszköz mód az Azure Active Directory szolgáltatása, amely a következőket teszi lehetővé:

* Az élvonalbeli dolgozókat támogató alkalmazások készítése
* Eszközök üzembe helyezése a feldolgozók számára és a megosztott eszköz üzemmódjának bekapcsolása

### <a name="build-applications-that-support-frontline-workers"></a>Az élvonalbeli dolgozókat támogató alkalmazások készítése

Az alkalmazásokban a Microsoft Authentication Library (MSAL) és a [Microsoft Authenticator alkalmazás](../user-help/user-help-auth-app-overview.md) használatával is támogathatja az alkalmazások Frontline-feldolgozóit, ha a *megosztott eszköz üzemmódot* szeretné engedélyezni. Ha egy eszköz megosztott eszköz módban van, a Microsoft információt nyújt az alkalmazásnak, amely lehetővé teszi, hogy az eszköz felhasználójának állapota alapján módosítsa a viselkedését, és védje a felhasználói adatokat.

A támogatott szolgáltatások a következők:

* **A felhasználók eszközeit teljes egészében bejelentkezhet** bármely támogatott alkalmazáson keresztül.
* Teljes **körű felhasználói eszköz kijelentkezése** bármely támogatott alkalmazáson keresztül.
* Az **eszköz állapotának lekérdezése** annak megállapításához, hogy az alkalmazás olyan eszközön van-e, amely megosztott eszköz módban van.
* Az eszközön lévő **felhasználó állapotának lekérdezése** annak megállapításához, hogy minden változás történt-e az alkalmazás legutóbbi használata óta.

A megosztott eszközök támogatását az alkalmazás biztonsági fejlesztésének és funkciójának frissítéseként kell figyelembe venni, és így növelheti annak bevezetését olyan, magasan szabályozott környezetekben, mint az egészségügy és a pénzügy.

A felhasználók attól függnek, hogy az adatok nem szivárognak-e egy másik felhasználó felé. A megosztási eszköz mód hasznos jeleket biztosít az alkalmazásnak, amelyet a felügyelni kívánt módosítás okoz. Az alkalmazás felelős azért, hogy ellenőrizze az eszköz felhasználójának állapotát az alkalmazás minden egyes használatakor, és törölje az előző felhasználó adatait. Ez magában foglalja a többszörös feladatokból származó háttérből való újratöltést. A felhasználó változásakor győződjön meg arról, hogy az előző felhasználó adatai törlődnek, és az alkalmazásban megjelenő gyorsítótárazott adatfájlok törlődnek. Javasoljuk, hogy mindig végezzen alapos biztonsági felülvizsgálati folyamatot, miután hozzáadta a megosztott eszköz mód funkcióját az alkalmazáshoz.

Az alkalmazások megosztott eszköz módra való módosításával kapcsolatos további információkért tekintse meg a cikk végén található [következő lépések](#next-steps) szakaszt.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>Eszközök üzembe helyezése a feldolgozók számára és a megosztott eszköz üzemmódjának bekapcsolása

Miután az alkalmazásai támogatják a megosztott eszköz üzemmódot, és tartalmazzák a szükséges adatkezelési és biztonsági módosításokat, meghirdetheti őket az Frontline-feldolgozók által használhatóként.

A szervezet eszközeinek rendszergazdái a mobileszköz-kezelési (MDM) megoldáson (például Microsoft Intune) keresztül telepíthetik eszközeiket és alkalmazásaikat az áruházba és a munkahelyekre. A kiépítési folyamat részeként az eszköz megjelölése *megosztott eszközként* történik. A rendszergazdák úgy konfigurálhatják a megosztott eszköz üzemmódot, hogy a konfigurációs paraméterek használatával telepítik a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md) és a megosztott eszköz üzemmódot. A lépések elvégzése után a megosztott eszköz üzemmódot támogató összes alkalmazás a Microsoft Authenticator alkalmazást fogja használni a felhasználói állapotának kezeléséhez, valamint az eszköz és a szervezet biztonsági funkcióinak biztosításához.

## <a name="next-steps"></a>Következő lépések

Az iOS-és Android-platformokat a megosztott eszközök üzemmódjában támogatjuk. Az alábbi dokumentációban megtekintheti a platformját, amellyel megkezdheti az alkalmazásokban az élvonalbeli dolgozók támogatását.

* [Az iOS rendszerhez készült megosztott eszköz mód támogatása](msal-ios-shared-devices.md)
* [Az Android rendszerhez készült megosztott eszköz mód támogatása](msal-android-shared-devices.md)
