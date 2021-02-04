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
ms.openlocfilehash: 31cfb321a60e700aecfdc1288ccdd379b89658ba
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531904"
---
1. Keresse meg a létrehozott virtuális WAN-t. A virtuális WAN lapon a **kapcsolat** szakaszban válassza a **hubok** lehetőséget.
2. A hubok lapon válassza az **+ új hub** lehetőséget a **virtuális központ létrehozása** lap megnyitásához.

    ![A képernyőképen a virtuális központ létrehozása panel jelenik meg, ahol az alapok lap van kiválasztva.](./media/virtual-wan-tutorial-hub-include/basics.png "Alapbeállítások")
3. A **virtuális központ létrehozása** lap **alapjai** lapon végezze el a következő mezőket:

    **Projekt részletei**

   * Régió (korábbi nevén hely)
   * Name
   * Hub magánhálózati címtartomány. A minimális címtartomány/24 a hub létrehozásához, ami azt jelenti, hogy a/25-től/32-ig terjedő minden tartomány hibát eredményez a létrehozás során. Azt javasoljuk azonban, hogy egy/23 címtartomány vagy nagyobb kapacitás kiosztásával biztosítsa, hogy elegendő IP-cím álljon rendelkezésre a virtuális központban üzembe helyezhető jövőbeli szolgáltatások támogatásához. Az Azure Virtual WAN a Microsoft által felügyelt szolgáltatás, amely a különböző átjárók/szolgáltatások (például VPN-átjárók, ExpressRoute-átjárók, felhasználói VPN/pont – hely átjárók, tűzfal, útválasztás stb.) esetében létrehozza a megfelelő alhálózatokat a virtuális központban. Nincs szükség arra, hogy a felhasználó explicit módon tervezze meg a virtuális központ szolgáltatásainak alhálózati címterület-területét, mivel a Microsoft ezt a szolgáltatást részeként kezeli.
4. Válassza **a Next (tovább) lehetőséget: helyek közötti** kapcsolat.

    ![A képernyőképen a virtuális központ létrehozása panel jelenik meg, ahol a hely és hely van kiválasztva.](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Helyek közötti kapcsolat")

5. A **helyek közötti** lapon hajtsa végre a következő mezőket:

   * A helyek közötti VPN létrehozásához válassza az **Igen** lehetőséget.
   * A AS Number mező jelenleg nem szerkeszthető a virtuális központban.
   * Válassza ki az **átjáró méretezési egységének** értékét a legördülő listából. A skálázási egység lehetővé teszi, hogy kiválassza a virtuális központban létrehozott VPN-átjáró összesített átviteli sebességét, hogy a helyekhez kapcsolódjon. Ha 1 skálázási egység = 500 Mbps-t választ, azt jelenti, hogy a redundancia két példánya jön létre, amelyek mindegyike 500 Mbps maximális átviteli sebességgel rendelkezik. Ha például öt ág van, amelyek mindegyike 10 Mbps-t használ a fiókirodában, akkor a fő végponton 50 Mbps összesített értékre van szükség. Az Azure VPN Gateway összesített kapacitásának megtervezését a hub-ágak számának támogatásához szükséges kapacitás kiértékelése után kell elvégezni.
6. Válassza a **felülvizsgálat + létrehozás** elemet az ellenőrzéshez.
7. Válassza a **Létrehozás** lehetőséget a központ létrehozásához. 30 perc elteltével a **frissítés** gombra kattintva megtekintheti a hubot a **hubok** oldalon. Az erőforráshoz való **ugráshoz válassza az erőforrás keresése** lehetőséget.
