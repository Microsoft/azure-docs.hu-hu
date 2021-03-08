---
title: 'Azure Virtual WAN: hozzon létre egy hálózati virtuális berendezést (NVA) a központban'
description: Ismerje meg, hogyan helyezhet üzembe egy hálózati virtuális berendezést a virtuális WAN-központban.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: f02edf8e192f4d32f8bd2583d46bbb17c86d2049
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454804"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Hálózati virtuális berendezés létrehozása Azure-beli virtuális WAN-központban (előzetes verzió)

Ez a cikk bemutatja, hogyan használható a Virtual WAN az Azure-beli erőforrásokhoz való kapcsolódáshoz egy **hálózati virtuális berendezésen** (NVA) keresztül az Azure-ban. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. A virtuális WAN-ról további információt a [Mi a virtuális WAN?](virtual-wan-about.md)című témakörben talál.

A cikkben ismertetett lépések segítséget nyújtanak a **Barracuda CLOUDGEN WAN** hálózati virtuális berendezés létrehozásában a virtuális WAN-központban. Ennek a gyakorlatnak a végrehajtásához a Barracuda Cloud bevezetési eszközt (CPE) és a Barracuda CloudGen WAN-berendezés licencét kell megadnia, mielőtt megkezdené a központi telepítést.

A **Cisco SD-WAN** Azure Virtual WAN-on belüli üzembe helyezési dokumentációjában tekintse [meg a Cisco Cloud OnRamp for multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)című témakört. 

