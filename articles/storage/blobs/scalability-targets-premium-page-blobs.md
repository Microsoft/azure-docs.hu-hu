---
title: Méretezhetőségi célok a prémium oldal blob Storage-fiókjaihoz
titleSuffix: Azure Storage
description: A Premium Performance oldal blob Storage-fiókja írási/olvasási műveletekre van optimalizálva. Ez a típusú Storage-fiók egy nem felügyelt lemezt készít egy Azure-beli virtuális géphez.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 23a176bfa5e3861dbc4ad5c03ea54fc847d3f56b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96922529"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Méretezhetőségi és teljesítményi célok a prémium oldal blob Storage-fiókjaihoz

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Célok méretezése a prémium oldal blob-fiókjaihoz

A prémium teljesítményű blob Storage-fiók írási/olvasási műveletekre van optimalizálva. Ez a típusú Storage-fiók egy nem felügyelt lemezt készít egy Azure-beli virtuális géphez.

> [!NOTE]
> A Microsoft azt javasolja, hogy ha lehetséges, felügyelt lemezeket használjon az Azure Virtual Machines (VM) használatával. További információ a felügyelt lemezekről: a [virtuális gépek Azure Disk Storage áttekintése](../../virtual-machines/managed-disks-overview.md).

A prémium oldal blob Storage-fiókjai a következő skálázhatósági célokat szolgálják:

| Teljes fiók kapacitása                            | Teljes sávszélesség egy helyileg redundáns Storage-fiókhoz                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Lemez kapacitása: 4 TB (különálló lemez)/35 TB (az összes lemez összesített összege) <br>Pillanatkép kapacitása: 10 TB | Akár 50 Gigabit másodpercenként a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> a Storage-fiókba küldött összes adatérték (kérelem)

<sup>2</sup> minden, a Storage-fiókból fogadott adatok (válaszok)

A Premium Page blob-fiók a prémium szintű teljesítményhez konfigurált általános célú fiók. Az általános célú v2 Storage-fiókok használata ajánlott.

Ha prémium szintű blob Storage-fiókokat használ a nem felügyelt lemezekhez, és az alkalmazása meghaladja az egyetlen Storage-fiók skálázhatósági célját, akkor a Microsoft javasolja a felügyelt lemezekre való áttelepítést. További információ a felügyelt lemezekről: a [virtuális gépek Azure Disk Storage áttekintése](../../virtual-machines/managed-disks-overview.md).

Ha nem tud áttérni a felügyelt lemezekre, hozza létre az alkalmazást több Storage-fiók használatára, és particionálja az adatait a Storage-fiókok között. Ha például a 51 TB-os lemezeket több virtuális gépen szeretné csatlakoztatni, akkor a két Storage-fiók között kell elosztania őket. 35 TB az egyetlen prémium szintű Storage-fiókra vonatkozó korlát. Győződjön meg arról, hogy egy prémium szintű teljesítményű Storage-fiók soha nem rendelkezik több mint 35 TB kiosztott lemezzel.

## <a name="see-also"></a>Lásd még

- [A standard szintű Storage-fiókok méretezhetősége és teljesítménybeli céljai](../common/scalability-targets-standard-account.md)
- [Méretezhetőségi célok a prémium szintű blokk blob Storage-fiókokhoz](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure-előfizetési korlátok és kvóták](../../azure-resource-manager/management/azure-subscription-service-limits.md)
