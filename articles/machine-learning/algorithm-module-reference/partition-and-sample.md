---
title: 'Partíció és minta: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning a partíció és a minta modult az adatkészletek mintavételezésének végrehajtásához, vagy az adatkészlet partícióinak létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898494"
---
# <a name="partition-and-sample-module"></a>Partíció és minta modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

A partíció és a minta modul használatával mintavételezést végezhet egy adatkészleten, vagy partíciókat hozhat létre az adatkészletből.

A mintavétel fontos eszköz a gépi tanulásban, mivel lehetővé teszi az adatkészlet méretének csökkentését az értékek azonos arányának fenntartása mellett. Ez a modul több kapcsolódó feladatot is támogat, amelyek fontosak a gépi tanulásban: 

- Az adatait több, azonos méretű alszakaszra oszthatja. 

  Használhatja a partíciókat a kereszt-ellenőrzéshez, illetve az esetek véletlenszerű csoportokba való hozzárendelését is.

- Az adatok csoportokba való elválasztása, majd egy adott csoport adatainak használata. 

  Az esetek különböző csoportokhoz való véletlenszerű hozzárendelését követően előfordulhat, hogy módosítania kell a csak egy csoporthoz társított szolgáltatásokat.

- Mintavételi. 

  Kinyerheti az adatok százalékos arányát, véletlenszerű mintavételezést alkalmazhat, vagy kiválaszthatja az adatkészlet kiegyensúlyozásához használandó oszlopot, és rétegzett mintavételezést hajthat végre az értékein.

- Kisebb adatkészlet létrehozása teszteléshez. 

  Ha sok adat van, érdemes csak az első *n* sort használni a folyamat beállítása során, majd a modell létrehozásakor váltson a teljes adatkészlet használatára. A mintavétel használatával kisebb adathalmazt is létrehozhat a fejlesztéshez.

## <a name="configure-the-module"></a>A modul konfigurálása

Ez a modul a következő módszereket támogatja az adatpartíciók és a mintavételezések felosztásához. Először válassza ki a módszert, majd adja meg a metódus által igényelt további beállításokat.

- Head
- Mintavételezés
- Hozzárendelés a kidobásokhoz
- Kidobás

### <a name="get-top-n-rows-from-a-dataset"></a>ELSŐ N sor beolvasása egy adatkészletből

Ezzel a móddal csak az első *n* sor kerül be. Ez a beállítás akkor hasznos, ha egy folyamatot kis számú sorban szeretne tesztelni, és nincs szükség az adat kiegyensúlyozására vagy bármilyen módon történő mintavételezésére.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz a csatolón, és kapcsolódjon az adatkészlethez.  

1. **Partíciós vagy minta mód**: ezt a beállítást állítsa a **Head** értékre.

1. **Kijelölni kívánt sorok száma**: adja meg a visszaadni kívánt sorok számát.

   A sorok számának nem negatív egész számnak kell lennie. Ha a kiválasztott sorok száma nagyobb, mint az adatkészlet sorainak száma, a rendszer a teljes adathalmazt adja vissza.

1. A folyamat elküldése.

A modul egyetlen adatkészlet kimenetét adja meg, amely csak a megadott számú sort tartalmazza. A sorok mindig az adatkészlet elejétől lesznek beolvasva.

### <a name="create-a-sample-of-data"></a>Adatminta létrehozása

Ez a beállítás támogatja az egyszerű véletlenszerű mintavételezést vagy a rétegzett véletlenszerű mintavételt. Akkor hasznos, ha kisebb reprezentatív minta adatkészletet szeretne létrehozni teszteléshez.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz, és kapcsolódjon az adatkészlethez.

1. **Partíció vagy minta mód**: ezt a beállítást a  **mintavételezéshez** kell beállítani.

