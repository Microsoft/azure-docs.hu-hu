---
title: Forgalom irányítása NVA keresztül egyéni beállítások használatával
titleSuffix: Azure Virtual WAN
description: Ez a forgatókönyv segítséget nyújt a forgalom NVA-n keresztüli irányításához, ha egy másik NVA használ az internetre kötött forgalomhoz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8916fbc7c2a0b9789dcc73697324cee370f1fc1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704905"
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
|---|:---:|:---:|:---:|:---:|:---:|
| **Küllők**| ->| közvetlenül |közvetlenül | a Service VNet |Peremhálózati VNet |
| **Szolgáltatás VNet**| ->| közvetlenül |n.a.| közvetlenül | |
| **Ágak** | ->| a Service VNet |közvetlenül| közvetlenül |  |

A kapcsolati mátrix minden cellája azt írja le, hogy a kapcsolat közvetlenül a virtuális WAN-on vagy egy NVA rendelkező virtuális hálózatokon keresztül áramlik-e.

Figyelje meg a következő részleteket:

* Küllők
  * A küllők más küllőket érnek el közvetlenül a virtuális WAN-hubokon.
  * A küllők a szolgáltatás VNet mutató statikus útvonalon keresztül kapnak kapcsolatot az ágakkal. Nem tanulnak meg konkrét előtagokat az ágakból, mivel ezek az előtagok konkrétabbak, és felülbírálják az összegzést.
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
> Győződjön meg arról, hogy a küllős virtuális hálózatok nem propagálják az alapértelmezett címkét. Ez gondoskodik arról, hogy az ágaktól a küllő virtuális hálózatok érkező forgalom továbbítva legyen a NVA.

Ezek a statikus útvonalak biztosítják, hogy a virtuális hálózat és az ág felé irányuló forgalom a szolgáltatás VNet (VNet 4) keresztül haladjon a NVA:

| Leírás | Útválasztási táblázat | Statikus útvonal              |
| ----------- | ----------- | ------------------------- |
| Ágak    | RT_V2B      | 10.2.0.0/16 – > vnet4conn  |
| NVA küllők  | Alapértelmezett     | 10.1.0.0/16 – > vnet4conn  |

Most már használhatja a virtuális WAN-t, hogy kiválassza a megfelelő kapcsolódási lehetőséget a csomagok küldéséhez. A virtuális WAN-t is kell használnia, hogy kiválassza a csomagok fogadásakor végrehajtandó helyes műveletet. Ehhez a következő módon használhatja a kapcsolatok útválasztási táblázatait:

| Leírás | Kapcsolat | Statikus útvonal            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 – > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 – > 10.4.0.5 |

További információ: [a virtuális központ útválasztása](about-virtual-hub-routing.md).

## <a name="architecture"></a>Architektúra

A következő ábrán a cikkben korábban ismertetett architektúra látható.

A diagramon egy hub található; **1. központ**

* Az **1. hub** közvetlenül kapcsolódik a NVA virtuális hálózatok **VNet 4** és a **VNet 5** rendszerhez.

* Az 1, 2, 3 virtuális hálózatok és az ágak közötti forgalomnak a **VNet 4 NVA** -10.4.0.5 keresztül kell haladnia.

* Az 1., 2. és 3. virtuális hálózatok összes internetre kötött forgalma a **VNet 5 NVA** -10.5.0.5 keresztül várható.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="A hálózati architektúra ábrája.":::

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Munkafolyamat diagramja" lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Az Útválasztás NVA-n keresztüli beállításához vegye figyelembe a következő lépéseket:

