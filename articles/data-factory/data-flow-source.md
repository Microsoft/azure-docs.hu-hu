---
title: Forrás-átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan állíthatja be a forrás-átalakítást a leképezési adatfolyamban.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 72c36a60227c1f8598909c4e1888084c02b90c7b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368030"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Forrás-átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A forrás-átalakítás konfigurálja az adatforrást az adatfolyamhoz. Az adatfolyamatok tervezésekor az első lépés mindig a forrás-átalakítás konfigurálása. Forrás hozzáadásához jelölje ki a **forrás hozzáadása** mezőt az adatfolyam vásznon.

Minden adatfolyamhoz szükség van legalább egy forrás-átalakításra, de az adatátalakítások végrehajtásához tetszőleges számú forrást adhat hozzá. Ezeket a forrásokat összekapcsolhatja a csatlakozással, a kereséssel vagy a Union-transzformációval együtt.

Minden forrás-átalakítás pontosan egy adatkészlethez vagy egy társított szolgáltatáshoz van társítva. Az adatkészlet határozza meg az adatokat, amelyeket írni vagy olvasni szeretne. Ha fájl alapú adatkészletet használ, a forrásban helyettesítő karakterekkel és fájlokkal is használhat egyszerre több fájllal való munkát.

## <a name="inline-datasets"></a>Beágyazott adatkészletek

A forrás-átalakítás létrehozásakor létrehozott első döntés az, hogy a forrásadatok definiálva vannak-e egy adatkészlet-objektumban vagy a forrás-átalakításon belül. A legtöbb formátum csak az egyikben vagy a másikban érhető el. Egy adott összekötő használatának megismeréséhez tekintse meg a megfelelő összekötő-dokumentumot.

Ha egy formátum a beágyazott és egy adatkészlet objektumban is támogatott, akkor mindkettőnek van előnye. Az adatkészlet-objektumok újrafelhasználható entitások, amelyek más adatfolyamatokban és hasonló tevékenységekben, például másolásban is használhatók. Ezek az újrafelhasználható entitások különösen akkor hasznosak, ha megerősített sémát használ. Az adathalmazok nem a Sparkon alapulnak. Esetenként előfordulhat, hogy felül kell bírálnia bizonyos beállításokat vagy séma-kivetítést a forrás-átalakításban.

A beágyazott adatkészletek használata akkor ajánlott, ha rugalmas sémákat, egyszeri forrásoldali példányokat vagy paraméteres forrásokat használ. Ha a forrás erősen paraméteres, a beágyazott adatkészletek lehetővé teszik, hogy ne hozzon létre egy "dummy" objektumot. A beágyazott adatkészletek a Sparkon alapulnak, és a tulajdonságaik az adatfolyamhoz képest natívak.

Ha egy beágyazott adatkészletet szeretne használni, válassza ki a kívánt formátumot a **Forrástípus** -választóban. A forrás-adatkészlet kiválasztása helyett válassza ki azt a társított szolgáltatást, amelyhez csatlakozni szeretne.

