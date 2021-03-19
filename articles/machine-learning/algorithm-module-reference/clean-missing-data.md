---
title: 'Hiányzó adatértékek: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan távolíthatja el, cserélheti vagy következtetheti ki a hiányzó értékeket a Azure Machine Learning tiszta hiányzó adatmoduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5b7943b2026d640ae7e5d119e165bd752ae2fe7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898831"
---
# <a name="clean-missing-data-module"></a>Hiányzó adatmodul törlése

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal törölheti, lecserélheti vagy következtetheti ki a hiányzó értékeket. 

Az adatszakértők gyakran tekintik meg a hiányzó értékek adatait, majd különböző műveleteket hajtanak végre az adatok kijavításához vagy új értékek beszúrásához. Az ilyen tisztítási műveletek célja, hogy megakadályozza a modell betanításakor felmerülő hiányzó adatok által okozott problémákat. 

Ez a modul több típusú műveletet támogat a "tisztítás" hiányzó értékekhez, beleértve a következőket:

+ Hiányzó értékek cseréje helyőrző, középérték vagy más értékre
+ A hiányzó értékeket tartalmazó sorok és oszlopok teljes eltávolítása
+ Statisztikai módszerek alapján kikövetkeztetett értékek


A modul használata nem módosítja a forrás adatkészletet. Ehelyett létrehoz egy új adatkészletet a munkaterületen, amelyet a következő munkafolyamatban használhat. Azt is megteheti, hogy újra felhasználja az új, megtisztított adatkészletet.

Ez a modul a hiányzó értékek tisztításához használt átalakítás definícióját is megjeleníti. Ezt az átalakítást újrahasználhatja más, azonos sémával rendelkező adatkészleteken az [átalakítási modul alkalmazása](./apply-transformation.md) lehetőség használatával.  

## <a name="how-to-use-clean-missing-data"></a>A tiszta hiányzó adatértékek használata

Ez a modul lehetővé teszi a tisztítási művelet definiálását. A tisztítási műveletet úgy is mentheti, hogy később új adatként is alkalmazható legyen. A takarítási folyamat létrehozásáról és mentéséhez a következő részekben talál további információt: 
 
