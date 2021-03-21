---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "73491568"
---
1. Keresse meg a létrehozott virtuális WAN-t. A virtuális WAN lapon a **kapcsolat** szakaszban válassza a **hubok** lehetőséget.
2. A hubok lapon válassza az **+ új hub** lehetőséget a **virtuális központ létrehozása** lap megnyitásához.
3. A **virtuális központ létrehozása** lap **alapjai** lapon végezze el a következő mezőket:

   ![Alapvető beállítások](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Alapvető beállítások")

    **Projekt részletei**

   * Régió (korábbi nevén hely)
   * Name
   * Hub magánhálózati címtartomány. A minimális címtartomány/24 a hub létrehozásához, ami azt jelenti, hogy a/25-től/32-ig terjedő minden tartomány hibát eredményez a létrehozás során.
4. Válassza a **ExpressRoute lapot**.

5. A **ExpressRoute** lapon végezze el a következő mezőket:

   ![ExpressRoute](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Válassza az **Igen** lehetőséget egy **ExpressRoute** -átjáró létrehozásához.
   * Válassza ki az **átjáró méretezési egységének** értékét a legördülő listából.
6. Válassza a **felülvizsgálat + létrehozás** elemet az ellenőrzéshez.
7. Válassza a **Létrehozás** lehetőséget a központ létrehozásához. 30 perc elteltével a **frissítés** gombra kattintva megtekintheti a hubot a **hubok** oldalon. Az erőforráshoz való **ugráshoz válassza az erőforrás keresése** lehetőséget.