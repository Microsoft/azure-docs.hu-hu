---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a ARCUL ütötte modult a Azure Machine Learningban az adathalmazok alacsony előfordulású példáinak számának növelésével a túlmintavételezés használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 501f3e8946023d28d67a33fbbfca661afbc6306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898261"
---
# <a name="smote"></a>SMOTE

Ez a cikk azt ismerteti, hogyan használható a ARCUL ütötte modul a Azure Machine Learning Designerben a gépi tanuláshoz használt adatkészletben lévő, az általa képviselt esetek számának növelésére. A ARCUL ütötte jobb módszer a ritka esetek számának növelésére, mint a meglévő esetek egyszerű duplikálása.  

A ARCUL ütötte modult egy *kiegyensúlyozatlan* adatkészlet számára kapcsolja össze. Számos oka lehet annak, hogy egy adatkészlet kiegyensúlyozatlan. Előfordulhat például, hogy a megcélzott kategória ritkán fordul elő a populációban, vagy az adatok összegyűjtése nehéz lehet. Az elemezni kívánt *osztályt* általában a arcul ütötte használatával lehet használni. 
  
A modul az eredeti mintákat tartalmazó adatkészletet ad vissza. A megadott százalékértéktől függően számos szintetikus kisebbségi mintát is ad vissza.  
  
## <a name="more-about-smote"></a>További információ a ARCUL ütötte

A szintetikus kisebbségi túlmintavételezési módszer (ARCUL ütötte) egy statisztikai módszer, amellyel az adatkészletben lévő esetek száma kiegyensúlyozott módon növelhető. A modul úgy működik, hogy új példányokat hoz létre a meglévő, bemenetként megadott kisebbségi esetekből. A ARCUL ütötte ezen implementációja *nem* módosítja a többségi esetek számát.

Az új példányok nem csak a meglévő kisebbségi esetek másolatai. Ehelyett az algoritmus mintákat vesz az egyes célcsoportok és a hozzájuk *tartozó szomszédok számára.* Az algoritmus ezután olyan új példákat hoz létre, amelyek a cél eset funkcióit kombinálják a szomszédai szolgáltatásaival. Ez a megközelítés növeli az egyes osztályok által elérhető funkciókat, és általánosabban teszi a mintákat.
  
A ARCUL ütötte a teljes adatkészletet bemenetként veszi fel, de csak a kisebbségi esetek százalékos arányát növeli. Tegyük fel például, hogy van egy kiegyensúlyozatlan adatkészlet, ahol az esetek mindössze 1 százaléka az a célként megadott érték (a kisebbségi osztály), az esetek 99 százalékában pedig a B érték szerepel. Ha a kisebbségi esetek százalékos arányát az előző százalékban kétszer szeretné megnövelni, a modul tulajdonságainál a **200** értéket kell megadnia **arcul ütötte százalékban** .  
  
## <a name="examples"></a>Példák  

Azt javasoljuk, hogy a ARCUL ütötte-t kis adatkészlettel próbálja meg használni a működésének megtekintéséhez. Az alábbi példa a Azure Machine Learning Designerben elérhető véradási adatkészletet használja.
  
Ha hozzáadja az adatkészletet egy folyamathoz, és kiválasztja az adatkészlet kimenetének **megjelenítését** , láthatja, hogy az adatkészletben szereplő 748-es sorok vagy esetek közül a 570-esetek (76 százalék) 0, a 178-as (24 százalék) pedig 1. osztály. Bár ez az eredmény nem okoz rettenetesen kiegyensúlyozatlan, az 1. osztály a vért adományozó személyeket jelöli, így ezek a sorok tartalmazzák a modellezni kívánt *szolgáltatás területét* .
 
Az esetek számának növeléséhez a következő módon állíthatja be a **arcul ütötte százalékos** értékét a 100 többszörösével:

||0. osztály|1. osztály|összesen|  
|-|-------------|-------------|-----------|  
|Eredeti adatkészlet<br /><br /> (a **arcul ütötte százalékos értékkel**  =  egyenértékű **0**)|570<br /><br /> 76%|178<br /><br /> 24|748|  
|**Arcul ütötte százalékos aránya**  =  **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**Arcul ütötte százalékos aránya**  =  **200**|570<br /><br /> 52%|534<br /><br /> 48%|1,104|  
|**Arcul ütötte százalékos aránya**  =  **300**|570<br /><br /> 44%|712<br /><br /> 56%|1 282|  
  
> [!WARNING]
> Az esetek számának növelése a ARCUL ütötte használatával nem garantált, hogy pontosabb modelleket hozzon létre. Próbálja ki a csővezetékeket különböző százalékokkal, különböző szolgáltatásokkal, valamint a legközelebbi szomszédok különböző számával, hogy megtekintse, hogyan befolyásolja a modell az esetek hozzáadását.  
  
## <a name="how-to-configure-smote"></a>A ARCUL ütötte konfigurálása
  
1.  Adja hozzá a ARCUL ütötte modult a folyamathoz. A modult az **Adatátalakítási modulok** területen találja a **manipuláció** kategóriában.

