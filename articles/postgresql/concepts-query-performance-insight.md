---
title: Lekérdezési terheléselemző-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL egyetlen kiszolgáló Lekérdezési terheléselemző funkcióját ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a7573e99de96e3d13509056c49bc24be7fe166bf
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906529"
---
# <a name="query-performance-insight"></a>Lekérdezési terheléselemző 

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egykiszolgálós verzió: 9,6, 10, 11

Lekérdezési terheléselemző segítségével gyorsan azonosíthatja a leghosszabb ideig futó lekérdezéseket, hogyan változnak az idő múlásával, és mi vár rájuk.

## <a name="permissions"></a>Engedélyek
A Lekérdezési terheléselemző lekérdezéseinek szövegét **Tulajdonos** vagy **Közreműködő** jogosultsággal lehet megjeleníteni. **Olvasó** jogosultsággal a diagramok és táblázatok megtekinthetők, de a lekérdezés szövege nem.

## <a name="prerequisites"></a>Előfeltételek
A Lekérdezési terheléselemző működéséhez az adatnak léteznie kell a [lekérdezési tárolóban](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>A teljesítmény-áttekintés megtekintése
A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg. 

A Azure Database for PostgreSQL-kiszolgáló portál lapján válassza a **lekérdezési teljesítmény betekintése** lehetőséget a menüsáv **intelligens teljesítmény** szakaszában.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Hosszú ideig futó lekérdezések Lekérdezési terheléselemző":::

A **hosszú ideig futó lekérdezések** lapon az első öt lekérdezés az átlagos időtartam szerint, 15 perces időközökben összesítve jelenik meg. Több lekérdezés megtekintéséhez válasszon értéket a **Lekérdezések száma** legördülő menüből. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Azt is megteheti, hogy a nagyítás és kicsinyítés ikon használatával kisebb vagy nagyobb időt szeretne megtekinteni.

A diagram alatti táblázat további részleteket tartalmaz a hosszú ideig futó lekérdezésekről az adott időablakban.

A kiszolgálóval kapcsolatos megfelelő várakozási adatok vizualizációinak megjelenítéséhez válassza a **Várakozási statisztikák** fület.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Lekérdezési terheléselemző vár statisztikái":::

## <a name="considerations"></a>Megfontolandó szempontok
* Lekérdezési terheléselemző nem érhető el az [olvasási replikák](concepts-read-replicas.md)esetében.

## <a name="next-steps"></a>Következő lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).


