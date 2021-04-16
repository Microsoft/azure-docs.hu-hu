---
title: Nem használt ITSM-összekötők törlése
description: Ez a cikk az ITSM-összekötők és a hozzá társított műveletcsoportok törlésének magyarázatát biztosítja.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387938"
---
# <a name="delete-unused-itsm-connectors"></a>Nem használt ITSM-összekötők törlése

A nem használt összekötők (ITSM IT-szolgáltatásmenedzsment törlésének folyamata két fázisból áll. Az ITSM-összekötőhöz társított összes műveletet törli, majd magát az összekötőt is törli. Először törölje a műveleteket, mert az összekötő nélküli műveletek hibákat okozhatnak az előfizetésben.

## <a name="delete-associated-actions"></a>Társított műveletek törlése

1. A Azure Portal válassza a **Monitor lehetőséget.**
  
    ![A Figyelés kiválasztásának képernyőképe.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Válassza **a Riasztások lehetőséget.**
   
    ![A Riasztások kiválasztásának képernyőképe.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Válassza **a Műveletek kezelése lehetőséget.**
   
    ![Képernyőkép a Műveletek kezelése beállításról.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Válassza ki a törölni kívánt ITSM-összekötőhöz társított műveletcsoportot. Ez a cikk egy Cherwell-összekötő példáját használja.
   
    ![Képernyőkép a Cherwell-összekötőhöz társított műveletekről.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Tekintse át az adatokat, majd válassza a **Műveletcsoport törlése lehetőséget.**

    ![Képernyőkép a műveletcsoport információiról és a csoport törlésére vonatkozó gombról.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Az összekötő törlése

1. A keresősávban keressen rá a **servicedesk kifejezésre.** Ezután válassza **a ServiceDesk** lehetőséget az erőforrások listájában.

    ![A ServiceDesk keresésének és kiválasztásának képernyőképe.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Válassza **az ITSM-kapcsolatok** lehetőséget, majd a Cherwell-összekötőt.

    ![A Cherwell I T S M-összekötő képernyőképe.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Válassza a **Törlés** elemet.

    ![Képernyőkép az I T S M-összekötő törlés gombjáról.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Következő lépések

* [ITSM-összekötővel kapcsolatos problémák elhárítása](./itsmc-resync-servicenow.md)
