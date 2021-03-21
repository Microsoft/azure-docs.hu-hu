---
title: Kivetítések meghatározása a Knowledge Store-ban
titleSuffix: Azure Cognitive Search
description: Példák arra, hogy miként lehet a dúsított dokumentumokat a Power BI vagy az Azure ML-vel való használatra használni a Tudásbázisban.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97509920"
---
# <a name="how-to-shape-and-export-enrichments"></a>A dúsítások formázása és exportálása

A kivetítések az adott Tudásbázisban a dúsított dokumentumok fizikai kifejezései. A dúsított dokumentumok hatékony használata struktúrát igényel. Ebben a cikkben a struktúra és a kapcsolatok megismerésével, a kivetítési tulajdonságok kiépítésével, valamint a létrehozott vetületi típusok közötti adatkapcsolattal kapcsolatos tudnivalókkal foglalkozunk. 

A kivetítés létrehozásához az adatszerkezetet formázó [képességgel](cognitive-search-skill-shaper.md) formázhatja egyéni objektum létrehozásához, vagy egy leképezési definíción belül a beágyazott formázási szintaxis használatával. 

Egy adatalakzat tartalmazza a projekthez szánt összes olyan adatforrást, amely csomópontok hierarchiája. Ez a cikk számos olyan módszert mutat be az adatátalakításhoz, amely a jelentéskészítési, elemzési vagy az alsóbb rétegbeli feldolgozást eredményező fizikai struktúrákba való vetítésre alkalmas. 

Az ebben a cikkben bemutatott példák ebben a [REST API mintában](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)találhatók, amely http-ügyfélben tölthető le és futtatható.

## <a name="introduction-to-projection-examples"></a>Bevezetés a kivetítési példákba

Háromféle [kivetítési](knowledge-store-projection-overview.md)típus létezik:

+ Táblázatok
+ Objektumokat
+ Fájlok

A táblázatos előrejelzések tárolása az Azure Table Storage szolgáltatásban történik. Az objektumok és a fájlok kivetítése blob Storage-ba történik, ahol az objektum-kivetítések JSON-fájlként lesznek mentve, és tartalmazhatnak tartalmakat a forrás dokumentumból, valamint bármilyen képességbeli kimenetet vagy dúsítást is. A dúsítási folyamat olyan bináris fájlokat is képes kinyerni, mint például a képek, ezek a bináris fájlok fájl-kivetítésként vannak kiválasztva. Ha egy bináris objektum kivetítése objektumként történik, csak a hozzá társított metaadatokat JSON-blobként menti a program. 

Az Adatátalakítási és-kivetítések közötti metszéspont megismeréséhez a következő készségkészlet fogjuk használni a különböző konfigurációk feltárásához. Ez a készségkészlet a nyers képet és a szöveges tartalmat dolgozza fel. A kivetítések a dokumentum tartalmából és a képességek kimenetei alapján lesznek meghatározva a kívánt forgatókönyvekhez.

> [!IMPORTANT] 
> A kivetítések kipróbálásakor hasznos az [Indexelő gyorsítótár tulajdonságának beállítása](search-howto-incremental-index.md) a Cost Control biztosításához. A kivetítések szerkesztése azt eredményezi, hogy a teljes dokumentumot újra gazdagítja a rendszer, ha nincs beállítva az indexelő gyorsítótára. Ha a gyorsítótár be van állítva, és csak az előrejelzések frissülnek, a korábban dúsított dokumentumok készségkészlet végrehajtása nem eredményez új Cognitive Services díjat.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Ha ezt a készségkészlet használja a nullával, `knowledgeStore` az első példa az objektum kitöltésére szolgál `knowledgeStore` , amely olyan kivetítésekkel van konfigurálva, amelyek táblázatos adatstruktúrákat hoznak létre, amelyeket más helyzetekben használhatunk. 

## <a name="projecting-to-tables"></a>Vetítés táblázatokra

Az Azure Storage-táblákba való kivetítés hasznos lehet az olyan eszközök használatával történő jelentéskészítéshez és elemzésekhez, mint például a Power BI. A Power BI beolvashatja a táblázatokat, és felderítheti a kapcsolatokat a kivetítés során generált kulcsok alapján. Ha irányítópultot próbál létrehozni, a kapcsolódó adatokkal a feladat egyszerűbbé válik. 

Hozzon létre egy irányítópultot, amely megjeleníti a dokumentumokból Word-felhőként kinyert legfontosabb kifejezéseket. A megfelelő adatstruktúra létrehozásához adjon hozzá egy formáló képességet a készségkészlet egy olyan egyéni alakzat létrehozásához, amely a dokumentumra vonatkozó részleteket és a legfontosabb kifejezéseket tartalmaz. Az egyéni alakzatot a rendszer a `pbiShape` `document` legfelső szintű csomóponton hívja meg.

