---
title: 'VPN Gateway: az átjáró IP-címének beállításainak módosítása: Azure Portal'
description: Ez a cikk végigvezeti a helyi hálózati átjáró IP-címének előtagjainak a Azure Portal használatával történő módosításán.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143118"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Helyi hálózati átjáró beállításainak módosítása az Azure Portal használatával

A helyi hálózati átjáró AddressPrefix vagy GatewayIPAddress vonatkozó beállítások néha változnak. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszer használatával is módosíthatja, ha a következő listából választ egy másik lehetőséget:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Helyi hálózati átjáró konfigurációja

Az alábbi képernyőképen egy helyi hálózati átjáró erőforrásának **konfigurációs** lapja látható nyilvános IP-cím végpont használatával:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="IP-cím beállításai" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Ez a konfigurációs oldal FQDN-végponttal:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="FQDN-beállítások":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Az átjáró IP-címének vagy teljes tartománynevének módosítása

> [!NOTE]
> Helyi hálózati átjáró nem módosítható FQDN-végpont és IP-cím végpontja között. Törölnie kell az ehhez a helyi hálózati átjáróhoz társított összes kapcsolatot, létre kell hoznia egy újat az új végponttal (IP-cím vagy teljes tartománynév), majd újra létre kell hoznia a kapcsolatokat.
>

Ha a VPN-eszköz, amelyhez csatlakozni szeretne, megváltoztatta a nyilvános IP-címét, módosítsa a helyi hálózati átjárót a következő lépésekkel:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában válassza a **Konfigurálás** lehetőséget.
2. Az **IP-cím** mezőben módosítsa az IP-címet.
3. A beállítások mentéséhez válassza a **Mentés** lehetőséget.

Ha a VPN-eszköz, amelyhez csatlakozni szeretne, megváltoztatta a teljes tartománynevet (teljes tartománynév), módosítsa a helyi hálózati átjárót a következő lépésekkel:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában válassza a **Konfigurálás** lehetőséget.
2. A **teljes** tartománynév mezőben módosítsa a tartománynevet.
3. A beállítások mentéséhez válassza a **Mentés** lehetőséget.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-címek előtagjainak módosítása

További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában válassza a **Konfigurálás** lehetőséget.
2. Adja hozzá az IP-címtartományt a *további címtartomány hozzáadása* mezőben.
3. A beállítások mentéséhez kattintson a **Mentés** gombra.

Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában válassza a **Konfigurálás** lehetőséget.
2. Válassza a **"..."** lehetőséget az eltávolítani kívánt előtagot tartalmazó sorban.
3. Válassza az **Eltávolítás** lehetőséget.
4. A beállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>A BGP-beállítások módosítása

BGP-beállítások hozzáadása vagy frissítése:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában válassza a **Konfigurálás** lehetőséget.
2. A **"BGP-beállítások konfigurálása"** lehetőség kiválasztásával megjelenítheti vagy frissítheti a helyi hálózati átjáró BGP-konfigurációit
3. Az autonóm rendszerszám vagy BGP-társ IP-cím hozzáadása vagy frissítése a megfelelő mezőkben
4. A beállítások mentéséhez kattintson a **Mentés** gombra.

A BGP-beállítások eltávolítása:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában válassza a **Konfigurálás** lehetőséget.
2. A **"BGP-beállítások konfigurálása"** lehetőség kiválasztásával távolítsa el a meglévő BGP ASN-és BGP-társ IP-címét
3. A beállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

Ellenőrizheti az átjáró-kapcsolatokat. Lásd: [átjáró-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).
