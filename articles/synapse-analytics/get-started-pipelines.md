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
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365467"
---
# <a name="integrate-with-pipelines"></a>Integrálás folyamatokkal

Ez az oktatóanyag bemutatja, hogyan integrálhatók folyamatok és tevékenységek a Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Folyamat létrehozása és jegyzetfüzet-tevékenység hozzáadása



1. A Synapse Studio az Integrálás **központot.**
1. Új **+**  >  **folyamat létrehozásához** válassza a Folyamat lehetőséget. Kattintson az új folyamat objektumra a Folyamattervező megnyitásához.
1. A **Tevékenységek alatt** bontsa ki a **Synapse mappát,** és húzzon egy **Notebook-objektumot** a tervezőbe.
1. Válassza a **Jegyzetfüzet-tevékenység** tulajdonságai párbeszédpanel Beállítások lapját. A legördülő listában az aktuális Synapse-munkaterület bármelyik jegyzetfüzetét kiválaszthatja.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>A folyamat ütemezése óránkénti futásra

1. A folyamatban válassza az **Eseményindító hozzáadása**  >  **Új/szerkesztés lehetőséget.**
1. Az **Eseményindító kiválasztása mezőben** válassza az **Új** lehetőséget, és állítsa az **Ismétlődés** beállítását "1 óránként" beállításra.
1. Válassza az **OK** lehetőséget. 
1. Kattintson **Az összes közzététele** gombra. 


## <a name="monitor-pipeline-execution"></a>Folyamat végrehajtásának monitorozása

1. A folyamat közzététele után a folyamat azonnali futtatásához a következő óra várakozása nélkül válassza az **Eseményindító hozzáadása**  >  **eseményindító most lehetőséget.**
1. A Synapse Studio a Figyelő **központot.**
1. Válassza **a Folyamatfuttatások lehetőséget** a folyamat végrehajtási folyamatának monitorozása érdekében.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok megjelenítése Power BI használatával](get-started-visualize-power-bi.md)
