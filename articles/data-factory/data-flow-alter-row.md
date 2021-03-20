---
title: A sorok átalakításának módosítása a leképezési adatfolyamban
description: Az adatbázis céljának frissítése az Alter Row Transformation használatával a leképezési adatforgalomban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82982649"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>A sorok átalakításának módosítása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Alter sort Transformation paranccsal szúrhatja be a sorokba az INSERT, DELETE, Update és upsert szabályzatokat. Egy-a-többhöz feltételeket adhat hozzá kifejezésként. Ezeket a feltételeket prioritási sorrendben kell megadni, mivel minden egyes sor az első megfeleltetési kifejezésnek megfelelő szabályzattal lesz megjelölve. Ezek a feltételek egy sor (vagy sor) beszúrását, frissítését, törlését vagy upserted eredményezik. Az Alter Row a DDL-& a DML-műveleteket is előkészítheti az adatbázison.

![Módosítási sor beállításai](media/data-flow/alter-row1.png "Módosítási sor beállításai")

Az Alter sor átalakítások csak adatbázis-vagy CosmosDB-tárolókban működnek az adatfolyamban. A sorokhoz rendelt műveletek (INSERT, Update, DELETE, upsert) nem lépnek fel a hibakeresési munkamenetek során. Futtasson egy végrehajtási adatfolyamati tevékenységet egy folyamaton belül, hogy meghozza a módosítási sor szabályzatait az adatbázis tábláiban.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>Alapértelmezett sor szabályzatának meghatározása

Hozzon létre egy Alter sort átalakítást, és határozzon meg egy feltételt tartalmazó sort `true()` . Minden olyan sor meg lesz jelölve, amely nem felel meg a korábban definiált kifejezéseknek. Alapértelmezés szerint minden olyan sor meg lesz jelölve, amely nem felel meg a feltételes kifejezéseknek `Insert` .

![Módosítási sor házirendje](media/data-flow/alter-row4.png "Módosítási sor házirendje")

> [!NOTE]
> Az összes sor egyetlen házirenddel való megjelöléséhez létrehozhat egy feltételt az adott szabályzathoz, és a feltételt is megadhatja `true()` .

## <a name="view-policies-in-data-preview"></a>Szabályzatok megtekintése az adatelőnézetben

[Hibakeresési mód](concepts-data-flow-debug-mode.md) használatával megtekintheti az Alter Row-szabályzatok eredményeit az adatelőnézet ablaktáblán. Az Alter Row Transformation adatelőnézete nem hoz létre DDL-vagy DML-műveleteket a célhelyen.

![Módosítási sor házirendjei](media/data-flow/alter-row3.png "Módosítási sor házirendjei")

Minden módosítási sor házirendjét egy ikon jelöli, amely jelzi, hogy bekövetkezik-e az INSERT, a Update, a upsert vagy a Deleted művelet. A felső fejléc azt mutatja, hogy az egyes szabályzatok hány sort érintenek az előzetes verzióban.

## <a name="allow-alter-row-policies-in-sink"></a>Módosítási sor szabályzatának engedélyezése a fogadóban

Ahhoz, hogy az Alter Row-szabályzatok működjenek, az adatfolyamnak egy adatbázisba vagy egy Cosmos-fogadóba kell írnia. A fogadó **Beállítások** lapján engedélyezze, hogy az adott fogadó számára engedélyezett legyen a módosítási sor szabályzata.

![Módosítási sor fogadója](media/data-flow/alter-row2.png "Módosítási sor fogadója")

Az alapértelmezett viselkedés csak beszúrások engedélyezése. Ha engedélyezni szeretné a frissítéseket, a upsert vagy a törlést, jelölje be az adott feltételnek megfelelő fogadó jelölőnégyzetét. Ha a frissítések, a upsert vagy a törlések engedélyezve vannak, meg kell adnia, hogy a fogadó mely kulcs oszlopait kell megegyeznie.

> [!NOTE]
> Ha a lapkák, a frissítések vagy a upsert módosítja a céltábla sémáját a fogadóban, az adatfolyam sikertelen lesz. Ha módosítani szeretné a célként megadott sémát az adatbázisban, válassza a tábla **újbóli létrehozása** lehetőséget a tábla műveletként. Ezzel eldobásra kerül, és újból létrehozza a táblát az új séma-definícióval.

A fogadó átalakításhoz egy vagy több kulcsra van szükség a céladatbázis egyedi sorainak azonosításához. SQL-elsüllyedés esetén állítsa be a kulcsokat a fogadó beállításai lapon. A CosmosDB állítsa be a partíció kulcsát a beállítások között, és állítsa be az "id" CosmosDB rendszer mezőt a fogadó leképezésében. A CosmosDB esetében kötelező megadni a (z) "id" rendszeroszlopot a frissítésekhez, a upsert és a törlésekhez.

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Egyesítés és upsert Azure SQL Database és Szinapszisokkal

Az ADF-adatfolyamatok támogatják a Azure SQL Database és a szinapszis adatbázis-készlet (adattárház) egyesítését a upsert kapcsolóval.

Előfordulhat azonban, hogy olyan forgatókönyvekben fut, amelyekben a célként megadott adatbázis-séma a kulcs oszlopainak Identity (identitás) tulajdonságát használta. Az ADF-nek meg kell határoznia azokat a kulcsokat, amelyeket a rendszer a frissítések és upsert sorainak megfeleltetéséhez fog használni. Ha azonban a cél oszlopban az Identity tulajdonság be van állítva, és a upsert szabályzatot használja, a céladatbázis nem teszi lehetővé az oszlopba való írást. Előfordulhat, hogy hibákba ütközik, amikor megpróbál upsert egy elosztott tábla terjesztési oszlopával.

A következő módszerekkel oldható meg:

1. Nyissa meg a fogadó átalakítási beállításokat, és állítsa be a "kihagyás a kulcsok oszlopainak" beállítást. Ezzel a beállítással megadhatja, hogy az ADF ne írja meg a leképezés kulcsaként kiválasztott oszlopot.

2. Ha a kulcs oszlop nem az a oszlop, amely a problémát okozza az identitás oszlopaiban, akkor használhatja a fogadó átalakítás előtti SQL-beállítást: ```SET IDENTITY_INSERT tbl_content ON``` . Ezt követően kapcsolja ki a feldolgozás utáni SQL-tulajdonságot: ```SET IDENTITY_INSERT tbl_content OFF``` .

3. Mind az identitás, mind a terjesztési oszlop esetében átválthatja a logikát a Upsert-ről egy külön frissítési feltételre és egy külön beszúrási feltételre egy feltételes felosztású átalakítás használatával. Így beállíthatja a leképezést a frissítési útvonalon, hogy figyelmen kívül hagyja a kulcs oszlopának leképezését.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Syntax

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű módosítási sor átalakítása `CleanData` , amely egy bejövő adatfolyamot vesz igénybe, `SpecifyUpsertConditions` és három módosítási sor feltételt hoz létre. Az előző átalakítás során a `alterRowCondition` rendszer kiszámítja a nevű oszlopot, amely meghatározza, hogy a rendszer beszúrja, frissíti vagy törli-e a sort az adatbázisban. Ha az oszlop értéke olyan karakterlánc-értékkel rendelkezik, amely megfelel az Alter Row szabálynak, akkor azt a rendszer hozzárendeli a szabályzathoz.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Módosítható sor – példa](media/data-flow/alter-row4.png "Módosítható sor – példa")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Következő lépések

A módosítási sor átalakítását követően érdemes lehet [az adatait egy célhely adattárba menteni](data-flow-sink.md).
