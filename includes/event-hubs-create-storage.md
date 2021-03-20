---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75692576"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Tárfiók létrehozása az Event Processor Host számára
Az Event Processor Host egy intelligens ügynök, amely leegyszerűsíti az események fogadását az Event Hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat. Az ellenőrzőpontok használatához az Event Processor Hostnak tárfiókra van szüksége. Az alábbi példa bemutatja, hogyan hozhat létre tárfiókot és hogyan szerezheti be a hozzáférési kulcsokat:

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

    ![Erőforrás menüelem létrehozása Microsoft Azure Portal](./media/event-hubs-create-storage/create-resource.png)

2. Válassza a **Tárolás** > **Tárfiók** lehetőséget.
   
    ![Válassza ki a Storage-fiókot, Microsoft Azure Portal](./media/event-hubs-create-storage/select-storage-account.png)

3. A **Tárfiók létrehozása** oldalon hajtsa végre az alábbi lépéseket: 

   1. Adja meg a **Storage-fiók nevét**.
   2. Válasszon ki egy Azure- **előfizetést** , amely az Event hub-t tartalmazza.
   3. Válassza ki vagy hozza létre az Event hub-t tartalmazó **erőforráscsoportot** .
   4. Válasszon egy **helyet** , amelyben létre kívánja hozni az erőforrást. 
   5. Válassza az **Áttekintés + létrehozás** lehetőséget.
   
        ![Felülvizsgálat + létrehozás, Storage-fiók létrehozása, Microsoft Azure Portal](./media/event-hubs-create-storage/review-create.png)

4. A **Felülvizsgálat + létrehozás** oldalon tekintse át az értékeket, majd kattintson a **Létrehozás** lehetőségre. 

    ![A Storage-fiók beállításainak áttekintése és létrehozás, Microsoft Azure Portal](./media/event-hubs-create-storage/create-storage-account.png)
5. Miután megtalálta a **központi telepítések sikeres** üzenetét az értesítésekben, válassza az **erőforrás** megnyitása lehetőséget a Storage-fiók lap megnyitásához. Azt is megteheti, hogy kibontja a **központi telepítés részleteit** , majd kiválasztja az új erőforrást az erőforrások listájából.  

    ![Ugrás az erőforrás, a Storage-fiók üzembe helyezése, Microsoft Azure Portal](./media/event-hubs-create-storage/go-to-resource.png) 
6. Válassza a **tárolók** lehetőséget.

    ![Válassza ki a Blobok tároló szolgáltatást, a Storage-fiókokat, Microsoft Azure Portal](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Válassza a felső **+ tároló** elemet, adja meg a tároló **nevét** , majd kattintson **az OK gombra**. 

    ![Új blob-tároló, Storage-fiók létrehozása Microsoft Azure Portal](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Válassza a **hozzáférési kulcsok** elemet a **Storage-fiók** lap menüjében, és másolja a **key1** értékét.

    Mentse a következő értékeket a Jegyzettömbbe vagy egy másik ideiglenes helyre.
    - A Storage-fiók neve
    - A Storage-fiók elérési kulcsa
    - A tároló neve
