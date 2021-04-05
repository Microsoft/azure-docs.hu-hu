---
author: baanders
description: fájl belefoglalása az engedélyek előfeltételeihez az Azure digitális Twins telepítésekor
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 919cc934920d922a82ceb9e30630006627af44e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205486"
---
## <a name="prerequisites-permission-requirements"></a>Előfeltételek: engedélyezési követelmények

A cikkben szereplő összes lépés végrehajtásához rendelkeznie kell egy olyan [szerepkörrel az előfizetésben](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) , amely a következő engedélyekkel rendelkezik:
* Azure-erőforrások létrehozása és kezelése
* Az Azure-erőforrásokhoz való felhasználói hozzáférés kezelése (beleértve az engedélyek megadását és delegálását)

A követelménynek megfelelő általános szerepkörök a *tulajdonos*, a *fiók rendszergazdája* vagy a *felhasználói hozzáférés rendszergazdája* és a *közreműködő* kombinációja. A szerepkörök és engedélyek teljes körű ismertetését, beleértve a más szerepkörökhöz tartozó engedélyeket, a [*klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és az Azure ad-szerepköröket*](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) az Azure RBAC dokumentációjában találhatja meg.

Ha szeretné megtekinteni a szerepkörét az előfizetésében, látogasson el a Azure Portal [előfizetések oldalára](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) (ezt a hivatkozást használhatja, vagy keresse meg az *előfizetéseket* a portál keresősáv használatával). Keresse meg az Ön által használt előfizetés nevét, és tekintse meg a szerepkörét a *saját szerepkör* oszlopban:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="A Azure Portal előfizetések lapja, amely tulajdonosként jeleníti meg a felhasználót" lightbox="../articles/digital-twins/media/how-to-set-up-instance/portal/subscriptions-role.png":::

Ha úgy találja, hogy az érték *közreműködő*, vagy egy másik olyan szerepkör, amely nem rendelkezik a fentiekben leírt engedélyekkel, felveheti a kapcsolatot a felhasználóval az előfizetésében *, amely rendelkezik* ezekkel az engedélyekkel (például egy előfizetés tulajdonosával vagy a fiók rendszergazdájával), és az alábbi módokon végezheti el a műveletet:
* Kérje meg, hogy végezze el a jelen cikkben szereplő lépéseket az Ön nevében
* Kérje meg, hogy emelje a szerepkörét az előfizetésben, így Ön is jogosult lesz a folytatásra. Attól függően, hogy ez megfelelő-e, a szervezettől és a benne lévő szerepkörtől függ.