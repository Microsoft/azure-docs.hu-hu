---
title: 'Forgatókönyv: a virtuális hálózatok elkülönítése'
titleSuffix: Azure Virtual WAN
description: Forgatókönyvek az útválasztási elkülönítéshez virtuális hálózatok
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f725932b30fad062123d6c752f2d563b84f98b2f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267635"
---
# <a name="scenario-isolating-vnets"></a>Forgatókönyv: a virtuális hálózatok elkülönítése

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a forgatókönyvben a cél az, hogy megakadályozza, hogy a virtuális hálózatok más elérésű legyen. Ez az úgynevezett virtuális hálózatok elkülönítése. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tervezés

Ebben az esetben egy adott VNet belüli munkaterhelés elkülönített marad, és nem tud kommunikálni más virtuális hálózatok. A virtuális hálózatok azonban az összes ág (VPN, ÖÖÖ és felhasználói VPN) eléréséhez szükséges. Ha szeretné kideríteni, hogy hány útválasztási táblázatra van szükség, létrehozhat egy kapcsolati mátrixot. Ebben az esetben a következő táblázathoz hasonlóan fog kinézni, ahol az egyes cellák azt jelzik, hogy egy forrás (sor) tud-e kommunikálni egy adott céllal (oszlop):

| Forrás |   Művelet |  *Virtuális hálózatok* | *Ágak* |
| -------------- | -------- | ---------- | ---|
| Virtuális hálózatok     | &#8594;|           |     X    |
| Ágak   | &#8594;|    X     |     X    |

Az előző táblázatban szereplő összes cella azt ismerteti, hogy egy virtuális WAN-kapcsolat (a folyamat "feladó" oldala, a sorfejlécek) megtanulja-e a cél előtagot (a folyamat "to" oldalát, a dőlt betűs oszlop fejléceit) egy adott forgalmi folyamathoz, ahol az "X" azt jelenti, hogy a kapcsolatot a virtuális WAN nyújtja.

Ez a kapcsolati mátrix két különböző sor mintázatot biztosít, amelyek két útválasztási táblázatra fordíthatók le. A virtuális WAN már rendelkezik alapértelmezett útválasztási táblázattal, ezért szükség lesz egy másik útválasztási táblázatra. Ebben a példában az útválasztási táblázatot **RT_VNET**nevet adja.

A virtuális hálózatok ehhez a **RT_VNET** útválasztási táblához lesz társítva. Mivel az ágakhoz való kapcsolódásra van szükségük, az ágakat a **RT_VNETra** kell terjeszteni (ellenkező esetben a virtuális hálózatok nem fogja megtanulni a fiókirodák előtagjait). Mivel az ágak mindig az alapértelmezett útválasztási táblázathoz vannak társítva, a virtuális hálózatok az alapértelmezett útválasztási táblázatba kell terjeszteni. Ennek eredményeképpen ez a végső terv:

* Virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_VNET**
  * Propagálás az útválasztási táblákba: **alapértelmezett**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **RT_VNET** és **alapértelmezett**

Figyelje meg, hogy mivel csak az ágak vannak propagálva az útválasztási táblázat **RT_VNET**, ezek lesznek az egyetlen előtagok, amelyeket a virtuális hálózatok fog tanulni, és nem a többi virtuális hálózatok.

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Ennek a forgatókönyvnek a konfigurálásához a következő lépéseket kell figyelembe vennie:

1. Hozzon létre egy egyéni útválasztási táblázatot az egyes csomópontokon. A példában az útválasztási táblázat **RT_VNet**. Útválasztási táblázat létrehozásával kapcsolatban lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md). További információ az útválasztási táblákról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
2. A **RT_VNet** útválasztási táblázat létrehozásakor adja meg a következő beállításokat:

   * **Társítás**: válassza ki az elkülöníteni kívánt virtuális hálózatok.
   * **Propagálás**: válassza ki az ágak, a hozzá tartozó ág (VPN/er/P2S) kapcsolatok lehetőséget, majd propagálja az útvonalakat ebbe az útválasztási táblába.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Elkülönített virtuális hálózatok":::

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
