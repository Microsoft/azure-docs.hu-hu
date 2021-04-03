---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3507aacc68de25f7368cbe3cda917077564c56eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356244"
---
1. Lépjen az **erőforráscsoportok** elemre, és válassza ki azt az erőforráscsoportot, amelyet a társítási **erőforrás** létrehozásakor kiválasztott. Ha túl sok erőforráscsoportot használ, használja a **szűrő** mezőt.

    > [!div class="mx-imgBorder"]
    > ![Erőforráscsoportok](../media/setup-direct-get-resourcegroup.png)

1. Válassza ki a létrehozott **egyenrangú** erőforrást.

    > [!div class="mx-imgBorder"]
    > ![Az áttekintő lap a bal oldali panelen van kiválasztva. A PeeringResourceGroup kapcsolatos információkat jeleníti meg. A peering (társítás) listában a AshburnPeering ki van emelve.](../media/setup-direct-get-open.png)

1. Az **Áttekintés** oldalon az itt látható magas szintű információk láthatók.

    > [!div class="mx-imgBorder"]
    > ![Peering erőforrás – áttekintés panel](../media/setup-direct-get-overview.png)

1. A bal oldalon válassza az **ASN-adatok** lehetőséget a PeerAsn létrehozásakor elküldött információk megtekintéséhez.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú erőforrás ASN-információi](../media/setup-direct-get-asninfo.png)

1. A bal oldalon válassza a **kapcsolatok** lehetőséget. A képernyő felső részén megjelenik az ASN és a Microsoft közötti kapcsolati kapcsolatok összefoglalása, a Metron belüli különböző létesítmények között. A kapcsolatok összegzését az **Áttekintés** lapról is elérheti, ha a panel közepén található **kapcsolatok** lehetőségre kattint, ahogy az látható.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú erőforrás-kapcsolatok](../media/setup-direct-get-connectionssummary.png)

    * A **kapcsolódási állapot** megfelel a társ-összekapcsolási beállítás állapotának. Az ebben a mezőben megjelenített állapotok a közvetlen társítási [útmutatóban](../walkthrough-direct-all.md)látható állapotjelző diagramot követik.
    * Az **IPv4-munkamenet állapota** és az **IPv6-munkamenet állapota** az IPv4-és IPv6 BGP-munkamenet-állapotoknak felel meg. 
    * Amikor kiválaszt egy sort a képernyő felső részén, a lenti **kapcsolat** szakasz az egyes kapcsolatok részleteit jeleníti meg. A nyilak segítségével bontsa ki a **konfiguráció**, az **IPv4-cím** és az **IPv6-cím** elemet.
