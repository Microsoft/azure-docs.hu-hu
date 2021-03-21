---
title: 'Forgatókönyv: a virtuális hálózatok egyéni elkülönítése'
titleSuffix: Azure Virtual WAN
description: Az Útválasztás forgatókönyvei – megakadályozza, hogy a kiválasztott virtuális hálózatok egymáshoz jussanak
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92461821"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Forgatókönyv: a virtuális hálózatok egyéni elkülönítése

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. A virtuális hálózatok egyéni elkülönítési forgatókönyvében a cél annak megakadályozása, hogy a virtuális hálózatok adott készlete képes legyen a virtuális hálózatok más konkrét készletének elérésére. A virtuális hálózatok azonban az összes ág (VPN/ER/User VPN) eléréséhez szükséges. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tervezés

Ha szeretné kideríteni, hogy hány útválasztási táblázatra van szükség, létrehozhat egy kapcsolati mátrixot. Ennél a forgatókönyvnél a következőhöz hasonlóan fog kinézni, ahol minden cella azt jelöli, hogy egy forrás (sor) tud-e kommunikálni egy célhoz (oszlophoz):

| Forrás | Címzett:| *Kék virtuális hálózatok* | *Piros virtuális hálózatok* | *Ágak*|
|---|---|---|---|---|
| **Kék virtuális hálózatok** |   &#8594;|   Direct     |           |  Direct |
| **Piros virtuális hálózatok**  |   &#8594;|              |   Direct  |  Direct |
| **Ágak**   |   &#8594;|   Direct     |   Direct  |  Direct |

Az előző táblázatban szereplő összes cella azt ismerteti, hogy egy virtuális WAN-kapcsolat (a folyamat "feladó" oldala, a sorfejlécek) kommunikál-e egy célhoz (a folyamat "to" oldalára, a dőlt betűs oszlopokra). Ebben a forgatókönyvben nincsenek tűzfalak vagy hálózati virtuális berendezések, így a kommunikáció közvetlenül a virtuális WAN-kapcsolaton keresztül történik (ezért a táblázatban a "Direct" szó látható).

A különböző sorokban szereplő minták száma az ebben a forgatókönyvben szükséges útválasztási táblák száma lesz. Ebben az esetben három útvonal-útválasztási táblázat **RT_BLUE** és **RT_RED** a virtuális hálózatokhoz, és az ágak **alapértelmezett értéke** . Ne feledje, hogy az ágakat mindig az alapértelmezett útválasztási táblázathoz kell társítani.

Az ágaknak mind a vörös, mind a kék virtuális hálózatok meg kell ismerniük az előtagokat, így az összes virtuális hálózatok az alapértelmezett értékre kell terjesztenie (továbbá **RT_BLUE** vagy **RT_RED**). A kék és a piros virtuális hálózatok meg kell tanulnia az ágak előtagjait, így az ágak mindkét útválasztási táblára **RT_BLUE** és **RT_RED** is lesznek továbbítva. Ennek eredményeképpen ez a végső terv:

* Kék virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_BLUE**
  * Propagálás az útválasztási táblákba: **RT_BLUE** és **alapértelmezett**
* Vörös virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_RED**
  * Propagálás az útválasztási táblákba: **RT_RED** és **alapértelmezett**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **RT_BLUE**, **RT_RED** és **alapértelmezett**

> [!NOTE]
> Mivel az összes ágat hozzá kell rendelni az alapértelmezett útválasztási táblázathoz, és az azonos útválasztási táblákra kell terjeszteni, minden ág ugyanazzal a csatlakozási profillal fog rendelkezni. Más szóval a virtuális hálózatok vörös/kék koncepciója nem alkalmazható ágakra.

> [!NOTE]
> Ha a virtuális WAN több régióban van telepítve, létre kell hoznia a **RT_BLUE** és **RT_RED** útválasztási táblázatokat minden központban, és az egyes VNet-kapcsolatok útvonalait a terjesztési címkék használatával kell propagálni minden virtuális központ útválasztási tábláiba.

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Munkafolyamat

Az **1. ábrán** kék és piros VNet kapcsolatok találhatók.

* A kék kapcsolatban álló virtuális hálózatok elérheti egymással, valamint elérheti az összes ág (VPN/ER/P2S) kapcsolatait.
* A piros virtuális hálózatok elérheti egymást, valamint elérheti az összes ág (VPN/ER/P2S) kapcsolatait.

Az Útválasztás beállításakor vegye figyelembe a következő lépéseket.

1. Hozzon létre két egyéni útválasztási táblázatot a Azure Portalban, **RT_BLUE** és **RT_RED**.
2. Az útválasztási táblázat **RT_BLUE** a következő beállításokhoz:
   * **Társítás**: válassza az összes kék virtuális hálózatok elemet.
   * **Propagálás**: ágak esetében válassza a fiókok, a hozzá tartozó ág (VPN/er/P2S) kapcsolatok lehetőséget, majd propagálja az útvonalakat ebbe az útválasztási táblába.
3. Ismételje meg ugyanezeket a lépéseket a vörös virtuális hálózatok és ágakhoz **RT_RED** útválasztási táblázathoz (VPN/er/P2S).

Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az alábbi ábrán látható módon.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="1. ábra":::

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
