---
title: 'Virtuális WAN: virtuális központ útválasztási táblázatának létrehozása a NVA: Azure Portal'
description: Virtuális WAN virtuális központ útválasztási táblázata, amely a portál használatával irányítja át a forgalmat egy hálózati virtuális berendezésre.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267142"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Hozzon létre egy virtuális WAN hub útválasztási táblázatot a NVA: Azure Portal

Ebből a cikkből megtudhatja, hogyan irányíthatja át a forgalmat egy olyan ág (helyszíni hely) felé, amely a virtuális WAN-hubhoz csatlakozik egy küllős virtuális hálózaton (VNet) keresztül egy hálózati virtuális berendezésen (NVA) keresztül.

![Virtuális WAN ábrája](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy teljesültek-e az alábbi feltételek:

*  Rendelkezik egy hálózati virtuális berendezéssel (NVA). A hálózati virtuális berendezés egy olyan külső gyártótól származó szoftver, amelyet általában az Azure Marketplace-en kell kiépíteni egy virtuális hálózaton.

    * A NVA hálózati adapterhez hozzá kell rendelni egy magánhálózati IP-címet.

    * A NVA nincs telepítve a virtuális központban. Ezt külön virtuális hálózatban kell telepíteni.

    *  Előfordulhat, hogy a NVA virtuális hálózat egy vagy több virtuális hálózattal van csatlakoztatva. Ebben a cikkben a NVA virtuális hálózatot "közvetett küllős VNet"-ként tekintjük át. Ezek a virtuális hálózatok a VNet-társítás használatával csatlakoztathatók a NVA VNet. A VNet-társítási hivatkozásokat fekete nyilak ábrázolják a fenti ábrán a VNet 1, VNet 2 és NVA VNet között.
*  Két virtuális hálózatot hozott létre. A rendszer küllős virtuális hálózatok fogja használni őket.

    * A VNet küllős címei a következők: VNet1:10.0.2.0/24 és VNet2:10.0.3.0/24. Ha a virtuális hálózat létrehozásával kapcsolatos információkra van szüksége, tekintse meg [a virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)című témakört.

    * Győződjön meg arról, hogy nincsenek virtuális hálózati átjárók a virtuális hálózatok egyikében sem.

    * A virtuális hálózatok nem igényel átjáró-alhálózatot.

## <a name="1-sign-in"></a><a name="signin"></a>1. bejelentkezés

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. virtuális WAN létrehozása

Hozzon létre egy virtuális WAN-t. Használja a következő példában szereplő értékeket:

* **Virtuális WAN neve:** myVirtualWAN
* **Erőforráscsoport:** testRG
* **Hely:** USA nyugati régiója

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. hub létrehozása

Hozza létre a hubot. Használja a következő példában szereplő értékeket:

* **Hely:** USA nyugati régiója
* **Név:** westushub
* **Központi magánhálózati címtartomány:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. hub-útválasztási táblázat létrehozása és alkalmazása

Frissítse a hubot egy hub útválasztási táblázattal. Használja a következő példában szereplő értékeket:

* **Küllős VNet:** (VNet1 és VNet2) 10.0.2.0/24 és 10.0.3.0/24
* **DMZ NVA hálózati adapter magánhálózati IP-címe:** 10.0.4.5

1. Navigáljon a virtuális WAN-ra.
2. Kattintson arra a hubhoz, amelyhez útválasztási táblázatot kíván létrehozni.
3. Kattintson a **...**, majd a **virtuális központ szerkesztése** elemre.
4. A **virtuális központ szerkesztése** lapon görgessen le, és jelölje be a **tábla használata az útválasztáshoz** jelölőnégyzetet.
5. A **Ha cél előtagja** oszlopban adja hozzá a Címterület mezőt. A **Küldés a következő ugrásra** oszlopba adja hozzá a DMZ NVA hálózati adapter magánhálózati IP-címét.

   > [!NOTE]
   > A DMZ NVA-hálózat a helyi hubhoz érvényes.
   
6. Kattintson a **Confirm (megerősítés** ) gombra a központ erőforrásának az útválasztási táblázat beállításaival való frissítéséhez.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. hozza létre az VNet-kapcsolatokat

Hozzon létre egy virtuális hálózati kapcsolatokat az egyes közvetett küllős VNet (VNet1 és VNet2) a hubhoz. Ezek a virtuális hálózati kapcsolatok a fenti ábrán látható kék nyilak szerint jelennek meg. Ezután hozzon létre egy VNet-kapcsolódást a NVA VNet az ábrán látható fekete nyílra.

 Ebben a lépésben a következő értékeket használhatja:

| Virtuális hálózat neve| Kapcsolat neve|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

Ismételje meg az alábbi eljárást minden olyan virtuális hálózat esetében, amelyhez csatlakozni szeretne.

1. A virtuális WAN lapján kattintson a **Virtuális hálózati kapcsolatok** elemre.
2. A virtuális hálózati kapcsolat lapján kattintson a **+Kapcsolat hozzáadása** elemre.
3. A **Kapcsolat hozzáadása** lapon töltse ki a következő mezőket:

    * **Kapcsolat neve** – Nevezze el a kapcsolatot.
    * **Elosztók** – Válassza ki azt az elosztót, amelyet a kapcsolattal társítani kíván.
    * **Előfizetés** – Ellenőrizze az előfizetést.
    * **Virtuális hálózat** – Válassza ki azt a virtuális hálózatot, amelyet az elosztóhoz csatlakoztatni kíván. A virtuális hálózat nem rendelkezhet már meglévő virtuális hálózati átjáróval.
4. A kapcsolódás létrehozásához kattintson **az OK** gombra.

## <a name="next-steps"></a>Következő lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.
