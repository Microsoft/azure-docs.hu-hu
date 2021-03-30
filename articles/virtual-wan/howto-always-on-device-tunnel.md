---
title: Always-On VPN-alagút konfigurálása
titleSuffix: Azure Virtual WAN
description: Az Always On VPN-eszközök bújtatásának konfigurálása a virtuális WAN-hoz
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983690"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Always On VPN-eszköz bújtatásának konfigurálása virtuális WAN-hoz

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

Létre kell hoznia egy pont – hely konfigurációt, és szerkesztenie kell a virtuális központ hozzárendelését. A következő részekben talál útmutatást:

* [Pont–hely konfiguráció létrehozása](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Hub létrehozása a P2S-átjáróval](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Az eszköz bújtatásának konfigurálása

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Profil eltávolítása

A profil eltávolításához futtassa a következő parancsot:

![A képernyőképen egy PowerShell-ablak látható, amely a Remove-VpnConnection-Name MachineCertTest parancsot futtatja.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.