> [!NOTE] 
> A tábla-előrejelzések az Azure Storage-táblák, amelyek az Azure Storage által kiszabott tárolási korlátokra vonatkoznak. További információ: [Table Storage-korlátok](/rest/api/storageservices/understanding-the-table-service-data-model). Érdemes tudni, hogy az entitás mérete nem haladhatja meg az 1 MB-ot, és egyetlen tulajdonság sem lehet nagyobb, mint 64 KB. Ezek a megkötések jó megoldást biztosítanak a táblák nagy számú kis entitásának tárolására.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Egyéni alakzat létrehozása a Shapeer-szakértelem használatával

Hozzon létre egy egyéni alakzatot, amelyet táblázatos tárolóba tud készíteni. Egyéni alakzat nélkül a leképezés csak egyetlen csomópontra hivatkozhat (a kimenet egy vetülete). Az egyéni alakzat létrehozásakor a különböző elemek egy új logikai egészbe kerülnek, amelyet egyetlen táblázatként lehet kiépíteni, vagy a táblázatok egy gyűjteménye között darabolva és elosztva. 

Ebben a példában az egyéni alakzat a metaadatokat és az azonosított entitásokat és a kulcsfontosságú kifejezéseket ötvözi. Az objektum neve `pbiShape` és a szülője `/document` . 

> [!IMPORTANT] 
> Az egyik célja annak biztosítása, hogy az összes dúsítási csomópontot megfelelően formázott JSON-ban fejezzük ki, amely a Knowledge Store-ba való kivetítéshez szükséges. Ez különösen akkor igaz, ha egy alkoholtartalom-növelési fa olyan csomópontokat tartalmaz, amelyek nem megfelelően formázott JSON-t tartalmaznak (például ha a dúsítás egy primitív, például egy karakterlánc).
>
> Figyelje meg az utolsó két csomópontot, `KeyPhrases` és `Entities` . Ezek egy érvényes JSON-objektumba vannak becsomagolva a következővel: `sourceContext` . Erre azért van szükség, mert a `keyphrases` `entities` primitívek gazdagítása szükséges, és az érvényes JSON-ra kell alakítani, mielőtt a projekt elérhetővé válnak.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Adja hozzá a fenti formáló képességet a készségkészlet. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Most, hogy minden szükséges adattal rendelkezünk a táblákhoz, frissítse a knowledgeStore objektumot a táblázat-definíciókkal. Ebben a példában három táblázat van megadva, a `tableName` és a tulajdonságok beállításával `source` `generatedKeyName` .

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Az alábbi lépéseket követve feldolgozhatja a munkáját:

1. Állítsa a ```storageConnectionString``` tulajdonságot egy érvényes v2 általános célú Storage-fiók kapcsolódási karakterláncára.  

1. Frissítse a készségkészlet a PUT kérelem kiállításával.

1. A készségkészlet frissítése után futtassa az indexelő. 

Most már van egy működő kivetítése három táblával. Ezeknek a tábláknak a Power BIba való importálása a kapcsolatok automatikus felfedését Power BI eredményezi.

Mielőtt továbblép a következő példára, a táblázat vetületének szempontjait áttekintve megismerheti a szeletelők és a kapcsolódó adatmennyiségek szerkezetét.

### <a name="slicing"></a>Szeletelés 

A szeletelés olyan technika, amely a teljes konszolidált alakzatot kioszthatja a rendszerelemek részévé. Az eredmény különálló, de kapcsolódó táblákból áll, amelyeket külön-külön dolgozhat fel.

A példában a az `pbiShape` összevont alakzat (vagy a dúsítási csomópont). A vetítés definíciójában a `pbiShape` további táblákba kerül, így az alakzat részeit ```keyPhrases``` és ```Entities``` . Power BI ez hasznos lehet, ha több entitást és alkifejezést társít az egyes dokumentumokhoz, és további elemzéseket fog kapni, ha kategorizált adatként látja az entitásokat és a kifejezéseket.

A szeletelés implicit módon létrehoz egy kapcsolatot a szülő és a gyermek tábla között, és a ```generatedKeyName``` fölérendelt táblában létrehoz egy azonos nevű oszlopot az alárendelt táblában. 

### <a name="naming-relationships"></a>Elnevezési kapcsolatok

