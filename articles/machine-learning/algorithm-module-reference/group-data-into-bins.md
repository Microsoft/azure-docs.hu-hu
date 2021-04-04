---
title: 'Adatcsoportosítás a raktárhelyekre: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a csoportosítási adatok a Raktárhelyek modulba a számok csoportosításához vagy a folyamatos adatok eloszlásának módosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/13/2020
ms.openlocfilehash: 392cb9b4c2ded1b98b79ce8dcd780ac59e96b78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91998495"
---
# <a name="group-data-into-bins-module"></a>Adatcsoportosítás a Raktárhelyek modulba

Ez a cikk azt ismerteti, hogyan használhatók a csoportba foglalt adatok a Azure Machine Learning Designerben, a számok csoportosításához vagy a folyamatos adatok eloszlásának módosításához.

A csoportosítási adatként a dobozolási több beállítást is támogat. Testre szabhatja a Raktárhelyek szegélyének beállítását, valamint azt, hogy az értékek hogyan legyenek kiosztva a rekeszekben. Például a következőket teheti:  

+ Adja meg manuálisan az értékek egy sorozatát, amely raktárhely határként szolgál.  
+ Értékeket rendelhet a raktárhelyekhez a *quantiles* vagy a percentilis sorainak használatával.  
+ Az értékek egyenletes eloszlásának kényszerítése a raktárhelyekre.  

## <a name="more-about-binning-and-grouping"></a>További információ a dobozolási és a csoportosításról

A *dobozolási* vagy az adatcsoportosítás (más néven *kvantálás*) fontos eszköze a numerikus adatfeldolgozások előkészítésének a gépi tanulás számára. A következő esetekben hasznos:

+ A folytonos számok egy oszlopa túl sok egyedi értéket tartalmaz a modell hatékony modellezéséhez. Így automatikusan vagy manuálisan rendeli hozzá az értékeket a csoportokhoz, hogy kisebb különálló tartományokat hozzon létre.

+ Olyan számok oszlopát szeretné lecserélni, amelyek meghatározott tartományokat jelképező kategorikus értékekkel rendelkeznek.

    Előfordulhat például, hogy az értékeket egy Age oszlopban szeretné csoportosítani egyéni tartományok (például 1-15, 16-22, 23-30) megadásával, és így tovább a felhasználói demográfiai adatokhoz.

+ Az adatkészlet néhány szélsőséges értékkel rendelkezik, amelyek mindegyike a várt tartományon kívül esik, és ezek az értékek a betanított modellre is hatással vannak. A modell torzulásának enyhítése érdekében az quantiles metódus használatával átalakíthatja az adategységeket egy egységes eloszlásba.

    Ezzel a módszerrel a csoportosított adattárolók modul meghatározza az ideális raktárhelyek helyét és a raktárhely szélességét, így biztosítva, hogy nagyjából azonos számú minta legyen az egyes raktárhelyekre. Ezt követően a kiválasztható normalizálás módszertől függően a rendszer a Raktárhelyek értékeit átalakítja a percentilis értékre, vagy hozzárendeli azt egy raktárhely számhoz.

### <a name="examples-of-binning"></a>Példák dobozolási

Az alábbi ábrán a dobozolási előtti és utáni numerikus értékek eloszlása látható a *quantiles* metódussal. Figyelje meg, hogy a bal oldali nyers adatmennyiséggel összehasonlítva az adatmennyiséget dobozolni, és átalakítottuk a normál méretre.  

> [!div class="mx-imgBorder"]
> ![Eredmény vizualizációja](media/module/group-data-into-bins-result-example.png)

Mivel az adatok csoportosítása számos módon történik, az összes testreszabható, javasoljuk, hogy kísérletezzen különböző módszerekkel és értékekkel. 

## <a name="how-to-configure-group-data-into-bins"></a>A csoportosítási adatkészletek beállítása a raktárhelyekre

1. Adja hozzá a **csoport adatait a tárolók** modulhoz a tervezőben a folyamathoz. Ez a modul az **adatátalakítás** kategóriában található.

2. Kösse össze a numerikus adatokat tartalmazó adatkészletet a bin értékkel. A kvantálás csak numerikus értékeket tartalmazó oszlopokra alkalmazható. 

    Ha az adatkészlet nem numerikus oszlopokat tartalmaz, az oszlopok [kiválasztása az adatkészlet-](select-columns-in-dataset.md) modulban elem kiválasztásával válassza ki a használni kívánt oszlopok egy részhalmazát.