1. Ahhoz, hogy az internetre kötött forgalom a VNet 5 használatával menjen végig, a virtuális hálózatok 1, 2 és 3 értékre van szüksége, hogy közvetlenül kapcsolódjon a virtuális hálózati kapcsolaton keresztül a VNet 5-öt. Szükség van egy felhasználó által definiált útvonalra is, amely a 0.0.0.0/0 és a következő ugrási 10.5.0.5 virtuális hálózatokban van beállítva.

   Ha nem szeretné, hogy az 1., 2. és 3. virtuális hálózatok csatlakoztassa a VNet 5-öt, hanem csak a VNet 4 NVA használja, hogy a 0.0.0.0/0 forgalmat az ágakból (helyszíni VPN-vagy ExpressRoute-kapcsolatokból) irányítsa, nyissa meg a [másodlagos munkafolyamatot](#alternate).

   Ha azonban azt szeretné, hogy a VNet a NVA keresztül áthaladjon a VNet, le kell választania a VNet 1, 2, 3 elemet a virtuális központból, és csatlakoztatnia kell, vagy azt a NVA küllő Vnet4 felé fölé kell kapcsolnia. A virtuális WAN-ban a VNet-VNet forgalom áthalad a virtuális WAN-központban vagy egy virtuális WAN hub Azure Firewall (biztonságos központ) használatával. Ha a virtuális hálózatok-társ közvetlenül a VNet-társítást használja, akkor a virtuális központon keresztül közvetlenül megkerülheti az átvitelt.

1. A Azure Portal lépjen a virtuális hubhoz, és hozzon létre egy **RT_Shared** nevű egyéni útválasztási táblázatot. Ez a táblázat az útvonalakat az összes virtuális hálózatról és ág-kapcsolatról történő propagálás útján tanulja meg. Ezt az üres táblázatot a következő ábrán láthatja.

   * **Útvonalak:** Nincs szükség statikus útvonalak hozzáadására.

   * **Társítás:** Válassza a 4. és az 5. virtuális hálózatok, ami azt jelenti, hogy ezeknek a virtuális hálózatoknak a kapcsolatai az útválasztási táblázat **RT_Sharedhoz** vannak hozzárendelve.

   * **Propagálás:** Mivel azt szeretné, hogy az összes ág és virtuális hálózati kapcsolat dinamikusan terjessze az útvonalakat az útválasztási táblázatba, válassza az ágak és az összes virtuális hálózat lehetőséget.

1. Hozzon létre egy **RT_V2B** nevű egyéni útválasztási táblázatot az 1., 2. és 3. virtuális hálózatok érkező forgalom átirányításához az ágakba.

   * **Útvonalak:** Vegyen fel egy összesített statikus útvonal-bejegyzést az ágakhoz a következő ugrással, a VNet 4 kapcsolattal. Konfiguráljon egy statikus útvonalat a VNet 4 kapcsolata ág előtagjaihoz. Adja meg a következő ugrást a NVA adott IP-címéhez a VNet 4-ben.

   * **Társítás:** Válassza az összes **virtuális hálózatok 1, 2 és 3** elemet. Ez azt jelenti, hogy az 1., 2. és 3. VNet-kapcsolatok ehhez az útválasztási táblázathoz lesznek hozzárendelve, és képesnek kell lenniük arra, hogy megismerjék az útvonalakat (statikus és dinamikus propagálással) ebben az útválasztási táblázatban.

   * **Propagálás:** A kapcsolatok propagálják az útvonalakat az útválasztási táblákhoz. Az 1., 2. és 3. virtuális hálózatok kiválasztásával engedélyezheti a terjesztési útvonalakat az 1., 2. és 3. virtuális hálózatok az útválasztási táblázatba. Nem kell propagálni az útvonalakat az ág kapcsolatairól **RT_V2Bre**, mert az ág virtuális hálózati forgalma a 4. VNet NVA keresztül megy keresztül.
  
1. Szerkessze az alapértelmezett útválasztási táblázatot, a **DefaultRouteTable**.

   Az összes VPN-, Azure-ExpressRoute és felhasználói VPN-kapcsolat az alapértelmezett útválasztási táblázathoz van társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz.

   * **Útvonalak:** Vegyen fel egy összesített statikus útválasztási bejegyzést az 1., 2. és 3. virtuális hálózatok, a következő ugrással a VNet 4 kapcsolattal. Az 1., 2. és 3. összesítő előtagokhoz tartozó statikus útvonal konfigurálása VNet 4 kapcsolaton keresztül. Adja meg a következő ugrást a NVA adott IP-címéhez a VNet 4-ben.

   * **Társítás:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, így biztosítva, hogy a helyszíni ág kapcsolatai az alapértelmezett útválasztási táblázathoz legyenek társítva.

   * **Propagálás forrása:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, így biztosítva, hogy a helyszíni kapcsolatok propagálják az útvonalakat az alapértelmezett útválasztási táblázatba.

## <a name="alternate-workflow"></a><a name="alternate"></a>Másodlagos munkafolyamat

Ebben a munkafolyamatban nem csatlakozik az 1., 2. és 3. virtuális hálózatok az VNet 5-ös verzióhoz. Ehelyett a VNet 4 NVA használja a 0.0.0.0/0 forgalom útválasztásához az ágakból (helyszíni VPN-vagy ExpressRoute-kapcsolatok).

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Másodlagos munkafolyamat diagramja" lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Az Útválasztás NVA-n keresztüli beállításához vegye figyelembe a következő lépéseket:

1. A Azure Portal lépjen a virtuális hubhoz, és hozzon létre egy **RT_NVA** nevű egyéni útválasztási táblázatot, amely a NVA-10.4.0.5 keresztül irányítja át a forgalmat.  

   * **Útvonalak:**   Nincs szükség beavatkozásra.

   * **Társítás:**   Válassza a **vnet4 felé** lehetőséget. Ez azt jelenti, hogy a 4. VNet-csatlakozás ehhez az útválasztási táblázathoz lesz hozzárendelve, és képes megtanulni az útvonalakat (statikus és dinamikus terjesztési használatával) ebben az útválasztási táblázatban.

   * **Propagálás:**   A kapcsolatok propagálják az útvonalakat az útválasztási táblákhoz. Az 1., 2. és 3. virtuális hálózatok kiválasztásával engedélyezheti a terjesztési útvonalakat az 1., 2. és 3. virtuális hálózatok az útválasztási táblázatba. Az ágak kiválasztása (VPN/ER/P2S) lehetővé teszi útvonalak továbbítását az ágakból/helyszíni kapcsolatokból az útválasztási táblázatba. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz.

1. Hozzon létre egy **RT_VNET** nevű egyéni útválasztási táblázatot, amely az 1., 2. és 3. virtuális hálózatok forgalmat az ágakba vagy az internetre (0.0.0.0/0) irányítja a vnet4 felé NVA keresztül. A VNet és a VNet közötti forgalom közvetlen lesz, és nem a VNet 4 NVA. Ha azt szeretné, hogy a forgalom átugorjon a NVA, bontsa ki az 1., 2. és 3. VNet, és csatlakoztassa őket a VNet-társítással a Vnet4 felé-hez.

   * **Útvonalak**  

     * Vegyen fel egy "10.2.0.0/16" összesített útvonalat a következő ugrásként a VNet 4 kapcsolattal az 1., 2. és 3. virtuális hálózatok felé haladó adatforgalomhoz. A Vnet4 felé-kapcsolatban állítson be egy útvonalat a "10.2.0.0/16" értékre, és jelezze a következő ugrást, hogy a NVA megadott IP-címe legyen a VNet 4-ben.

     * Adja hozzá a "0.0.0.0/0" útvonalat a következő ugrásként a VNet 4 kapcsolathoz. a "0.0.0.0/0" hozzá lett adva, amely az internetre irányuló adatforgalmat jelenti. Nem vonatkozik a virtuális hálózatok vagy ágakra vonatkozó konkrét címek előtagjaira. A Vnet4 felé-kapcsolatban állítson be egy útvonalat a "0.0.0.0/0" értékre, és jelezze a következő ugrást, hogy a NVA megadott IP-címe legyen a VNet 4-ben.

   * **Társítás:**   Válassza az összes **virtuális hálózatok 1, 2 és 3** elemet. Ez azt jelenti, hogy az 1., 2. és 3. VNet-kapcsolatok ehhez az útválasztási táblázathoz lesznek hozzárendelve, és képesnek kell lenniük arra, hogy megismerjék az útvonalakat (statikus és dinamikus propagálással) ebben az útválasztási táblázatban.

   * **Propagálás:**   A kapcsolatok propagálják az útvonalakat az útválasztási táblákhoz. Az 1., 2. és 3. virtuális hálózatok kiválasztásával engedélyezheti a terjesztési útvonalakat az 1., 2. és 3. virtuális hálózatok az útválasztási táblázatba. Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás nincs kiválasztva. Ez biztosítja, hogy a helyszíni kapcsolatok nem tudnak az 1, 2 és 3 virtuális hálózatok jutni közvetlenül.

1. Szerkessze az alapértelmezett útválasztási táblázatot, a **DefaultRouteTable**.

   Az összes VPN-, Azure-ExpressRoute és felhasználói VPN-kapcsolat az alapértelmezett útválasztási táblázathoz van társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz.

   * **Útvonalak**  

     * Adja hozzá az **1., 2. és 3. virtuális hálózatok** tartozó összesített útvonalat a következő ugrásként, a **VNet 4 kapcsolattal**.

     * Adja hozzá a "0.0.0.0/0" útvonalat a következő ugrásként a **VNet 4 kapcsolathoz**. a "0.0.0.0/0" hozzá lett adva, amely az internetre irányuló adatforgalmat jelenti. Nem vonatkozik a virtuális hálózatok vagy ágakra vonatkozó konkrét címek előtagjaira. A Vnet4 felé-kapcsolat előző lépésében már beállította a "0.0.0.0/0" útvonalat, a következő ugrással pedig a NVA adott IP-címe lesz a VNet 4-ben.

   * **Társítás:**   Győződjön meg arról, hogy az ágak **(VPN/er/P2S)** lehetőség van kiválasztva. Ez biztosítja, hogy a helyszíni ág kapcsolatai az alapértelmezett útválasztási táblázathoz legyenek társítva. Az összes VPN-, Azure-ExpressRoute és felhasználói VPN-kapcsolat csak az alapértelmezett útválasztási táblázathoz van társítva.

   * **Propagálás forrása:**   Győződjön meg arról, hogy az ágak **(VPN/er/P2S)** lehetőség van kiválasztva. Ez biztosítja, hogy a helyszíni kapcsolatok az útvonalakat az alapértelmezett útválasztási táblázatba propagálják. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat propagálja az útvonalakat az "azonos útválasztási táblákhoz".

   >[!Note]
   >
   > * A portál felhasználóknak engedélyeznie kell a "propagálás az alapértelmezett útvonalon" beállítást a (z) 0.0.0.0/0 útvonal kapcsolatok (VPN/ER/P2S/VNet) esetében a érvénybe léptetéséhez.
   > * A PS/CLI/REST felhasználóknál a "enableinternetsecurity" jelzőt igaz értékre kell állítani a 0.0.0.0/0 útvonal érvénybe léptetéséhez.
   >

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