A ```generatedKeyName``` és a ```referenceKeyName``` Tulajdonságok a táblák közötti vagy akár a kivetítési típusok közötti összekötésére szolgálnak. A gyermektábla és a leképezés minden sorának egy tulajdonsága a szülőre mutat vissza. A gyermek oszlopának vagy tulajdonságának neve a ```referenceKeyName``` szülőből származik. Ha a ```referenceKeyName``` nincs megadva, a szolgáltatás alapértelmezés szerint a szülőtől a értékre kerül ```generatedKeyName``` . 

A Power BI ezek a generált kulcsok a táblákon belüli kapcsolatok felderítésére támaszkodnak. Ha a gyermektábla oszlopát másképp kell elnevezni, állítsa be a ```referenceKeyName``` tulajdonságot a fölérendelt táblán. Az egyik példa az ```generatedKeyName``` as azonosító beállítása a pbiDocument táblában és a ```referenceKeyName``` as DocumentID. Ez azt eredményezi, hogy a pbiEntities és a pbiKeyPhrases tábla oszlopa a DocumentID nevű dokumentum AZONOSÍTÓját tartalmazza.

## <a name="projecting-to-objects"></a>Kivetítés az objektumokra

Az objektum-kivetítések nem rendelkeznek ugyanazokkal a korlátozásokkal, mint a tábla-kivetítések, és jobban illeszkednek a nagyméretű dokumentumok kivetítéséhez. Ebben a példában a teljes dokumentumot egy objektum-kivetítésként küldjük el. Az objektum-kivetítések egyetlen kivetítésre korlátozódnak egy tárolóban, és nem lehet szeletelt.

Az objektumok kivetítésének definiálásához használja a ```objects``` tömböt a vetítésekben. Létrehozhat egy új alakzatot a formázó eszközzel, vagy az objektum leképezésének beágyazott alakításával. Míg a táblák példája egy alakzat és egy szeletelés létrehozásának megközelítését szemlélteti, ez a példa a beágyazott alakítás használatát mutatja be. 

A beágyazott kialakítás lehetővé teszi, hogy új alakzatot hozzon létre a bemenetek definíciójában a kivetítéshez. A beágyazott kialakítás egy névtelen objektumot hoz létre, amely megegyezik azzal, amit egy formázó képesség előállít (esetünkben `pbiShape` ). A beágyazott formázás akkor hasznos, ha olyan alakzatot határoz meg, amelyet nem kíván használni.

A vetítések tulajdonság egy tömb. Ez a példa egy új leképezési példányt hoz létre a tömbhöz, ahol a knowledgeStore-definíció beágyazott kivetítéseket tartalmaz. A beágyazott kivetítések használatakor kihagyhatja a formázó képességet.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Kivetítés fájlba

A fájlok kivetítése a forrás dokumentumból kinyert vagy a dúsítási folyamatból kibontható alkoholtartalom-kimenetek. Az objektumok kivetítéséhez hasonló fájl-kivetítések az Azure Storage-ban blobként valósulnak meg, és tartalmazzák a rendszerképet. 

A fájlok leképezésének létrehozásához használja a `files` tömböt a leképezési objektumban. Ebben a példában a dokumentumból kinyert összes lemezképet egy nevű tárolóba tervezik `samplefile` .

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Kivetítés több típusra

Az összetettebb forgatókönyvek esetében előfordulhat, hogy a tartalom a leképezési típusok között szerepel. Ha például bizonyos adatmennyiségeket, például kulcsfontosságú kifejezéseket és entitásokat szeretne felvenni a táblákba, mentse a szöveg és az elrendezés szövegének OCR-eredményeit objektumként, majd a képeket fájlként adja ki. 

Ez a példa a következő változásokkal frissíti a készségkészlet:

1. Hozzon létre egy táblázatot az egyes dokumentumok sorával.
1. Hozzon létre egy táblázatot a táblázathoz kapcsolódó minden egyes, az ebben a táblázatban szereplő sorként azonosított kulcsszóval.
1. Hozzon létre egy táblázatot a táblázathoz kapcsolódóan minden olyan entitással, amely az ebben a táblázatban szereplő sorként van meghatározva.
1. Hozzon létre egy objektumot az elrendezés szövegével minden képhez.
1. Hozzon létre egy leképezést a kinyert képek kivetítéséhez.
1. Hozzon létre egy kereszthivatkozási táblázatot, amely a dokumentum táblára mutató hivatkozásokat tartalmaz, valamint az elrendezés szövegét és a fájl leképezését tartalmazó objektumot.

Ezeket a módosításokat a knowledgeStore-definíció lejjebb mutatja. 

### <a name="shape-data-for-cross-projection"></a>Alakzatadatok a több leképezéshez

