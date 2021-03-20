---
title: Engedélyek Azure Advisor
description: Az Advisor engedélyei, valamint az, hogy miként lehet letiltani az előfizetések konfigurálását, illetve elhalasztani vagy elvetheti az ajánlásokat.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91712922"
---
# <a name="permissions-in-azure-advisor"></a>Engedélyek Azure Advisor

A Azure Advisor az Azure-erőforrások és-előfizetések használata és konfigurációja alapján nyújt javaslatokat. Az Advisor az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md) által biztosított [beépített szerepköröket](../role-based-access-control/built-in-roles.md) használja a javaslatok és az Advisor funkcióinak hozzáférésének kezeléséhez. 

## <a name="roles-and-their-access"></a>Szerepkörök és a hozzájuk tartozó hozzáférés

A következő táblázat az Advisoron belüli szerepköröket és hozzáféréseket határozza meg:

| **Szerepkör** | **Javaslatok megtekintése** | **Szabályok szerkesztése** | **Előfizetés konfigurációjának szerkesztése** | **Erőforráscsoport konfigurációjának szerkesztése**| **Javaslatok bezárása és elhalasztása**|
|---|:---:|:---:|:---:|:---:|:---:|
|Előfizetés tulajdonosa|**X**|**X**|**X**|**X**|**X**|
|Előfizetés közreműködői|**X**|**X**|**X**|**X**|**X**|
|Előfizetés-olvasó|**X**|--|--|--|--|
|Erőforráscsoport tulajdonosa|**X**|--|--|**X**|**X**|
|Erőforráscsoport közreműködői|**X**|--|--|**X**|**X**|
|Erőforráscsoport-olvasó|**X**|--|--|--|--|
|Erőforrás tulajdonosa|**X**|--|--|--|**X**|
|Erőforrás-közreműködő|**X**|--|--|--|**X**|
|Erőforrás-olvasó|**X**|--|--|--|--|

> [!NOTE]
> A javaslatok megtekintésére való hozzáférés attól függ, hogy milyen hatással van az ajánlás érintett erőforrására.

## <a name="permissions-and-unavailable-actions"></a>Engedélyek és nem elérhető műveletek

A megfelelő engedélyek hiánya letilthatja a műveletek végrehajtását az Advisorban. Az alábbiakban néhány gyakori problémát ismertetünk.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nem lehet konfigurálni az előfizetéseket vagy az erőforráscsoportokat

Ha előfizetéseket vagy erőforráscsoportokat próbál konfigurálni az Advisorban, láthatja, hogy a Belefoglalás vagy a kizárás lehetőség le van tiltva. Ez az állapot azt jelzi, hogy nem rendelkezik megfelelő szintű engedéllyel az adott erőforráscsoporthoz vagy előfizetéshez. A probléma megoldásához tekintse át [a felhasználói hozzáférés megadásának](../role-based-access-control/quickstart-assign-role-user-portal.md)módját.

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nem lehet elhalasztani vagy elvetni egy javaslatot

Ha a javaslat elhalasztása vagy elutasítása során hibaüzenetet kap, előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel. Győződjön meg arról, hogy legalább közreműködői hozzáférése van az elhalasztott vagy elutasított javaslat érintett erőforrásához. A probléma megoldásához tekintse át [a felhasználói hozzáférés megadásának](../role-based-access-control/quickstart-assign-role-user-portal.md)módját.

## <a name="next-steps"></a>Következő lépések

Ez a cikk áttekintést nyújt arról, hogy az Advisor hogyan használja az Azure RBAC a felhasználói engedélyek vezérlésére és a gyakori problémák megoldására. Az Advisor szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következőt:

- [Mi az Azure Advisor?](./advisor-overview.md)
- [Ismerkedés az Azure Advisor szolgáltatással](./advisor-get-started.md)
