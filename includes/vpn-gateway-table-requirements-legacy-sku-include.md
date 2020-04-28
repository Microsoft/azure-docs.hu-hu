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
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179008"
---
A következő táblázat a házirendalapú és a Útvonalalapú VPN-átjárók követelményeit sorolja fel. Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes. A klasszikus modell esetében a házirendalapú VPN-átjárók ugyanazok, mint a statikus átjárók, és az útválasztó-alapú átjárók ugyanazok, mint a dinamikus átjárók.

|  | **Alapszintű VPN Gateway házirendalapú** | **Alapszintű VPN Gateway Útvonalalapú** | **Útvonalalapú standard VPN Gateway** | **Útvonalalapú nagy teljesítményű VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Helyek közötti kapcsolat   (S2S)** |Házirendalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |Útvonalalapú VPN-konfiguráció |
| **Pont–hely típusú kapcsolat (P2S**) |Nem támogatott |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |Támogatott (párhuzamosan használható az S2S mellett) |
| **Hitelesítési módszer** |Előre megosztott kulcs |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |Előre megosztott kulcs S2S-kapcsolatokhoz, tanúsítvány P2S-kapcsolatokhoz |
| **S2S-kapcsolatok maximális száma** |1 |10 |10 |30 |
| **P2S-kapcsolatok maximális száma** |Nem támogatott |128 |128 |128 |
| **Aktív útválasztás-támogatás (BGP)** |Nem támogatott |Nem támogatott |Támogatott |Támogatott |
