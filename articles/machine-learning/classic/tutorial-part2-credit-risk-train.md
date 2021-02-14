---
title: 'ML Studio (klasszikus) oktatóanyag 2: kredit kockázati modellek betanítása – Azure'
description: Részletes oktatóanyag, amely bemutatja, hogyan hozhat létre a hitelkockázat értékelésére szolgáló prediktív elemzési megoldást Azure Machine Learning Studio (klasszikus). Ez az oktatóanyag egy háromrészes oktatóanyag-sorozat második része. Bemutatja, hogyan kell betanítani és kiértékelni a modelleket.
keywords: hitelkockázat, prediktív elemzési megoldás,kockázatértékelés
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 677c5b791f475468fbcf15c81fbabdcdbb1972de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517450"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>2. Oktatóanyag: a kredit kockázati modelljeinek betanítása – Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik.  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus)   ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Ez az oktatóanyag részletesen bemutatja a prediktív elemzési megoldások fejlesztésének folyamatát. Egy egyszerű modellt fejleszt Machine Learning Studio (klasszikus).  Ezután Azure Machine Learning webszolgáltatásként helyezheti üzembe a modellt.  Ez a központilag telepített modell új adatelemzést végezhet az előrejelzésekhez. Ez az oktatóanyag **egy háromrészes oktatóanyag-sorozat második része**.

Tegyük fel, hogy előrejelzést kell készíteni egy személy hitelkockázatáról az általa kitöltött hitelkérelemben megadott adatok alapján.  

A hitelkockázat-értékelés összetett probléma, de ez az oktatóanyag egy kicsit egyszerűbbé válik. Példaként fogja használni, hogyan hozhat létre prediktív elemzési megoldást Microsoft Azure Machine Learning Studio (klasszikus) használatával. Ehhez a megoldáshoz Azure Machine Learning Studio (klasszikus) és egy Machine Learning webszolgáltatást fog használni.  

Ebben a három részből álló oktatóanyagban nyilvánosan elérhető hitelkockázat-adatforrásokkal kezdheti meg.  Ezután fejlesztheti és betaníthatja a prediktív modellt.  Végül üzembe helyezi a modellt webszolgáltatásként.

[Az oktatóanyag első részében](tutorial-part1-credit-risk.md)létrehozott egy Machine learning Studio (klasszikus) munkaterületet, feltöltött és létrehozott egy kísérletet.

Az oktatóanyag ezen részében:
 
> [!div class="checklist"]
> * Több modell betanítása
> * A modellek pontszáma és kiértékelése


