---
title: Funkció kiválasztása a csoportos adatelemzési folyamatban
description: Ismerteti a szolgáltatások kiválasztásának célját, és példákat nyújt a szerepkörre a gépi tanulás adatfejlesztési folyamatában.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321327"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funkcióválasztás a csoportos adatelemzési folyamatban (TDSP)
Ez a cikk ismerteti a szolgáltatások kiválasztásának célját, és példákat nyújt a szerepkörére a gépi tanulás adatfejlesztési folyamatában. Ezek a példák a Azure Machine Learning Studioból származnak.

A szolgáltatások mérnöki és kiválasztási funkciója a csoportos adatelemzési folyamat (TDSP) egyik része, amely a következő cikkben található: [Mi a csoportos adatelemzési folyamat?](overview.md). A funkciók mérnöki és kiválasztási funkciója a TDSP **szolgáltatások fejlesztése** lépésének részei.

* **szolgáltatás-mérnöki**: Ez a folyamat megkísérli további releváns funkciók létrehozását az adatok meglévő nyers szolgáltatásaiból, és a prediktív teljesítmény növelését a tanulási algoritmusra.
* **funkció kiválasztása**: Ez a folyamat kiválasztja az eredeti adatszolgáltatások kulcsának részhalmazát, hogy megpróbálja csökkenteni a dimenzióját.

A rendszer általában a szolgáltatások **fejlesztését** alkalmazza a további funkciók létrehozásához, majd a **funkció kiválasztása** lépéssel megszünteti a lényegtelen, redundáns vagy szorosan korrelált funkciókat.

## <a name="filter-features-from-your-data---feature-selection"></a>Funkciók szűrése az adatokból – funkció kiválasztása
A szolgáltatás kiválasztása besorolási vagy regressziós feladatokhoz is használható. A cél az, hogy kiválassza az eredeti adatkészlet azon funkcióinak egy részhalmazát, amelyek csökkentik a méreteiket a minimálisan szükséges funkciók használatával, hogy az az adatok maximális mennyiségét képviseljék. A funkciók ezen részhalmaza a modell betanítására szolgál. A funkció kiválasztása két fő célt szolgál.

* Első lépésként a funkció kiválasztása gyakran növeli a besorolási pontosságot a lényegtelen, redundáns vagy szorosan korrelált funkciók kiiktatásával.
* Másodszor csökkenti a funkciók számát, ami hatékonyabbá teszi a modell betanítási folyamatát. A hatékonyság fontos az olyan tanulók számára, akik a támogatási vektoros gépek betanítására költségesek.

Bár a funkciók kiválasztása csökkenti a modell betanításához használt adatkészlet funkcióinak számát, a "dimenzióját Reduction" kifejezés nem hivatkozik rá. A szolgáltatás kiválasztási módszerei az adatok eredeti funkcióinak egy részhalmazát kinyerik a módosítás nélkül.  A dimenzióját-csökkentési módszerek olyan mérnöki funkciókat alkalmaznak, amelyek az eredeti funkciókat át tudják alakítani, és így módosíthatják azokat. A dimenzióját-csökkentési módszerek példái közé tartozik a fő összetevő-elemzés, a kanonikus korrelációs elemzés és az egyes számú értékek elbomlása.

Többek között a felügyelt környezetekben az egyik legszélesebb körben alkalmazott szolgáltatás-kiválasztási módszer a "szűrésen alapuló funkció kiválasztása". Az egyes szolgáltatások és a célként megadott attribútumok közötti korreláció kiértékelésével ezek a módszerek statisztikai mértéket alkalmaznak az egyes funkciókhoz tartozó pontszámok hozzárendeléséhez. A funkciók ezután a pontszám szerint vannak rangsorolva, amely segítségével megadható a küszöbérték egy adott szolgáltatás megőrzése vagy kiküszöbölése érdekében. Az ezekben a módszerekben használt statisztikai intézkedések közé tartozik például a személy korrelációja, a kölcsönös információ és a Chi négyzetes teszt.

Azure Machine Learning Studio a funkciók kiválasztásához modulok vannak megadva. Ahogy az az alábbi ábrán is látható, ezek a modulok a [Filter-alapú funkciók kiválasztása][filter-based-feature-selection] és a [Fisher lineáris diszkrimináns elemzése][fisher-linear-discriminant-analysis].

![Funkció-kiválasztási modulok](./media/select-features/feature-Selection.png)

Vegyük például a [Filter-alapú funkciók kiválasztási][filter-based-feature-selection] moduljának használatát. A kényelmes használat érdekében folytassa a Text Mining példa használatát. Tegyük fel, hogy egy regressziós modellt szeretne létrehozni a 256-szolgáltatások egy készletének létrehozása után a [szolgáltatás kivonatolási][feature-hashing] modulján keresztül, és a válasz változó a "Col1", amely az 1 és 5 közötti könyv-felülvizsgálati minősítéseket tartalmazza. A "szolgáltatás pontozási metódusa" beállítás "Pearson korreláció" értékre állításával a "cél oszlop" "Col1", a "kívánt funkciók száma" pedig 50-re van állítva. Ezt követően a modul [Filter-alapú funkciója][filter-based-feature-selection] a 50 funkciókat tartalmazó adatkészletet hoz létre, amely a "Col1" célként megadott attribútummal együtt. Az alábbi ábra a kísérlet folyamatát és a bemeneti paramétereket mutatja be:

![Filter-Based funkció kiválasztási moduljának tulajdonságai](./media/select-features/feature-Selection1.png)

A következő ábra az eredményül kapott adatkészleteket mutatja be:

![A szűrő alapú funkciók kiválasztási moduljának eredményül kapott adatkészlete](./media/select-features/feature-Selection2.png)

Mindegyik funkció a saját maga és a "Col1" cél attribútum közötti Pearson-korreláción alapul. A legfontosabb pontszámokkal rendelkező funkciók megmaradnak.

A kiválasztott szolgáltatások megfelelő pontszámai az alábbi ábrán láthatók:

![A Filter-alapú funkciók kiválasztási moduljának pontszámai](./media/select-features/feature-Selection3.png)

Ennek a [szűrő-alapú funkció-kiválasztási modulnak a Kiválasztásával][filter-based-feature-selection] 50-ből 256-funkciók közül választhat, mivel azok a leginkább korrelált funkciókkal rendelkeznek a "Col1" megcélzott változóval, a "Pearson korreláció" pontozási módszer alapján.

## <a name="conclusion"></a>Összegzés
A funkciók mérnöki és funkcióinak kiválasztása két gyakran használt és kiválasztott szolgáltatás, így növelheti az adatokban található kulcsfontosságú információk kinyeréséhez szükséges képzési folyamat hatékonyságát. Emellett javítják ezeknek a modelleknek a hatékonyságát a bemeneti adatok pontos osztályozása érdekében, valamint a kamatok kiszámításának hatékonyabbá válását. A funkciók mérnöki és kijelölési funkciói is kombinálhatók, így a tanulás több számítási feltételt is igénybe vehet. Ezt a modell kalibrálásához vagy betanításához szükséges szolgáltatások számának növelésével és csökkentésével végzi. Matematikailag a modell betanítására kiválasztott funkciók a független változók minimális készlete, amelyek ismertetik az adatmintázatokat, majd megjósolják az eredmények sikerességét.

Nem mindig feltétlenül kell végrehajtania a szolgáltatások mérnöki vagy szolgáltatásbeli kijelölését. Szükség van-e rá, vagy nem függ az összegyűjtött adatoktól, a kiválasztott algoritmustól és a kísérlet céljától.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis