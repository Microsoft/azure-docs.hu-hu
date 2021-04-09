---
title: Virtuális központ útválasztásának konfigurálása
titleSuffix: Azure Virtual WAN
description: Ez a cikk a virtuális központ útválasztásának konfigurálását ismerteti
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313688"
---
# <a name="how-to-configure-virtual-hub-routing"></a>Virtuális központ útválasztásának konfigurálása

A virtuális központ több átjárót is tartalmazhat, például a helyek közötti VPN-átjárót, a ExpressRoute átjárót, a pont – hely átjárót és a Azure Firewall. A virtuális központ útválasztási funkcióit egy olyan útválasztó biztosítja, amely az összes útválasztást, beleértve az átirányítási útválasztást is, az átjárók közötti Border Gateway Protocol (BGP) használatával felügyeli. Ez az útválasztó a virtuális hubhoz csatlakozó virtuális hálózatok közötti átviteli kapcsolatot is biztosít, amely akár 50 GB/s-os összesített átviteli sebességet is képes támogatni. Ezek az útválasztási képességek a szabványos virtuális WAN-ügyfelekre vonatkoznak.

További információ: [a virtuális központ útválasztása](about-virtual-hub-routing.md).

## <a name="create-a-route-table"></a><a name="create-table"></a>Útválasztási táblázat létrehozása

1. A Azure Portal navigáljon a virtuális hubhoz.
2. A **kapcsolat** területen válassza az **Útválasztás** elemet. Az Útválasztás lapon megtekintheti az **alapértelmezett** és a **none** útválasztási táblákat.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="Útválasztási lap":::
3. Válassza az **+ útválasztási táblázat létrehozása** lehetőséget az **útválasztási táblázat létrehozása** lap megnyitásához.
4. Az útválasztási táblázat létrehozása lap **alapjai** lapon végezze el a következő mezőket.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="Alapbeállítások lap":::

   * **Név**
   * **Útvonalak**
   * **Útvonal neve**
   * **Cél típusa**
   * **Cél előtagja**: összesítheti az előtagokat. Például: VNet 1:10.1.0.0/24 és VNet 2:10.1.1.0/24 10.1.0.0/16 lehet összesíteni. A **fiókirodai** útvonalak az összes csatlakoztatott VPN-webhelyre, ExpressRoute-áramkörre és felhasználói VPN-kapcsolatra vonatkoznak.
   * **Következő ugrás**: a virtuális hálózati kapcsolatok listája vagy Azure Firewall.

     Ha virtuális hálózati kapcsolatokat választ, a **statikus útvonalak konfigurálása** lehetőség látható. Ez egy opcionális konfigurációs beállítás. További információ: [statikus útvonalak konfigurálása](about-virtual-hub-routing.md#static).

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="Következő ugrás":::

5. Válassza a **címkék** fület a feliratok nevének konfigurálásához. A címkék olyan mechanizmust biztosítanak, amely logikailag csoportosítja az útválasztási táblákat.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="Címkék nevének konfigurálása":::

6. Válassza a **társítások** fület az útválasztási táblázat kapcsolatainak társításához.
Látni fogja az **ágakat**, a **virtuális hálózatokat** és a kapcsolatok **aktuális beállításait** .

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="Társítási kapcsolatok az útválasztási táblázathoz":::

7. Válassza a **propagálások** fület, hogy az útvonalakat propagálja a kapcsolatokból az útválasztási táblázatba.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="Útvonalak propagálása":::

8. Válassza a **Létrehozás** lehetőséget az útválasztási táblázat létrehozásához.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>Útválasztási táblázat szerkesztése

A Azure Portal keresse meg a virtuális központ útválasztási táblázatát. Válassza ki az útválasztási táblázatot az adatok szerkesztéséhez.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>Útválasztási táblázat törlése

A Azure Portal keresse meg a virtuális központ útválasztási táblázatát. Nem törölhet alapértelmezett vagy egyik útválasztási táblázatot sem. Az összes egyéni útválasztási táblát azonban törölheti. Kattintson a **"..."** elemre, majd válassza a **Törlés** lehetőséget.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>A hatályos útvonalak megtekintése

A Azure Portal keresse meg a virtuális központ útválasztási táblázatát. Kattintson a **"..."** lehetőségre, és válassza a **hatályos útvonalak** lehetőséget a kiválasztott útválasztási táblázat által megismert útvonalak megtekintéséhez. A rendszer automatikusan kitölti az útválasztási táblázathoz való kapcsolódásból származó propagált útvonalakat az útválasztási táblázat **tényleges útvonalán** . További információ: [a hatékony útvonalak](effective-routes-virtual-hub.md).

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="Érvényes útvonalak megtekintése" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>A virtuális hálózati kapcsolatok útválasztási konfigurációjának beállítása

1. A Azure Portal navigáljon a virtuális WAN-hoz, és a **kapcsolat** területen válassza a **Virtual Network kapcsolatok** elemet.
1. Válassza a **+ kapcsolatok hozzáadása** elemet.
1. Válassza ki a virtuális hálózatot a legördülő listából.
1. Állítsa be az útválasztási konfigurációt, hogy az egy útválasztási táblázathoz legyen hozzárendelve. Az **útválasztási táblázathoz** válassza ki az útválasztási táblázatot a legördülő listából.
1. Állítsa be az útválasztási konfigurációt egy vagy több útválasztási táblázatba való propagáláshoz. A **propagálás az útválasztási táblában** beállításnál válassza a lehetőséget a legördülő listából.
1. **Statikus útvonalak** esetében konfigurálja a statikus útvonalakat a hálózati virtuális berendezéshez (ha van ilyen). A virtuális WAN egy virtuális hálózati kapcsolaton belül egyetlen következő ugrási IP-címet támogat a statikus útvonalhoz. Ha például külön virtuális berendezéssel rendelkezik a bejövő és a kimenő forgalom forgalmához, érdemes lehet a virtuális berendezéseket külön virtuális hálózatok, és csatlakoztatni a virtuális hálózatok a virtuális hubhoz.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="Útválasztási konfiguráció beállítása" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>Következő lépések

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
