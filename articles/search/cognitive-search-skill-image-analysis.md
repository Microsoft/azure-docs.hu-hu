---
title: Képelemzés – kognitív képesség
titleSuffix: Azure Cognitive Search
description: Szemantikai szöveg kinyerése képelemzéssel a képelemzés kognitív képességgel az Azure Cognitive Search mesterséges intelligencia-gazdagító folyamatában.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 69b84a3edb606ed99b6aaca7db5ad0e57124f1b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91948935"
---
# <a name="image-analysis-cognitive-skill"></a>Képelemzés – kognitív képesség

A **képelemzési** képesség a vizualizációs funkciók gazdag készletét Kinyeri a kép tartalma alapján. Létrehozhat például egy képfeliratot egy képből, létrehozhat címkéket, vagy azonosíthatja a hírességeket és a tereptárgyait. Ez a képesség a Cognitive Services [Computer Vision](../cognitive-services/computer-vision/overview.md) által biztosított gépi tanulási modelleket használja. 

> [!NOTE]
> A kis méretű kötetek (20 tranzakció alatt) ingyenesen végrehajthatók az Azure Cognitive Searchban, de a nagyobb munkaterhelésekhez [számlázható Cognitive Services erőforrást kell csatolni](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. vízió. ImageAnalysisSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| `defaultLanguageCode` |  A visszaadni kívánt nyelvet jelző sztring. A szolgáltatás egy megadott nyelven adja vissza az elismerés eredményét. Ha a paraméter nincs megadva, az alapértelmezett érték az "en". <br/><br/>A támogatott nyelvek a következők: <br/>*hu* – angol (alapértelmezett) <br/> *es* – spanyol <br/> *ja* – Japán <br/> *PT* – portugál <br/> *zh* -egyszerűsített kínai|
| `visualFeatures` |    Karakterláncok tömbje, amely a vizuális szolgáltatások visszatérési típusait jelzi. A vizuális funkciók érvényes típusai a következők:  <ul><li>*felnőtt* – észleli, ha a rendszerkép a természetben (meztelenség vagy szexuális cselekmény ábrázolása), vagy véres (a szélsőséges erőszakot vagy vért ábrázol). A rendszer a szexuálisan szuggesztív tartalmat (más néven a zamatos tartalmat) is észleli.</li><li>*márkák* – különböző márkákat észlel egy képen belül, beleértve a hozzávetőleges helyet is. A *Brands* vizualizáció funkció csak angol nyelven érhető el.</li><li> *Kategóriák* – a rendszerképek tartalmának kategorizálása a Cognitive Services [Computer Vision dokumentációjában](../cognitive-services/computer-vision/category-taxonomy.md)meghatározott besorolásnak megfelelően. </li><li>*Leírás* – a képek tartalmának teljes mondatát írja le a támogatott nyelveken.</li><li>*Faces* – észleli, hogy vannak-e arcok. Ha van, a a koordinátákat, a nemeket és a kort hozza létre.</li><li>  *objektumok* – a képen belül különböző objektumokat észlel, beleértve a hozzávetőleges helyet is. Az *objektumok* vizualizáció funkció csak angol nyelven érhető el.</li><li> *címkék* – a képet a képtartalommal kapcsolatos szavak részletes listájával címkézheti.</li></ul> A vizualizációs funkciók nevei megkülönböztetik a kis-és nagybetűket. Vegye figyelembe, hogy a *színes* és a *imageType* vizualizációs funkciók elavultak, de ez a funkció továbbra is elérhető egy [Egyéni képességgel](./cognitive-search-custom-skill-interface.md).|
| `details` | Karakterláncok tömbje, amely azt jelzi, hogy melyik tartományra vonatkozó adatokat kell visszaadnia. A vizuális funkciók érvényes típusai a következők: <ul><li>*hírességek* – azonosítja a hírességeket, ha a rendszerkép észleli őket.</li><li>*tereptárgyak* – a rendszerképben észlelt tereptárgyak azonosítására szolgál. </li></ul> |

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Description                                          |
|---------------|------------------------------------------------------|
| `image`         | Összetett típus. A jelenleg csak az Azure Blob indexelő által létrehozott "/Document/normalized_images" mezővel működik, ha a értéke nem a (z ```imageAction``` ) értékre van állítva ```none``` . További információért tekintse meg a [mintát](#sample-output) .|



##  <a name="sample-skill-definition"></a>Példa a szaktudás meghatározására

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "tags",
                "categories",
                "description",
                "faces",
                "brands"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                },
                {
                    "name": "brands"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>Minta index (csak a kategóriák, a leírás, az arcok és a címkék mezőihez)
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>Minta kimeneti mező leképezése (a fenti indexhez)
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/brands/*/name",
            "targetFieldName": "brands"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>A kimeneti mezők leképezésének variációja (beágyazott tulajdonságok)

Az alacsonyabb szintű tulajdonságokhoz (például tereptárgyak vagy hírességek) is meghatározhat kimeneti mező-hozzárendeléseket. Ebben az esetben győződjön meg arról, hogy az index sémája tartalmaz egy mezőt, amely kifejezetten a tereptárgyak adatait tartalmazza.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>Példabemenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Példakimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "categories": [
          {
            "name": "abstract_",
            "score": 0.00390625
          },
          {
            "name": "people_",
            "score": 0.83984375,
            "detail": {
              "celebrities": [
                {
                  "name": "Satya Nadella",
                  "faceBoundingBox": [
                        {
                            "x": 273,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 309
                        },
                        {
                            "x": 395,
                            "y": 431
                        },
                        {
                            "x": 273,
                            "y": 431
                        }
                    ],
                  "confidence": 0.999028444
                }
              ],
              "landmarks": [
                {
                  "name": "Forbidden City",
                  "confidence": 0.9978346
                }
              ]
            }
          }
        ],
        "adult": {
          "isAdultContent": false,
          "isRacyContent": false,
          "isGoryContent": false,
          "adultScore": 0.0934349000453949,
          "racyScore": 0.068613491952419281,
          "goreScore": 0.08928389008070282
        },
        "tags": [
          {
            "name": "person",
            "confidence": 0.98979085683822632
          },
          {
            "name": "man",
            "confidence": 0.94493889808654785
          },
          {
            "name": "outdoor",
            "confidence": 0.938492476940155
          },
          {
            "name": "window",
            "confidence": 0.89513939619064331
          }
        ],
        "description": {
          "tags": [
            "person",
            "man",
            "outdoor",
            "window",
            "glasses"
          ],
          "captions": [
            {
              "text": "Satya Nadella sitting on a bench",
              "confidence": 0.48293603002174407
            }
          ]
        },
        "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
        "metadata": {
          "width": 1500,
          "height": 1000,
          "format": "Jpeg"
        },
        "faces": [
          {
            "age": 44,
            "gender": "Male",
            "faceBoundingBox": [
                {
                    "x": 1601,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 395
                },
                {
                    "x": 1653,
                    "y": 447
                },
                {
                    "x": 1601,
                    "y": 447
                }
            ]
          }
        ],
        "objects": [
          {
            "rectangle": {
              "x": 25,
              "y": 43,
              "w": 172,
              "h": 140
            },
            "object": "person",
            "confidence": 0.931
          }
        ],
        "brands":[  
           {  
              "name":"Microsoft",
              "confidence": 0.903,
              "rectangle":{  
                 "x":20,
                 "y":97,
                 "w":62,
                 "h":52
              }
           }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Hibák esetei
A következő hiba esetekben egyetlen elem sincs kibontva.

| Hibakód | Description |
|------------|-------------|
| `NotSupportedLanguage` | A megadott nyelv nem támogatott. |
| `InvalidImageUrl` | A képurl-cím helytelen formátumú vagy nem érhető el.|
| `InvalidImageFormat` | A bemeneti adatok nem érvényes rendszerkép. |
| `InvalidImageSize` | A bemeneti rendszerkép túl nagy. |
| `NotSupportedVisualFeature`  | A megadott szolgáltatástípus érvénytelen. |
| `NotSupportedImage` | Nem támogatott rendszerkép, például gyermekpornográfia. |
| `InvalidDetails` | A tartományhoz tartozó modell nem támogatott. |

Ha a következőhöz hasonló hibaüzenetet kap `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"` , ellenőrizze az elérési utat. A hírességek és a tereptárgyak is a tulajdonságok alatt találhatók `detail` .

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](/rest/api/searchservice/create-indexer)