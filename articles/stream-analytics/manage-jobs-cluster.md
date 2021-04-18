---
title: Feladatok létrehozása és törlése egy Azure Stream Analytics fürtben
description: Megtudhatja, hogyan kezelhetők Stream Analytics feladatok egy Azure Stream Analytics fürtben
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 04/16/2021
ms.openlocfilehash: 4a6334d33dea959bdd3704f848e7bc8250b6e7c6
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600723"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Feladatok hozzáadása és eltávolítása egy Azure Stream Analytics fürtben

Egy fürtön több Azure Stream Analytics is Stream Analytics futtathat. A feladatok fürtön való futtatása egyszerű kétlépéses folyamat: adja hozzá a feladatot a fürthöz, és indítsa el a feladatot. Ez a cikk bemutatja, hogyan adhat hozzá és távolíthat el feladatokat egy meglévő fürtből. Kövesse a rövid útmutatót [egy Stream Analytics fürt létrehozásához,](create-cluster.md) ha még nem rendelkezik ilyen fürtöt.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Új Stream Analytics hozzáadása egy fürthöz

Fürtökhöz csak Stream Analytics meglévő és meglévő feladatok hozzáadhatóak. Kövesse a rövid [útmutatót, amelyből megtudhatja, hogyan hozhat létre](stream-analytics-quick-create-portal.md) feladatot a Azure Portal. Ha már van egy feladat, amit hozzá szeretne adni egy fürthöz, a következő lépésekkel adjuk hozzá a feladatot a fürthöz.

1. A Azure Portal keresse meg és válassza ki Stream Analytics fürtöt.

1. A **Beállítások alatt** válassza a Stream Analytics **lehetőséget.** Ezután válassza a **Meglévő feladat hozzáadása lehetőséget.**

1. Válassza ki az előfizetést Stream Analytics a fürthöz hozzáadni kívánt feladathoz. A Stream Analytics csak olyan feladatokat lehet hozzáadni a fürthöz, amelyek ugyanabban a régióban vannak, mint a fürt.

   ![Feladat hozzáadása fürthöz](./media/manage-jobs-cluster/add-job.png)

1. Miután hozzáadta a feladatot a fürthöz, keresse meg a feladat erőforrását, és [indítsa el a feladatot.](start-job.md#azure-portal) A feladat ezután elindul a fürtön.

Az összes többi műveletet, például a figyelési, riasztási és diagnosztikai naplókat a feladat Stream Analytics végrehajtásához.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Új Stream Analytics eltávolítása egy fürtből

Stream Analytics feladatoknak leállított állapotban kell lennie, mielőtt eltávolíthatóak a fürtből. Ha a feladat még fut, állítsa le a feladatot, mielőtt továbblép a következő lépésekre.

1. Keresse meg és válassza ki Stream Analytics fürtöt.

1. A **Beállítások alatt** válassza a Stream Analytics **lehetőséget.**

1. Válassza ki a fürtből eltávolítani kívánt feladatokat, majd válassza az **Eltávolítás lehetőséget.**

   ![feladat eltávolítása a fürtből](./media/manage-jobs-cluster/remove-job.png)

   Amikor eltávolít egy feladatot egy Stream Analytics fürtből, vissza fog térni a standard több-bérlős környezetbe.

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan adhat hozzá és távolíthat el feladatokat a Azure Stream Analytics fürtben. A következő lépés a privát végpontok kezelése és a fürtök méretezése:

* [Fürt Azure Stream Analytics méretezése](scale-cluster.md)
* [Privát végpontok kezelése egy Azure Stream Analytics fürtben](private-endpoints.md)
