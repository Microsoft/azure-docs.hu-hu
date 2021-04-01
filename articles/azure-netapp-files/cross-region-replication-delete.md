---
title: Kötetek replikálásának vagy köteteinek törlése Azure NetApp Files régiók közötti replikációhoz | Microsoft Docs
description: Útmutató olyan replikációs kapcsolat törléséhez, amely már nem szükséges a forrás és a cél kötetek között.
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95249893"
---
# <a name="delete-volume-replications-or-volumes"></a>Kötetreplikációk vagy kötetek törlése

Ez a cikk a kötetek replikálásának törlését ismerteti. Emellett leírja a forrás vagy a cél kötetének törlését is.

## <a name="delete-volume-replications"></a>Kötetek replikálásának törlése

A replikálási kapcsolatot a forrás és a cél kötetek között a kötetek replikálásának törlésével állíthatja le. Törölnie kell a replikálást a cél kötetről. A törlési művelet csak a replikálás engedélyezését távolítja el; nem távolítja el a forrást vagy a célként megadott kötetet. 

1. A kötetek replikálásának törlése előtt győződjön meg arról, hogy a replikálási társítás megszakadt. A replikálási társítás megszüntetése: 

    1. Válassza ki a *cél* kötetet. Kattintson a **replikálás** lehetőségre a Storage szolgáltatás alatt.  

    2.  A folytatás előtt keresse meg a következő mezőket:  
        * Győződjön meg arról, hogy a tükrözési állapot ***tükrözöttként*** jelenik meg.   
            Ne kísérelje meg a replikálási társítások megszakítását, ha a tükrözés állapota nem *inicializált*.
        * Győződjön meg arról, hogy a kapcsolat állapota ***inaktív***.   
            Ne kísérelje meg a replikálási társítások megszakítását, ha a kapcsolat állapota *átadást* mutat.   

        Lásd: [a replikációs kapcsolat](cross-region-replication-display-health-status.md)állapotának megjelenítése. 

    3.  Kattintson a társítás **megszakítása** elemre.  

    4.  Ha a rendszer kéri, írja be az **Igen** értéket, és kattintson a **töréspont** 

        ![Replikációs társítás megszakítása](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. A kötetek replikálásának törléséhez válassza a forrás vagy a cél kötet **replikáció** elemét.  

2. Kattintson a **Törlés** gombra.    

3. A törlés megerősítéséhez írja be az **Igen értéket** , és kattintson a **Törlés** gombra.   

    ![Replikáció törlése](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Forrás vagy cél kötetek törlése

Ha törölni szeretné a forrás vagy a cél kötetet, a következő lépéseket kell elvégeznie a leírt sorrendben. Ellenkező esetben a `Volume with replication cannot be deleted` hiba történik.  

1. A cél köteten [törölje a kötet replikálását](#delete-volume-replications).   

2. Szükség szerint törölje a cél vagy a forrás kötetet, majd kattintson a jobb gombbal a kötet nevére, és válassza a **Törlés** lehetőséget.   

    ![Képernyőkép, amely egy kötet jobb kattintásos menüjét jeleníti meg.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Régiók közötti replikáció – replikálás](troubleshoot-cross-region-replication.md)

