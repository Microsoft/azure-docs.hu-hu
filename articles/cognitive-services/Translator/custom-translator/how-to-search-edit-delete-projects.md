---
title: Projekt keresése, szerkesztése és törlése – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Az egyéni Translator számos módszert kínál a projektek hatékony kezelésére. Több projektet is létrehozhat, a feltételek alapján kereshet, és szerkesztheti a projekteket. Egy projekt törlése az egyéni fordítóban is lehetséges.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896443"
---
# <a name="search-edit-and-delete-projects"></a>Projektek keresése, szerkesztése és törlése

Az egyéni Translator számos módszert kínál a projektek hatékony kezelésére. Több projektet is létrehozhat, a feltételek alapján kereshet, és szerkesztheti a projekteket. Egy projekt törlése az egyéni fordítóban is lehetséges.  

## <a name="search-and-filter-projects"></a>Projektek keresése és szűrése

A szűrő eszköz lehetővé teszi a projektek különböző szűrési feltételek szerinti keresését. Az informatikai szűrők, például a projekt neve, állapota, a forrás és a cél nyelve, valamint a projekt kategóriája.

1. Kattintson a szűrő gombra.

    ![Keresési projekt](media/how-to/how-to-search-project.png)

2. A következő mezők bármelyikének (vagy mindegyikének) szűrésére van lehetőség: a projekt neve, a forrás nyelve, a cél nyelve, a kategória és a projekt rendelkezésre állása.

3. Kattintson az Alkalmaz gombra.

    ![Keresési projekt szűrési beállításai](media/how-to/how-to-search-project-filters.png)

4. Törölje a szűrőt az összes projekt megtekintéséhez a "Törlés" gombra koppintva.

## <a name="edit-a-project"></a>Projekt szerkesztése

Az egyéni fordító lehetővé teszi a projekt nevének és leírásának szerkesztését. Más projekt-metaadatok, például a kategória, a forrás nyelv és a célnyelv nem érhetők el szerkesztésre. Az alábbi lépések a projektek szerkesztésének módját ismertetik.

1. Kattintson a ceruza ikonra, amely akkor jelenik meg, amikor egy projekt fölé viszi a kurzort.

    ![Projekt szerkesztése](media/how-to/how-to-edit-project.png)

2. A párbeszédpanelen módosíthatja a projekt nevét, a projekt leírását, a kategória leírását, valamint a projekt címkéjét, ha nincs modell telepítve. A projekt létrehozása után nem módosítható a kategória vagy a nyelvi pár.

    ![Projekt szerkesztése párbeszédpanel](media/how-to/how-to-edit-project-dialog.png)

3. Kattintson a Save (Mentés) gombra.

## <a name="delete-a-project"></a>Projekt törlése

Ha már nincs szüksége rá, törölheti a projektet. Győződjön meg arról, hogy a projekt nem rendelkezik aktív állapotú modellekkel (például központilag telepített, betanított, adatfeldolgozás, üzembe helyezés stb.), ellenkező esetben a törlési művelet meghiúsul. Az alábbi lépések bemutatják a projektek törlésének módját.

1. Vigye a kurzort bármelyik projekt rekordra, és kattintson a Kuka ikonra.

   ![Projekt törlése](media/how-to/how-to-delete-project.png)

2. Erősítse meg a törlést. Egy projekt törlésével a projekten belül létrehozott összes modell törlődni fog. A projekt törlése nem befolyásolja a dokumentumokat.

   ![Megerősítő párbeszédpanel törlése](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Következő lépések

- [Dokumentumok feltöltése](how-to-upload-document.md) az egyéni fordítási modell létrehozásához.
