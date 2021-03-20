---
title: Azure-beli virtuális WAN frissítése alapszintről standardra – Azure Portal | Microsoft Docs
description: A virtuális WAN-típust a nagyobb funkcionalitáshoz is frissítheti.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91447355"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Virtuális WAN frissítése alapszintről standard verzióra

Ebből a cikkből megtudhatja, hogyan frissíthet egy alapszintű WAN-t egy standard WAN-ra. Az "alapszintű" WAN-típus az alapszintű SKU-hubokként hozza létre az összes csomópontot. Alapszintű központban csak a helyek közötti VPN-funkciókra korlátozódik. A "standard" WAN-típus a benne található összes hubokat szabványos SKU-hubokként hozza létre. A standard szintű hubok használatakor engedélyezheti a ExpressRoute, a felhasználó (pont – hely) VPN-t, a teljes szembőségű hubot és a VNet-VNet továbbítást az Azure-hubokon keresztül.

A következő táblázat az egyes WAN-típusok számára elérhető konfigurációkat mutatja be:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>A virtuális WAN típusának módosítása

1. A virtuális WAN lapon válassza a **konfiguráció** elemet a konfigurációs lap megnyitásához.

   ![Képernyőkép, amely a "Configuration" (konfiguráció) lapot jeleníti meg egy információs szövegmezővel, amely az alján található, szabványos típusú virtuális WAN-ra való frissítésre mutat.](./media/upgrade-virtual-wan/1.png)
2. A virtuális WAN típusnál válassza a **standard** lehetőséget a legördülő menüből.

   ![A "virtuális WAN-típus" legördülő menüt bemutató képernyőkép.](./media/upgrade-virtual-wan/2.png)
3. Ismerje meg, hogy ha standard virtuális WAN-ra frissít, nem térhet vissza alapszintű virtuális WAN-ra. Válassza a **megerősítés** lehetőséget, ha frissíteni szeretné.

   ![A "frissítés megerősítése" párbeszédpanelt megjelenítő képernyőkép.](./media/upgrade-virtual-wan/4.png)
4. A módosítás mentése után a virtuális WAN-oldal ehhez a példához hasonlóan néz ki.

   ![Virtuális WAN ábrája](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.