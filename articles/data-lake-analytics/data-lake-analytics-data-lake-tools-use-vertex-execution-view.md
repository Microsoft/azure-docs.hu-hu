---
title: A Visual studióhoz készült Data Lake-eszközök csúcspont-végrehajtási nézete
description: Ez a cikk azt ismerteti, hogyan használható a csúcspont-végrehajtási nézet a vizsga Data Lake Analytics feladatokhoz.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215858"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>A Visual studióhoz készült Data Lake-eszközök csúcspont-végrehajtási nézetének használata
Megtudhatja, hogyan használhatja a csúcspont-végrehajtási nézetet Data Lake Analytics feladatok vizsgára.


## <a name="open-the-vertex-execution-view"></a>A csúcspont-végrehajtási nézet megnyitása
Nyisson meg egy U-SQL-feladatot a Visual studióhoz készült Data Lake-eszközökben. Kattintson a bal alsó sarokban található **csúcspont-végrehajtási nézet** elemre. Előfordulhat, hogy a rendszer először betölti a profilokat, és a hálózati kapcsolattól függően hosszabb időt is igénybe vehet.

![A Data Lake Analytics-eszközök csúcspont-végrehajtási nézetét bemutató képernyőkép](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>A vertex végrehajtási nézetének ismertetése
A csúcspont-végrehajtás nézet három részből áll:

![Képernyőkép, amely a csúcspont-végrehajtási nézetet jeleníti meg a "Vertex választó" és a közép-felső és Közép-alsó ablaktáblák kiemelésével.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

A bal oldali **csúcspont-választó** lehetővé teszi a csúcspontok kiválasztását a szolgáltatások (például az első 10 adatolvasás vagy a fázis szerinti választás) alapján. Az egyik leggyakrabban használt szűrő a **kritikus elérési úton található csúcspontokat** látja. A **kritikus elérési út** a U-SQL-feladatok csúcspontjának leghosszabb lánca. A kritikus elérési út megértése hasznos lehet a feladatok optimalizálásához, ha ellenőrzi, hogy a csúcspont a leghosszabb ideig tart-e.
  
![Képernyőfelvétel: a csúcspont-végrehajtási nézet felső középső ablaktáblája, amely megjeleníti az összes csúcspont futási állapotát.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

A felső középső ablaktáblán az **összes csúcspont futási állapota** látható.
  
![Képernyőkép, amely megjeleníti a csúcspont-végrehajtási nézet alsó-középső ablaktábláját, amely az egyes csúcspontokkal kapcsolatos információkat jeleníti meg.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Az alsó középső ablaktábla az egyes csúcspontokkal kapcsolatos információkat jeleníti meg:
* Folyamat neve: a csúcspont-példány neve. A StageName különböző részeiből áll | VertexName | VertexRunInstance. Például a (SV7_Split [62]. v1 csúcspont a második futó példánynál (. v1, indextől kezdődően) a (SV7_Split) fázisban, a 62-as számú csúcson áll.
* Összes olvasott/írt adatmennyiség: a csúcspont által beolvasott/írt adatmennyiség.
* Állapot/kilépés állapota: a csúcspont befejezésének végső állapota.
* Kilépési kód/hiba típusa: Ez a hiba, ha a csúcspont meghiúsult.
* Létrehozás oka: a csúcspont létrehozásának okai.
* Erőforrás-késés/folyamat késése/PN-várólista késése: a csúcspontnak az erőforrások megvárnia, az adatfeldolgozás és a várólistán való tartózkodáshoz szükséges idő.
* Folyamat/létrehozó GUID: GUID az aktuálisan futó csúcsponthoz vagy annak létrehozója számára.
* Verzió: a futó csúcspont N-edik példánya (a rendszer több okból is ütemezhet egy csúcspont új példányait, például feladatátvétel, számítási redundancia stb.).
* A verzió létrehozásának ideje.
* Folyamat létrehozása indítási idő/folyamat várólistán lévő idő/folyamat indítási idő/folyamat befejezése: Ha a csúcspont-folyamat elindítja a létrehozást, Amikor a csúcspont-folyamat elindul a várólistára; Ha az adott csúcspont-folyamat elindul; Ha az adott csúcspont befejeződik.

## <a name="next-steps"></a>Következő lépések
* A diagnosztikai információk naplózása: [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](data-lake-analytics-diagnostic-logs.md).
* Egy összetettebb lekérdezés megtekintéséhez lásd: [Webhelynaplók elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md).
* A feladat részleteinek megtekintéséhez lásd: [Az Azure-beli és az Azure-beli adat-Lake Analytics-feladatok](data-lake-analytics-data-lake-tools-view-jobs.md) feladatának használata
