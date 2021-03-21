---
title: Szerepköralapú hozzáférés biztosítása a felhasználók számára
titleSuffix: Azure Maps
description: Szerepköralapú hozzáférés-vezérlés használata a felhasználók engedélyezésének megadásához Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101102818"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Szerepköralapú hozzáférés biztosítása a felhasználók számára a Azure Maps

Azure *szerepköralapú hozzáférés-vezérlést (Azure RBAC)* biztosít egy Azure ad-csoport vagy rendszerbiztonsági tag egy vagy több Azure Maps szerepkör-definícióhoz való hozzárendelésével. Az Azure Maps számára elérhető Azure-szerepkör-definíciók megtekintéséhez lépjen a **hozzáférés-vezérlés (iam)** elemre. Válassza ki a **szerepkörök** elemet, majd keressen rá a *Azure Maps* kezdetű szerepkörökre.

* Az [Azure ad-csoportokkal](../../active-directory/fundamentals/active-directory-manage-groups.md)hatékonyan kezelhet nagy mennyiségű felhasználói hozzáférést Azure Mapshoz.
* Ahhoz, hogy a felhasználók hitelesíteni tudják az alkalmazást, a felhasználókat létre kell hozni az Azure AD-ben. Lásd: [felhasználók hozzáadása vagy törlése az Azure ad-](../../active-directory/fundamentals/add-users-azure-active-directory.md)vel.

További információk az [Azure ad](../../active-directory/fundamentals/index.yml) -ben a felhasználók címtárának hatékony kezeléséhez.

1. Nyissa meg **Azure Maps-fiókját**. Válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelés** elemet.

    ![Hozzáférés biztosítása az Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. A **szerepkör-hozzárendelések** lap **szerepkör** területén válassza ki a beépített Azure Maps szerepkör-definíciót, például **Azure Maps Adatolvasó** vagy **Azure Maps adatközreműködő**. A **hozzáférés társítása** területen válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** lehetőséget. Válassza ki a résztvevőt név alapján. Kattintson a **Mentés** gombra.

   * Tekintse meg az [Azure-szerepkörök hozzárendelésének](../../role-based-access-control/role-assignments-portal.md)részleteit.

> [!WARNING]
> Azure Maps beépített szerepkör-definíciók nagyon nagy engedélyezési hozzáférést biztosítanak számos Azure Maps REST API-hoz. Ha minimálisra szeretné korlátozni az API-kat, tekintse meg az [Egyéni szerepkör-definíció létrehozása és a felhasználók társítása](../../role-based-access-control/custom-roles.md) az egyéni szerepkör-definícióhoz című témakört. Ez lehetővé teszi a felhasználók számára, hogy az alkalmazáshoz szükséges legalacsonyabb jogosultsággal rendelkezzenek.