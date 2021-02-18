---
title: Azure Monitor munkafüzet összetett sávjának renderelése
description: További információ a Azure Monitor munkafüzet összetett görgetősávjának vizualizációja.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 3bcbd2a58764003c174c02ec637050b57632048a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618875"
---
# <a name="composite-bar-renderer"></a>Összetett sáv megjelenítő

A munkafüzet lehetővé teszi az adatmegjelenítést az összetett sáv használatával, egy sáv, amely több görgetősávból áll.

Az alábbi képen az adatbázis állapotához tartozó összetett sáv látható, amely azt jelzi, hogy hány kiszolgáló online, offline állapotban van, és helyreállítás alatt áll.

![Az adatbázis állapotára vonatkozó összetett sáv képernyőképe.](./media/workbooks-composite-bar/database-status.png)

Az összetett sáv megjelenítő a rácsok, csempék és gráfok vizualizációi esetében támogatott.

## <a name="adding-composite-bar-renderer"></a>Összetett sáv renderelésének hozzáadása

1. Váltson át a munkafüzet szerkesztési módjára az eszköztár *szerkesztése* elem kiválasztásával.
2. Válassza a *Hozzáadás* , majd a *lekérdezés hozzáadása* lehetőséget.
3. Állítsa be az *adatforrást* "JSON" értékre, és a *vizualizációt* a "Grid" értékre.
4. Adja hozzá a következő JSON-adatkészletet.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Lekérdezés futtatása.
6. A beállítások megnyitásához válassza az **oszlop beállításai** lehetőséget.
7. Válassza az "összes" lehetőséget az *oszlopok* közül, és válassza az "összetett sáv" lehetőséget az *oszlopok megjelenítéséhez*.
8. Adja meg a következő beállításokat az *összetett sáv beállításai* területen.

| Oszlop neve | Szín        |
|-------------|--------------|
| online      | Zöld        |
| helyreállítása  | Sárga       |
| Offline     | Piros (fényes) |

9. Címke hozzáadása:`["online"] of ["total"] are healthy`
10. Az oszlop beállításai online, offline és helyreállítás esetén beállítható, hogy a "rejtett" (nem kötelező) oszlop jelenik meg.
11. Válassza a *címkék* lehetőséget a felső oszlopban, és az összes oszlop frissítési címkéjét "adatbázis állapota" (opcionális) értékre.
12. **Alkalmazás** kiválasztása

Az összetett sáv beállításai az alábbi képernyőképhez hasonlóan jelennek meg:

![Képernyőkép az összetett sáv oszlopainak beállításairól a fent leírt beállításokkal.](./media/workbooks-composite-bar/composite-bar-settings.png)

A fenti beállításokkal rendelkező összetett sáv:

![Képernyőkép az összetett sávon.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Összetett sáv beállításai

Az oszlop neve és a megfelelő szín kiválasztásával megjelenítheti az adott színű oszlopot az összetett sáv részeként. Beszúrhatja, törölheti és áthelyezheti a sorokat.

### <a name="label"></a>Címke

Az összetett sáv címkéje az összetett sáv tetején jelenik meg. A statikus szöveg, oszlopok és paraméterek kombinációját is használhatja.  Ha a címke üres, az aktuális oszlopok értéke feliratként jelenik meg. Az előző példában, ha a címke mezőt feketén hagytuk, a teljes oszlop értéke megjelenik.

Az oszlopokat a következővel tekintheti meg: `["columnName"]` .

Tekintse meg a paramétereket a következővel: `{paramName}` .

Az oszlopnév és a paraméter neve is megkülönbözteti a kis-és nagybetűket. A hivatkozásokat úgy is megteheti, hogy az "Ez az elem legyen hivatkozásként" lehetőséget választja, majd felveszi a hivatkozás beállításait.

### <a name="aggregation"></a>Összesítés

Az összesítések a faszerkezetek és a csoportok számára hasznosak a vizualizációk esetében. A csoportosítási sorhoz tartozó oszlopra vonatkozó adatértékeket az adott oszlop összesítési készlete határozza meg. Az összetett sávokhoz háromféle összesítés alkalmazható: nincs, Sum és öröklés.

Csoportosítási beállítások hozzáadása:

1. Az oszlop beállításai területen lépjen arra az oszlopra, amelyhez beállításokat kíván hozzáadni.
2. A *Tree/Group By settings by* *Tree Type (fatípus*) területen válassza a **Group By** lehetőséget.
3. Válassza ki azt a mezőt, amelyet szeretne csoportosítani.

![Képernyőfelvétel a csoportosítási beállításokról.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Nincs

A none összesítés azt jelenti, hogy a csoportosítási sorok nem jelenítik meg az adott oszlopra vonatkozó eredményeket.

![Képernyőkép az összetett sáv és a nincs összesítés között.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Sum

Ha az Összesítés beállítása Sum (összeg), akkor a csoportosítási sorban lévő oszlop a megjelenítéséhez használt oszlopok összegét használva megjeleníti az összetett sávot. A címke a benne szereplő oszlopok összegét is használja.

Az alábbi példában az online, a kapcsolat nélküli és a helyreállítás az összes maximális összesítéssel értékre van állítva, a teljes oszlop összesítése pedig Sum.

![Képernyőkép az összetett sávon a Sum összesítéssel.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Öröklik

Ha az Összesítés beállítása örökölt, akkor a csoportosítási sorban lévő oszlop az összetett sávot jeleníti meg a felhasználók által a megjelenítéshez használt összesítések alapján. A címkében használt oszlopok a felhasználó által beállított összesítést is használják. Ha az aktuális oszlop-megjelenítő összetett sáv, és a címkében (például a "Total" kifejezéssel) van megjelölve, a Sum függvény az adott oszlop összesítését használja.

Az alábbi példában az online, a kapcsolat nélküli és a helyreállítás mind a maximális összesítéssel van beállítva, és a teljes oszlop összesítése örökli.

![A kompozit sáv képernyőképe az Összesítés öröklésével.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Rendezés

A rácsos vizualizációk esetében az oszlop sorainak rendezése az összetett sáv renderelő használatával az összetett sáv számítógépének dinamikus megjelenítéséhez használt oszlopok összege alapján működik. Az előző példákban a rendezéshez használt érték az adott sorhoz kapcsolódó online, helyreállítás és offline oszlopok összege.

## <a name="tiles-visualization"></a>Csempék vizualizációja

1. Válassza a **Hozzáadás** és a *lekérdezés hozzáadása* lehetőséget.
2. Módosítsa az adatforrást a JSON-be az [előző példából](#adding-composite-bar-renderer)származó adatok megadásával.
3. Vizualizációk módosítása *csempére*
4. Lekérdezés futtatása.
5. Válassza a **csempe beállításai** lehetőséget.
6. Válassza a *bal oldali* elemet a csempe mezőiben.
7. Adja meg az alábbi beállításokat a *mező beállításai* területen.
    1. Használja az oszlopot: "Server".
    2. Oszlop megjelenítő: "text".
8. Válassza az *alsó* elemet a csempe mezőiben.
9. Adja meg az alábbi beállításokat a *mező beállításai* területen.
    1. Oszlop használata: "Total".
    2. Oszlop megjelenítő: "összetett sáv".
    3. Adja meg a következő beállításokat az "összetett sáv beállításai" területen.

    | Oszlop neve | Szín        |
    |-------------|--------------|
    | online      | Zöld        |
    | helyreállítása  | Sárga       |
    | Offline     | Piros (fényes) |

    4. Címke hozzáadása: `["online"] of ["total"] are healthy` .
10. Kattintson az **Alkalmaz** gombra.

A csempék összetett sávjának beállításai:

![Képernyőkép az összetett sáv csempe beállításairól a fent leírt beállításokkal.](./media/workbooks-composite-bar/tiles-settings.png)

A fenti beállításokkal rendelkező csempék összetett sávjának nézete a következőképpen fog kinézni:

![Képernyőkép az összetett sávok csempéről.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Gráfok vizualizációja

Az alábbi utasításokat követve készíthet összetett görgetősávot a graphs vizualizációhoz (típus: kaptár-fürtök).

1. Válassza a **Hozzáadás** és a *lekérdezés hozzáadása* lehetőséget.
2. Módosítsa az adatforrást a JSON-be az [előző példából](#adding-composite-bar-renderer)származó adatok megadásával.
3. Vizualizációk módosítása *gráfokra*.
4. Lekérdezés futtatása.
5. Válassza a **Graph-beállítások** lehetőséget.
6. Válassza ki a *középső tartalom* elemet a csomópont formátumának beállításainál.
7. Adja meg az alábbi beállításokat a *mező beállításai* területen.
    1. Oszlop használata: "Total".
    2. Oszlop megjelenítő: "összetett sáv".
    3. Adja meg a következő beállításokat az *összetett sáv beállításai* területen.

    |Oszlop neve  |     Szín    |
    |-------------|--------------|
    | online      | Zöld        |
    | helyreállítása  | Sárga       |
    | Offline     | Piros (fényes) |

   4. Címke hozzáadása: `["online"] of ["total"] are healthy` .
9. Adja meg az alábbi beállításokat az *elrendezési beállítások* területen.
    1. Gráf típusa: **kaptár-fürtök**.
    2. Csomópont-azonosító: válassza a "kiszolgáló" lehetőséget.
    3. Csoportosítás mező szerint: "None".
    4. Csomópont mérete: 100.
    5. Hatszögek közötti árrés: 5.
    6. Színezés típusa típus: **nincs**.
1. Kattintson az **Alkalmaz** gombra.
    
A gráfok összetett sávjának beállításai:

![Képernyőkép az összetett sáv gráf beállításairól a fent leírt beállításokkal.](./media/workbooks-composite-bar/graphs-settings.png)

A fenti beállításokkal rendelkező gráfhoz tartozó összetett sáv nézet a következőképpen fog kinézni:

![Képernyőfelvétel a kaptár-fürtökkel rendelkező összetett oszlopdiagram-diagramokról.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Következő lépések

* Munkafüzetek [üzembe helyezése](../visualize/workbooks-automate.md) Azure Resource Managersal.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](../platform/workbooks-access-control.md) és megosztása.
