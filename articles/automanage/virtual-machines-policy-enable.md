---
title: Azure Policy használatával engedélyezheti a virtuális gépek autofelügyeletét
description: Megtudhatja, hogyan engedélyezheti a virtuális gépek Azure-beli autofelügyeletét a Azure Portal beépített Azure Policy.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91714903"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Azure Policy használatával engedélyezheti a virtuális gépek autofelügyeletét

Ha engedélyezni szeretné a sok virtuális gép autofelügyeletét, ezt egy beépített [Azure Policy](..\governance\azure-management.md)használatával teheti meg. Ez a cikk végigvezeti a megfelelő házirend megkeresésének és a hozzárendelésének módján, hogy az automatikusan kezelhesse a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

> [!IMPORTANT]
> A következő Azure RBAC engedélyre van szükség az automanage engedélyezéséhez: **tulajdonosi** szerepkör vagy **közreműködő** a **felhasználói hozzáférés rendszergazdai** szerepköreivel együtt.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>A szabályzat megkeresése és kiosztása

1. Navigáljon a **házirendhez** a Azure Portal
1. Ugrás a **definíciók** panelre
1. Kattintson a **Kategóriák** legördülő listára az elérhető lehetőségek megtekintéséhez
1. Jelölje be az **automanage engedélyezése – Azure virtuális gépek ajánlott eljárásai** lehetőséget
1. Most a lista frissülni fog egy beépített szabályzattal, amelynek a neve az *automanage engedélyezése...*
1. Kattintson az *automanage engedélyezése – Azure virtuális gépek ajánlott eljárások* beépített házirend neve elemre.
1. A házirendre való kattintás után a **definíció** lapot láthatja

    > [!NOTE]
    > A Azure Policy definíciója az automatikus kezelés paramétereinek beállítására szolgál, például a konfigurációs profilhoz vagy a fiókhoz. Emellett olyan szűrőket is beállít, amelyek biztosítják, hogy a házirend csak a megfelelő virtuális gépekre vonatkozzon.

1. Hozzárendelés létrehozásához kattintson a **hozzárendelés** gombra.
1. Az **alapok** lapon töltse ki a **hatókört** az *előfizetés* és az *erőforráscsoport* beállításával.

    > [!NOTE]
    > A hatókör segítségével meghatározhatja, hogy a szabályzat mely virtuális gépekre vonatkozzon. Az alkalmazást az előfizetés szintjén vagy az erőforráscsoport szintjén is beállíthatja. Ha beállít egy erőforráscsoportot, az összes olyan virtuális gép, amely jelenleg az adott erőforráscsoporthoz tartozik, vagy a hozzá hozzáadott bármely jövőbeli virtuális gép automatikusan engedélyezve lesz az automatikus felügyelettel. 

1. Kattintson a **Parameters (paraméterek** ) lapra, és állítsa be az automatikus **felügyelet fiókot** és a kívánt **konfigurációs profilt** . 
1. A **felülvizsgálat + létrehozás** lapon tekintse át a beállításokat.
1. A hozzárendelés alkalmazásához kattintson a **Létrehozás** gombra.
1. A hozzárendelések megtekintése a **hozzárendelések** lapon a **definíció** mellett

> [!NOTE]
> Némi időbe telik, amíg a szabályzat megkezdi az aktuálisan az erőforráscsoporthoz vagy az előfizetésbe tartozó virtuális gépek érvénybe léptetését.


## <a name="next-steps"></a>Következő lépések 

Ismerje meg, hogyan engedélyezheti a virtuális gépek Azure-beli autofelügyeletét a Azure Portalon keresztül. 

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)