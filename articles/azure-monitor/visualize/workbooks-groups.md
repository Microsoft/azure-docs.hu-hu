---
title: Azure Monitor munkafüzetek csoportjai
description: Csoportok használata Azure Monitor munkafüzetekben.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: 2614117d153b1db50d4487bf4045a63af430f3ac
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615598"
---
# <a name="how-to-use-groups-in-workbooks"></a>Csoportok használata munkafüzeteknél

A munkafüzetben lévő cikkcsoportok lehetővé teszik, hogy logikailag csoportosítsa a lépéseket egy munkafüzetben.

A munkafüzetek csoportjai több dolog esetében hasznosak:

- Layout
  - Azokban az esetekben, amikor az elemeket függőlegesen szeretné szervezni, létrehozhat olyan elemek csoportjait, amelyek mindegyike összesítve lesz, és a csoport stílusát úgy állítja be, hogy az egyes elemek százalékos szélessége ne legyen a százalékban megadva.
- Láthatóság
  - Olyan helyzetekben, ahol sok elemet szeretne elrejteni/megjeleníteni, beállíthatja az elemek teljes csoportjának láthatóságát az egyes elemek láthatósági beállításainak megadása helyett. Ez hasznos lehet a lapokat használó sablonokban, mivel a lap tartalmaként egy csoportot is használhat, a teljes csoport pedig elrejthető/megjeleníthető a kijelölt lapon beállított paraméterek alapján.
