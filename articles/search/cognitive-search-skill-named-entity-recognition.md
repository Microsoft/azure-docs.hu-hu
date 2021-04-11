---
title: Elnevezett entitások felismerése – kognitív képesség
titleSuffix: Azure Cognitive Search
description: Elnevezett entitások kinyerése az Azure Cognitive Search mesterséges intelligencia-dúsítási folyamatában lévő személy, hely és szervezet számára.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e598f16c6b441cf986c7ac82d67c037f75be8982
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547441"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Elnevezett entitások felismerése – kognitív képesség

Az **elnevezett entitás-felismerési** képességek nevű entitások szövegből való kibontása. Az elérhető entitások közé tartoznak a típusok `person` `location` és a `organization` .

> [!IMPORTANT]
> Az elnevezett entitás-felismerési képesség már megszűnt a [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)helyett. A támogatás 2019. február 15-én megszűnt, és az API-t a 2019. május 2-án eltávolították a termékből. Kövesse az [elavult kognitív keresési ismeretekben](cognitive-search-skill-deprecated.md) szereplő javaslatokat a támogatott szaktudásra való áttéréshez.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. NamedEntityRecognitionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a következőképpen mérve: [`String.Length`](/dotnet/api/system.string.length) . Ha meg kell szakítania az adatokat, mielőtt elküldené a kivonó kifejezést, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| kategóriák    | A kinyerni kívánt kategóriák tömbje.  Lehetséges kategóriájú típusok: `"Person"` , `"Location"` , `"Organization"` . Ha nincs megadva kategória, a rendszer az összes típust adja vissza.|
|defaultLanguageCode |  A bemeneti szöveg nyelvi kódja A következő nyelvek támogatottak: `de, en, es, fr, it`|
| minimumPrecision  | 0 és 1 közötti szám. Ha a pontosság ennél az értéknél kisebb, az entitás nem lesz visszaadva. Az alapértelmezett érték a 0.|

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett szint a `"en"`.  |
| szöveg          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| személyek      | Karakterláncok tömbje, amelyben minden sztring egy személy nevét jelöli. |
| helyek  | Karakterláncok tömbje, amelyben minden sztring egy helyet jelöl. |
| organizations  | Karakterláncok tömbje, amelyben minden sztring egy szervezetet jelöl. |
| szervezetek | Összetett típusok tömbje. Minden összetett típus a következő mezőket tartalmazza: <ul><li>Kategória ( `"person"` , `"organization"` , vagy `"location"` )</li> <li>érték (a tényleges entitás neve)</li><li>eltolás (az a hely, ahol a szöveg található)</li><li>megbízhatóság (0 és 1 közötti érték, amely azt jelzi, hogy az érték tényleges entitás)</li></ul> |

##  <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
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
##  <a name="sample-input"></a>Példabemenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
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
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="warning-cases"></a>Figyelmeztető esetek
Ha a dokumentumhoz tartozó nyelvi kód nem támogatott, a rendszer figyelmeztetést ad vissza, és egyetlen entitás sincs kibontva.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md)