A **VMware SD-WAN** Azure Virtual WAN-on belüli üzembe helyezési dokumentációjában tekintse meg a [VMware SD-WAN telepítési útmutatója a Virtual WAN hub-ban](https://kb.vmware.com/s/article/82746) című témakört.

## <a name="prerequisites"></a>Előfeltételek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Szerezze be a Barracuda CloudGen WAN-átjáró licencét. Ha többet szeretne megtudni Ennek módjáról, tekintse meg a [Barracuda CLOUDGEN WAN dokumentációját](https://www.barracuda.com/products/cloudgenwan) .

* Rendelkezik egy virtuális hálózattal, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a helyszíni hálózatok egyik alhálózata sem fedi át azokat a virtuális hálózatokat, amelyekhez csatlakozni szeretne. Ha virtuális hálózatot szeretne létrehozni a Azure Portalban, tekintse meg a rövid [útmutatót.](../virtual-network/quick-create-portal.md)

* A virtuális hálózat nem rendelkezik virtuális hálózati átjárókkal. Ha a virtuális hálózat átjáróval rendelkezik (VPN vagy ExpressRoute), akkor el kell távolítania az összes átjárót. Ehhez a konfigurációhoz az szükséges, hogy a virtuális hálózatok a virtuális WAN hub-átjáróhoz legyenek csatlakoztatva.

* Igényeljen egy IP-címtartományt az elosztó régiójában. A hub egy virtuális WAN által létrehozott és használt virtuális hálózat. Az hubhoz megadott címtartomány nem fedi át a meglévő virtuális hálózatait, amelyhez csatlakozik. Emellett nem lehet átfedésben azokkal a címtartományokkal sem, amelyekhez a helyszínen csatlakozik. Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtartományok körét, akkor egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek.

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Virtuális WAN létrehozása

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Elosztó létrehozása

A hub olyan virtuális hálózat, amely átjárókat tartalmazhat a helyek közötti, a ExpressRoute, a pont – hely vagy a hálózati virtuális berendezés funkcióihoz. A központ létrehozását követően a szolgáltatás akkor is díjköteles, ha nem csatol hozzá egyetlen webhelyet sem. Ha két hálózat közötti pont-pont típusú VPN-átjárót hoz létre, akkor 30 percet vesz igénybe, hogy létrehozza a helyek közötti VPN-átjárót a virtuális központban. A helyek közötti, a ExpressRoute vagy a pont – hely kapcsolattól eltérően a hubot először létre kell hoznia, mielőtt üzembe helyezi a hálózati virtuális berendezést a központi VNet.

1. Keresse meg a létrehozott virtuális WAN-t. A **virtuális WAN** lapon a **kapcsolat** szakaszban válassza a **hubok** lehetőséget.
1. A **hubok** lapon válassza az + új hub lehetőséget a **virtuális központ létrehozása** lap megnyitásához.

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Alapvető beállítások":::
1. A **virtuális központ létrehozása** lap **alapjai** lapon végezze el a következő mezőket:

   **Projekt részletei**

   * Régió (korábbi nevén hely)
   * Name
   * Hub magánhálózati címtartomány. A minimális címtartomány/24 a hub létrehozásához, ami azt jelenti, hogy a/25-től/32-ig terjedő minden tartomány hibát eredményez a létrehozás során. Az Azure Virtual WAN a Microsoft által felügyelt szolgáltatás, amely a megfelelő alhálózatokat hozza létre a virtuális központban a különböző átjárók/szolgáltatások számára. (Például: hálózati virtuális berendezések, VPN-átjárók, ExpressRoute-átjárók, felhasználói VPN/pont – hely átjárók, tűzfal, útválasztás stb.). Nincs szükség arra, hogy a felhasználó explicit módon tervezze meg a virtuális központ szolgáltatásainak alhálózati címterület-területét, mert a Microsoft ezt a szolgáltatást részeként kezeli.
1. Válassza a **felülvizsgálat + létrehozás** elemet az ellenőrzéshez.
1. Válassza a **Létrehozás** lehetőséget a központ létrehozásához.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>A hálózati virtuális berendezés létrehozása a központban

Ebben a lépésben egy hálózati virtuális berendezést fog létrehozni a központban. Az egyes NVA vonatkozó eljárás minden NVA-partner terméke esetében eltérő lesz. Ebben a példában egy Barracuda CloudGen WAN-átjárót hozunk létre.

1. Keresse meg az előző lépésben létrehozott virtuális WAN-hubot, és nyissa meg.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Virtuális központ":::
1. Keresse meg a hálózati virtuális berendezések csempét, és válassza a **Létrehozás** hivatkozást.
1. A **hálózati virtuális berendezés** panelen válassza a **Barracuda CloudGen WAN** elemet, majd kattintson a **Létrehozás** gombra.

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="NVA kiválasztása":::
1. Ekkor megnyílik a Barracuda CloudGen WAN-átjáró Azure Marketplace-ajánlata. Olvassa el a használati feltételeket, majd válassza a **Létrehozás** gombot, ha elkészült.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Barracuda NVA alapjai":::
1. Az **alapok** lapon a következő információkat kell megadnia:

   * **Előfizetés** – válassza ki a virtuális WAN és hub üzembe helyezéséhez használt előfizetést.
   * **Erőforráscsoport** – válassza ki ugyanazt az erőforráscsoportot, amelyet a virtuális WAN és a hub üzembe helyezéséhez használt.
   * **Régió** – válassza ki azt a régiót, amelyben a virtuális központ erőforrása található.
   * **Alkalmazás neve** – a BARRACUDA NextGen WAN egy felügyelt alkalmazás. Olyan nevet adjon meg, amely megkönnyíti az erőforrás azonosítását, mivel ezt fogja meghívni, amikor megjelenik az előfizetésében.
   * **Felügyelt erőforráscsoport** – ez az a felügyelt erőforráscsoport neve, amelyben a Barracuda telepíti az általuk felügyelt erőforrásokat. Ezt a nevet előre ki kell tölteni.
1. Válassza a **Next: CLOUDGEN WAN Gateway (tovább** ) gombot.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen WAN-átjáró":::
1. Adja meg a következő információkat:

   * **Virtual WAN hub** – a virtuális WAN hub, amelyre a NVA telepíteni kívánja.
   * **NVA infrastruktúra-egységek** – Itt adhatja meg, hogy hány NVA infrastruktúra-egységre kívánja telepíteni ezt a NVA. Válassza ki azt az összesített sávszélesség-kapacitást, amelyet az összes olyan fiókirodában meg szeretne adni, amely ezen a hubhoz csatlakozik ezen a NVA keresztül.
   * A **token** -Barracuda megköveteli, hogy itt hitelesítő jogkivonatot adjon meg, hogy azonosítsa magát a termék regisztrált felhasználója számára. Ezt a Barracuda-től kell megszereznie.
1. A folytatáshoz kattintson a **felülvizsgálat és létrehozás** gombra.
1. Ezen az oldalon a Co-Admin hozzáférési szerződés feltételeinek elfogadását kéri a rendszer. Ez a szabványos olyan felügyelt alkalmazásokkal, amelyekben a közzétevő hozzáférhet a központi telepítés egyes erőforrásaihoz. Jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>A VNet összekötése a hubhoz

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Mi a virtuális WAN?](virtual-wan-about.md) lapon talál.
* Ha többet szeretne megtudni a virtuális WAN-központban található NVA, tekintse meg [a virtuális WAN-központ (előzetes verzió) hálózati virtuális berendezéssel](about-nva-hub.md)foglalkozó témakörét.
