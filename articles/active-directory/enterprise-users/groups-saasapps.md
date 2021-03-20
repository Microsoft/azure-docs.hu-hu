---
title: SaaS-alkalmazásokhoz való hozzáférés kezelése csoport használatával – Azure AD | Microsoft Docs
description: A Azure Active Directory csoportok használata a Azure Active Directory integrált SaaS-alkalmazásokhoz való hozzáféréshez.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: df36cd334dbb455cf1717bf18fc6c8337d0ee6d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547882"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Hozzáférés kezelése SaaS alkalmazásokhoz egy csoport használatával

Ha Azure Active Directory (Azure AD) használatával prémium szintű Azure AD-licenccel rendelkezik, akkor a csoportok segítségével hozzáférést rendelhet az Azure AD-vel integrált SaaS-alkalmazásokhoz. Ha például öt különböző SaaS-alkalmazás használatára szeretné hozzárendelni a marketing részleghez való hozzáférést, létrehozhat egy csoportot, amely a marketing részleg felhasználóit tartalmazza, majd ezt a csoportot hozzárendelheti a marketing részleg által igényelt öt SaaS-alkalmazáshoz. Így időt takaríthat meg, ha a marketing részleg tagságát egy helyen kezeli. A felhasználók ezután az alkalmazáshoz lesznek rendelve, amikor a marketing csoport tagjaként hozzá lettek adva, és az alkalmazásból eltávolítják a hozzárendeléseiket a marketing csoportból. Ez a funkció több száz alkalmazással is használható, amelyeket hozzáadhat az Azure AD-alkalmazás-katalógusból.

> [!IMPORTANT]
> Ezt a funkciót csak prémium szintű Azure AD próbaverzió elindítása vagy prémium szintű Azure AD-licenc megvásárlása után használhatja.
> A csoport alapú hozzárendelés csak biztonsági csoportok esetén támogatott.
> A beágyazott csoporttagság az alkalmazásokhoz történő csoportalapú hozzárendeléseknél egyelőre nem támogatott.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Felhasználó vagy csoport hozzáférésének kiosztása SaaS-alkalmazáshoz

1. Az [Azure ad felügyeleti központban](https://aad.portal.azure.com)válassza a **vállalati alkalmazások** lehetőséget.
2. Válassza ki azt az alkalmazást, amelyet az alkalmazás-katalógusból adott hozzá a megnyitásához.
3. Válassza a **felhasználók és csoportok** lehetőséget, majd válassza a **felhasználó hozzáadása** elemet.
4. A **hozzárendelés hozzáadása** lapon válassza a **felhasználók és csoportok** lehetőséget a **felhasználók és csoportok** kiválasztási listájának megnyitásához.
6. Válassza ki a kívánt számú csoportot vagy felhasználót, majd kattintson vagy koppintson a **kiválasztás** lehetőségre a **hozzárendelés hozzáadása** listához való hozzáadáshoz. Ezen a ponton a felhasználóhoz is hozzárendelhet szerepkört.
7. Válassza a **hozzárendelés** lehetőséget a felhasználók vagy csoportok kiválasztott vállalati alkalmazáshoz való hozzárendeléséhez.

## <a name="next-steps"></a>Következő lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../enterprise-users/groups-settings-cmdlets.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
