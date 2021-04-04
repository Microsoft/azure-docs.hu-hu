---
title: Alkalmazás-& erőforrás-hozzáférés kezelése csoportok használatával – Azure AD
description: Ismerje meg, hogyan kezelheti a szervezet felhőalapú alkalmazásaihoz, helyszíni alkalmazásaihoz és erőforrásaihoz való hozzáférést Azure Active Directory csoportok használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735f838ad4532b140eddcb4ce1ced24fba9a81be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369114"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Alkalmazás- és erőforrás-hozzáférés kezelése Azure Active Directory-csoportokkal
Azure Active Directory (Azure AD) segítségével csoportok használatával kezelheti a felhőalapú alkalmazásokhoz, a helyszíni alkalmazásokhoz és az erőforrásokhoz való hozzáférést. Az erőforrások lehetnek az Azure AD-szervezet részei, például az objektumok felügyeletéhez szükséges engedélyek az Azure AD-ben vagy a szervezeten kívül, például a szolgáltatott szoftver (SaaS) alkalmazások, az Azure-szolgáltatások, a SharePoint-webhelyek és a helyszíni erőforrások számára.

>[!NOTE]
> A Azure Portalban megtekintheti azokat a csoportokat, amelyek tagsági és csoportos adatait nem tudja kezelni a portálon:
>
> - A helyszíni Active Directory szinkronizált csoportok csak a helyszíni Active Directory kezelhetők.
> - A más típusú csoportok, például a terjesztési és a levelezésre képes biztonsági csoportok csak az Exchange felügyeleti központban vagy Microsoft 365 felügyeleti központban kezelhetők. A csoportok kezeléséhez be kell jelentkeznie az Exchange felügyeleti központba vagy Microsoft 365 felügyeleti központba.

## <a name="how-access-management-in-azure-ad-works"></a>Hogyan működik a hozzáférés-kezelés az Azure AD-ben?

Az Azure AD segítségével hozzáférést biztosíthat a szervezet erőforrásaihoz azáltal, hogy hozzáférési jogokat biztosít egyetlen felhasználónak vagy egy teljes Azure AD-csoportnak. A csoportok használatával az erőforrás (vagy az Azure AD-címtár) tulajdonosa hozzáférési engedélyeket rendelhet a csoport minden tagjához ahelyett, hogy ezt egyesével kellene megtennie. Az erőforrás vagy a könyvtár tulajdonosa a tagok számára is megadhatja a felügyeleti jogokat, például a részleg kezelője vagy a segélyszolgálat rendszergazdája számára, hogy szükség szerint adjon hozzá és távolítson el tagokat. További információ a csoportok tulajdonosainak kezeléséről: [tulajdonosok kezelése](active-directory-accessmanagement-managing-group-owners.md)

![Ábra: Az Azure Active Directory hozzáférés-kezelése](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Hozzáférési jogosultságok kiosztásának módjai

Az erőforrás-hozzáférési jogosultságokat négyféle módon rendelheti hozzá a felhasználókhoz:

- **Közvetlen hozzárendelés.** Az erőforrás tulajdonosa közvetlenül hozzárendeli a felhasználót az erőforráshoz.

- **Csoport-hozzárendelés.** Az erőforrás tulajdonosa egy Azure AD-csoportot rendel az erőforráshoz, amely automatikusan lehetővé teszi az összes csoport tagjai számára az erőforráshoz való hozzáférést. A csoporttagság kezelését a csoport tulajdonosa és az erőforrás tulajdonosa is kezeli, így a tulajdonos a csoport tagjainak hozzáadását vagy eltávolítását is lehetővé teszi. A csoporttagság hozzáadásával vagy eltávolításával kapcsolatos további információkért lásd [: útmutató: Csoport hozzáadása vagy eltávolítása egy másik csoportból a Azure Active Directory-portál használatával](active-directory-groups-membership-azure-portal.md). 

- **Szabály-alapú hozzárendelés.** Az erőforrás tulajdonosa létrehoz egy csoportot, és egy olyan szabályt használ, amely meghatározza, hogy mely felhasználók vannak hozzárendelve egy adott erőforráshoz. A szabály az egyes felhasználókhoz rendelt attribútumok alapján történik. Az erőforrás tulajdonosa kezeli a szabályt, és meghatározza, hogy mely attribútumok és értékek szükségesek az erőforráshoz való hozzáférés engedélyezéséhez. További információ: [dinamikus csoport létrehozása és állapotának ellenõrzése](../enterprise-users/groups-create-rule.md).

    Ezt a rövid videót is megtekintheti a dinamikus csoportok létrehozásával és használatával kapcsolatos gyors magyarázattal:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Külső szolgáltató hozzárendelése.** A hozzáférés egy külső forrásból, például egy helyszíni címtárból vagy egy SaaS-alkalmazásból származik. Ebben az esetben az erőforrás tulajdonosa hozzárendel egy csoportot az erőforráshoz való hozzáférés biztosításához, majd a külső forrás kezeli a csoporttagokat.

   ![Ábra: A hozzáférés-kezelés áttekintése](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>A felhasználók hozzárendelés nélkül csatlakozhatnak a csoportokhoz?
A csoport tulajdonosa lehetővé teheti a felhasználók számára, hogy a hozzárendelésük helyett a saját Csoportjaikat is megtalálják a csatlakozáshoz. A tulajdonos úgy is beállíthatja a csoportot, hogy automatikusan fogadja az összes csatlakozni kívánó felhasználót, vagy jóváhagyást kér.

Miután egy felhasználó megpróbált csatlakozni egy csoporthoz, a rendszer továbbítja a kérést a csoport tulajdonosának. Ha szükséges, a tulajdonos jóváhagyhatja a kérést, és a felhasználó értesítést kap a csoporttagságról. Ha azonban több tulajdonossal rendelkezik, és egyikük sem hagyja jóvá, a rendszer értesítést küld a felhasználónak, de nem adja hozzá a csoportot. További információt és útmutatást arról, hogyan kérheti a felhasználók a csoportok összekapcsolását: az [Azure ad beállítása, hogy a felhasználók csatlakozzanak a csoporthoz](../enterprise-users/groups-self-service-management.md)

## <a name="next-steps"></a>Következő lépések
Most, hogy már rendelkezik egy kis bevezetéssel a hozzáférés-kezeléshez a csoportok használatával, megkezdheti az erőforrások és alkalmazások kezelését.

- [Új csoport létrehozása Azure Active Directory használatával](active-directory-groups-create-azure-portal.md) vagy [új csoport létrehozása és kezelése PowerShell-parancsmagok használatával](../enterprise-users/groups-settings-v2-cmdlets.md)

- [Csoportok használata egy integrált SaaS-alkalmazáshoz való hozzáféréshez](../enterprise-users/groups-saasapps.md)

- [Helyszíni csoport szinkronizálása az Azure-ba a Azure AD Connect használatával](../hybrid/whatis-hybrid-identity.md)
