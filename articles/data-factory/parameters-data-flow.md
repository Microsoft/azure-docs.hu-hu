---
title: Leképezési adatfolyamok paraméterezése
description: Ismerje meg, hogyan parametrizálja egy leképezési adatfolyamot a adatfeldolgozó-folyamatokból
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 564c7cf6e9627db08d543b964ce476e71bfb473d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93040753"
---
# <a name="parameterizing-mapping-data-flows"></a>Leképezési adatfolyamok paraméterezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

A Azure Data Factory adatforgalmának leképezése támogatja a paraméterek használatát. Definiáljon paramétereket az adatfolyam-definícióban, és használja őket a kifejezéseken belül. A paraméterek értékeit a hívó folyamat állítja be az adatfolyam végrehajtása tevékenységen keresztül. Az adatáramlási tevékenység kifejezésekben az értékek beállítására három lehetőség áll rendelkezésre:

* Dinamikus érték beállítása a folyamat-vezérlési folyamat kifejezésének nyelvén
* Dinamikus érték beállítása az adatfolyam-kifejezés nyelvén
* A kifejezés nyelvének használata statikus literál érték megadásához

Ezzel a képességgel általános célú, rugalmas és újrahasznosítható adatfolyamatokat végezhet. Ezekkel a paraméterekkel parametrizálja az adatáramlási beállításokat és kifejezéseket.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Paraméterek létrehozása egy leképezési adatfolyamban

Ha paramétereket szeretne adni az adatfolyamathoz, kattintson az adatáramlási vászon üres részére az általános tulajdonságok megtekintéséhez. A beállítások ablaktáblán egy **paraméter** nevű lap jelenik meg. Új paraméter létrehozásához válassza az **új** lehetőséget. Minden paraméterhez hozzá kell rendelnie egy nevet, ki kell választania egy típust, és opcionálisan meg kell adnia egy alapértelmezett értéket.

![Adatfolyam paramétereinek létrehozása](media/data-flow/create-params.png "Adatfolyam paramétereinek létrehozása")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Paraméterek használata egy leképezési adatfolyamban 

A paraméterek bármely adatfolyam-kifejezésben szerepelhetnek. A paraméterek a $-vel kezdődnek, és nem változtathatók meg. Az elérhető paraméterek listáját a **Parameters (paraméterek** ) lapon találja a Expression builden belül.

![Képernyőfelvétel: az elérhető paraméterek a paraméterek lapon láthatók.](media/data-flow/parameter-expression.png "Adatfolyam paraméterének kifejezése")

Az **új paraméter** kiválasztásával és a név és a típus megadásával gyorsan hozzáadhat további paramétereket.

![Képernyőfelvétel: a parameters (paraméterek) lapon található paraméterek a hozzáadott új paraméterekkel láthatók.](media/data-flow/new-parameter-expression.png "Adatfolyam paraméterének kifejezése")

## <a name="assign-parameter-values-from-a-pipeline"></a>Paraméterek értékének kiosztása egy folyamatból

Miután létrehozott egy adatfolyamatot a paraméterekkel, végrehajthatja azt egy folyamaton az adatfolyam végrehajtása tevékenységgel. Miután hozzáadta a tevékenységet a folyamat vásznon, megjelenik a tevékenység **Paraméterek** lapján elérhető adatfolyam-paraméterek.

A paraméterek értékének kiosztásakor a [folyamat kifejezésének nyelvét](control-flow-expression-language-functions.md) vagy az [adatfolyam kifejezésének nyelvét](data-flow-expression-functions.md) használhatja Spark típusok alapján. Az egyes leképezési adatfolyamok a folyamat és az adatfolyam-kifejezés paramétereinek bármely kombinációját tartalmazhatják.

![Képernyőfelvétel: a parameters (paraméterek) lap, a myparam értékének kiválasztott adatfolyam-kifejezéssel.](media/data-flow/parameter-assign.png "Adatfolyam-paraméter beállítása")

### <a name="pipeline-expression-parameters"></a>Folyamat kifejezésének paraméterei

A folyamat kifejezésének paraméterei lehetővé teszik a rendszerváltozók, függvények, folyamat paramétereinek és a többi feldolgozási tevékenységhez hasonló változók hivatkozását. Amikor a **folyamat kifejezésre** kattint, megnyílik egy oldalsó NAV, amely lehetővé teszi egy kifejezés megadását a Kifejezésszerkesztő használatával.

![A képernyőképen a Kifejezésszerkesztő panel látható.](media/data-flow/parameter-pipeline.png "Adatfolyam-paraméter beállítása")

Ha a rendszer hivatkozik rá, a rendszer kiértékeli a folyamat paramétereit, majd a rendszer az adatáramlás kifejezésének nyelvén használja az értéket. A folyamat típusú kifejezésnek nem kell megegyeznie az adatfolyam-paraméter típusával. 

