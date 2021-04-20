---
title: Leképezési adatfolyamok paraméterezése
description: Megtudhatja, hogyan paraméteresít egy leképezési adatfolyamot az adat-előállító folyamatokból
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725709"
---
# <a name="parameterizing-mapping-data-flows"></a>Leképezési adatfolyamok paraméterezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Az adatfolyamok leképezése Azure Data Factory és Azure Synapse Analytics támogatja a paraméterek használatát. Definiálja a paramétereket az adatfolyam definíciójában, és használja őket a kifejezésekben. A paraméterértékeket a hívó folyamat a Végrehajtási művelet Adatfolyam állíthatja be. Az adatfolyam-tevékenységkifejezések értékeinek beállítására három lehetőség áll rendelkezésre:

* Dinamikus érték beállítása a folyamatvezérlési folyamat kifejezési nyelvével
* Dinamikus érték beállítása az adatfolyam-kifejezés nyelvének használatával
* Statikus konstansérték beállítása bármelyik kifejezésnyelv használatával

Ezzel a képességgel általános célú, rugalmas és újrahasználható adatfolyamokat használhat. Ezekkel a paraméterekkel paraméterzheti az adatfolyam beállításait és kifejezéseket.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Paraméterek létrehozása leképezési adatfolyamban

Ha paramétereket szeretne hozzáadni az adatfolyamhoz, kattintson az adatfolyamvászon üres részéhez az általános tulajdonságokat. A beállítások panelen egy Paraméter nevű lap **látható.** Új **paraméter létrehozásához** válassza az Új lehetőséget. Minden paraméterhez hozzá kell rendelnie egy nevet, ki kell választania egy típust, és opcionálisan be kell állítania egy alapértelmezett értéket.

![Új Adatfolyam létrehozása](media/data-flow/create-params.png "Új Adatfolyam létrehozása")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Paraméterek használata leképezési adatfolyamban 

A paraméterek bármely adatfolyam-kifejezésben hivatkozhatóak. A paraméterek $ értékekkel kezdődnek, és nem módosíthatók. Az elérhető paraméterek listáját a Kifejezésszerkesztő Paraméterek lapján **találja.**

![A Paraméterek lapon elérhető paramétereket bemutató képernyőkép.](media/data-flow/parameter-expression.png "Adatfolyam-paraméter kifejezése")

Gyorsan hozzáadhat további paramétereket, ha kiválasztja az **Új** paramétert, és megadja a nevet és a típust.

![Képernyőkép a Paraméterek lapon látható paraméterekről, hozzáadott új paraméterekkel.](media/data-flow/new-parameter-expression.png "Adatfolyam-paraméter kifejezése")

## <a name="assign-parameter-values-from-a-pipeline"></a>Paraméterértékek hozzárendelése folyamatból

Miután létrehozott egy paraméterekkel egy adatfolyamot, végrehajthatja azt egy folyamatból az Execute Adatfolyam Activity (Végrehajtás Adatfolyam tevékenységgel. Miután hozzáadta a tevékenységet a folyamatvászonhoz, a tevékenység Paraméterek lapján jelennek meg az elérhető **adatfolyam-paraméterek.**

Paraméterértékek hozzárendelésekor a folyamatkifejezés nyelvét [](data-flow-expression-functions.md) vagy a spark-típusokon alapuló adatfolyam-kifejezésnyelvet használhatja. [](control-flow-expression-language-functions.md) Minden leképezési adatfolyam folyamat- és adatfolyam-kifejezési paraméterek bármilyen kombinációjával rendelkezik.

![Képernyőkép a Paraméterek lapról, Adatfolyam kifejezés van kiválasztva a myparam értékeként.](media/data-flow/parameter-assign.png "Paraméter Adatfolyam beállítása")

### <a name="pipeline-expression-parameters"></a>Folyamatkifejezés paraméterei

A folyamatkifejezés-paraméterek lehetővé teszik, hogy más folyamattevékenységekhez hasonló rendszerváltozókra, függvényre, folyamatparaméterre és változókra hivatkozhat. Ha a **Pipeline expression (Folyamatkifejezés) elemre kattint,** megnyílik egy oldal navigációs ablak, amely lehetővé teszi egy kifejezés beíratését a kifejezésszerkesztővel.

![Képernyőkép a kifejezésszerkesztő panelről.](media/data-flow/parameter-pipeline.png "Paraméter Adatfolyam beállítása")

A rendszer a hivatkozáskor kiértékeli a folyamatparamétereket, majd az értéküket használja az adatfolyam-kifejezés nyelvében. A folyamatkifejezés-típusnak nem kell megegyezni az adatfolyam-paraméter típussal. 

#### <a name="string-literals-vs-expressions"></a>Sztring-literálok és kifejezések

Sztring típusú folyamatkifejezés-paraméter hozzárendelésekor a rendszer alapértelmezés szerint idézőjeleket ad hozzá, és az értéket konstansként értékeli ki. A paraméter értékének adatfolyam-kifejezésként való beolvassa a kifejezés jelölőnégyzetét a paraméter mellett.

![Képernyőkép egy paraméterhez kiválasztott Adatfolyam-paraméterek panelről.](media/data-flow/string-parameter.png "Paraméter Adatfolyam beállítása")

Ha az adatfolyam-paraméter `stringParam` egy értékű folyamatparaméterre `upper(column1)` hivatkozik. 

- Ha a kifejezés be van jelölve, a függvény az 1. oszlop összes `$stringParam` nagybetűs értékét értékeli ki.
- Ha a kifejezés nincs bejelölve (alapértelmezett viselkedés),  `$stringParam` a kiértékelése: `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Időbélyegek átadása

A folyamatkifejezések nyelvén az olyan rendszerváltozók, mint a és a függvény, az időbélyegeket sztringekként, `pipeline().TriggerTime` `utcNow()` "yyyy-MM-dd \' T \' HH:mm:ss" formátumban adja vissza. SSSSSSZ" (SSSSSZ) Ha ezeket időbélyeg típusú adatfolyam-paraméterekké szeretné alakítani, használjon sztring-interpolációt a kívánt időbélyeg egy függvénybe való `toTimestamp()` beszámítására. Ha például a folyamat eseményindító-idejét adatfolyam-paraméterre konvertálja, használhatja a következőt: `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Képernyőkép a Paraméterek lapról, ahol megadhatja az eseményindító idejét.](media/data-flow/parameter-timestamp.png "Paraméter Adatfolyam beállítása")