3. A dobozolási mód meghatározása. A dobozolási mód meghatározza a többi paramétert, ezért először válassza a **dobozolási mód** lehetőséget. A következő típusú dobozolási támogatottak:

    - **Quantiles**: a quantile metódus értékeket rendel a raktárhelyekhez a percentilis sorai alapján. Ez a metódus egyenlő magasságú dobozolási néven is ismert.

    - **Egyenlő szélesség**: ezzel a beállítással a Raktárhelyek teljes számát kell megadnia. Az adatoszlop értékeit a rendszer a rekeszekben helyezi el, így az egyes raktárhelyek a kezdő és a záró értékek között azonos intervallummal rendelkeznek. Ennek eredményeképpen előfordulhat, hogy egyes raktárhelyek több értékkel rendelkeznek, ha az adatok egy adott pont körül vannak összerakásban.

    - **Egyéni élek**: megadhatja az egyes raktárhelyeket megkezdő értékeket. A peremhálózati érték mindig a raktárhely alsó határa. 
    
      Tegyük fel például, hogy két raktárhelyre kívánja csoportosítani az értékeket. Az egyik értéke 0-nál nagyobb lesz, és az egyik érték nullánál kisebb vagy azzal egyenlő lesz. Ebben az esetben a Raktárhelyek széleinél **0** értéket kell megadnia a **bin szegélyek vesszővel tagolt listájában**. A modul kimenete 1 és 2 lesz, ami azt jelzi, hogy az egyes sorok értéke a bin index. Vegye figyelembe, hogy a vesszővel tagolt értékek listájának növekvő sorrendben kell lennie, például 1, 3, 5, 7.
    
    > [!Note]
    > Az *ENTRÓPIA mdl* mód a Studióban (klasszikus) van definiálva, és nincs megfelelő nyílt forráskódú csomag, amely a tervezőben még nem használható.        

4. Ha a **Quantiles** és az **egyenlő szélességű** dobozolási módot használja, a **raktárhelyek száma** beállítással megadhatja, hogy hány raktárhelyet vagy *Quantiles* kíván létrehozni.

5. Az **oszlopok raktárhelyre** való kiválasztásához használja az oszlop-választót, és válassza ki azokat az oszlopokat, amelyeknek a raktárhelyét szeretné kijelölni. Az oszlopoknak numerikus adattípusnak kell lenniük.

    Ugyanazt a dobozolási szabályt alkalmazza a rendszer az összes kiválasztott oszlopra. Ha egyes oszlopokat más módszer használatával kell kiosztania, használja a csoport adatai egy külön példányát a rekeszek modulba az egyes oszlopok oszlopaihoz.

    > [!WARNING]
    > Ha olyan oszlopot választ, amely nem engedélyezett típusú, futásidejű hiba jön létre. A modul hibát ad vissza, amint megkeresi a nem engedélyezett típusok bármelyik oszlopát. Ha hibaüzenetet kap, tekintse át az összes kijelölt oszlopot. A hiba nem sorolja fel az összes érvénytelen oszlopot.

6. **Kimeneti mód** esetén adja meg, hogyan szeretné kiadni a kvantálási értékeket:

    + **Hozzáfűzés**: egy új oszlopot hoz létre a dobozolni értékekkel, és hozzáfűzi a bemeneti táblához.

    + **Helyben**: az eredeti értékeket az adatkészlet új értékeivel helyettesíti.

    + **ResultOnly**: csak az eredmény oszlopokat adja vissza.

7. Ha a **Quantiles** dobozolási módot választja, akkor a **Quantile normalizálása** beállítással határozhatja meg, hogy az értékek hogyan legyenek normalizálva a Quantiles való rendezés előtt. Vegye figyelembe, hogy az értékek normalizálása átalakítja az értékeket, de a Raktárhelyek végső számát nem befolyásolja.

    A következő normalizáló típusok támogatottak:

    + **Százalék**: az értékek normalizálva vannak a (z) [0100] tartományon belül.

    + **PQuantile**: az értékek a [0, 1] tartományon belül normalizálva vannak.

    + **QuantileIndex**: az értékek az [1, a Raktárhelyek száma] tartományon belül normalizálva vannak.

8. Ha a **Custom EDGeS (egyéni élek** ) lehetőséget választja, adjon meg egy vesszővel tagolt listát, amelyet a rendszer *raktárhely élekként* használ a bin EDGeS szövegmező **vesszővel tagolt listájában** . 

    Az értékek megjelölik a Raktárhelyek felosztására szolgáló pontot. Ha például egy bin Edge-értéket ad meg, a rendszer két raktárhelyet hoz létre. Ha két bin Edge-értéket ad meg, a rendszer három raktárhelyet hoz létre.

    Az értékeket a Raktárhelyek létrehozási sorrendjében kell rendezni, a legalacsonyabbtól a legmagasabbig.

10. Válassza az **oszlopok címkézése kategorikusként** lehetőséget annak jelzésére, hogy a kvantálási oszlopokat kategorikus változókként kell kezelni.

11. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A csoportosítási adatok a rekeszek modulba olyan adatkészletet ad vissza, amelyben az egyes elemek a megadott mód alapján dobozolni. 

Egy *dobozolási-transzformációt* is visszaad. Ezt a függvényt átadhatja az [átalakítási modul alkalmazásával](apply-transformation.md) az új minták az adatoknak az azonos dobozolási mód és paraméterek használatával történő kiosztásához.  

> [!TIP]
> Ha a dobozolási használja a betanítási adataihoz, ugyanazt a dobozolási módszert kell használnia a teszteléshez és előrejelzéshez használt adataihoz. Ugyanezeket a raktárhelyeket és a Raktárhelyek szélességét is fel kell használni. 
> 
> Azt javasoljuk, hogy az adatelemzést mindig ugyanazzal a dobozolási módszerrel alakítsa át, ezért ajánlott a hasznos adatátalakítások mentése. Ezután alkalmazza azokat más adatkészletekbe az [átalakítási modul alkalmazása](apply-transformation.md) paranccsal.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
