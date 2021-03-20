---
title: Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az OpenVPN-t az Azure VPN Gateway Windows, Linux és Mac operációs rendszerű ügyfelek számára.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435779"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>OpenVPN-ügyfelek konfigurálása az Azure VPN Gatewayhez

Ez a cikk segítséget nyújt az **OpenVPN &reg; protokoll** ügyfeleinek konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek

Ellenőrizze, hogy elvégezte-e a VPN-átjáróhoz tartozó OpenVPN konfigurálásához szükséges lépéseket. Részletekért lásd: [az OpenVPN konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, hogy a VPN-ügyfelek hozzáférhessenek egy másik VNet található erőforrásokhoz, kövesse a [VNet – VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikk utasításait egy VNet-VNet kapcsolat beállításához. Ügyeljen arra, hogy az átjárók és a kapcsolatok esetében engedélyezze a BGP-t, ellenkező esetben a forgalom nem fog folytatódni.

**Az "OpenVPN" az OpenVPN Inc védjegye.**
