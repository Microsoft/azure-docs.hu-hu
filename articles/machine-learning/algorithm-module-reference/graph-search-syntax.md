---
title: Gráfkeresés lekérdezési szintaxisa
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Designer keresési lekérdezési szintaxisát a csomópontok kereséséhez a folyamat gráfban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420767"
---
# <a name="graph-search-query-syntax"></a>Gráfkeresés lekérdezési szintaxisa

Ebben a cikkben megismerheti a Graph keresési lekérdezés szintaxisát Azure Machine Learning. A gráf keresési funkciója lehetővé teszi a csomópontok keresését a név és a tulajdonságok alapján. 

 ![Példa a Graph keresési felületét bemutató animált képernyőképre](media/search/graph-search.gif)

A Graph Search támogatja a teljes szöveges kulcsszavas keresést a csomópont neve és megjegyzései alapján. A csomópont-tulajdonsághoz (például runStatus, időtartam, computeTarget) is szűrőket használhat. A kulcsszavas keresés a Lucene-lekérdezésen alapul. A teljes keresési lekérdezés így néz ki:  

**[Lucene-lekérdezés | [lekérdezés szűrése]** 

Használhat Lucene-lekérdezést vagy szűrési lekérdezést is. Mindkét esetben használja az **|** elválasztót. A szűrési lekérdezés szintaxisa szigorúbb, mint a Lucene-lekérdezés. Ha tehát az ügyfél bemenete is értelmezhető, a rendszer a szűrő lekérdezést fogja alkalmazni.

 

## <a name="lucene-query"></a>Lucene-lekérdezés

A Graph Search a Lucene egyszerű lekérdezést használja teljes szöveges keresési szintaxisként a "Name" és a "comment" csomóponton. A következő Lucene-operátorok támogatottak:

 
- ÉS/VAGY
- Helyettesítő karakteres egyezés **?** és **\*** operátorok.

### <a name="examples"></a>Példák

- Egyszerű keresés: `JSON Data`

- ÉS/VAGY: `JSON AND Validation`

- Több és/vagy: `(JSON AND Validation) OR (TSV AND Training)`

 
- Helyettesítő karakter: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> "*" Karaktert tartalmazó Lucene-lekérdezés nem indítható el.

##  <a name="filter-query"></a>Lekérdezés szűrése

 
A szűrési lekérdezések a következő mintát használják:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
A következő csomópont-tulajdonságokat használhatja kulcsként:

- runStatus
- számítás
- duration
- újbóli használatának

És használja a következő operátorokat:

- Nagyobb vagy egyenlő: `>=`
- Kisebb vagy egyenlő: `<=`
- Nagyobb `>`
- Kisebb `<`
- Egyenlő `==`
- Tartalmaz `=`
- NotEqual: `!=`
- A `in`

 
 

### <a name="example"></a>Példa

- időtartam > 100;
- állapot {failed, NotStarted}
- számítás {GPU-cluster}; runStatus {Completed}

## <a name="technical-notes"></a>Technikai megjegyzések

- Több szűrő közötti kapcsolat: "és"
- Ha `>=,  >,  <, or  <=` a lehetőséget választja, a rendszer automatikusan a szám típusúra konvertálja az értékeket. Ellenkező esetben a rendszer a karakterlánc-típusokat használja az összehasonlításhoz.
- Minden karakterlánc típusú érték esetén az eset nem különbözteti meg az összehasonlítást.
- A "in" operátor egy gyűjteményt vár értékként, a gyűjtemény szintaxisa `{name1, name2, name3}`
- A szóköz figyelmen kívül lesz hagyva a kulcsszavak között