2. A fokozni kívánt adatkészlet csatlakoztatása. Ha meg szeretné adni az új esetek létrehozásához szükséges területet, vagy csak bizonyos oszlopokat használ, vagy ha nem szeretne kihagyni néhányat, használja az [Oszlopok kiválasztása az adatkészlet](select-columns-in-dataset.md) modulban lehetőséget. Ezután el tudja különíteni a használni kívánt oszlopokat a ARCUL ütötte használata előtt.
  
    Ellenkező esetben a ARCUL ütötte-on keresztül létrehozott új esetek az *összes* bemenetként megadott oszlopon alapulnak. A szolgáltatás oszlopainak legalább egy oszlopa numerikus.
  
3.  Győződjön meg arról, hogy a címkét vagy a cél osztályt tartalmazó oszlop van kiválasztva. A ARCUL ütötte csak bináris címkéket fogad el.
  
4.  A ARCUL ütötte modul automatikusan azonosítja a kisebbségi osztályt a Label (címke) oszlopban, majd beolvassa a kisebbségi osztály összes példáját. Az összes oszlopnak nem lehet NaN-értéke.
  
5.  A **arcul ütötte százalékos értéke** beállításban adjon meg egy egész számot, amely a kimeneti adatkészletben a kisebbségi esetek céljának százalékos arányát jelzi. Például:  
  
    - Adja meg a **0** értéket. A ARCUL ütötte modul pontosan ugyanazt az adatkészletet adja vissza, amelyet bemenetként adott meg. Nem hoz létre új kisebbségi eseteket. Ebben az adatkészletben az osztály aránya nem módosult.  
  
    - Adja meg a **100** értéket. A ARCUL ütötte modul új, kismértékű eseteket hoz létre. Az eredeti adatkészletben lévő azonos számú kisebbségi esetet adja hozzá. Mivel a ARCUL ütötte nem növekszik a többségi esetek száma, az egyes osztályokba tartozó esetek aránya megváltozott.  
  
    - Adja meg a **200** értéket. A modul megduplázza a kisebbségi esetek százalékos arányát az eredeti adatkészlethez képest. Ez azt eredményezi, hogy az előzőekben leírtak szerint kétszer annyi kisebbségi ügyet *tartalmaz* . Az adatkészlet mérete inkább úgy növekszik, hogy a többségi esetek száma ugyanaz marad. A kisebbségi esetek száma megnő, amíg meg nem felel a kívánt százalékos értéknek.  
  
    > [!NOTE]
    > A ARCUL ütötte százalékban csak a 100 többszörösét használja.

6.  A **legközelebbi szomszédok** lehetőség használatával határozza meg, hogy a arcul ütötte algoritmus milyen méretben használja az új esetek kiépítésekor. A legközelebbi szomszéd egy olyan adathalmaz, amely hasonlít a cél esetére. A két eset közötti távolságot a rendszer az összes funkció súlyozott vektorának kombinálásával méri.  
  
    + A legközelebbi szomszédok számának növelésével több esetben is kap funkciókat.
    + A legközelebbi szomszédok számának alacsony értékre állításával olyan szolgáltatásokat használhat, amelyek az eredeti mintában jobban hasonlítanak.  
  
7. Adjon meg egy értéket a **véletlenszerű mag** mezőben, ha ugyanazt az eredményt szeretné biztosítani ugyanazon folyamat ugyanazon adatcsatornán keresztül futtatva. Ellenkező esetben a modul egy véletlenszerű magot generál a Processzor órajelének értéke alapján a folyamat telepítésekor. A véletlenszerű magok generálása némileg eltérő eredményeket eredményezhet a futtatások során.

8. A folyamat elküldése.  
  
   A modul kimenete egy adatkészlet, amely tartalmazza az eredeti sorokat, valamint több, a kisebbségi esetekkel felvett sort.  

## <a name="technical-notes"></a>Technikai megjegyzések

+ Ha a **arcul ütötte** modult használó modellt tesz közzé, távolítsa el a **arcul ütötte** a prediktív folyamatból, mielőtt webszolgáltatásként tenné közzé. Ennek az az oka, hogy a ARCUL ütötte célja a modell fejlesztése a képzés során, nem pedig pontozással. Hibaüzenet jelenhet meg, ha egy közzétett prediktív folyamat tartalmazza a ARCUL ütötte modult.

+ A ARCUL ütötte alkalmazása előtt gyakran jobb eredményeket érhet el, ha törli a hiányzó értékeket vagy más átalakításokat alkalmaz az adatok javítására. 

+ Egyes kutatók megvizsgálták, hogy a ARCUL ütötte hatékony-e a nagy dimenziós vagy ritka adatokat, például a szöveges besorolásban vagy a genomikai adatkészletekben használt adatokat. Ez a tanulmány jól összefoglalja a ARCUL ütötte alkalmazásának hatásait és elméleti érvényességét: [Blagus és LUSA: arcul ütötte a nagy dimenziós osztály által kiegyensúlyozatlan adatmennyiségek esetében](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Ha a ARCUL ütötte nem érvényes az adatkészletben, az esetlegesen megfontolandó egyéb megközelítések a következők:
  + A kisebbségi esetek túlmintavételezését vagy a többségi esetek mintavételezését végző metódusok.
  + Olyan Ensemble-technikák, amelyek a tanulót közvetlenül a fürtözés, a csomagok és az adaptív növelés használatával segítik elő.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