+ [Hiányzó értékek cseréje](#replace-missing-values)
  
+ [Takarítási átalakítás alkalmazása új adatértékekre](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> A hiányzó értékek kezelésére használt tisztítási módszer jelentősen befolyásolhatja az eredményeket. Javasoljuk, hogy kísérletezzen különböző módszerekkel. Vegye fontolóra egy adott módszer használatának indoklását, valamint az eredmények minőségét.

### <a name="replace-missing-values"></a>Hiányzó értékek cseréje  

Minden alkalommal, amikor a  [tiszta hiányzó](./clean-missing-data.md) adatmodult alkalmazza egy adathalmazra, a rendszer az összes kiválasztott oszlopra alkalmazza ugyanazt a tisztítási műveletet. Ezért ha eltérő módszerekkel kell megtisztítani a különböző oszlopokat, használja a modul különálló példányait.

1.  Vegye fel a [tiszta hiányzó](./clean-missing-data.md) adatmodult a folyamatba, és kapcsolja össze a hiányzó értékeket tartalmazó adatkészletet.  
  
2.  A **tisztítani kívánt oszlopok** esetében válassza ki azokat az oszlopokat, amelyek tartalmazzák a hiányzó értékeket. Több oszlopot is kiválaszthat, de az összes kijelölt oszlopban ugyanazt a helyettesítő metódust kell használnia. Ezért általában a sztring oszlopokat és a numerikus oszlopokat külön kell megtisztítani.

    Például a hiányzó értékek kereséséhez az összes numerikus oszlopban:

    1. Válassza ki a **tiszta hiányzó** adatmodult, majd kattintson a modul jobb oldali paneljén található **Szerkesztés oszlopra** .

    3. A **include (Belefoglalás**) mezőben válassza ki az **oszlopok típusai** elemet a legördülő listából, majd válassza a **numerikus** lehetőséget. 
  
    Minden kiválasztott tisztítási vagy helyettesítési módszernek a kijelölés **összes** oszlopára érvényesnek kell lennie. Ha valamelyik oszlopban lévő adatok nem kompatibilisek a megadott művelettel, a modul hibát ad vissza, és leállítja a folyamatot.
  
3.  A **minimális hiányzó érték aránynál** határozza meg a művelet végrehajtásához szükséges hiányzó értékek minimális számát.  
  
    Ezt a kapcsolót a **hiányzó értékek maximális arányával** együtt használva definiálhatja azokat a feltételeket, amelyekben a tisztítási művelet elvégzése történik az adatkészleten. Ha túl sok vagy túl kevés sor hiányzik az értékek közül, a művelet nem hajtható végre. 
  
    A beírt szám a hiányzó értékek **arányát** jelöli az oszlopban lévő összes értékre. Alapértelmezés szerint a **hiányzó érték arány** tulajdonság értéke 0. Ez azt jelenti, hogy a hiányzó értékek akkor is törlődnek, ha csak egy hiányzó érték van megadva. 

    > [!WARNING]
    > Ezt az állapotot minden egyes oszlopnak teljesítenie kell ahhoz, hogy a megadott művelet vonatkozzon. Tegyük fel például, hogy három oszlopot jelölt ki, majd a hiányzó értékek minimális arányát .2 (20%) értékre állítja, de csak egy oszlop ténylegesen 20% hiányzó értéket tartalmaz. Ebben az esetben a karbantartási művelet csak az oszlopra vonatkozik, amely több mint 20% hiányzó értéket tartalmaz. Ezért a többi oszlop változatlan marad.
    > 
    > Ha kétségei vannak, hogy a hiányzó értékek módosultak-e, válassza a **hiányzó érték jelző oszlop létrehozása** lehetőséget. Az adatkészlethez egy oszlop kerül, amely jelzi, hogy az egyes oszlopok megfelelnek-e a minimális és a maximális tartományhoz megadott feltételeknek.  
  
4. A **hiányzó érték maximális arányához** határozza meg a művelet végrehajtásához szükséges hiányzó értékek maximális számát.   
  
    Előfordulhat például, hogy a hiányzó érték-helyettesítést csak akkor szeretné végrehajtani, ha a sorok 30%-a vagy annál kevesebb értéket tartalmaz, de az értékeket a következőképpen hagyja, ha a sorok több mint 30%-a hiányzó értékkel rendelkezik.  
  
    A számot a hiányzó értékek arányát adja meg az oszlopban lévő összes értékhez. Alapértelmezés szerint a **hiányzó értékek maximális arányának** értéke 1. Ez azt jelenti, hogy a hiányzó értékek akkor is törlődnek, ha az oszlopban szereplő értékek 100%-a hiányzik.  
  
   
  
5. A **tisztítási mód** beállításnál válassza a következő lehetőségek egyikét a hiányzó értékek lecseréléséhez vagy eltávolításához:  
  
  
    + **Egyéni helyettesítési érték**: ezzel a beállítással megadhat egy helyőrző értéket (például 0 vagy na), amely az összes hiányzó értékre vonatkozik. A helyettesítőként megadott értéknek kompatibilisnek kell lennie az oszlop adattípusával.
  
    + **Csere az Mean kifejezéssel**: kiszámítja az oszlop középértékét, és a középértéket használja az oszlopban szereplő összes hiányzó értékhez tartozó helyettesítő értékként.  
  
        Csak olyan oszlopokra vonatkozik, amelyek egész, dupla vagy logikai adattípussal rendelkeznek.  
  
    + **Lecserélés közepes** értékre: kiszámítja az oszlop középértékét, és a középértéket használja az oszlopban szereplő hiányzó értékek pótlására.  
  
        Csak olyan oszlopokra vonatkozik, amelyek egész vagy dupla adattípussal rendelkeznek. 
  
    + **Csere** a következő módra: kiszámítja az oszlop módját, és a módot használja helyettesítő értékként az oszlopban szereplő összes hiányzó értékhez.  
  
        Egész, dupla, logikai vagy kategorikus adattípusú oszlopokra vonatkozik. 
  
    + **Teljes sor eltávolítása**: teljesen eltávolítja az adatkészlet bármely olyan sorát, amely egy vagy több hiányzó értéket tartalmaz. Ez akkor lehet hasznos, ha a hiányzó érték véletlenszerűen van megadva.  
  
    + **Teljes oszlop eltávolítása**: teljesen eltávolítja az adatkészlet minden olyan oszlopát, amely egy vagy több hiányzó értékkel rendelkezik.  
  
    
  
6. Ha a beállítás, az **Egyéni helyettesítési** érték lehetőség van kiválasztva, a lehetőség **helyettesítő értéke** elérhető. Írjon be egy új értéket, amelyet helyettesítő értékként kíván használni az oszlopban található összes hiányzó értékhez.  
  
    Vegye figyelembe, hogy ezt a beállítást csak egész számmal, dupla, logikai vagy sztring típusú oszlopokban használhatja.
  
7. **Hiányzó érték jelző oszlop létrehozása**: válassza ezt a beállítást, ha azt szeretné, hogy az oszlopban szereplő értékek megfelelnek a hiányzó érték tisztításának feltételeinek. Ez a beállítás különösen akkor hasznos, ha új tisztítási műveletet állít be, és szeretné meggyőződni arról, hogy az a tervezett módon működik-e.
  
8. A folyamat elküldése.

### <a name="results"></a>Results (Eredmények)

A modul két kimenetet ad vissza:  

-   **Tisztított adatkészlet**: a kijelölt oszlopokból álló adatkészlet, amely a megadott módon kezelt, hiányzó értékekkel, valamint egy kijelző oszlopával együtt, ha ezt a beállítást választotta.  

    A tisztításra kijelölt oszlopok is áthaladnak.  
  
-  **Tisztító transzformáció**: a tisztításhoz használt adatátalakítás, amely a munkaterületre menthető, és később is alkalmazható az új adataira.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Mentett tisztítási művelet alkalmazása új adatértékre  

Ha gyakran kell megismételni a tisztítási műveleteket, javasoljuk, hogy mentse a receptet Adattisztításra *átalakításként*, hogy újra ugyanazzal az adatkészlettel használja fel őket. A takarítási transzformáció mentése különösen akkor hasznos, ha gyakran újra kell importálni, majd törölni kell az azonos sémával rendelkező információkat.  
      
1.  Adja hozzá az [átalakítási modul alkalmazása](./apply-transformation.md) a folyamathoz lehetőséget.  
  
2.  Adja hozzá a tisztítani kívánt adatkészletet, és kapcsolja össze az adatkészletet a jobb oldali bemeneti porthoz.  
  
3.  Bontsa ki az **átalakítások** csoportot a tervező bal oldali paneljén. Keresse meg a mentett átalakítást, és húzza a folyamatba.  

4.  Kapcsolja össze a mentett átalakítást az [alkalmazás átalakításának](./apply-transformation.md)bal oldali bemeneti portjával. 

    Ha mentett transzformációt alkalmaz, nem választhatja ki azokat az oszlopokat, amelyekre az átalakítást alkalmazni szeretné. Ennek oka, hogy a transzformáció már definiálva van, és automatikusan az eredeti műveletben megadott oszlopokra vonatkozik.

    Tegyük fel azonban, hogy létrehozott egy átalakítást a numerikus oszlopok egy részhalmazán. Ezt az átalakítást alkalmazhatja vegyes oszlop típusú adathalmazra anélkül, hogy hibát kellene felvennie, mert a hiányzó értékek csak a megfelelő numerikus oszlopokban változnak.

6.  A folyamat elküldése.  

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 