A kivetítésekhez szükséges alakzatok beszerzéséhez hozzon létre egy új, formázó nevű objektumot, amely létrehoz egy nevű alakzatot `crossProjection` . 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Táblázat-, objektum-és fájl-kivetítések definiálása

A konszolidált crossProjection objektumból illessze be az objektumot több táblázatba, rögzítse az OCR kimenetet blobként, majd mentse a lemezképet fájlként (a blob Storage-ban is).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                    {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                ]
            }
        ]
    }
```

Az objektumok kivetítéséhez az egyes kivetítésekhez, az objektumok kivetítéséhez vagy a fájl-kivetítésekhez szükséges tároló neve nem osztható meg tárolóval. 

### <a name="relationships-among-table-object-and-file-projections"></a>Táblák, objektumok és a fájl-kivetítések közötti kapcsolatok

Ez a példa a kivetítések egy másik funkcióját is kiemeli. Ha egyazon kivetítési objektumon belül több típusú kivetítést határoz meg, akkor a és a különböző típusok (táblák, objektumok, fájlok) közötti kapcsolat van megadva. Ez lehetővé teszi, hogy a dokumentum egy táblázat sorával kezdjen, és megkeresse az adott dokumentumban található lemezképek összes OCR-szövegét az objektum kivetítése során. 

Ha nem szeretné, hogy a kapcsolódó adat, a különböző vetületi objektumokban adja meg a kivetítéseket. Az alábbi kódrészlet például a táblákat fogja eredményezni, a táblák és az objektum (OCR-szöveg) közötti kapcsolat nélkül. 

A kivetítési csoportok akkor hasznosak, ha ugyanazokat az információkat különböző alakzatokban szeretné felvenni a különböző igényekhez. Például egy kivetítési csoport a Power BI irányítópulthoz, valamint egy másik vetületi csoport a gépi tanulási modell egyéni szakértelembe való betanításához használt adatrögzítéshez.

A különböző típusú vetítések kiépítésekor először a fájl-és objektum-kivetítések jönnek létre, és az elérési utak hozzáadódnak a táblákhoz.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Gyakori problémák

A kivetítés meghatározásakor van néhány gyakori probléma, amely nem várt eredményeket okozhat. Ellenőrizze ezeket a problémákat, ha a tudásbázisbeli kimenet nem az, amit várt.

+ A karakterlánc-gazdagítás nem alakítható ki érvényes JSON-értékre. Ha a karakterláncok gazdagítva vannak, például a `merged_content` kulcsfontosságú kifejezésekkel gazdagítva, a dúsított tulajdonság a `merged_content` dúsítási fában lévő gyermekként jelenik meg. Az alapértelmezett ábrázolás nem megfelelően formázott JSON. A kivetítés időpontjában ügyeljen arra, hogy a dúsítást egy névvel és egy értékkel rendelkező érvényes JSON-objektumba alakítsa át.

+ A ```/*``` forrás elérési útjának végének kihagyása. Ha a kivetítés forrása `/document/pbiShape/keyPhrases` , a Key mondatok tömbje egyetlen objektumként/sorba van kiemelve. Ehelyett állítsa be úgy a forrás elérési útját, `/document/pbiShape/keyPhrases/*` hogy egyetlen sort vagy objektumot hozzon létre minden egyes kulcs kifejezéshez.

+ Elérésiút-szintaktikai hibák. Az elérésiút-választók megkülönböztetik a kis-és nagybetűket, és hiányzó bemeneti figyelmeztetéseket okozhatnak, ha nem a pontos esetet használja a választóhoz.

## <a name="next-steps"></a>Következő lépések

A jelen cikkben szereplő példák a kivetítések létrehozásának általános mintáit mutatják be. Most, hogy már jól ismeri a fogalmakat, jobban fel van szerelve, hogy kivetítéseket építsen ki az adott forgatókönyvhöz.

Az új funkciók megismerése érdekében vegye figyelembe a növekményes bővítést a következő lépésként. A növekményes bővítés a gyorsítótárazáson alapul, amely lehetővé teszi, hogy a készségkészlet módosítása által más módon nem érintett bővítéseket használja fel. Ez különösen olyan folyamatok esetében hasznos, amelyek OCR-t és képelemzést tartalmaznak.

> [!div class="nextstepaction"]
> [A növekményes gazdagodás és a gyorsítótárazás bemutatása](cognitive-search-incremental-indexing-conceptual.md)

A kivetítésekkel kapcsolatos további információkért tekintse meg a csoportok és a szeletelők funkcióit, valamint azt, hogy miként [definiálja őket egy készségkészlet](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Kivetítések a Knowledge Store-ban](knowledge-store-projection-overview.md)