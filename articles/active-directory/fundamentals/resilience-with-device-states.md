---
title: Rugalmasság kialakítása a Azure Active Directory eszköz állapotának használatával
description: Útmutató az építészek és a rendszergazdák számára a rugalmasság kialakításához az eszközök állapotának használatával
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724610"
---
# <a name="build-resilience-with-device-states"></a>Rugalmasság kiépítése az eszközök állapotával

Az [eszközök](../devices/overview.md) az Azure ad-vel való engedélyezésével a rendszergazdák olyan [feltételes hozzáférési szabályzatokat](../conditional-access/overview.md) hozhatnak létre, amelyek az eszköz állapotán alapuló alkalmazásokhoz való hozzáférést szabályozzák. Az eszközök hozzáadott előnye, hogy megfelel az erőforrásokhoz való hozzáférés erős hitelesítési követelményeinek, így csökkentve a további MFA hitelesítési kérelmeket, és javítja a rugalmasságot. 

A következő folyamatábra az eszközök az Azure AD-ben való bevezetésének különböző módjait mutatja be az eszköz állapotának engedélyezéséhez. A szervezetben több is használható.

![az eszközök állapotának kiválasztására szolgáló folyamatábra](./media/resilience-with-device-states/admin-resilience-devices.png)

Az [eszközök állapotának](../devices/overview.md)használatakor a felhasználók a legtöbb esetben egyszeri bejelentkezést tapasztalnak az erőforrásokhoz egy [elsődleges frissítési jogkivonat](../devices/concept-primary-refresh-token.md) (PRT) használatával. A PRT a felhasználóval és az eszközzel kapcsolatos jogcímeket tartalmaz, és az alkalmazásokhoz való hozzáféréshez használható hitelesítési tokenek beszerzésére szolgál. A PRT 14 napig érvényes, és folyamatosan megújul, amíg a felhasználó aktívan használja az eszközt, és rugalmas élményt nyújt a felhasználóknak. A PRT több módon is beszerezhet egy multi-Factor Authentication-jogcímet. További információkért lásd: [Ha egy PRT-t kap egy MFA-jogcím beszerzéséhez](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Hogyan segíthetnek az eszközök állapota?

Ha egy PRT-t használ az alkalmazáshoz való hozzáférés kérelmezéséhez, az eszköz, a munkamenet és az MFA-jogcímek az Azure AD-ben megbízhatók. Ha a rendszergazdák olyan házirendeket hoznak létre, amelyek eszköz-vagy többtényezős hitelesítési vezérlőt igényelnek, akkor a házirend követelménye a többtényezős hitelesítés megkísérlése nélkül is teljesíthető. A felhasználók nem látják a további multi-Factor Authentication-kéréseket ugyanarra az eszközre. Ez növeli a rugalmasságot az Azure MFA szolgáltatás megszakadásával, illetve a hozzájuk tartozó függőségekkel, például a helyi távközlési szolgáltatókkal.

## <a name="how-do-i-implement-device-states"></a>Hogyan az eszközök állapotának megvalósítását?

* Engedélyezze a [hibrid Azure ad](../devices/hybrid-azuread-join-plan.md) - [csatlakozást és az Azure ad Joint](../devices/azureadjoin-plan.md) a vállalat tulajdonában lévő Windows-eszközökhöz, és szükség esetén csatlakoztassa őket. Ha nem lehetséges, regisztrálni kell őket.

  Ha a szervezetben a Windows régebbi verziói vannak, frissítse ezeket az eszközöket a Windows 10 használatára.

* A felhasználói böngésző hozzáférésének szabványosítása a [Microsoft Edge](/deployedge/microsoft-edge-security-identity) vagy a Google Chrome olyan [támogatott](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [bővítményekkel](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) való használatához, amelyek zökkenőmentes egyszeri bejelentkezést engedélyeztek a webes alkalmazásoknak a PRT használatával.

* A személyes vagy vállalati tulajdonú iOS-és Android-eszközök esetében telepítse a [Microsoft Authenticator alkalmazást](../user-help/user-help-auth-app-overview.md). A többtényezős hitelesítés és a jelszó nélküli bejelentkezési képességek mellett a Microsoft Authenticator alkalmazás lehetővé teszi az egyszeri bejelentkezést a natív alkalmazáson keresztül, felügyelt [hitelesítéssel](../develop/msal-android-single-sign-on.md) , kevesebb hitelesítési kéréssel a végfelhasználók számára.

* A személyes vagy vállalati tulajdonú iOS-és Android-eszközökön a [mobileszközök kezelése](/mem/intune/apps/app-management) szolgáltatással kevesebb hitelesítési kérelemmel férhet hozzá a vállalati erőforrásokhoz. 

* [Használja az Apple-eszközökhöz készült Microsoft Enterprise SSO beépülő modult (előzetes verzió)](../develop/apple-sso-plugin.md). Ez regisztrálja az eszközt, és egyszeri bejelentkezést biztosít a böngészőben és a natív Azure AD-alkalmazásokban. 

## <a name="next-steps"></a>Következő lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság létrehozása a hitelesítőadat-kezeléssel](resilience-in-credentials.md)

* [Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének (CAE) használatával](resilience-with-continuous-access-evaluation.md)

* [Rugalmasság létrehozása külső felhasználói hitelesítéssel](resilience-b2b-authentication.md)

* [Hozzon létre rugalmasságot a hibrid hitelesítésben](resilience-in-hybrid.md)

* [Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához](resilience-on-premises-access.md)


Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)