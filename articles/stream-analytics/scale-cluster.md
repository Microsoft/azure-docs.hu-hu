---
title: Azure Stream Analytics-fürt átméretezése
description: Megtudhatja, hogyan méretezheti fel és állíthatja le egy Azure Stream Analytics-fürt méretét.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: e469e9bfd13bf8d89084b66e954cb51b27c2ebc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020026"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>Azure Stream Analytics-fürt átméretezése

A Stream Analytics-fürtök kapacitása folyamatos átviteli egységekben (SU) mérhető. Több feladat is futtatható párhuzamosan ugyanabban a fürtben, ha az összes futó feladathoz hozzárendelt SUs összege nem haladja meg a fürt kapacitását.

A fürt kapacitása felfelé és lefelé is méretezhető, hogy megfeleljen a folyamatos átviteli feladatok méretének. A fürt méretezése időt vesz igénybe, és a gyakori skálázás nem ajánlott. Azt javasoljuk, hogy egy olyan fürtöt tervezzen és helyezzen üzembe, amely pontos mennyiségű SUs-t tervez használni.

## <a name="change-the-scale-of-your-cluster"></a>A fürt méretezésének módosítása

1. A Azure Portal keresse meg és válassza ki a Stream Analytics-fürtöt.

1. Az **Áttekintés** szakaszban válassza a **skála** lehetőséget. Láthatja, hogy hány SUs van hozzárendelve a fürthöz. A kiválasztó segítségével szükség szerint növelheti vagy csökkentheti az SUs-t.

   ![fürt méretezése](./media/scale-cluster/scale-cluster.png)

A skálázási művelet nem érinti a jelenleg futó feladatokat.

## <a name="next-steps"></a>Következő lépések

Most már tudja, hogyan méretezheti fel és le a Stream Analytics-fürtöket. Következő lépésként megismerheti a privát végpontok kezelését és a feladatok automatikus méretezését:

* [Privát végpontok kezelése Azure Stream Analytics-fürtben](private-endpoints.md)
* [Feladatok kezelése Azure Stream Analytics-fürtben](manage-jobs-cluster.md)
