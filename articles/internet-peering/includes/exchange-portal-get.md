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
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92548212"
---
Miután a  társítási erőforrás üzembe helyezése sikeres volt, a következő lépésekkel tekintheti meg.

1. Lépjen az **erőforráscsoportok** elemre, és válassza ki azt az erőforráscsoportot, amelyet a társítási **erőforrás** létrehozásakor kiválasztott. Ha túl sok erőforráscsoportot használ, használja a **szűrő** mezőt.

    > [!div class="mx-imgBorder"]
    > ![Erőforráscsoportok](../media/setup-direct-get-resourcegroup.png)

1. Válassza ki a létrehozott **egyenrangú** erőforrást.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép, amely a létrehozott egyenrangú erőforrást jeleníti meg.](../media/setup-direct-get-open.png)

1. Az **Áttekintés** oldalon az itt látható magas szintű információk láthatók.

    > [!div class="mx-imgBorder"]
    > ![Peering erőforrás – áttekintés panel](../media/setup-exchange-get-overview.png)

1. A bal oldalon válassza az **ASN-adatok** lehetőséget a PeerAsn létrehozásakor elküldött információk megtekintéséhez.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú erőforrás ASN-információi](../media/setup-direct-get-asninfo.png)

1. A bal oldalon válassza a **kapcsolatok** lehetőséget. A képernyő felső részén megjelenik az ASN és a Microsoft közötti kapcsolati kapcsolatok összefoglalása, a Metron belüli különböző létesítmények között. A kapcsolatok összegzését az **Áttekintés** lapról is elérheti a középső ablaktáblán látható **kapcsolatok** lehetőség kiválasztásával.

    > [!div class="mx-imgBorder"]
    > ![Egyenrangú erőforrás-kapcsolatok](../media/setup-exchange-get-connectionssummary.png)

    * A **kapcsolódási állapot** megfelel a társ-összekapcsolási beállítás állapotának. Az ebben a mezőben megjelenített állapotok az Exchange-társítási [útmutatóban](../walkthrough-exchange-all.md)látható állapotjelző diagramot követik.
    * Az **IPv4-munkamenet állapota** és az **IPv6-munkamenet állapota** az IPv4-és IPv6 BGP-munkamenet-állapotoknak felel meg.  
    * Amikor kiválaszt egy sort a képernyő felső részén, a lenti **kapcsolat** szakasz az egyes kapcsolatok részleteit jeleníti meg. A nyilak segítségével bontsa ki a **konfiguráció**, az **IPv4-cím** és az **IPv6-cím** elemet.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép, amely kiemel egy szakaszt kibontó nyilat.](../media/setup-exchange-get-connectionsipv4.png)
