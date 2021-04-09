---
title: Különbözeti formátum a Azure Data Factoryban
description: Adatok átalakítása és áthelyezése egy Delta-tótól a különbözeti formátum használatával
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: daperlov
ms.openlocfilehash: 74df809f2206a105b405ba184949ef887096ebc2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932505"
---
# <a name="delta-format-in-azure-data-factory"></a>Különbözeti formátum a Azure Data Factoryban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt mutatja be, hogyan másolhat adatok egy [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) vagy [Azure Blob Storage](connector-azure-blob-storage.md) tárolt Delta-tóból a Delta formátum használatával. Ez az összekötő [beágyazott adatkészletként](data-flow-source.md#inline-datasets) érhető el a leképezési adatforgalomban forrásként és fogadóként.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Ez az összekötő [beágyazott adatkészletként](data-flow-source.md#inline-datasets) érhető el a leképezési adatforgalomban forrásként és fogadóként.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázat a Delta forrás által támogatott tulajdonságokat sorolja fel. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `delta` | igen | `delta` | formátumban |
| Fájlrendszer | A Delta-tó tárolója/fájlrendszere | igen | Sztring | Fájlrendszer |
| Mappa elérési útja | A Delta-tó közvetlen | igen | Sztring | folderPath |
| Tömörítés típusa | A különbözeti tábla tömörítési típusa | nem | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | fájba |
| Tömörítési szint | Válassza ki, hogy a tömörítés a lehető leggyorsabban befejeződjön-e, vagy ha az eredményül kapott fájlt optimálisan kell tömöríteni. | kötelező, ha meg `compressedType` van adva. | `Optimal` vagy `Fastest` | compressionLevel |
| Utazási idő | Válassza ki, hogy szeretne-e lekérdezni egy különbözeti tábla egy régebbi pillanatképét | nem | Lekérdezés timestamp szerint: időbélyeg <br> Lekérdezés verzió: Integer | timestampAsOf <br> versionAsOf |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>Séma importálása

A Delta csak beágyazott adatkészletként érhető el, és alapértelmezés szerint nem rendelkezik társított sémával. Az oszlop metaadatainak beszerzéséhez kattintson a **vetítés** lapon található **séma importálása** gombra. Ez lehetővé teszi a corpus által megadott oszlopnevek és adattípusok hivatkozását. A séma importálásához egy [adatfolyam-hibakeresési munkamenetnek](concepts-data-flow-debug-mode.md) aktívnak kell lennie, és rendelkeznie kell egy meglévő CDM-entitás-definíciós fájllal, amely a következőre mutat:.
 

### <a name="delta-source-script-example"></a>Példa a Delta forrás parancsfájlra

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban a különbözeti fogadó által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `delta` | igen | `delta` | formátumban |
| Fájlrendszer | A Delta-tó tárolója/fájlrendszere | igen | Sztring | Fájlrendszer |
| Mappa elérési útja | A Delta-tó közvetlen | igen | Sztring | folderPath |
| Tömörítés típusa | A különbözeti tábla tömörítési típusa | nem | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | fájba |
| Tömörítési szint | Válassza ki, hogy a tömörítés a lehető leggyorsabban befejeződjön-e, vagy ha az eredményül kapott fájlt optimálisan kell tömöríteni. | kötelező, ha meg `compressedType` van adva. | `Optimal` vagy `Fastest` | compressionLevel |
| Vacuum | A tábla régebbi verziói esetében a megőrzési küszöbértéket órában kell megadni. 0 vagy kevesebb alapértelmezett érték 30 nap. | igen | Egész szám | vákuum |
| Frissítési módszer | Itt adhatja meg, hogy mely frissítési műveletek engedélyezettek a Delta-tavon. A nem beszúrt metódusok esetében a sorok megjelöléséhez egy előző módosítási sor átalakítására van szükség. | igen | `true` vagy `false` | törölhető <br> Insertable <br> frissíthető <br> Körlevél |
| Optimalizált írás | Az írási művelethez nagyobb átviteli sebességet érhet el, a belső Shuffle-t a Spark-végrehajtók optimalizálása révén. Ennek eredményeképpen előfordulhat, hogy kevesebb partíciót és fájlt kell megfigyelni, amely nagyobb méretű | nem | `true` vagy `false` | optimizedWrite: true |
| Automatikus tömörítés | Az írási műveletek befejezését követően a Spark automatikusan végrehajtja a ```OPTIMIZE``` parancsot az adat újraszervezéséhez, így szükség esetén további partíciókat eredményez, így a jövőben jobb olvasási teljesítményt nyújt. | nem | `true` vagy `false` |   autocompact: igaz |

### <a name="delta-sink-script-example"></a>Példa a különbözeti fogadó parancsfájlra

A társított adatfolyam-parancsfájl:

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>Ismert korlátozások

A különbözeti fogadóba való írás során létezik egy ismert korlátozás, amelyben a rendszer nem adja vissza az írásos sorok számát a figyelési kimenetben.

## <a name="next-steps"></a>Következő lépések

* Forrás- [átalakítás](data-flow-source.md) létrehozása a leképezési adatfolyamban.
* Hozzon létre egy fogadó [transzformációt](data-flow-sink.md) a leképezési adatfolyamatban.
* Hozzon létre egy [Alter Row átalakítást](data-flow-alter-row.md) a sorok Beszúrás, frissítés, upsert vagy törlés céljából való megjelöléséhez.
