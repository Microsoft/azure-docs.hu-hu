---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504689"
---
Egy virtuális hálózati átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. Ha egy átjáró magasabb szintű termékváltozatát választja, a rendszer több processzort és hálózati sávszélességet foglal le a számára, ezért az átjáró nagyobb hálózati átviteli sebesség támogatására képes a virtuális hálózaton. 

A ExpressRoute virtuális hálózati átjárók a következő SKU-ket használhatják:

|     | VPN Gateway és ExpressRoute párhuzamos használata | FastPath | Áramköri kapcsolatok maximális száma |
| --- | --- | --- | --- |
| **Standard** | Igen | Nem | 4 |
| **HighPerformance** | Igen | Nem | 4 |
| **UltraPerformance** | Igen | Yes | 16 |