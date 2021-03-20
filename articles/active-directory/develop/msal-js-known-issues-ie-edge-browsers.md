---
title: Problémák az Internet Explorerben & a Microsoft Edge-ben (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az Internet Explorer és a Microsoft Edge böngészővel rendelkező JavaScript (MSAL.js) Microsoft-hitelesítési könyvtárának használatakor felmerülő problémákat.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83772080"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Az Internet Explorer és a Microsoft Edge böngészők ismert problémái (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Biztonsági zónák miatti problémák
Az IE-ben és a Microsoft Edge-ben történt hitelesítéssel kapcsolatos hibákról több jelentést is készítettünk (mivel a *Microsoft Edge böngésző verziójának* frissítése a 40.15063.0.0-re). Ezeket követjük nyomon, és tájékoztatták a Microsoft Edge-csapatot. Míg a Microsoft Edge egy megoldáson dolgozik, itt látható a gyakran előforduló problémák és a megvalósítható lehetséges megkerülő megoldások leírása.

### <a name="cause"></a>Ok
A legtöbb probléma oka a következő: A munkamenet-tároló és a helyi tároló a Microsoft Edge böngésző biztonsági zónái között van particionálva. A Microsoft Edge ezen verziójában, amikor az alkalmazást átirányítják a zónák között, a munkamenet-tároló és a helyi tároló törlődik. A munkamenet-tárolót a böngésző normál navigációs sávjában törli, és a munkamenet és a helyi tárterület is törlődik a böngésző InPrivate módjában. MSAL.js bizonyos állapotot ment a munkamenet-tárolóban, és ennek az állapotnak a ellenőrzésére támaszkodik a hitelesítési folyamatok során. Ha a munkamenet-tároló törlődik, ez az állapot elvész, ezért a rendszer hibás élményt eredményez.

### <a name="issues"></a>Problémák

- **A hitelesítés során a végtelen átirányítási hurkok és a lap újratöltődik**. Amikor a felhasználók bejelentkeznek az alkalmazásba a Microsoft Edge-ben, a rendszer visszairányítja őket a HRE bejelentkezési oldaláról, és egy végtelen átirányítási hurokba ragadnak, ami ismétlődő oldal újratöltését eredményezi. Ezt általában `invalid_state` a munkamenet-tárolóban lévő hiba kíséri.

- **Végtelen beszerzési jogkivonat-hurkok és AADSTS50058-hiba**. Ha egy Microsoft Edge-ben futó alkalmazás egy erőforráshoz tartozó jogkivonatot próbál beszerezni, az alkalmazás a beszerzési jogkivonat-hívás végtelen ciklusában is elakadhat, és a következő hibaüzenetet kaphatja a hálózati nyomkövetés HRE:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- Az **előugró ablak nem zárul be, vagy a bejelentkező használatával történő bejelentkezéskor a hitelesítéshez beragadt**. Ha a Microsoft Edge vagy az IE (InPrivate) előugró ablakán keresztül végez hitelesítést, a hitelesítő adatok beírása és a bejelentkezés után, ha több tartomány található a biztonsági zónákban, az előugró ablak nem zárul le, mert MSAL.js elveszíti a leírót az előugró ablakba.  

### <a name="update-fix-available-in-msaljs-023"></a>Frissítés: elérhető javítás a MSAL.js 0.2.3
A hitelesítés átirányítási ciklusával kapcsolatos hibák javításait [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)tették közzé. A `storeAuthStateInCookie` javítás kihasználása érdekében engedélyezze a MSAL.js konfigurációjában a jelölőt. Alapértelmezés szerint ez a jelző hamis értékre van állítva.

Ha a `storeAuthStateInCookie` jelző engedélyezve van, MSAL.js a böngésző cookie-jait fogja használni a hitelesítési folyamatok érvényesítéséhez szükséges kérelem állapotának tárolásához.

> [!NOTE]
> Ez a javítás még nem érhető el a msal és msal-angularjs burkolók esetében. Ez a javítás nem foglalkozik a felugró ablakokkal kapcsolatos problémával.

Használja az alábbi áthidaló megoldásokat.

#### <a name="other-workarounds"></a>Egyéb megkerülő megoldások
Győződjön meg arról, hogy a probléma csak a Microsoft Edge böngésző adott verziójánál fordul elő, és a megkerülő megoldások elfogadása előtt működik a többi böngészőben.  
1. A problémák megkerülésének első lépéseként győződjön meg arról, hogy az alkalmazás tartománya és a hitelesítési folyamat átirányításában részt vevő többi hely megbízható helyként lesz hozzáadva a böngésző biztonsági beállításaiban, hogy azok ugyanahhoz a biztonsági zónához tartozzanak.
Ehhez kövesse az alábbi lépéseket:
    - Nyissa meg az **Internet Explorert** , és kattintson a **Beállítások** (fogaskerék ikon) elemre a jobb felső sarokban
    - **Internetes beállítások** kiválasztása
    - Válassza a **Biztonság** fület.
    - A **megbízható helyek** lehetőségnél kattintson a **helyek** gombra, és adja hozzá az URL-címeket a megnyíló párbeszédpanelen.

2. Ahogy korábban említettük, mivel a normál Navigálás során csak a munkamenet-tárolót törli a rendszer, a MSAL.jst a helyi tárterület használatára is beállíthatja. Ez beállítható a `cacheLocation` MSAL inicializálásakor a konfigurációs paraméterként.

Vegye figyelembe, hogy ez nem oldja meg az InPrivate-böngészés hibáját, mivel a munkamenet és a helyi tárterület is törlődik.

## <a name="issues-due-to-popup-blockers"></a>Felugró ablakos blokkolók miatti problémák

Előfordulhat, hogy az előugró ablakok le vannak tiltva az IE-ben vagy a Microsoft Edge-ben, például ha egy második előugró ablak jelenik meg a [multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)során. A böngészőben megjelenik egy riasztás, amely lehetővé teszi, hogy az előugró ablak egyszer vagy bármikor elérhető legyen. Ha úgy dönt, hogy engedélyezi, a böngésző automatikusan megnyitja az előugró ablakot, és visszaadja a `null` hozzá tartozó leírót. Ennek eredményeképpen a függvénytár nem rendelkezik leíróval az ablakhoz, és nincs lehetőség az előugró ablak bezárására. Ugyanez a probléma nem fordul elő a Chrome-ban, amikor rákérdez, hogy engedélyezi-e a felugró ablakokat, mert nem nyit meg automatikusan előugró ablakot.

**Megkerülő megoldásként** a fejlesztőknek engedélyeznie kell a felugró ablakokat az IE-ben és a Microsoft Edge-ben, mielőtt elkezdik használni az alkalmazást a probléma elkerüléséhez.

## <a name="next-steps"></a>Következő lépések
További információ az [MSAL.js használatáról az Internet Explorerben](msal-js-use-ie-browser.md).
