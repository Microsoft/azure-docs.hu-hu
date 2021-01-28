---
title: Forgalom irányítása NVA keresztül egyéni beállítások használatával
titleSuffix: Azure Virtual WAN
description: Ez a forgatókönyv segítséget nyújt a forgalom NVA-n keresztüli irányításához, ha egy másik NVA használ az internetre kötött forgalomhoz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e51d7d00120f6facb0fb53a8e379d157ae79ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938575"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Forgatókönyv: forgalom továbbítása NVA keresztül egyéni beállítások használatával

Ha az Azure Virtual WAN-útválasztással dolgozik, számos lehetőség áll rendelkezésére. A cikk célja, hogy ha egy hálózati virtuális berendezésen (NVA) keresztül szeretné átirányítani a forgalmat a virtuális hálózatok és ágak közötti kommunikációhoz, és egy másik NVA használ az internethez kötött forgalomhoz. További információ: [a virtuális központ útválasztása](about-virtual-hub-routing.md).

## <a name="design"></a>Tervezés

* **Küllő** a virtuális hubhoz csatlakozó virtuális hálózatokhoz. (Például a cikk későbbi részében a VNet 1, VNet 2 és VNet 3 szerepel a diagramon.)
* A virtuális hálózatok **VNet** , amelyekben a felhasználók üzembe helyezettek egy NVA a nem internetes forgalom ellenőrzéséhez, és esetleg a küllők által elért közös szolgáltatásokkal. (Például a cikk későbbi részében található VNet 4.) 
* A virtuális hálózatok **peremhálózati VNet** , amelyekben a felhasználók telepítettek egy NVA, amely az internetes forgalom vizsgálatára szolgál. (Például a cikk későbbi részében a VNet 5.)
* A Microsoft által felügyelt virtuális WAN-hubok **központjai** .

A következő táblázat összefoglalja az ebben a forgatókönyvben támogatott kapcsolatokat:

| Forrás          | Művelet|Küllők|Szolgáltatás VNet|Ágak|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Küllők**| ->| közvetlenül |közvetlenül | a Service VNet |Peremhálózati VNet |
| **Szolgáltatás VNet**| ->| közvetlenül |n.a.| közvetlenül | |
| **Ágak** | ->| a Service VNet |közvetlenül| közvetlenül |  |

A kapcsolati mátrix minden cellája azt írja le, hogy a kapcsolat közvetlenül a virtuális WAN-on vagy egy NVA rendelkező virtuális hálózatokon keresztül áramlik-e. 

Figyelje meg a következő részleteket:
* Küllők
  * A küllők más küllőket érnek el közvetlenül a virtuális WAN-hubokon.
  * A küllők a szolgáltatás VNet mutató statikus útvonalon keresztül kapnak kapcsolatot az ágakkal. Nem tanulnak meg konkrét előtagokat az ágakból, mivel ezek konkrétabbak, és felülbírálják az összegzést.
  * A küllők internetes forgalmat küldenek a peremhálózat VNet egy közvetlen VNet-kapcsolaton keresztül.
* Az ágak a szolgáltatás VNet mutató statikus útvonalon keresztül fognak megszólalni. Nem tanulnak meg olyan előtagokat a virtuális hálózatokból, amelyek felülbírálják az összegzett statikus útvonalat.
* A szolgáltatás VNet hasonló lesz egy megosztott szolgáltatások VNet, amelynek minden virtuális hálózatból és elágazásból elérhetőnek kell lennie.
* A peremhálózati VNet nem kell a virtuális WAN-kapcsolaton keresztül csatlakoznia, mert az egyetlen, az általa támogatott forgalom a közvetlen virtuális hálózati kapcsolatok esetében fog megjelenni. A konfiguráció egyszerűsítése érdekében azonban ugyanazt a kapcsolódási modellt használja, mint a peremhálózati VNet.

Három különböző csatlakozási minta van, amelyek három útválasztási táblázatra fordíthatók le. A különböző virtuális hálózatokhoz tartozó társítások a következők:

* Küllők
  * Társított útválasztási táblázat: **RT_V2B**
  * Propagálás az útválasztási táblákba: **RT_V2B** és **RT_SHARED**
* NVA virtuális hálózatok (Service VNet és DMZ VNet):
  * Társított útválasztási táblázat: **RT_SHARED**
  * Propagálás az útválasztási táblákba: **RT_SHARED**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **RT_SHARED** és **alapértelmezett**

> [!NOTE] 
> Győződjön meg arról, hogy a küllős virtuális hálózatok nem propagálják az alapértelmezett címkét. Ez biztosítja, hogy az ágaktól a küllő virtuális hálózatok érkező forgalom továbbítva legyen a NVA.

Ezek a statikus útvonalak biztosítják, hogy a virtuális hálózat és az ág felé irányuló forgalom a szolgáltatás VNet (VNet 4) keresztül haladjon a NVA:

| Description | Útválasztási táblázat | Statikus útvonal              |
| ----------- | ----------- | ------------------------- |
| Ágak    | RT_V2B      | 10.2.0.0/16 – > vnet4conn  |
| NVA küllők  | Alapértelmezett     | 10.1.0.0/16 – > vnet4conn  |

