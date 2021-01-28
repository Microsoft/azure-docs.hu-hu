---
title: fájl belefoglalása
description: fájl belefoglalása
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947843"
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

| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| CPU-alapú egyidejű feladatok | akár 100 egyidejű feladat | ugyanaz, mint az alapértelmezett korlát |
| FPGA-alapú egyidejű feladatok | legfeljebb 10 egyidejű feladat | ugyanaz, mint az alapértelmezett korlát |
| Solver – óra | 1 000 óra/hó  | akár 50 000 óra/hó |

Ha korlátozni kell a korlátot, lépjen kapcsolatba az Azure-támogatással. 

További információkért tekintse át az [Azure Quantum díjszabását ismertető oldalt](https://aka.ms/AQ/Pricing).
Harmadik féltől származó ajánlatokkal kapcsolatos információkért tekintse át a Azure Portal megfelelő szolgáltató lapját.
