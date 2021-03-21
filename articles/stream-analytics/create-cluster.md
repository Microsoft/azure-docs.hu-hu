---
title: Azure Stream Analytics-fürt létrehozása – rövid útmutató
description: Megtudhatja, hogyan hozhat létre Azure Stream Analytics-fürtöt.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 6b7a7c5feb6165f5b2c7c0ea052318a255e8025c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600224"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Rövid útmutató: dedikált Azure Stream Analytics-fürt létrehozása Azure Portal használatával

A Azure Portal használatával hozzon létre egy Azure Stream Analytics-fürtöt. A [stream Analytics-fürt](cluster-overview.md) egy egybérlős üzemelő példány, amely összetett és igényes adatfolyam-használati esetekhez használható. Stream Analytics-fürtökön több Stream Analytics feladatot is futtathat.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A rövid útmutató befejezése [: hozzon létre egy stream Analytics feladatot a Azure Portal használatával](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Stream Analytics-fürt létrehozása

Ebben a szakaszban létre fog hozni egy Stream Analytics fürterőforrás-erőforrást.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza az **Erőforrás létrehozása** lehetőséget. A *Keresés a piactéren* mezőbe írja be a kifejezést, és válassza ki **stream Analytics fürtöt**. Ezután válassza a **Hozzáadás** elemet.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Stream Analytics a fürt keresési eredményét.":::

1. A **stream Analytics-fürt létrehozása** lapon adja meg az új fürt alapszintű beállításait.

   |Beállítás|Érték|Leírás |
   |---|---|---|
   |Előfizetés|Előfizetés neve|Válassza ki a Stream Analytics-fürthöz használni kívánt Azure-előfizetést. |
   |Erőforráscsoport|Erőforráscsoport neve|Válasszon ki egy erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget, majd adjon meg egy egyedi nevet az új erőforráscsoport számára. |
   |Fürt neve|Egyedi név|Adjon meg egy nevet a Stream Analytics-fürt azonosításához.|
   |Hely|Az adatforrásokhoz és a mosdóhoz legközelebb eső régió|Válasszon ki egy földrajzi helyet a Stream Analytics-fürt üzemeltetéséhez. Használja az adatforrásokhoz legközelebb eső helyet, és az alacsony késésű elemzésekhez.|
   |Folyamatos átviteli egység kapacitása|36 – 216 |Határozza meg a fürt méretét úgy, hogy megbecsüli, hány Stream Analytics feladatot szeretne futtatni, és a feladatokhoz szükséges teljes SUs-t. A 36 SUs és a későbbi vertikális fel-vagy leskálázás igény szerint kezdődhet.|

   ![Fürt létrehozása](./media/create-cluster/create-cluster.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **címkék** szakaszt kihagyhatja.

1. Tekintse át a fürt beállításait, majd válassza a **Létrehozás** lehetőséget. A fürt létrehozása hosszú ideig futó művelet, amely körülbelül 60 percet is igénybe vehet. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. Addig is létrehozhat és fejleszthet [stream Analytics feladatokat](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) , amelyeket ezen a fürtön futtatni szeretne, ha még nem tette meg.

1. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy a stream Analytics-fürt lapra ugorjon.

## <a name="delete-your-cluster"></a>Fürt törlése

Ha nem tervezi Stream Analytics feladatok futtatását, törölheti a Stream Analytics-fürtöt. Törölje a fürtöt a következő lépésekkel a Azure Portal:

1. Lépjen **stream Analytics feladatok** elemre a **Beállítások** területen, és állítsa le az összes futó feladatot.

1. Nyissa meg a fürt **áttekintését** . Válassza a **Törlés** lehetőséget, majd kövesse az utasításokat a fürt törléséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Azure Stream Analytics-fürtöt. A következő cikkből megtudhatja, hogyan futtathat egy Stream Analytics feladatot a fürtön:

> [!div class="nextstepaction"]
> [Stream Analytics feladatok kezelése Stream Analytics-fürtben](manage-jobs-cluster.md)