![A beágyazott kijelölést bemutató képernyőkép.](media/data-flow/inline-selector.png "A beágyazott kijelölést bemutató képernyőkép.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Támogatott források típusai

Az adatforgalom leképezése egy kinyerési, betöltési és átalakítási (ELT) módszert követ, és az Azure-ban mind az *előkészítési* adatkészletekkel működik. Jelenleg a következő adatkészletek használhatók forrás-átalakításban.

| Összekötő | Formátum | Adatkészlet/beágyazott |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Service](format-common-data-model.md#source-properties)<br>[Tagolt szöveg](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Felügyelt Azure SQL-példány (előzetes verzió)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Az ezekhez az összekötőhöz tartozó beállítások a **forrás beállításai** lapon találhatók. A beállításokkal kapcsolatos információ-és adatfolyam-parancsfájlok példák az összekötő dokumentációjában találhatók.

Azure Data Factory több mint [90 natív összekötőhöz](connector-overview.md)férhet hozzá. Az adatfolyamatban lévő más forrásokból származó adatok belefoglalásához használja a másolási tevékenységet az adatok betöltéséhez az egyik támogatott átmeneti területre.

## <a name="source-settings"></a>Forrásbeállítások

Miután hozzáadta a forrást, konfigurálja a **beállításokat a forrás beállításai** lapon. Itt kiválaszthatja vagy létrehozhatja a forrásként szolgáló adatkészletet. Kiválaszthatja az adataihoz tartozó séma-és mintavételi beállításokat is.

Az adatkészlet paramétereinek fejlesztői értékeit a [hibakeresési beállításokban](concepts-data-flow-debug-mode.md)lehet konfigurálni. (A hibakeresési módot be kell kapcsolni.)

![A forrás beállításai lapot megjelenítő képernyőkép.](media/data-flow/source1.png "A forrás beállításai lapot megjelenítő képernyőkép.")

**Kimeneti adatfolyam neve**: a forrás-átalakítás neve.

**Forrás típusa**: válassza ki, hogy egy beágyazott adatkészletet vagy egy meglévő adatkészlet-objektumot szeretne használni.

**Kapcsolat tesztelése**: Ellenőrizze, hogy az adatfolyam Spark szolgáltatása sikeresen tud-e csatlakozni a forrás adatkészletben használt társított szolgáltatáshoz. A funkció engedélyezéséhez a hibakeresési módot kell bekapcsolni.

**Séma drift**: a [séma eltolódása](concepts-data-flow-schema-drift.md) Data Factory a rugalmas sémák natív kezelésére az adatfolyamatokban anélkül, hogy explicit módon meg kellene határozni az oszlopok módosításait.

* Jelölje be a **séma-drift engedélyezése** jelölőnégyzetet, ha a forrás oszlopai gyakran változnak. Ez a beállítás lehetővé teszi, hogy az összes bejövő forrás mező áthaladjon a fogadóra történő átalakításokon.

* A **következtetett oszlopok típusának** kiválasztása arra utasítja a Data Factoryt, hogy észlelje és definiálja az egyes felderített új oszlopok adattípusait. Ha ez a funkció ki van kapcsolva, az összes lebegő oszlop karakterlánc típusú lesz.

**Séma ellenőrzése:** Ha a **séma ellenőrzése** beállítás be van jelölve, az adatfolyam nem fog futni, ha a bejövő forrásadatok nem egyeznek az adatkészlet meghatározott sémájával.

**Sorok kihagyása**: a sorok **kihagyása** mező meghatározza, hogy az adatkészlet elején hány sort kell figyelmen kívül hagyni.

**Mintavételezés**: engedélyezze a **mintavételezést** a forrásból származó sorok számának korlátozásához. Akkor használja ezt a beállítást, ha hibakeresési célból teszteli vagy felveszi az adatait a forrásból.

Ha ellenőrizni szeretné, hogy a forrás megfelelően van-e konfigurálva, kapcsolja be a hibakeresési módot, és olvassa be az adatelőnézett. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Ha a hibakeresési mód be van kapcsolva, a hibakeresési beállításokban szereplő sorok korlátozása beállítás felülírja a forrás mintavételi beállításait az adatelőnézet során.

## <a name="source-options"></a>Forrás beállításai

A **forrás beállításai** lap az összekötőre és a választott formátumra vonatkozó beállításokat tartalmazza. További információkat és példákat a megfelelő [összekötő dokumentációjában](#supported-sources)talál.

## <a name="projection"></a>Vetület

Az adatkészletekben lévő sémák esetében a forrás vetülete határozza meg a forrásadatok adatoszlopait, típusait és formátumait. A legtöbb adatkészlet, például az SQL és a parketta esetében a forrásban lévő leképezés egy adatkészletben definiált sémának megfelelően van rögzítve. Ha a forrásfájlok nem szigorúak (például a parketta-fájlok helyett a Flat. CSV fájlok), megadhatja a forrás-átalakítás egyes mezőinek adattípusait.

![A vetítés lapon megjelenő beállításokat megjelenítő képernyőkép.](media/data-flow/source3.png "A vetítés lapon megjelenő beállításokat megjelenítő képernyőkép.")

Ha a szövegfájl nem rendelkezik meghatározott sémával, válassza az **adattípusok észlelése** lehetőséget, hogy Data Factory az adattípusok mintavételezését és következtetését. Válassza az **alapértelmezett formátum megadása** lehetőséget az alapértelmezett adatformátumok automatikus észleléséhez.

A **séma alaphelyzetbe állítása** visszaállítja a leképezést a hivatkozott adatkészletben definiált értékre.

Az oszlop adattípusait egy alsóbb rétegbeli származtatott oszlop transzformációjában módosíthatja. Az oszlopnevek módosításához válasszon transzformációt.

### <a name="import-schema"></a>Séma importálása

A **leképezés** lapon kattintson a **séma importálása** gombra, hogy egy aktív hibakeresési fürtöt hozzon létre a séma-kivetítés létrehozásához. Minden Forrástípus esetében elérhető. A séma importálása itt felülbírálja az adatkészletben definiált leképezést. Az adatkészlet-objektum nem módosul.

A séma importálása olyan adatkészletekben hasznos, mint a Avro és a Azure Cosmos DB, amelyek olyan összetett adatstruktúrákat támogatnak, amelyek nem igényelnek sémát az adatkészletben. A beágyazott adatkészletek esetében a séma importálása az egyetlen módja a séma-eltolódás nélküli oszlop metaadatainak hivatkozásának.

## <a name="optimize-the-source-transformation"></a>A forrás átalakítás optimalizálása

Az **optimalizálás** lapon megadhatja a partíciók adatainak szerkesztését az egyes átalakítási lépésekben. A legtöbb esetben az **aktuális particionálás használata** optimalizálja a forrás ideális particionálási struktúráját.

Ha Azure SQL Database forrásról olvas, az egyéni **forrás** -particionálás valószínűleg a leggyorsabb adatok olvasását végzi. Data Factory nagy lekérdezéseket olvas be azzal, hogy párhuzamosan csatlakozik az adatbázishoz. Ez a forrás particionálás egy oszlopon vagy egy lekérdezés használatával végezhető el.

![A forrás partíció beállításait megjelenítő képernyőkép.](media/data-flow/sourcepart3.png "A forrás partíció beállításait megjelenítő képernyőkép.")

További információ a leképezési adatfolyamon belüli optimalizálásról: [optimalizálás lap](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Következő lépések

Megkezdheti az adatforgalom kiépítése egy [származtatott oszlopos átalakítással](data-flow-derived-column.md) és egy [kiválasztott átalakítással](data-flow-select.md).
