---
title: Machine Learning algoritmus Cheat Sheet-Designer
titleSuffix: Azure Machine Learning
description: A nyomtatható Machine Learning algoritmusok Cheat Sheet segítségével kiválaszthatja a megfelelő algoritmust a prediktív modellhez Azure Machine Learning Designerben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
adobe-target: true
ms.openlocfilehash: 730fcf3fb552b92a02b255ad654f64d5ff7a92fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100367056"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>A Azure Machine Learning Designer Machine Learning algoritmushoz tartozó Cheat Sheet

A **Azure Machine learning algoritmust tartalmazó Cheat Sheet** segítségével kiválaszthatja a tervezőtől a prediktív elemzési modellhez megfelelő algoritmust.

A Azure Machine Learning a **besorolási** _, az _*_ajánlott rendszerek_*_, a _*_fürtözés_*_, a _*_rendellenességek észlelése_*_, a _*_regresszió_*_ és a _ *_text Analytics_** termékcsaládok nagy könyvtára. Mindegyik úgy van kialakítva, hogy más típusú gépi tanulási problémát lehessen kezelni.

További útmutatás: [algoritmusok kiválasztása](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Letöltés: Machine Learning algoritmus Cheat Sheet

**Töltse le a Cheat lapot itt: [Machine learning algoritmus Cheat Sheet (11x17 in)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning algoritmust tartalmazó Cheat Sheet: útmutató Machine Learning algoritmus kiválasztásához.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Töltse le és nyomtassa ki a Machine Learning algoritmust tartalmazó Cheat lapot a bulvárlap méretében, hogy az hasznos legyen, és kérjen segítséget az algoritmus kiválasztásában.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>A Machine Learning algoritmust tartalmazó Cheat adatlap használata

Az ebben az algoritmusban felajánlott javaslatok megközelítik a szabályokat. Néhány lehet hajlított, és néhány nyíltan megsérthető. Ez a Cheat-táblázat kiindulási pontot javasol. Ne féljen, hogy az adatain alapuló több algoritmus között ne kelljen Head-to-Head versenyt futtatni. Egyszerűen nem helyettesíthető az egyes algoritmusok alapelveinek és az adatait létrehozó rendszernek az értelmezése.

Minden gépi tanulási algoritmusnak saját stílusa vagy induktív torzítása van. Egy adott probléma esetén több algoritmus is lehetséges, és az egyik algoritmus jobban illeszkedik, mint a többi. Ez azonban nem mindig lehet előre, ami a legjobb illeszkedés. Ilyen esetekben több algoritmus is szerepel a Cheat táblázatban. Egy megfelelő stratégia egy algoritmus kipróbálására szolgál, és ha az eredmények még nem kielégítőek, próbálkozzon a többivel. 

Ha többet szeretne megtudni a Azure Machine Learning Designer algoritmusait illetően, ugorjon az [algoritmus és modul hivatkozására](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Gépi tanulás típusa

A gépi tanulásnak három fő kategóriája van: a *felügyelt tanulás*, a nem *felügyelt tanulás* és a *megerősítő tanulás*.

### <a name="supervised-learning"></a>Felügyelt tanulás

A felügyelt tanulásban az egyes adatpontok címkéje vagy kategóriája vagy értéke van társítva. Egy kategorikus címke például egy "Cat" vagy "Dog" típusú képet rendel hozzá. Az érték címkére példa egy használt autóhoz tartozó eladási díj. A felügyelt tanulás célja, hogy több, hasonló címkével ellátott példát tanulmányozzon, majd a jövőbeli adatpontokra vonatkozó előrejelzéseket lehessen készíteni. Például azonosíthatja az új fényképeket a megfelelő állattal, vagy pontos eladási árakat rendelhet más használt autókhoz. Ez egy népszerű és hasznos típusú gépi tanulás.

### <a name="unsupervised-learning"></a>Nem felügyelt tanulás

A nem felügyelt tanulásban az adatpontokhoz nem tartoznak címkék. Ehelyett egy nem felügyelt tanulási algoritmus célja az, hogy valamilyen módon szervezze az adatmennyiséget, vagy leírja a szerkezetét. A nem felügyelt tanulási csoportokba tartozó adatcsoportok, mint K-azt jelenti, hogy nem, vagy különböző módokon keresi az összetett adatnézeteket, így egyszerűbbnek tűnik.

### <a name="reinforcement-learning"></a>Megerősítő tanulás

A megerősítő tanulásban az algoritmus az egyes adatpontokra adott válaszként kiválaszt egy műveletet. A robotika közös megközelítése, amelyben egy adott időpontban az érzékelő beolvasása egy adatpont, és az algoritmusnak a robot következő műveletét kell választania. A eszközök internetes hálózata-alkalmazások számára is természetes. A tanulási algoritmus rövid idő múlva egy jutalom jelzést is kap, amely azt jelzi, hogy milyen jó döntés volt. A jel alapján az algoritmus módosítja a stratégiát a legmagasabb jutalom elérése érdekében. 

## <a name="next-steps"></a>Következő lépések

* Az [algoritmusok kiválasztásával](how-to-select-algorithms.md) kapcsolatos további útmutatásért lásd:

* [Ismerkedjen meg a studióval Azure Machine learning és az Azure Portal](overview-what-is-azure-ml.md).

* [Oktatóanyag: előrejelzési modell létrehozása Azure Machine learning Designerben](tutorial-designer-automobile-price-train-score.md).

* [Ismerkedjen meg a Deep learning és a Machine learning](concept-deep-learning-vs-machine-learning.md)szolgáltatással.
