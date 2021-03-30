---
title: Képességcsoport létrehozása
titleSuffix: Azure Cognitive Search
description: Meghatározhatja az adatok kinyerését, a természetes nyelvi feldolgozást vagy a képelemzési lépéseket az adatokból az Azure-Cognitive Search való használatra szánt strukturált adatok dúsításához és kinyeréséhez.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 39a7c92ca6c83684658cf767722698806ed994ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935449"
---
# <a name="how-to-create-a-skillset-in-an-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Készségkészlet létrehozása AI-bővítési folyamatokban az Azure-ban Cognitive Search 

![indexelő szakaszai](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "indexelő szakaszai")

A készségkészlet meghatározza azokat a műveleteket, amelyek kinyerik és gazdagítják az adatok kereshetővé tételét. A készségkészlet a szöveg és a képek tartalmának kibontása a forrás dokumentumaiból történik, és a forrásdokumentum bármely mezője (opcionálisan) egy indexben vagy egy tudásbázisban található célhelyre van leképezve.

A készségkészlet egy vagy több olyan *kognitív szaktudást* tartalmaz, amelyek egy adott dúsítási műveletet képviselnek, például szöveget fordítanak le, kinyerik a kulcsfontosságú kifejezéseket, vagy elvégezhetik az optikai karakterfelismerést egy képfájlból. A készségkészlet létrehozásához használhatja a Microsoft [beépített képességeit](cognitive-search-predefined-skills.md) , illetve az Ön által megadott modelleket vagy feldolgozási logikát tartalmazó egyéni képességeket (lásd [: példa: egyéni képesség létrehozása mesterséges intelligencia-növelési folyamatokban](cognitive-search-create-custom-skill-example.md) további információk).

Ebből a cikkből megtudhatja, hogyan hozhat létre a használni kívánt képességek bővítési folyamatát. A készségkészlet egy Azure Cognitive Search [Indexelő](search-indexer-overview.md)van csatolva. A folyamat kialakításának egyik része, amely ebben a cikkben is szerepel, a készségkészlet maga hozza létre. 

