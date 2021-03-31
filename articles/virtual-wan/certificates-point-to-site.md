---
title: Tanúsítványok létrehozása és exportálása a felhasználói VPN-kapcsolatokhoz | Azure-beli virtuális WAN
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a felhasználói VPN-kapcsolatokhoz a PowerShell használatával Windows 10 vagy Windows Server 2016 rendszeren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328038"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Tanúsítványok létrehozása és exportálása a felhasználói VPN-kapcsolatokhoz

A felhasználói VPN (pont – hely) kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt a PowerShell használatával Windows 10 vagy Windows Server 2016 rendszeren.

A cikkben ismertetett lépéseket Windows 10 vagy Windows Server 2016 rendszert futtató számítógépen kell végrehajtani. A tanúsítványok létrehozásához használt PowerShell-parancsmagok az operációs rendszer részét képezik, és nem működnek a Windows más verzióin. A Windows 10 vagy Windows Server 2016 rendszerű számítógépeknek csak a tanúsítványok létrehozásához van szükségük. A tanúsítványok létrehozása után feltöltheti őket, vagy telepítheti azokat bármely támogatott ügyféloldali operációs rendszeren.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a [VPN-kapcsolat virtuális WAN-kapcsolatainak lépéseit](virtual-wan-about.md)
