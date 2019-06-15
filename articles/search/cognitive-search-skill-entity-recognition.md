---
title: Entitások felismerése cognitive search szakértelem – Azure Search
description: Eltérő típusú entitásokat kigyűjtése a szöveg Azure Search-kognitív keresés folyamatban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f05161dbbfd9293cd7b1cbf447bb7ca1c313250c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65023445"
---
#    <a name="entity-recognition-cognitive-skill"></a>Entitások felismerése cognitive szakértelem

A **entitások felismerése** szakértelem különböző típusú entitás kigyűjti a szöveget. Ezen a képzettségi használja a gépi tanulási modellek által biztosított [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) a Cognitive Services.

> [!NOTE]
> Bontsa ki a hatókört által a feldolgozás, gyakoriságának növelése további dokumentumok hozzáadása, vagy adja hozzá a további AI-algoritmusokat, kell [számlázható Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md). A díjakat API-k hívásakor, a Cognitive Services, valamint a lemezkép kinyerése a az Azure Search-dokumentumfeltörést fázis részeként. Nem számítunk fel díjat a szövegkinyerés dokumentumok közül.
>
> Végrehajtási beépített képességek a meglévő díjakat [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/). A kép kinyerési díjszabás leírása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
Egy rekord maximális mérete 50 000 karakter által mért kell lennie `String.Length`. Ha az adatok tördelésével, mielőtt elküldené a kulcskifejezések információkinyerő van szüksége, fontolja meg a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Ismeretek paraméterek

Paraméterek kis-és nagybetűket, és minden megadása nem kötelező.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | Ki kell nyerni kategóriákat tömbje.  Lehetséges kategória típusok: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Ha nincs kategória áll rendelkezésre, a rendszer minden adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvkódja. A következő nyelvek támogatottak: `de, en, es, fr, it`|
|minimumPrecision | Nem használt. Jövőbeli használatra fenntartva. |
|includeTypelessEntities | Ha a beállítás igaz értékre, ha a szöveg egy jól ismert entitást tartalmaz, de nem egy támogatott kategóriák osztályozhatók, azt fogja visszaadni részeként a `"entities"` összetett kimeneti mező. 
Ezek a jól ismert, de a jelenlegi támogatott "kategóriák" részeként nem besorolt entitásokon. "Windows 10-es" például egy jól ismert entitás (termék), de "Termék" nem szerepelnek a jelenleg támogatott kategóriák. Alapértelmezett érték `false` |


## <a name="skill-inputs"></a>Ismeretek bemenetek

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett szint a `"en"`.  |
| szöveg          | Az elemzendő szöveg.          |

## <a name="skill-outputs"></a>Ismeretek kimenetek

> [!NOTE]
> Nem minden entitás kategória minden nyelv esetében támogatottak. Csak _en_, _es_ kivonása támogatja `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` típusokat.

| Kimeneti név     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Egy karakterlánctömb, ahol minden egyes karakterlánc egy személy nevét jelöli. |
| locations  | Egy karakterlánctömb, ahol az egyes sztringek jelenti egy helyre. |
| organizations  | Ahol az egyes sztringek jelenti egy szervezet karakterláncok tömbje. |
| mennyiség  | Egy karakterlánctömb, ahol az egyes sztringek jelenti mennyiséget. |
| Időpontok  | Egy karakterlánctömb, ahol minden karakterláncot jelenti-e a dátum és idő (ahogyan az a szöveg látható) értéket. |
| URL-címek | Egy karakterlánctömb, ahol az egyes sztringek jelenti egy URL-cím |
| e-mailek | Egy karakterlánctömb, ahol az egyes sztringek jelenti egy e-mailt |
| namedEntities | Egy tömb, összetett típusok, amely a következő mezőket tartalmazzák: <ul><li>category</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (a hely hol található a szövegben)</li><li>megbízhatósági (egyelőre nem használt. A-1 értékre lesz beállítva)</li></ul> |
| Entitások | Egy tömb, amely a kinyert szöveget, a következő mezőkkel entitásokkal kapcsolatos részletes információkat tartalmaz, összetett típusok <ul><li> név (a tényleges entitás neve. Ez határozza meg a "normalized" űrlap)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (Wikipédia-oldal az entitás mutató)</li><li>bingId</li><li>(az entitás elismert kategóriáját) típusa</li><li>altípusa (csak az adott kategóriába számára elérhető, ezáltal egy részletesebb nézet typu entity)</li><li> egyezések (összetett tartalmazó gyűjteményhez)<ul><li>szöveg (a nyers szöveg az entitás)</li><li>eltolás (a hely hol található)</li><li>hossza (a nyers entitás szöveg hossza)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Minta-definíció

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##  <a name="sample-input"></a>Minta beviteli

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Hibák esetén
A dokumentum a nyelvkód nem támogatott, ha hibát ad vissza, és nincsenek entitások ki kell olvasni.

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
