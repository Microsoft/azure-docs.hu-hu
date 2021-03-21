---
title: 'Adatdarabolás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan oszthat meg egy adatkészletet két különálló készletre a Azure Machine Learning az adatfelosztási modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90907815"
---
# <a name="split-data-module"></a>Adategység felosztása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Az adatkészletek felosztása két különálló készletre az adatfelosztási modul használatával.

Ez a modul akkor hasznos, ha külön kell elkülönítenie az információkat a képzési és tesztelési készletekben. Testre szabhatja az adatmegosztás módját is. Egyes lehetőségek támogatják a véletlenszerű adatmennyiséget. Mások egy adott adattípus vagy modell típusára vannak igazítva.

## <a name="configure-the-module"></a>A modul konfigurálása

> [!TIP]
> A felosztási mód kiválasztása előtt olvassa el az összes lehetőséget a szükséges felosztási típus meghatározásához.
> Ha megváltoztatja a felosztási módot, előfordulhat, hogy az összes többi beállítás alaphelyzetbe áll.

1. Adja hozzá a **feldarabolt** adatmodult a folyamathoz a tervezőben. Ez a modul az **adatátalakítás** alatt, a **minta és a felosztás** kategóriában található.

1. **Felosztási mód**: válasszon az alábbi módok közül, attól függően, hogy milyen típusú adattípussal rendelkezik, és hogyan szeretné felosztani. Az egyes felosztási módok különböző beállításokkal rendelkeznek.

   - **Sorok felosztása**: akkor használja ezt a beállítást, ha csak két részre szeretné osztani az adatterületet. Megadhatja az egyes felosztásokban felvenni kívánt adatmennyiség százalékos arányát. Alapértelmezés szerint az adathalmaz 50/50-re van osztva.

     Az egyes csoportokban lévő sorok kijelölését, valamint rétegzett mintavételezést is használhat. Rétegzett mintavételezés esetén ki kell választania egy olyan adatoszlopot, amelynek az értékeit egyenlően kell kiosztani a két eredmény-adatkészletek között.  

   - **Reguláris kifejezés felosztása**: akkor válassza ezt a lehetőséget, ha az adatkészletet egy érték egyetlen oszlopának tesztelésével szeretné osztani.

     Ha például a hangulat elemzését végzi, a szövegmezőben megtekintheti egy adott terméknév jelenlétét. Ezután feloszthatja az adathalmazt sorokra a cél terméknév és a sorok megjelölése nélkül.

   - **Relatív kifejezés felosztása**: akkor használja ezt a beállítást, ha egy feltételt egy Number oszlopra kíván alkalmazni. A szám lehet egy dátum/idő mező, egy olyan oszlop, amely az életkor vagy a dollár összegét, vagy akár egy százalékot is tartalmaz. Előfordulhat például, hogy az adatkészletet az elemek díjszabása alapján szeretné megosztani, a csoportba tartozó személyeket korcsoport szerint, vagy az adatokat egy naptári dátummal elválasztva.

### <a name="split-rows"></a>Sorok felosztása

1. Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz a tervezőben, és kapcsolódjon a felosztani kívánt adatkészlethez.
  
1. A **felosztási mód** beállításnál válassza a **sorok felosztása** elemet. 

1. **Sorok töredékei az első kimeneti adatkészletben**: ezzel a beállítással határozható meg, hogy hány sor kerül be az első (bal oldali) kimenetbe. Az összes többi sor bekerül a második (jobb oldali) kimenetbe.

   Az arány az első kimeneti adatkészletbe küldendő sorok százalékos arányát jelöli, ezért 0 és 1 közötti decimális számot kell megadnia.
     
   Ha például a **0,75** értéket adja meg értékként, az adatkészlet felosztása 75/25 lesz. Ebben a felosztásban a sorok 75 százaléka lesz elküldve az első kimeneti adatkészletbe. A fennmaradó 25 százalékot a rendszer a második kimeneti adatkészletbe küldi el.
  
1. Válassza ki a **randomizált felosztási** lehetőséget, ha az adatválasztást véletlenszerűen szeretné kijelölni a két csoportban. Ez az előnyben részesített lehetőség, ha képzési és tesztelési adatkészleteket hoz létre.

1. **Véletlenszerű mag**: adjon meg egy nem negatív egész értéket a használni kívánt példányok bájtján elvégeznek-sorozatának elindításához. Ezt az alapértelmezett magot használja a rendszer minden olyan modulban, amely véletlenszerű számokat eredményez. 

   A magok megadásával megismételhetővé válik az eredmények. Ha meg kell ismételnie egy felosztási művelet eredményét, meg kell adnia egy magot a véletlenszám-generátorhoz. Ellenkező esetben a véletlenszerű magok értéke alapértelmezés szerint **0**, ami azt jelenti, hogy a kezdeti mag értékét a rendszer órája szerzi be. Ennek eredményeképpen az adatok eloszlása némileg eltérő lehet minden alkalommal, amikor elvégez egy felosztást. 

