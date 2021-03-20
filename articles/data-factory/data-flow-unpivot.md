---
title: Az átalakítás kibontása a leképezési adatfolyamban
description: Azure Data Factory leképezési adatfolyamok kibontásának átalakítása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040205"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Az átalakítás kibontása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A nem normalizált adatkészletek egy normalizált verzióban való kibontásához használja az ADF-leképezési adatfolyamatot úgy, hogy egy rekord több oszlopának értékeit több, egyetlen oszlopban szereplő értékkel rendelkező rekordba bővíti.

![A képernyőképen a kiválasztható jelölés látható a menüből.](media/data-flow/unpivot1.png "Kibontási beállítások 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Csoportosítás bontása

![Képernyőfelvétel: a kibontási beállítások megjelenítése a Csoportbontás kijelölésével lapon.](media/data-flow/unpivot5.png "Kibontási beállítások 2")

Először állítsa be azokat az oszlopokat, amelyeket meg szeretne határozni az unpivot összesítéshez. Állítson be egy vagy több oszlopot a csoportosítás kibontásához az oszlopok listája melletti + jelre.

## <a name="unpivot-key"></a>Kulcs elforgatása

![Képernyőfelvétel: a kibontási beállítások megjelenítése a kiválasztható kulccsal lapon.](media/data-flow/unpivot6.png "Kibontási beállítások 3")

A kibontási kulcs az az oszlop, amelyet az ADF az oszloptól a sorba fog mutatni. Alapértelmezés szerint a mezőhöz tartozó adatkészlet minden egyedi értéke egy sorra mutat. Azonban megadhatja az adatkészletből azokat az értékeket, amelyeket meg szeretne adni a sorok értékeinek.

## <a name="unpivoted-columns"></a>Nem csuklós oszlopok

![Képernyőfelvétel: a kibontási beállítások megjelenítése a kijelölt adatelőnézet lapon.](media/data-flow//unpivot7.png "Kibontási beállítások 4")

Végül válassza ki az oszlop nevét a sorokba átalakított, nem tagolt oszlopok értékeinek tárolásához.

Választható NULL értékű sorokat is elhelyezhet.

A SumCost például a fent megosztott példában kiválasztott oszlop neve.

![Az a kép, amely a PO, a szállító és a gyümölcs oszlopokat mutatja a unipivot átalakítás előtt és után, a Fruit (gyümölcs) oszlop használatával, unipivot kulcsként.](media/data-flow/unpivot3.png)

Ha az oszlop elrendezését a "NORMAL" értékre állítja, akkor az összes új, nem felmutatott oszlop egyetlen értékből lesz csoportosítva. Ha az oszlopok elrendezése az "oldalirányú" értékre van állítva, akkor a rendszer egy meglévő oszlopból generált új, nem felmutatott oszlopokat csoportosítja.

![A képernyőkép a transzformáció eredményét jeleníti meg.](media/data-flow//unpivot7.png "Kibontási beállítások 5")

A végleges, nem felváltott adateredményhalmaz azt mutatja, hogy az oszlopok összesítései mostantól külön sorba vannak bontva.

## <a name="next-steps"></a>Következő lépések

Sorok oszlopokra való kimutatásához használja a [pivot transzformációt](data-flow-pivot.md) .
