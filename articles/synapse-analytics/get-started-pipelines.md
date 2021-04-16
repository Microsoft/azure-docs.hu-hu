---
title: 'Oktatóanyag: Első lépések a folyamatokkal való integrációhoz'
description: Ez az oktatóanyag bemutatja, hogyan integrálhatók folyamatok és tevékenységek a Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518252"
---
# <a name="integrate-with-pipelines"></a>Integrálás folyamatokkal

Ez az oktatóanyag bemutatja, hogyan integrálhatók folyamatok és tevékenységek a Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Folyamat létrehozása és jegyzetfüzet-tevékenység hozzáadása

1. A Synapse Studio az Integrálás **központot.**
1. Új **+**  >  **folyamat létrehozásához** válassza a Folyamat lehetőséget. Kattintson az új folyamatobjektumra a Folyamattervező megnyitásához.
1. A **Tevékenységek alatt** bontsa ki a **Synapse mappát,** és húzzon egy **Notebook-objektumot** a tervezőbe.
1. Válassza a **Jegyzetfüzet-tevékenység** tulajdonságai párbeszédpanel Beállítások lapját. A legördülő listában válasszon ki egy jegyzetfüzetet az aktuális Synapse-munkaterületről.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>A folyamat ütemezése óránkénti futásra

1. A folyamatban válassza az **Eseményindító hozzáadása**  >  **Új/szerkesztés lehetőséget.**
1. Az **Eseményindító kiválasztása mezőben** válassza az **Új** lehetőséget, és állítsa az **Ismétlődés** beállítását "1 óránként" -re.
1. Válassza az **OK** lehetőséget. 
1. Kattintson **Az összes közzététele** gombra. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Folyamat azonnali futtatásának kényszerítés

A folyamat közzététele után előfordulhat, hogy azonnal futtatni szeretné anélkül, hogy egy órát várnia kell.

1. Nyissa meg a folyamatot.
1. Kattintson **az Eseményindító hozzáadása**  >  **eseményindító most elemre.**

## <a name="monitor-pipeline-execution"></a>Folyamat végrehajtásának monitorozása

1. Ugrás a **Figyelő központra.**
1. Válassza **a Folyamatfuttatások lehetőséget** a folyamat végrehajtási folyamatának monitorozása érdekében.
1. Ebben a nézetben válthat a táblázatos lista **grafikus** **Gantt-diagram megjelenítéséhez.** 
1. Kattintson egy folyamat nevére a folyamatban végzett tevékenységek állapotának megtekintése érdekében.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok megjelenítése Power BI használatával](get-started-visualize-power-bi.md)
