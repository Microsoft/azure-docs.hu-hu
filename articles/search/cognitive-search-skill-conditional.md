---
title: Feltételes kognitív képességek
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search feltételes képességei lehetővé teszik a szűrést, az Alapértelmezések létrehozását és az értékek egyesítését egy készségkészlet-definícióban.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f47ca56fa1b40422edeb0d4e11c24be6f60e49e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666370"
---
# <a name="conditional-cognitive-skill"></a>Feltételes kognitív képességek

A **feltételes** képesség lehetővé teszi, hogy az Azure Cognitive Search olyan forgatókönyvek, amelyek Boolean műveletet igényelnek a kimenethez rendelendő adatok meghatározásához. Ilyen forgatókönyvek például a szűrés, az alapértelmezett érték kiosztása és az adatok egyesítése egy adott feltétel alapján.

A következő pseudocode bemutatja, hogy a feltételes képességek hogyan valósulnak meg:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Ez a képesség nem egy Azure Cognitive Services API-hoz van kötve, és nem számítunk fel díjat a használatára. Azonban továbbra is [csatolnia kell egy Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md) az "ingyenes" erőforrás-beállítás felülbírálásához, amely korlátozza a naponta kis mennyiségű dúsítást.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. util. ConditionalSkill


## <a name="evaluated-fields"></a>Kiértékelt mezők

Ez a képesség speciális, mert a hozzá tartozó bemenetek kiértékelése mezők.

A következő elemek egy kifejezés érvényes értékei:

-   A jegyzet elérési útjai (a kifejezésekben szereplő elérési utakat "$ (" és ")" karakterrel kell tagolni)
 <br/>
    Angol nyelvű Példák:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literálok (karakterláncok, számok, igaz, hamis, null) <br/>
    Angol nyelvű Példák:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Összehasonlító operátorokat használó kifejezések (= =,! =, >=, >, <=, <) <br/>
    Angol nyelvű Példák:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Logikai operátorokat használó kifejezések (&&, | |,!, ^) <br/>
    Angol nyelvű Példák:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Numerikus operátorokat használó kifejezések (+,-, \* ,/,%) <br/>
    Angol nyelvű Példák: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Mivel a feltételes képesség támogatja a kiértékelést, ezt kisebb átalakítási helyzetekben is használhatja. Lásd például a [4. szakértelem definícióját](#transformation-example).

## <a name="skill-inputs"></a>Szaktudás bemenetei
A bemenetek megkülönböztetik a kis-és nagybetűket.

| Bevitel   | Leírás |
|-------------|-------------|
| feltétel   | Ez a bemenet egy [kiértékelt mező](#evaluated-fields) , amely az értékelendő feltételt jelöli. Ezt az állapotot egy logikai értékre (*true* vagy *false*) kell kiértékelni.   <br/>  Angol nyelvű Példák: <br/> "= true" <br/> "= $ (/Document/Language) = =" FR " <br/> "= $ (/Document/Pages/ \* /Language) = = $ (/Document/expectedLanguage)" <br/> |
| whenTrue    | Ez a bemenet egy [kiértékelt mező](#evaluated-fields) , amely a visszatérési értéket jelöli, ha a feltétel *igaz* értékre van kiértékelve. Az állandó sztringeket szimpla idézőjelek ("és") szerint kell megadni. <br/>Mintavételezési értékek: <br/> "=" szerződés ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/> |
| whenFalse   | Ez a bemenet egy [kiértékelt mező](#evaluated-fields) , amely a visszatérési értéket jelöli, ha a feltétel *hamis* értékre van kiértékelve. <br/>Mintavételezési értékek: <br/> "=" szerződés ""<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/>

## <a name="skill-outputs"></a>Szaktudás kimenetei
Egyetlen kimenetre van szükség, amelyet egyszerűen "output"-nek nevezünk. A *whenFalse* értéket adja vissza, ha a feltétel hamis vagy *whenTrue* , ha a feltétel igaz.

## <a name="examples"></a>Példák

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>1. példa: szakértelem meghatározása: dokumentumok szűrése csak francia dokumentumok visszaküldéséhez

A következő kimenet a mondatok ("/document/frenchSentences") tömbjét adja vissza, ha a dokumentum nyelve francia. Ha a nyelv nem francia, az érték *NULL értékűre* van állítva.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Ha a "/document/frenchSentences" egy másik képesség *kontextusában* van használatban, ez a képesség csak akkor fut, ha a "/Document/frenchSentences" tulajdonság értéke nem *Null*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>2. minta-definíció: a nem létező érték alapértelmezett értékének beállítása

A következő kimenet létrehoz egy jegyzetet ("/document/languageWithDefault"), amely a dokumentum nyelvére vagy "es" értékre van beállítva, ha nincs beállítva a nyelv.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Példa a skill definition 3: értékek egyesítése két mezőből egyetlenre

Ebben a példában néhány mondatnak van egy *frenchSentiment* tulajdonsága. Ha a *frenchSentiment* tulajdonság értéke null, a *englishSentiment* értéket szeretnénk használni. A kimenetet egy " *hangulat* " ("/Document/sentences/*/Sentiment") nevű tag számára rendeljük hozzá.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Transzformációs példa
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>4. minta: az adatátalakítás egyetlen mezőben

Ebben a példában egy 0 és 1 közötti *érzést* kapunk. Azt szeretnénk, hogy az-1 és 1 közötti értékre alakítsa át. Ezt a kisebb átalakítást a feltételes képesség használatával teheti meg.

Ebben a példában nem használjuk a szakértelem feltételes aspektusát, mert a feltétel mindig *igaz*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Különleges szempontok
Bizonyos paraméterek kiértékelése megtörténik, ezért különösen körültekintően kell követnie a dokumentált mintát. A kifejezéseknek egyenlő jellel kell kezdődnie. Az elérési utat "$ (" és ")" karakterrel kell elválasztani. Ügyeljen arra, hogy a sztringeket szimpla idézőjelek között helyezze el. Ez segíti az értékelést a karakterláncok és a tényleges elérési utak és operátorok közötti különbségtételben. Győződjön meg arról is, hogy az operátorok körül a szóköz (például egy "*", egy elérési út esetében pedig a szorzás) érték van.


## <a name="next-steps"></a>Következő lépések

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
