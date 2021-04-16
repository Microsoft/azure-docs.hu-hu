---
title: Metrikák Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files a lefoglalt tárterületre, a tényleges tárterület-használatra, a kötet IOPS-értékre és a késésre vonatkozó metrikákat biztosít. Ezekkel a metrikákkal megértheti a használatot és a teljesítményt.
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
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388608"
---
# <a name="metrics-for-azure-netapp-files"></a>Az Azure NetApp Files metrikái

Azure NetApp Files a lefoglalt tárterületre, a tényleges tárterület-használatra, a kötet IOPS-értékre és a késésre vonatkozó metrikákat biztosít. A metrikák elemzésével jobban megértheti a NetApp-fiókok használati mintáját és kötetteljesítményét.  

Kapacitáskészlethez vagy kötethez a kapacitáskészlet vagy kötet kiválasztásával **találhat** **metrikákat.**  Ezután kattintson a **Metrika** elemre az elérhető metrikák megtekintéséhez: 

[![A Metrika lekért lista lépését bemutató pillanatkép. ](../media/azure-netapp-files/metrics-navigate-volume.png)](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Kapacitáskészletek használati metrikai

- *Lefoglalt készlet mérete*   
    A készlet kiépített mérete.

- *Kötetmérethez lefoglalt készlet*  
    Az adott kapacitáskészletben található kötetkvóta (GiB) teljes mennyisége (a kapacitáskészletben kiépített kötetek teljes mérete).  
    Ez a méret a kötet létrehozása során kiválasztott méret.  

- *Készlet felhasznált mérete*  
    A kapacitáskészletben lévő kötetek között felhasznált logikai terület (GiB) teljes száma.  

- *A készlet teljes pillanatképmérete*    
    A készletben található összes kötet pillanatképméretének összege.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Használati metrikák kötetek esetén

- *Százalékos felhasznált kötetméret*    
    A felhasznált kötet százalékos aránya, beleértve a pillanatképeket is.  
- *Lefoglalt kötet mérete*   
    Kötet kiépített mérete
- *Kötetkvóta mérete*    
    A kvóta mérete (GiB), amely alapján a kötet ki van építve.   
- *Felhasznált kötet mérete*   
    A kötet logikai mérete (bájtban).  
    Ez a méret magában foglalja az aktív fájlrendszerek és pillanatképek által használt logikai területet.  
- *Kötet pillanatképének mérete*   
   Egy kötet összes pillanatképének mérete.  

## <a name="performance-metrics-for-volumes"></a>Kötetek teljesítménymetrikák

- *Átlagos olvasási késés*   
    A kötetből való olvasások átlagos ideje ezredmásodpercben.
- *Átlagos írási késés*   
    A kötetről való írás átlagos ideje ezredmásodpercben.
- *IOPS olvasása*   
    A köteten másodpercenkénti olvasások száma.
- *IOPS írása*   
    A köteten másodpercenkénti írások száma.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Kötetreplikációs metrikák

> [!NOTE] 
> * A hálózati átvitel mérete  (például a kötetreplikáció teljes átviteli metrika) eltérhet a régiók közötti replikáció forrás- vagy célköteteitől. Ennek a viselkedésnek az az eredménye, hogy hatékony replikációs motort használnak a hálózatátviteli költségek minimalizálásához.
> * A kötetreplikációs metrikák jelenleg nem a replikációs kapcsolat forrásaként, csak a replikáció célköteteiként vannak feltöltve.

- *Kifogástalan állapotú-e a kötetreplikáció*   
    A replikációs kapcsolat feltétele. A kifogástalan állapotot a `1` jelöli. A nem megfelelő állapotot a `0` jelzi.

- *Kötetreplikáció átvitele*    
    Azt határozza meg, hogy a kötetreplikáció állapota "átvitel"-e. 
 
- *Kötetreplikáció késése*   
    Az az idő másodpercben, amely alatt a tükrözött adatok a forrás mögött maradnak. 

- *Kötetreplikáció utolsó átvitelének időtartama*   
    Az utolsó átvitel befejezéséhez szükséges idő másodpercben. 

- *Kötetreplikáció utolsó átvitelének mérete*    
    Az utolsó átvitel részeként átvitt bájtok teljes száma. 

- *Kötetreplikáció állapota*    
    Az aktuális átviteli művelethez átvitt adatok teljes mennyisége. 

- *Kötetreplikáció teljes átvitele*   
    A kapcsolathoz átvitt halmozott bájtok. 

## <a name="next-steps"></a>Következő lépések

* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
