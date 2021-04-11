---
title: Ismerkedés a huzavona adatforgalmával Azure Data Factory
description: Oktatóanyag az Azure Data Factory huzavona-adatfolyam használatával történő előkészítéséhez
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: cf15d6f669718cca8b99d67a7912d3959d9c191f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732505"
---
# <a name="prepare-data-with-data-wrangling"></a>Adatfeldolgozás az huzavona

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A huzavona-ben tárolt adatmennyiség lehetővé teszi, hogy natív módon hozzon létre egy interaktív Power Query Mash-UPS-t az ADF-ben, majd hajtsa végre a méretezést egy ADF-folyamaton belül.

> [!NOTE]
> Az ADF-ben Power Query tevékenység jelenleg nyilvános előzetes verzióban érhető el

## <a name="create-a-power-query-activity"></a>Power Query tevékenység létrehozása

A Azure Data Factory kétféleképpen hozhat létre Power Query. Az egyik módszer, ha a plusz ikonra kattint, és kiválasztja az **adatfolyamot** a gyári erőforrások ablaktáblán.

> [!NOTE]
> Korábban a huzavona funkció az adatfolyam munkafolyamatában található. Most létrehozza az adatait huzavona mash-up-ból ```New > Power query```

![A gyári erőforrások ablaktáblán látható Power Query bemutató képernyőkép.](media/data-flow/power-query-wrangling.png)

A másik módszer a folyamat vászonjának tevékenységek ablaktábláján található. Nyissa meg a **Power Query** harmonikát, és húzza a **Power Query** tevékenységet a vászonra.

![Képernyőkép, amely kiemeli az adathuzavonai beállítást.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Power Query adathuzavona-tevékenység szerzője

Adja hozzá a Power Query mash-up **forrás-adatkészletét** . Válasszon egy meglévő adatkészletet, vagy hozzon létre egy újat. Kiválaszthat egy fogadó adatkészletet is. Kiválaszthat egy vagy több forrás adatkészletet, de egyszerre csak egy fogadót lehet engedélyezni. A fogadó adatkészlet kiválasztása nem kötelező, de legalább egy forrás adatkészletet kötelező megadni.

![Huzavona](media/wrangling-data-flow/tutorial4.png)

Kattintson a **Létrehozás** elemre a Power Query online adategyesítés-szerkesztő megnyitásához.

![Képernyőfelvétel: a Create (létrehozás) gombra kattintva megnyílik a Power Query online mashup-szerkesztő.](media/wrangling-data-flow/tutorial5.png)

A huzavona Power Query a kód nélküli adatelőkészítéssel. Az elérhető függvények listáját lásd: [transzformációs függvények](wrangling-functions.md). Az ADF lefordítja az M-szkriptet egy adatfolyam-parancsfájlba, hogy a Power Query a Azure Data Factory adatfolyam Spark-környezettel lehessen végrehajtani.

![Képernyőfelvétel: az adathuzavona Power Query készítésének folyamatát bemutató képernyőkép.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Power Query huzavona-tevékenység futtatása és figyelése

Egy Power Query tevékenységhez tartozó hibakeresési művelet végrehajtásához kattintson a folyamat vásznon a **hibakeresés** elemre. Miután közzétette a folyamatot, az **trigger most** végrehajtja az utolsó közzétett folyamat igény szerinti futtatását. Power Query folyamatok ütemezhetők az összes meglévő Azure Data Factory eseményindítóval.

![Képernyőkép, amely bemutatja, hogyan adhat hozzá egy Power Query adathuzavona tevékenységet.](media/wrangling-data-flow/tutorial3.png)

Az aktivált Power Query tevékenység kimenetének megjelenítéséhez nyissa meg a **figyelés** lapot.

![Képernyőkép, amely megjeleníti egy aktivált huzavona kimenetét Power Query tevékenység futtatásakor.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre leképezési](tutorial-data-flow.md)adatfolyamot.
