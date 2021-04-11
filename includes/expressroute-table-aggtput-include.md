---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504690"
---
| Átjáró SKU | Másodpercenkénti kapcsolatok | Folyamatok száma | Mega-bit/másodperc | Másodpercenkénti csomagok száma | Kapcsolatcsoport sávszélessége | Az átjáró által hirdetett útvonalak száma (MSEE) | Az átjáró által megismert útvonalak száma (MSEE) | Virtuális gépek száma a virtuális hálózaton |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Alapszintű SKU (elavult)** | N.A. | N.A. | 500 | N.A. | N.A. | N.A. | N.A. | N.A. |
| **Standard SKU/ErGw1AZ** | 7 000 | 400,000 | >1 000 | >100 000 | 1 Gbps |  500 | 4,000 | 2 000 (a karbantartási időszakban a 1 000-re csökkenti a visszaállítást.) | 
| **Nagy teljesítményű SKU/ErGw2AZ** | 14 000 | 840 000 | >2 000 | 250 000 | 1 Gbps | 500 | ~ 9 500 (csökkentse a 4 000-es méretet, ha több mint 6 500 virtuális gép van a virtuális hálózaton.) | 4.500 |
| **Ultra Performance SKU/ErGw3AZ** | 16000 | 950 000 | ~ 10 000 | 1,000,000 | 1 Gbps | 500 | ~ 9 500 | 11 000 |
