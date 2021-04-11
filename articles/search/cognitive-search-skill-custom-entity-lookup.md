---
title: Egyéni entitás keresési kognitív keresési képességei
titleSuffix: Azure Cognitive Search
description: Kinyerheti a különböző egyéni entitásokat egy Azure Cognitive Search kognitív keresési folyamat szövege alapján. Ez a képesség jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 0f233bdff54822037d15fde9ac62e34193794ad3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110308"
---
#     <a name="custom-entity-lookup-cognitive-skill"></a>Egyéni entitások keresése – kognitív képesség

Az **egyéni entitás keresési** képessége szövegeket keres a szavak és kifejezések egyéni, felhasználó által definiált listájából. Ezzel a listával minden olyan dokumentumot felcímkéz, amely minden egyező entitással rendelkezik. A képesség emellett olyan zavaros egyezést is támogat, amely a hasonló, de nem pontos egyezések keresésére is alkalmazható.  

Ez a képesség nem kötődik Cognitive Services API-hoz, és az előzetes verzió időtartama alatt díjmentesen használható. A napi alkoholtartalom-növelési korlát felülbírálásához azonban továbbra is [csatolni kell egy Cognitive Services erőforrást](./cognitive-search-attach-cognitive-services.md). A napi korlát arra az esetre vonatkozik, ha az Azure Cognitive Search keresztül éri el Cognitive Services ingyenes hozzáférését.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Adatkorlátok
+ A maximálisan támogatott bemeneti rekordok mérete 256 MB. Ha meg kell szüntetnie az adatokat, mielőtt elküldené az egyéni entitás keresési képességeinek, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.
+ A maximális entitások definíciós táblázata 10 MB, ha a *entitiesDefinitionUri* paraméter használatával van megadva. 
+ Ha az entitások beágyazottként vannak definiálva, a *inlineEntitiesDefinition* paraméter használatával a maximálisan támogatott méret 10 kb.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| `entitiesDefinitionUri`    | Egy olyan JSON-vagy CSV-fájl elérési útja, amely az összes célként megadott szöveget tartalmazza. Az entitás definíciója az indexelő futtatásának elején olvasható. a fájlhoz tartozó összes frissítés nem lesz megvalósítva, amíg az újabb futtatások nem futnak. A konfigurációnak HTTPS protokollon keresztül kell elérhetőnek lennie. A várt CSV-vagy JSON-sémához lásd alább az [egyéni entitás-definíció](#custom-entity-definition-format) formátumát.|
|`inlineEntitiesDefinition` | Beágyazott JSON-entitások definíciói. Ez a paraméter felülírja a entitiesDefinitionUri paramétert, ha van ilyen. Legfeljebb 10 KB konfigurációt lehet megadni. A várt JSON-sémáért lásd alább az [egyéni entitások definícióját](#custom-entity-definition-format) . |
|`defaultLanguageCode` |    Választható A bemeneti szöveg tokenize és körülhatárolása során használt szövegbeviteli szöveg nyelvi kódja. A következő nyelvek támogatottak: `da, de, en, es, fi, fr, it, ko, pt` . Az alapértelmezett érték az angol ( `en` ). Ha languagecode-országhívószám formátumot továbbít, a rendszer csak a formátum languagecode-részét használja.  |
|`globalDefaultCaseSensitive` | Választható A szakértelem alapértelmezett kis-és nagybetűs értéke. Ha `defaultCaseSensitive` nincs megadva egy entitás értéke, ez az érték lesz az `defaultCaseSensitive` adott entitás értéke. |
|`globalDefaultAccentSensitive` | Választható A szakértelem alapértelmezett ékezetes értéke. Ha `defaultAccentSensitive` nincs megadva egy entitás értéke, ez az érték lesz az `defaultAccentSensitive` adott entitás értéke. |
|`globalDefaultFuzzyEditDistance` | Választható A szakértelem alapértelmezett fuzzy szerkesztési távolságának értéke. Ha `defaultFuzzyEditDistance` nincs megadva egy entitás értéke, ez az érték lesz az `defaultFuzzyEditDistance` adott entitás értéke. |

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| `text`          | Az elemezni kívánt szöveg.          |
| `languageCode`    | Választható. Az alapértelmezett szint a `"en"`.  |


## <a name="skill-outputs"></a>Szaktudás kimenetei


| Kimenet neve      | Leírás                   |
|---------------|-------------------------------|
| `entities` | Olyan objektumok tömbje, amelyek tartalmazzák a talált egyezésekre vonatkozó információkat, valamint a kapcsolódó metaadatokat. Az azonosított entitások mindegyike a következő mezőket tartalmazza:  <ul> <li> *Name (név*): a legfelső szintű entitás azonosítva. Az entitás a "normalizált" űrlapot jelöli. </li> <li> *azonosító*: az entitás egyedi azonosítója, amelyet a felhasználó határoz meg az "egyéni entitás definíciójának formátuma" formátumban.</li> <li> *Leírás*: az entitások leírása a felhasználó által az "egyéni entitás definíciójának formátuma" kifejezésben meghatározott módon. </li> <li> *írja be a következőt:* Az entitás típusa, amelyet a felhasználó határoz meg az "egyéni entitás definíciójának formátuma" formátumban.</li> <li> *altípus:* Az entitás altípusa a felhasználó által az "egyéni entitás definíciójának formátuma" beállításban meghatározott.</li>  <li> *egyezések*: gyűjtemény, amely leírja az adott entitáshoz tartozó összes egyezést a forrás szövegén. Minden egyezés a következő tagokkal fog rendelkezni: </li> <ul> <li> *text (szöveg*): a forrás dokumentum szövege megegyezik a nyers szöveggel. </li> <li> *eltolás*: az a hely, ahol a egyezés megtalálható a szövegben. </li> <li> *length (hossz*): az egyező szöveg hossza. </li> <li> *matchDistance*: a megfeleltetéstől eltérő karakterek száma az eredeti entitás neve vagy aliasa volt.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Egyéni entitás definíciós formátuma

Az egyéni entitások keresési képességeinek megjelenítéséhez 3 különböző módszer áll rendelkezésre. Megadhatja a listát a alkalmazásban. CSV-fájl, a. JSON-fájl vagy beágyazott definícióként a szaktudás definíciójának részeként.  

Ha a definíciós fájl egy. CSV vagy. JSON-fájl, a fájl elérési útját kell megadni a *entitiesDefinitionUri* paraméter részeként. Ebben az esetben a fájl minden indexelő futtatásának elején le lesz töltve. A fájlnak elérhetőnek kell lennie, feltéve, hogy az indexelő futtatni kívánja. Emellett a fájlnak UTF-8 kódolással kell rendelkeznie.

Ha a definíciót beágyazottként adták meg, akkor azt a *inlineEntitiesDefinition* skill paraméter tartalmának megfelelően kell megadni. 

### <a name="csv-format"></a>CSV-formátum

Megadhatja az egyéni entitások definícióját Comma-Separated érték (CSV) fájlban való kereséshez, ha megadja a fájl elérési útját, és beállítja azt a *entitiesDefinitionUri*  skill paraméterben. Az elérési útnak HTTPS-helyen kell lennie. A definíciós fájl mérete legfeljebb 10 MB lehet.

A CSV formátum egyszerű. Minden sor egy egyedi entitást képvisel, ahogy az alábbi ábrán látható:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

Ebben az esetben három entitást lehet visszaadni, mint a talált entitások (Bill Gates, a Microsoft), de azonosítva lesznek, ha a (z) sorban (aliasok) található feltételek bármelyike megfelel a szövegnek. Ha például a "William H. Gates" karakterlánc szerepel a dokumentumban, a rendszer a "Bill Gates" entitáshoz tartozó egyezést adja vissza.

### <a name="json-format"></a>JSON formátum

Megadhatja a JSON-fájlokban megkeresni kívánt egyéni entitások definícióját is. A JSON formátuma valamivel nagyobb rugalmasságot biztosít, mivel lehetővé teszi az egyeztetési szabályok megadását a feltételek szerint. Például megadhatja az egyes feltételekhez tartozó fuzzy megfeleltetési távolságot (Damerau-Levenshtein távolságot), illetve azt, hogy a megfeleltetés megkülönbözteti-e a kis-és nagybetűket. 

 Akárcsak a CSV-fájlokhoz, meg kell adnia a JSON-fájl elérési útját, és be kell állítania a *entitiesDefinitionUri* skill paraméterben. Az elérési útnak HTTPS-helyen kell lennie. A definíciós fájl mérete legfeljebb 10 MB lehet.

Az alapszintű JSON egyéni entitások listájának definíciója az egyeztetendő entitások listája lehet:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

A JSON-definíció összetettebb példája opcionálisan megadhatja az egyes entitások azonosítóját, leírását, típusát és altípusát, valamint más *aliasokat* is. Ha egy alias-kifejezés egyezik, a rendszer az entitást is visszaadja:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Az alábbi táblázatok részletesen ismertetik a különböző konfigurációs paramétereket, amelyekkel meghatározhatja az entitások egyeztetését:

|  Mező neve  |        Leírás  |
|--------------|----------------------|
| `name` | A legfelső szintű entitás leírója A képzettségi kimenetben szereplő egyezések ezen név szerint vannak csoportosítva, és a talált szöveg "normalizált" formáját jelölik.  |
| `description`  | Választható Ez a mező a megfeleltetett szöveg (ek) egyéni metaadatait továbbítóként is használható. Ennek a mezőnek az értéke megjelenik a képzettségi kimenetben lévő entitás minden egyezésével. |
| `type` | Választható Ez a mező a megfeleltetett szöveg (ek) egyéni metaadatait továbbítóként is használható. Ennek a mezőnek az értéke megjelenik a képzettségi kimenetben lévő entitás minden egyezésével. |
| `subtype` | Választható Ez a mező a megfeleltetett szöveg (ek) egyéni metaadatait továbbítóként is használható. Ennek a mezőnek az értéke megjelenik a képzettségi kimenetben lévő entitás minden egyezésével. |
| `id` | Választható Ez a mező a megfeleltetett szöveg (ek) egyéni metaadatait továbbítóként is használható. Ennek a mezőnek az értéke megjelenik a képzettségi kimenetben lévő entitás minden egyezésével. |
| `caseSensitive` | Választható Az alapértelmezett érték a false. Logikai érték, amely azt jelzi, hogy az entitás nevével való összehasonlításnak érzékenynek kell lennie a karakterkészletre. Példa a kis-és nagybetűket megkülönböztető "Microsoft" találatokra: Microsoft, microSoft, MICROSOFT |
| `accentSensitive` | Választható Az alapértelmezett érték a false. Logikai érték, amely azt jelzi, hogy az ékezetes és az ékezetes karakterek, például az "é" és az "e" azonosak-e. |
| `fuzzyEditDistance` | Választható Az alapértelmezett érték 0. A maximális érték 5. Annak az eltérő karaktereknek az elfogadható számát jelöli, amelyek továbbra is egyeznek az entitás nevével. A rendszer a megadott egyezések legkisebb lehetséges bizonytalanság adja vissza.  Ha például a szerkesztési távolság értéke 3, a "Windows 10" továbbra is egyezik a "Windows", a "Windows10" és a "Windows 7" értékkel. <br/> Ha a kis-és nagybetűk megkülönböztetése hamis értékre van állítva, a kis-és nagybetűk nem számítanak bele a bizonytalanság-tűrésbe, de máskülönben |
| `defaultCaseSensitive` | Választható Az entitás alapértelmezett érzékenységi értékének módosítása. Az összes alias caseSensitive alapértelmezett értékének módosítására használható. |
| `defaultAccentSensitive` | Választható Megváltoztatja az entitás alapértelmezett ékezetes érzékenységi értékét. Az összes alias accentSensitive alapértelmezett értékének módosítására használható.|
| `defaultFuzzyEditDistance` | Választható Megváltoztatja az entitás alapértelmezett homályos szerkesztési távolságának értékét. Az összes alias fuzzyEditDistance alapértelmezett értékének módosítására használható. |
| `aliases` | Választható Összetett objektumok tömbje, amellyel alternatív helyesírásokat vagy szinonimákat adhat meg a gyökér entitás nevéhez. |

| Alias tulajdonságai | Leírás |
|------------------|-------------|
| `text`  | Egy megcélzott entitás nevének alternatív helyesírása vagy ábrázolása.  |
| `caseSensitive` | Választható Ugyanaz, mint a legfelső szintű "caseSensitive" paraméter, de csak erre az aliasra vonatkozik. |
| `accentSensitive` | Választható Ugyanaz, mint a legfelső szintű "accentSensitive" paraméter, de csak erre az aliasra vonatkozik. |
| `fuzzyEditDistance` | Választható Ugyanaz, mint a legfelső szintű "fuzzyEditDistance" paraméter, de csak erre az aliasra vonatkozik. |


### <a name="inline-format"></a>Beágyazott formátum

Bizonyos esetekben érdemes lehet megadnia az egyéni entitások listáját, amelyek közvetlenül a szakértelem-definícióba illeszkednek. Ebben az esetben a fentiekben ismertetett módon hasonló JSON-formátumot használhat, de a szaktudás definíciójában szerepel.
Csak a 10 KB-nál kevesebb konfiguráció (szerializált méret) lehet definiálva. 

##    <a name="sample-definition"></a>Minta definíciója

Az alábbi példa egy beágyazott formátumot használó minta-képzettségi definíciót mutat be:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Ha úgy dönt, hogy az entitások definíciós fájljára mutató mutatót ad meg, akkor az alábbi formátumot használó minta-képzettségi definíció `entitiesDefinitionUri` látható:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Példabemenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Példakimenet

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Figyelmeztetés: elérte a maximális kapacitást a találatok számára, kihagyva az összes további ismétlődő egyezést.

Ez a figyelmeztetés akkor jelenik meg, ha az észlelt egyezések száma meghaladja a maximálisan megengedettet. Ebben az esetben az ismétlődő egyezéseket is leállítjuk. Ha ez nem fogadható el, küldjön egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) , hogy segítséget nyújtson az egyéni használati esetekhez.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Entitás-felismerési szakértelem (a jól ismert entitások kereséséhez)](cognitive-search-skill-entity-recognition.md)
