---
title: Incidensek diagnosztizálása a metrikai tanácsadó használatával
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan diagnosztizálhatja az incidenseket a mérőszámok tanácsadójának használatával, és részletes nézeteket kaphat az adatokban mutatkozó rendellenességekről.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703422"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Útmutató: incidens diagnosztizálása metrikai tanácsadó használatával

A metrikák Advisor számos funkciót biztosít a diagnosztika számára, és részletes áttekintést nyújt az észlelt incidensekről, és alapvető okok elemzését teszi lehetővé. Ha egy mérőszámban észlelt rendellenességek egy csoportja, a metrikai tanácsadó egy hierarchiába csoportosítja a rendellenességeket, és elemzi őket.

> [!NOTE]
> Az aktuális metrikai tanácsadó támogatja az incidensek diagnosztizálását legalább egy dimenzióval rendelkező mérőszámokhoz, és a  *numerikus* típussal mérhető. A metrikának összesített dimenzió értékkel kell rendelkeznie, például az egyes dimenziók ÖSSZEGét, amely a diagnosztikai hierarchia összeállítására szolgál. A metrikák Advisor [**automatikus összesítő beállításokat**](onboard-your-data.md#automatic-roll-up-settings) biztosít az összesített értékek generálásához. 

A bal oldali navigációs ablakban kattintson az **incidens hub** elemre az adott metrika összes incidensének megtekintéséhez. A lap tetején kiválaszthatja a különböző mérőszámokat az észlelési konfigurációk megjelenítéséhez, valamint az észlelés eredményeinek megtekintéséhez és az időtartomány módosításához.

> [!TIP]
> Az **incidens központja** a következővel is elvégezhető:
> * Kattintson egy adatpontra a metrika vizualizációjában, és használja a megjelenő **visszajelzés hozzáadása** ablak alján található hivatkozásokat.
> * Kattintson valamelyik rendellenességre a metrika **incidensek** lapján. 

Az **Áttekintés** szakasz az észlelési eredményeket tartalmazza, beleértve a rendellenességek és a riasztások számát a kiválasztott időtartományon belül.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Incidens központja" lightbox="../media/diagnostics/incident-hub-overview.png":::

A rendszer a kiválasztott metrika és időtartományon belül észlelt incidenseket sorolja fel az **incidensek listájában**. Lehetőség van az incidensek szűrésére és rendezésére. Például súlyosság szerint. Kattintson az egyik incidensre az **incidens** oldalon a további diagnosztika érdekében.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Incidensek listája" lightbox="../media/diagnostics/incident-list.png":::

A **diagnosztikai** szakasz lehetővé teszi az incidensek részletes elemzését, valamint a kiváltó okok azonosítására szolgáló eszközöket.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Incidens diagnosztizálása" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Alapvető okok – tanácsok

Ha rendellenességet észlel egy mérőszámban, és incidenst okoz, a metrikai tanácsadó megkísérli elemezni az incidens kiváltó okát. Az **alapvető okokkal kapcsolatos tanácsok** automatikus javaslatokat nyújtanak az incidensek valószínű okaihoz. Ez a funkció csak akkor érhető el, ha a dimenzión belül összesített érték van. Ha a metrika nem tartalmaz dimenziót, akkor a kiváltó ok maga lesz. A kiváltó okok a jobb oldali panelen jelennek meg, és több oka is lehet. Ha nem található adat a táblában, az azt jelenti, hogy a dimenzió nem felel meg az elemzés végrehajtásához szükséges követelményeknek.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Alapvető okok – tanácsok":::


Ha a kiváltó ok mérőszáma adott dimenziókkal van megadva, a metrika további részleteinek megtekintéséhez kattintson a **metrika** elemre.

## <a name="incident-tree"></a>Incidens fája

A lehetséges kiváltó okok automatizált elemzése mellett a metrikák tanácsadója az **incidensek fájának** használatával támogatja a manuális kiváltó okok elemzését. Az incidensek oldalán kétféle incidens található: a **gyors diagnosztizálási** fa és az **interaktív fa**.

A gyors diagnosztikai fa egy aktuális incidens diagnosztizálására szolgál, és a legfelső szintű csomópont az aktuális incidensek legfelső szintű csomópontjára korlátozódik. A facsomópontok kibontásához és összecsukásához kattintson rá, és az adatsorozata a fa fölötti diagram aktuális incidens sorozatával együtt jelenik meg.

Az interaktív fa lehetővé teszi az aktuális incidensek, valamint a régebbi incidensek és az azokhoz kapcsolódó események diagnosztizálását. Az interaktív fa használatakor kattintson a jobb gombbal egy csomópontra egy művelet menü megnyitásához, ahol kiválaszthat egy dimenziót a legfelső szintű csomópontok részletezéséhez, valamint egy dimenziót, amely részletezi az egyes csomópontok részletezését. A felső dimenzió lista Mégse gombjára kattintva eltávolíthatja a befúrást ebből a dimenzióból. kattintson a bal gombbal egy csomópontra, és jelenítse meg a sorozatot a diagram aktuális incidens sorozatával együtt.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Incidens fája" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Anomália részletezése

Az incidensek adatainak megtekintésekor előfordulhat, hogy részletesebb információkat kell megadnia, például a különböző dimenziók és az időbélyegek esetében. Ha az adatai egy vagy több dimenzióval rendelkeznek, a részletezés funkcióval részletesebben is megtekintheti a nézetet. 

A részletezési funkció használatához kattintson az **incidens központ** **metrika fúrás** fülére. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Metrikai fúrás ":::

A **méretek** beállítás egy incidens dimenzióinak listája, és mindegyikhez kiválaszthat más elérhető dimenzió értékeket is. A dimenzió értékeinek módosítása után. Az **időbélyeg** -beállítás lehetővé teszi, hogy az aktuális incidenst különböző időpontokban tekintse meg.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Válassza ki a fúrási beállításokat, és válasszon egy dimenziót

Kétféle részletezési lehetőség létezik: **részletezés** és **horizontális összehasonlítás**.

> [!Note]
> 1. A lefúráshoz különböző dimenziós értékeket is megvizsgálhat, kivéve a jelenleg kiválasztott dimenziókat. 
> 2. A horizontális összehasonlításhoz az adatok különböző dimenzió értékekről való megismerése az összes dimenzió kivételével.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Dimenzió részletezése":::

### <a name="value-comparison-for-different-dimension-values"></a>Értékek összehasonlítása a különböző dimenzióértékeket

A részletezés lap második szakasza egy tábla, amely a különböző dimenzió értékeit hasonlítja össze. Magában foglalja az értéket, az alapértéket, a különbözeti értéket, a különbözeti értéket, valamint azt, hogy anomália-e.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Részletezés összehasonlítása" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Érték és várt érték-összehasonlítás a különböző dimenzió értékhez

A részletezés lap harmadik szakasza egy, az értékeket és a várt értékeket tartalmazó hisztogram a különböző dimenzió értékeknél. A hisztogram az érték és a várt érték közötti különbség alapján van rendezve. A nem várt értéket a legnagyobb hatással találja. A fenti képen például megtalálhatja, hogy a teljes érték kivételével a **US7** a legnagyobb mértékben járul hozzá a anomáliához.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Részletezési táblázat" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Nyers érték megjelenítése
A lefúrás lap utolsó része a nyers értékek egyik sora. Ha a diagramot megadja, a részletek megtekintéséhez nem kell megnyitnia a metrika lapot.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Diagram részletezése" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Hasonló anomáliák megtekintése az idősorozat-fürtszolgáltatás használatával

Az incidensek megtekintésekor a **hasonló idősorozat-fürtözés** lapon megtekintheti a hozzá társított különböző adatsorozatokat. Az adatsorozatok összefoglalása egy csoportban történik. A fenti képen látható, hogy legalább két adatsorozat-csoport létezik. Ez a funkció csak akkor érhető el, ha az alábbi követelmények teljesülnek:

1. A metrikák egy vagy több dimenzióval vagy dimenzió értékkel kell rendelkezniük.
2. Az egy mérőszámon belüli adatsorozatnak hasonló trendnek kell lennie.

Az elérhető dimenziók a lap tetején jelennek meg, és kiválaszthatja az adatsorozatot.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Adatsorozat-csoport":::

## <a name="compare-time-series"></a>Idősorozat összehasonlítása

Időnként előfordulhat, hogy ha egy adott idősorozatban anomáliát észlel, hasznos lehet összehasonlítani azt egyetlen vizualizáción belül több más sorozattal. Kattintson az **eszközök összehasonlítása** lapra, majd kattintson a kék **+ Hozzáadás** gombra. 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Összehasonlítható adatsorozat hozzáadása" lightbox="../media/diagnostics/add-series.png":::

Válasszon ki egy sorozatot az adatcsatornából. Ugyanezt a részletességet vagy egy másikat is kiválaszthatja. Válassza ki a célként megadott dimenziókat, és töltse be a sorozat trendjét, majd kattintson **az OK** gombra az előző sorozattal való összehasonlításhoz. A sorozat egy vizualizációban lesz egyesítve. Továbbra is hozzáadhat további adatsorozatokat az összehasonlításhoz, és további információhoz juthat. Kattintson az **eszközök összevetése** lap tetején található legördülő menüre, és hasonlítsa össze az idősorozat-adatmennyiséget egy adott időeltolású időszakon belül.  

> [!Warning]
> Az összehasonlításhoz az idősorozat-adatelemzéshez szükség lehet az adatpontok eltolására, így az adatai részletességét támogatni kell. Ha például az adatai hetente szerepelnek, és a nap **folyamán** összehasonlítjuk az összehasonlítást, akkor a rendszer nem fog eredményt kapni. Ebben a példában **a hónapot a havi összehasonlítás helyett** érdemes használni.

Az időeltolásos összehasonlítás kiválasztása után kiválaszthatja, hogy össze kívánja-e hasonlítani az adatértékeket, a különbözeti értékeket vagy a százalékos különbözetet.

> [!Note]
> * Az **adatérték** a nyers adatérték.
> * A **különbözet értéke** a nyers érték és az összehasonlítási érték közötti különbség.
> * A **százalékos különbözet értéke** a nyers érték és az összehasonlítási érték közötti különbség.

## <a name="related-incidents-across-metrics"></a>Kapcsolódó incidensek a metrikák között

Esetenként előfordulhat, hogy ellenőriznie kell a különböző metrikák incidenseit egy időben, vagy a kapcsolódó incidenseket más mérőszámokban. A kapcsolódó incidensek listáját a **több mérőszámok elemzése** szakaszban találja. 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="kapcsolódó incidensek a metrikák között":::

Ahhoz, hogy megjelenjenek a kapcsolódó incidensek az aktuális metrika esetében, hozzá kell adnia egy kapcsolatot a metrikák között. Kapcsolat hozzáadásához kattintson a **mérőszámok gráf beállításai** elemre. Csak azok a metrikák lehetnek társítva, amelyek azonos dimenzió névvel rendelkeznek. A következő paramétereket használja.

- Aktuális adatcsatorna & metrika: az aktuális incidens adatcsatornája és metrikája
- Irány: két metrika közötti kapcsolat iránya. (nem befolyásolja a kapcsolódó incidensek listáját most)
- Egy másik adatcsatorna & metrika: az aktuális metrikával való kapcsolódáshoz használandó adatcsatorna és metrika


## <a name="next-steps"></a>Következő lépések 

- [Anomáliadetektálás beállítása visszajelzés használatával](anomaly-feedback.md)
- [Metrikák konfigurálása és a konfiguráció észlelésének finomhangolása](configure-metrics.md)
