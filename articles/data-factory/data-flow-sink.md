---
title: Fogadó átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan konfigurálhat egy fogadó transzformációt a leképezési folyamatokban.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 5548d82326ec4ac2306e2c8945bedc20236a4e54
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009351"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Fogadó átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Miután befejezte az adatátalakítást, írja azt egy célhelyre a fogadó transzformáció használatával. Minden adatfolyamhoz szükség van legalább egy fogadó átalakításra, de az átalakítási folyamat befejezéséhez annyi süllyedés szükséges, amennyit csak lehet. További mosogatók írásához hozzon létre új streameket új ágak és feltételes felosztások segítségével.

Minden fogadó transzformáció pontosan egy Azure Data Factory adatkészlet-objektumhoz vagy társított szolgáltatáshoz van társítva. A fogadó átalakítás meghatározza a írni kívánt adatmennyiséget és helyet.

## <a name="inline-datasets"></a>Beágyazott adatkészletek

A fogadó-átalakítás létrehozásakor adja meg, hogy a fogadó információ egy adatkészlet-objektumon belül vagy a fogadó átalakításban van-e definiálva. A legtöbb formátum csak az egyikben vagy a másikban érhető el. Egy adott összekötő használatának megismeréséhez tekintse meg a megfelelő összekötő-dokumentumot.

Ha egy formátum a beágyazott és egy adatkészlet objektumban is támogatott, akkor mindkettőnek van előnye. Az adatkészlet-objektumok újrafelhasználható entitások, amelyek más adatfolyamatokban és hasonló tevékenységekben, például másolásban is használhatók. Ezek az újrafelhasználható entitások különösen akkor hasznosak, ha megerősített sémát használ. Az adathalmazok nem a Sparkon alapulnak. Esetenként előfordulhat, hogy felül kell bírálnia bizonyos beállításokat vagy séma-kivetítést a fogadó átalakításban.

A beágyazott adatkészletek használata akkor ajánlott, ha rugalmas sémákat, egyszeri fogadó példányokat vagy paraméteres mosogatókat használ. Ha a fogadó erősen paraméteres, a beágyazott adatkészletek lehetővé teszik, hogy ne hozzon létre egy "dummy" objektumot. A beágyazott adatkészletek a Sparkon alapulnak, és a tulajdonságaik az adatfolyamhoz képest natívak.

Ha egy beágyazott adatkészletet szeretne használni, válassza ki a kívánt formátumot a fogadó **típus** -választóban. A fogadó adatkészlet kiválasztása helyett válassza ki azt a társított szolgáltatást, amelyhez csatlakozni szeretne.

![A beágyazott kijelölést bemutató képernyőkép.](media/data-flow/inline-selector.png "A beágyazott kijelölést bemutató képernyőkép.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Támogatott fogadó típusok

Az adatforgalom leképezése egy kinyerési, betöltési és átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. Jelenleg a következő adatkészletek használhatók forrás-átalakításban.

| Összekötő | Formátum | Adatkészlet/beágyazott |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Service](format-common-data-model.md#sink-properties)<br>[Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Felügyelt Azure SQL-példány (előzetes verzió)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Az ezekhez az összekötőhöz tartozó beállítások a **Beállítások** lapon találhatók. A beállításokkal kapcsolatos információ-és adatfolyam-parancsfájlok példák az összekötő dokumentációjában találhatók.

Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)férhet hozzá. Ha az adatfolyamatból más forrásoknak is szeretne adatírást készíteni, a másolási tevékenység használatával töltse be az adatok egy támogatott fogadóból.

## <a name="sink-settings"></a>Fogadó beállításai

Miután hozzáadta a fogadót, konfigurálja a fogadó **lapon.** Itt kiválaszthatja vagy létrehozhatja azt az adatkészletet, amelyet a fogadó ír. Az adatkészlet paramétereinek fejlesztői értékeit a [hibakeresési beállításokban](concepts-data-flow-debug-mode.md)lehet konfigurálni. (A hibakeresési módot be kell kapcsolni.)

