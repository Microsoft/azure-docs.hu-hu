---
title: Azure NetApp Files replikációs kapcsolat állapotának megjelenítése | Microsoft Docs
description: Ismerteti, hogyan lehet megtekinteni a replikálási állapotot a forrás köteten vagy Azure NetApp Files cél kötetén.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: a14656bc1abc42fdc3e948fdae1ca07469681d21
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249852"
---
# <a name="display-health-status-of-replication-relationship"></a>Replikációs kapcsolat állapotának megjelenítése 

Megtekintheti a replikálás állapotát a forrás köteten vagy a célként megadott köteten.  

## <a name="display-replication-status"></a>Replikáció állapotának megjelenítése

1. A forrás vagy a cél kötetről válassza a **replikálás** a tárolási szolgáltatás alatt lehetőséget a kötethez.

    A következő replikációs állapot és állapot információi láthatók:  
    * **Végpont típusa** – meghatározza, hogy a kötet a replikálás forrása vagy célja-e.
    * **Állapot** – megjeleníti a replikációs kapcsolat állapotát.
    * **Tükrözés állapota** – az alábbi értékek egyikét jeleníti meg:
        * Nincs *inicializálva*:  
            Ez a kezdeti és alapértelmezett állapot, amikor létrejön egy társítási kapcsolat. Az állapot még nincs inicializálva, amíg az inicializálás sikeresen befejeződik.
        * *Tükrözött*:   
            A célként megadott kötet inicializálása megtörtént, és készen áll a tükrözési frissítések fogadására.
        * *Hibás*:   
            Ez az állapot a társítási kapcsolat megszakítása után. A cél kötet `‘RW’` és a pillanatképek jelen vannak.
    * **Kapcsolat állapota** – az alábbi értékek egyikét jeleníti meg: 
        * *Tétlen*:  
            Nincs folyamatban lévő átviteli művelet, és a jövőbeli átvitelek nem lesznek letiltva.
        * *Átvitel*:  
            Folyamatban van egy átviteli művelet, és a jövőbeli átvitelek nem lesznek letiltva.
    * **Replikálási ütemterv** – azt mutatja, hogy a rendszer hogyan hajtja végre a növekményes tükrözési frissítéseket az inicializálás (alapkonfiguráció) befejeződése után.

    * **Teljes folyamat** – megjeleníti az aktuális átviteli művelethez továbbított adatmennyiséget (bájtban). Ez az összeg az átvitt tényleges bitek száma, és a forrás és a cél kötetek által jelentett logikai területtől eltérő lehet.  

    ![Replikációs állapot állapota](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> A replikációs kapcsolat állapota nem kifogástalan *állapotú, ha* a korábbi replikálási feladatok nem fejeződött be. Ez az állapot az alacsonyabb adatátviteli időszaktal továbbított nagy méretű kötetek eredménye (például egy 10 perces adatátviteli idő nagy mennyiség esetén). Ebben az esetben a kapcsolat állapota az *átvitel* és az állapot állapotát *mutatja.*

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [Vészhelyreállítás kezelése](cross-region-replication-manage-disaster-recovery.md)
* [Kötet replikációs metrikái](azure-netapp-files-metrics.md#replication)
* [Kötetek replikálásának vagy köteteinek törlése](cross-region-replication-delete.md)
* [Régiók közötti replikáció hibaelhárítása](troubleshoot-cross-region-replication.md)

