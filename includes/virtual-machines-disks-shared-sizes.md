---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81008339"
---
Egyelőre csak az ultra-lemezek és a prémium SSD-k engedélyezhetik a megosztott lemezeket. A különböző méretű lemezek eltérő `maxShares` korláttal rendelkezhetnek, ami nem haladhatja meg az `maxShares` érték beállítását. A prémium SSD-k esetében a lemezek megosztását támogató lemezek mérete P15 és nagyobb.

Minden lemez esetében megadhat egy értéket, `maxShares` amely a lemez párhuzamosan megosztható csomópontok maximális számát jelöli. Ha például egy 2 csomópontos feladatátvevő fürtöt szeretne beállítani, állítsa be a következőt: `maxShares=2` . A maximális érték egy felső határ. A csomópontok csatlakozhatnak vagy elhagyhatják a fürtöt (a lemez csatlakoztatása vagy leválasztása), feltéve, hogy a csomópontok száma alacsonyabb a megadott `maxShares` értéknél.

> [!NOTE]
> Az `maxShares` érték csak akkor állítható be vagy szerkeszthető, ha a lemez le van választva az összes csomópontról.

### <a name="premium-ssd-ranges"></a>Tartományok prémium SSD

Az alábbi táblázat a prémium szintű lemezek megengedett maximális értékeit mutatja be `maxShares` :

|Lemezek mérete  |maxShares korlátja  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

A IOPS és a sávszélességre vonatkozó korlátokat nem érinti az `maxShares` érték. Egy P15-lemez Max IOPS például 1100, hogy maxShares = 1 vagy maxShares > 1.

### <a name="ultra-disk-ranges"></a>Ultra-lemezes tartományok

A minimális `maxShares` érték 1, a maximális érték pedig `maxShares` 5. Az ultra-lemezeken nincs méretre vonatkozó korlátozás, bármilyen méretű, ultra-lemez bármilyen értéket használhat a számára `maxShares` , akár a maximális értéket is beleértve.