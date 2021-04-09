---
title: 'Gyors útmutató: útvonal-kiszolgáló létrehozása és konfigurálása a Azure Portal használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és konfigurálhat útválasztási kiszolgálókat a Azure Portal használatával.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547992"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Gyors útmutató: útvonal-kiszolgáló létrehozása és konfigurálása a Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure Route Servert egy hálózati virtuális berendezéssel (NVA) a virtuális hálózaton a Azure Portal használatával. Az Azure Route Server a NVA érkező útvonalakat tanulja meg, és a virtuális hálózatban lévő virtuális gépeken is programba veszi őket. Az Azure Route Server a virtuális hálózati útvonalakat is meghirdeti a NVA. További információért olvassa el az [Azure Route Servert](overview.md).

> [!IMPORTANT]
> Az Azure Route Server (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tekintse át az [Azure Route Server szolgáltatási korlátozásait](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Útvonal-kiszolgáló létrehozása

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="create-a-route-server"></a>Útvonal-kiszolgáló létrehozása

1. Nyissa meg a következőt: https://aka.ms/routeserver.

1. Válassza az **+ új útvonal-kiszolgáló létrehozása** lehetőséget.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="A Route Server kezdőlapjának képernyőképe."::: 

1. Az **útvonal-kiszolgáló létrehozása** lapon adja meg vagy válassza ki a szükséges adatokat.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Az útvonal-kiszolgáló létrehozása lap képernyőképe.":::     

    | Beállítások | Érték |
    |----------|-------|
    | Előfizetés | Válassza ki az útválasztási kiszolgáló üzembe helyezéséhez használni kívánt Azure-előfizetést. |
    | Erőforráscsoport | Válasszon ki egy erőforráscsoportot az útvonal-kiszolgáló létrehozásához a alkalmazásban. Ha nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egy újat. |
    | Name | Adja meg az útvonal-kiszolgáló nevét. |
    | Region | Válassza ki azt a régiót, amelyben az útvonal-kiszolgáló létre lesz hozva. Válassza ki ugyanazt a régiót, mint a korábban létrehozott virtuális hálózatot, és tekintse meg a virtuális hálózatot a legördülő menüből. |
    | Virtual Network | Válassza ki azt a virtuális hálózatot, amelyben az útvonal-kiszolgálót létre kívánja hozni. Létrehozhat egy új virtuális hálózatot, vagy használhat egy meglévő virtuális hálózatot is. Ha meglévő virtuális hálózatot használ, győződjön meg arról, hogy a meglévő virtuális hálózatnak elegendő lemezterülete van legalább egy/27 alhálózat számára az útválasztási kiszolgáló alhálózati követelményeinek kielégítéséhez. Ha nem látja a virtuális hálózatot a legördülő listából, győződjön meg róla, hogy a megfelelő erőforráscsoportot vagy régiót választotta. |
    | Alhálózat | Miután létrehozott vagy kiválasztott egy virtuális hálózatot, megjelenik az alhálózat mező. Ez az alhálózat csak az útválasztásra szolgál. Válassza az **alhálózat konfigurációjának kezelése** lehetőséget, és hozza létre az Azure Route Server alhálózatát. Válassza az **+ alhálózat** lehetőséget, és hozzon létre egy alhálózatot az alábbi irányelvek alapján:</br><br>– Az alhálózatnak *RouteServerSubnet* néven kell lennie.</br><br>– Az alhálózatnak legalább/27 vagy nagyobb számnak kell lennie.</br> |

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át az összegzést, majd kattintson a **Létrehozás** gombra. 

    > [!NOTE]
    > Az útvonal-kiszolgáló üzembe helyezése körülbelül 20 percet vesz igénybe.

## <a name="set-up-peering-with-nva"></a>Társítás beállítása a NVA

A szakasz segítséget nyújt a BGP-társak konfigurálásához a NVA.

1. Nyissa meg a Azure Portal az [útvonal-kiszolgálót](https://aka.ms/routeserver) , és válassza ki a konfigurálni kívánt útválasztási kiszolgálót.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Az útvonal-kiszolgálók listájának képernyőképe"::: 

1. A bal oldali navigációs panel *Beállítások* területén válassza a **társak** lehetőséget. Ezután válassza a **+ Hozzáadás** lehetőséget egy új társ hozzáadásához.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Képernyőkép a partnerek kezdőlapján."::: 

1. Adja meg az alábbi adatokat a NVA-társával kapcsolatban.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="A társ hozzáadása lap képernyőképe.":::

    | Beállítások | Érték |
    |----------|-------|
    | Név | Adja meg az útvonal-kiszolgáló és a NVA közötti társítás nevét. |
    | ASN |  Adja meg a NVA autonóm rendszer számát (ASN). |
    | IPv4-címek | Adja meg annak a NVA az IP-címét, amellyel az útválasztási kiszolgáló kommunikálni fog a BGP létrehozásához. |

1. A partner hozzáadásához válassza a **Hozzáadás** lehetőséget.

## <a name="complete-the-configuration-on-the-nva"></a>A konfiguráció befejezése a NVA

A BGP-munkamenet létrehozásához szüksége lesz az Azure Route Server társközi IP-címeire és az ASN-re a NVA konfigurációjának befejezéséhez. Ezt az információt az útválasztási kiszolgáló áttekintés lapjáról szerezheti be.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Az útválasztási kiszolgáló áttekintő oldalának képernyőképe.":::

## <a name="configure-route-exchange"></a>Útvonal-Exchange konfigurálása

Ha rendelkezik ExpressRoute-átjáróval és/vagy VPN-átjáróval, és azt szeretné, hogy az útválasztási kiszolgálóval Exchange-útvonalakat cseréljen, akkor engedélyezheti az útválasztási Exchange használatát.

1. Nyissa meg a Azure Portal az [útvonal-kiszolgálót](https://aka.ms/routeserver) , és válassza ki a konfigurálni kívánt útválasztási kiszolgálót.

1. A bal oldali navigációs panel *Beállítások* területén válassza a **Konfigurálás** lehetőséget.

1. Válassza az **Engedélyezés** lehetőséget az **ág – ág** beállításnál, majd válassza a **Mentés** lehetőséget.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="A Route Exchange engedélyezésének képernyőképe.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure Route Serverre, válassza a **Törlés** lehetőséget az Áttekintés lapon az útvonal-kiszolgáló megszüntetéséhez.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Képernyőkép az útválasztási kiszolgáló törléséről.":::

## <a name="next-steps"></a>Következő lépések

Az Azure Route Server létrehozása után folytassa a további tudnivalókat arról, hogy az Azure Route Server hogyan kommunikál a ExpressRoute és a VPN-átjárókkal: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute és Azure VPN-támogatás](expressroute-vpn-support.md)
