---
title: Feladatok létrehozása és törlése egy Azure Stream Analytics-fürtben
description: Megtudhatja, hogyan felügyelheti Stream Analytics feladatait egy Azure Stream Analytics-fürtben
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 09a67d11ac4daf3e87a50ee2171f1ca49060c5bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018122"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Feladatok hozzáadása és eltávolítása egy Azure Stream Analytics-fürtben

Stream Analytics-fürtökön több Azure Stream Analytics feladatot is futtathat. A feladatok fürtön való futtatása egyszerű, kétlépéses folyamat: adja hozzá a feladatot a fürthöz, és indítsa el a feladatot. Ebből a cikkből megtudhatja, hogyan adhat hozzá és távolíthat el feladatokat egy meglévő fürtből. Ha még nem rendelkezik ilyennel, kövesse a rövid útmutatót [stream Analytics-fürt létrehozásához](create-cluster.md) .

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Stream Analytics-feladatok hozzáadása fürthöz

Csak a meglévő Stream Analytics feladatok adhatók hozzá a fürtökhöz. A rövid [útmutatóból megtudhatja, hogyan hozhat létre feladatokat](stream-analytics-quick-create-portal.md) a Azure Portal használatával. Ha van egy, a fürthöz hozzáadni kívánt feladatot, a következő lépésekkel adhatja hozzá a feladatot a fürthöz.

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások** területen válassza a **stream Analytics feladatok** elemet. Ezután válassza a **meglévő feladatok hozzáadása** elemet.

1. Válassza ki azt az előfizetést és Stream Analytics feladatot, amelyet hozzá szeretne adni a fürthöz. A fürthöz csak olyan Stream Analytics feladatok adhatók hozzá, amelyek ugyanabban a régióban találhatók, mint a fürt.

   ![Feladatok hozzáadása a fürthöz](./media/manage-jobs-cluster/add-job.png)

1. Miután hozzáadta a feladatot a fürthöz, navigáljon a feladatok erőforráshoz, és [indítsa el a feladatot](start-job.md#azure-portal). Ekkor a rendszer elindítja a feladatot a fürtön való futtatáshoz.

Az összes többi műveletet, például a figyelést, a riasztásokat és a diagnosztikai naplókat a Stream Analytics feladatok erőforrása lapon végezheti el.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Stream Analytics-feladatok eltávolítása egy fürtből

Stream Analytics feladatnak leállított állapotban kell lennie ahhoz, hogy el lehessen távolítani a fürtből. Ha a feladata még fut, állítsa le a feladatot, mielőtt továbblép a következő lépésekre.

1. Keresse meg és válassza ki a Stream Analytics-fürtöt.

1. A **Beállítások** területen válassza a **stream Analytics feladatok** elemet.

1. Válassza ki a fürtből eltávolítani kívánt feladatokat, majd válassza az **Eltávolítás** lehetőséget.

   ![feladatok eltávolítása a fürtből](./media/manage-jobs-cluster/remove-job.png)

   Ha eltávolít egy feladatot egy Stream Analytics-fürtről, a rendszer visszaadja a standard több-bérlős környezetet.

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan adhat hozzá és távolíthat el feladatokat a Azure Stream Analytics-fürtben. Ezután megtudhatja, hogyan kezelheti a privát végpontokat és méretezheti a fürtöket:

* [Azure Stream Analytics-fürt méretezése](scale-cluster.md)
* [Privát végpontok kezelése Azure Stream Analytics-fürtben](private-endpoints.md)
