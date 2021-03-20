---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77111213"
---
| **Szállító** | **Eszközcsalád** | **Belső vezérlőprogram verziója** |
| --- | --- | --- |
|Cisco | ISR| IOS 15,1 (előzetes verzió)|
|Cisco | ASA | ASA (*) Útvonalalapú (IKEv2-No BGP) az ASA esetében 9,8 alatt |
|Cisco | ASA | ASA Útvonalalapú (IKEv2-No BGP) az ASA 9.8 +-hoz |
|Juniper | SRX_GA | 12. x|
|Juniper | SSG_GA | ScreenOS 6.2. x|
|Juniper | JSeries_GA | JunOs 12. x|
|Juniper | SRX | JunOs 12. x Útvonalalapú BGP |
|Ubiquiti| EdgeRouter| EdgeOS v 1.10 x Útvonalalapú VTI|
|Ubiquiti| EdgeRouter| EdgeOS v 1.10 x Útvonalalapú BGP|

> [!NOTE]
> ( * ) Kötelező: NarrowAzureTrafficSelectors (UsePolicyBasedTrafficSelectors engedélyezése) és CustomAzurePolicies (IKE/IPsec)
>
