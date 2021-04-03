---
title: 'Ismétlődő sorok eltávolítása: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan távolíthatja el a lehetséges duplikált adatokat az adatkészletből a Azure Machine Learning duplikált sorok eltávolítása moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: bf35d08128aa8a3e8f545ed7184866694219f2cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905222"
---
# <a name="remove-duplicate-rows-module"></a>Ismétlődő sorok moduljának eltávolítása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal távolíthatja el a lehetséges duplikált adatokat egy adatkészletből.

Tegyük fel például, hogy az adatok úgy néznek ki, mint a következő, és a betegek több rekordját jelöli. 

| PatientID | Monogram| Nem|Életkor|Elismerte|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb.|
|4| F.M.| M| 23| Feb.|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Egyértelmű, hogy ez a példa több, potenciálisan ismétlődő adattal rendelkező oszlopot tartalmaz. Függetlenül attól, hogy valóban duplikált elemek-e, az adatok ismerete függ. 

+ Előfordulhat például, hogy sok betegnek ugyanaz a neve. Nem távolítja el a duplikált elemeket bármely név oszlop használatával, csak az **azonosító** oszlopot. Így csak az ismétlődő azonosító értékekkel rendelkező sorok vannak kiszűrve, függetlenül attól, hogy a páciensek neve megegyezik-e.

+ Másik lehetőségként dönthet úgy is, hogy engedélyezi az ismétlődéseket az azonosító mezőben, és más fájlok kombinációját használja az egyedi rekordok, például az utónév, a vezetéknév, az életkor és a nemek kereséséhez.  

Ha meg szeretné határozni, hogy egy sor duplikált-e vagy sem, akkor adja meg a **kulcsként** használni kívánt egyetlen oszlopot vagy oszlopokat. Két sor csak akkor tekinthető ismétlődőnek, ha az **összes** kulcs oszlop értékei egyenlőek. Ha bármelyik sorban hiányzik a **kulcsok** értéke, nem lesznek ismétlődő sorok. Ha például a nemek és a kor értéke kulcsokként van beállítva a fenti táblázatban, a 6. és a 7. sor nem duplikált sorokból áll, mert az életkorban hiányzik az érték.

Amikor futtatja a modult, létrehoz egy jelölt adatkészletet, és olyan sorok halmazát adja vissza, amelyek nem rendelkeznek ismétlődéssel a megadott oszlopok között.

> [!IMPORTANT]
> A forrás-adatkészlet nem módosult; Ez a modul egy új adatkészletet hoz létre, amely a megadott feltételek alapján szűri a duplikált elemek kizárását.

## <a name="how-to-use-remove-duplicate-rows"></a>Ismétlődő sorok eltávolításának használata

1. Adja hozzá a modult a folyamathoz. Az **ismétlődő sorok eltávolítása** modult az **adatátalakítás**, a **manipuláció** lehetőség alatt találja.  

2. Kösse össze az adatkészletet, amelynek ismétlődő sorait szeretné megkeresni.

3. A **Tulajdonságok** ablaktáblában a **kulcs oszlop kiválasztása szűrő kifejezés** alatt kattintson az **oszlop kiválasztásának indítása** lehetőségre az ismétlődések azonosításához használandó oszlopok kiválasztásához.

    Ebben a kontextusban a **kulcs** nem egy egyedi azonosítót jelent. Az oszlop Választójának használatával kiválasztott összes oszlop **kulcs oszlopként** van megjelölve. Az összes nem kijelölt oszlop nem kulcsfontosságú oszlopnak minősül. A kulcsként kiválasztott oszlopok kombinációja határozza meg a rekordok egyediségét. (Úgy gondolja, hogy olyan SQL-utasításként működik, amely több egyenlő illesztést használ.)

    Angol nyelvű Példák:

    + "Szeretném biztosítani, hogy az azonosítók egyediek legyenek": csak az azonosító oszlopot válassza ki.
    + "Szeretném biztosítani, hogy az utónév, a vezetéknév és az azonosító kombinációja egyedi legyen": mindhárom oszlop kijelölése.

4. Az **első ismétlődő sor megőrzése** jelölőnégyzet bejelölésével jelezheti, hogy melyik sort kell visszaadnia az ismétlődések keresésekor:

    + Ha be van jelölve, a rendszer az első sort adja vissza, és a többi elvet. 
    + Ha törli ezt a beállítást, a rendszer az utolsó ismétlődő sort tárolja az eredmények között, másokat pedig elvet. 

5. A folyamat elküldése.

6. Az eredmények áttekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés** lehetőséget. 

> [!TIP]
> Ha az eredmények nehezen érthetőek, vagy ha ki szeretne zárni egyes oszlopokat a megfontolásból, eltávolíthatja az oszlopokat az [adatkészlet kiválasztása](./select-columns-in-dataset.md) modulban.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 