A következő videó számos különböző fogadó lehetőséget ismertet a szöveggel tagolt fájltípusok esetében.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![A fogadó beállításait bemutató képernyőkép.](media/data-flow/sink-settings.png "A fogadó beállításait bemutató képernyőkép.")

**Séma drift**: a [séma eltolódása](concepts-data-flow-schema-drift.md) Data Factory a rugalmas sémák natív kezelésére az adatfolyamatokban anélkül, hogy explicit módon meg kellene határozni az oszlopok módosításait. Engedélyezze a séma eltolásának engedélyezése **lehetőséget** , hogy további oszlopokat írjon a fogadó adatsémában definiált elemek fölé.

**Séma ellenőrzése**: Ha a séma ellenőrzése beállítás be van jelölve, az adatfolyam sikertelen lesz, ha a bejövő forrás sémájának bármely oszlopa nem található a forrás kivetítésben, vagy ha az adattípusok nem egyeznek. Ezzel a beállítással kényszerítheti ki, hogy a forrásadatok megfelelnek a megadott leképezési szerződésnek. Az adatbázis-forrás forgatókönyvekben hasznos lehet az oszlopnevek vagy típusok megváltozásának jelzésére.

## <a name="cache-sink"></a>Gyorsítótár fogadója

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

A *gyorsítótár* -fogadó akkor történik meg, amikor egy adatfolyam az adattár helyett a Spark-gyorsítótárba ír egy adatot. Az adatfolyamatok leképezése során a *gyorsítótár-kereséssel* többször is hivatkozhat ezekre az adatfolyamatokra. Ez akkor hasznos, ha egy kifejezés részeként szeretne információkat hivatkozni, de nem szeretné explicit módon csatlakoztatni az oszlopokat. Gyakori példák arra, hogy egy gyorsítótár-fogadó képes legyen a maximális érték megkeresésére egy adattárban, és a hibakódok megfeleltetése egy hibaüzenet-adatbázishoz. 

Egy gyorsítótár-fogadóba való íráshoz vegyen fel egy fogadó-átalakítást, és válassza a **gyorsítótár** lehetőséget a fogadó típusaként. A többi fogadó típustól eltérően nem kell kiválasztania egy adatkészletet vagy társított szolgáltatást, mert nem ír külső tárolóba. 

![Gyorsítótár-fogadó kiválasztása](media/data-flow/select-cache-sink.png "Gyorsítótár-fogadó kiválasztása")

