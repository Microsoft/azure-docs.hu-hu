---
title: Séma-eltolódás a leképezési adatfolyamban
description: Rugalmas adatfolyamatok létrehozása a Azure Data Factory a séma drift
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027482"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Séma-eltolódás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A séma drift az a helyzet, amikor a források gyakran változtatják meg a metaadatokat. A mezőket, oszlopokat és típusokat a rendszer menet közben is hozzáadhatja, eltávolíthatja vagy módosíthatja. A séma eltolódása nélkül az adatfolyam a felsőbb rétegbeli adatforrások változásaihoz lesz kitéve. A tipikus ETL-minták meghiúsulnak, amikor a bejövő oszlopok és mezők megváltoznak, mivel ezek a forrás nevükhöz vannak kötve.

A séma-eltolódás elleni védelemhez fontos, hogy a létesítmények egy adatáramlási eszközben legyenek, amely lehetővé teszi, hogy adatmérnökként:

* Megváltoztathatatlan mezőneveket, adattípusokat, értékeket és méreteket tartalmazó források megadása
* Olyan átalakítási paramétereket határozhat meg, amelyek rögzített mezők és értékek helyett adatmintákkal működhetnek.
* Megadhatja azokat a kifejezéseket, amelyek megértik a bejövő mezőkkel egyező mintákat, és nem a nevesített mezőket használja

A Azure Data Factory natív módon támogatja a végrehajtásról a végrehajtásra váltást igénylő rugalmas sémákat, így általános Adatátalakítási logikát építhet ki az adatfolyamatok újrafordításának szükségessége nélkül.

Az adatfolyamatban építészeti döntés szükséges ahhoz, hogy elfogadja a séma-eltolódást a folyamat során. Ha ezt teszi, a forrásokból is védelmet biztosíthat a séma változásai ellen. Az oszlopok és típusok korai kötését azonban elveszítheti az adatfolyamban. Azure Data Factory a séma-drift folyamatokat a késői kötésű folyamatokként kezeli, így az átalakítások létrehozásakor az átadott oszlopnevek nem lesznek elérhetők a séma nézeteiben a folyamat során.

Ez a videó bevezetést nyújt néhány olyan összetett megoldásról, amelyet egyszerűen létrehozhat az ADF-ben az adatfolyam sémájának drift funkciójával. Ebben a példában újrafelhasználható mintákat hozunk létre rugalmas adatbázis-sémák alapján:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Séma-eltolódás a forrásban

A forrás definíciójában lévő adatfolyamatba érkező oszlopok "sodródott" értékűek, ha nem jelennek meg a forrás kivetítésében. A forrás-kivetítést a forrás-átalakítás leképezés lapján tekintheti meg. Ha kijelöl egy adatkészletet a forráshoz, az ADF automatikusan átveszi a sémát az adatkészletből, és létrehoz egy leképezést az adatkészlet sémájának definíciójában.

A forrás-átalakításban a séma-eltolódás olyan oszlopok olvasására van meghatározva, amelyek nem határozzák meg az adatkészlet sémáját. A séma eltolódásának engedélyezéséhez jelölje be a **séma eltolódásának engedélyezése** a forrás-átalakításban lehetőséget.

![Séma-drift forrás](media/data-flow/schemadrift001.png "Séma-drift forrás")

Ha engedélyezve van a séma-eltolódás, a rendszer az összes bejövő mezőt beolvassa a forrásból a végrehajtás során, és a teljes folyamatot átadja a fogadónak. Alapértelmezés szerint az összes újonnan észlelt oszlop (más néven *lebegő oszlop*) sztring adattípusként érkezik. Ha azt szeretné, hogy az adatfolyam automatikusan kikövetkeztetse az oszlopok adattípusait, ellenőrizze, hogy a **kikövetkeztetett oszlop típusa** szerepel-e a forrás beállításai között.

## <a name="schema-drift-in-sink"></a>Séma-eltolódás a fogadóban

A fogadó átalakításban a séma-eltolódás akkor van, ha további oszlopokat ír a fogadó adatsémában meghatározottak szerint. A séma eltolódásának engedélyezéséhez jelölje be a **séma eltolódásának engedélyezése** a fogadó transzformációjában lehetőséget.

![Séma drift fogadó](media/data-flow/schemadrift002.png "Séma drift fogadó")

Ha engedélyezve van a séma-eltolódás, győződjön meg arról, hogy a leképezés lapon be van kapcsolva az **automatikus leképezés** csúszkája. Ezzel a csúszkával az összes bejövő oszlop a célhelyre íródik. Ellenkező esetben szabály-alapú hozzárendelést kell használnia a lebegő oszlopok írásához.

![Fogadó automatikus leképezése](media/data-flow/automap.png "Fogadó automatikus leképezése")

## <a name="transforming-drifted-columns"></a>Sodródó oszlopok átalakítása

Ha az adatfolyamnak vannak sodródott oszlopai, az átalakításokban a következő módszerekkel érheti el őket:

* A `byPosition` és a `byName` kifejezések használatával explicit módon hivatkozhat egy oszlopra név vagy pozíció száma alapján.
* Oszlop mintázatának hozzáadása egy származtatott oszlophoz vagy összesítő átalakításhoz a név, a stream, a pozíció, a forrás vagy a típus tetszőleges kombinációjának megfelelően
* Szabályon alapuló leképezés hozzáadása egy Select vagy mosogató átalakításban, hogy az oszlopok aliasai megegyezzenek a mintázattal

Az oszlopok mintáinak megvalósításával kapcsolatos további információkért lásd: [az adatforgalom leképezése az oszlopok mintái között](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Elsodródott oszlopok gyors műveletének leképezése

A lebegő oszlopok explicit módon történő hivatkozásához gyorsan létrehozhat leképezéseket ezekhez az oszlopokhoz az adatelőnézet gyors művelete révén. Ha a [hibakeresési mód](concepts-data-flow-debug-mode.md) be van kapcsolva, nyissa meg az adatelőnézet lapot, és kattintson a **frissítés** gombra az adatelőnézet beolvasásához. Ha az adatelőállító észleli, hogy az elsodródott oszlopok léteznek, kattintson az **Elsodródott térképre** , és készítsen egy származtatott oszlopot, amely lehetővé teszi, hogy az összes áthelyezett oszlopra hivatkozzon a séma nézeteiben.

![Képernyőfelvétel: az adatelőnézet lap, amelyen a Térkép látható.](media/data-flow/mapdrifted1.png "Térkép felúszik")

A generált származtatott oszlop transzformációjában minden egyes lebegő oszlop az észlelt névvel és adattípussal van leképezve. A fenti adatelőnézetben a "movieId" oszlop egész számként van észlelve. Ha a **leképezési** felszínre kattintott, a MovieId a származtatott oszlopban van definiálva, `toInteger(byName('movieId'))` és a séma nézeteiben szerepel az alsóbb rétegbeli átalakításokban.

![Képernyőfelvétel: a származtatott oszlop beállításainak lapja.](media/data-flow/mapdrifted2.png "Térkép felúszik")

## <a name="next-steps"></a>Következő lépések
Az [adatáramlás kifejezésének nyelvén](data-flow-expression-functions.md)további létesítményeket talál az oszlopok és a séma drift esetében, beleértve az "byName" és a "ByPosition" típust is.
