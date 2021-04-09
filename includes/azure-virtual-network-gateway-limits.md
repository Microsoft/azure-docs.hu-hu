---
title: fájl belefoglalása
description: fájl belefoglalása
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554945"
---
| Erőforrás                                | Korlát        |
|-----------------------------------------|------------------------------|
| VNet-címek előtagjai                   | 600/VPN-átjáró          |
| Összesített BGP-útvonalak                    | 4 000/VPN-átjáró        |
| Helyi hálózati átjáró címeinek előtagjai  | 1000/helyi hálózati átjáró               |
| S2S-kapcsolatok                         | [Az átjáró SKU-jának függ](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Pont–hely kapcsolatok                         | [Az átjáró SKU-jának függ](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| P2S-útvonal korlátja – IKEv2                 | 256 nem Windows **/** 25 Windows rendszerhez           |
| P2S-útvonal korlátja – OpenVPN               | 1000                         |
| Legfeljebb folyamatok                              | 100K a VpnGw1/AZ  **/**  512K for VpnGw2-4/az|