Most már használhatja a virtuális WAN-t, hogy kiválassza a megfelelő kapcsolódási lehetőséget a csomagok küldéséhez. A virtuális WAN-t is kell használnia, hogy kiválassza a csomagok fogadásakor végrehajtandó helyes műveletet. Ehhez a következő módon használhatja a kapcsolatok útválasztási táblázatait:

| Description | Kapcsolat | Statikus útvonal            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 – > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 – > 10.4.0.5 |

További információ: [a virtuális központ útválasztása](about-virtual-hub-routing.md).

## <a name="architecture"></a>Architektúra

Itt látható a cikkben korábban ismertetett architektúra ábrája.

Egy hub **1. hub** néven található.

* Az **1. hub** közvetlenül kapcsolódik a NVA virtuális hálózatok **VNet 4** és a **VNet 5** rendszerhez.

* Az 1., 2. és 3. virtuális hálózatok közötti adatforgalmat, valamint az ágakat a **VNet 4 NVA** -10.4.0.5 keresztül kell eljárni.

* Az 1., 2. és 3. virtuális hálózatok összes internetre kötött forgalma a **VNet 5 NVA** -10.5.0.5 keresztül várható.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="A hálózati architektúra ábrája.":::

## <a name="workflow"></a>Munkafolyamat

Az Útválasztás NVA-n keresztüli beállításához a következő lépéseket kell figyelembe venni:

1. Ahhoz, hogy az internetre kötött forgalom a VNet 5 használatával menjen végig, a virtuális hálózatok 1, 2 és 3 értékre van szüksége, hogy közvetlenül kapcsolódjon a virtuális hálózati kapcsolaton keresztül a VNet 5-öt. Szükség van egy felhasználó által definiált útvonalra is, amely a 0.0.0.0/0 és a következő ugrási 10.5.0.5 virtuális hálózatokban van beállítva. A virtuális WAN jelenleg nem engedélyezi a következő ugrási NVA a 0.0.0.0/0 virtuális központban.

1. A Azure Portal lépjen a virtuális hubhoz, és hozzon létre egy **RT_Shared** nevű egyéni útválasztási táblázatot. Ez a táblázat az útvonalakat az összes virtuális hálózatról és ág-kapcsolatról történő propagálás útján tanulja meg. Ezt az üres táblázatot a következő ábrán láthatja.

   * **Útvonalak:** Nincs szükség statikus útvonalak hozzáadására.

   * **Társítás:** Válassza a 4. és az 5. virtuális hálózatok, ami azt jelenti, hogy ezeknek a virtuális hálózatoknak a kapcsolatai az útválasztási táblázat **RT_Sharedhoz** vannak hozzárendelve.

   * **Propagálás:** Mivel azt szeretné, hogy az összes ág és virtuális hálózati kapcsolat dinamikusan terjessze az útvonalakat az útválasztási táblázatba, válassza az ágak és az összes virtuális hálózat lehetőséget.

1. Hozzon létre egy **RT_V2B** nevű egyéni útválasztási táblázatot az 1., 2. és 3. virtuális hálózatok érkező forgalom átirányításához az ágakba.

   * **Útvonalak:** Vegyen fel egy összesített statikus útvonal-bejegyzést az ágakhoz a következő ugrással, a VNet 4 kapcsolattal. Konfiguráljon egy statikus útvonalat a VNet 4 kapcsolata a fiókirodák előtagjaihoz, és jelezze a következő ugrást, hogy a NVA megadott IP-címe legyen a VNet 4-ben.

   * **Társítás:** Válassza az összes virtuális hálózatok 1, 2 és 3 elemet. Ez azt jelenti, hogy az 1., 2. és 3. VNet-kapcsolatok ehhez az útválasztási táblázathoz lesznek hozzárendelve, és képesnek kell lenniük arra, hogy megismerjék az útvonalakat (statikus és dinamikus propagálással) ebben az útválasztási táblázatban.

   * **Propagálás:** A kapcsolatok propagálják az útvonalakat az útválasztási táblákhoz. Az 1., 2. és 3. virtuális hálózatok kiválasztásával engedélyezheti a terjesztési útvonalakat az 1., 2. és 3. virtuális hálózatok az útválasztási táblázatba. Nem kell propagálni az útvonalakat az ág kapcsolatairól **RT_V2Bre**, mert az ág virtuális hálózati forgalma a 4. VNet NVA keresztül megy keresztül.
  
1. Szerkessze az alapértelmezett útválasztási táblázatot, a **DefaultRouteTable**.

   Az összes VPN-, Azure-ExpressRoute és felhasználói VPN-kapcsolat az alapértelmezett útválasztási táblázathoz van társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz.

   * **Útvonalak:** Vegyen fel egy összesített statikus útválasztási bejegyzést az 1., 2. és 3. virtuális hálózatok, a következő ugrással a VNet 4 kapcsolattal. Konfiguráljon egy statikus útvonalat a VNet 4 kapcsolatában az 1., 2. és 3. összesítő VNet, és jelezze a következő ugrást a NVA megadott IP-címére a VNet 4-ben.

   * **Társítás:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, így biztosítva, hogy a helyszíni ág kapcsolatai az alapértelmezett útválasztási táblázathoz legyenek társítva.

   * **Propagálás forrása:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, így biztosítva, hogy a helyszíni kapcsolatok propagálják az útvonalakat az alapértelmezett útválasztási táblázatba.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Munkafolyamat diagramja" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
