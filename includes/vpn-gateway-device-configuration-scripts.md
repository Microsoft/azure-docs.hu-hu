---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157518"
---
| **Szállító** | **Eszközcsalád** | **Belső vezérlőprogram verziója** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (előzetes verzió)|
|Cisco | ASA | Az alábbi 9.8 ASA ASA (*) RouteBased (IKEv2-nincs BGP) |
|Cisco | ASA | Az ASA 9.8 + ASA RouteBased (IKEv2 - nincs BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) Szükséges: (Engedélyezi a UsePolicyBasedTrafficSelectors beállítást) NarrowAzureTrafficSelectors és CustomAzurePolicies (IKE/IPsec)
>
