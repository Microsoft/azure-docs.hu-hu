---
title: Common Data Model formátum
description: Adatok átalakítása a Common adatmodell metaadat-rendszerén keresztül
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: makromer
ms.openlocfilehash: 45f5334ebee3365c17bfa52c8d47ed75b82bdfa1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387699"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Közös adatmodell-formátum a Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Common adatmodell (CDM) metaadatrendszer lehetővé teszi, hogy az adatok és az azt jelenti, hogy könnyen meg lehessen osztani az alkalmazások és az üzleti folyamatok között. További információ: [Common adatmodell](/common-data-model/) – áttekintés.

A Azure Data Factory-ben a felhasználók a [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2)-ban tárolt model.jsés jegyzékfájlok adatait is át tudják alakítani a leképezési adatfolyamatok használatával. Az adatok CDM-formátumban is elhelyezhetők a CDM-entitások olyan hivatkozásaival, amelyek CSV-vagy parketta-formátumban szeretnék kiadni az adatait a particionált mappákban. 

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A Common adatmodell a forrásként és a fogadóként is elérhető [beágyazott adatkészletként](data-flow-source.md#inline-datasets) érhető el a leképezési adatforgalomban.

> [!NOTE]
> A CDM-entitások írásakor olyan meglévő CDM-entitás-definíciót (metaadat-sémát) kell használni, amelyet már definiáltak hivatkozásként való használatra. Az ADF-adatfolyam fogadója beolvassa a CDM-entitás fájlját, és importálja a sémát a fogadóba a mezők leképezéséhez.

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázatban a CDM-forrás által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `cdm` | igen | `cdm` | formátumban |
| Metaadatok formátuma | Ahol az entitás az adatelemre hivatkozik. Ha a CDM 1,0-es verzióját használja, válassza a jegyzékfájlt. Ha 1,0 előtti CDM-verziót használ, válassza a model.jslehetőséget. | Yes | `'manifest'` vagy `'model'` | manifestType |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve. Alapértelmezett érték: "default"  | Nem | Sztring | manifestName |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Időbélyeg | modifiedAfter <br> modifiedBefore | 
| Séma-társított szolgáltatás | A társított szolgáltatás, ahol a corpus található | igen, ha a jegyzékfájlt használja | `'adlsgen2'` vagy `'github'` | corpusStore | 
| Entitás-hivatkozási tároló | A Container Corpus a | igen, ha a manifest és a corpus használata ADLS Gen2 | Sztring | adlsgen2_fileSystem |
| Entitás-hivatkozási adattár | GitHub-adattár neve | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring | github_repository |
| Entitás hivatkozási ága | GitHub-tárház ág | igen, ha a GitHubon és a corpuson használja a GitHubot | Sztring |  github_branch |
| Corpus-mappa | a corpus gyökerének helye | igen, ha a jegyzékfájlt használja | Sztring | corpusPath |
| Corpus-entitás | Entitás hivatkozásának elérési útja | igen | Sztring | entitás |
| Nem található fájlok engedélyezése | Ha az értéke igaz, a rendszer nem dobja el a hibát, ha nem található fájl | nem | `true` vagy `false` | ignoreNoFilesFound |

Ha a forrás-és a fogadó átalakításban is kiválasztja az "Entity Reference" lehetőséget, a következő három lehetőség közül választhat az entitás-hivatkozás helyeként:

* A helyi használja az ADF által már használt jegyzékfájlban definiált entitást.
* Az egyéni parancs arra kéri, hogy mutasson egy olyan entitási jegyzékfájlra, amely eltér a jegyzékfájl ADF-fájljának a használatával
* A standard a alkalmazásban fenntartott CDM-entitások szabványos könyvtárából származó entitásokra mutató hivatkozást fog használni ```Github``` .

### <a name="sink-settings"></a>Fogadó beállításai

* Mutasson a CDM-entitás hivatkozási fájljára, amely tartalmazza az írni kívánt entitás definícióját.

![entitás beállításai](media/data-flow/common-data-model-111.png "Entitások bemutatása")

* Adja meg a kimeneti fájlok partíciójának elérési útját és formátumát, amelyet az ADF az entitások írásához használni kíván.

![entitás formátuma](media/data-flow/common-data-model-222.png "Entitás formátuma")

* Állítsa be a kimeneti fájl helyét, valamint a jegyzékfájl helyét és nevét.

![CDM helye](media/data-flow/common-data-model-333.png "CDM helye")


#### <a name="import-schema"></a>Séma importálása

A CDM csak beágyazott adatkészletként érhető el, és alapértelmezés szerint nem rendelkezik társított sémával. Az oszlop metaadatainak beszerzéséhez kattintson a **vetítés** lapon található **séma importálása** gombra. Ez lehetővé teszi a corpus által megadott oszlopnevek és adattípusok hivatkozását. A séma importálásához egy [adatfolyam-hibakeresési munkamenetnek](concepts-data-flow-debug-mode.md) aktívnak kell lennie, és rendelkeznie kell egy meglévő CDM-entitás-definíciós fájllal, amely a következőre mutat:.

Amikor az adatáramlási oszlopokat az entitás tulajdonságaira rendeli a fogadó átalakításban, kattintson a "leképezés" fülre, és válassza a "séma importálása" lehetőséget. Az ADF beolvassa az entitásra mutató hivatkozást, amelyet a fogadó beállításaiban mutatott, így a cél CDM-sémához képezhető le.

![CDM-fogadó beállításai](media/data-flow/common-data-model-444.png "CDM-leképezés")

> [!NOTE]
>  Ha olyan model.jshasznál, amely Power BI vagy a Power platform adatfolyamok származik, akkor a forrás-átalakítás során a "corpus Path null vagy üres" hibaüzenet jelenhet meg. Ez valószínűleg a partíció helyének elérési útjának formázásával kapcsolatos, a fájl model.js. A probléma megoldásához kövesse az alábbi lépéseket: 

1. A model.jsfájl megnyitása egy szövegszerkesztőben
2. Keresse meg a partíciókat. Location tulajdonság 
3. A "blob.core.windows.net" módosítása a "dfs.core.windows.net" értékre
4. Javítsa ki a (z) "% 2F" kódolást az URL-ben a "/" értékre.
5. Ha az ADF-adatfolyamatokat használja, a partíciós fájl elérési útjában szereplő speciális karaktereket alfanumerikus értékekkel kell helyettesíteni, vagy váltania kell a szinapszis adatforgalmára.

### <a name="cdm-source-data-flow-script-example"></a>A CDM-forrás adatáramlási parancsfájljának példája

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban a CDM-fogadó által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Formátum | A formátumot kötelező megadni `cdm` | igen | `cdm` | formátumban |
| Gyökér helye: tároló | A CDM-mappa tárolójának neve | igen | Sztring | Fájlrendszer |
| Gyökér helye: mappa elérési útja | A CDM-mappa gyökérmappa helye | igen | Sztring | folderPath |
| Manifest-fájl: entitás elérési útja | Az entitás mappájának elérési útja a gyökérkönyvtáron belül | nem | Sztring | entityPath |
| Manifest-fájl: jegyzékfájl neve | A jegyzékfájl neve. Alapértelmezett érték: "default" | Nem | Sztring | manifestName |
| Séma-társított szolgáltatás | A társított szolgáltatás, ahol a corpus található | igen | `'adlsgen2'` vagy `'github'` | corpusStore | 
| Entitás-hivatkozási tároló | A Container Corpus a | igen, ha a corpus ADLS Gen2 | Sztring | adlsgen2_fileSystem |
| Entitás-hivatkozási adattár | GitHub-adattár neve | igen, ha Corpus a GitHubban | Sztring | github_repository |
| Entitás hivatkozási ága | GitHub-tárház ág | igen, ha Corpus a GitHubban | Sztring |  github_branch |
| Corpus-mappa | a corpus gyökerének helye | igen | Sztring | corpusPath |
| Corpus-entitás | Entitás hivatkozásának elérési útja | igen | Sztring | entitás |
| Partíció elérési útja | A partíció írásának helye | nem | Sztring | partitionPath |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Formátum típusa | Válassza a parketta formátumának megadása lehetőséget. | nem | `parquet` Ha meg van adva | alformátum |
| Oszlop elválasztója | Ha DelimitedText ír, az oszlopok lehatároló módja | igen, ha írás a DelimitedText | Sztring | columnDelimiter |
| Első sor fejlécként | DelimitedText használata esetén, hogy az oszlopnevek fejlécként vannak-e hozzáadva | nem | `true` vagy `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>A CDM-gyűjtő adatfolyam-parancsfájljának példája

A társított adatfolyam-parancsfájl:

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>Következő lépések

Forrás- [átalakítás](data-flow-source.md) létrehozása a leképezési adatfolyamban.
