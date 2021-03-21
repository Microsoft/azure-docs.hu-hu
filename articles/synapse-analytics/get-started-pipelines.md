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
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219402"
---
# <a name="integrate-with-pipelines"></a>Integráció a folyamatokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a folyamatokat és tevékenységeket a szinapszis Studio használatával. 

## <a name="overview"></a>Áttekintés

Az Azure Szinapszisban számos feladatot integrálhat.

1. A szinapszis Studióban nyissa meg az **integrációs** hubot.
1. Válassza **+**  >  a **folyamat** lehetőséget egy új folyamat létrehozásához. Kattintson az új folyamat objektumra a folyamat-tervező megnyitásához.
1. A **tevékenységek** területen bontsa ki a **szinapszis** mappát, és húzzon egy **Jegyzetfüzet** -objektumot a tervezőbe.
1. Válassza a jegyzetfüzet tevékenység tulajdonságai párbeszédpanel **Beállítások** lapját. A legördülő listából kiválaszthatja az aktuális szinapszis-munkaterületről származó összes jegyzetfüzetet. 
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
