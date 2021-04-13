---
title: Virtuális gépek automatikus automatizálásának engedélyezése a Azure Policy
description: Megtudhatja, hogyan engedélyezheti Azure Automanage virtuális gépekhez a virtuális gépekhez a Azure Policy beépített Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365195"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Virtuális gépek automatikus automatizálásának engedélyezése a Azure Policy

Ha sok virtuális gép automatikus szolgáltatását szeretné engedélyezni, ezt egy beépített [Azure Policy.](..\governance\azure-management.md) Ez a cikk bemutatja, hogyan találja meg a megfelelő szabályzatot, és hogyan rendelheti hozzá az Automatikus kiosztás engedélyezéséhez a Azure Portal.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) létre egy fiókot.

> [!NOTE]
> Az ingyenes próbafiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Frissítsen egy fizetős előfizetésre.

> [!IMPORTANT]
> Az automatikus elérés engedélyezéséhez a következő Azure RBAC-engedélyre van szükség: **Tulajdonosi** szerepkör vagy **Közreműködő,** valamint felhasználói **hozzáférés-rendszergazdai szerepkörök.**

## <a name="direct-link-to-policy"></a>Szabályzatra mutató közvetlen hivatkozás
Az automatikus automatizációs szabályzatdefiníció a Azure Portal a Következő virtuális gépek konfigurálása a [Azure Automanage.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3) Ha erre a hivatkozásra kattint, ugorjon közvetlenül a 8. lépésre a szabályzat megkeresését és [hozzárendelését lásd](#locate-and-assign-the-policy) alább.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>A szabályzat megkeresve és hozzárendelve

1. Lépjen a **Szabályzat lapra** a Azure Portal
1. Ugrás a **Definíciók panelre**
1. Az elérhető **lehetőségeket** a Kategóriák legördülő menüben láthatja
1. Válassza az Automatikus automatizálás engedélyezése – Azure-beli virtuális **gépek – ajánlott eljárások lehetőséget**
1. A lista most frissül, hogy egy beépített szabályzatot mutasson az Automatikus alkalmazás *engedélyezése... névvel.*
1. Kattintson az Automatikus automatizálás engedélyezése – Azure-beli virtuális *gépek ajánlott eljárásai* beépített szabályzatnévre
1. Miután rákattintott a szabályzatra, használhatja a **Definíció** lapot

    > [!NOTE]
    > A Azure Policy a konfigurációs profilhoz vagy a fiókhoz hasonló automatikusmanage paraméterek beállítását használja. Szűrőket is beállítja, amelyek biztosítják, hogy a szabályzat csak a megfelelő virtuális gépekre vonatkozik.

1. Hozzárendelés **létrehozásához** kattintson a Hozzárendelés gombra
1. Az Alapvető **beállítások lapon** töltse ki a **Hatókört** az Előfizetés *és* az *Erőforráscsoport beállításával.*

    > [!NOTE]
    > A Hatókör beállítással meghatározhatja, hogy mely virtuális gépekre vonatkozik ez a szabályzat. Az alkalmazást az előfizetés vagy az erőforráscsoport szintjén állíthatja be. Ha beállít egy erőforráscsoportot, az adott erőforráscsoportban lévő összes virtuális gép vagy bármely további hozzáadt virtuális gép automatikus automatikusan engedélyezve lesz.

1. Kattintson a **Paraméterek lapra,** és állítsa be az **Automatikus fiók** és a kívánt konfigurációs profil **beállítását**
1. Az Áttekintés **+ létrehozás lapon** tekintse át a beállításokat
1. A Hozzárendelés alkalmazásához kattintson a Létrehozás **gombra.**
1. A hozzárendelések megtekintése a **Definíció melletti Hozzárendelések** **lapon**

> [!NOTE]
> A szabályzat effektusba fog esni az erőforráscsoportban vagy előfizetésben jelenleg lévő virtuális gépeken.


## <a name="next-steps"></a>Következő lépések

Ismerje meg a virtuális gépek Azure Automanage engedélyezésének egy másik módját a Azure Portal.

> [!div class="nextstepaction"]
> [A virtuális gépek automatikus Azure Portal](quick-create-virtual-machines-portal.md)