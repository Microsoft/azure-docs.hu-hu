---
title: Fájlok másolása több tárolóból
description: Megtudhatja, hogyan másolhat több tárolóból származó fájlokat Azure Data Factory használatával a megoldási sablon használatával.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: f78d0b02c9790234a63ef64200dcab72bc64c033
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629425"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Több mappa másolása Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk egy megoldási sablont ismertet, amely több másolási tevékenységet is használhat a tárolók és mappák másolásához a fájl alapú tárolók között, ahol minden egyes másolási tevékenységnek egy tárolót vagy mappát kell másolni. 

> [!NOTE]
> Ha egyetlen tárolóból szeretné átmásolni a fájlokat, hatékonyabban használhatja a [adatok másolása eszközt](copy-data-tool.md) egyetlen másolási tevékenységgel rendelkező folyamat létrehozásához. Az ebben a cikkben szereplő sablon több, mint amire szüksége van az adott egyszerű forgatókönyvhöz.

## <a name="about-this-solution-template"></a>Tudnivalók a megoldási sablonról

Ez a sablon a forrás Storage-tárolóban lévő adott szülőmappa mappáit sorolja fel. Ezután az összes mappát átmásolja a célhelyre.

A sablon három tevékenységet tartalmaz:
- A **GetMetaData** megvizsgálja a forrásként szolgáló tárolót, és lekéri az almappák listáját egy adott szülő mappából.
- A **foreach** lekéri az almappák listáját a **GetMetaData** tevékenységből, majd megismétli a listát, és átadja az egyes mappákat a másolási tevékenységnek.
- **Másolja** az egyes mappákat a forrásként szolgáló tárolóból a célhelyre.

A sablon a következő paramétereket definiálja:
- A *SourceFileFolder* része az adatforrás-tároló szülő mappájának elérési útja: *SourceFileFolder/SourceFileDirectory* , ahol lekérheti az almappák listáját. 
- A *SourceFileDirectory* része az adatforrás-tároló szülő mappájának elérési útja: *SourceFileFolder/SourceFileDirectory* , ahol lekérheti az almappák listáját. 
- A *DestinationFileFolder* része a szülőmappa elérési útja: *DestinationFileFolder/DestinationFileDirectory* , ahol a fájlokat a rendszer átmásolja a célhelyre. 
- A *DestinationFileDirectory* része a szülőmappa elérési útja: *DestinationFileFolder/DestinationFileDirectory* , ahol a fájlokat a rendszer átmásolja a célhelyre. 

Ha több tárolót szeretne másolni a gyökérmappa között a tárolók között, a négy paramétert is beírhatja a következőre: */* . Ezzel a művelettel mindent replikálhat a Storage-tárolók között.

## <a name="how-to-use-this-solution-template"></a>A megoldás sablonjának használata

1. Lépjen a **több fájl másolása tárolók között a fájlmegosztás** sablonba. Hozzon létre egy **új** kapcsolódást a forrás Storage-tárolóhoz. A forrásként szolgáló tár az a hely, ahol a fájlokat több tárolóból kívánja másolni.

    ![Új kapcsolódás létrehozása a forráshoz](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. Hozzon létre egy **új** kapcsolódást a cél Storage-tárolóhoz.

    ![Új kapcsolódás létrehozása a célhoz](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. Válassza **a sablon használata** lehetőséget.

    ![Sablon használata](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. Ekkor megjelenik a folyamat, ahogy az alábbi példában is látható:

    ![A folyamat megjelenítése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. Válassza a **hibakeresés** lehetőséget, adja meg a **paramétereket** , majd kattintson a **Befejezés gombra** .

    ![A folyamat futtatása](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. Tekintse át az eredményt.

    ![Az eredmény áttekintése](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>Következő lépések

- [Tömeges másolás adatbázisból egy Azure Data Factory tartalmazó vezérlőelem-táblázat használatával](solution-template-bulk-copy-with-control-table.md)

- [Több tárolóból származó fájlok másolása Azure Data Factory](solution-template-copy-files-multiple-containers.md)
