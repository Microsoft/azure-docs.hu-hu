---
title: Személyes adatok észlelése – kognitív képességek (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Személyes adatok kinyerése és maszkolása az Azure Cognitive Searchban lévő alkoholtartalom-növelési folyamatban. Ez a képesség jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: acacf617d3f1d9ab891d08b32fc2dfb14deb64a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540523"
---
# <a name="pii-detection-cognitive-skill"></a>Személyes adatok észlelése – kognitív képességek

> [!IMPORTANT] 
> Ez a képesség jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Jelenleg nincs portál vagy .NET SDK-támogatás.

A személyre szabási **észlelési** képesség a személyes adatokat egy bemeneti szövegből gyűjti, és lehetővé teszi a maszkolását. Ez a képesség a Cognitive Services [text Analytics](../cognitive-services/text-analytics/overview.md) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.

## <a name="odatatype"></a>@odata.type

Microsoft. Skills. Text. PIIDetectionSkill

## <a name="data-limits"></a>Adatkorlátok

A rekordok maximális méretének 50 000 karakternek kell lennie, a következőképpen mérve: [`String.Length`](/dotnet/api/system.string.length) . Ha az adatokat a szakértelemre való elküldésük előtt meg kell osztania, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket, és az összes nem kötelező.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| `defaultLanguageCode` |    A bemeneti szöveg nyelvi kódja Egyelőre csak a `en` támogatott. |
| `minimumPrecision` | 0,0 és 1,0 közötti érték. Ha a megbízhatósági pontszám (a `piiEntities` kimenetben) kisebb, mint a beállított `minimumPrecision` érték, akkor az entitás nem lesz visszaadva vagy maszkolt. Az alapértelmezett érték a 0,0. |
| `maskingMode` | Egy paraméter, amely különböző módokon teszi lehetővé a bemeneti szövegben észlelt személyes adatok maszkolását. A következő lehetőségek támogatottak: <ul><li>`none` (alapértelmezett): nem történik maszkolás, és a `maskedText` rendszer nem adja vissza a kimenetet. </li><li> `redact`: Eltávolítja az észlelt entitásokat a bemeneti szövegből, és nem cseréli le a törölt értékeket. Ebben az esetben a kimenet eltolása az `piiEntities` eredeti szöveggel lesz összefüggésben, nem a maszkolt szöveggel. </li><li> `replace`: Lecseréli az észlelt entitásokat a paraméterben megadott karakterre `maskingCharacter` . A rendszer megismétli a karaktert az észlelt entitás hosszára, hogy az eltolások megfelelően megfeleljenek a bemeneti szövegnek és a kimenetnek `maskedText` .</li></ul> |
| `maskingCharacter` | A szöveg maszkolására szolgáló karakter, ha a `maskingMode` paraméter értéke `replace` . A következő lehetőségek támogatottak: `*` (alapértelmezett), `#` , `X` . Ezt a paramétert csak akkor lehet `null` megadni, ha `maskingMode` a értéke nem `replace` . |

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Választható. Az alapértelmezett szint a `en`.  |
| `text`          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve      | Description                   |
|---------------|-------------------------------|
| `piiEntities` | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>szöveg (a tényleges személyes adatok kinyerése)</li> <li>típus</li><li>Altípus</li><li>pontszám (a magasabb érték azt jelenti, hogy nagyobb a valószínűsége, hogy valódi entitás lesz)</li><li>eltolás (a bemeneti szövegbe)</li><li>hossz</li></ul> </br> [Itt megtalálhatja a lehetséges típusokat és altípusokat.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | Ha a értéke nem a (z) `maskingMode` értékre van állítva `none` , akkor ez a kimenet lesz a bemenő szövegen a kiválasztott módon végrehajtott maszkolás karakterláncának eredménye `maskingMode` .  Ha a `maskingMode` értéke `none` , akkor ez a kimenet nem jelenik meg. |

## <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```

## <a name="sample-input"></a>Példabemenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

## <a name="sample-output"></a>Példakimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

A rendszer közvetlenül visszaküldi az entitások által visszaadott eltolásokat a [text Analytics APIból](../cognitive-services/text-analytics/overview.md), ami azt jelenti, hogy ha az eredeti sztring indexbe való indexelését használja, akkor a [StringInfo](/dotnet/api/system.globalization.stringinfo) osztályt kell használnia a .net-ben a megfelelő tartalom kinyeréséhez.  [További részleteket itt találhat.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések

Ha a dokumentumhoz tartozó nyelvi kód nem támogatott, a rendszer figyelmeztetést ad vissza, és egyetlen entitás sincs kibontva.
Ha a szöveg üres, a rendszer figyelmeztetést ad vissza.
Ha a szöveg 50 000 karakternél nagyobb, akkor csak az első 50 000 karakter lesz elemezve, és a rendszer figyelmeztetést ad ki.

Ha a szakértelem figyelmeztetést ad vissza, `maskedText` Előfordulhat, hogy a kimenet üres, ami hatással lehet a kimenetre várható alsóbb rétegbeli képességekre. Ezért mindenképpen vizsgálja meg a hiányzó kimenettel kapcsolatos összes figyelmeztetést a készségkészlet-definíció írásakor.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)