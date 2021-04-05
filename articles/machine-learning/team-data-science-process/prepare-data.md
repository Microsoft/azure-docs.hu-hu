---
title: Előkészítési adat ML Studio (klasszikus) – csoportos adatelemzési folyamat
description: Előre feldolgozhatja és megtisztíthatja az adatfeldolgozást, hogy előkészítse a gépi tanuláshoz való hatékony használatot.
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
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012407"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Az adatok bővített gépi tanulásra való előkészítésének feladatai
Az adatok előfeldolgozása és tisztítása olyan fontos feladatok, amelyeket el kell végezni ahhoz, hogy adatkészletet lehessen használni a modell betanításához. A nyers adatok gyakran zajosak és megbízhatatlanok, és lehet, hogy hiányoznak az értékek. Ha ilyen adatokat használ a modellezéshez, félrevezető eredményeket hozhat létre. Ezek a feladatok a csoportos adatelemzési folyamat (TDSP) részét képezik, és általában egy olyan adatkészlet kezdeti vizsgálatát végzik, amely a szükséges előzetes feldolgozás felderítésére és megtervezésére szolgál. A TDSP folyamatával kapcsolatos részletesebb útmutatásért tekintse meg a [csoportos adatelemzési folyamat](overview.md)lépéseit.

Az előfeldolgozási és tisztítási feladatok, például az adatfeltárási feladat számos különböző környezetben, például az SQL-ben vagy a kaptárban, illetve a Azure Machine Learning Studio (klasszikus), valamint különböző eszközökkel és nyelvekkel (például R vagy Python) végezhetők el, attól függően, hogy az adatok hol tárolódnak és hogyan vannak formázva. Mivel a TDSP ismétlődő jellegű, ezeket a feladatokat a folyamat munkafolyamatának különböző lépésein végezheti el.

Ez a cikk különféle adatfeldolgozási fogalmakat és feladatokat mutat be, amelyek az adatoknak a Azure Machine Learning Studioba (klasszikus) történő betöltése előtt vagy után hajthatók végre.

Az Azure Machine Learning Studio (klasszikus) szolgáltatásban végzett adatfeltárásra és előfeldolgozásra például az [adatfeldolgozást előkészítő](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) videóban olvashat.

## <a name="why-pre-process-and-clean-data"></a>Miért érdemes előre feldolgozni és megtisztítani az adatfeldolgozást?
A valós világbeli adatok gyűjtése különböző forrásokból és folyamatokból történik, és az adatkészletek minőségét veszélyeztető szabálytalanságokat vagy sérült adatokat is tartalmazhat. A felmerülő jellemző adatminőségi problémák a következők:

* **Hiányos**: az adatok nem tartalmaznak attribútumokat, vagy hiányoznak a hiányzó értékek.
* **Zajos**: az adatok hibás rekordokat vagy kiugró adatokat tartalmaznak.
* **Inkonzisztens**: az adatok ütköző rekordokat vagy eltéréseket tartalmaznak.

A minőség-előrejelző modellek előfeltétele a minőségi adatgyűjtés. A "szemetet a-ban, a kitakarításban" és az adatminőség javítása érdekében, és így a modell teljesítményének növelése érdekében elengedhetetlen, hogy az adatállapot-képernyőt az adatproblémák korai szakaszában, a megfelelő adatfeldolgozási és tisztítási lépések elvégzésével végezze el.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Mik azok a tipikus adatállapot-képernyők?
Az általános minőséget az alábbiak ellenőrzésével ellenőrizheti:

