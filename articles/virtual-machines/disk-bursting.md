---
title: Felügyelt lemez kitörése
description: Ismerje meg az Azure-lemezekhez és az Azure-beli virtuális gépekhez készült lemezek kitörését.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576193"
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