1. **Mintavételezési sebesség**: adjon meg egy 0 és 1 közötti értéket. Ez az érték határozza meg a forrás adatkészletből a kimeneti adatkészletbe foglalandó sorok százalékos arányát.

   Ha például csak az eredeti adatkészlet felét szeretné használni, adja meg, hogy `0.5` a mintavételezési sebesség 50% legyen.

   A bemeneti adatkészlet sorait a rendszer a megadott arány szerint rendezi és szelektíven helyezi el a kimeneti adatkészletben.

1. **Véletlenszerű magok mintavételezéshez**: opcionálisan adjon meg egy egész számot, amelyet mag értékként kíván használni.

   Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok minden alkalommal azonos módon legyenek felosztva. Az alapértelmezett érték **0**, ami azt jelenti, hogy a rendszer órája alapján hozza létre a kezdő magot. Ez az érték némileg eltérő eredményeket eredményezhet a folyamat minden egyes indításakor.

1. **Rétegzett felosztás mintavételezéshez**: akkor válassza ezt a lehetőséget, ha fontos, hogy az adatkészlet sorait a mintavételezés előtt a kulcs oszlopa egyenletesen ossza meg.

   A **mintavételezési kulcs oszlopához** válassza ki az adatkészlet felosztásához használni kívánt egyetlen *rétegbeli oszlopot* . Az adatkészlet sorai a következőképpen oszlanak meg:

   1. Az összes bemeneti sor (rétegzett) a megadott rétegek oszlop értékei szerint csoportosítva van.

   1. A sorok az egyes csoportokon belül vannak megkeverve.

   1. Az egyes csoportok szelektíven hozzáadódnak a kimeneti adatkészlethez, hogy megfeleljenek a megadott aránynak.


1. A folyamat elküldése.

   Ezzel a beállítással a modul egyetlen adatkészletet ad eredményül, amely az adatokat reprezentáló mintavételt tartalmaz. Az adatkészlet fennmaradó, nem mintavételezés alatt álló része nem kimenet. 

## <a name="split-data-into-partitions"></a>Az adatfelosztás partícióra

Akkor használja ezt a beállítást, ha az adatkészletet az adat részhalmazára szeretné osztani. Ez a beállítás akkor is hasznos, ha egyéni számú kitöltést szeretne létrehozni a kereszt-ellenőrzéshez, vagy több csoportba kell osztania a sorokat.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz, és kapcsolódjon az adatkészlethez.

1. A **partíció vagy a minta mód** esetében válassza **a hozzárendelés a kidobáshoz** lehetőséget.

1. **Csere használata a particionáláskor**: válassza ezt a lehetőséget, ha azt szeretné, hogy a mintában szereplő sor visszakerüljön a sorok készletére a lehetséges újrafelhasználáshoz. Ennek eredményeképpen előfordulhat, hogy ugyanaz a sor több bedobáshoz is hozzá van rendelve.

   Ha nem használja a helyettesítő elemet (az alapértelmezett beállítás), a rendszer nem helyezi vissza a mintavételezési sort az esetleges újrafelhasználáshoz. Ennek eredményeképpen minden egyes sor csak egy összecsukott elemhez rendelhető hozzá.

1. **Randomizált felosztás**: akkor válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok véletlenszerűen legyenek kiosztva a kidobáshoz.

   Ha nem jelöli be ezt a beállítást, a sorok a ciklikus multiplexelés használatával lesznek kiosztva.

1. **Véletlenszerű mag**: igény szerint adjon meg egy egész számot, amelyet a mag értékként kíván használni. Ez a beállítás akkor fontos, ha azt szeretné, hogy a sorok minden alkalommal azonos módon legyenek felosztva. Ellenkező esetben az alapértelmezett **0** érték azt jelenti, hogy a rendszer véletlenszerű kezdő magot használ.

