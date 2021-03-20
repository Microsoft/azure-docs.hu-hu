---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 134f1dc7cb6e53c181b2f518055e5cb758fccf31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812719"
---
1. A virtuális WAN területen válassza a hubok lehetőséget, és válassza az **+ új hub** elemet.

   :::image type="content" source="media/virtual-wan-p2s-hub/new-hub.jpg" alt-text="új központ":::

1. A virtuális központ létrehozása lapon töltse ki a következő mezőket.

   * **Régió** – válassza ki azt a régiót, amelyben a virtuális hubot telepíteni szeretné.
   * **Név** – adja meg a virtuális központ meghívásához használni kívánt nevet.
   * **Hub magánhálózati címtartomány** – a központ CIDR jelölése.

   :::image type="content" source="media/virtual-wan-p2s-hub/create-hub.jpg" alt-text="virtuális központ létrehozása":::

1. A pont – hely lapon hajtsa végre a következő mezőket:

   * **Átjáró skálázási egységei** – ez a felhasználói VPN-átjáró összesített kapacitását jelenti.
   * **Mutasson a hely konfigurációja** lehetőségre, amelyet az előző lépésben hozott létre.
   * **Ügyféloldali címkészlet** – a távoli felhasználók számára.
   * **Egyéni DNS-kiszolgáló IP-címe**.

   :::image type="content" source="media/virtual-wan-p2s-hub/hub-with-p2s.png" alt-text="központ pont – hely kapcsolattal":::

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Az **átadott érvényesítés** lapon válassza a **Létrehozás** lehetőséget.