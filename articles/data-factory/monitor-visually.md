---
title: Azure-beli adat-előállítók vizuális monitorozására |} A Microsoft Docs
description: 'Útmutató: Azure adat-előállítók vizuális monitorozására'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: c2967de97e9cc3b6f59eb742ecbfef9acbe64d20
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019775"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure-beli adat-előállítók vizuális monitorozására
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.
Ebből a gyorsútmutatóból megtudhatja vizuálisan egy egyetlen sor kód megírása nélkül a data factory v2-folyamatok figyelése.
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="monitor-data-factory-pipelines"></a>Data Factory-folyamatok figyelése

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be a [az Azure portal](https://portal.azure.com/).
3. A létrehozott data factory panel az Azure Portalon keresse meg, és kattintson a "Monitor & Manage" csempére, és indítsa el a Data Factory visual figyeléshez.

## <a name="list-view-monitoring"></a>Listanézet figyelés

Egyszerű lista nézet felülettel folyamat és a tevékenységek futásának monitorozása. Minden futtatás a helyi böngésző időzónájában jelenik meg. Módosíthatja az időzónát, és minden dátum-idő mezők oszlopokhoz illesztés a választott időzónára.  

### <a name="monitoring-pipeline-runs"></a>Folyamatfuttatások figyelése
A listanézetben látható a Data Factory v2 verzióban futó összes folyamat. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. |
| Műveletek | Egyetlen művelettel tevékenységfuttatások megtekintéséhez. |
| Futtatás kezdete | Folyamat-Futtatás kezdő időpontja (hh/nn/éééé, óó: pp: mm AM és PM) |
| Időtartam | Futtatás időtartama (ÓÓ) |
| Kiváltó | Manuális eseményindító, az ütemezési eseményindító |
| status | Nem sikerült, a feladat végül sikerült folyamatban |
| Paraméterek | Folyamatfuttatás paraméterei (név, érték párok) |
| Hiba | Folyamat futásának (ha/any) hiba |
| Futtatási azonosító | A folyamat futásának Azonosítóját |

![Folyamatfuttatások monitorozása](media/monitor-visually/pipeline-runs.png)

### <a name="monitoring-activity-runs"></a>Tevékenység-végrehajtások figyelése
A listanézetben láthatók az egyes folyamatokhoz tartozó tevékenység-végrehajtások. Kattintson a **"Tevékenységfuttatások"** ikon mellett a **"Actions"** oszlopban megtekintheti a tevékenység fut minden folyamatfuttatáshoz. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Műveletnév | A folyamat belül a tevékenység neve. |
| Tevékenységtípus | A tevékenység típusa, például a másolás, HDInsightSpark, HDInsightHive, stb. |
| Futtatás kezdete | Tevékenység-végrehajtásonként kezdő dátum (hh/nn/éééé, óó: pp: mm AM és PM) |
| Időtartam | Futtatás időtartama (ÓÓ) |
| status | Nem sikerült, a feladat végül sikerült folyamatban |
| Input (Bemenet) | A tevékenység bemeneti leíró JSON-tömb |
| Kimenet | A tevékenység kimeneteiből leíró JSON-tömb |
| Hiba | Tevékenységfuttatási hiba (ha/any) |

![Tevékenységfuttatások monitorozása](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Kattintson a kell **"Frissítés"** ikon felül a frissítési folyamat- és tevékenységfuttatások listája. Az automatikus frissítés jelenleg nem támogatott.
>

![Frissítés](media/monitor-visually/refresh.png)

## <a name="monitoring-features"></a>Figyelési funkciók

### <a name="select-a-data-factory-to-monitor"></a>Válassza ki az adat-előállító figyelése
Vigye a mutatót a **adat-előállító** ikonra a bal felső sarokban. Kattintson a "" nyílikon megfigyeléséhez azure előfizetések és data factoryk listájának megtekintéséhez.

![Adat-előállító kiválasztása](media/monitor-visually/select-datafactory.png)

### <a name="rich-ordering-and-filtering"></a>Részletes rendezés és szűrés

Rendelés folyamatfuttatásokat a az asc/desc elindításához futtassa, és szűrő folyamatfuttatásokat következő oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. Beállítások gyors szűrők belefoglalása: elmúlt 24 órában","Elmúlt hét","az elmúlt 30 nap", vagy válasszon ki egy egyéni dátum és időpont. |
| Futtatás kezdete | Folyamat futásának kezdő dátum |
| Futtatási állapota | Állapot: sikeres, sikertelen, a folyamatban lévő szűrő futtathatja |

![Szűrés](media/monitor-visually/filter.png)

### <a name="addremove-columns-in-list-view"></a>A listanézetben oszlopok hozzáadása és eltávolítása
Kattintson a jobb gombbal a listanézet fejlécére, és válassza ki a nézetben megjeleníteni kívánt oszlopok

![Oszlopok](media/monitor-visually/columns.png)

### <a name="reorder-column-widths-in-list-view"></a>Listanézet oszlopszélességeinek átrendezése
Növelheti és csökkentheti a listanézet oszlopszélességeinek az egérmutatót az oszlop fejlécére

### <a name="user-properties"></a>Felhasználói tulajdonságok

Minden entitás, amely nyomon követheti, hogy minden tevékenység karakterekhez is előléptetni egy felhasználói tulajdonságot. Például, előléptetheti az **forrás** és **cél** felhasználó tulajdonságai, a folyamat másolási tevékenysége tulajdonságait. Lehetőség kiválasztásával **automatikus** létrehozni a **forrás** és **cél** egy másolási tevékenység felhasználói tulajdonságok.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Legfeljebb 5 folyamat tevékenység tulajdonságai csak előléptetni felhasználói tulajdonságokat.

Miután létrehozta a felhasználói tulajdonságok, ezt követően megfigyelheti őket a figyelési nézetekben listája. Ha a másolási tevékenység a forrás egy tábla nevét, a forrás-tábla neve egy oszlopot az a tevékenység futása lista nézetben követheti nyomon.

![Tevékenységfuttatások listája a felhasználói tulajdonságok nélkül](media/monitor-visually/monitor-user-properties-image2.png)

![Felhasználói tulajdonságok oszlopok hozzáadása a tevékenységek futtatások listája](media/monitor-visually/monitor-user-properties-image3.png)

![Tevékenységfuttatások listája a felhasználói tulajdonságok oszlopokkal](media/monitor-visually/monitor-user-properties-image4.png)

### <a name="guided-tours"></a>Az interaktív bemutatók
Kattintson a bal alsó a "információ ikonra", majd kattintson a "Bemutatók irányított" a lépésenkénti útmutatást a folyamat és a tevékenységek futásának monitorozása.

![Az interaktív bemutatók](media/monitor-visually/guided-tours.png)

### <a name="feedback"></a>Visszajelzés
Kattintson a visszajelzését a különböző funkciókról és esetleges problémákat, amelyek előfordulnak "Visszajelzés" ikonra.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Riasztások

A támogatott mérőszámok az adat-előállító riasztásokat is növelheti. Válassza ki a figyelő -> riasztások és a Data Factory monitor metrikák lapon a kezdéshez.

![](media/monitor-visually/alerts01.png)

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Kattintson a **Új riasztási szabály** egy új riasztást létrehozni.

    ![](media/monitor-visually/alerts02.png)

1.  Adja meg a szabály nevét, és válassza ki a riasztást **súlyossági**.

    ![](media/monitor-visually/alerts03.png)

1.  Válassza ki a riasztási feltételeket.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Az Alert logic konfigurálása. A kiválasztott metrika folyamatok és a hozzájuk tartozó tevékenységek riasztást hozhat létre. Kiválaszthat egy adott tevékenység típusa, a tevékenység nevét, a folyamat neve vagy a egy hiba típusa is.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurálása **e-mailek és SMS és leküldéses/Hangvétel** a riasztáshoz tartozó értesítések. Hozzon létre vagy válasszon egy meglévő **műveletcsoport** a riasztási értesítésekhez.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  A riasztási szabályt létrehozni.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>További lépések

Lásd: [figyelő programozással felügyelheti a folyamatokat és](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) figyelése és kezelése a folyamatok kapcsolatos cikket.
