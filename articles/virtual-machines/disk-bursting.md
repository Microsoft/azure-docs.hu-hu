---
title: Felügyelt lemez kitörése
description: Ismerje meg az Azure-lemezekhez és az Azure-beli virtuális gépekhez készült lemezek kitörését.
author: albecker1
ms.author: albecker
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1cedac5814d1c547a28e9b1c894f416af5a924b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585096"
---
# <a name="managed-disk-bursting"></a>Felügyelt lemez kitörése
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Virtuális gépek szintjének felszakadása
A virtuális gépek szintjének kitörése a következő virtuálisgép-sorozaton engedélyezhető az összes olyan régióban, amelyet támogat:
- [Lsv2 sorozat](lsv2-series.md)
- [Dsv3-sorozat](dv3-dsv3-series.md)
- [Esv3 sorozat](ev3-esv3-series.md)

A burst beállítás alapértelmezés szerint engedélyezve van az azt támogató virtuális gépek esetében.

## <a name="disk-level-bursting"></a>Lemez szintjének felszakadása
A [prémium szintű SSD](disks-types.md#premium-ssd) -lemezeken az Azure-beli nyilvános, kormányzati és kínai felhők minden régiójában P20 és kisebb méretekben is elérhetők a betörések. A lemez kitörése alapértelmezés szerint engedélyezve van az azt támogató lemezek összes új és meglévő központi telepítésén. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné tudni, hogyan szerezhet be betekintést a feltört erőforrásaiba, tekintse meg a [lemezek felszakadási mérőszámait](disks-metrics.md).
