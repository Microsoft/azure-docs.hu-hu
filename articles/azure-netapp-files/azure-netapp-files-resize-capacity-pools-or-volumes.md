---
title: A kapacitási készlet vagy a kötet átméretezése Azure NetApp Files | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja a kapacitási készlet vagy a kötet méretét. A kapacitás-készlet átméretezése megváltoztatja a megvásárolt Azure NetApp Files kapacitást.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594478"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>A kapacitáskészlet vagy kötet átméretezése
Szükség szerint módosíthatja a kapacitási készlet vagy a kötet méretét. 

## <a name="resize-the-capacity-pool"></a>A kapacitási készlet átméretezése 

A kapacitási készlet mérete 1 – TiB-es vagy csökkenő értékben módosítható. A kapacitási készlet mérete azonban nem lehet kisebb, mint 4 TiB. A kapacitás-készlet átméretezése megváltoztatja a megvásárolt Azure NetApp Files kapacitást.

1. A NetApp-fiók kezelése panelen kattintson az átméretezni kívánt kapacitási készletre. 
2. Kattintson a jobb gombbal a kapacitási készlet nevére, vagy kattintson a "..." elemre. a kapacitási készlet sorainak végén található ikon a helyi menü megjelenítéséhez. 
3. A kapacitási készlet átméretezéséhez vagy törléséhez használja a helyi menü beállításait.

## <a name="resize-a-volume"></a>Kötet átméretezése

Szükség szerint módosíthatja a kötetek méretét. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.

1. A NetApp-fiók kezelése panelen kattintson a **kötetek** elemre. 
2. Kattintson a jobb gombbal az átméretezni kívánt kötet nevére, vagy kattintson a "..." elemre. ikon a kötet sor végén a helyi menü megjelenítéséhez.
3. A kötet átméretezéséhez vagy törléséhez használja a helyi menü beállításait.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Régiók közötti replikálási cél kötetének átméretezése 

[Régiók közötti replikációs](cross-region-replication-introduction.md) kapcsolatban a rendszer automatikusan átméretezi a célként megadott kötetet a forrás kötet mérete alapján. Így nem kell külön átméreteznie a cél kötetét. Ez az automatikus átméretezési viselkedés akkor alkalmazható, ha a kötetek aktív replikációs kapcsolatban vannak, vagy amikor a replikálási társítás megszakadt az [újraszinkronizálási művelettel](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

A következő táblázat a cél kötet átméretezési viselkedését írja le a [tükrözési állapot](cross-region-replication-display-health-status.md)alapján:

|  Tükrözés állapota  | Cél kötet átméretezési viselkedése |
|-|-|
| *Tükrözött* | Ha a célként megadott kötet inicializálása megtörtént, és készen áll a tükrözési frissítések fogadására, a kötet átméretezése automatikusan átméretezi a célhely köteteit. |
| *Törött* | Ha átméretezi a forrás kötetet, és a tükrözött állapot *megszakad*, a rendszer automatikusan átméretezi a célként megadott kötetet az [újraszinkronizálási művelettel](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Nincs inicializálva* | Ha átméretezi a forrás kötetet, és a tükrözés állapota még nincs *inicializálva*, a célként megadott kötet átméretezését manuálisan kell elvégezni. Ezért javasoljuk, hogy várjon, amíg az inicializálás befejeződik (azaz amikor a tükrözött állapot *tükrözött* lesz) a forrás kötet átméretezéséhez. | 

> [!IMPORTANT]
> Győződjön meg arról, hogy a kapacitás-készletek elegendő belmagassággal rendelkeznek a régiók közötti replikálás forrásához és a célként megadott kötetekhez is. Ha átméretezi a forrás kötetet, a rendszer automatikusan átméretezi a célként megadott kötetet. Ha azonban a célként megadott kötetet üzemeltető kapacitás-készlet nem rendelkezik elegendő belmagassággal, a forrás és a cél kötetek átméretezése sikertelen lesz.

## <a name="next-steps"></a>Következő lépések

- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
- [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
- [Kötetek szolgáltatásszintjének dinamikus módosítása](dynamic-change-volume-service-level.md) 