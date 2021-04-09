---
title: Virtuális hálózatok közötti társviszony-létesítési problémák hibaelhárítása
description: A legtöbb virtuális hálózati társítási probléma megoldásához szükséges lépések.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200531"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Virtuális hálózatok közötti társviszony-létesítési problémák hibaelhárítása

Ez a hibaelhárítási útmutató a legtöbb [virtuális hálózati](virtual-network-peering-overview.md) társítási probléma megoldásához nyújt segítséget.

![Virtuális hálózat társításának ábrája](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Virtuális hálózati társítás konfigurálása két virtuális hálózat között

Azonos előfizetésben vagy eltérő előfizetésben található virtuális hálózatok?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>A virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak.

Az azonos előfizetésben található virtuális hálózatok virtuális hálózati társításának konfigurálásához használja az alábbi cikkekben ismertetett módszereket:

* Ha a virtuális hálózatok *ugyanabban a régióban* találhatók, tekintse meg a társítás [létrehozása](./virtual-network-manage-peering.md#create-a-peering)című témakört.
* Ha a virtuális hálózatok a *különböző régiókban* találhatók, tekintse meg a [virtuális hálózat](./virtual-network-peering-overview.md)társítása című témakört. 

> [!Note]
> A kapcsolat nem működik a globális virtuális hálózati kapcsolatok esetében a következő erőforrásokhoz: 
>
> * Virtuális gépek (VM) az alapszintű belső terheléselosztó (ILB) SKU mögött
> * Redis cache (alapszintű ILB SKU-t használ)
> * Application Gateway v1 (alapszintű ILB SKU-t használ)
> * Virtuálisgép-méretezési csoportok (alapszintű ILB SKU-t használ)
> * Azure Service Fabric-fürtök (alapszintű ILB SKU-t használ)
> * SQL Server always on (alapszintű ILB SKU-t használ)
> * PowerApps-Azure App Service Environment (alapszintű ILB SKU-t használ)
> * Azure API Management (alapszintű ILB SKU-t használ)
> * Azure Active Directory Domain Services (Azure AD DS) (alapszintű ILB SKU-t használ)

További információért lásd a globális társítás [követelményeit és korlátozásait](./virtual-network-peering-overview.md#requirements-and-constraints) .

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>A virtuális hálózatok különböző előfizetésekben vagy Active Directory-bérlőben találhatók

Ha a virtuális hálózatokat különböző előfizetésekben vagy Active Directory-bérlőben lévő virtuális hálózatokhoz szeretné konfigurálni, tekintse meg a következő témakört: társítás [létrehozása különböző előfizetésekben az Azure CLI-hez](./create-peering-different-subscriptions.md#cli).

> [!Note]
> A hálózati társítás konfigurálásához a hálózat **közreműködői** engedélyekkel kell rendelkeznie mindkét előfizetésben. További információ: [egyenrangú engedélyek](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>A virtuális hálózati topológia konfigurálása a helyi erőforrásokat használó sugaras topológiával

![A helyszíni küllős virtuális hálózatok összevonásának ábrája](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Helyek közötti kapcsolathoz vagy ExpressRoute-kapcsolathoz

Kövesse a következő témakörben ismertetett lépéseket: [VPN Gateway tranzit beállítása virtuális hálózatokhoz](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Pont – hely kapcsolatok esetén

1. Kövesse a következő témakörben ismertetett lépéseket: [VPN Gateway tranzit beállítása virtuális hálózatokhoz](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. A virtuális hálózati társítás létrehozása vagy módosítása után töltse le és telepítse újra a pont – hely típusú csomagokat, hogy a pont – hely ügyfelek megkapják a frissített útvonalakat a küllős virtuális hálózatnak.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Virtuális hálózati társítás konfigurálása sugaras topológiai virtuális hálózattal

![A virtuális hálózatok egymással való társításának ábrája küllős](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>A virtuális hálózatok ugyanabban a régióban találhatók


1. A hub virtuális hálózatban állítson be egy hálózati virtuális berendezést (NVA).
1. A küllős virtuális hálózatokban a felhasználó által megadott útvonalakat a következő ugrás típusú "hálózati virtuális berendezés" alkalmazta.

További információ: [Service láncolása](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Ha segítségre van szüksége egy NVA beállításához, [forduljon a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655).

A NVA-eszközök beállításával és útválasztásával kapcsolatos segítségért lásd: a [hálózati virtuális berendezések problémái az Azure-ban](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>A virtuális hálózatok különböző régiókban találhatók

A globális virtuális hálózati kapcsolaton keresztüli átvitel mostantól támogatott. A kapcsolódás nem működik a globális virtuális hálózati kapcsolaton keresztül a következő erőforrásokhoz:

* Alapszintű ILB-termékváltozat mögötti virtuális gépek
* Redis cache (alapszintű ILB SKU-t használ)
* Application Gateway (alapszintű ILB SKU-t használ)
* Méretezési csoportok (alapszintű ILB SKU-t használ)
* Service Fabric-fürtök (alapszintű ILB-termékváltozatot használnak)
* SQL Server always on (alapszintű ILB SKU-t használ)
* App Service Environment (alapszintű ILB SKU-t használ)
* API Management (alapszintű ILB-termékváltozatot használ)
* Azure AD DS (alapszintű ILB SKU-t használ)

Ha többet szeretne megtudni a globálisan megjelenő követelményekről és a korlátozásokról, tekintse meg a [virtuális hálózat](./virtual-network-peering-overview.md#requirements-and-constraints)társítása című témakört.

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Két egymással összekapcsolt virtuális hálózat közötti csatlakozási probléma megoldása

Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) egy olyan fiókkal, amely rendelkezik a szükséges [szerepkörökkel és engedélyekkel](virtual-network-manage-peering.md#permissions). Válassza ki a virtuális hálózatot, válassza a **társítás lehetőséget,** majd jelölje be az **állapot** mezőt. Mi az az állapot?

### <a name="the-peering-status-is-connected"></a>Az egyenrangú állapot "Connected"

A probléma megoldásához:

1. A hálózati forgalom forgalmának ellenõrzése:

   [Kapcsolódási hibák](../network-watcher/network-watcher-connectivity-overview.md) és [IP-forgalom ellenőrzése](../network-watcher/network-watcher-ip-flow-verify-overview.md) a forrás virtuális gépről a cél virtuális gépre annak megállapításához, hogy van-e olyan NSG vagy UDR, amely interferenciát okoz a forgalmi folyamatokban.

   Ha tűzfalat vagy NVA használ: 
   1. Dokumentálja a UDR paramétereket, hogy a lépés befejezése után vissza tudja állítani őket.
   2. Távolítsa el a UDR a forrás virtuálisgép-alhálózatról vagy hálózati adapterről, amely a NVA mutat a következő ugrásként. Győződjön meg arról, hogy a forrás virtuális gép kapcsolata közvetlenül a célhelyre kerül, amely megkerüli a NVA. Ha ez a lépés nem működik, tekintse meg a [NVA-hibakeresőt](./virtual-network-troubleshoot-nva.md).

2. Hálózati nyomkövetés készítése: 
   1. Indítsa el a hálózati nyomkövetést a cél virtuális gépen. A Windows rendszerben a **netsh** használható. Linux esetén használja a **TCPDump**-t.
   2. Futtassa a **TcpPing** vagy a **PsPing** a FORRÁSRÓL a cél IP-címhez.

      Ez egy példa egy **TcpPing** -parancsra: `tcping64.exe -t <destination VM address> 3389`

   3. A **TcpPing** befejezése után állítsa le a hálózati nyomkövetést a célhelyen.
   4. Ha a csomagok érkeznek a forrásból, nincs hálózati probléma. Vizsgálja meg a virtuálisgép-tűzfalat és a portot figyelő alkalmazást a konfigurációs probléma megkereséséhez.

   > [!Note]
   > Nem lehet csatlakozni a következő típusú erőforrásokhoz a globális virtuális hálózati kapcsolaton keresztül (a különböző régiókban található virtuális hálózatok esetében):
   >
   > * Alapszintű ILB-termékváltozat mögötti virtuális gépek
   > * Redis cache (alapszintű ILB SKU-t használ)
   > * Application Gateway (alapszintű ILB SKU-t használ)
   > * Méretezési csoportok (alapszintű ILB SKU-t használ)
   > * Service Fabric-fürtök (alapszintű ILB-termékváltozatot használnak)
   > * SQL Server always on (alapszintű ILB SKU-t használ)
   > * App Service Environment (alapszintű ILB SKU-t használ)
   > * API Management (alapszintű ILB-termékváltozatot használ)
   > * Azure AD DS (alapszintű ILB SKU-t használ)

További információért lásd a globális társítás [követelményeit és korlátozásait](./virtual-network-peering-overview.md#requirements-and-constraints) .

### <a name="the-peering-status-is-disconnected"></a>A társítás állapota "leválasztva"

A probléma megoldásához törölje a társítást mindkét virtuális hálózatról, majd hozza létre újra.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Egy sugaras virtuális hálózat és egy helyszíni erőforrás közötti csatlakozási probléma megoldása

A hálózata harmadik féltől származó NVA vagy VPN-átjárót használ?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>A hálózat egy harmadik féltől származó NVA-vagy VPN-átjárót használ

A harmadik féltől származó NVA vagy VPN-átjárót érintő csatlakozási problémák elhárításához tekintse meg a következő cikkeket:

* [NVA-hibakereső](./virtual-network-troubleshoot-nva.md)
* [Szolgáltatásláncolás](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>A saját hálózat nem használ külső gyártótól származó NVA-vagy VPN-átjárót

A hub virtuális hálózatának és a küllős virtuális hálózatnak van VPN-átjárója?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>A hub virtuális hálózatnak és a küllős virtuális hálózatnak is van VPN-átjárója

Távoli átjáró használata nem támogatott.

Ha a küllő virtuális hálózat már rendelkezik egy VPN-átjáróval, a küllős virtuális hálózat nem támogatja a **távoli átjáró használata** lehetőséget. Ennek oka egy virtuális hálózati társítási korlátozás.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>A hub virtuális hálózatának és a küllős virtuális hálózatnak sem van VPN-átjárója.

Helyek közötti vagy Azure ExpressRoute-kapcsolatok esetén a helyi kapcsolati problémák alábbi elsődleges okait ellenőrizheti a távoli virtuális hálózattal:

* Az átjáróval rendelkező virtuális hálózaton ellenőrizze, hogy be van-e jelölve a **továbbított forgalom engedélyezése** jelölőnégyzet.
* Győződjön meg arról, hogy az átjárót nem tartalmazó virtuális hálózaton be van jelölve a **távoli átjáró használata** jelölőnégyzet.
* Győződjön meg arról, hogy a hálózati rendszergazda ellenőrzi a helyszíni eszközöket annak ellenőrzéséhez, hogy az összes virtuális hálózati címtartomány hozzá lett-e adva.

Pont – hely kapcsolatok esetén:

* Az átjáróval rendelkező virtuális hálózaton ellenőrizze, hogy be van-e jelölve a **továbbított forgalom engedélyezése** jelölőnégyzet.
* Győződjön meg arról, hogy az átjárót nem tartalmazó virtuális hálózaton be van jelölve a **távoli átjáró használata** jelölőnégyzet.
* Töltse le és telepítse újra a pont – hely ügyfél-csomagot. Az újonnan létrehozott virtuális hálózati útvonalak nem vesznek fel automatikusan útvonalakat a pont – hely ügyfelekhez.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Küllős hálózati kapcsolat problémáinak megoldása az azonos régióban található küllős virtuális hálózatok között

A hub-hálózatnak tartalmaznia kell egy NVA. Konfigurálja a UDR olyan küllőit, amelyek NVA vannak beállítva a következő ugrásként, és engedélyezze a **továbbított forgalom** engedélyezését a hub virtuális hálózaton.

További információ: a [szolgáltatás láncolása](./virtual-network-peering-overview.md#service-chaining), és a követelmények megvitatása a választott [NVA-gyártóval](https://mskb.pkisolutions.com/kb/2984655) .

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Sugaras hálózati kapcsolat problémáinak megoldása különböző régiókban található küllős virtuális hálózatok között

A globális virtuális hálózati kapcsolaton keresztüli átvitel mostantól támogatott. A kapcsolat nem működik a globális virtuális hálózati kapcsolatok esetében a következő erőforrásokhoz:

* Alapszintű ILB-termékváltozat mögötti virtuális gépek
* Redis cache (alapszintű ILB SKU-t használ)
* Application Gateway (alapszintű ILB SKU-t használ)
* Méretezési csoportok (alapszintű ILB SKU-t használ)
* Service Fabric-fürtök (alapszintű ILB-termékváltozatot használnak)
* SQL Server always on (alapszintű ILB SKU-t használ)
* App Service Environment (alapszintű ILB SKU-t használ)
* API Management (alapszintű ILB-termékváltozatot használ)
* Azure AD DS (alapszintű ILB SKU-t használ)

További információért lásd a globális és a [különböző VPN-topológiák](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2) [követelményeit és korlátozásait](./virtual-network-peering-overview.md#requirements-and-constraints) .

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Egy webalkalmazás és a küllős virtuális hálózat közötti, sugaras hálózati kapcsolattal kapcsolatos probléma megoldása

A probléma megoldásához:

1. Jelentkezzen be az Azure Portalra. 
1. A webalkalmazásban válassza a **hálózatkezelés**, majd a **VNet-integráció** elemet.
1. Ellenőrizze, hogy látható-e a távoli virtuális hálózat. Adja meg manuálisan a távoli virtuális hálózati címtartomány (**szinkronizált hálózat** és **útvonalak hozzáadása**).

További információért tekintse át a következő cikkeket:

* [Alkalmazás integrálása egy Azure-beli virtuális hálózattal](../app-service/web-sites-integrate-with-vnet.md)
* [Tudnivalók a pont–hely VPN-útválasztásról](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Virtuális hálózati társ-beállítási konfigurációs hibaüzenet hibáinak megoldása 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>A jelenlegi bérlő `<TENANT ID>` nem jogosult a csatolt előfizetés elérésére

A probléma megoldásához tekintse meg a [peering-Azure CLI létrehozása](./create-peering-different-subscriptions.md#cli)című témakört.

### <a name="not-connected"></a>Nincs kapcsolat

A probléma megoldásához törölje a társítást mindkét virtuális hálózatról, majd hozza létre újra.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Nem sikerült a Databricks virtuális hálózat társítása

A probléma megoldásához konfigurálja a **Azure Databricks** alatt a virtuális hálózat társítását, majd adja meg a cél virtuális hálózatot az **erőforrás-azonosító** használatával. További információ: [Databricks virtuális hálózat létrehozása távoli virtuális hálózathoz](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>A távoli virtuális hálózat nem rendelkezik átjáróval

Ez a probléma akkor fordul elő, ha különböző bérlők és később konfigurálni kívánt egyenrangú virtuális hálózatok vannak `Use Remote Gateways` . A Azure Portal korlátozása, hogy nem tudja ellenőrizni a virtuális hálózati átjárók jelenlétét egy másik bérlő virtuális hálózatában.

A probléma két módon oldható meg:

 * Törölje a társításokat, és aktiválja a `Use Remote Gateways` lehetőséget, amikor új társítást hoz létre.
 * Az engedélyezéshez használja a PowerShellt vagy a CLI-t a Azure Portal helyett `Use Remote Gateways` .

## <a name="next-steps"></a>Következő lépések

* [Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
