---
title: A szakértelmével lévő bemenetek és kimenetek referenciái
titleSuffix: Azure Cognitive Search
description: Ismerteti a jegyzet szintaxisát, valamint azt, hogyan lehet egy készségkészlet bemenetében és kimenetében lévő jegyzeteket az Azure Cognitive Search AI-dúsítási folyamatában.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935534"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Megjegyzések áttekintése egy Azure Cognitive Search készségkészlet

Ebből a cikkből megtudhatja, hogyan hivatkozhat a megjegyzésekre a szaktudás-definíciókban, példákkal illusztrálva a különböző forgatókönyveket. Mivel a dokumentumok tartalma szaktudáson keresztül áramlik, a jegyzetek használatával gazdagítva lesz. A jegyzetek használhatók bemenetként a további alsóbb rétegbeli dúsításhoz, vagy egy index kimeneti mezőjéhez is hozzárendelve. 
 
A cikkben szereplő példák az [Azure Blob-indexek](search-howto-indexing-azure-blob-storage.md) által automatikusan generált *tartalom* mező alapján jelennek meg a dokumentum repedési fázisának részeként. Ha blob-tárolóból származó dokumentumokra hivatkozik, használjon például egy olyan formátumot `"/document/content"` , amelyben a *Content (tartalom* ) mező a *dokumentum* részét képezi. 

## <a name="background-concepts"></a>Háttérbeli fogalmak

A szintaxis felülvizsgálata előtt tekintse át a néhány fontos fogalmat, hogy jobban megértse a jelen cikk későbbi részében ismertetett példákat.

| Időszak | Leírás |
|------|-------------|
| Dúsított dokumentum | A dúsított dokumentumok egy belső struktúra, amelyet a folyamat hozott létre, és amely a dokumentumhoz kapcsolódó összes jegyzet tárolására szolgál. Tekintse meg a dúsított dokumentumokat jegyzetekkel ellátott faszerkezetként. Általában egy korábbi jegyzetből létrehozott jegyzet lesz a gyermeke.<p/>A dúsított dokumentumok csak a készségkészlet végrehajtásának időtartamára léteznek. Ha a tartalom a keresési indexre van leképezve, a bővített dokumentumra már nincs szükség. Bár a dúsított dokumentumokkal nem folytatja közvetlenül a kapcsolatot, a készségkészlet létrehozásakor hasznos lehet a dokumentumok mentális modellje. |
| Dúsítási környezet | Az a környezet, amelyben a gazdagodás zajlik, és amely alapján a rendszer kibővíti az elemet. Alapértelmezés szerint a dúsítási környezet az `"/document"` egyes dokumentumokra kiterjedő szinten van. A szaktudás futtatásakor a szaktudás kimenetei [a definiált környezet tulajdonságai](#example-2)lesznek.|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>1. példa: egyszerű jegyzet leírása

Tegyük fel, hogy az Azure Blob Storage-ban számos olyan fájl található, amely az Entity Recognition használatával kinyerni kívánt személyek nevére mutató hivatkozásokat tartalmaz. Az alábbi szakértelem-definícióban a `"/document/content"` teljes dokumentum szöveges ábrázolása, a "személyek" pedig teljes nevek kinyerése a személyként azonosított entitások számára.

Mivel az alapértelmezett környezet a `"/document"` , a felhasználók listája most már hivatkozhat `"/document/people"` . Ebben az esetben `"/document/people"` egy jegyzet, amely most már leképezhető egy index egy mezőjére, vagy egy másik, ugyanabban a készségkészlet lévő képességben.

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

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>2. példa: egy tömbre mutató hivatkozás egy dokumentumon belül

Ez a példa az előzőre épül, amely azt mutatja be, hogyan hívhat meg egyszerre többször a dúsítási lépést ugyanazzal a dokumentummal. Tegyük fel, hogy az előző példa olyan karakterláncok tömbjét generálta, amelyek 10 személy névvel rendelkeznek egyetlen dokumentumból. Egy ésszerű következő lépés lehet egy második dúsítás, amely Kinyeri a vezetéknevet a teljes névben. Mivel a rendszer 10 nevet használ, ezt a lépést a jelen dokumentumban 10 alkalommal kell meghívni, minden személyre egyszer. 

A megfelelő számú iteráció meghívásához állítsa be a kontextust úgy `"/document/people/*"` , hogy a csillag () a kibővített `"*"` dokumentum összes csomópontját a következőként jelenítse meg `"/document/people"` . Bár ez a képesség csak egyszer van definiálva a szaktudás tömbben, a rendszer minden tag számára meghívja a dokumentumot, amíg az összes tagot fel nem dolgozza.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Ha a jegyzetek tömbök vagy sztringek gyűjteményei, előfordulhat, hogy a tömb egésze helyett meghatározott tagokat szeretne megcélozni. A fenti példa létrehoz egy jegyzetet `"last"` , amelyet a környezet által jelzett csomópontok alatt nevezünk. Ha ezt a családot szeretné megtekinteni a megjegyzések közül, használhatja a szintaxist `"/document/people/*/last"` . Ha egy adott jegyzetre szeretne hivatkozni, használhat egy explicit indexet: `"/document/people/1/last` "a dokumentumban azonosított első személy vezetéknevének hivatkozásához. Figyelje meg, hogy ebben a szintaxisban a tömbök "0 indexelt".

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>3. példa: a tömbben lévő hivatkozási tagok

Előfordulhat, hogy egy adott típushoz tartozó összes jegyzetet egy adott szaktudásba kell átadnia. Vegyünk egy feltételezett egyéni képességet, amely a 2. példában kinyert összes vezetéknevet azonosítja a leggyakoribb vezetéknevek közül. Ha csak az utolsó nevet szeretné megadni az egyéni szakértelem számára, adja meg a kontextust `"/document"` és a bemenetet a következőként: `"/document/people/*/lastname"` .

Figyelje meg, hogy a kardinális érték `"/document/people/*/lastname"` nagyobb, mint a dokumentum. A dokumentumhoz csak egy dokumentum-csomópont tartozhat. Ebben az esetben a rendszer automatikusan létrehoz egy tömböt, amely  `"/document/people/*/lastname"` tartalmazza a dokumentum összes elemét.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Lásd még
+ [Egyéni szakértelem integrálása a dúsítási folyamatba](cognitive-search-custom-skill-interface.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Készségkészlet létrehozása (REST)](/rest/api/searchservice/create-skillset)
+ [A dúsított mezők indexhez való leképezése](cognitive-search-output-field-mapping.md)