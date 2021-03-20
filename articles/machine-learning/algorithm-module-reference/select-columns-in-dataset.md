---
title: 'Oszlopok kiválasztása az adatkészletben: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az Oszlopok kiválasztása a Azure Machine Learning adatkészletben modulban az alsóbb rétegbeli műveletekben használandó oszlopok részhalmazának kiválasztásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90890293"
---
# <a name="select-columns-in-dataset-module"></a>Oszlopok kiválasztása az adatkészlet modulban

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal kiválaszthatja az alsóbb rétegbeli műveletekben használandó oszlopok egy részhalmazát. A modul nem távolítja el fizikailag az oszlopokat a forrás adatkészletből; Ehelyett egy adatbázis- *nézethez* vagy *kivetítéshez* hasonlóan az oszlopok egy részhalmazát hozza létre.

Ez a modul akkor lehet hasznos, ha korlátozni szeretné az alsóbb rétegbeli művelethez rendelkezésre álló oszlopokat, vagy ha csökkenteni kívánja az adatkészlet méretét a szükségtelen oszlopok eltávolításával.

Az adatkészlet oszlopai ugyanabban a sorrendben jelennek meg, mint az eredeti adatokat, még akkor is, ha más sorrendben adja meg őket.

## <a name="how-to-use"></a>Használat

Ez a modul nem tartalmaz paramétereket. Az oszlop választóval kiválaszthatja a belefoglalni vagy kizárni kívánt oszlopokat.

### <a name="choose-columns-by-name"></a>Oszlopok kiválasztása név szerint

A modulban több lehetőség is van az oszlopok név szerinti kiválasztására: 

+ Szűrés és keresés

    Kattintson a **név szerint** lehetőségre.

    Ha már kitöltött adatkészlet van csatlakoztatva, akkor megjelenik az elérhető oszlopok listája. Ha egyetlen oszlop sem jelenik meg, előfordulhat, hogy az oszlopok listájának megtekintéséhez felsőbb rétegbeli modulokat kell futtatnia.

    A lista szűréséhez írja be a kifejezést a keresőmezőbe. Ha például beírja a betűt `w` a keresőmezőbe, a rendszer szűri a listát a betűt tartalmazó oszlopnevek megjelenítéséhez `w` .

    Jelölje ki az oszlopokat, és kattintson a jobbra mutató nyílra a kijelölt oszlopok a jobb oldali ablaktáblán a listára való áthelyezéséhez.

    + Az oszlopnevek folytonos tartományának kiválasztásához nyomja le a **SHIFT + kattintás** billentyűkombinációt.
    + Ha egyes oszlopokat szeretne hozzáadni a kijelöléshez, nyomja le a **CTRL + kattintás** billentyűkombinációt.

    Kattintson a pipa gombra a mentéshez és a bezáráshoz.

+ Nevek használata más szabályokkal együtt

    Kattintson a **with Rules (szabályokkal** ) lehetőségre.
    
    Válasszon egy szabályt, például egy adott adattípus oszlopainak megjelenítését.

    Ezután kattintson az adott típusú oszlopokra név alapján, hogy hozzáadja őket a kiválasztási listához.

+ Írja be vagy illessze be az oszlopnevek vesszővel tagolt listáját

    Ha az adatkészlet széles, egyszerűbb lehet az indexek vagy a generált Névlista használata ahelyett, hogy külön oszlopokat kellene kiválasztania. Feltételezve, hogy előkészítette a listát előre:

    1. Kattintson a **with Rules (szabályokkal** ) lehetőségre. 
    2. Válassza a **nem oszlopok** lehetőséget, válassza a  **Belefoglalás** lehetőséget, majd kattintson a piros felkiáltójelet tartalmazó szövegmezőbe. 
    3. Illessze be vagy írja be a korábban érvényesített oszlopnevek vesszővel tagolt listáját. A modul nem menthető, ha bármelyik oszlop érvénytelen névvel rendelkezik, ezért ügyeljen arra, hogy a neveket előre ellenőrizze.
    
    Ezzel a módszerrel az oszlopok listáját is megadhatja az index értékei alapján. 

### <a name="choose-by-type"></a>Választás típus szerint

Ha a **with Rules** kapcsolót használja, több feltételt is alkalmazhat az oszlopok kiválasztására. Előfordulhat például, hogy csak numerikus adattípusú szolgáltatások oszlopait kell lekérnie.

A **kezdete** beállítás határozza meg a kiindulási pontot, és fontos az eredmények megismeréséhez. 

+ Ha a **minden oszlop** lehetőséget választja, a rendszer az összes oszlopot hozzáadja a listához. Ezt követően a **kizárás** lehetőséget kell használnia az egyes feltételeknek megfelelő oszlopok *eltávolításához* . 

    Előfordulhat például, hogy az összes oszlopot megkezdi, majd eltávolítja az oszlopokat név vagy típus szerint.

+ Ha a **nincs oszlop** lehetőséget választja, az oszlopok listája üresen elindul. Ezután megadhatja az oszlopok listához való *hozzáadásának* feltételeit. 

    Ha több szabályt alkalmaz, minden feltétel **adalékanyag**. Tegyük fel például, hogy nem oszlopokat ad meg, majd hozzáad egy szabályt az összes numerikus oszlop beolvasásához. Az autó árát tartalmazó adatkészletben 16 oszlopot eredményez. Ezután kattintson a **+** jelre egy új feltétel hozzáadásához, majd válassza a **minden szolgáltatás belefoglalása** lehetőséget. Az eredményül kapott adatkészlet tartalmazza az összes numerikus oszlopot, valamint az összes funkció oszlopát, beleértve a karakterlánc-funkciók egyes oszlopait is.

### <a name="choose-by-column-index"></a>Oszlopos index választása

Az oszlop indexe az oszlopnak az eredeti adatkészleten belüli sorrendjére hivatkozik.

+ Az oszlopok egymás után sorszámozással kezdődnek, 1-től kezdődően.  
+ Egy sor oszlop beszerzéséhez használjon kötőjelet. 
+ A nyílt végű specifikációk (például `1-` vagy `-3` ) nem engedélyezettek.
+ Ismétlődő index-értékek (vagy oszlopnevek) nem engedélyezettek, és hibát okozhatnak.

Tegyük fel például, hogy az adatkészletnek legalább nyolc oszlopa van, az alábbi példák bármelyikét beillesztheti több nem összefüggő oszlop visszaküldéséhez: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

az utolsó példa nem eredményez hibát; azonban az oszlop egyetlen példányát adja vissza `4` .



### <a name="change-order-of-columns"></a>Oszlopok sorrendjének módosítása

Az oszlopok **ismétlődésének és megőrzésének engedélyezése a kijelölésben** üres listával kezdődik, és a név vagy index alapján megadott oszlopokat adja meg. A többi lehetőségtől eltérően, amely mindig a "természetes sorrendben" adja vissza az oszlopokat, ez a beállítás az oszlopokat a név vagy a lista sorrendjében jeleníti meg. 

Például a Col1, Col2, Col3 és Col4 oszlopokkal rendelkező adatkészletekben visszavonhatja az oszlopok sorrendjét, és kihagyhatja a 2. oszlopot a következő felsorolások egyikének megadásával:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 