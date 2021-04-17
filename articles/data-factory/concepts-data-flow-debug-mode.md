---
title: Adatfolyam leképezése – Hibakeresési mód
description: Interaktív hibakeresési munkamenetet indít adatfolyamok kiépítésekor
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2021
ms.openlocfilehash: 681a3643c04472cc42c1f672f4c9433da30e3955
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565499"
---
# <a name="mapping-data-flow-debug-mode"></a>Adatfolyam-leképezési hibakeresési mód

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Áttekintés

Azure Data Factory adatfolyam hibakeresési módjának leképezése lehetővé teszi az adatalak-átalakítás interaktív figyelését az adatfolyamok buildelése és hibakeresése közben. A hibakeresési munkamenet a tervezési munkamenetekben Adatfolyam a folyamat hibakeresése során is használható. A hibakeresési mód bekapcsolához használja az **adatfolyam Adatfolyam** felső sávjában található Hibakeresés gombot, ha adatfolyam-tevékenységek vannak.

![Az 1. hibakeresési csúszka helyének képernyőképe](media/data-flow/debug-button.png)

![A 2. hibakeresési csúszka helyének képernyőképe](media/data-flow/debug-button-4.png)

A csúszka bekapcsolása után meg kell adnia, hogy melyik integrációskörnyezeti konfigurációt szeretné használni. Ha az AutoResolveIntegrationRuntime van kiválasztva, egy nyolc magos általános számítási kapacitású fürt lesz felfelé állítva, amely az alapértelmezett 60 perces üzemidőt használja. Ha szeretné, hogy több tétlen csapat is elteljön a munkamenet időkorrekta előtt, választhat egy magasabb TTL-beállítást. Az adatfolyam-integrációs runtime-okkal kapcsolatos további információkért lásd: [Adatfolyam-teljesítmény.](concepts-data-flow-performance.md#ir)

![Ir-kiválasztás hibakeresése](media/data-flow/debug-new-1.png "Ir-kiválasztás hibakeresése")

Ha a hibakeresési mód be van kapcsolva, interaktívan fogja felépíteni az adatfolyamot egy aktív Spark-fürt segítségével. A munkamenet akkor zárul le, ha kikapcsolja a hibakeresést a Azure Data Factory. Tisztában kell lennie a hibakeresési munkamenet Azure Databricks során felmerülő óránkénti díjakkal.

A legtöbb esetben jó gyakorlat az adatfolyamok hibakeresési módban való kiépítése, hogy érvényesítse az üzleti logikát, és megtekintse az adatátalakításokat, mielőtt közzétenné a Azure Data Factory. A folyamatpanel "Hibakeresés" gombjával tesztelje az adatfolyamot egy folyamatban.

![Adatfolyam hibakeresési munkamenetének megtekintése](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Minden olyan hibakeresési munkamenet, amely a felhasználó az ADF böngésző felhasználói felületről indul, új munkamenet a saját Spark-fürttel. A fenti hibakeresési munkamenetek figyelési nézetével megtekintheti és kezelheti a gyári hibakeresési munkameneteket. Az egyes hibakeresési munkamenetek minden órájáért fizetnie kell, beleértve az TTL-időt is.

## <a name="cluster-status&quot;></a>Fürt állapota

Ha a fürt készen áll a hibakeresésre, a tervezési felület tetején található fürt állapotjelzője zöldre változik. Ha a fürt már meleg, akkor a zöld jelző szinte azonnal megjelenik. Ha a fürt még nem futott, amikor hibakeresési módba lépett, a Spark-fürt hidegindítást hajt végre. A jelző addig pörget, amíg a környezet készen nem áll az interaktív hibakeresésre.

Ha végzett a hibakereséssel, kapcsolja ki a Hibakeresés kapcsolót, hogy a Spark-fürt leállhat, és a továbbiakban ne számlázható legyen a hibakeresési tevékenység.

## <a name=&quot;debug-settings&quot;></a>Hibakeresési beállítások

A hibakeresési mód bekapcsolása után szerkesztheti, hogy az adatfolyam hogyan tekintse meg az adatokat. A hibakeresési beállítások a vászon eszköztárának &quot;Hibakeresési beállítások&quot; Adatfolyam szerkeszthetők. Itt választhatja ki az egyes forrásátalakítások sorkorlátját vagy fájlforrását. A beállítás sorkorlátja csak az aktuális hibakeresési munkamenetre vonatkozik. Kiválaszthatja azt az átmeneti csatolt szolgáltatást is, amely egy adott forráshoz Azure Synapse Analytics használni. 

![Hibakeresési beállítások](media/data-flow/debug-settings.png &quot;Hibakeresési beállítások")

Ha rendelkezik paraméterekkel a Adatfolyam vagy bármelyik hivatkozott adatkészletében, a Paraméterek lapon megadhatja, hogy milyen értékeket használjon a **hibakeresés** során.

Az itt található mintavételezési beállításokkal mintafájlokra vagy mintaadattáblákra mutathat, így nem kell módosítania a forrásadatkészleteket. Ha itt mintafájlt vagy táblázatot használ, az adatok egy részkészletének tesztelése közben is megtarthatja az adatfolyam ugyanazon logikai és tulajdonságbeállítását.

![Hibakeresési beállítások paraméterei](media/data-flow/debug-settings2.png "Hibakeresési beállítások paraméterei")

Az ADF-adatfolyamok hibakeresési módjához használt alapértelmezett integrációs mód egy kis, 4 magos egy feldolgozó csomópont, 4 magos egyillesztős csomóponttal. Ez az adatfolyam logikájának tesztelése során kisebb adatmintákkal is jól működik. Ha kibontja a sorkorlátokat a hibakeresési beállításokban az adatelőnézet során, vagy nagyobb számú mintavételt tartalmazó sort ad meg a forrásban a folyamat hibakeresése során, érdemes lehet nagyobb számítási környezetet beállítani egy új Azure Integration Runtime. Ezután újraindíthatja a hibakeresési munkamenetet a nagyobb számítási környezet használatával.

## <a name="data-preview"></a>Adatelőnézet

A hibakeresés után az adatelőnézet lap kigyűjt az alsó panelen. Hibakeresési mód nélkül a Adatfolyam az egyes átalakítások aktuális metaadatait fogja mutatni a Vizsgálat lapon. Az adatelőnézet csak a hibakeresési beállításokban beállított sorok számát fogja lekérdezni. Kattintson **a Frissítés gombra** az adatelőnézet lekéréséhez.

![Adatelőnézet](media/data-flow/datapreview.png "Adatelőnézet")

> [!NOTE]
> A fájlforrások csak a látható sorokat korlátozzák, az éppen beolvasott sorokat nem. Nagyon nagy adatkészletek esetén javasoljuk, hogy a fájl egy kis részét használja a teszteléshez. A Hibakeresési beállítások menüben minden olyan forráshoz kiválaszthat egy ideiglenes fájlt, amely fájladatkészlet-típus.

Ha hibakeresési módban futtatja Adatfolyam, az adatok nem lesznek a fogadó-átalakításba írva. A hibakeresési munkamenetek az átalakítások tesztelési keretrendszereként szolgálnak. A fogadókra a hibakeresés során nincs szükség, és az adatfolyam figyelmen kívül hagyja őket. Ha tesztelni szeretné az adatok a fogadóban való írását, hajtsa végre a Adatfolyam egy Azure Data Factory-folyamatból, és használja a hibakeresési végrehajtást egy folyamatból.

Az Adatelőnézet egy pillanatkép az átalakított adatokról sorkorlátok és adat-mintavételezés használatával a Spark-memóriában lévő adatkeretek alapján. Ezért ebben a forgatókönyvben a fogadó illesztőprogramok nincsenek használva vagy tesztelve.

### <a name="testing-join-conditions"></a>Az illesztés feltételeinek tesztelése

Az egységtesztelés során az Illesztés, az Létezik vagy a Keresés átalakításkor győződjön meg arról, hogy a teszthez ismert adatok egy kis készletét használja. A fenti Hibakeresési beállítások lehetőséggel beállíthatja a teszteléshez használni kívánt ideiglenes fájlt. Erre azért van szükség, mert amikor egy nagyméretű adathalmaz sorait korlátozza vagy mintavételekor, nem tudja előrejelezni, hogy mely sorok és mely kulcsok lesznek beolvasva a folyamatba tesztelés céljából. Az eredmény nem determinisztikus, ami azt jelenti, hogy az illesztés feltételei meghiúsulhatnak.

### <a name="quick-actions"></a>Gyorsműveletek

Ha látja az adatelőnézetet, létrehozhat egy gyors átalakítást, amely típuscímzést, eltávolítást vagy egy oszlop módosítását teszi lehetővé. Kattintson az oszlop fejlécére, majd válassza ki valamelyik lehetőséget az adatelőnézet eszköztárán.

![Képernyőkép az adatelőnézet eszköztárról a következő beállításokkal: Typecast, Modify, Statistics és Remove.](media/data-flow/quick-actions1.png "Gyorsműveletek")

A módosítás kiválasztása után az adatok előnézete azonnal frissül. Új **átalakítás létrehozásához** kattintson a jobb felső sarokban található Confirm (Megerősítés) gombra.

![A Megerősítés gombot bemutató képernyőkép.](media/data-flow/quick-actions2.png "Gyorsműveletek")

**A Typecast** és **a Modify** egy Származtatott oszlop típusú átalakítást hoz létre, az **Eltávolítás** pedig a Select átalakítást.

![Képernyőkép a Származtatott oszlop beállításairól.](media/data-flow/quick-actions3.png "Gyorsműveletek")

> [!NOTE]
> Ha szerkeszti a Adatfolyam, a gyors átalakítás hozzáadása előtt újra be kell kérnie az adatok előnézetét.

### <a name="data-profiling"></a>Adatprofil-készítés

Ha kijelöl egy oszlopot az  adatelőnézeti lapon, és az adatelőnézet eszköztárában a Statisztika elemre kattint, megjelenik egy diagram az adatrács jobb távoli területén, az egyes mezők részletes statisztikáival. Azure Data Factory a megjelenítendő diagramtípus adat-mintavétele alapján fog döntést tenni. A nagy számosságú mezők alapértelmezett értéke NULL/NOT NULL, míg az alacsony számosságú kategorikus és numerikus adatok sávdiagramokat jelenítnek meg az adatértékek gyakoriságával. Emellett láthatja a sztringmezők maximális/hosszértékét, a numerikus mezők minimális/maximális értékeit, a standard fejlesztést, a percentiliseket, a darabszámokat és az átlagot.

![Oszlopstatisztika](media/data-flow/stats.png "Oszlopstatisztika")

## <a name="next-steps"></a>Következő lépések

* Miután elkészült az adatfolyam létrehozása és hibakeresése, hajtsa [végre azt egy folyamatból.](control-flow-execute-data-flow-activity.md)
* Amikor adatfolyammal teszteli a folyamatot, használja a [Folyamat hibakeresési futtatás-végrehajtási beállítását.](iterative-development-debugging.md)
