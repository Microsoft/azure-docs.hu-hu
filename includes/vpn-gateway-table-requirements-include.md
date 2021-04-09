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
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010795"
---
A következő táblázat a házirendalapú és a Útvonalalapú VPN-átjárók követelményeit sorolja fel. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A klasszikus modell esetében a házirendalapú VPN-átjárók ugyanazok, mint a statikus átjárók, és az útválasztó-alapú átjárók ugyanazok, mint a dinamikus átjárók.

|  | **Alapszintű VPN Gateway házirendalapú** | **Alapszintű VPN Gateway Útvonalalapú** | **Útvonalalapú standard VPN Gateway** | **Útvonalalapú nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Helyek közötti kapcsolat   (S2S)** |Házirendalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztási támogatás (BGP)** (*) |Nem támogatott |Nem támogatott |Támogatott |Támogatott |

  (*) A BGP nem támogatott a klasszikus üzemi modellben.