#### <a name="string-literals-vs-expressions"></a>Karakterlánc-literálok vs kifejezések

Ha karakterlánc típusú adatcsatorna-kifejezési paramétert rendel hozzá, a rendszer alapértelmezés szerint hozzáadja az idézőjeleket, és az értéket literálként értékeli ki. Ha a paraméter értékét adatfolyam-kifejezésként szeretné olvasni, jelölje be a paraméter melletti kifejezés mezőt.

![A képernyőképen a paraméterhez kiválasztott adatfolyam-paraméterek panel kifejezés látható.](media/data-flow/string-parameter.png "Adatfolyam-paraméter beállítása")

Ha az adatáramlási paraméter `stringParam` értékkel rendelkező folyamat paraméterre hivatkozik `upper(column1)` . 

- Ha a kifejezés be van jelölve, `$stringParam` a kiértékeli az összes nagybetűs Column1 értékét.
- Ha a kifejezés nincs bejelölve (az alapértelmezett viselkedés),  `$stringParam` a kiértékelés a következőre `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Időbélyegek átadása

A folyamat kifejezésének nyelvén a rendszerváltozók, például a és függvények, például a `pipeline().TriggerTime` `utcNow()` visszatérési időbélyegek karakterláncként, "éééé-hh-nn \' T \' óó: PP: SS" formátumban. SSSSSSZ'. Ha timestamp típusú adatfolyam-paraméterekre kívánja konvertálni ezeket, használja a karakterlánc-interpolációt, hogy tartalmazza a kívánt időbélyeget egy `toTimestamp()` függvényben. Ha például át szeretné alakítani a folyamat indításának időpontját egy adatfolyam-paraméterbe, használhatja a következőt: `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Képernyőfelvétel: a parameters (paraméterek) lap, amelyen megadhatja az indítás időpontját.](media/data-flow/parameter-timestamp.png "Adatfolyam-paraméter beállítása")

> [!NOTE]
> Az adatfolyamatok legfeljebb 3 ezredmásodperces számjegyet támogatnak. A `left()` függvény a további számjegyek kivágására szolgál.

#### <a name="pipeline-parameter-example"></a>Példa a folyamat paraméterére

Tegyük fel, hogy van egy Integer paraméter `intParam` , amely egy string típusú folyamat-paraméterre hivatkozik `@pipeline.parameters.pipelineParam` . 

![A képernyőképen a stringParam és a intParam nevű paraméterek lap látható.](media/data-flow/parameter-pipeline-2.png "Adatfolyam-paraméter beállítása")

`@pipeline.parameters.pipelineParam` futásidőben van hozzárendelve érték `abs(1)` .

![Képernyőfelvétel: a parameters (paraméterek) lap, ahol a b s (1) érték van kiválasztva.](media/data-flow/parameter-pipeline-4.png "Adatfolyam-paraméter beállítása")

Ha egy `$intParam` kifejezés, például egy származtatott oszlop hivatkozik rá, a rendszer a visszaadott értéket `abs(1)` adja vissza `1` . 

![A képernyőképen az oszlopok érték látható.](media/data-flow/parameter-pipeline-3.png "Adatfolyam-paraméter beállítása")

### <a name="data-flow-expression-parameters"></a>Adatfolyam-kifejezés paraméterei

Válassza az adatfolyam- **kifejezés** lehetőséget, majd nyissa meg az adatfolyamat-kifejezés-szerkesztőt. A függvényeket, egyéb paramétereket és az adatfolyamatban definiált séma-oszlopokat is hivatkozhat. Ezt a kifejezést a rendszer a hivatkozásként fogja értékelni.

> [!NOTE]
> Ha érvénytelen kifejezést ad át, vagy olyan séma oszlopra hivatkozik, amely nem létezik az átalakításban, a paraméter értéke NULL lesz.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Oszlop nevének átadása paraméterként

Egy általános minta egy oszlop neve paraméterként való továbbítása. Ha az oszlop definiálva van az adatfolyam-sémában, hivatkozhat rá közvetlenül karakterlánc-kifejezésként. Ha az oszlop nincs definiálva a sémában, használja a `byName()` függvényt. Ne felejtse el átadni az oszlopot a megfelelő típusra egy öntési függvénnyel, például: `toString()` .

Ha például egy paraméteren alapuló karakterlánc-oszlopot szeretne leképezni `columnName` , hozzáadhat egy származtatott oszlopot, amely egyenlő a következővel: `toString(byName($columnName))` .

![Oszlop nevének átadása paraméterként](media/data-flow/parameterize-column-name.png "Oszlop nevének átadása paraméterként")

## <a name="next-steps"></a>Következő lépések
* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Vezérlési folyamat kifejezései](control-flow-expression-language-functions.md)
