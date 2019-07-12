---
title: Az Azure Data Factory adatokat folyamat séma eltéréseket leképezése
description: Rugalmas adatáramlás a séma eltéréseket az Azure Data Factory létrehozása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 6fd610afc0a21a97a8544b9e4b173f207f5fb50f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722889"
---
# <a name="mapping-data-flow-schema-drift"></a>Data Flow séma eltéréseket leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A séma eltéréseket fogalma a helyzet, ahol az adatforrások metaadatainak gyakran változhat. Mezők, oszlopok, típusok és az egyéb hozzáadhatja, eltávolítani vagy menet közben megváltozott. Anélkül, hogy a séma eltéréseket kezelését, az adatfolyam válik a felsőbb rétegbeli az adatforrás-módosításokkal sérülékeny. Amikor a bejövő oszlopok és a mezők módosításához tipikus ETL-mintákban sikertelen, mert a adathalmazokon szokták végezni, illetve forrás nevére.

Annak érdekében, hogy a séma eltéréseket ellen védelmet biztosító, fontos a funkciók egy adatfolyam-eszközben mint egy adat-mérnök, lehetővé teszi, hogy:

* Adatforrások, amelyek ezekre kapott válaszokon mezőnevek, adattípusok, értékek és méret megadása
* Változtatható mezők és értékek helyett adatmintákhoz szűrősablonokat együttműködő átalakító paraméterek megadása
* Adja meg a kifejezések, amelyek a bejövő mezők helyett mezők nevű-minták megismerése

## <a name="how-to-implement-schema-drift"></a>Séma eltéréseket megvalósítása

* Válassza a "Séma eltéréseket engedélyezése" az a forrás átalakítást

<img src="media/data-flow/schemadrift001.png" width="400">

* Ezt a beállítást választotta, amikor az összes bejövő mezők lévő minden adat a folyamat végrehajtását a forrásból kell olvasni, és a fogadó révén a teljes folyamatot lesznek átadva.

* Ellenőrizze, hogy a fogadó átalakításában szerepel a minden új mezők leképezése, így minden új mező első kivételezett felfelé és a cél érkezett az "Auto-térkép" használatával.

<img src="media/data-flow/automap.png" width="400">

* Minden fog működni, amikor új mezők jelennek meg, hogy egy egyszerű forgatókönyvet a forrás -> fogadó (más néven másolása) leképezés.

* A munkafolyamatban, amely kezeli a séma eltéréseket átalakítások hozzáadásához használhatja mintamegfeleltetés oszlopok neve, típusa és értéke megfelelő.

* Ha szeretne, amely együttműködik a "Schema eltéréseket" átalakítást hoz létre, kattintson a "Hozzáadása oszlop minta" származtatott oszlopot vagy összesítő átalakítást.

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Végezze el az architektúrával kapcsolatos döntés, fogadja el a folyamat során a séma eltéréseket az adatfolyama kell. Ha így tesz, a forrásokból származó sémaváltozások ellen védheti. Korai kötés az oszlopok és típusok azonban elvesznek az adatokat a folyamat során. Az Azure Data Factory kezeli séma eltéréseket folyamatok pozdní vazbu folyamatokat, így az átalakításokat hoz létre, amikor az oszlopnevek nem lesz elérhető a teljes folyamat során a séma nézetekben.

<img src="media/data-flow/taxidrift1.png" width="400">

A Taxi bemutató mintát adatfolyam van egy minta séma eltéréseket az alsó adatfolyam a TripFare forrással. Az összesített átalakítást figyelje meg, hogy az "oszlop minta" tervezési az összesítési mezők használjuk. Helyett elnevezési adott oszlopok, illetve az oszlopok alapján keres, feltételezzük, hogy az adatok módosíthatja, és ugyanabban a sorrendben futtatásai között nem szerepelhet.

Ebben a példában az Azure Data Factory adatfolyam séma eltéréseket kezelése hoztunk létre, és összesítést, amely megkeresi a "double" típus ismerete, hogy az adatok tartományi tartalmazza minden egyes út díjai oszlopait. Azt is végezze el az összesítő matematikai számítási között a forrás, függetlenül attól, ahol az oszlop hajtanak végre, és az oszlop elnevezési függetlenül minden dupla mező.

Az Azure Data Factory adatfolyam szintaxis $$ használ, amely képviseli minden egyező oszlop a tartományegyeztetési minta. Az oszlopnevek használatával összetett karakterlánc keresése és Reguláriskifejezés-függvényeket is egyeztetheti. Ebben az esetben fogjuk létrehozni minden oszlop "dupla" típusú egyezés alapján új összesített mező nevét, és fűzze hozzá a szöveg ```_total``` az egyes egyező nevei: 

```concat($$, '_total')```

A rendszer ciklikus és az értékek összeg minden egyező oszlopokat:

```round(sum ($$))```

Tesztelheti a felskálázás az Azure Data Factory adatfolyam minta "Taxi bemutató". Váltás a hibakeresési munkamenet a hibakeresési váltógomb az adatfolyam tervezőfelületére tetején, hogy az eredmények interaktívan tekintheti meg:

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Hozzáférés aktiválásához az új oszlopok

Oszlop-minták az új oszlopok generál, ha későbbi részében a flow adatátalakítások "byName" kifejezés funkció használatával érheti el a új oszlopokat.

## <a name="next-steps"></a>További lépések

Az a [Data Flow Kifejezésnyelveket](data-flow-expression-functions.md) oszlop minták és séma eltéréseket, beleértve a "byName" és "byPosition" a további lehetőségeket talál.
