---
title: Regisztrálja az alkalmazást, hogy az Azure Active Directory |} A Microsoft Docs
description: Az informatikai szakemberek számára írt, ez a cikk útmutatást nyújt a az Azure-alkalmazások integrálása az Active Directoryval.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: celested
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 341d7667f039a7272a7ad3fec80a445aa95b619d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440232"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Az Azure Active Directory-üzleti alkalmazások fejlesztése
Ez az útmutató áttekintést fejlesztése – üzletági (LoB) alkalmazásokat az Azure Active Directory (AD). A célközönség az Active Directory vagy Office 365 globális rendszergazdái.

## <a name="overview"></a>Áttekintés
A szervezete egyszeri bejelentkezés az Office 365-tel az Azure AD-val integrált alkalmazásokat biztosít a felhasználóknak. Az alkalmazás kellene az Azure ad-ben szabályozható a hitelesítési szabályzatot az alkalmazáshoz. További információ a feltételes hozzáférés és a többtényezős hitelesítés (MFA) tekintse meg az alkalmazások védelme [hozzáférési szabályok konfigurálása](../conditional-access/app-based-mfa.md).

Regisztrálja az alkalmazást, hogy az Azure Active Directory. Az alkalmazás regisztrálása, az azt jelenti, hogy a fejlesztők használhatják-e az Azure ad-ben hitelesítheti a felhasználókat, és kérjen hozzáférést a felhasználói erőforrások, például az e-mailek, naptár és dokumentumok.

A könyvtár (nem a vendégek) bármely tagja is regisztrálni egy alkalmazást, más néven *létrehozása egy alkalmazásobjektumot*.

Az alkalmazások regisztrálásának lehetővé teszi, hogy minden felhasználó számára tegye a következőket:

* Az identitás lekérése az alkalmazáshoz, amely felismeri az Azure AD
* Egy vagy több titkos kódok és kulcsok beolvasása, amellyel az alkalmazás AD hitelesítse magát
* Saját arculat az alkalmazás az Azure Portalon, egy egyéni nevet, emblémát, stb.
* Az Azure AD engedélyezési funkciókat alkalmazni, hogy az alkalmazás többek között:

  * Szerepköralapú hozzáférés-vezérlés (RBAC)
  * Az Azure Active Directoryhoz, oAuth-engedélyezési kiszolgáló (az alkalmazás által elérhetővé tett API-k biztonságos)
* Deklarálja szükség az alkalmazás működéséhez szükséges engedélyekkel, megfelelően működik, beleértve:

     - Alkalmazásengedélyek (csak a globális rendszergazdák). Példa: Szerepkörtagság egy másik Azure AD alkalmazás vagy szerepkör a csoporttagság megváltozott azóta viszonyított egy Azure Resource, erőforráscsoport, vagy előfizetésbe
     - Delegált engedélyek (minden felhasználó). Példa: Az Azure AD-be, és olvasási profil

> [!NOTE]
> Alapértelmezés szerint minden tag regisztrálhat egy alkalmazást. Meghatározott tagok alkalmazások regisztrálása engedélyeinek korlátozása kapcsolatban lásd: [hogyan alkalmazások felvétele az Azure ad-ben](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).
>
>

Íme, mi a globális rendszergazdának kell tennie segítségével a fejlesztők, győződjön meg arról, az alkalmazás készen áll az éles környezetben:

* Hozzáférési szabályok (hozzáférési házirend/MFA) konfigurálása
* Felhasználó-hozzárendelés szükséges, és hozzárendelhet felhasználókat az alkalmazás konfigurálása
* Az alapértelmezett felhasználói jóváhagyás élmény elrejtése

## <a name="configure-access-rules"></a>Hozzáférési szabályok konfigurálása
Állítsa be az alkalmazás hozzáférési szabályok az SaaS-alkalmazásokhoz. Például többtényezős hitelesítés, vagy csak felhasználók férhessenek hozzá a megbízható hálózatokon. A részleteket a érhetők el a dokumentumot [hozzáférési szabályok konfigurálása](../conditional-access/app-based-mfa.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Felhasználó-hozzárendelés szükséges, és hozzárendelhet felhasználókat az alkalmazás konfigurálása
Alapértelmezés szerint felhasználók érvényessége a felhasználóhoz anélkül is hozzáférhetnek alkalmazásaikhoz. Ha az alkalmazás elérhetővé teszi a szerepkörök, vagy ha azt szeretné, hogy az alkalmazás a felhasználó hozzáférési panelen megjelenő, felhasználó-hozzárendelés érdemes beállítani.

Ha Ön egy Azure AD prémium vagy nagyvállalati mobilitási csomag (EMS) előfizető, javasoljuk, hogy a csoportok. Csoportok hozzárendelése az alkalmazás lehetővé teszi, hogy a csoport tulajdonosa a folyamatban lévő hozzáférési felügyelet delegálása. Hozza létre a csoportot, vagy kérje meg a felelős fél létrehozni a csoport felügyeleti funkció segítségével a csoportot a szervezetben.

[Felhasználók és csoportok hozzárendelése egy alkalmazáshoz](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>Felhasználói beleegyezés elrejtése
Alapértelmezés szerint minden felhasználó végighalad egy jóváhagyási felületen való bejelentkezéshez. Lehet, hogy a felhasználók számára, aki ismeri az ilyen döntéseket disconcerting a jóváhagyási felületen, a felhasználóktól egy alkalmazás engedélyeket.

Megbízható alkalmazások beleegyezik abba, hogy a szervezet nevében az alkalmazás által egyszerűsítheti a felhasználói élményt.

További információ a felhasználói beleegyezés és a jóváhagyás működik az Azure-ban, lásd: [alkalmazások az Azure Active Directoryval való integrálását](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Biztonságos távoli hozzáférést a helyszíni alkalmazások az Azure AD-alkalmazásproxy engedélyezése](application-proxy.md)
* [Az Azure AD-alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)