* A **rekordok** száma.
* Az **attribútumok** (vagy **szolgáltatások**) száma.
* Az attribútum **adattípusai** (névleges, sorszám vagy folytonos).
* A **hiányzó értékek** száma.
* **Jól formázott** adategységek.
  * Ha az adatközpont TSV vagy CSV formátumú, ellenőrizze, hogy az oszlopok és a sorok elválasztói mindig helyesen vannak-e elkülönítve az oszlopokat és a sorokat.
  * Ha az adatai HTML-vagy XML-formátumban vannak, ellenőrizze, hogy az adatai megfelelően vannak-e kialakítva a megfelelő szabványok alapján.
  * A strukturált adatok félig strukturált vagy strukturálatlan adatokból való kinyeréséhez is szükség lehet az elemzésre.
* **Inkonzisztens adatrekordok**. Győződjön meg arról, hogy az értékek tartománya engedélyezett. Például, ha az adattanulói GPA (a minőségi pont átlaga) tartalmaz, ellenőrizze, hogy a GPA a kijelölt tartományban van-e, azaz 0 ~ 4.

Ha az adatokkal kapcsolatos problémákat tapasztal, a **feldolgozási lépések** szükségesek, ami gyakran magában foglalja a hiányzó értékek törlését, az adatnormalizálás, a diszkretizálási, a szöveg feldolgozását, valamint a beágyazott karakterek eltávolítását és/vagy cseréjét, amelyek hatással lehetnek az adatok igazítására, a közös mezőkben szereplő vegyes adattípusokra és mások

A **Azure Machine learning jól formázott táblázatos adatokat** használ.  Ha az adatokat már táblázatos formában végzik, az adatfeldolgozást közvetlenül a Machine Learning Azure Machine Learning Studio (klasszikus) használatával is elvégezheti.  Ha az adatokat nem táblázatos formában, tegyük fel, hogy az XML-ben van, akkor az adatokat táblázatos formában kell konvertálni.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Mik az adatok előfeldolgozásával kapcsolatos főbb feladatok?
* **Adattisztítás**: adja meg a hiányzó értékeket, azonosítsa és távolítsa el a zajos adatokat és a kiugró adatokat.
* **Adatátalakítás**: a méretek és a zaj csökkentése érdekében normalizálja az adatmennyiséget.
* **Adatmennyiség-csökkentés**: adatrekordok vagy attribútumok a könnyebb adatkezelés érdekében.
* **Adatdiszkretizálási**: folyamatos attribútumok átalakítása kategorikus attribútumokra a könnyű használat érdekében bizonyos gépi tanulási módszerekkel.
* **Szöveg tisztítása**: távolítsa el az adatok helytelen igazítását okozó beágyazott karaktereket, például a tabulátorral tagolt adatfájlban lévő beágyazott lapokat, a beágyazott új sorokat, amelyek például a rekordokat bontják fel.

Az alábbi részekben részletesen ismertetjük ezeket az adatfeldolgozási lépéseket.

## <a name="how-to-deal-with-missing-values"></a>Hogyan lehet kezelni a hiányzó értékeket?
A hiányzó értékek kezeléséhez érdemes először azonosítani a hiányzó értékek okát, hogy jobban kezeljék a problémát. A jellemző hiányzó értékek kezelési módszerei a következők:

* **Törlés**: hiányzó értékekkel rendelkező rekordok eltávolítása
* **Próbabábu-helyettesítés**: hiányzó értékek kicserélése egy dummy értékkel: például *ismeretlen* a kategorikus vagy a 0 értékre a numerikus értékeknél.
* **Mean helyettesítés**: Ha a hiányzó adatok numerikusak, cserélje le a hiányzó értékeket az átlag értékre.
* **Gyakori helyettesítés**: Ha a hiányzó adatok kategorikusak, cserélje le a hiányzó értékeket a leggyakoribb elemre.
* **Regressziós helyettesítés**: használjon regressziós metódust a hiányzó értékek romlott értékekkel való lecseréléséhez.  

## <a name="how-to-normalize-data"></a>Hogyan lehet az adatnormalizálni?
Az adatok normalizálása átméretezi a numerikus értékeket egy megadott tartományba. A népszerű adatnormalizálás módszerei többek között az alábbiak:

* **Min-max normalizálás**: lineárisan alakítsa át az értékeket egy tartományba, azaz 0 és 1 közötti értéket, ahol a minimális érték 0, a maximális érték pedig 1.
* **Z – pontszám normalizálása**: az adatméretezés a középérték és a szórás alapján: az adatmennyiség és a szórás közötti különbség felosztása.
* **Decimális skálázás**: méretezheti az adatmennyiséget az attribútumérték decimális pontjának áthelyezésével.  

## <a name="how-to-discretize-data"></a>Hogyan diszkretizálásához az adatkezelés?
Az adatok diszkretizált a folytonos értékek névleges attribútumokra vagy intervallumokra való konvertálásával. Ennek néhány módja a következő:

* **Egyenlő szélességű dobozolási**: az attribútumok összes lehetséges értékének tartományát azonos méretű N csoportba sorolja, és hozzárendeli azokat az értékeket, amelyek a raktárhely számával ellátott raktárhelyen jelennek meg.
* **Egyenlő magasságú dobozolási**: az attribútumok összes lehetséges értékének tartományát N csoportokba osztja, amelyek mindegyike azonos számú példányt tartalmaz, majd hozzárendeli azokat az értékeket, amelyek az adott raktárhely számával ellátott raktárhelyre esnek.  

## <a name="how-to-reduce-data"></a>Hogyan csökkentheti az adatmennyiséget?
A könnyebb adatkezelés érdekében különböző módszerekkel csökkentheti az adatmennyiséget. Az adatmérettől és a tartománytól függően a következő metódusokat lehet alkalmazni:

* **Mintavétel rögzítése**: kóstolja meg az adatrekordokat, és csak a reprezentatív részhalmazt válassza ki az adatokból.
* **Attribútumok mintavételezése**: válassza ki a legfontosabb attribútumok egy részhalmazát az adatok közül.  
* **Összesítés**: az adatok csoportokba osztása és az egyes csoportok számának tárolása. Például az elmúlt 20 évben egy éttermi lánc napi bevételi száma összesíthető a havi bevételsel az adatok méretének csökkentése érdekében.  

## <a name="how-to-clean-text-data"></a>Hogyan lehet megtisztítani a szöveges adatbevitelt?
**A táblázatos adatok szöveges mezői** tartalmazhatják az oszlopok igazítását és/vagy a rekordok határait befolyásoló karaktereket. A tabulátorral tagolt fájlok beágyazott lapjai például az oszlopok helytelen igazítását okozzák, a beágyazott új sortörési sorok pedig sortöréseket is tartalmazhatnak. A szöveges kódolás nem megfelelő, ha a szöveg írása vagy olvasása során az adatok elvesztését eredményezi, a nem olvasható karakterek (például nullák) véletlen bevezetését, és a szöveg elemzését is érintheti. Alapos elemzésre és szerkesztésre lehet szükség ahhoz, hogy a szövegmezőket a megfelelő igazításhoz és/vagy strukturált adatok kinyeréséhez strukturálatlan vagy részben strukturált szöveges adatokból lehessen kinyerni.

Az **adatfeltárási** szolgáltatás korai áttekintést nyújt az adatvédelemről. Ebben a lépésben számos adatprobléma feloldható, és a kapcsolódó módszerek alkalmazhatók a problémák megoldására.  Fontos, hogy olyan kérdéseket tegyenek fel, mint a probléma forrása, és hogy a probléma milyen módon lett bevezetve. Ez a folyamat a megoldásához szükséges adatfeldolgozási lépések eldöntését is segíti. A végső használati esetek és a personák azonosításával is felhasználható az adatfeldolgozási tevékenység rangsorolása.

## <a name="references"></a>Hivatkozások
> *Adatbányászat: fogalmak és technikák*, harmadik kiadás, Morgan Kaufmann, 2011, Jiawei Han, Michelin Kamber és Jian Pei
> 
> 