Az [oktatóanyag harmadik részében](tutorial-part3-credit-risk-deploy.md)webszolgáltatásként fogja telepíteni a modellt.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be [az oktatóanyag első részét](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Több modell betanítása

A gépi tanulási modellek létrehozásához Azure Machine Learning Studio (klasszikus) használatának egyik előnye, hogy egyetlen kísérlet során egyszerre több típusú modellt próbálnak ki, és összehasonlítjuk az eredményeket. Ez a fajta kísérletezés segít megtalálni a legjobb megoldást a problémára.

Az oktatóanyagban fejlesztés alatt álló kísérlet során két különböző típusú modellt hoz létre, majd összehasonlítja a pontozási eredményeit, hogy eldöntse, melyik algoritmust kívánja használni a végső kísérlet során.  

Többféle modell közül választhat. A rendelkezésre álló modellek megtekintéséhez bontsa ki a **Machine learning** csomópontot a modul palettáján, majd bontsa ki az **inicializálási modell** és az alatta lévő csomópontok elemet. Ebben a kísérletben kiválasztja a [kétosztályos támogató vektoros gépet][two-class-support-vector-machine] (SVM) és a [kétosztályos kibővített döntési][two-class-boosted-decision-tree] famodulokat.

> [!TIP]
> Ha szeretné eldönteni, hogy melyik Machine Learning algoritmus a legjobban megfelel a megoldani kívánt problémának, tekintse meg az [algoritmusok kiválasztása Microsoft Azure Machine learning Studio (klasszikus)](../how-to-select-algorithms.md)című témakört.
> 
> 

Ebben a kísérletben a [kétosztályos növelt döntési fa][two-class-boosted-decision-tree] modult és a [kétosztályos támogatás vektoros gépi][two-class-support-vector-machine] modulját is fel kell vennie.

### <a name="two-class-boosted-decision-tree"></a>Kétosztályos súlyozott döntési fa

Először állítsa be a megnövelt döntési fa modelljét.

1. Keresse meg a [kétosztályos növelt döntési fa][two-class-boosted-decision-tree] modult a modul palettáján, és húzza a vászonra.

1. Keresse meg a [betanítási modell][train-model] modult, húzza a vászonra, majd kapcsolja a [kétosztályos növelt döntési fa][two-class-boosted-decision-tree] modul kimenetét a [Train Model][train-model] modul bal oldali bemeneti portjához.
   
   A [kétosztályos felerősített döntési][two-class-boosted-decision-tree] famodul inicializálja az általános modellt, és a [betanítási modell][train-model] betanítási adatként használja a modell betanításához. 

1. A bal oldali [végrehajtási R script][execute-r-script] modul bal oldali kimenetének összekötése a [betanítási modell][train-model] moduljának jobb oldali bemeneti portjára (ebben az oktatóanyagban az adatok felosztása modul [bal oldaláról származó adatokat használta](#train) a betanításhoz).
   
   > [!TIP]
   > Ehhez a kísérlethez nem szükséges két bemenet és az [R-szkript végrehajtása][execute-r-script] modul egyik kimenete, hogy ne legyenek csatolva. 
   > 
   > 

A kísérletnek ez a része most a következőhöz hasonlóan néz ki:  

![Modell betanítása](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Most meg kell adnia a [betanítási modell][train-model] modulját, amely alapján a modell megjósolhatja a hitelkockázat értékét.

1. Válassza ki a [Train Model][train-model] modult. A **Tulajdonságok** ablaktáblán kattintson az **oszlop kiválasztó indítása** elemre.

1. Az **egyetlen oszlop kijelölése** párbeszédpanelen írja be a "hitelkockázat" kifejezést az **elérhető oszlopok** területen található Keresés mezőbe, válassza a "hitelkockázat" lehetőséget az alábbi listában, majd kattintson a jobbra mutató nyílra ( **>** ) a "hitelkockázat" gombra a **kijelölt oszlopokra**. 

    ![Válassza ki a betanítási modell moduljának hitelkockázat oszlopát](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Kattintson az **OK** pipa jelre.

### <a name="two-class-support-vector-machine"></a>Kétosztályos támogató vektorgép

Ezután állítsa be a SVM modellt.  

Először is, egy kis magyarázat a SVM-ről. A megnövelt döntési fák bármilyen típusú funkcióval jól működnek. Mivel azonban a SVM modul lineáris osztályozó hoz létre, az általa generált modell a legjobb tesztelési hibával rendelkezik, ha az összes numerikus funkció ugyanazzal a mérettel rendelkezik. Ha az összes numerikus funkciót ugyanarra a skálára szeretné átalakítani, használja a "TANH" transzformációt (az [adategység normalizálása][normalize-data] modullal). Ez átalakítja a számokat a [0, 1] tartományba. A SVM modul a karakterlánc-szolgáltatásokat a kategorikus funkciókra, majd a bináris 0/1-funkciókra konvertálja, így nem kell manuálisan átalakítania a karakterlánc-szolgáltatásokat. Azt is megteheti, hogy nem kívánja átalakítani a hitelkockázat oszlopot (21. oszlop) – Ez numerikus, de ez az az érték, amelyet a modell betanításával jósolunk meg, így egyedül kell hagyni.  

A SVM modell beállításához tegye a következőket:

1. Keresse meg a [kétosztályos támogatás Vector Machine][two-class-support-vector-machine] modult a modul palettáján, és húzza a vászonra.

1. Kattintson a jobb gombbal a [vonat modell][train-model] modulra, válassza a **Másolás** lehetőséget, majd kattintson a jobb gombbal a vászonra, és válassza a **Beillesztés** lehetőséget. A [betanítási modell][train-model] moduljának másolata ugyanazzal az oszloppal van kiválasztva, mint az eredeti.

1. A [kétosztályos támogatású vektoros gép][two-class-support-vector-machine] kimenetének összekötése a második [Train Model][train-model] -modul bal oldali bemeneti portjával.

1. Keresse meg az [adathalmaz normalizálása][normalize-data] modult, és húzza a vászonra.

1. Csatlakoztassa a bal oldali [végrehajtási R script][execute-r-script] modul bal oldali kimenetét a modul bemenetéhez (figyelje meg, hogy egy modul kimeneti portja több másik modulhoz is kapcsolódhat).

1. A [normalizált adat][normalize-data] modul bal oldali kimeneti portját a második [Train Model][train-model] -modul megfelelő bemeneti portjához kapcsolja.

A kísérlet ezen részében a következőhöz hasonló módon kell kinéznie:  

![A második modell betanítása](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Most konfigurálja a [normalizálás][normalize-data] adatmodult:

1. Kattintson ide az [Adatnormalizálás][normalize-data] modul kiválasztásához. A **Tulajdonságok** panelen válassza a **TANH** lehetőséget az **átalakítási módszer** paraméterhez.

1. Kattintson **az oszlop kiválasztásának indítása** lehetőségre, válassza a "nincs oszlop" lehetőséget a **kezdéshez**, **jelölje be az** első legördülő menüben válassza az **oszlop típusa** lehetőséget a második legördülő menüben, majd válassza a **numerikus** lehetőséget a harmadik legördülő menüben. Ez azt jelenti, hogy az összes numerikus oszlop (és csak numerikus) át lett alakítva.

1. Kattintson a sor jobb oldalán a pluszjelre (+), ez egy sort hoz létre a legördülő listáról. Válassza a **kizárás** lehetőséget az első legördülő menüben, majd a második legördülő listában válassza az **oszlopnevek** lehetőséget, majd a szövegmezőbe írja be a "hitelkockázat" értéket. Ez azt jelenti, hogy a hitelkockázat oszlopot figyelmen kívül kell hagyni (ezt azért kell megtennie, mert ez az oszlop numerikus, ezért a rendszer átalakítja, ha nem zárja ki).

1. Kattintson az **OK** pipa jelre.  

    ![Oszlopok kiválasztása az adatnormalizálás modulhoz](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


A [normalizálás][normalize-data] adatmodul most úgy van beállítva, hogy az összes numerikus oszlopon TANH-átalakítást végezzen, kivéve a hitelkockázat oszlopot.  

## <a name="score-and-evaluate-the-models"></a>A modellek pontszáma és kiértékelése

az [adatok felosztása][split] modul által elválasztott tesztelési adatok segítségével kiértékelheti a betanított modelleket. Ezután összehasonlíthatja a két modell eredményét, hogy megtudja, melyik generált jobb eredményeket.  

### <a name="add-the-score-model-modules"></a>A pontszám modell moduljainak hozzáadása

1. Keresse meg a [pontszám modell][score-model] modult, és húzza rá a vászonra.

1. Csatlakoztassa a [kétosztályos felerősített döntési fa][two-class-boosted-decision-tree] modulhoz csatlakoztatott [Train Model][train-model] modult a [pontszám modell][score-model] modul bal oldali bemeneti portjához.

1. A jobb oldali [végrehajtási R script][execute-r-script] modul (tesztelési adatok) összekötése a [pontszám-modell][score-model] modul megfelelő bemeneti portjára.

    ![Pontszám modell modul csatlakoztatva](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   A [pontszám modell][score-model] modul mostantól elvégezheti a kreditek adatait a tesztelési adatokból, futtathatja azt a modellen keresztül, és összehasonlíthatja a modell által generált előrejelzéseket a tesztelési adatok tényleges hitelkockázat oszlopával.

1. Másolja és illessze be a [pontszám modell][score-model] modult egy második másolat létrehozásához.

1. Csatlakoztassa a SVM-modell kimenetét (vagyis a [kétosztályos támogatású, a Kétosztályú támogató vektoros][two-class-support-vector-machine] modulhoz csatlakoztatott [Train Model][train-model] modul kimeneti portját) a második [pontszám-modell][score-model] modul bemeneti portjához.

1. A SVM modell esetében ugyanazt az átalakítást kell végrehajtania, mint a betanítási adatként. Ezért másolja ki és illessze be a [normalizálás][normalize-data] adatmodult egy második másolat létrehozásához, és kapcsolja össze a jobb oldali [végrehajtási R-szkript][execute-r-script] modulhoz.

1. A második [normalizált][normalize-data] adatmodul bal oldali kimenetét a második [pontszám modell][score-model] moduljának jobb oldali bemeneti portjára kell kapcsolni.

    ![Mindkét pontszám modell modulok csatlakoztatva](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>A modell kiértékelése modul hozzáadása

A két pontozási eredmény kiértékeléséhez és összehasonlításához használja a [modell kiértékelése][evaluate-model] modult.  

1. Keresse meg a [modell kiértékelése][evaluate-model] modult, és húzza rá a vászonra.

1. A [modell kiértékelése][evaluate-model] modul bal oldali bemeneti portjához kapcsolódjon a Kiemelt döntési famodellhez társított [pontszám modell][score-model] modul kimeneti portjához.

1. Kapcsolódjon a másik [pontszám modell][score-model] modulhoz a megfelelő bemeneti porthoz.  

    ![Modellhez kapcsolódó modul kiértékelése](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Futtassa a kísérletet, és vizsgálja meg az eredményeket

A kísérlet futtatásához kattintson a vászon alatti **Futtatás** gombra. Ennek futtatása eltarthat néhány percig. Az egyes modulokon futó forgó kijelzőn látható, hogy fut, majd egy zöld pipa jelzi, hogy mikor fejeződött be a modul. Ha az összes modulnál pipa szerepel, a kísérlet befejezte a futtatást.

A kísérletnek ekkor az alábbihoz hasonlóan kell kinéznie:  

![Mindkét modell kiértékelése](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Az eredmények ellenőrzéséhez kattintson a [modell kiértékelése][evaluate-model] modul kimeneti portjára, és válassza a **Megjelenítés** lehetőséget.  

A [modell kiértékelése][evaluate-model] modul olyan görbéket és mérőszámokat hoz létre, amelyek lehetővé teszik a két pontozásos modell eredményének összehasonlítását. Az eredményeket megtekintheti a fogadó operátor jellemző (ROC) görbék, a pontosság/visszahívás görbék vagy a lift görbék használatával. További megjelenített adatok: a görbe (AUC) alatti terület összesített értékei, valamint egyéb mérőszámok. A küszöbértéket a csúszka balra vagy jobbra mozgatásával módosíthatja, és megtekintheti, hogyan befolyásolja a metrikák készletét.  

A gráftól jobbra kattintson a **pontozásos adatkészlet** vagy a **pontozásos adatkészlet** elemre a társított görbe kiemeléséhez és az alábbi kapcsolódó mérőszámok megjelenítéséhez. A görbék jelmagyarázatában a "pontozásos adatkészlet" a [modell kiértékelése][evaluate-model] modul bal oldali bemeneti portjához tartozik – ebben az esetben ez a megnövelt döntési famodell. "Az összehasonlítandó adathalmaz" megfelel a megfelelő bemeneti portnak – a SVM modell. Ha az egyik címkére kattint, a rendszer kijelöli a modell görbéjét, és megjeleníti a megfelelő mérőszámokat, ahogy az alábbi ábrán is látható.  

![ROC-görbék modellek számára](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ezeknek az értékeknek a vizsgálatával eldöntheti, hogy melyik modell legyen a legközelebb a keresett eredmények megadásához. Visszatérhet a kísérlethez, és megismételheti a paramétereket a különböző modellekben lévő paraméterek értékének módosításával. 

Az eredmények értelmezése és a modell teljesítményének finomhangolása az oktatóanyag hatókörén kívül esik. További segítségért olvassa el a következő cikkeket:
- [Modell teljesítményének kiértékelése Azure Machine Learning Studio (klasszikus)](evaluate-model-performance.md)
- [Paraméterek kiválasztása az algoritmusok optimalizálásához Azure Machine Learning Studio (klasszikus)](algorithm-parameters-optimize.md)
- [A modell eredményeinek értelmezése Azure Machine Learning Studio (klasszikus)](interpret-model-results.md)

> [!TIP]
> Minden alkalommal, amikor futtatja a kísérletet, a rendszer a futtatási előzményekben tárolja az iterációs rekordokat. Ezeket az iterációkat megtekintheti, és visszatérhet bármelyikhez a vászon alatti **futtatási előzmények megtekintése** lehetőségre kattintva. A **Tulajdonságok** ablaktáblán a **korábbi Futtatás** lehetőségre kattintva visszatérhet a megnyíló előtt közvetlenül megjelenő iterációhoz.
> 
> A kísérlet ismétlésének másolatát a vászon alatti **Mentés** gombra kattintva teheti meg. 
> A kísérlet **összefoglalásának** és **leírásának** tulajdonságaival rögzítheti, hogy mit próbált meg a kísérlet ismétlései között.
> 
> További információ: [a kísérletek közelítésének kezelése Azure Machine learning Studio (klasszikus)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következő lépéseket végezte el: 
 
> [!div class="checklist"]
> * Kísérlet létrehozása
> * Több modell betanítása
> * A modellek pontszáma és kiértékelése

Most már készen áll az ilyen típusú adatmodellek üzembe helyezésére.

> [!div class="nextstepaction"]
> [3. oktatóanyag – Modellek üzembe helyezése](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/