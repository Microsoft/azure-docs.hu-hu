---
title: Azure AD szerepkör-definíciók listázása – Azure AD
description: Ismerje meg, hogyan listázhatja az Azure beépített és egyéni szerepköreit.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467831"
---
# <a name="list-azure-ad-role-definitions"></a>Azure AD szerepkör-definíciók listázása

A szerepkör-definíció az engedélyek olyan gyűjteménye, amely elvégezhető, például olvasás, írás és törlés. Általában csak szerepkörnek nevezik. Azure Active Directory több mint 60 beépített szerepkörrel rendelkezik, vagy létrehozhat saját egyéni szerepköröket is. Ha már tudja, hogy "mi a teendő ezek a szerepkörök?", megtekintheti az egyes szerepkörök engedélyeinek részletes listáját.

Ez a cikk bemutatja, hogyan listázhatja az Azure AD beépített és egyéni szerepköreit, valamint azok engedélyeit.

## <a name="list-all-roles"></a>Az összes szerepkör listázása

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) , és válassza a **Azure Active Directory** lehetőséget.

1. Válassza a **szerepkörök és rendszergazdák** lehetőséget az összes elérhető szerepkör listájának megtekintéséhez.

    ![szerepkörök listája az Azure AD-portálon](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. A jobb oldalon kattintson a három pontra, majd a **leírásra** a szerepkör engedélyeinek teljes listájának megtekintéséhez.

    A lap a kapcsolódó dokumentációra mutató hivatkozásokat tartalmaz, amelyek segítenek a szerepkörök felügyeletében.

    ![Képernyőkép, amely a "globális rendszergazda – Leírás" lapot jeleníti meg.](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>Következő lépések

* Nyugodtan ossza meg velünk az [Azure ad rendszergazdai szerepkörökkel foglalkozó fórumát](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* A szerepkörökkel és a rendszergazdai szerepkör-hozzárendeléssel kapcsolatos további információkért lásd: [rendszergazdai szerepkörök hozzárendelése](permissions-reference.md).
* Az alapértelmezett felhasználói engedélyek összehasonlítását lásd: a [vendég és a tag alapértelmezett felhasználói engedélyeinek összehasonlítása](../fundamentals/users-default-permissions.md).
