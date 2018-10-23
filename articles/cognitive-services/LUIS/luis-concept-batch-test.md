---
title: A Batch a LUIS-alkalmazás – beszédfelismerés tesztelése
titleSuffix: Azure Cognitive Services
description: Tesztelés a batch segítségével folyamatosan dolgozunk azon, pontosítsa és javítható a beszédfelismerés annak az alkalmazás.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a35135d6d19ba10e1fd7473053be747715202440
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49637255"
---
# <a name="batch-testing-in-luis"></a>A Batch a LUIS tesztelése

Batch tesztelés érvényesíti a [aktív](luis-concept-version.md#active-version) betanított modell méréséhez az előrejelzés pontosságát. Az egyes szándékot és entitás pontosságát megtekintése a diagramon az aktuális betanított modell egy batch-teszt segítségével. Tekintse át a batch-vizsgálati eredmények pontosságának, például további példa beszédmódok hozzáadása megjelölésű, ha az alkalmazás gyakran nem tudja azonosítani a helyes cél növelése érdekében a megfelelő műveletet.

## <a name="group-data-for-batch-test"></a>A batch-teszt csoport adatai
Fontos, hogy batch tesztelésére utterances nem ismeri a LUIS. Ha utterances az adatkészlet, megcímkézzen három csoportokba oszthatja: utterances megjelölésű hozzáadott, a közzétett végpontról érkezett utterances és használt batch-próbára LUIS, ha be van tanítva kimondott szöveg. 

## <a name="a-dataset-of-utterances"></a>Egy adatkészlet a kimondott szöveg
Küldje el a fájlt egy kötegfájlban utterances, más néven egy *adatkészlet*, kötegelt teszteléséhez. Az adatkészlet maximum 1000 feliratú tartalmazó JSON-formátumú fájlt **egyszer előforduló** kimondott szöveg. Legfeljebb 10 adatkészletek tesztelheti egy alkalmazásban. Ha szeretne további tesztelése, adatkészlet törlése, és adja hozzá az egy újat.

|**Szabályok**|
|--|
|* Nincsenek ismétlődő kimondott szöveg|
|A hierarchikus gyermekek nem|
|1000 utterances vagy kevesebb|

* Ismétlődések karakterlánc pontos egyezést, nem a rendszer először tokenekre bontott egyezéstípust minősülnek. 

## <a name="entities-allowed-in-batch-tests"></a>Entitások engedélyezett a batch-tesztek
Entitások például az egyszerű, hierarchikus szülők és összetett. Az ilyen jellegű összes entitások jelennek meg entitások szűrő batch teszt, akkor sem, ha nincs megfelelő entitásokat a parancsfájlba.


<a name="json-file-with-no-duplicates"></a>
<a name="example-batch-file"></a>
## <a name="batch-file-format"></a>Batch-fájlformátum
A kötegfájl utterances áll. Minden kimondásakor rendelkeznie kell egy várt szándék előrejelzési együtt bármely [gép megismert entitások](luis-concept-entity-types.md#types-of-entities) várhatóan észlelhető. 

A következő egy példa a parancsfájl a megfelelő szintaxist:

```JSON
[
  {
    "text": "Are there any janitorial jobs currently open?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 14,
            "endPos": 23
        }
    ]
  },
  {
    "text": "I would like a fullstack typescript programming with azure job",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 15,
            "endPos": 46
        }
    ]
  },
  {
    "text": "Is there a database position open in Los Colinas?",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 11,
            "endPos": 18
        }
    ]
  },
  {
    "text": "Please find database jobs open today in Seattle",
    "intent": "GetJobInformation",
    "entities": 
    [
        {
            "entity": "Job",
            "startPos": 12,
            "endPos": 19
        }
    ]
  }
]
```

## <a name="batch-syntax-template"></a>Batch-szintaxis sablon

Az alábbi sablon használatával indítsa el a parancsfájlba:

```JSON
[
  {
    "text": "example utterance goes here",
    "intent": "intent name goes here",
    "entities": 
    [
        {
            "entity": "entity name 1 goes here",
            "startPos": 14,
            "endPos": 23
        },
        {
            "entity": "entity name 2 goes here",
            "startPos": 14,
            "endPos": 23
        }
    ]
  }
]
```

A szkript a **startPos** és **endPos** megjegyezni elején és végén egy entitás tulajdonságai. Az értékek nulláról induló és kell nem kezdődik vagy fejeződjön be egy szóközt. 

Ez különbözik a lekérdezés naplókat, amelyek startIndex és endIndex tulajdonságokkal. 


## <a name="common-errors-importing-a-batch"></a>Gyakori hibák a batch importálása
Gyakori hibák a következők: 

> * Több mint 1000 kimondott szöveg
> * Az utterance (kifejezés) JSON-objektum, amely nem rendelkezik az entitások tulajdonság
> * A több entitás feliratú szavak

## <a name="batch-test-state"></a>Batch-teszt állapota
A LUIS minden adathalmaz utolsó teszt állapotát követi nyomon. Ez magában foglalja a legutóbbi futtatás mérete (a köteg utterances száma), dátum, és a legutóbbi eredmény (utterances sikeresen előre jelzett száma).

<a name="sections-of-the-results-chart"></a>
## <a name="batch-test-results"></a>A Batch terhelésiteszt-eredményei
A batch-vizsgálat eredményének a pontdiagram grafikon, egy hiba mátrix néven. Ez a diagram a fájlt, és az aktuális modell előre jelzett szándékot és entitások megcímkézzen 4-módszer összehasonlítását. 

Az adatpontok a **hamis pozitív** és **téves negatív** szakaszok jelzi a hibákat, amelyeket meg kell vizsgálni. Összes adatpont esetén a **valódi pozitívak** és **igaz negatív** részei, akkor az alkalmazás pontossága tökéletes megoldás az adatkészlethez.

![Diagram négy részből áll](./media/luis-concept-batch-test/chart-sections.png)

Ez a diagram segít helytelenül alapján a jelenlegi képzést, amely előrejelzi a LUIS utterances található. Az eredmények jelennek meg a diagram régiónként. Válassza ki a diagramon, tekintse át az utterance (kifejezés) adatokat, vagy válassza ki az adott régióban utterance (kifejezés) eredményeinek áttekintése régió neve egyedi pontokat.

![Kötegelt tesztelés](./media/luis-concept-batch-test/batch-testing.png)

## <a name="errors-in-the-results"></a>Eredmények hibái
A batch-teszt utalnak, hogy a rendszer nem jelzett másolóeszközt feljegyzett leképezések. Hibák a diagram a két piros szakaszban szerepelnek. 

A hamis pozitív szakasz azt jelzi, hogy az utterance (kifejezés) egyezik a leképezés vagy entitás pedig nem kellett volna. A téves negatív azt jelzi, hogy az utterance (kifejezés) nem egyezik a leképezés vagy entitás amikor rendelkeznie kell. 

## <a name="fixing-batch-errors"></a>A batch hibáinak javítása
Ha hiba van a batch-tesztelés, is vagy további beszédmódok hozzáadása egy beszédszándék és/vagy az entitáshoz, LUIS, győződjön meg arról, közötti leképezések megkülönböztetés érdekében további utterances címkézését. Ha hozzáadta a kimondott szöveg, és feliratú őket, és továbbra is get előrejelzési hibák a batch tesztelése, érdemes lehet hozzáadni egy [kifejezéslista](luis-concept-feature.md) szolgáltatása, amely rendelkezik a tartomány-specifikus szöveg szóhasználati, ismerje meg, gyorsabb LUIS segítségével. 

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [kötegelt tesztelése](luis-how-to-batch-test.md)