> [!NOTE]
> Az adatfolyamok legfeljebb 3 ezredmásodpercnél több számjegyet támogatnak. A `left()` függvény további számjegyek levágítását használja.

#### <a name="pipeline-parameter-example"></a>Példa folyamatparaméterre

Például egy sztring típusú folyamatparaméterre hivatkozó egész szám típusú `intParam` `@pipeline.parameters.pipelineParam` paraméterrel. 

![Képernyőkép a Parameters (Paraméterek) lapról stringParam és intParam nevű paraméterekkel.](media/data-flow/parameter-pipeline-2.png "Paraméter Adatfolyam beállítása")

`@pipeline.parameters.pipelineParam` A a értéket `abs(1)` rendeli hozzá futásidőben.

![Képernyőkép a Paraméterek lapról a kiválasztott b s (1) értékkel.](media/data-flow/parameter-pipeline-4.png "Paraméter Adatfolyam beállítása")

Ha egy kifejezésben, például egy származtatott oszlopban hivatkozik `$intParam` rá, a visszaadott értéket `abs(1)` adja `1` vissza. 

![Az oszlopok értékét bemutató képernyőkép.](media/data-flow/parameter-pipeline-3.png "Paraméter Adatfolyam beállítása")

### <a name="data-flow-expression-parameters"></a>Adatfolyam-kifejezés paraméterei

Az **Adatfolyam-kifejezés kiválasztása** megnyitja az adatfolyam-kifejezésszerkesztőt. Az adatfolyamban függvényre, más paraméterekre és bármely meghatározott sémaoszlopra hivatkozhat. Ezt a kifejezést a rendszer a hivatkozáskor ennek alapján értékeli ki.

> [!NOTE]
> Ha érvénytelen kifejezést ad meg, vagy olyan sémaoszlopra hivatkozik, amely nem létezik az átalakításban, a paraméter null értéket ad vissza.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Oszlopnév átadása paraméterként

Gyakori minta az oszlopnév paraméterértékként való megadása. Ha az oszlop az adatfolyam-sémában van definiálva, közvetlenül hivatkozhat rá sztringkifejezésként. Ha az oszlop nincs definiálva a sémában, használja a `byName()` függvényt. Ne felejtse el az oszlopot a megfelelő típusra átgépelni egy olyan típussal, mint a `toString()` .

Ha például egy paraméter alapján szeretne leképezni egy sztringoszlopot, hozzáadhat egy értékű származtatott `columnName` `toString(byName($columnName))` oszlopátalakítást.

![Oszlopnév átadása paraméterként](media/data-flow/parameterize-column-name.png "Oszlopnév átadása paraméterként")

## <a name="next-steps"></a>Következő lépések
* [Adatfolyam-tevékenység végrehajtása](control-flow-execute-data-flow-activity.md)
* [Folyamkifejezések szabályozása](control-flow-expression-language-functions.md)
