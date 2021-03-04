---
title: Riasztási példányok kezelése a Azure Monitorban
description: Riasztási példányok kezelése az Azure-ban
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: dc4b62f937e9338051c4018554ec47c94f01a743
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033537"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Riasztási példányok kezelése egyesített riasztásokkal

A Azure Monitor [egységes riasztások terén](./alerts-overview.md) az összes különböző típusú riasztást láthatja az Azure-ban. Ez több előfizetést ölel fel egyetlen ablaktáblán. Ez a cikk bemutatja, hogyan tekintheti meg a riasztási példányokat, és hogyan keresheti meg a hibaelhárításhoz adott riasztási példányokat.

> [!NOTE]
> Csak az elmúlt 30 napban generált riasztások érhetők el.

## <a name="go-to-the-alerts-page"></a>Ugrás a riasztások oldalra

A riasztások oldal a következő módokon léphet fel:

- A [Azure Portal](https://portal.azure.com/)válassza a   >  **riasztások** figyelése lehetőséget.  

     ![Képernyőkép a figyelő riasztásokról](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Egy adott erőforrás kontextusának használata. Nyisson meg egy erőforrást, nyissa meg a **figyelés** szakaszt, és válassza a **riasztások** lehetőséget. A Kezdőlap előre szűrve van a riasztások számára az adott erőforráson.

     ![Az erőforrás-figyelési riasztások képernyőképe](media/alerts-managing-alert-instances/alert-resource.JPG)

- Egy adott erőforráscsoport környezetének használata. Nyisson meg egy erőforráscsoportot, nyissa meg a **figyelés** szakaszt, és válassza a **riasztások** lehetőséget. A Kezdőlap előre szűrve van az adott erőforráscsoporthoz tartozó riasztásokhoz.    

     ![Az erőforráscsoport-figyelési riasztások képernyőképe](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Riasztási példányok keresése

A **riasztások összegzése** lap áttekintést nyújt az összes Azure-beli riasztási példányról. Az összegző nézetet úgy módosíthatja, hogy **több előfizetést** választ ki (legfeljebb 5), vagy az **erőforráscsoportok**, adott **erőforrások** vagy **időtartományok** szűrésével. Válassza ki az **összes riasztást**, vagy a súlyossági sávok bármelyikét, hogy megnyissa a riasztások listájának nézetét.     

![A riasztások összegzése lap képernyőképe](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
A **minden riasztás** oldalon az összes Azure-beli riasztási példány megjelenik. Ha riasztási értesítésből érkezik a portálra, az adott riasztási példányon elérhető szűrők használatával szűkítheti a rendelkezésre álló szűrőket.

> [!NOTE]
> Ha a súlyossági sávok bármelyikét kiválasztva az oldalra került, a lista az adott súlyossági szinthez előre szűrve lesz.

Az előző oldalon elérhető szűrőktől eltekintve a figyelési szolgáltatás (például a mérőszámok platformja), a figyelési feltétel (kilőtt vagy feloldott), a súlyosság, a riasztási állapot (új/visszaigazolt/lezárt), vagy az intelligens csoport azonosítója alapján is szűrhető.

![Képernyőkép az összes riasztás oldalról](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Ha a súlyossági sávok bármelyikét kiválasztva az oldalra került, a lista az adott súlyossági szinthez előre szűrve lesz.

Bármelyik riasztási példány kiválasztásával megnyílik a **riasztás részletei** lap, amely lehetővé teszi az adott riasztási példányra vonatkozó további részletek megtekintését.   

![A riasztás részletei lap képernyőképe](media/alerts-managing-alert-instances/alert-details.jpg)