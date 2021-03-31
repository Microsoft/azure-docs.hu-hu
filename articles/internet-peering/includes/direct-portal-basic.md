---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83846148"
---
1. Válassza az **erőforrás létrehozása** az  >  **összes** megjelenítése lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Keresés a társításban](../media/setup-seeall.png)

1. Keressen rá a **Keresés a keresőmezőbe** , és válassza az **ENTER billentyűt** a billentyűzeten. Az eredmények közül válasszon ki egy **egyenrangú** erőforrást.

    > [!div class="mx-imgBorder"]
    > ![Peering elindítása](../media/setup-launch.png)

1. A **társítás elindítása után** tekintse át a lapot a részletek megismeréséhez. Ha elkészült, válassza a **Létrehozás** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Peering létrehozása](../media/setup-create.png)

1. A társítás **létrehozása** oldalon az **alapok** lapon adja meg az itt látható mezőket.

    > [!div class="mx-imgBorder"]
    > ![A peering alapjai lap](../media/setup-basics-tab.png)

    * Válassza ki az Azure- **előfizetését**.
    * Az **erőforráscsoport** esetében választhat egy meglévő erőforráscsoportot a legördülő listából, vagy létrehozhat egy új csoportot az **új létrehozása** lehetőség kiválasztásával. Ehhez a példához hozzunk létre egy új erőforráscsoportot.
    * A **név** megegyezik az erőforrás nevével, és bármi lehet, amit választ.
    * Ha meglévő erőforráscsoportot választott, akkor a **régiót** a rendszer kijelöli. Ha úgy döntött, hogy új erőforráscsoportot hoz létre, ki kell választania azt az Azure-régiót, ahol az erőforrást tárolni szeretné.

        > [!NOTE]
        > Az a régió, ahol az erőforráscsoport található, független attól a helytől, ahol a Microsofttal kívánja létrehozni a kapcsolatot. Az ajánlott eljárás azonban az, hogy a legközelebb eső Azure-régiókban található erőforráscsoportok között szervezzen egyenrangú erőforrásokat. Például a Ashburn-ben való társítások esetén létrehozhat egy erőforráscsoportot az USA keleti régiójában vagy a Kelet-RÉGIÓJA.

    * Válassza ki az ASN-t a **társ ASN** mezőben.

        > [!IMPORTANT]
        > * A rendszer csak a jóváhagyott ValidationState rendelkező ASN-ket választhatja, mielőtt elküld egy kérést. Ha most elküldte a PeerAsn kérelmet, várjon 12 órát, vagy ha az ASN-társítást jóvá szeretné hagyni. Ha a kiválasztott ASN-hitelesítés függőben van, hibaüzenet jelenik meg. 
        > * Ha nem látja a kiválasztható ASN-t, ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva. Ha igen, ellenőrizze, hogy már létrehozott-e PeerAsn az [Azure-előfizetéshez való társítási peer ASN](../howto-subscription-association-portal.md)használatával.

        > [!div class="mx-imgBorder"]
        > ![A peering alapjai kitöltve](../media/setup-direct-basics-filled-tab.png)

    * A folytatáshoz válassza a **következő lehetőséget: Configuration >** .
