---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93042579"
---
Ugyanazt a VPN-ügyfél-konfigurációs csomagot használhatja minden Windows-ügyfélszámítógépen, ha a verzió megfelel az ügyfél architektúrájának. A támogatott ügyféloldali operációs rendszerek listáját a [VPN Gateway gyakori kérdések](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)pont – hely szakasza tartalmazza.

>[!NOTE]
>Rendszergazdai jogosultságokkal kell rendelkeznie azon a Windows-ügyfélszámítógépen, amelyről csatlakozni szeretne.
>

A következő lépésekkel konfigurálhatja a natív Windows VPN-ügyfelet a tanúsítványalapú hitelesítéshez:

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza. 
1. Kattintson duplán a csomagra a telepítéséhez. Ha megjelenik a SmartScreen egy előugró ablaka, kattintson a **További információ**, majd a **Futtatás mindenképpen** elemre.
1. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.
1. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség.