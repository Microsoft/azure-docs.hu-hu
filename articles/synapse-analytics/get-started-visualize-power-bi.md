---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással – munkaterület-adatmegjelenítés Power BI'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Power BI munkaterületet, hogyan kapcsolhatja össze az Azure szinapszis-munkaterületet, és hogyan hozhat létre olyan Power BI-adatkészletet, amely az Azure szinapszis munkaterületen használja az információkat.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 03/25/2021
ms.openlocfilehash: 829daaa9c739657528a9085201c61c88635931d0
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608442"
---
# <a name="visualize-data-with-power-bi"></a>Adatok megjelenítése Power BI használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Power BI munkaterületet, hogyan kapcsolhatja össze az Azure szinapszis-munkaterületet, és hogyan hozhat létre olyan Power BI adatkészletet, amely az Azure szinapszis-munkaterületen található információkat használja. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez [telepítse a Power bi Desktop](https://aka.ms/pbidesktopstore).

## <a name="overview"></a>Áttekintés

A New York-i taxi-adatokból összesített adatkészleteket hoztunk létre két táblában:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Power BI munkaterületet az Azure szinapszis-munkaterülethez kapcsolhat. Ez a funkció lehetővé teszi, hogy egyszerűen beolvassa az adatait a Power BI munkaterületre. Az Power BI-jelentéseket közvetlenül az Azure szinapszis munkaterületen szerkesztheti. 

### <a name="create-a-power-bi-workspace"></a>Power BI munkaterület létrehozása

1. Jelentkezzen be a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Kattintson a **munkaterületek** elemre, majd válassza **a munkaterület létrehozása** lehetőséget. Hozzon létre egy **NYCTaxiWorkspace1** vagy hasonló nevű új Power bi munkaterületet, mert ennek a névnek egyedinek kell lennie.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Az Azure szinapszis-munkaterület összekapcsolása az új Power BI munkaterülettel

1. A szinapszis Studióban lépjen a   >  **társított szolgáltatások** kezelése elemre.
1. Válassza   >  **az új kapcsolódás a Power BIhoz** lehetőséget.
1. Állítsa a **nevet** a **NYCTaxiWorkspace1** értékre.
1. Állítsa be a **munkaterület nevét** a fent létrehozott Power bi munkaterületre, hasonlóan a **NYCTaxiWorkspace1**-hez.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Az Azure szinapszis-munkaterületen adatokat használó Power BI adatkészlet létrehozása

1. A szinapszis Studióban lépjen a **fejlesztés**  >  **Power bi** elemre.
1. Lépjen a **NYCTaxiWorkspace1**  >  **Power bi adatkészletek** elemre, és válassza az **új Power bi adatkészlet** lehetőséget. Kattintson a **Start** gombra.
1. Válassza ki a **SQLPOOL1** -adatforrást, majd kattintson a **Folytatás** gombra.
1. Kattintson a **Letöltés** gombra a **NYCTaxiWorkspace1SQLPOOL1. pbids** fájl. pbids fájljának letöltéséhez. Kattintson a **Folytatás** gombra.
1. Nyissa meg a letöltött **. pbids** fájlt. A Power BI Desktop megnyílik, és automatikusan csatlakozik a **SQLDB1** -hez az Azure szinapszis-munkaterületen.
1. Ha megjelenik egy **SQL Server adatbázis** nevű párbeszédpanel:
    1. Válassza a **Microsoft-fiók** lehetőséget.
    1. Válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be a fiókjába.
    1. Válassza a **Kapcsolódás** lehetőséget.
1. A **navigátor** párbeszédpanel megnyitása után jelölje be a **PassengerCountStats** tábla jelölőnégyzetét, és válassza a **Betöltés** lehetőséget.
1. Ha megjelenik a **kapcsolatbeállítások** párbeszédpanel, válassza a **DirectQuery**  >  **OK** elemet.
1. Kattintson a bal oldalon található **jelentés** gombra.
1. A **vizualizációk** területen kattintson a vonal diagram ikonjára, és vegyen fel egy **vonalas diagramot** a jelentésbe.
    1. A **mezők** területen húzza a **PassengerCount** oszlopot a **vizualizációk**  >  **tengelyére**.
    1. Húzza a **SumTripDistance** és a **AvgTripDistance** oszlopot a **vizualizációk**  >  **értékeire**.
1. A **Kezdőlap** lapon válassza a **Közzététel** lehetőséget.
1. A módosítások mentéséhez válassza a **Mentés** gombot.
1. Válassza ki a **PassengerAnalysis. pbix** fájl nevét, majd kattintson a **Mentés** gombra.
1. A **közzététel Power bi** ablakban a **cél kiválasztása** területen válassza ki a **NYCTaxiWorkspace1**, majd kattintson a **kiválasztás** elemre.
1. Várjon, amíg a közzététel befejeződik. 

### <a name="configure-authentication-for-your-dataset"></a>Az adatkészlet hitelesítésének konfigurálása

1. Nyissa meg a [powerbi.microsoft.com](https://powerbi.microsoft.com/) , és **Jelentkezzen be**.
1. A bal oldalon, a **munkaterületek** területen válassza a **NYCTaxiWorkspace1** -munkaterületet.
1. A munkaterületen belül keresse meg az **utas-elemzés** nevű adatkészletet és egy **utas-elemzés** nevű jelentést.
1. Vigye a kurzort a **PassengerAnalysis** adatkészlet fölé, válassza a három pontot (...), majd válassza a **Beállítások** lehetőséget.
1. Az **adatforrás hitelesítő adatai** területen kattintson a **Szerkesztés** elemre, állítsa a **hitelesítési módszert** **OAuth2** értékre, majd válassza a **Bejelentkezés** lehetőséget.

### <a name="edit-a-report-in-synapse-studio"></a>Jelentés szerkesztése a szinapszis Studióban

1. Lépjen vissza a szinapszis studióba, és válassza a **Bezárás és frissítés** lehetőséget.
1. Nyissa meg a **fejlesztés** központot.
1. A **Power bi** réteg, a három pont (...) gombtól jobbra, majd a **frissítés** gombra kattintva frissítheti az **Power bi jelentések** csomópontot.
1. **Power bi** alatt meg kell jelennie a következőknek:
    * A **NYCTaxiWorkspace1**  >  **Power bi adatkészletekben** egy új, **PassengerAnalysis** nevű adathalmazt.
    * A **NYCTaxiWorkspace1**  >  **Power bi-jelentések** területen egy új, **PassengerAnalysis** nevű jelentés szerepel.
1. Válassza ki a **PassengerAnalysis** jelentést. Megnyílik a jelentés, amely közvetlenül a szinapszis Studióban szerkeszthető.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Figyelés](get-started-monitor.md)
                                 