1. **Adja meg a particionáló metódust**: jelezze, hogy az egyes partíciók számára milyen módon kell megosztani az adatmegosztást, a következő lehetőségek használatával:

   - **Partíció egyenletes** használata: ezzel a beállítással az egyes partíciókban megegyező számú sort helyezhet el. A kimeneti partíciók számának megadásához adjon meg egy egész számot a **kidobások számának megadása a dobozba egyenletes felosztáshoz** .

   - **Partíció testreszabott arányokkal**: ezzel a beállítással megadhatja az egyes partíciók méretét vesszővel tagolt listaként.

     Tegyük fel például, hogy három partíciót kíván létrehozni. Az első partíció az adatmennyiség 50 százalékát fogja tartalmazni. A fennmaradó két partíció az adatmennyiség 25 százalékát fogja tartalmazni. A (z) **vesszővel elválasztott arányok listájában** adja meg a következő számokat: **.5,. 25,. 25**.

     Az összes partíciós méret összegének pontosan 1 értékkel kell rendelkeznie.

     Ha 1-nél *kisebb* számot ad meg, a fennmaradó sorok tárolására külön partíció jön létre. Ha például a **.2** és a **.3** értékeket adja meg, a rendszer egy harmadik partíciót hoz létre, amely az összes sor fennmaradó 50 százalékát fogja tárolni.
     
     Ha *több mint 1* értéket ad meg, akkor a folyamat futtatásakor hiba lép fel.

1. **Rétegzett felosztás**: válassza ezt a lehetőséget, ha azt szeretné, hogy a sorok a felosztáskor rétegzett legyenek, majd válassza a _rétegek oszlopot_.

1. A folyamat elküldése.

   Ha ezt a lehetőséget választja, a modul több adatkészletet ad eredményül. Az adatkészletek particionálva vannak a megadott szabályoknak megfelelően.

### <a name="use-data-from-a-predefined-partition"></a>Előre definiált partíció adatainak használata  

Akkor használja ezt a beállítást, ha több partícióra osztott egy adatkészletet, és most további elemzés vagy feldolgozás céljából szeretné betölteni az egyes partíciókat.

1. Adja hozzá a **partíciót és a minta** modult a folyamathoz.

1. Kapcsolja össze a modult a **partíció és a minta** előző példányának kimenetével. A példánynak a hozzárendelés a **Kidobáshoz** lehetőséget kell használnia bizonyos számú partíció létrehozásához.

1. **Partíció vagy minta mód**: válassza a **pick fold** lehetőséget.

1. **Adja meg, hogy melyik** kiválasztható mintát szeretné kiválasztani: válassza ki a használni kívánt partíciót az indexének megadásával. A partíciós indexek 1-alapúak. Ha például három részre osztotta az adatkészletet, akkor a partíciók az 1, 2 és 3 indexekkel rendelkezhetnek.

   Ha érvénytelen index értéket ad meg, a rendszer tervezésidejű hibát jelez: "hiba 0018: az adatkészlet érvénytelen adatokat tartalmaz."

   Az adatkészlet összevonása mellett az adatkészletet két csoportba is elkülönítheti: a cél kidobását és minden mást. Ehhez írja be az egyetlen fold indexét, majd válassza a **kiválasztott fold komplement** (kiválasztás) elemét, hogy mindent megtegyen a megadott foldon.

1. Ha több partíciót használ, a partíció **és a minta** modul több példányát is fel kell vennie az egyes partíciók kezelésére.

   A második sorban lévő **partíció és minta** modul például úgy van beállítva, hogy a **kiosztásokhoz legyen hozzárendelve**, és a harmadik sorban lévő modul a **kidobásra** van beállítva.   

   ![Partíció és minta](./media/module/partition-and-sample.png)

1. A folyamat elküldése.

   Ezzel a beállítással a modul egyetlen adatkészlet kimenetét adja meg, amely csak az ehhez a foldhoz rendelt sorokat tartalmazza.

> [!NOTE]
>  A fold megjelöléseit nem lehet közvetlenül megtekinteni. Csak a metaadatokban jelennek meg.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 