---
title: A ITSM-összekötő és a hozzá tartozó művelet törlése
description: Ez a cikk azt ismerteti, hogyan lehet törölni a ITSM-összekötőt és a hozzá társított műveleti csoportokat.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530941"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Nem használt ITSM-összekötők törlése

A nem használt összekötő törlésének folyamata 2 fázist tartalmaz:

1. A társított műveletek törlése: az ITSM-összekötőhöz társított összes műveletet törölni kell. Erre azért van szükség, hogy az összekötő nélkül ne legyenek olyan műveletek, amelyek hibákat okozhatnak az előfizetésében.

2. A nem használt ITSM-összekötő törlése.

## <a name="deletion-of-the-associated-actions"></a>A társított műveletek törlése

1. A műveleti csoport megkereséséhez lépjen a  ![ figyelő kiválasztása képernyőképre.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Válassza a "riasztások"  ![ képernyőképet a riasztások kiválasztásáról.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Válassza a "műveletek kezelése"  ![ képernyőképet a műveletek kezelése lehetőség kiválasztásához.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Válassza ki az összes olyan ITSM-összekötőt, amely a  ![ Cherwell-hez kapcsolódó ITSM-összekötők Cherwell képernyőképéhez csatlakozik.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Törölje a műveleti csoport  ![ törlését a műveleti csoport törlése képernyőről.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>A nem használt ITSM-összekötő törlése

1. A  ![ Keresés és a "ügyfélszolgálati" La kiválasztása lapon válassza a "ügyfélszolgálati" La elemet a keresési sáv tetején található képernyőképen.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Válassza a "ITSM kapcsolatok" lehetőséget, és válassza ki a  ![ CHERWELL ITSM-összekötők Cherwell-összekötő képernyőképét.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Válassza a "Törlés"  ![ képernyőképet az ITSM-összekötő törléséről.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Következő lépések

* [Hibaelhárítás az ITSM-összekötőben](./itsmc-resync-servicenow.md)
