---
title: Jóváhagyott ügyfélalkalmazások feltételes hozzáféréssel – Azure Active Directory
description: Megtudhatja, hogyan igényelhet jóváhagyott ügyfélalkalmazások a felhőalapú alkalmazásokhoz való hozzáféréshez Azure Active Directory-ben feltételes hozzáféréssel.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 122cc6a2be17cb35e77b638a60fc5fa4f035c0d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91266139"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Útmutató: jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel

A felhasználók a személyes és munkahelyi feladatokhoz egyaránt rendszeresen használják a mobileszközök használatát. A munkavégzés hatékonysága érdekében a szervezetek emellett azt is meg szeretnék akadályozni, hogy a potenciálisan nem biztonságos alkalmazások adatvesztést okoznak. A feltételes hozzáférés révén a szervezetek korlátozhatják a jóváhagyott (modern hitelesítésre képes) ügyfélalkalmazások hozzáférését.

Ez a cikk két forgatókönyvet mutat be a feltételes hozzáférési házirendek konfigurálásához olyan erőforrásokhoz, mint a Microsoft 365, az Exchange Online és a SharePoint Online.

- [1. forgatókönyv: Microsoft 365 alkalmazásokhoz jóváhagyott ügyfélalkalmazás szükséges](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [2. forgatókönyv: az Exchange Online és a SharePoint Online alkalmazáshoz jóváhagyott ügyfélalkalmazás szükséges](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

A feltételes hozzáférésben ez a funkció egy jóváhagyott ügyfélalkalmazás megkövetelése. A jóváhagyott ügyfélalkalmazások listáját itt tekintheti meg: [jóváhagyott ügyfélalkalmazás-követelmény](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Az iOS-és Android-eszközökhöz jóváhagyott ügyfélalkalmazások megköveteléséhez ezeket az eszközöket először regisztrálni kell az Azure AD-ben.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>1. forgatókönyv: Microsoft 365 alkalmazásokhoz jóváhagyott ügyfélalkalmazás szükséges

Ebben az esetben a contoso úgy döntött, hogy a mobileszközök használó felhasználók hozzáférhetnek az összes Microsoft 365 szolgáltatáshoz, feltéve, hogy a jóváhagyott ügyfélalkalmazások, például az Outlook Mobile, a OneDrive és a Microsoft Teams használata engedélyezett. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő három lépést kell végrehajtaniuk ahhoz, hogy egy jóváhagyott ügyfélalkalmazás használatát meg lehessen követelni a mobileszközökön.

**1. lépés: az Android-és iOS-alapú modern hitelesítési ügyfelekre vonatkozó szabályzat, amely a jóváhagyott ügyfélalkalmazás használatát igényli az Exchange Online-hoz való hozzáféréskor.**

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
1. A **feltételek** területen válassza az **ügyfélalkalmazások (előzetes verzió)** lehetőséget.
   1. Állítsa **az** **Igen** értékre.
   1. Válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, **jóváhagyott ügyfélalkalmazás megkövetelése**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**2. lépés: Azure AD feltételes hozzáférési szabályzat konfigurálása az Exchange Online-hoz az ActiveSync (EAS) segítségével**

1. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
1. Válassza az **új szabályzat** lehetőséget.
1. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
1. A **hozzárendelések** alatt válassza a **felhasználók és csoportok** lehetőséget.
   1. A **Belefoglalás** területen válassza ki az **összes felhasználó** elemet, illetve azokat a **felhasználókat és csoportokat** , amelyekre alkalmazni szeretné a szabályzatot. 
   1. Válassza a **Kész** lehetőséget.
1. A **Cloud apps vagy a műveletek** területen  >  válassza az **Office 365 Exchange Online** lehetőséget.
1. **Feltételek**:
   1. **Ügyfélalkalmazások (előzetes verzió)**:
      1. Állítsa **az** **Igen** értékre.
      1. Válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, **jóváhagyott ügyfélalkalmazás megkövetelése**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára.**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>2. forgatókönyv: az Exchange Online és a SharePoint Online alkalmazáshoz jóváhagyott ügyfélalkalmazás szükséges

Ebben az esetben a contoso úgy döntött, hogy a felhasználók csak akkor férhetnek hozzá a mobileszközök e-mail-és SharePoint-adataihoz, ha egy jóváhagyott ügyfélalkalmazás, például az Outlook Mobile használatát használják. Az összes felhasználó már be van jelentkezni az Azure AD hitelesítő adataival, és rendelkezik a hozzájuk rendelt licenccel prémium szintű Azure AD P1 vagy P2 és Microsoft Intune.

A szervezeteknek a következő három lépést kell végrehajtaniuk ahhoz, hogy egy jóváhagyott ügyfélalkalmazás használatát meg lehessen követelni a mobileszközök és az Exchange ActiveSync-ügyfelek számára.

**1. lépés: az Android-és iOS-alapú modern hitelesítési ügyfelekre vonatkozó szabályzat, amely a jóváhagyott ügyfélalkalmazás használatát igényli az Exchange Online-hoz és a SharePoint Online-hoz való hozzáféréshez.**

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
1. A **feltételek** területen válassza az **ügyfélalkalmazások (előzetes verzió)** lehetőséget.
   1. Állítsa **az** **Igen** értékre.
   1. Válassza a **Mobile apps és az asztali ügyfelek** és a **modern hitelesítési ügyfelek** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, **jóváhagyott ügyfélalkalmazás megkövetelése**, majd a **kiválasztás** lehetőséget.
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
1. **Feltételek**:
   1. **Ügyfélalkalmazások (előzetes verzió)**:
      1. Állítsa **az** **Igen** értékre.
      1. Válassza a **Mobile apps és az asztali ügyfelek** és az **Exchange ActiveSync-ügyfelek** lehetőséget.
1. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, **jóváhagyott ügyfélalkalmazás megkövetelése**, majd a **kiválasztás** lehetőséget.
1. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
1. Válassza a **Létrehozás** lehetőséget a szabályzat létrehozásához és engedélyezéséhez.

**3. lépés: az Intune app Protection-szabályzat konfigurálása iOS-és Android-ügyfélalkalmazások számára.**

Tekintse át az alkalmazás-védelmi szabályzatok létrehozását [és hozzárendelését](/intune/apps/app-protection-policies)ismertető cikket, amely az Android és az iOS rendszerhez készült alkalmazás-védelmi szabályzatok létrehozásának lépéseit ismerteti. 

## <a name="next-steps"></a>Következő lépések

[Mit jelent a feltételes hozzáférés?](overview.md)

[Feltételes hozzáférési összetevők](concept-conditional-access-policies.md)

[Általános feltételes hozzáférési szabályzatok](concept-conditional-access-policy-common.md)
