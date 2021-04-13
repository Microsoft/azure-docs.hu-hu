---
title: 'Oktatóanyag: a folyamatok integrálásának első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a folyamatokat és tevékenységeket a szinapszis Studio használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 22631cfd872ed226fc78a97d38c423fbc300f78e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304714"
---
# <a name="integrate-with-pipelines"></a>Integráció a folyamatokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a folyamatokat és tevékenységeket a szinapszis Studio használatával. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Folyamat létrehozása és jegyzetfüzet-tevékenység hozzáadása



1. A szinapszis Studióban nyissa meg az **integrációs** hubot.
1. Válassza **+**  >  a **folyamat** lehetőséget egy új folyamat létrehozásához. Kattintson az új folyamat objektumra a folyamat-tervező megnyitásához.
1. A **tevékenységek** területen bontsa ki a **szinapszis** mappát, és húzzon egy **Jegyzetfüzet** -objektumot a tervezőbe.
1. Válassza a jegyzetfüzet tevékenység tulajdonságai párbeszédpanel **Beállítások** lapját. A legördülő listából kiválaszthatja az aktuális szinapszis-munkaterületről származó összes jegyzetfüzetet.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>A folyamat óránkénti futtatásának ütemezett futtatása

1. A folyamatban válassza a **Hozzáadás trigger**  >  **új/szerkesztés** lehetőséget.
1. Az **eseményindító kiválasztása** területen válassza az **új** lehetőséget, majd állítsa az **ismétlődést** "minden 1 órában" értékre.
1. Válassza az **OK** lehetőséget. 
1. Kattintson **Az összes közzététele** gombra. 


## <a name="monitor-pipeline"></a>Folyamat figyelése

1. Miután közzétette a folyamatot, hogy a folyamat azonnal fusson, és ne várjon a következő órára, válassza az **aktiválási**  >  **trigger hozzáadása most** lehetőséget.
1. A szinapszis Studióban nyissa meg a **figyelő** központot, és válassza a **folyamat futtatása** lehetőséget a folyamat végrehajtási folyamatának figyeléséhez.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok megjelenítése Power BI használatával](get-started-visualize-power-bi.md)
