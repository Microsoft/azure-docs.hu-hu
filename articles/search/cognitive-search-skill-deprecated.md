---
title: Elavult kognitív képességek
titleSuffix: Azure Cognitive Search
description: Ez az oldal az elavultnak tekintett kognitív képességek listáját tartalmazza, és az Azure Cognitive Search szakértelmével közeljövőben nem lesz támogatva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936095"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Elavult kognitív képességek az Azure-ban Cognitive Search

Ez a dokumentum az elavultnak tekintett kognitív képességeket ismerteti. Használja a következő útmutatót a tartalomhoz:

* Szaktudás neve: az elavult képesség neve, amely az attribútumhoz lesz leképezve @odata.type .
* Utolsó elérhető API-verzió: az Azure Cognitive Search nyilvános API utolsó verziója, amely a megfelelő elavult képességet tartalmazó szakértelmével hozható létre/frissíthető.
* Támogatás befejezése: az utolsó nap, amely után a megfelelő képesség nem támogatottnak minősül. A korábban létrehozott szakértelmével továbbra is működőképesnek kell lennie, de a felhasználókat ajánlott áttelepíteni egy elavult képességből.
* Javaslatok: az áttelepítési útvonal egy támogatott képesség használatára való továbbításra szolgál. A felhasználóknak javasoljuk, hogy kövessék az ajánlásokat, hogy továbbra is megkapják a támogatást.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft. Skills. Text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Utolsó elérhető API-verzió

2017. 11. 11. dátumú előzetes verzió

### <a name="end-of-support"></a>Támogatás vége

2019. február 15.

### <a name="recommendations"></a>Javaslatok 

Használja helyette a [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . A NamedEntityRecognitionSkill legtöbb funkciója magasabb színvonalú. Emellett a komplex kimeneti mezőiben is gazdagabb információk szerepelnek.

Az [entitás-felismerési szakértelemre](cognitive-search-skill-entity-recognition.md)való áttelepítéshez az alábbi módosítások közül egyet vagy többet kell végrehajtania a szaktudás definíciójában. A skill definition [Update KÉSZSÉGKÉSZLET API](/rest/api/searchservice/update-skillset)használatával frissítheti.

> [!NOTE]
> A megbízhatósági pontszám jelenleg nem támogatott. A `minimumPrecision` paraméter a `EntityRecognitionSkill` későbbi használatra és a visszamenőleges kompatibilitás érdekében létezik.

1. *(Kötelező)* Módosítsa a `@odata.type` elemet a következőre: `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` `"#Microsoft.Skills.Text.EntityRecognitionSkill"` .

2. *(Nem kötelező)* Ha a `entities` kimenetet használja, használja `namedEntities` inkább az összetett gyűjtemény kimenetét `EntityRecognitionSkill` . A `targetName` skill Definition (a) használatával leképezheti a nevű jegyzetet `entities` .

3. *(Nem kötelező)* Ha nem adja meg explicit módon a `categories` -t, a `EntityRecognitionSkill` képes különböző típusú kategóriákat visszaadni a által támogatottak mellett `NamedEntityRecognitionSkill` . Ha ez a viselkedés nem kívánatos, ügyeljen arra, hogy explicit módon állítsa be a paramétert a következőre: `categories` `["Person", "Location", "Organization"]` .

    _Példa áttelepítési definíciók_

    * Egyszerű áttelepítés

        _Előtt NamedEntityRecognition-definíció_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _Után EntityRecognition-definíció_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Kis mértékben bonyolult áttelepítés

        _Előtt NamedEntityRecognition-definíció_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _Után EntityRecognition-definíció_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md)