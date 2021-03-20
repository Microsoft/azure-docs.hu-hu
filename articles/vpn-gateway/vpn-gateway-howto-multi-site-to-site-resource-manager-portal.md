---
title: 'Több VPN Gateway-helyek közötti kapcsolat hozzáadása egy VNet: Azure Portal'
description: Többhelyes S2S-kapcsolatok hozzáadása meglévő kapcsolattal rendelkező VPN-átjáróhoz
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890148"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>További S2S-kapcsolatok hozzáadása egy VNet: Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>

Ez a cikk segítséget nyújt további helyek közötti (S2S) kapcsolatok hozzáadásához egy meglévő kapcsolattal rendelkező VPN-átjáróhoz. Ezt az architektúrát gyakran "többhelyes" konfigurációnak nevezzük. Hozzáadhat S2S-kapcsolatokat olyan VNet, amely már rendelkezik S2S kapcsolattal, pont – hely kapcsolattal vagy VNet – VNet kapcsolattal. A kapcsolatok hozzáadásakor bizonyos korlátozások vonatkoznak. A konfigurálás megkezdése előtt ellenőrizze a cikk [előfeltételeinek](#before) szakaszát.

Ez a cikk olyan Resource Manager-virtuális hálózatok vonatkozik, amelyek Útvonalalapú VPN-átjáróval rendelkeznek. Ezek a lépések nem vonatkoznak az új ExpressRoute vagy helyek közötti kapcsolati konfigurációkra. Ha azonban csupán új VPN-kapcsolattal bővít egy már meglévő, egyidejű konfigurációt, ezeket a lépéseket használhatja. Az egyidejű kapcsolatokkal kapcsolatos információkért lásd: [ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) egyidejű kapcsolatok.

## <a name="prerequisites"></a><a name="before"></a>Előfeltételek

Ellenőrizze a következő elemeket:

* Nem konfigurál új, meglévő ExpressRoute és VPN Gateway konfigurációt.
* Van olyan virtuális hálózata, amely a Resource Manager-alapú üzemi modellel lett létrehozva egy meglévő kapcsolatban.
* A VNet virtuális hálózati átjárója Útvonalalapú. Ha házirendalapú VPN-átjáróval rendelkezik, törölnie kell a virtuális hálózati átjárót, és létre kell hoznia egy új VPN-átjárót Útvonalalapú.
* A címtartományok egyike sem fedi át a virtuális hálózatok, amelyhez ez a VNet csatlakozik.
* Kompatibilis a VPN-eszközzel, és valaki, aki be tudja állítani. Lásd: [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Tudnivalók a VPN-eszközökről). Ha nem jártas a VPN-eszköz konfigurálásában, vagy nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, együtt kell működnie valakivel, aki ezeket az adatokat megadhatja Önnek.
* A VPN-eszközhöz külsőleg megtekintett nyilvános IP-címet kell megnéznie.

## <a name="configure-a-connection"></a><a name="configure"></a>Kapcsolatok konfigurálása

1. Egy böngészőből keresse fel az [Azure Portalt](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
1. Válassza ki az **összes erőforrást** , és keresse meg a **virtuális hálózati átjárót** az erőforrások listájából, és jelölje ki.
1. A **virtuális hálózati átjáró** lapon válassza a **kapcsolatok** lehetőséget.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN Gateway-kapcsolatok":::
1. A **kapcsolatok** lapon válassza a **+ Hozzáadás** lehetőséget.
1. Ekkor megnyílik a **kapcsolatok hozzáadása** lap.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="A kapcsolatok hozzáadása lap":::
1. A **kapcsolatok hozzáadása** lapon töltse ki a következő mezőket:

   * **Név:** Az a név, amelyet a helyhez szeretne adni, amelyhez a kapcsolódást hozza létre.
   * **Kapcsolattípus:** Válassza **a helyek közötti (IPSec)** lehetőséget.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Helyi hálózati átjáró hozzáadása

1. A **helyi hálózati átjáró** mezőben válassza ki **_a helyi hálózati átjáró_(*_) lehetőséget. Ekkor megnyílik a* helyi hálózati átjáró kiválasztása** lap.
1. Válassza az **+ új létrehozása** lehetőséget a **helyi hálózati átjáró létrehozása** lap megnyitásához.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Helyi hálózati átjáró létrehozása lap":::
1. A **helyi hálózati átjáró létrehozása** lapon töltse ki a következő mezőket:

   * **Név:** A helyi hálózati átjáró erőforrásához adni kívánt név.
   * **Végpont:** Azon a helyen található VPN-eszköz nyilvános IP-címe, amelyhez csatlakozni szeretne, vagy a végpont teljes tartományneve.
   * **Címterület:** Az új helyi hálózati helyre átirányítani kívánt címtartomány.
1. A módosítások mentéséhez kattintson az **OK gombra** a **helyi hálózati átjáró létrehozása** lapon.

## <a name="add-the-shared-key"></a><a name="part3"></a>A megosztott kulcs hozzáadása

1. A helyi hálózati átjáró létrehozása után térjen vissza a **Kapcsolódás hozzáadása** lapra.
1. Fejezze be a fennmaradó mezőket. A **megosztott kulcs (PSK)** esetében beolvashatja a megosztott kulcsot a VPN-eszközről, vagy létrehozhat egyet, majd beállíthatja, hogy a VPN-eszköz ugyanazt a megosztott kulcsot használja. A lényeg az, hogy a kulcsok pontosan ugyanazok.

## <a name="create-the-connection"></a><a name="create"></a>A kapcsolat létrehozása

1. A lap alján kattintson az **OK gombra** a kapcsolódás létrehozásához. A létrehozás azonnal elindul.
1. A kapcsolatok befejeződése után megtekintheti és ellenőrizheti.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>A VPN-kapcsolat megtekintése és ellenőrzése

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Következő lépések

Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információ: [Virtual Machines learning paths](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
