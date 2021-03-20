---
title: OpenVPN-ügyfelek konfigurálása az Azure Virtual WAN-hoz
description: Az Azure Virtual WAN-hoz készült OpenVPN-ügyfelek konfigurálásának lépései
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491001"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>OpenVPN-ügyfél konfigurálása az Azure Virtual WAN-hoz

Ez a cikk segítséget nyújt az **OpenVPN &reg; protokoll** ügyfeleinek konfigurálásához. A Windows 10-es Azure VPN-ügyfelét is használhatja az OpenVPN protokollon keresztüli csatlakozáshoz. További útmutatást [itt](openvpn-azure-ad-client.md) találhat

## <a name="before-you-begin"></a>Előkészületek

Hozzon létre egy felhasználói VPN (pont – hely) konfigurációt. Győződjön meg arról, hogy az "OpenVPN" lehetőséget választja az alagút típusaként. A lépéseket lásd: [P2S-konfiguráció létrehozása az Azure Virtual WAN-hoz](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Következő lépések

A felhasználói VPN-kapcsolatokról (pont – hely) a [felhasználói VPN-kapcsolatok létrehozása](virtual-wan-point-to-site-portal.md)című témakörben olvashat bővebben.

**Az "OpenVPN" az OpenVPN Inc védjegye.**