A fogadó beállításaiban megadhatja a gyorsítótár-fogadó fő oszlopait is. Ezeket a rendszer az egyező feltételekként használja, amikor a `lookup()` függvényt használja a gyorsítótár-keresésben. Ha kulcs oszlopokat ad meg, nem használhatja a `outputs()` függvényt a gyorsítótár-keresésben. A gyorsítótár-keresési szintaxissal kapcsolatos további tudnivalókért lásd a [gyorsítótárazott keresések](concepts-data-flow-expression-builder.md#cached-lookup)című témakört.

![Gyorsítótár fogadó kulcsának oszlopai](media/data-flow/cache-sink-key-columns.png "Gyorsítótár fogadó kulcsának oszlopai")

Ha például egyetlen kulcs oszlopot ad meg `column1` egy nevű gyorsítótár-fogadóban `cacheExample` , a híváshoz egy `cacheExample#lookup()` paraméterrel megadható, hogy a gyorsítótár-fogadó melyik sorát kell megegyeznie. A függvény egyetlen összetett oszlopot ad eredményül minden leképezett oszlop aloszlopával.

> [!NOTE]
> A gyorsítótár-fogadónak egy teljes mértékben független adatfolyamban kell lennie, amely minden, a gyorsítótár-kereséssel hivatkozó átalakításból elérhető. A gyorsítótár-fogadónak az első befogadót is meg kell írnia. 

## <a name="field-mapping"></a>Mezőleképezés

A kiválasztott átalakításhoz hasonlóan a fogadó **leképezés** lapján eldöntheti, hogy a bejövő oszlopok milyen módon lesznek írva. Alapértelmezés szerint a rendszer az összes bemeneti oszlopot leképezi, beleértve az elsodródott oszlopokat is. Ezt a viselkedést az *automapping* szolgáltatásnak nevezzük.

Ha kikapcsolja az automapping szolgáltatást, a rögzített oszlop alapú hozzárendelések vagy a szabályokon alapuló leképezések is hozzáadhatók. A szabályokon alapuló hozzárendelésekkel a mintázatok egyeztetésével írhat kifejezéseket. A rögzített leképezési leképezések logikai és fizikai oszlopnevek. A szabályokon alapuló leképezéssel kapcsolatos további információkért lásd: az [adatforgalom leképezése az oszlopok mintái között](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Egyéni fogadó rendelés

Alapértelmezés szerint az determinált sorrendben több mosogatóba történik az adatgyűjtés. A végrehajtó motor párhuzamosan írja az adatelemzési logikát, mivel az átalakítási logika befejeződött, és az egyes futtatások sorrendje eltérő lehet. A fogadó pontos sorrendjének megadásához engedélyezze az **Egyéni fogadó sorrendet** az adatfolyam **általános** lapján. Ha engedélyezve van, a mosogatók egymás után, növekvő sorrendben íródnak.

![Képernyőkép, amely az egyéni fogadó sorrendet mutatja.](media/data-flow/custom-sink-ordering.png "Képernyőkép, amely az egyéni fogadó sorrendet mutatja.")

> [!NOTE]
> A [gyorsítótárazott keresések](./concepts-data-flow-expression-builder.md#cached-lookup)használatakor győződjön meg arról, hogy a fogadó megrendelése a gyorsítótárazott tárolók értéke 1, a legalacsonyabb (vagy az első) a sorrend.

![Egyéni fogadó rendelés](media/data-flow/cache-2.png "Egyéni fogadó rendelés")

### <a name="sink-groups"></a>Fogadó csoportok

A mosogatók egymásba rendezéséhez ugyanazt a sorszámot kell alkalmazni egy adatsorozathoz. Az ADF ezeket a gyűjtőket olyan csoportoknak fogja kezelni, amelyek párhuzamosan hajthatók végre. A párhuzamos végrehajtás lehetőségei a folyamat adatforgalmának tevékenységében lesznek felszínre.

## <a name="error-row-handling"></a>Hibát tartalmazó sorok kezelése

Az adatbázisba való íráskor bizonyos sorok nem hajthatók végre, mert a cél által meghatározott korlátozások vannak megadva. Alapértelmezés szerint az adatfolyamatok futtatása meghiúsul az első hiba esetén. Bizonyos összekötők esetében dönthet úgy, hogy **folytatja a hibát** , amely lehetővé teszi az adatfolyam befejeződését, még akkor is, ha az egyes sorok hibákkal rendelkeznek. Ez a funkció jelenleg csak Azure SQL Databaseban érhető el. További információ: [Az Azure SQL dB-ben a hibajelentések feldolgozása](connector-azure-sql-database.md#error-row-handling).

Az alábbi videó bemutatja, hogyan használható az adatbázis-hibák sora automatikusan a fogadó transzformációban.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Az adatelőnézet a fogadóban

Amikor egy hibakeresési fürtön beolvas egy előnézetet, a rendszer nem írja le az adatait a fogadóba. Egy pillanatkép arról, hogy az adatok milyen módon lesznek visszaadva, de semmi sem kerül a célhelyre. Az adatok a fogadóba való írásának teszteléséhez futtasson egy folyamat-hibakeresést a folyamat vásznon.

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta az adatfolyamatot, adjon hozzá egy [adatfolyam-tevékenységet a folyamathoz](concepts-data-flow-overview.md).