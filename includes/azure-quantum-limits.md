---
title: fájl belefoglalása
description: fájl belefoglalása
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920468"
---
### <a name="provider-limits--quota"></a>A szolgáltató korlátozza & kvótát

Az Azure Quantum szolgáltatás az első és a harmadik féltől származó szolgáltatókat is támogatja. A harmadik féltől származó szolgáltatók saját korlátaik és kvótáik. A felhasználók megtekinthetik az ajánlatokat és a korlátozásokat a Azure Portalban, amikor külső szolgáltatókat konfigurálnak a szolgáltató panelen. 

A Microsoft első féltől származó optimalizálási megoldási szolgáltatójának közzétett kvóta-korlátait alább találja. 

#### <a name="learn--develop-sku"></a>További információ az SKU fejlesztéséről &

| Erőforrás | Korlát |
| --- | --- |
| CPU-alapú egyidejű feladatok | legfeljebb 5 egyidejű feladat |
| FPGA-alapú egyidejű feladatok | legfeljebb 2 egyidejű feladat |
| CPU-alapú Solver-órák | havonta 20 óra  |
| FPGA-alapú Solver órája | havonta 1 óra  |

Ha a Learn & fejlesztése SKU-t használja, **nem** kérhet növekedést a kvóta korlátain. Ehelyett váltson a Scale SKU teljesítményére.

#### <a name="performance-at-scale-sku"></a>Teljesítmény a Scale SKU-ban

| Erőforrás | Korlát |
| --- | --- |
| CPU-alapú egyidejű feladatok | akár 100 egyidejű feladat |
| FPGA-alapú egyidejű feladatok | legfeljebb 10 egyidejű feladat |
| Solver – óra | 50 000 óra/hó  |

Ha korlátozni kell a korlátot, lépjen kapcsolatba az Azure-támogatással. 

További információkért tekintse át az [Azure Quantum díjszabását ismertető oldalt](https://aka.ms/AQ/Pricing).
Harmadik féltől származó ajánlatokkal kapcsolatos információkért tekintse át a Azure Portal megfelelő szolgáltató lapját.
