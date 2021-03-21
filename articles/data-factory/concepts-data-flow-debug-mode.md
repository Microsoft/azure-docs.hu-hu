---
title: Adatfolyam-hibakeresési mód leképezése
description: Interaktív hibakeresési munkamenet elindítása az adatfolyamatok létrehozásakor
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: 0aa472aca40acbaf3f8c8a09469d08fe6b37187a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699759"
---
# <a name="mapping-data-flow-debug-mode"></a>Adatfolyam-hibakeresési mód leképezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Áttekintés

Azure Data Factory a leképezési adatfolyam hibakeresési módja lehetővé teszi, hogy interaktív módon figyelje az adatalakzatok átalakítóját az adatfolyamatok létrehozásakor és hibakeresése során. A hibakeresési munkamenet egyaránt használható az adatfolyam-tervezési munkamenetekben, valamint az adatfolyamatok hibakeresési folyamatának végrehajtása során. A hibakeresési mód bekapcsolásához használja a tervezési felület tetején található "adatfolyam-hibakeresés" gombot.

![Hibakeresési csúszka](media/data-flow/debugbutton.png "Hibakeresési csúszka")

A csúszka bekapcsolását követően meg kell adnia, hogy melyik Integration Runtime-konfigurációt kívánja használni. Ha a AutoResolveIntegrationRuntime van kiválasztva, akkor a fürt nyolc maggal rendelkező általános számítási feladatokkal, az alapértelmezett 60 perces élettartammal megpördült. Ha több tétlen csapatot szeretne engedélyezni a munkamenet időkorlátja előtt, akkor magasabb TTL-beállítást választhat. Az adatfolyam-integrációs modulokkal kapcsolatos további információkért lásd: [adatáramlási teljesítmény](concepts-data-flow-performance.md#ir).

![IR-kijelölés hibakeresése](media/data-flow/debug-new-1.png "IR-kijelölés hibakeresése")

Ha a hibakeresési mód be van kapcsolva, interaktív módon felépítheti az adatfolyamot egy aktív Spark-fürttel. A munkamenet akkor zárul le, ha a Azure Data Factoryban bekapcsolja a hibakeresést. Tisztában kell lennie azzal, hogy a hibakeresési munkamenet bekapcsolásának ideje alatt a Azure Databricks óradíjat számol fel.

A legtöbb esetben célszerű hibakeresési módban felépíteni az adatfolyamatokat, hogy ellenőrizni tudja az üzleti logikát, és megtekintheti az adatátalakításokat, mielőtt közzéteszi a munkáját a Azure Data Factoryban. Az adatfolyamatok folyamaton belüli teszteléséhez használja a folyamat panel "hibakeresés" gombját.

![Adatfolyam-hibakeresési munkamenetek megtekintése](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Minden olyan hibakeresési munkamenet, amelyet a felhasználó az ADF böngésző felhasználói felületéről indít, egy új munkamenet a saját Spark-fürttel. A fenti hibakeresési munkamenetek figyelés nézetével megtekintheti és kezelheti a gyári hibakeresési munkameneteket. Az egyes hibakeresési munkamenetek esetében minden órában díjat kell fizetni, beleértve a TTL-időt is.

## <a name="cluster-status"></a>Fürt állapota

A fürt állapotjelzője a tervezési felület tetején zöldre vált, amikor a fürt készen áll a hibakeresésre. Ha a fürt már meleg, akkor a zöld kijelző szinte azonnal megjelenik. Ha a fürt nem fut a hibakeresési mód megadásakor, akkor a Spark-fürt hideg rendszerindítást hajt végre. A kijelző addig forog, amíg a környezet nem áll készen az interaktív hibakeresésre.

Ha befejezte a hibakeresést, kapcsolja ki a hibakeresési kapcsolót úgy, hogy a Spark-fürt leálljon, és a továbbiakban nem lesz számlázva a hibakeresési tevékenységért.

## <a name="debug-settings"></a>Hibakeresési beállítások

A hibakeresési mód bekapcsolása után szerkesztheti, hogyan tekintheti át az adatfolyamok az adatfolyamatokat. A hibakeresési beállítások szerkesztéséhez kattintson a "hibakeresési beállítások" lehetőségre az adatfolyam-vászon eszköztáron. Itt kiválaszthatja az egyes forrás-átalakításokhoz használandó sor korlátját vagy forrását. Az ebben a beállításban szereplő sorokra vonatkozó korlátok csak az aktuális hibakeresési munkamenet esetében érvényesek. Azt is megteheti, hogy az átmeneti társított szolgáltatást az Azure szinapszis Analytics-forráshoz kívánja használni. 

![Hibakeresési beállítások](media/data-flow/debug-settings.png "Hibakeresési beállítások")

Ha az adatfolyamban vagy valamelyik hivatkozott adatkészletben paraméterek vannak megadva, megadhatja, hogy a hibakeresés során milyen értékeket kell használni a **Parameters (paraméterek** ) lapon.

Az itt található mintavételi beállítások segítségével megtekintheti a mintaadatok vagy az adatminta-táblázatok adatait, így nem kell módosítania a forrás adatkészleteket. Ha itt egy minta fájlt vagy táblázatot használ, megtarthatja az adatforgalomban megjelenő logikai és tulajdonság-beállításokat, miközben az adathalmazok alapján végez tesztelést.

![Hibakeresési beállítások paraméterei](media/data-flow/debug-settings2.png "Hibakeresési beállítások paraméterei")

Az ADF-adatfolyamatok hibakeresési módjához használt alapértelmezett IR egy 4 magos, egyetlen illesztőprogram-csomóponttal rendelkező, 4 magos önálló feldolgozó csomópont. Ez az adatfolyam-logika tesztelésekor is jól működik kisebb adatmennyiséggel. Ha a hibakeresési beállításokban kibontja a sorok korlátait az adatok előnézete során, vagy ha nagyobb számú mintavételezési sort állít be a forrásban a folyamat hibakeresése során, érdemes lehet nagyobb számítási környezetet beállítani egy új Azure Integration Runtime. Ezután újraindíthatja a hibakeresési munkamenetet a nagyobb számítási környezet használatával.

## <a name="data-preview"></a>Adatelőnézet

Ha a hibakeresés be van kapcsolva, az adatelőnézet lap az alsó panelen jelenik meg. A hibakeresési mód nélkül az adatfolyam csak az egyes átalakítások aktuális metaadatait jeleníti meg a vizsgálat lapon. Az adatelőnézet csak a hibakeresési beállításokban beállított sorok számát kérdezi le. Az adatelőnézet beolvasásához kattintson a **frissítés** gombra.

![Adatelőnézet](media/data-flow/datapreview.png "Adatelőnézet")

> [!NOTE]
> A fájlok forrása csak a megjelenő sorokra korlátozza a beolvasott sorokat. A nagyon nagy adatkészletek esetében ajánlott a fájl egy kis részének elvégzése, és a teszteléshez használni. Egy ideiglenes fájlt is kijelölhet a hibakeresési beállításokban minden olyan forrás esetében, amely egy fájl-adatkészlet típusú.

Ha hibakeresési módban fut az adatfolyamban, az adatai nem lesznek beírva a fogadó átalakítóba. A hibakeresési munkamenetek célja, hogy tesztelési hámként szolgáljon az átalakításokhoz. A rendszer nem igényel mosogatót a hibakeresés során, és figyelmen kívül hagyja az adatfolyamban. Ha szeretné tesztelni a fogadóban lévő adatok írását, hajtsa végre az adatfolyamatot egy Azure Data Factory folyamatból, és használja egy folyamat hibakeresési végrehajtását.

Az adatelőnézet az átalakított adatokról készült pillanatkép, amely a Spark-memóriában lévő adatkeretek által használt sorokra vonatkozó korlátozásokkal és adatok mintavételezésével kapcsolatos Ezért ebben az esetben a fogadó illesztőprogramok nem használhatók és nem lettek tesztelve.

### <a name="testing-join-conditions"></a>Csatlakozási feltételek tesztelése

Ha a mértékegység-tesztelés összekapcsolását, létezését vagy keresési átalakítását végzi, ügyeljen arra, hogy a teszthez egy kis mennyiségű ismert adat legyen használatban. A fenti hibakeresési beállítások lehetőséggel megadhat egy ideiglenes fájlt, amelyet a teszteléshez használhat. Erre azért van szükség, mert egy nagyméretű adatkészletből származó sorok korlátozása vagy mintavételezése esetén nem lehet megjósolni, hogy mely sorok és milyen kulcsok lesznek beolvasva a folyamatba a teszteléshez. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztési feltételek sikertelenek lehetnek.

### <a name="quick-actions"></a>Gyors műveletek

Ha az adatelőnézet megjelenik, gyorsan átalakíthatja a typecast, eltávolíthatja vagy végrehajthatja az oszlop módosítását. Kattintson az oszlop fejlécére, majd válassza ki az egyik lehetőséget az adatok előnézetének eszköztárán.

![Képernyőfelvétel: a Typecast, a módosítás, a statisztika és az Eltávolítás lehetőséggel megjeleníthető adatelőnézet eszköztár.](media/data-flow/quick-actions1.png "Gyors műveletek")

Miután kiválasztott egy módosítást, az adatelőnézet azonnal frissülni fog. Új átalakítás létrehozásához kattintson a jobb felső sarokban található **megerősítés** gombra.

![A képernyőképen a Confirm (megerősítés) gomb látható.](media/data-flow/quick-actions2.png "Gyors műveletek")

A **Typecast** és a **módosítás** egy származtatott oszlop átalakítását eredményezi, és az **Eltávolítás** egy kiválasztott átalakítást fog eredményezni.

![Képernyőfelvétel: a származtatott oszlop beállításait jeleníti meg.](media/data-flow/quick-actions3.png "Gyors műveletek")

> [!NOTE]
> Ha szerkeszti az adatfolyamatot, a gyors átalakítás hozzáadása előtt újra be kell olvasnia az adatelőnézetet.

### <a name="data-profiling"></a>Adatprofilkészítés

Ha kijelöl egy oszlopot az adatelőnézet lapon, és a **statisztikák** elemre kattint az adatelőnézet eszköztáron, az adatrács jobb oldalán megjelenik egy diagram, amely az egyes mezők részletes statisztikáit jeleníti meg. A Azure Data Factory az adatmintavétel alapján határozza meg, hogy milyen típusú diagramot kell megjeleníteni. A legfelső szintű mezők alapértelmezett értéke NULL/nem NULL értékű, míg a kategorikus és a numerikus adat, amely alacsony fokú, az adatértékek gyakoriságát ábrázoló sáv diagramokat jeleníti meg. A sztring mezők, a minimális/maximális érték numerikus mezőkben, a standard fejlesztés, a percentilis, a Count és az átlag mezőben is látható.

![Oszlop statisztikái](media/data-flow/stats.png "Oszlop statisztikái")

## <a name="next-steps"></a>Következő lépések

* Miután befejezte az adatfolyamok létrehozását és hibakeresését, [hajtsa végre a folyamatot egy folyamatból.](control-flow-execute-data-flow-activity.md)
* Amikor adatfolyamként teszteli a folyamatot, használja a folyamat [hibakeresési futtatásának futtatása beállítást.](iterative-development-debugging.md)
