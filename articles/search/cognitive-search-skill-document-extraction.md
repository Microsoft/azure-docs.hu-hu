---
title: Dokumentum-extrakciós kognitív képesség
titleSuffix: Azure Cognitive Search
description: A tartalom kibontása egy fájlból a dúsítási folyamaton belül. Ez a képesség jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: 144e8058e640f98dc6b0ef60534405525532b00e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547866"
---
# <a name="document-extraction-cognitive-skill"></a>Dokumentum-extrakciós kognitív képesség

A **dokumentum-kivonási** képesség Kinyeri a tartalmat egy fájlból a dúsítási folyamaton belül. Ez lehetővé teszi, hogy kihasználja az olyan dokumentum-kivonási lépést, amely általában a készségkészlet végrehajtása előtt történik, amelyet más szakismeretek is létrehozhatnak.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum részeként, az indexelés során. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kinyerésének díjszabását a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)találja.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bevitelek | Megengedett értékek | Leírás |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Olyan `default` fájlok kibontására van beállítva, amelyek nem tiszta szöveg vagy JSON formátumúak. Állítsa a (z) értékre `text` az egyszerű szövegfájlok teljesítményének növelése érdekében. Állítsa be a `json` elemet a következőre: strukturált tartalom kinyerése JSON-fájlokból Ha `parsingMode` nincs explicit módon definiálva, akkor a következőre lesz beállítva: `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Állítsa be értékre `contentAndMetadata` az összes metaadat és szöveges tartalom kinyeréséhez minden fájlból. Állítsa be, hogy `allMetadata` csak a [tartalom típusának metaadat-tulajdonságait](search-blob-metadata-properties.md) (például a csak. png fájlokra jellemző metaadatokat) szeretné kibontani. Ha `dataToExtract` nincs explicit módon definiálva, akkor a következőre lesz beállítva: `contentAndMetadata` . |
| `configuration` | Lásd alább. | A dokumentumok kinyerésének módját módosító választható paraméterek szótára. A támogatott konfigurációs tulajdonságok leírását az alábbi táblázat tartalmazza. |

| Konfigurációs paraméter   | Megengedett értékek | Leírás |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Állítsa az értékre `none` , hogy figyelmen kívül hagyja az adatkészletben lévő beágyazott képeket vagy képfájlokat. Ez az alapértelmezett beállítás. <br/>Ha a [képelemzést kognitív képességekkel szeretné használni](cognitive-search-concept-image-scenarios.md), úgy állítsa be, hogy `generateNormalizedImages` a készség a dokumentum repedésének részeként normalizált rendszerképekből álló tömböt hozzon létre. Ehhez a művelethez a és a értékre kell állítani `parsingMode` `default` `dataToExtract` `contentAndMetadata` . A normalizált kép olyan további feldolgozásra utal, amely egységes képkimenetet, méretet és elforgatást eredményez, így a konzisztens renderelés elősegítése, ha képeket tartalmaz a vizuális keresési eredményekben (például egy gráf vezérlőelemben a [JFK bemutatóban](https://github.com/Microsoft/AzureSearch_JFK_Files)látható azonos méretű fényképeket). Ez az információ minden rendszerképhez létrejön, ha ezt a beállítást használja.  <br/>Ha a értékre van állítva `generateNormalizedImagePerPage` , a PDF-fájlokat a rendszer a beágyazott képek kibontása helyett eltérően fogja kezelni, és ennek megfelelően a rendszer az egyes lapokat képként jeleníti meg.  A nem PDF típusú fájltípusok ugyanúgy lesznek kezelve, mint ha `generateNormalizedImages` be van állítva.
| `normalizedImageMaxWidth` | 50-10000 közötti egész szám | A generált normalizált képek maximális szélessége (képpontban). Az alapértelmezett érték 2000. | 
| `normalizedImageMaxHeight` | 50-10000 közötti egész szám | A generált normalizált képek maximális magassága (képpontban). Az alapértelmezett érték 2000. |

> [!NOTE]
> A normalizált képek maximális szélességének és magasságának 2000 képpont alapértéke az [OCR-képesség](cognitive-search-skill-ocr.md) és a [képelemzési képesség](cognitive-search-skill-image-analysis.md)által támogatott maximális méretektől függ. Az [OCR-képesség](cognitive-search-skill-ocr.md) legfeljebb 4200-es szélességet és magasságot támogat a nem angol nyelvű és a 10000 angol nyelv esetén.  Ha csökkenti a maximális korlátot, a feldolgozás a készségkészlet-definíciótól és a dokumentumok nyelvétől függően meghiúsulhat a nagyobb rendszerképeken. 
## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név     | Leírás |
|--------------------|-------------|
| `file_data` | Az a fájl, amelyből a tartalmat ki kell olvasni. |

A "file_data" bemenetnek az alábbiak szerint definiált objektumnak kell lennie:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

A fájl hivatkozási objektuma háromféleképpen hozható létre:

 - Az `allowSkillsetToReadFileData` Indexelő definíciójában lévő paraméter értéke "true" (igaz).  Ez egy olyan elérési utat hoz létre `/document/file_data` , amely a blob-adatforrásból letöltött eredeti fájl adatait jelképezi. Ez a paraméter csak a blob Storage-ban lévő adatértékekre vonatkozik.

 - A `imageAction` paramétert az indexelő definíciójában a következő értékre állítja be: `none` .  Ez létrehoz egy tömböt a rendszerképekről, amelyek a szükséges konvenciót követik, ha a rendszer külön (azaz) adta át ezt a képességet `/document/normalized_images/*` .

 - Az egyéni képességek egy JSON-objektumot adnak vissza, amely pontosan a fentiek szerint van meghatározva.  A `$type` paramétert pontosan kell beállítani `file` , és a `data` paraméternek a fájl tartalmának alap 64 kódolású bájtos tömb adatának kell lennie.

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve    | Leírás |
|--------------|-------------|
| `content` | A dokumentum szöveges tartalma. |
| `normalized_images`   | Ha a értéke `imageAction` ettől eltérő értékre van állítva `none` , az új *normalized_images* mező képek tömbjét fogja tartalmazni. Az egyes rendszerképek kimeneti formátumával kapcsolatos további részletekért tekintse meg [a képek kinyerésének dokumentációját](cognitive-search-concept-image-scenarios.md) . |

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Példabemenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
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
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Információk feldolgozása és kinyerése a rendszerképekből a kognitív keresési helyzetekben](cognitive-search-concept-image-scenarios.md)