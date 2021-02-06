---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f469664c716ecef6b82de2befa40b33f253e229f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627706"
---
1. Keresse meg a létrehozott virtuális WAN-t. A virtuális WAN lapon a **kapcsolat** szakaszban válassza a **hubok** lehetőséget.
2. A **hubok** lapon válassza az **+ új hub** lehetőséget a **virtuális központ létrehozása** lap megnyitásához.

    ![A képernyőképen a virtuális központ létrehozása panel jelenik meg, ahol az alapok lap van kiválasztva.](./media/virtual-wan-tutorial-hub-include/basics.png "Alapbeállítások")
3. A **virtuális központ létrehozása** lap **alapjai** lapon végezze el a következő mezőket:

   * **Régió** (korábbi nevén hely)
   * **Név**
   * **Hub saját címtartomány** – a minimális címtartomány/24 a hub létrehozásához. Ha a/25 és/32 közötti tartományban bármit használ, a létrehozás során hibát fog eredményezni. Nem kell explicit módon megterveznie a szolgáltatásokhoz tartozó alhálózati címtartományt a virtuális központban. Mivel az Azure Virtual WAN felügyelt szolgáltatás, létrehozza a megfelelő alhálózatokat a virtuális központban a különböző átjárók/szolgáltatások számára (például VPN-átjárók, ExpressRoute-átjárók, felhasználói VPN pont – hely átjárók, tűzfal, útválasztás stb.).
4. Válassza **a Next (tovább) lehetőséget: helyek közötti** kapcsolat.

    ![A képernyőképen a virtuális központ létrehozása panel jelenik meg, ahol a hely és hely van kiválasztva.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Helyek közötti kapcsolat")

5. A **helyek közötti** lapon hajtsa végre a következő mezőket:

   * A helyek közötti VPN létrehozásához válassza az **Igen** lehetőséget.
   * Az AS Number mező nem szerkeszthető.
   * Válassza ki az **átjáró méretezési egységének** értékét a legördülő listából. A skálázási egység lehetővé teszi, hogy kiválassza a virtuális központban létrehozott VPN-átjáró összesített átviteli sebességét, hogy a helyekhez kapcsolódjon. Ha 1 skálázási egység = 500 Mbps-t választ, azt jelenti, hogy a redundancia két példánya jön létre, amelyek mindegyike 500 Mbps maximális átviteli sebességgel rendelkezik. Ha például öt ág van, amelyek mindegyike 10 Mbps-t használ a fiókirodában, akkor a fő végponton 50 Mbps összesített értékre van szükség. Az Azure VPN Gateway összesített kapacitásának megtervezését a hub-ágak számának támogatásához szükséges kapacitás kiértékelése után kell elvégezni.
6. Válassza a **felülvizsgálat + létrehozás** elemet az ellenőrzéshez.
7. Válassza a **Létrehozás** lehetőséget a központ létrehozásához. 30 perc elteltével a **frissítés** gombra kattintva megtekintheti a hubot a **hubok** oldalon. Az erőforráshoz való **ugráshoz válassza az erőforrás keresése** lehetőséget.
