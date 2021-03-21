---
title: Alkalmazás-védelmi szabályzatok feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan igényelhet alkalmazás-védelmi szabályzatot a felhőalapú alkalmazásokhoz való hozzáféréshez Azure Active Directory-ben való feltételes hozzáféréssel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575771"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Útmutató: az alkalmazás védelmi szabályzatának és a felhőalapú alkalmazások hozzáférésének megkövetelése feltételes hozzáféréssel

A felhasználók a személyes és munkahelyi feladatokhoz egyaránt rendszeresen használják a mobileszközök használatát. A munkavégzés hatékonysága érdekében a szervezetek emellett azt is meg szeretnék akadályozni, hogy a potenciálisan nem biztonságos alkalmazások adatvesztést okoznak. A feltételes hozzáféréssel a szervezetek a jóváhagyott (modern hitelesítésre képes) ügyfélalkalmazások hozzáférését a rájuk alkalmazott Intune app Protection-szabályzatokkal korlátozhatják.

Ez a cikk három forgatókönyvet mutat be a feltételes hozzáférési házirendek konfigurálásához olyan erőforrásokhoz, mint a Microsoft 365, az Exchange Online és a SharePoint.

- [1. forgatókönyv: Microsoft 365 alkalmazásokban jóváhagyott alkalmazások szükségesek az alkalmazás-védelmi házirendekkel](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [2. forgatókönyv: a böngésző alkalmazásai jóváhagyott alkalmazásokat igényelnek az alkalmazás védelmi házirendjeivel](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [3. forgatókönyv: az Exchange Online és a SharePoint jóváhagyott ügyfélalkalmazás-és alkalmazás-védelmi szabályzatot igényel](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

A feltételes hozzáférésben ezek az ügyfélalkalmazások ismertek, hogy védelmet kapnak az alkalmazás védelmi házirendjével. Az alkalmazás-védelmi szabályzatokkal kapcsolatos további információkért tekintse meg az [app Protection-házirendek áttekintése](/intune/apps/app-protection-policy) című cikket.

> [!WARNING]
> Nem minden alkalmazás támogatott, és nem támogatja az alkalmazás-védelmi szabályzatokat. A jogosult ügyfélalkalmazások listáját a következő témakörben tekintheti meg: az [alkalmazás védelmére vonatkozó követelmény](concept-conditional-access-grant.md#require-app-protection-policy).

> [!NOTE]
> "A kijelölt vezérlők egyikének megkövetelése" az engedélyezési vezérlők területen, például egy vagy záradék. Ez a házirend lehetővé teszi a felhasználók számára, hogy olyan alkalmazásokat használjanak, amelyek támogatják az **alkalmazás-védelmi házirend megkövetelését** vagy a **jóváhagyott ügyfélalkalmazás** -engedélyezési vezérlőket. Az **alkalmazás védelmi szabályzatának megkövetelése** akkor lép érvénybe, ha az alkalmazás támogatja az adott vezérlőt. További információ arról, hogy mely alkalmazások támogatják az **alkalmazás-védelmi házirend megkövetelése** vezérlő használatát: az [alkalmazás védelmére vonatkozó követelmény](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>1. forgatókönyv: Microsoft 365 alkalmazásokban jóváhagyott alkalmazások szükségesek az alkalmazás-védelmi házirendekkel

Ebben az esetben a contoso úgy döntött, hogy az Microsoft 365 erőforrásokhoz való összes mobil hozzáférésnek jóváhagyott ügyfélalkalmazások, például az Outlook Mobile és a OneDrive használatát kell használnia, amely a hozzáférés megkezdése előtt egy app Protection-házirend által védett. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő lépéseket kell végrehajtaniuk ahhoz, hogy a jóváhagyott ügyfélalkalmazás használatát kötelezővé lehessen tenni a mobileszközökön.

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása Microsoft 365hoz**

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365** lehetőséget.
1. A **feltételek** területen válassza az **eszközök platformok** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. **Android** és **iOS** is.
1. A **feltételek** területen válassza az **ügyfélalkalmazások** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. Válassza a **Mobile apps és az asztali ügyfelek** lehetőséget, és törölje a jelölést minden más elemnél.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza ki a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazásvédelmi szabályzat megkövetelése**
   - **A kiválasztott vezérlők egyikének megkövetelése**
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az ActiveSync (EAS) segítségével**

Az ebben a lépésben megadott feltételes hozzáférési szabályzat esetében konfigurálja a következő összetevőket:

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365 Exchange Online** lehetőséget.
1. A **feltételek** területen válassza az **ügyfélalkalmazások** elemet:
   1. Állítsa **az** **Igen** értékre.
   1. Válassza az **Exchange ActiveSync-ügyfelek** lehetőséget, és törölje a jelölést minden más elemnél.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, az **alkalmazás-védelmi házirend megkövetelése** lehetőséget, és válassza a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>2. forgatókönyv: a böngésző alkalmazásai jóváhagyott alkalmazásokat igényelnek az alkalmazás védelmi házirendjeivel

Ebben a forgatókönyvben a contoso úgy döntött, hogy az Microsoft 365-erőforrásokhoz való összes mobil webböngészésnek egy jóváhagyott ügyfélalkalmazás, például az iOS és Android rendszerhez való hozzáférését kell használnia, a hozzáférés megkezdése előtt egy app Protection-házirend által védett alkalmazás. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő lépéseket kell végrehajtaniuk ahhoz, hogy a jóváhagyott ügyfélalkalmazás használatát kötelezővé lehessen tenni a mobileszközökön.

**1. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása Microsoft 365hoz**

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365** lehetőséget.
1. A **feltételek** területen válassza az **eszközök platformok** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. **Android** és **iOS** is.
1. A **feltételek** területen válassza az **ügyfélalkalmazások** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. Válassza ki a **böngészőt** , és törölje a jelölést minden más elemnél
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza ki a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazásvédelmi szabályzat megkövetelése**
   - **A kiválasztott vezérlők egyikének megkövetelése**
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**2. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>3. forgatókönyv: az Exchange Online és a SharePoint jóváhagyott ügyfélalkalmazás-és alkalmazás-védelmi szabályzatot igényel

Ebben az esetben a contoso úgy döntött, hogy a felhasználók csak akkor férhetnek hozzá a mobileszközök e-mail-és SharePoint-adataihoz, ha olyan jóváhagyott ügyfélalkalmazás-alkalmazást használnak, mint az alkalmazás-védelmi szabályzat által védett Outlook Mobile. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő három lépést kell végrehajtaniuk ahhoz, hogy egy jóváhagyott ügyfélalkalmazás használatát meg lehessen követelni a mobileszközök és az Exchange ActiveSync-ügyfelek számára.

**1. lépés: az Android-és iOS-alapú modern hitelesítési ügyfelekre vonatkozó szabályzat, amely a jóváhagyott ügyfélalkalmazás és az alkalmazás-védelmi szabályzat használatát igényli az Exchange Online és a SharePoint eléréséhez.**

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365 Exchange Online** és az **Office 365 SharePoint Online** lehetőséget.
1. A **feltételek** területen válassza az **eszközök platformok** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. **Android** és **iOS** is.
1. A **feltételek** területen válassza az **ügyfélalkalmazások** elemet.
   1. Állítsa **az** **Igen** értékre.
   1. Válassza a **Mobile apps és az asztali ügyfelek** lehetőséget, és törölje a jelölést minden más elemnél.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza ki a következő beállításokat:
   - **Jóváhagyott ügyfélalkalmazás megkövetelése**
   - **Alkalmazásvédelmi szabályzat megkövetelése**
   - **A kiválasztott vezérlők egyikének megkövetelése**
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**2. lépés: a jóváhagyott ügyfélalkalmazás használatát igénylő Exchange ActiveSync-ügyfelek házirendje.**

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365 Exchange Online** lehetőséget.
1. A **feltételek** területen válassza az **ügyfélalkalmazások** elemet:
   1. Állítsa **az** **Igen** értékre.
   1. Válassza az **Exchange ActiveSync-ügyfelek** lehetőséget, és törölje a jelölést minden más elemnél.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, az **alkalmazás-védelmi házirend megkövetelése** lehetőséget, és válassza a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára.**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="next-steps"></a>Következő lépések

[Mit jelent a feltételes hozzáférés?](overview.md)

[Feltételes hozzáférési összetevők](concept-conditional-access-policies.md)

[Általános feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)

