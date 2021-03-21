---
title: 'Azure Virtual WAN: felhasználói VPN-ügyféloldali profilok'
description: Ez segít az ügyfél-profil fájljának működésében
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980915"
---
# <a name="working-with-user-vpn-client-profile-files"></a>A felhasználói VPN-ügyféloldali profilok fájljainak használata

A profil fájljai a VPN-kapcsolat konfigurálásához szükséges információkat tartalmazzák. Ez a cikk segítséget nyújt a felhasználói VPN-ügyfél profiljához szükséges információk beszerzésében és értelmezésében.

## <a name="download-the-profile"></a>A profil letöltése

A [profil letöltése](global-hub-profile.md) című cikkben ismertetett lépéseket követve letöltheti az ügyfél profiljának zip-fájlját.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* Az **OpenVPN-mappa** tartalmazza azt a *ovpn* -profilt, amelyet módosítani kell a kulcs és a tanúsítvány belefoglalásához. További információ: az [OpenVPN-ügyfelek konfigurálása](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Következő lépések

További információ a virtuális WAN felhasználói VPN-ről: [felhasználói VPN-kapcsolat létrehozása](virtual-wan-point-to-site-portal.md).
