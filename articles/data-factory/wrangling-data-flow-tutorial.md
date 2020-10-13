---
title: Ismerkedés a huzavona adatforgalmával Azure Data Factory
description: Oktatóanyag az Azure Data Factory huzavona-adatfolyam használatával történő előkészítéséhez
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e9a4f7266522fc17c33e2086af6cb7367bbed39e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650680"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Az huzavona-adatfolyamokkal történő előkészítés

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> A huzavona-adatfolyam jelenleg nyilvános előzetes verzióban avilable

## <a name="create-a-wrangling-data-flow"></a>Huzavona-adatfolyam létrehozása

A Azure Data Factory kétféleképpen hozhat létre huzavona-adatfolyamokat. Az egyik módszer, ha a plusz ikonra kattint, és kiválasztja az **adatfolyamot** a gyári erőforrások ablaktáblán.

![Képernyőkép, amely a gyári erőforrások ablaktáblán megjeleníti az adatfolyamatot.](media/wrangling-data-flow/tutorial7.png)

A másik módszer a folyamat vászonjának tevékenységek ablaktábláján található. Nyissa meg az **áthelyezés és átalakítás** egyezést, és húzza az **adatfolyam** tevékenységet a vászonra.

Mindkét módszernél a megnyíló oldal ablaktáblán válassza az **új adatfolyam létrehozása** elemet, és válassza a **huzavona-adatfolyam**lehetőséget. Kattintson az OK gombra.

![Képernyőkép a huzavona-adatfolyam lehetőségről.](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Huzavona-adatfolyam készítése

Adja hozzá a huzavona-adatfolyam **forrás-adatkészletét** . Válasszon egy meglévő adatkészletet, vagy hozzon létre egy újat. Kiválaszthat egy fogadó adatkészletet is. Kiválaszthat egy vagy több forrás adatkészletet, de egyszerre csak egy fogadót lehet engedélyezni. A fogadó adatkészlet kiválasztása nem kötelező, de legalább egy forrás adatkészletet kötelező megadni.

> [!NOTE]
> Korlátozott előzetes verzió esetén csak a ADLS Gen 2 tagolt szöveg támogatott. 

![Huzavona](media/wrangling-data-flow/tutorial4.png)

Kattintson a **Létrehozás** elemre a Power Query online adategyesítés-szerkesztő megnyitásához.

![Képernyőfelvétel: a Create (létrehozás) gombra kattintva megnyílik a Power Query online mashup-szerkesztő.](media/wrangling-data-flow/tutorial5.png)

A huzavona-adatfolyamok létrehozása kód nélküli adatelőkészítéssel. Az elérhető függvények listáját lásd: [transzformációs függvények](wrangling-data-flow-functions.md).

![Képernyőkép, amely a huzavona-adatfolyam készítésének folyamatát mutatja be.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Huzavona-adatfolyam futtatása és figyelése

Egy huzavona-adatfolyam hibakeresési folyamatának végrehajtásához kattintson a folyamat vásznon a **hibakeresés** elemre. Miután közzétette az adatfolyamot, a **trigger most** végrehajtja az utolsó közzétett folyamat igény szerinti futtatását. Az huzavona-adatfolyamatok az összes meglévő Azure Data Factory eseményindítóval ütemezhetők.

![Képernyőkép, amely bemutatja, hogyan adhat hozzá egy huzavona-adatfolyamot.](media/wrangling-data-flow/tutorial3.png)

Lépjen a **figyelés** lapra, és jelenítse meg az aktivált huzavona adatfolyam-tevékenységek kimenetét.

![Képernyőkép, amely egy aktivált huzavona adatfolyam-tevékenység kimenetét jeleníti meg.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre leképezési](tutorial-data-flow.md)adatfolyamot.