---
title: 'Tanúsítványok előállítása és exportálása pont – hely kapcsolatokhoz: Linux: parancssori felület'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a Linux (alapú strongswan) parancssori felület használatával.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: 0be5bb042649b0fe425077b5b3feb3cea728218c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394006"
---
# <a name="generate-and-export-certificates"></a>Tanúsítványok előállítása és exportálása

A pont – hely kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt a linuxos parancssori felület és a alapú strongswan használatával. Ha más tanúsítványokra vonatkozó utasításokat keres, tekintse meg a [PowerShell](vpn-gateway-certificates-point-to-site.md) -vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) -cikkeket. További információ a alapú strongswan a parancssori felület helyett a GUI használatával történő telepítéséről: az [ügyfél-konfigurációval](point-to-site-vpn-client-configuration-azure-cert.md#install) kapcsolatos cikk lépései.

## <a name="install-strongswan"></a>A alapú strongswan telepítése

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Tanúsítványok előállítása és exportálása

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a pont – hely konfigurációval a VPN- [ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
