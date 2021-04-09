---
title: Modell értelmezése Azure Machine Learningban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan értelmezheti & a gépi tanulási modell előrejelzéseit az Azure Machine Learning Python SDK-val való & következtetések betanítása során.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: 44ccf6b6d2459b87040fcac7d9cdcd336cc7b82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522036"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Modell értelmezése Azure Machine Learningban (előzetes verzió)


## <a name="model-interpretability-overview"></a>Modell-értelmező áttekintése

A modell értelmezése kritikus fontosságú az adatszakértők, a könyvvizsgálók és az üzleti döntéshozók számára, hogy biztosítsák a vállalati szabályzatoknak, az iparági szabványoknak és a kormányzati szabályozásoknak való megfelelést:

+ Az adatszakértőknek meg kell adniuk modelljeiket a vezetők és az érdekelt felek számára, hogy megértsék az eredmények értékét és pontosságát. Emellett a modelljeik hibakereséséhez és a megalapozott döntések meghozatalához is szükségesek. 

+ A jogi ellenőrök olyan eszközöket igényelnek, amelyekkel a modelleket érvényesíteni kell a jogszabályoknak való megfelelés tekintetében, és azt is, hogy a modellek milyen hatással vannak az emberi 

+ Az üzleti döntéshozóknak szem előtt kell tartaniuk az átláthatóságot a végfelhasználók számára. Ez lehetővé teszi, hogy a megbízhatóságot megszerezzék és megőrizzék.

A modell fejlesztésének két fő fázisában fontos a gépi tanulási modell elmagyarázása.

+ A betanítási fázisban a modell-tervezők és-értékelők a modell értelmező kimenetét használhatják a hipotézisek ellenőrzéséhez és az érdekelt felekkel való bizalom kiépítéséhez. Emellett a modellen alapuló elemzéseket is felhasználják a hibakereséshez, a modell viselkedésének érvényesítéséhez, valamint a modell méltánytalan vagy jelentéktelen funkcióinak ellenőrzéséhez.

+ A következtetési fázisban az üzembe helyezett modellek átláthatósága lehetővé teszi a vezetők számára, hogy megértsék a modell működésének módját, valamint azt, hogy a rendszer hogyan kezeli és befolyásolja a valós életben lévő döntéseket. 

## <a name="interpretability-with-azure-machine-learning"></a>Értelmezés Azure Machine Learning

A modell-értelmező osztályok a következő SDK-csomagon keresztül érhetők el: (útmutató az [SDK-csomagok telepítéséhez a Azure Machine learning](/python/api/overview/azure/ml/install))

* `azureml.interpret`, a Microsoft által támogatott funkciókat tartalmaz.

`pip install azureml-interpret`Általános használatra használható.

## <a name="how-to-interpret-your-model"></a>A modell értelmezése

Az SDK osztályok és metódusok használatával a következőket teheti:
+ Magyarázza el a modell előrejelzését azáltal, hogy a teljes modell és/vagy az egyes datapoints esetében a szolgáltatás fontossági értékeit hozza létre. 
+ A valós idejű adatkészletek modell-értelmezésének megvalósítása a képzés és a következtetések során.
+ Interaktív vizualizációs irányítópult használatával felderítheti az adatmintákat és magyarázatokat a képzés ideje alatt

A gépi tanulásban a **funkciók** a célként megadott adatpontok előrejelzésére szolgáló adatmezők. Például a hitelkockázat előrejelzéséhez az életkor, a fiók mérete és a fiók kora adatmezőket lehet használni. Ebben az esetben a kor, a fiók mérete és a fiók kora **funkciók**. A szolgáltatás fontossága azt mutatja be, hogy az egyes adatmezők hogyan érintik a modell előrejelzéseit. Előfordulhat például, hogy az életkor nagy mértékben használatban van az előrejelzésben, míg a fiók mérete és kora nem befolyásolja jelentősen az előrejelzési értékeket. Ez a folyamat lehetővé teszi, hogy az adatszakértők elmagyarázzák az eredményül kapott előrejelzéseket, így az érdekelt felek betekintést nyerhetnek a modellbe a legfontosabb funkciókba.

