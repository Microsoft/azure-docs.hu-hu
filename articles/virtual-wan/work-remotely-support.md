---
title: Azure virtuális WAN és távoli munka
description: Ez az oldal azt ismerteti, hogyan használhatja az Azure Virtual WAN-t a COVID-19 világjárvány miatti távoli működéshez.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023490"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure virtuális WAN és a távoli működés támogatása

>[!NOTE]
>Ez a cikk azt ismerteti, hogyan használhatja az Azure Virtual WAN, az Azure, a Microsoft Network és az Azure partner-ökoszisztémát a COVID-19 válság miatt felmerülő hálózati problémák megoldásához.
>

Felhasználja a kapcsolatot a távoli felhasználók számára?
Hirtelen látni szeretné, hogy az Ön által tervezettnél nagyobb mennyiségű felhasználót kell támogatni?
Szüksége van a felhasználó otthonról való kapcsolódásra, és nem csak a felhőhöz, hanem a helyszínen is elérhető?
Szüksége van a távoli felhasználók számára, hogy elérhetők legyenek a privát WAN mögötti erőforrások?
Szükség van-e a felhasználók számára a Felhőbeli erőforrásokhoz való hozzáférésre anélkül, hogy be kellene állítania a régiók közötti kapcsolatot?
Mivel ez a globális világjárvány példátlan változásokat teremt az USA-ban, az Azure-beli virtuális WAN csapata itt gondoskodik a kapcsolati igények kielégítéséről.

Az Azure Virtual WAN egy hálózati szolgáltatás, amely számos hálózati, biztonsági és útválasztási funkciót biztosít, így egyetlen operatív felületet biztosíthat. Ezen funkciók közé tartoznak az ág-kapcsolat (a virtuális WAN-partneri eszközöktől, például az SD-WAN vagy a VPN CPE-től származó kapcsolat automatizálásán keresztül), helyek közötti VPN-kapcsolat, a távoli felhasználói VPN (pont – hely) kapcsolat, a magánhálózati (ExpressRoute) kapcsolat, a felhőn belüli kapcsolat (virtuális hálózatok tranzitív kapcsolata), a VPN-ExpressRoute közötti kapcsolat, az Útválasztás, az Azure tűzfal, a privát kapcsolatok titkosítása stb. A virtuális WAN használatának megkezdéséhez nem szükséges az összes ilyen használati eset. Első lépésként csak egy használati esetet érhet el, és úgy állíthatja be a hálózatot, ahogy az fejlődik.

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwan1.png)

Most, hogy a távoli felhasználókról beszél, megtekintheti, hogy mire van szüksége a hálózat működésének megkezdéséhez:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Távoli felhasználói kapcsolat beállítása

Az Azure-beli erőforrásokhoz IPsec/IKE (IKEv2) vagy OpenVPN-kapcsolat használatával kapcsolódhat. Az ilyen típusú kapcsolathoz VPN-ügyfélre van szükség a távoli felhasználó számára. Ez az ügyfél lehet az [Azure VPN-ügyfél](https://go.microsoft.com/fwlink/?linkid=2117554) vagy az OpenVPN-ügyfél, vagy bármely olyan ügyfél, amely támogatja a IKEv2. További információ: [pont – hely kapcsolat létrehozása](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Kapcsolódás a távoli felhasználó és a helyszíni környezet között

Itt két lehetőség közül választhat:

* Helyek közötti kapcsolat beállítása bármely meglévő VPN-eszközzel. Amikor az IPsec VPN-eszközt az Azure Virtual WAN hubhoz csatlakoztatja, a pont – hely felhasználói VPN (távoli felhasználó) és a helyek közötti VPN közötti kapcsolat automatikusan megtörténik. A helyek közötti VPN a helyi VPN-eszközről az Azure Virtual WAN-ba való beállításával kapcsolatos további információkért lásd: [helyek közötti kapcsolat létrehozása virtuális WAN használatával](virtual-wan-site-to-site-portal.md).

* Csatlakoztathatja a ExpressRoute-áramkört a virtuális WAN-hubhoz. A ExpressRoute-áramkör csatlakoztatásához egy ExpressRoute-átjárót kell telepíteni a virtuális WAN-ban. Amint üzembe helyezte az egyiket, a pont – hely felhasználói VPN-és ExpressRoute-felhasználó közötti kapcsolat automatikus. A ExpressRoute-kapcsolat létrehozásával kapcsolatban lásd: [ExpressRoute-kapcsolat létrehozása virtuális WAN használatával](virtual-wan-expressroute-portal.md). Az Azure Virtual WAN-hoz való kapcsolódáshoz meglévő ExpressRoute-áramkört használhat.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Meglévő alapszintű virtuális WAN-ügyfél

Az alapszintű virtuális WAN csak helyek közötti VPN-t biztosít. Ahhoz, hogy a távoli felhasználók csatlakozni tudjanak, frissítenie kell a virtuális WAN-t a standard virtuális WAN-ra. A virtuális WAN verziófrissítésének lépéseiért lásd: [virtuális WAN frissítése alapszintről standard verzióra](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>További információ

A virtuális WAN régiónként/helyenként egy hubot támogat. A tartózkodási helyről a [virtuális WAN-partnerek és-helyek](virtual-wan-locations-partners.md) című cikkben talál további információt. Minden központ legfeljebb 10 000 távoli felhasználói kapcsolatot, 1 000 ág-kapcsolatot, négy ExpressRoute-áramkört és legfeljebb 500 Virtual Network kapcsolatot támogat. Ha bármilyen kérdése van, a távoli felhasználók vertikális felskálázása esetén ne habozzon, hogy küldjön e-mailt a címre azurevirtualwan@microsoft.com . Ha technikai támogatásra van szüksége, győződjön meg arról, hogy a Azure Portal egy támogatási jegyet nyit, és a Súgó a következőképpen fog megjelenni.

## <a name="faq"></a><a name="faq"></a>GYIK

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a [virtuális WAN áttekintése](virtual-wan-about.md) című témakörben talál.