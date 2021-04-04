---
title: Alkalmazás megjelölése közzétevőként ellenőrzöttként – Microsoft Identity platform | Azure
description: Leírja, hogyan kell megjelölni egy alkalmazást közzétevőként. Ha az alkalmazás közzétevőként van megjelölve, az azt jelenti, hogy a közzétevő ellenőrizte az identitását egy olyan Microsoft Partner Network-fiókkal, amely befejezte az ellenőrzési folyamatot, és társította ezt az MPN-fiókot az alkalmazás regisztrálásával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 2b658cdc58777274bb14f9e8069cef2facdb0479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92363453"
---
# <a name="mark-your-app-as-publisher-verified"></a>Alkalmazás ellenőrzött közzétevővel rendelkezőként való megjelölése

Ha egy alkalmazás regisztrálása ellenőrzött közzétevővel rendelkezik, az azt jelenti, hogy az alkalmazás közzétevője [ellenőrizte](/partner-center/verification-responses) az identitását a Microsoft Partner Network (MPN) fiók használatával, és társítja ezt az MPN-fiókot az alkalmazás regisztrálásával. Ez a cikk bemutatja, hogyan végezheti el a [közzétevő ellenőrzési](publisher-verification-overview.md) folyamatát.

## <a name="quickstart"></a>Gyorsútmutató
Ha már regisztrált a Microsoft Partner Network (MPN) szolgáltatásban, és teljesítette az [előfeltételeket](publisher-verification-overview.md#requirements), azonnal megkezdheti az első lépéseket: 

1. Bejelentkezés az [alkalmazás regisztrációs portálján](https://aka.ms/PublisherVerificationPreview) a [multi-Factor Authentication](../fundamentals/concept-fundamentals-mfa-get-started.md) használatával

1. Válasszon ki egy alkalmazást, és kattintson a **branding** elemre. 

1. Kattintson az **MPN-azonosító hozzáadása elemre a közzétevő ellenőrzéséhez** és a felsorolt követelmények áttekintéséhez.

1. Adja meg az MPN-azonosítót, és kattintson az **ellenőrzés és mentés** gombra.

Az egyes előnyökkel, követelményekkel és gyakori kérdésekkel kapcsolatos további információkért tekintse meg az [áttekintést](publisher-verification-overview.md).


## <a name="mark-your-app-as-publisher-verified"></a>Alkalmazás ellenőrzött közzétevővel rendelkezőként való megjelölése
Győződjön meg arról, hogy teljesítette az [előfeltételeket](publisher-verification-overview.md#requirements), majd kövesse az alábbi lépéseket az alkalmazás (ok) megjelöléséhez a közzétevő által ellenőrzöttként.  

1. Győződjön meg arról, hogy a [többtényezős hitelesítéssel](../fundamentals/concept-fundamentals-mfa-get-started.md) olyan szervezeti (Azure ad-) fiókkal jelentkezett be, amely jogosult a közzétevőként megjelölni kívánt alkalmazás (ok) módosítására, valamint a fiókpartner MPN-fiókjára.

    - Az Azure AD-ben a felhasználónak a következő [szerepkörök](../roles/permissions-reference.md)egyikének kell lennie: Application admin, Cloud Application admin, globális rendszergazda. 

    - A partner Centerben ennek a felhasználónak a következő [szerepkörökkel](/partner-center/permissions-overview)kell rendelkeznie: MPN-rendszergazda, fiókok rendszergazdája vagy globális rendszergazda (ez az Azure ad-ben elsajátított megosztott szerepkör). 

1. Navigáljon az alkalmazás regisztrációs portálján:  

1. Kattintson arra az alkalmazásra, amelyet Közzétevőként szeretne megjelölni, és nyissa meg a branding (védjegyezés) panelt. 

1. Győződjön meg arról, hogy az alkalmazás [közzétevői tartománya](howto-configure-publisher-domain.md) be van állítva. 

1. Győződjön meg arról, hogy a bérlő vagy a DNS által ellenőrzött [egyéni tartomány](../fundamentals/add-custom-domain.md) megegyezik az MPN-fiók ellenőrzési folyamata során használt e-mail-cím tartományával.

1. Kattintson az **MPN-azonosító hozzáadása elemre a kiadó ellenőrzéséhez** az oldal alján. 

1. Adja meg az **MPN-azonosítót**. Ennek az MPN-AZONOSÍTÓnak a következőnek kell lennie: 

    - Egy érvényes Microsoft Partner Network-fiók, amely befejezte az ellenőrzési folyamatot.  

    - A szervezethez tartozó partner globális fiók (PGA). 

1. Kattintson **az ellenőrzés és mentés** gombra. 

1. Várjon, amíg a rendszer feldolgozza a kérést, ez eltarthat néhány percig. 

1. Ha az ellenőrzés sikeres volt, a közzétevő ellenőrzési ablaka be lesz zárva, és visszatér a branding (védjegyezés) panelre. Az ellenőrzött **közzétevők megjelenítendő neve** mellett egy kékan ellenőrzött jelvény jelenik meg. 

1. Azok a felhasználók, akik megkérik az alkalmazás belefoglalását, azonnal megkezdik a jelvény megtekintését, miután sikeresen elvégezte a folyamatot, bár ez eltarthat egy ideig, amíg a rendszer replikálja. 

1. Tesztelje ezt a funkciót úgy, hogy bejelentkezik az alkalmazásba, és gondoskodik arról, hogy az ellenőrzött jelvény a beleegyezési képernyőn jelenik meg. Ha olyan felhasználóként jelentkezett be, aki már kapott beleegyezett az alkalmazásba, a *kérések* lekérdezése paraméterrel kényszerítheti a beleegyezés kérését. Ezt a paramétert csak tesztelésre kell használni, és soha nem kell az alkalmazás kéréseibe kódolni.

1. Szükség szerint ismételje meg a folyamatot minden olyan alkalmazáshoz, amelyhez meg szeretné jeleníteni a jelvényt. A Microsoft Graph használatával a lehető leggyorsabban elvégezhető tömegesen, a PowerShell-parancsmagok pedig hamarosan elérhetők lesznek. További információért tekintse meg a [Microsoft API Graph-hívások készítése](troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) című témakört. 

Készen is van. Tudassa velünk, hogy van-e visszajelzése a folyamatról, az eredményekről vagy a szolgáltatásról. 

## <a name="next-steps"></a>Következő lépések
Ha problémákat tapasztal, olvassa el a [hibaelhárítási információkat](troubleshoot-publisher-verification.md).
