---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178943"
---
>[!NOTE]
>2018. július 1-től az Azure VPN Gatewayből el lett távolítva a TLS 1.0 és 1.1 támogatása. Ettől kezdve az Azure VPN Gateway csak a TLS 1.2-es verzióját támogatja. A támogatás fenntartásához tekintse meg a [TLS1.2 támogatásának engedélyezéséhez szükséges frissítéseket.](#tls1)

2018. július 1-jén a következő régebbi algoritmusok is elavultak lesznek a TLS-ben:

* RC4 (Rivest Cipher 4)
* DES (adattitkosítási algoritmus)
* 3DES (háromszoros adattitkosítási algoritmus)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Hogyan engedélyezhetem a TLS 1.2 támogatását windows 7 és Windows 8.1 rendszerben?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