- Teljesítmény
  - Olyan esetekben, ahol nagyon nagy méretű sablonnal vagy lapokkal rendelkezik, az egyes szakaszokat saját alsablonba alakíthatja, és csoportok használatával betöltheti a legfelső szintű sablonban lévő összes alsablont. Az alsablonok tartalma nem töltődik be vagy fut addig, amíg egy felhasználó el nem látja a csoportokat. További információ a [nagyméretű sablonok számos](#how-to-split-a-large-template-into-many-templates)sablonba való felosztásáról.

## <a name="using-groups"></a>Csoportok használata

Ha csoportot szeretne felvenni a munkafüzetbe, válassza a *Hozzáadás* , majd a *Csoport hozzáadása* lehetőséget.

![Válassza a Hozzáadás, majd a csoport lehetőséget.](./media/workbooks-groups/add-group.png)

Az alábbiakban egy, az olvasási módban található csoport képernyőképe látható két elemmel: egy szöveges elemet és egy lekérdezési elemet.  

![Csoport olvasási módban.](./media/workbooks-groups/groups-view.png)

A munkafüzet szerkesztésekor láthatja, hogy a két elem valójában egy csoport elemen belül van:

![Csoport szerkesztési módban. ](./media/workbooks-groups/groups-edit.png)

A fenti képernyőképen a csoport szerkesztési módban van, amely azt mutatja, hogy két elemet tartalmaz (a szaggatott területen belül). Minden lépés szerkeszthető vagy olvasási módban is lehet, egymástól függetlenül. A szöveges lépés például szerkesztési módban van, amíg a lekérdezési lépés olvasási módban van.

## <a name="scoping"></a>Tartalmazó

A csoportok jelenleg a munkafüzet új hatókörével kezelhetők. A csoportban létrehozott paraméterek csak a csoporton belül láthatók. Ez az egyesítéshez is igaz, csak a csoporton belüli vagy a szülő szinten lévő információk láthatók.

## <a name="group-types"></a>Csoportok típusai

A munkafüzet csoport elem lehetővé teszi elemek csoportjának hozzáadását egy munkafüzethez. A munkafüzet szerzőjeként meg kell adnia, hogy milyen típusú csoport lesz. Két típusú csoport létezik:

- Szerkeszthető
  - A munkafüzetben lévő csoport lehetővé teszi a csoportban lévő elemek tartalmának hozzáadását, eltávolítását vagy szerkesztését. Ez leggyakrabban elrendezési és láthatósági célokra szolgál.
- Sablonból
  - A munkafüzetben lévő csoport az azonosító alapján betöltődik egy másik sablon tartalmából. A sablon tartalma betöltődik, és a munkafüzetbe való egyesítése futásidőben történik. Szerkesztési módban a csoport tartalma nem módosítható, mert a sablon legközelebb betöltésével újra betöltődik.  

## <a name="load-types"></a>Betöltési típusok

A csoportok tartalma többféleképpen is betölthető. A munkafüzet szerzőjeként megadhatja, hogy mikor és hogyan történjen a csoport tartalmának betöltése.

### <a name="lazy-the-default"></a>Lusta (alapértelmezett)

A csoport csak akkor töltődik be, ha az elem látható. Ez lehetővé teszi, hogy a csoportok tabulátor-elemekként legyenek használhatók. Ha a lap soha nem lett kiválasztva, a csoport soha nem lesz látható, ezért a tartalom nincs betöltve.

A sablonból létrehozott csoportok esetében a sablon tartalma nem lesz beolvasva, és a csoport elemei csak a csoport láthatóvá válása után jönnek létre. A felhasználó a tartalom lekérése közben megtekintheti a teljes csoport folyamatjelzőit.

### <a name="explicit"></a>Explicit

Ebben a módban egy gomb jelenik meg, ahol a csoport lenne, és a rendszer nem kérdezi le vagy hozza létre a tartalmat, amíg a felhasználó explicit módon nem kattint a gombra a tartalom betöltéséhez. Ez olyan esetekben hasznos, amikor a tartalom költséges lehet a számításhoz vagy a ritkán használt adatokhoz. A szerző meghatározhatja a gombon megjelenítendő szöveget.

Az alábbiakban látható egy képernyőkép az explicit betöltési beállításokról, amelyekben a "betöltés több" gomb látható.

![Explicit betöltési beállítások](./media/workbooks-groups/groups-explicitly-loaded.png)

A csoport a munkafüzetbe való betöltése előtt:

![Explicit csoport a munkafüzetbe való betöltés előtt](./media/workbooks-groups/groups-explicitly-loaded-before.png)

A csoport a munkafüzetbe való betöltése után:

![Explicit csoport a munkafüzetbe való betöltés után](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Mindig

Ebben a módban a rendszer mindig betölti és létrehozza a csoport tartalmát, amint a munkafüzet betöltődik. Ez leggyakrabban akkor használatos, ha csak elrendezési célokra használ egy csoportot, ahol a tartalom mindig látható lesz.

## <a name="using-templates-inside-a-group"></a>Sablonok használata csoporton belül

Ha egy csoport egy sablonból való betöltésre van konfigurálva, a rendszer alapértelmezés szerint betölti a tartalmat, és csak akkor töltődik be, ha a csoport látható.

Amikor egy sablon betöltődik egy csoportba, a munkafüzet megkísérli egyesíteni a sablonban deklarált, a csoportban már meglévő paraméterekkel betöltött paramétereket. Az azonos nevű munkafüzetben már létező paraméterek egyesítése a betöltés alatt álló sablonból történik. Ha a paraméter egy lépésének összes paramétere egyesítve van, akkor a teljes paraméterek lépés eltűnik.

### <a name="example-1-all-parameters-have-identical-names"></a>1. példa: az összes paraméter azonos névvel rendelkezik

Vegyünk egy olyan sablont, amely a tetején két paraméterrel rendelkezik.

- `TimeRange` – a Time Range paraméter.
- `Filter` – egy szöveges paraméter.

![Paraméterek szerkesztése elem: "legfelső szintű paraméterek"](./media/workbooks-groups/groups-top-level-params.png)

Ezután egy csoportba betöltődik egy második sablon, amely a saját két paraméterrel és egy szöveges lépéssel rendelkezik, ahol a paraméterek neve azonos:

![A második sablonhoz tartozó paraméterérték szerkesztése](./media/workbooks-groups/groups-merged-away.png)

Ha a második sablon betöltődik a csoportba, az ismétlődő paraméterek ki lesznek egyesítve. Mivel az összes paraméter össze van egyesítve, a belső paraméterek lépés is egyesítve lesz, ami a csak a szöveges lépést tartalmazó csoportot eredményezi.

### <a name="example-2-one-parameter-has-an-identical-name"></a>2. példa: az egyik paraméternek azonos a neve

Vegyünk egy olyan csoport sablonját, amely a tetején két paraméterrel rendelkezik.

- `TimeRange` – a Time Range paraméter.
- `FilterB` -egy szöveges paraméter, vegye figyelembe, hogy nem `Filter` a legfelső sablonhoz hasonló.

![A Group elemek szerkesztése a paraméterek eredményével egyesítve](./media/workbooks-groups/groups-wont-merge-away.png)

Ha a csoport item's-sablonja be van töltve, a `TimeRange` paraméter ki lesz egyesítve a csoportból. Ezután a munkafüzetben a kezdeti paraméterek és a (z) `TimeRange` `Filter` , valamint a csoport paraméterének lépései csak a következők lesznek: `FilterB`

![a nem egyesítésre kerülő paraméterek eredménye](./media/workbooks-groups/groups-wont-merge-away-result.png)

Ha a betöltött sablon tartalmazott `TimeRange` és `Filter` (a helyett `FilterB` ), akkor az eredményül kapott munkafüzetnek szerepelnie kell egy paraméterekkel rendelkező lépésnek, és egy csoportnak, amely csak a szöveg lépését adja meg.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Nagyméretű sablon felosztása számos sablonba

A teljesítmény javítása érdekében hasznos lehet egy nagyméretű sablon több kisebb sablonba való bontása, amely valamilyen tartalmat lusta vagy igény szerint betölt a felhasználótól. Így a kezdeti terhelés gyorsabb lesz, mivel a legfelső szintű sablon sokkal kisebb lehet.

A sablonok részekre bontásakor a sablonnak több sablonra (alsablonokra) kell bontania, amelyek mindegyike külön működik. Ha tehát a legfelső szintű sablon egy olyan `TimeRange` paraméterrel rendelkezik, amelyet más lépések is használnak, akkor az alsablonnak rendelkeznie kell egy, a pontos névvel rendelkező paramétert meghatározó paraméterrel. Ez lehetővé teszi, hogy az alsablonok egymástól függetlenül működjenek, és lehetővé teszi, hogy a csoportok nagyobb sablonjain belül is betöltsenek.

Nagyobb sablon több alsablonba való bekapcsolásához:

1.  Hozzon létre egy új, üres csoportot a munkafüzet teteje közelében a megosztott paraméterek után. Az új csoport végül egy alsablon lesz.
2. Hozzon létre egy másolatot a megosztott paraméterek lépésről, majd a másolás *csoport* használatával helyezze át a másolatot az 1. lépésben létrehozott csoportba. Ez a paraméter lehetővé teszi, hogy az alsablon a külső sablontól függetlenül működjön, és a külső sablonba való betöltéskor egyesítve lesz.
    > [!NOTE]
    > Az alsablonhoz nem szükséges, hogy ezek a paraméterek ne legyenek egyesítve, ha soha nem szeretné, hogy az alsablonok önmagukban is megjelenjenek. Ez azonban nagyon nehéz lesz a szerkesztéshez vagy a hibakereséshez, ha később erre van szükség.

3. Helyezze át az 1. lépésben létrehozott csoportba a munkafüzetben szereplő összes olyan tételt, amelyet az alsablonban szeretne használni.
4. Ha a 3. lépésben áthelyezett lépések feltételes visibilities váltak, akkor ez lesz a külső csoport láthatósága (például a lapokon használt). Távolítsa el őket a csoporton belül található elemekről, és adja hozzá a láthatósági beállítást a csoporthoz. Ide kattintva elkerülheti a módosítások elvesztését, és/vagy exportálhatja és mentheti a JSON-tartalom másolatát.
5. Ha azt szeretné, hogy a csoport egy sablonból legyen betöltve, használhatja a csoport eszköztár *szerkesztése* gombját. Ez csak az adott csoport tartalmát nyitja meg munkafüzetként egy új ablakban. Ezt követően mentheti a megfelelő módon, és lezárhatja a munkafüzet nézetét (ne zárjuk be a böngészőt, csak ezt a nézetet, hogy visszalépjen a korábban szerkesztett munkafüzetbe).
6. Ezt követően módosíthatja a csoport lépéseit a sablonból való betöltéshez, majd a sablon azonosítója mezőt az 5. lépésben létrehozott munkafüzethez/sablonhoz is beállíthatja. A munkafüzetek azonosítóinak használatához a forrásnak megosztott munkafüzet-erőforrás-AZONOSÍTÓnak kell lennie. Nyomja meg a *Load (Betöltés* ) gombot, és az adott csoport tartalma betöltődik az adott alsablonból a külső munkafüzetbe való mentés helyett.

## <a name="next-steps"></a>Következő lépések
- [Munkafüzetek áttekintése](../platform/workbooks-overview.md)
- [JSONPath használata munkafüzetek használatával](workbooks-jsonpath.md)