> [!NOTE]
> A folyamat kialakításának egy másik része a [következő lépésben](#next-step)lefedett indexelő megadása. Az indexelő definíciója a készségkészlet mutató hivatkozást, valamint a bemenetek a célként megadott indexben való csatlakoztatásához használt mező-hozzárendeléseket is tartalmazza.

Jegyezze fel a legfontosabb pontokat:

+ Indexelő esetében csak egy készségkészlet tartozhat.
+ A készségkészlet rendelkeznie kell legalább egy képességgel.
+ Több, azonos típusú szaktudást is létrehozhat (például egy képelemzési képesség változatai).

## <a name="begin-with-the-end-in-mind"></a>Kezdje a végét szem előtt tartva

Az ajánlott kezdeti lépés azt határozza meg, hogy mely adatok legyenek kinyerve a nyers adatokból, és hogyan szeretné használni ezeket az adatok egy keresési megoldásban. A teljes dúsítási folyamat illusztrációjának létrehozása segíthet a szükséges lépések azonosításában.

Tegyük fel, hogy több pénzügyi elemzői Megjegyzés feldolgozását érdekli. Minden fájlhoz ki kell bontania a vállalatok nevét és a megjegyzések általános hangulatát. Érdemes lehet olyan egyéni dúsítást is írni, amely a Bing Entity Search szolgáltatást használja a vállalattal kapcsolatos további információk megkereséséhez, például arról, hogy a vállalat milyen üzleti tevékenységet folytat. Lényegében a következőhöz hasonló adatokat szeretne kinyerni az egyes dokumentumok indexeléséhez:

| rekord – szöveg | vállalatok | hangulatelemzés | a cég leírása |
|--------|-----|-----|-----|
|minta – rekord| ["Microsoft", "LinkedIn"] | 0,99 | ["A Microsoft Corporation egy amerikai multinacionális technológiai cég...", "a LinkedIn egy üzleti és foglalkoztatás-orientált közösségi hálózat..."]

Az alábbi ábrán egy feltételezett alkoholtartalom-növelési folyamat látható:

![Egy feltételezett alkoholtartalom-növelési folyamat](media/cognitive-search-defining-skillset/sample-skillset.png "Egy feltételezett alkoholtartalom-növelési folyamat")


Ha jó ötlete van arról, hogy mit szeretne a folyamaton belül, megadhatja a lépéseket készségkészlet. A készségkészlet az indexelő definíciójának Azure Cognitive Searchba való feltöltésekor fejezi ki. Az indexelő feltöltésével kapcsolatos további tudnivalókért tekintse meg az [Indexelő dokumentációját](/rest/api/searchservice/create-indexer).


A diagramon a *repedések* megjelenő lépése automatikusan megtörténik. Az Azure Cognitive Search ismeri a jól ismert fájlok megnyitását és az egyes dokumentumokból kinyert szöveget tartalmazó *tartalmi* mezőt. A fehér dobozok beépített gazdagítók, a pontozott "Bing Entity Search" mező pedig egy Ön által létrehozott egyéni dúsítást jelöl. Ahogy az ábrán látható, a készségkészlet három ismerettel rendelkezik.

## <a name="skillset-definition-in-rest"></a>Készségkészlet-definíció a REST-ben

A készségkészlet a szaktudás tömbje van meghatározva. Az egyes képességek a bemenetek forrását és a létrehozott kimenetek nevét határozzák meg. A [Készségkészlet létrehozása REST API](/rest/api/searchservice/create-skillset)segítségével megadhatja az előző diagramnak megfelelő készségkészlet: 

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

```json
{
  "description": 
  "Extract sentiment from financial records, extract company names, and then find additional information about each company mentioned.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "Calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
  ]
}
```

## <a name="create-a-skillset"></a>Képességcsoport létrehozása

Készségkészlet létrehozásakor megadhat egy leírást, amely lehetővé teszi a készségkészlet öndokumentálása. A leírás nem kötelező, de hasznos, ha nyomon szeretné követni, hogy mi a készségkészlet. Mivel a készségkészlet egy JSON-dokumentum, amely nem engedélyezi a megjegyzéseket, ezt a elemet kell használnia `description` .

```json
{
  "description": 
  "This is our first skill set, it extracts sentiment from financial records, extract company names, and then finds additional information about each company mentioned.",
  ...
}
```

A készségkészlet következő része a szaktudás tömbje. Minden egyes képességet megtalálhat a gazdagodás primitívebb részeként. Az egyes képességek egy kis feladatot hajtanak végre ebben a dúsítási folyamatban. Mindegyiknek van egy bemenete (vagy a bemenetek halmaza), és néhány kimenetet ad vissza. A következő néhány szakaszban a beépített és az egyéni képességek megadására, valamint a bemeneti és kimeneti referenciák használatával történő láncolására koncentrálunk. A bemeneti adatok származhatnak a forrásadatokből vagy más képességből is. A kimenetek leképezhetők egy keresési index egy mezőjére, vagy az alárendelt képességek bemenetként használhatók.

## <a name="add-built-in-skills"></a>Beépített szaktudás hozzáadása

Nézzük meg az első szakértelmet, amely a beépített [entitás-felismerési képesség](cognitive-search-skill-entity-recognition.md):

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "context": "/document",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    }
```

* Minden beépített képesség a, `odata.type` `input` a és a `output` tulajdonságokkal rendelkezik. A szaktudás-specifikus tulajdonságok további információkat biztosítanak az adott szakértelmet illetően. Az entitások felismerése esetében az `categories` egyik entitás egy rögzített típusú entitás, amelyet az előre betanított modell képes felismerni.

* Minden egyes szaktudásnak rendelkeznie kell egy ```"context"``` . A környezet a műveletek elvégzésének szintjét jelöli. A fenti szaktudásban a kontextus a teljes dokumentum, ami azt jelenti, hogy az entitás-felismerési képességet dokumentum szerint egyszer kell meghívni. A kimenetek ezen a szinten is előállíthatók. Pontosabban a ```"organizations"``` a tagjaként jön létre ```"/document"``` . Az alárendelt szakismeretekben ezt az újonnan létrehozott információt tekintheti meg ```"/document/organizations"``` .  Ha a ```"context"``` mező nincs explicit módon beállítva, az alapértelmezett környezet a dokumentum.

* A skill egy "text" nevű bemenettel rendelkezik, amelyhez forrás bemenet van beállítva ```"/document/content"``` . A skill (Entity Recognition) minden dokumentum *tartalom* mezőjében működik, amely az Azure Blob indexelő által létrehozott szabványos mező. 

* A szakértelem egy kimenettel rendelkezik ```"organizations"``` . A kimenetek csak a feldolgozás során léteznek. Ha ezt a kimenetet egy alsóbb rétegbeli képesség bemenetéhez szeretné felvenni, hivatkozzon a kimenetre a következőre: ```"/document/organizations"``` .

* Egy adott dokumentum esetében az értéke a ```"/document/organizations"``` szövegből kinyert szervezetek tömbje. Például:

  ```json
  ["Microsoft", "LinkedIn"]
  ```

Bizonyos helyzetekben a tömb egyes elemeinek külön való hivatkozását kell meghívni. Tegyük fel például, hogy az egyes elemeket különállóan szeretné átadni ```"/document/organizations"``` egy másik képességre (például az egyéni Bing Entity Search-gazdagabbá). A tömb egyes elemeire úgy tekintheti meg, hogy egy csillagot ad hozzá az elérési úthoz: ```"/document/organizations/*"``` 

Az érzelmek kinyerésének második szaktudása ugyanaz a minta, mint az első gazdagabbá. Bemenetként fog megjelenni ```"/document/content"``` , és az egyes tartalom-példányok esetében a hangulati pontszámot adja vissza. Mivel a ```"context"``` mező explicit módon nem lett beállítva, a kimenet (mySentiment) már gyermeke ```"/document"``` .

```json
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
```

## <a name="add-a-custom-skill"></a>Egyéni képesség hozzáadása

Hívja fel az egyéni Bing Entity Search gazdagabbáer struktúráját:

```json
    {
      "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
     "description": "This skill calls an Azure function, which in turn calls Bing Entity Search",
      "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeTextAnalyticsV3?code=foo",
      "httpHeaders": {
          "Ocp-Apim-Subscription-Key": "foobar"
      },
      "context": "/document/organizations/*",
      "inputs": [
        {
          "name": "query",
          "source": "/document/organizations/*"
        }
      ],
      "outputs": [
        {
          "name": "description",
          "targetName": "companyDescription"
        }
      ]
    }
