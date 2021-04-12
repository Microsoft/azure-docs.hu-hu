---
title: Gráfkeresés lekérdezési szintaxisa
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Designer keresési lekérdezési szintaxisát a csomópontok megkereséséhez a pipeline Graphban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259224"
---
# <a name="graph-search-query-syntax"></a>Gráfkeresés lekérdezési szintaxisa

Ebből a cikkből megismerheti a Azure Machine Learning gráf keresési funkcióit. 

A Graph Search lehetővé teszi, hogy gyorsan navigáljon egy csomóponthoz, amikor hibakeresést végez, vagy felépít egy folyamatot. Megadhatja a kulcs szót vagy lekérdezést az eszköztár beviteli mezőjében, vagy a keresés elindításához a bal oldali panel keresés lapján. Az összes egyező eredmény sárga színnel jelenik meg a vásznon, és ha kijelöl egy eredményt a bal oldali panelen, a vászonhoz tartozó csomópont vörös színnel lesz kiemelve.

![A Graph keresési felületét ábrázoló képernyőkép](media/search/graph-search-0322.png)

A Graph Search támogatja a teljes szöveges kulcsszavas keresést a csomópont neve és megjegyzései alapján. A csomópont-tulajdonságot is szűrheti, például runStatus, időtartam, computeTarget. A kulcsszavas keresés a Lucene-lekérdezésen alapul. A teljes keresési lekérdezés így néz ki:  

**[[Lucene-lekérdezés] | [lekérdezés szűrése]]** 

Használhat Lucene-lekérdezést vagy szűrési lekérdezést is. Mindkét esetben használja az **|** elválasztót. A szűrési lekérdezés szintaxisa szigorúbb, mint a Lucene-lekérdezés. Ha tehát az ügyfél bemenete is értelmezhető, a rendszer a szűrő lekérdezést fogja alkalmazni.

Ez például olyan `data OR model | compute in {cpucluster}` csomópontok keresése, amelyekben a név vagy a Megjegyzés tartalmazza a (z `data` `model` ), és a számítás cpucluster.
 

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
- publish
- tags

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