1. **Rétegzett felosztás**: ezt a beállítást állítsa **igaz** értékre, hogy a két kimeneti adatkészlet a *rétegek oszlopban* vagy a *csoportosítási kulcs oszlopban* szereplő értékek reprezentatív mintáját tartalmazza. 

   Rétegzett mintavételezés esetén az adatokat a rendszer úgy osztja szét, hogy mindegyik kimeneti adatkészlet nagyjából az egyes célértékek azonos százalékát kapja meg. Előfordulhat például, hogy biztosítani szeretné, hogy a képzés és a tesztelési készletek nagyjából egyensúlyban legyenek az eredmény vagy más oszlop (például a nemek) tekintetében.

1. A folyamat elküldése.


## <a name="select-a-regular-expression"></a>Reguláris kifejezés kiválasztása

1. Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz, és kapcsolja be bemenetként a felosztani kívánt adatkészlethez.  
  
1. A **felosztási mód** beállításnál válassza a **reguláris kifejezés felosztása** elemet.

1. A **reguláris kifejezés** mezőbe írjon be egy érvényes reguláris kifejezést. 
  
   A reguláris kifejezésnek a Python szintaxisát kell követnie reguláris kifejezésekhez.

1. A folyamat elküldése.

   Az Ön által megadott reguláris kifejezés alapján az adatkészlet két sorra van osztva: sorokba, amelyek megfelelnek a kifejezésnek és az összes többi sornak. 

Az alábbi példák bemutatják, hogyan oszthat meg egy adatkészletet a **reguláris kifejezés** lehetőség használatával. 

### <a name="single-whole-word"></a>Egyetlen egész szó 

Ez a példa az első adatkészlet minden olyan sorát tartalmazza, amely az oszlopban lévő szöveget tartalmazza `Gryphon` `Text` . Más sorokat helyez el a **felosztott adatokat** tartalmazó második kimenetbe.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Substring

Ez a példa a megadott karakterláncot az adatkészlet második oszlopában található bármely pozícióban keresi. A pozíciót az 1. index értéke jelöli. A egyezés megkülönbözteti a kis-és nagybetűket.

```text
(\1) ^[a-f]
```

Az első eredmény adatkészlet minden olyan sort tartalmaz, amelyben az index oszlop a következő karakterek egyikével kezdődik:,,,,, `a` `b` `c` `d` `e` `f` . Minden más sor a második kimenetre lesz irányítva.

## <a name="select-a-relative-expression"></a>Relatív kifejezés kiválasztása

1. Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz, és kapcsolja be bemenetként a felosztani kívánt adatkészlethez.
  
1. A **felosztási mód** beállításnál válassza a **relatív kifejezés** lehetőséget.
  
1. A **relációs kifejezés** mezőben adjon meg egy olyan kifejezést, amely egy összehasonlítási műveletet hajt végre egyetlen oszlopon.

   **Numerikus oszlop** esetén:
   - Az oszlop a numerikus adattípusok számát tartalmazza, beleértve a dátum-és időtípusokat is.
   - A kifejezés legfeljebb egy oszlop nevét hivatkozhat.
   - A és a művelethez használja a jel karaktert `&` . A vagy a művelethez használja a pipe karaktert `|` .
   - A következő operátorok támogatottak:,,, `<` `>` `<=` `>=` , `==` , `!=` .
   - A és a használatával nem csoportosíthatjuk a műveleteket `(` `)` .
   
   **Karakterlánc-oszlop** esetén:
   - A következő operátorok támogatottak: `==` , `!=` .

1. A folyamat elküldése.

   A kifejezés két sorból osztja el az adatkészletet: a feltételnek megfelelő értékeket tartalmazó sorok és az összes többi sor.

Az alábbi példák bemutatják, hogyan oszthat meg egy adatkészletet a **relatív kifejezés** lehetőséggel az **adatfelosztási** modulban.  

### <a name="calendar-year"></a>Naptári év

Gyakori forgatókönyv, hogy az adatkészletet évek szerint osztják el. A következő kifejezés kiválasztja azokat a sorokat, amelyekben az oszlopban szereplő értékek `Year` nagyobbak, mint `2010` .

```text
\"Year" > 2010
```

A Date kifejezésnek az adatoszlopban szereplő összes dátumérték szerepelnie kell. Az adatok oszlopban szereplő dátumok formátumának konzisztensnek kell lennie. 

Például a formátumot használó Date (dátum) oszlopban `mmddyyyy` a kifejezésnek a következőhöz hasonlónak kell lennie:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Oszlop indexe

A következő kifejezés azt mutatja be, hogyan használható az oszlop indexe az adatkészlet első oszlopában lévő összes olyan sor kiválasztására, amelyek értéke nem lehet kisebb, mint 30, de nem egyenlő 20 értékkel.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
