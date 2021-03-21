---
title: A Azure NetApp Files metrikái | Microsoft Docs
description: A Azure NetApp Files a lefoglalt tároló, a tényleges tárterület-használat, a mennyiségi IOPS és a késés mérőszámait biztosítja. Használja ezeket a mérőszámokat a használat és a teljesítmény megismeréséhez.
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
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: a17e6cc0479cf8ff2306736994a369d9e44dfdda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745944"
---
# <a name="metrics-for-azure-netapp-files"></a>Az Azure NetApp Files metrikái

A Azure NetApp Files a lefoglalt tároló, a tényleges tárterület-használat, a mennyiségi IOPS és a késés mérőszámait biztosítja. A metrikák elemzésével jobban megismerheti a NetApp-fiókok használati mintáját és a kötet teljesítményét.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>A kapacitási készletek használati metrikái

- *Készlet lefoglalt mérete*   
    A készlet kiépített mérete.

- *A kötet méretéhez lefoglalt készlet*  
    Az adott kapacitási készletben lévő mennyiségi kvóta (GiB) teljes mennyisége (azaz a kötetek kiosztott méretei összesen).  
    Ez a méret a kötetek létrehozásakor kiválasztott méret.  

- *Készlet felhasznált mérete*  
    A kapacitási készlet kötetei között felhasznált logikai terület összesen (GiB).  

- *A készlethez tartozó pillanatképek teljes mérete*    
    A készletben lévő összes kötetről származó Pillanatképek méretének összege.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>A kötetek használati metrikái

- *Százalékos mennyiség felhasznált mérete*    
    A felhasznált kötet százalékos aránya, beleértve a pillanatképeket is.  
- *Kötet lefoglalt mérete*   
    Kötet kiépített mérete
- *Kötet kvótájának mérete*    
    A kötet kiépített kvótájának mérete (GiB).   
- *Kötet felhasznált mérete*   
    A kötet logikai mérete (felhasznált bájtok).  
    Ez a méret magában foglalja az aktív fájlrendszerek és Pillanatképek által használt logikai teret.  
- *Kötet pillanatképének mérete*   
   Egy kötet összes pillanatképének mérete.  

## <a name="performance-metrics-for-volumes"></a>A kötetek teljesítmény-mérőszámai

- *Olvasási késleltetés átlagos késése*   
    A kötet olvasási idejének átlagos ideje ezredmásodpercben.
- *Írási késleltetés átlagos késése*   
    A köteten történő írások átlagos ideje ezredmásodpercben.
- *IOPS olvasása*   
    A kötetre irányuló olvasási műveletek száma másodpercenként.
- *IOPS írása*   
    A kötetre írások száma másodpercenként.
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


## <a name="volume-replication-metrics"></a><a name="replication"></a>Kötet replikációs metrikái

> [!NOTE] 
> * A hálózati átvitel mérete (például a *kötet replikálása teljes átvitel* mérőszámai) eltérhetnek a régiók közötti replikálás forrás-vagy cél köteteitől. Ennek a viselkedésnek a hatására a rendszer hatékony replikációs motort használ, hogy csökkentse a hálózati átvitel költségeit.
> * A kötet replikációs metrikái jelenleg a replikálási cél köteteihez vannak feltöltve, nem pedig a replikációs kapcsolat forrásaként.

- *A kötet replikálási állapota Kifogástalan*   
    A replikációs kapcsolat feltétele. A megfelelő állapotot a jelzi `1` . A nem kifogástalan állapotot a jelzi `0` .

- *A kötet-replikálás átadása*    
    Azt jelzi, hogy a kötet replikálásának állapota "átvitel". 
 
- *Kötet replikációs késési ideje*   
    Az az időtartam másodpercben, ameddig a tükrözött adatmennyiség a forrás mögött marad. 

- *Kötet replikálásának utolsó átvitelének időtartama*   
    Az utolsó átvitel befejezéséhez szükséges idő másodpercben. 

- *Kötet replikálásának utolsó átvitelének mérete*    
    Az utolsó átvitel részeként átvitt bájtok teljes száma. 

- *Kötet replikálásának folyamata*    
    Az aktuális adatátviteli művelethez továbbított adatok teljes mennyisége. 

- *Kötet replikálásának teljes átvitele*   
    A kapcsolathoz továbbított összesített bájtok száma. 

## <a name="next-steps"></a>Következő lépések

* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
