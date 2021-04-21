---
title: Rövid útmutató – Hozzáférés & bérlő létrehozásához – Azure AD
description: Útmutató új Azure Active Directory és új bérlő létrehozásához a szervezet számára.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc51c645c470f2b5b0a009eaf831db2f1957617e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780137"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Rövid útmutató: Új bérlő létrehozása a Azure Active Directory

Az Azure Active Directory (Azure AD) portált használva elvégezheti valamennyi felügyeleti feladatát, a szervezet számára új bérlő létrehozását is beleértve. 

Ebből a rövid útmutatóból megtudhatja, hogyan érheti el az Azure portált és az Azure Active Directory-t, és megtudhatja, hogyan hozhat létre alapszintű bérlőt a szervezete számára.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="create-a-new-tenant-for-your-organization"></a>Új bérlő létrehozása a szervezetén belül

Miután bejelentkezett az Azure portálra, létrehozhat egy új bérlőt a szervezet számára. Az új bérlő a szervezetét képviseli és segíti a Microsoft felhőszolgáltatások konkrét példányának kezelését a belső és külső felhasználók számára.

### <a name="to-create-a-new-tenant"></a>Új bérlő létrehozása

1. Jelentkezzen be a szervezet [Azure Portal.](https://portal.azure.com/)

1. A Azure Portal menüben válassza **a** Azure Active Directory lehetőséget.  

    <kbd>![Azure Active Directory – Áttekintés lap – Bérlő létrehozása](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Válassza **a Bérlő létrehozása lehetőséget.**

1. Az Alapvető beállítások lapon válassza ki a létrehozni kívánt bérlő típusát( Azure Active Directory **vagy** **Azure Active Directory (B2C).**

1. A **Tovább: Konfigurálás** lehetőséget választva lépjen tovább a Konfiguráció lapra.

    <kbd>![Azure Active Directory – Bérlői lap létrehozása – konfigurációs lap ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  A Konfiguráció lapon adja meg a következő adatokat:
    
    - Írja be _a Contoso Szervezet_ nevet a Szervezet **neve** mezőbe.

    - Írja _be a Contosoorg_ nevet a **Kezdeti tartománynév mezőbe.**

    - Hagyja meg az _Egyesült Államok_ beállítást az **Ország vagy régió** mezőben.

1. Válassza **a Tovább: Áttekintés + Létrehozás lehetőséget.** Tekintse át a megadott adatokat, és ha az adatok helyesek, válassza a **létrehozás lehetőséget.**

    <kbd>![Azure Active Directory – Bérlői oldal áttekintése és létrehozása](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Az új bérlő létrejön a contoso.onmicrosoft.com tartománnyal.

## <a name="your-user-account-in-the-new-tenant"></a>Az új bérlő felhasználói fiókja

Új Azure AD-bérlő létrehozásakor Ön lesz a bérlő első felhasználója. Első felhasználóként a rendszer automatikusan hozzárendeli a globális [rendszergazdai szerepkört.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference#global-administrator) A felhasználói fiókját a Felhasználók lapra [**navigálva ellenőrizheti.**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers)

Alapértelmezés szerint a bérlő technikai [](https://docs.microsoft.com/microsoft-365/admin/manage/change-address-contact-and-more?view=o365-worldwide#what-do-these-fields-mean) kapcsolattartójaként is megjelenik a listában. A műszaki kapcsolattartási adatok a Tulajdonságok tulajdonságokkal [**módosíthatja a következőt:**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

> [!WARNING]
> Győződjön meg arról, hogy a címtár legalább két fiókkal rendelkezik, amelyekhez globális rendszergazdai jogosultságok vannak hozzárendelve. Ez segít abban az esetben, ha egy globális rendszergazda ki van zárva. További részletekért tekintse meg a Vészelérési fiókok kezelése [az Azure AD-ban](../roles/security-emergency-access.md)cikket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, a következő lépésekkel törölheti a bérlőt:

- Győződjön meg arról, hogy be van jelentkezve a törölni kívánt címtárba a címtár Címtár **és** előfizetés szűrővel a Azure Portal. Szükség esetén váltson a célkönyvtárra.
- Válassza ki **Azure Active Directoryt**, majd a **Contoso – áttekintés** lapon a **Címtár törlése** lehetőséget.

    A bérlő és a hozzá kapcsolódó információk törlődnek.

    <kbd>![Áttekintő oldal, kiemelt Könyvtár törlése gombbal](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Következő lépések

- Tartománynevek módosítása és továbbiak felvétele: [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](add-custom-domain.md)

- Felhasználók felvétele: [Új felhasználó hozzáadása és törlése](add-users-azure-active-directory.md)

- Csoportok és tagok hozzáadása: [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- Ismerje meg [a szerepköralapú](../../role-based-access-control/best-practices.md) hozzáférést a Privileged Identity Management [és](../../role-based-access-control/conditional-access-azure-management.md) a feltételes hozzáférés használatával a szervezet alkalmazás- és erőforrás-hozzáférésének kezeléséhez.

- Tudnivalók az Azure Active Directoryról, beleértve [az alapszintű licenceléssel, a terminológiával és a társított szolgáltatásokkal](active-directory-whatis.md) kapcsolatos információkat.