## <a name="supported-interpretability-techniques"></a>Támogatott értelmezési technikák

 `azureml-interpret` a az [értelmezés – Közösség](https://github.com/interpretml/interpret-community/), a értelmezhető modellek betanítására szolgáló nyílt forráskódú Python-csomag, valamint a tábla AI-rendszerek ismertetésére szolgáló, értelmezhető technikákat használja. A [tolmácsolás – a Közösség](https://github.com/interpretml/interpret-community/) az SDK által támogatott magyarázatokat üzemeltető gazdagépként működik, és jelenleg a következő értelmező módszereket támogatja:

|Értelmező technika|Leírás|Típus|
|--|--|--------------------|
|ALAKÍTÁSi fa magyarázata| A SHAP 's Tree [Deformálója](https://github.com/slundberg/shap), amely a **fák és a fák különböző részeire** jellemző, a többhelyes idő gyors formálására szolgáló algoritmusra koncentrál.|Modell-specifikus|
|Mélyreható magyarázat kialakítása| A SHAPer magyarázata alapján a Deep deformáló "egy nagy sebességű közelítési algoritmus az értékek alakításához a Deep learning-modellekben, amely egy, a [SHAP](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)-beli DeepLIFT-ben leírt módon létesített kapcsolatban. A **TensorFlow** modellek és **kerasz** modellek támogatottak a TensorFlow-háttér használatával (a PyTorch előzetes támogatása is elérhető).|Modell-specifikus|
|Lineáris elmagyarázó ALAKÍTÁS| A SHAP lineáris elmagyarázó számítási funkciói a **lineáris modell** értékeit, opcionálisan elvégzik a szolgáltatások közötti korrelációk elszámolását.|Modell-specifikus|
|A rendszermag magyarázatának kialakítása| A SHAP kernel-magyarázata egy speciálisan súlyozott, helyi lineáris regressziót használ az **egyes modellek** formálási értékeinek becsléséhez.|Modell – agnosztikus|
|Elmagyarázó (globális helyettes)| Az adatutánozás elmagyarázása a [globális helyettesítő modellek](https://christophm.github.io/interpretable-ml-book/global.html) a tábla modelljeinek utánzására való betanításának gondolatán alapul. A globális helyettesítő modell egy belsőleg értelmezhető modell, amely úgy van kitanítva, hogy a lehető legpontosabban közelítse meg a **fekete Box-modellek** előrejelzéseit. Az adatszakértők a helyettesítő modellt úgy tudják értelmezni, hogy levonja a fekete Box-modellel kapcsolatos következtetéseket. A következő értelmezhető modellek egyikét használhatja helyettesítő modellként: LightGBM (LGBMExplainableModel), lineáris regresszió (LinearExplainableModel), sztochasztikus gradiens deillatos modell (SGDExplainableModel) és döntési fa (DecisionTreeExplainableModel).|Modell – agnosztikus|
|A permutáció funkció fontossági magyarázata (PFI)| A permutáció funkció fontossága egy olyan módszer, amely a [Breiman véletlenszerű erdőkkel kapcsolatos tanulmányai](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) által ihletett besorolási és regressziós modellek magyarázatára szolgál (lásd: 10. szakasz). Magas szinten a működésének módja az, hogy a teljes adatkészlet esetében véletlenszerűen végzi el az adatok egy funkciójának a kiszámítását, és kiszámítja, hogy a teljesítmény mérőszáma milyen mértékben változik. Minél nagyobb a változás, annál fontosabb a funkció. A PFI megmagyarázhatja a **mögöttes modellek** általános viselkedését, de nem magyarázza el az egyes előrejelzéseket. |Modell – agnosztikus|

A fent ismertetett értelmező technikák mellett egy másik, a nevű SHAP-alapú magyarázat is támogatott `TabularExplainer` . A modelltől függően `TabularExplainer` a támogatott SHAP-magyarázatok egyikét használja:

* TreeExplainer az összes fa alapú modellhez
* DeepExplainer DNN-modellekhez
* LinearExplainer lineáris modellekhez
* KernelExplainer az összes többi modellhez

`TabularExplainer` jelentős funkciókkal és teljesítménnyel kapcsolatos fejlesztéseket hajtott végre a Direct SHAP-magyarázatokkal:

* **Az inicializálási adatkészlet összefoglalása**. Azokban az esetekben, amikor a magyarázat sebessége a legfontosabb, összefoglaljuk az inicializálási adatkészletet, és létrehozunk egy kis reprezentatív mintát, amely felgyorsítja a teljes és az egyes funkciók fontossági értékeinek létrehozását.
* **A kiértékelési adatkészlet mintavételezése**. Ha a felhasználó a kiértékelési minták nagy készletét adja vissza, de valójában nem szükséges mindegyiket kiértékelni, a mintavételi paraméter értéke TRUE (igaz) lehet, hogy felgyorsítsa a teljes modell magyarázatának kiszámítását.

Az alábbi ábrán a támogatott magyarázatok aktuális szerkezete látható.

[![Machine Learning-értelmező architektúra](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Támogatott gépi tanulási modellek

Az `azureml.interpret` SDK csomagja a következő adatkészlet-formátumokkal betanított modelleket támogatja:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

A magyarázó függvények bemenetként is elfogadják a modelleket és a folyamatokat. Ha meg van adni modell, a modellnek meg kell valósítania az előrejelzési függvényt, `predict` vagy `predict_proba` a Scikit egyezménynek megfelelően kell megfelelnie. Ha a modell nem támogatja ezt, becsomagolhatja a modellt egy olyan függvénybe, amely ugyanazt az eredményt hozza létre, mint a `predict` `predict_proba` Scikit, és ezt a burkoló függvényt használja a kiválasztott magyarázattal. Ha egy folyamat van megadva, a magyarázat függvény azt feltételezi, hogy a futó folyamat parancsfájlja egy előrejelzést ad vissza. Ezzel a burkoló módszerrel a `azureml.interpret` PyTorch-, TensorFlow-és kerasz-alapú modelleket, valamint a klasszikus gépi tanulási modelleket is támogathatja.

## <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási cél

A `azureml.interpret` csomag úgy van kialakítva, hogy a helyi és távoli számítási célokkal is működjön. Ha helyileg fut, az SDK-függvények nem fognak kapcsolatba lépni az Azure-szolgáltatásokkal. 

A magyarázatot távolról is futtathatja Azure Machine Learning számításon, és naplózhatja a magyarázat adatait a Azure Machine Learning futtatási előzmények szolgáltatásba. Az információk naplózása után a magyarázatokból származó jelentések és vizualizációk azonnal elérhetők a Azure Machine Learning Studióban a felhasználók elemzéséhez.


## <a name="next-steps"></a>Következő lépések

- Tekintse meg az [útmutató](how-to-machine-learning-interpretability-aml.md) a modellek helyi és Azure Machine learning távoli számítási erőforrásokon való értelmezésének engedélyezéséhez című témakört. 
- További forgatókönyvek: [minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) . 
- Ha érdekli a szöveges forgatókönyvek értelmezése, tekintse meg a következőt: [értelmezési szöveg](https://github.com/interpretml/interpret-text), egy kapcsolódó nyílt forráskódú tárház, amely a [közösségi értelmezést](https://github.com/interpretml/interpret-community/)értelmezi a NLP értelmezési módszereiben. `azureml.interpret` a csomag jelenleg nem támogatja ezeket a technikákat, de megkezdheti a [szöveges besorolású jegyzetfüzetek](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)használatát.