```

Ez a definíció egy [Egyéni képesség](cognitive-search-custom-skill-web-api.md) , amely webes API-t hív meg a dúsítási folyamat részeként. Az entitások felismerése által azonosított minden szervezet számára ez a képesség meghívja a webes API-t, hogy megkeresse a szervezet leírását. A webes API meghívásának, valamint a kapott információk folyamatának előkészítését a dúsítási motor belsőleg kezeli. Azonban az egyéni API meghívásához szükséges inicializálást meg kell adni a JSON-ban (például URI, httpHeaders és a várt bemenetek). Az egyéni webes API-k bővítési folyamathoz való létrehozásával kapcsolatos útmutatásért lásd: [Egyéni felület definiálása](cognitive-search-custom-skill-interface.md).

Figyelje meg, hogy a "Context" mező ```"/document/organizations/*"``` csillaggal van beállítva, ami azt jelenti, hogy a dúsítási lépést *minden egyes* szervezethez meg kell hívni ```"/document/organizations"``` . 

A kimenet, ebben az esetben a vállalat leírása minden azonosított szervezethez létrejön. Ha egy alsóbb rétegbeli lépésben (például a Key kifejezés kibontásakor) hivatkozik a leírásra, az elérési utat kell használnia ```"/document/organizations/*/description"``` . 

## <a name="add-structure"></a>Struktúra hozzáadása

A készségkészlet strukturált adatokat hoz létre strukturálatlan adatokból. Tekintse meg a következő példát:

*"A negyedik negyedévében a Microsoft naplózott $1 100 000 000 a LinkedIn bevételeiből származik, az előző évben vásárolt közösségi hálózati vállalatnál. Az akvizíció lehetővé teszi, hogy a Microsoft a LinkedIn képességeit a CRM és az Office képességeivel kombinálja. Az eddigi előrehaladást a részvényesek is izgatottak. "*

A valószínű eredmény az alábbi ábrához hasonló generált struktúra lehet:

![Minta kimeneti szerkezete](media/cognitive-search-defining-skillset/enriched-doc.png "Minta kimeneti szerkezete")

Eddig ez a struktúra csak belső, csak a memóriában, és csak az Azure Cognitive Search indexekben használatos. A Tudásbázis hozzáadása lehetővé teszi, hogy a keresésen kívüli használatra is mentse az alakzatokat.

## <a name="add-a-knowledge-store"></a>Knowledge Store hozzáadása

A [Knowledge Store](knowledge-store-concept-intro.md) az Azure Cognitive Search egyik funkciója, amely a dúsított dokumentumok mentését teszi elérhetővé. A létrehozott, Azure Storage-fiókkal támogatott adattár az a tárház, amelyben a dúsított adatterületek szerepelnek. 

A rendszer egy Knowledge Store-definíciót ad hozzá egy készségkészlet. A teljes folyamatról további információt a következő témakörben talál: a [Knowledge Store létrehozása a REST-ben](knowledge-store-create-rest.md).

```json
"knowledgeStore": {
  "storageConnectionString": "<an Azure storage connection string>",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "containername",
          "source": "/document/EnrichedShape/",
          "key": "/document/Id"
        }
      ]
    }
  ]
}
```

Dönthet úgy, hogy a dúsított dokumentumokat táblázatként vagy a blob Storage-ban található JSON-dokumentumként menti. A készségkészlet bármely szakismeretének kimenete a leképezés bemenetének forrásaként is megadható. Ha egy adott alakzatba kívánja feltervezni az adott alakzatot, a frissített [formáló képesség](cognitive-search-skill-shaper.md) mostantól a használni kívánt összetett típusok modellezésére is képes. 

<a name="next-step"></a>

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a dúsítási folyamatot és a szakértelmével, folytassa a [jegyzetek készségkészlet való hivatkozását](cognitive-search-concept-annotations-syntax.md) , illetve a [kimeneteknek az index mezőire való leképezését](cognitive-search-output-field-mapping.md).