---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9caf63fc90be7bae0461ddc24c94594a32199765
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91812704"
---
#### <a name="microsoft-windows"></a>Microsoft Windows

##### <a name="openvpn"></a>OpenVPN

1. Töltse le az OpenVPN-ügyfelet a hivatalos webhelyről, majd telepítse.
1. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal felhasználói VPN-konfigurációk lapján végezheti el, vagy New-AzureRmVpnClientConfiguration a PowerShellben.
1. Csomagolja ki a profilt. Nyissa meg az OpenVPN mappában található vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben.
1. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. A PEM formátumú tanúsítványokban megnyithatja a. cer fájlt, és átmásolhatja a Base64-kulcsot a tanúsítvány fejlécei között. A lépéseket lásd: [Tanúsítvány exportálása a kódolt nyilvános kulcs lekéréséhez.](../articles/virtual-wan/certificates-point-to-site.md)
1. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A lépéseket lásd: [titkos kulcs kibontása.](../articles/virtual-wan/howto-openvpn-clients.md#windows).
1. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
1. Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
1. Kattintson a jobb gombbal a tálcán található OpenVPN ikonra, majd válassza a **kapcsolat** lehetőséget.

##### <a name="ikev2"></a>IKEv2

1. Válassza ki a Windows rendszerű számítógép architektúrájának megfelelő VPN-ügyfélkonfigurációs fájlokat. 64 bites processzorarchitektúra esetén a „VpnClientSetupAmd64” telepítőcsomagot válassza. 32 bites processzorarchitektúra esetén a „VpnClientSetupX86” telepítőcsomagot válassza.
1. Kattintson duplán a csomagra a telepítéséhez. Ha egy SmartScreen előugró ablak jelenik meg, válassza a **További információ**, majd a **Futtatás amúgy parancsot**.
1. Az ügyfélszámítógépen navigáljon a **hálózati beállítások** elemre, és válassza a **VPN** lehetőséget. A VPN-kapcsolat megjeleníti annak a virtuális hálózatnak a nevét, amelyhez csatlakozott.
1. Mielőtt megkísérli a csatlakozást, ellenőrizze, hogy telepített-e ügyféltanúsítványt az ügyfélszámítógépen. A natív Azure-tanúsítványhitelesítési típus használata esetén a hitelesítéshez ügyféltanúsítványra van szükség. A tanúsítványok létrehozásával kapcsolatos további információkért lásd: [tanúsítványok létrehozása](../articles/virtual-wan/certificates-point-to-site.md). Az ügyféltanúsítványok telepítésével kapcsolatos információkért lásd: [ügyféltanúsítvány telepítése](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md).
