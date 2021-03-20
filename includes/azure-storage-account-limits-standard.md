---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ef9efe389894af7c792e980922ca422e9d05929b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025468"
---
Az alábbi táblázat az Azure általános célú v1, v2, blob Storage és a blob Storage-fiókokra vonatkozó alapértelmezett korlátokat ismerteti. A *bejövő* forgalom korlátja a Storage-fiókba küldendő összes adattal vonatkozik. A *kimenő* forgalom korlátja a Storage-fiókból kapott összes adattal vonatkozik.

> [!NOTE]
> Nagyobb kapacitást és belépési korlátot is igényelhet. A növeléshez forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/faq/).

| Erőforrás | Korlát |
| --- | --- |
| A Storage-fiókok régiónként/előfizetések száma, beleértve a standard és a Premium Storage-fiókokat.| 250 |
| Storage-fiók maximális kapacitása | 5 PiB <sup>1</sup>|
| BLOB-tárolók, blobok, fájlmegosztás, táblák, várólisták, entitások vagy üzenetek maximális száma Storage-fiókban | Korlátlan |
| Kérelmek maximális száma<sup>1</sup> – Storage-fiók | 20 000 kérelem másodpercenként |
| Maximális belépés<sup>1</sup> /Storage-fiókra (USA, Európa régiói) | 10 Gbps |
| Maximális bejövő forgalom<sup>1</sup> /Storage-fiókban (az USA-beli és európai régión kívül) | 5 GB/s if RA-GRS/GRS engedélyezve, 10 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v2-és blob Storage-fiókokhoz (minden régió) | 50 GB/s |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (USA-régiók) | 20 GB/s if RA-GRS/GRS engedélyezve, 30 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Maximális kimenő forgalom az általános célú v1 Storage-fiókokhoz (nem USA-régiók) | 10 GB/s if RA-GRS/GRS engedélyezve, 15 GB/s a LRS/ZRS<sup>2</sup> esetében |
| Virtuális hálózati szabályok maximális száma Storage-fiókban | 200 |
| IP-címekre vonatkozó szabályok maximális száma Storage-fiókban | 200 |

<sup>1</sup> az Azure Storage standard fiókjai magasabb kapacitási korlátokat és magasabb korlátot támogatnak a bejövő forgalomra vonatkozóan. A fiók korlátozásának növeléséhez forduljon az [Azure ügyfélszolgálatához](https://azure.microsoft.com/support/faq/).

<sup>2</sup> ha a Storage-fiókhoz engedélyezve van a Geo-redundáns tárolás (ra-GRS) vagy a Geo-Zone-redundáns tárolás (ra-GZRS), akkor a másodlagos hely kimenő elérési céljai azonosak az elsődleges helyével. További információ: [Azure Storage-replikáció](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> A Microsoft azt javasolja, hogy a legtöbb forgatókönyv esetén általános célú v2 Storage-fiókot használjon. Az általános célú v1-et vagy az Azure Blob Storage-fiókot egy általános célú v2-fiókra is frissítheti állásidő nélkül, és anélkül, hogy adatmásolásra lenne szükség. További információ: [frissítés általános célú v2 Storage-fiókra](../articles/storage/common/storage-account-upgrade.md).

Az összes Storage-fiók egy egyszerű hálózati topológián fut, függetlenül attól, hogy mikor jöttek létre. Az Azure Storage lapos hálózati architektúrával és a méretezhetőséggel kapcsolatos további információkért lásd [: Microsoft Azure Storage: magas rendelkezésre állású felhőalapú tárolási szolgáltatás erős konzisztencia](/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)-használattal.