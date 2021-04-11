---
title: Az Azure Sentinel Azure Monitor-munkafüzetek használatával jelenítheti meg az adatait | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg az adatait az Azure Sentinel-munkafüzetek használatával.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2020
ms.author: yelevin
ms.openlocfilehash: 048a089209ef7c5f20c96f77593e2cf39590147e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600526"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Oktatóanyag: Az adatok megjelenítése és figyelése



Miután [csatlakoztatta az adatforrásokat](quickstart-onboard.md) az Azure sentinelhez, az Azure monitor-munkafüzetek Azure Sentinel-bevezetésével megjelenítheti és figyelheti az adatait, ami sokoldalúságot biztosít az egyéni irányítópultok létrehozásához. Míg a munkafüzetek eltérően jelennek meg az Azure Sentinelben, hasznos lehet megtekinteni, hogyan [hozhat létre interaktív jelentéseket Azure monitor-munkafüzetek](../azure-monitor/visualize/workbooks-overview.md)használatával. Az Azure Sentinel lehetővé teszi, hogy egyéni munkafüzeteket hozzon létre az adatai között, és a beépített munkafüzet-sablonokkal is lehetővé teszi, hogy az adatforrások összekapcsolását követően gyorsan szerezzen információt az adatelemzésről.


Ez az oktatóanyag segítséget nyújt az Azure Sentinelben tárolt adatai megjelenítéséhez.
> [!div class="checklist"]
> * Beépített munkafüzetek használata
> * Új munkafüzetek létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Sentinel munkaterület erőforráscsoporthoz legalább munkafüzet-olvasó vagy munkafüzet közreműködői engedélyekkel kell rendelkeznie.

> [!NOTE]
> Az Azure Sentinelben megtekinthető munkafüzetek az Azure Sentinel-munkaterület erőforráscsoport-csoportjában lesznek mentve, és azokat a munkaterületet címkézik, amelyben létrehozták őket.

## <a name="use-built-in-workbooks"></a>Beépített munkafüzetek használata

1. Nyissa meg a **munkafüzetek** elemet, majd válassza a **sablonok** lehetőséget az Azure Sentinel beépített munkafüzetek teljes listájának megtekintéséhez. Ha szeretné megtekinteni, hogy mely információkra van szüksége a csatlakoztatott adattípusokhoz, az egyes munkafüzetek **kötelező adattípusok** mezője a zöld pipa melletti adattípust sorolja fel, ha már továbbít releváns adatokat az Azure Sentinel szolgáltatásnak.
  ![Ugrás a munkafüzetekhez](./media/tutorial-monitor-data/access-workbooks.png)
1. A sablon **megtekintése** elemre kattintva megtekintheti az adataival feltöltött sablont.
  
1. A munkafüzet szerkesztéséhez válassza a **Mentés** lehetőséget, majd válassza ki azt a helyet, ahová menteni szeretné a sablon JSON-fájlját. 

   > [!NOTE]
   > Ez létrehoz egy Azure-erőforrást a megfelelő sablon alapján, és elmenti a munkafüzet JSON-fájlját, és nem az adatokat.


1. Válassza a **mentett munkafüzet megtekintése** lehetőséget. Ezután kattintson a felül található **Szerkesztés** gombra. Szerkesztheti a munkafüzetet, és igényei szerint testre szabhatja. A munkafüzet testreszabásával kapcsolatos további információkért lásd: [interaktív jelentések létrehozása Azure monitor-munkafüzetek](../azure-monitor/visualize/workbooks-overview.md)használatával.
![munkafüzetek megtekintése](./media/tutorial-monitor-data/workbook-graph.png)
1. A módosítások elvégzése után mentheti a munkafüzetet. 

1. A munkafüzet klónozása is megtehető: válassza a **Szerkesztés** , majd a **Mentés másként** lehetőséget, hogy egy másik névvel mentse azt az előfizetéshez és az erőforráscsoporthoz. Ezek a klónozott munkafüzetek a **saját munkafüzetek** lapon jelennek meg.


## <a name="create-new-workbook"></a>Új munkafüzet létrehozása

1. Nyissa meg a **munkafüzetek** elemet, majd válassza a **munkafüzet hozzáadása** lehetőséget, hogy új munkafüzetet hozzon létre a semmiből.
  ![Képernyőkép, amely az új munkafüzet képernyőjét jeleníti meg.](./media/tutorial-monitor-data/create-workbook.png)

1. A munkafüzet szerkesztéséhez válassza a **Szerkesztés** lehetőséget, majd szükség szerint adja hozzá a szöveget, a lekérdezéseket és a paramétereket. A munkafüzet testreszabásával kapcsolatos további információkért lásd: [interaktív jelentések létrehozása Azure monitor-munkafüzetek](../azure-monitor/visualize/workbooks-overview.md)használatával. 

1. Lekérdezés létrehozásakor győződjön meg arról, hogy az **adatforrás** **naplók** és **erőforrástípus** értékre **log Analytics** van állítva, majd válassza ki a megfelelő munkaterületet (ka) t. 

1. A munkafüzet létrehozása után mentse a munkafüzetet, és győződjön meg róla, hogy az Azure Sentinel-munkaterület előfizetés és erőforráscsoport területén menti.

1. Ha azt szeretné, hogy a szervezeten belül mások is használhassák a munkafüzetet, a **Mentés** lehetőség alatt válassza a **megosztott jelentések** lehetőséget. Ha azt szeretné, hogy ez a munkafüzet csak Ön számára legyen elérhető, válassza **a saját jelentések** lehetőséget.

1. A munkaterületen lévő munkafüzetek közötti váltáshoz válassza a **Megnyitás** ![ ikont a munkafüzet megnyitásához. ](./media/tutorial-monitor-data/switch.png) bármelyik munkafüzet felső ablaktábláján. A jobb oldalon megnyíló ablakban váltson a munkafüzetek között.

   ![Munkafüzetek váltása](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="print-a-workbook-or-save-as-pdf"></a>Munkafüzet nyomtatása vagy Mentés PDF formátumban

Egy munkafüzet nyomtatásához vagy PDF-fájlként való mentéséhez használja a munkafüzet címének jobb oldalán lévő beállítások menüt.

1. Válassza a beállítások > a :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Tartalom nyomtatása** lehetőséget. 
2. A Print (nyomtatás) képernyőn szükség szerint módosítsa a nyomtatási beállításokat, vagy válassza a **Mentés PDF-ként** lehetőséget a helyi mentéshez.

Például:

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Nyomtassa ki a munkafüzetet, vagy mentse PDF-ként.":::

## <a name="how-to-delete-workbooks"></a>Munkafüzetek törlése

Mentett munkafüzet (mentett sablon vagy testreszabott munkafüzet) törléséhez a munkafüzetek lapon válassza ki a törölni kívánt mentett munkafüzetet, és válassza a **Törlés** lehetőséget. Ezzel törli a mentett munkafüzetet.

> [!NOTE]
> Ezzel eltávolítja a munkafüzet-erőforrást, valamint a sablonon végrehajtott módosításokat. Az eredeti sablon továbbra is elérhető marad.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan jelenítheti meg adatait az Azure Sentinelben az Azure-munkafüzetek használatával.

A fenyegetésekre adott válaszok automatizálásával kapcsolatos további információkért lásd: [automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure sentinelben](tutorial-respond-threats-playbook.md).
