---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010823"
---
* **Házirendalapú:** A házirendalapú VPN-eket korábban statikus útválasztási átjáróknak nevezték a klasszikus üzemi modellben. A házirend-alapú VPN-ek az IPsec-alagutakon keresztül titkosítják és irányítják a csomagokat a helyszíni hálózat és az Azure VNet közötti címtartomány-kombinációkkal konfigurált IPsec-házirendek alapján. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-eszköz konfigurációjában. A házirendalapú VPN-típus értéke *házirendalapú*. Házirendalapú VPN használata esetén vegye figyelembe a következő korlátozásokat:
  
  * A házirendalapú VPN-EK **csak** az alapszintű átjáró SKU-ban használhatók. Ez a VPN-típus nem kompatibilis a többi átjáró SKU-val.
  * Házirendalapú VPN használata esetén csak 1 alagút tartozhat.
  * A házirendalapú VPN-eket csak S2S-kapcsolatokhoz használhatja, és csak bizonyos konfigurációkhoz. A legtöbb VPN Gateway konfigurációhoz Útvonalalapú VPN szükséges.
* **Útvonalalapú**: a útvonalalapú VPN-eket korábban dinamikus útválasztási átjáróknak nevezték a klasszikus üzemi modellben. A Útvonalalapú VPN-ek az IP-továbbítás vagy az útválasztási tábla "útvonalak" elemeit használva irányítják a csomagokat a megfelelő bújtatási felületekbe. Az alagútkapcsolatok ezután titkosítják vagy visszafejtik az alagutakba bemenő vagy onnan kijövő csomagokat. A Útvonalalapú VPN-EK házirendje (vagy forgalmi választója) bármely-a-any (vagy Wild Cards) típusúként van konfigurálva. A Útvonalalapú VPN-típus értéke *útvonalalapú*.
