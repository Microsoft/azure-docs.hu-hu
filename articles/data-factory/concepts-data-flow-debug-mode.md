---
title: Data Flow hibakeresési módban leképezése az Azure Data Factory
description: Egy interaktív hibakeresési munkamenetből, amikor áttérhettem folyamatok elindítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: a50778db5fd57202c17f05407045259371912586
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239198"
---
# <a name="mapping-data-flow-debug-mode"></a>Data Flow hibakeresési módban leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A hibakeresési mód, amely a tervezőfelületére tetején lévő adatok Flow hibakeresése gombbal bekapcsolható az Azure Data Factory leképezési adatfolyam rendelkezik. Amikor adatfolyam-gyűjteményre, a hibakeresési mód beállítása a kialakítása lehetővé teszi, hogy interaktív módon figyelje meg, hogy az adatok formázása átalakító hozhat létre és az adatfolyam-gyűjteményre hibakeresésekor. A hibakeresési munkamenet is használható, mind az adatfolyam-munkamenetekben tervezési, valamint folyamat hibakeresési adatfolyamok végrehajtása során.

![Hibakeresési gomb](media/data-flow/debugbutton.png "hibakeresési gomb")

## <a name="overview"></a>Áttekintés
Ha a hibakeresési módban, a Spark-fürt aktív adatfolyamait, interaktív módon fog létrehozni. A munkamenet bezárul kikapcsolása után hibakeresése az Azure Data Factoryban. A óránkénti díjat az időszakban, a hibakeresési munkamenet engedélyezve van az Azure Databricks felmerült tisztában kell lennie.

A legtöbb esetben tanácsos készítése, az adatok elkezdenek beérkezni hibakeresési módban, hogy az üzleti logika érvényesítéséhez, és a adatátalakítások megtekintése az Azure Data Factoryban a munkahelyi közzététele előtt. Az adatok folyamat alapjait az a folyamat teszteléséhez folyamat panelen is használjon a "Debug" gombra.

> [!NOTE]
> Bár a hibakeresési mód világos zöld, az adat-előállító eszköztáron, díjat számítunk fel az adatfolyam hibakeresési arányát 8 mag/óra általános számítási 60 perc a time-to-live 

## <a name="debug-mode-on"></a>Hibakeresési mód a
Amikor a hibakeresési módot, egy oldalpanel űrlapot, amely kérni fogja, hogy az interaktív Azure Databricks-fürt mutasson, és jelölje be a forrás mintavételi kéri. Az Azure Databricks egy interaktív fürtöt használ, és a forrás átalakítások válasszon mintavételi méret az egyes, vagy válasszon ki egy szövegfájlt a Tesztadatok használandó.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Az adatfolyam hibakeresési módban történő futtatásakor az adatok nem ír a fogadó átalakítása. A hibakeresési munkamenet célja egy teszt egyikükön > kihasználhatja az átalakításokat. Fogadóként hibakeresés során nem szükségesek, és a data folyamat figyelmen kívül hagyja. Ha az adatok írása tesztelni kívánt > a fogadó hajtsa végre az adatfolyam egy Azure Data Factory-folyamatot, és használja a hibakeresési végrehajtása egy folyamatból.

## <a name="debug-settings"></a>Hibakeresési beállításokhoz
Hibakeresési beállításokhoz az adatfolyam minden forrás az oldal panel fog megjelenni, és "adatforrás beállításaiban" a Tervező eszköztárán adatfolyam kiválasztásával is szerkeszthető. Kiválaszthatja a korlátok és/vagy itt a forrás-átalakítást az egyes használata forrásfájlt. A sor korlátokat az ezt a beállítást csak az aktuális hibakeresési munkamenet vonatkoznak. Sorok korlátozza az adatforrás-átalakítás be a forrás is használhatja a mintavételi beállítást.

## <a name="cluster-status"></a>Fürt állapota
A Tervező felület, amely zöldre, amikor a fürt készen áll a hibakeresési tetején lévő fürt állapotjelző van. Ha a fürt már meleg, majd a zöld jelző jelenik meg szinte azonnal. Ha a fürt nem volt már fut, ha a megadott hibakeresési módban, majd akkor várjon 5 – 7 perc, a fürt üzembe helyezése. A jelzőfény sárga lesz, amíg készen áll. Miután a fürt készen áll a hibakeresési adatfolyam, jelzőfény zöldre.

Amikor végzett a a hibakeresést, kikapcsolhatja a hibakeresési kapcsolót, hogy az Azure Databricks-fürt leállíthatja, és már nem díjköteles hibakeresési tevékenység.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Adatelőnézet
A hibakereséssel az adatok előzetes lapon fog világos felfelé az alsó panel. Hibakeresési módot adatfolyam jeleníti meg, csak az aktuális metaadatokat kicsinyítheti az átalakításokat mindegyike a vizsgálat lapon. Az adatok előzetes csak lekérdezi a korlátot, állított be a hibakeresési beállításokat tartalmazó sorok száma. Kattintson a "Adatlehívást" szükség lehet az adatok előzetes frissítéséhez.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Adatok profilok
Az adatok előzetes lapon kiválasztja egyes oszlopai fog előugró ablak a jobb szélső az adatrácsban az egyes mezők részletes statisztikáit, a diagram. Az Azure Data Factory fogja elérhetővé tenni a meghatározásához, hogy milyen típusú diagram az adat-mintavételezés alapján megjelenítéséhez. Magas – számosságú mezők alapértelmezett NULL / NOT NULL diagramok, amíg a kategorikus és numerikus adatok, amelyek alacsony Számosság adatok érték gyakoriságának megjelenítő sávdiagramok jeleníti meg. Látni fogja emellett maximális / karakterlánc-mezők len hossza minimális / maximális numerikus mezők, szabványos fejlesztési, percentilisei, számát és átlagos értékek. 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>További lépések

Ha már létrehozása befejeződött, és az adatfolyam-hibakeresés [hajtsa végre egy folyamat.](control-flow-execute-data-flow-activity.md)

Ha teszteli a folyamatot egy data flow-val, a folyamat használata [hibakeresési Futtatás végrehajtási beállítását.](iterative-development-debugging.md)
