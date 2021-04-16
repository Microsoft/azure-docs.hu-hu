---
title: Modell értelmezhetősége a Azure Machine Learning (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan & a gépi tanulási modell előrejelzéseit a betanítás során, & a Python SDK használatával Azure Machine Learning dedukciót.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: c517cf2fc8491d62cf2379c87acd2eaadde8fe15
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576431"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Modell értelmezhetősége a Azure Machine Learning (előzetes verzió)


## <a name="model-interpretability-overview"></a>A modell értelmezhetősége – áttekintés

A modell értelmezhetősége kritikus fontosságú az adatszakértők, az auditorok és az üzleti döntéshozók számára a vállalati szabályzatoknak, iparági szabványoknak és kormányzati szabályozásoknak való megfelelés biztosítása érdekében:

+ Az adattudósok számára szükség van arra, hogy elmagyarázzák modelljeiket a vezetőknek és az érdekelt feleknek, hogy megértsék az eredményeik értékét és pontosságát. A modellek hibakeresése és a továbbfejlhetőségükről hozott tájékozott döntések meghozatalához is értelmezhetőkre van szükségük. 

+ A jogi auditorok olyan eszközöket igényelnek, amelyek ellenőrzik a modelleket a jogszabályi megfelelőség szempontjából, és figyelik, hogy a modellek döntései milyen hatással vannak az emberekre. 

+ Az üzleti döntéshozóknak a végfelhasználói átláthatóságot szem előtt kell tartaniuk. Így elnyerik és fenntartják a bizalmukat.

A gépi tanulási modell magyarázatának képessége a modellfejlesztés két fő fázisában fontos:

+ A betanítás során a modelltervezők és -kiértékelők a modellek értelmezhetőségi kimenetével ellenőrizhetik a hipotéziseket, és megbízhatóságot építhetnek ki az érdekelt felekkel. Emellett a modell elemzéseit is használják a hibakereséshez, a modell viselkedésének ellenőrzéshez és a célkitűzéseknek való megfelelőség ellenőrzéshez, valamint a modell méltetlenségére vagy funkcióinak ellenőrzésére.

+ A következtetési fázisban, mivel az üzembe helyezett modellek átláthatósága lehetővé teszi a vezetők számára, hogy megértsék, hogyan működik a modell, és hogyan kezelik és befolyásolják a döntések az embereket a való életben. 

## <a name="interpretability-with-azure-machine-learning"></a>Értelmezhetőség a Azure Machine Learning

A modell értelmezhetőségi osztályát a következő SDK-csomaggal lehet elérhetővé tenni: (Ismerje meg, hogyan telepíthet SDK-csomagokat [a Azure Machine Learning](/python/api/overview/azure/ml/install))

* `azureml.interpret`A a Microsoft által támogatott funkciókat tartalmazza.

Általános `pip install azureml-interpret` felhasználáshoz használja a-t.

## <a name="how-to-interpret-your-model"></a>A modell értelmezése

Az SDK osztályokkal és metódusokkal a következő módokon járhat el:
+ A modell előrejelzésének magyarázata a funkció fontos értékeinek generálása a teljes modell és/vagy az egyes adatpontok számára. 
+ Valós adatkészletek modellértelmének nagy léptékű értelmezhetősége a betanítás és a következtetés során.
+ Interaktív vizualizációs irányítópult használata az adatok mintáinak és magyarázatának felderítéséhez a betanítás során

A gépi **tanulásban a jellemzők** a céladatpont előrejelzéséhez használt adatmezők. A hitelkockázat előrejelzéséhez például használhatók az életkor, a fiókméret és a fiók életkorának adatmezői. Ebben az esetben az életkor, a fiók mérete és a fiók kora **funkciók.** A funkció fontossága azt mutatja meg, hogy az egyes adatmezők milyen hatással vannak a modell előrejelzésére. Az életkort például erősen fel lehet használni az előrejelzésben, míg a fiók mérete és kora nem befolyásolja jelentősen az előrejelzési értékeket. Ez a folyamat lehetővé teszi az adattudósok számára az eredményül kapott előrejelzések magyarázatát, hogy az érintettek átlássák a modell legfontosabb jellemzőit.

## <a name="supported-interpretability-techniques"></a>Támogatott értelmezhetőségi technikák

 `azureml-interpret` az Interpret-Community (Közösség értelmezése) által kifejlesztett értelmezhetőségi technikákat használja, amely egy nyílt forráskódú [Python-csomag](https://github.com/interpretml/interpret-community/)az értelmezhető modellek betanítása és a feketedobozos AI-rendszerek magyarázatának segítése érdekében. [Az Interpret-Community](https://github.com/interpretml/interpret-community/) az SDK támogatott magyarázóinak gazdaje, és jelenleg a következő értelmezési technikákat támogatja:

|Értelmezhetőségi technika|Leírás|Típus|
|--|--|--------------------|
|SHAP-fa magyarázó| [Az SHAP](https://github.com/slundberg/shap)fa magyarázója, amely a polinom idő gyors SHAP-értékbecslési algoritmusával foglalkozik, a fákra és a fák **együttesére jellemzően.**|Modellspecifikus|
|SHAP – Mély magyarázó| Az SHAP magyarázata alapján a Deep Explainer "egy nagy sebességű közelítő algoritmus a mélytanulásmodellek SHAP-értékeihez, amely az [SHAP NIPS-tanulmányban](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)leírt Deep ALGORITHM-kapcsolatra épül. **A TensorFlow-háttért** használó TensorFlow-modellek és **Keras-modellek** támogatottak (a PyTorch előzetes támogatása is elérhető)."|Modellspecifikus|
|SHAP Linear Explainer| Az SHAP lineáris magyarázója SHAP-értékeket számít ki egy lineáris **modellhez,** opcionálisan a funkciók közötti korrelációk könyvét is.|Modellspecifikus|
|SHAP kernel magyarázó| Az SHAP Kernel-magyarázó egy speciálisan súlyozott helyi lineáris regressziót használ bármely modell **SHAP-értékeinek becsléséhez.**|Modelltől független|
|Mimic Explainer (Global Surrogate)| Az utánozó magyarázat alapja a [](https://christophm.github.io/interpretable-ml-book/global.html) globális helyettes modellek betanítása a feketedoboz-modellek utánzása során. A globális helyettes modell egy belsőleg értelmezhető modell, amely úgy  van betanítva, hogy a lehető legpontosabban megbecsülje a fekete dobozos modellek előrejelzéseit. Az adattudósok értelmezik a helyettes modellt, hogy következtetéseket vonnak le a fekete doboz modellel kapcsolatban. Helyettes modellként a következő értelmezhető modellek egyikét használhatja: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) és Decision Tree (DecisionTreeExplainableModel).|Modelltől független|
|Permutáció funkció fontossága magyarázó (PFI)| A permutáció funkció fontossága egy olyan módszer, amely a [Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) Véletlenszerű erdők című tanulmánya által ihletett besorolási és regressziós modellek magyarázatára használható (lásd a 10. szakaszt). Magas szinten úgy működik, hogy az adatokat véletlenszerűen megküldi egy jellemzővel a teljes adatkészletre, és kiszámítja, hogy mennyiben változik az érdeklődési mutató teljesítménymetrika. Minél nagyobb a változás, annál fontosabb a funkció. A PFI bármely mögöttes modell általános viselkedését ismerteti, az egyes előrejelzéseket azonban nem.  |Modelltől független|

A fent ismertetett értelmezhetőségi technikák mellett egy másik SHAP-alapú magyarázó is támogatott, a neve `TabularExplainer` . A modelltől függően `TabularExplainer` a a támogatott SHAP-magyarázók valamelyikét használja:

* TreeExplainer minden faalapú modellhez
* DeepExplainer DNN-modellekhez
* LinearExplainer lineáris modellekhez
* KernelExplainer az összes többi modellhez

`TabularExplainer` A emellett jelentős funkciókat és teljesítménybeli fejlesztéseket is tett a közvetlen SHAP-magyarázókhoz:

* **Az inicializálási adatkészlet összegzése.** Olyan esetekben, ahol a magyarázat sebessége a legfontosabb, összefoglaljuk az inicializálási adatkészletet, és létrehozunk egy kis mintakészletet, amely felgyorsítja az általános és az egyes funkciók fontos értékeinek előállítását.
* **Mintavételezés a kiértékelési adatkészletből.** Ha a felhasználó nagy mennyiségű kiértékelési mintát ad át, de ténylegesen nem szükséges mindegyiket kiértékelni, a mintavételezési paraméter true (igaz) értékére is beállítható, hogy felgyorsítsa a modell általános magyarázatának kiszámítását.

Az alábbi ábrán a támogatott magyarázók jelenlegi struktúrája látható.

[![Machine Learning architektúra értelmezése](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Támogatott gépi tanulási modellek

Az SDK csomagja a következő adatkészlet-formátumokkal `azureml.interpret` betanított modelleket támogatja:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

A magyarázati függvények bemenetként a modelleket és a folyamatokat is elfogadják. Ha a modell meg van téve, a modellnek implementálja az előrejelzési függvényt, vagy amely megfelel a `predict` `predict_proba` Scikit-konvenciónak. Ha a modell ezt nem támogatja, becsomagolhatja a modellt egy olyan függvénybe, amely ugyanazt az eredményt hozza létre, mint a Scikit vagy a , és használhatja ezt a burkoló függvényt a kiválasztott `predict` `predict_proba` magyarázóval. Ha meg van téve egy folyamat, a magyarázati függvény feltételezi, hogy a futó folyamatszk szkript előrejelzést ad vissza. Ezzel a burkoló technikával a `azureml.interpret` PyTorch, a TensorFlow és a Keras mélytanulási keretrendszerekkel betanított modelleket, valamint a klasszikus gépi tanulási modelleket is támogatja.

## <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási cél

A `azureml.interpret` csomag úgy lett kialakítva, hogy helyi és távoli számítási célokkal is működjön. Helyi futtatás esetén az SDK-függvények nem fognak kapcsolatba lépni egyik Azure-szolgáltatáshoz sem. 

A magyarázatokat távolról futtathatja a Compute Azure Machine Learning, és naplózhatja a magyarázati adatokat a Azure Machine Learning előzmények szolgáltatásban. Miután naplózza ezt az információt, a magyarázatból származó jelentések és vizualizációk azonnal elérhetők Azure Machine Learning stúdió felhasználói elemzéshez.


## <a name="next-steps"></a>Következő lépések

- Tekintse meg [a helyileg](how-to-machine-learning-interpretability-aml.md) és a távoli számítási erőforrásokon betanított modellek Azure Machine Learning értelmezhetőségét. 
- Megtudhatja, hogyan engedélyezheti [az automatizált gépi tanulási modellek értelmezhetőségét.](how-to-machine-learning-interpretability-automl.md)
- További [forgatókönyvekért tekintse meg](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) a mintajegyzetfüzeteket. 
- Ha érdekli a szöveges forgatókönyvek értelmezhetősége, az NLP-re vonatkozó értelmezhetőségi technikákért lásd: [Interpret-text](https://github.com/interpretml/interpret-text)( Szöveg értelmezése) , az [Interpret-Community](https://github.com/interpretml/interpret-community/)(Közösségi értelmező közösség) egy kapcsolódó nyílt forráskódú tára. `azureml.interpret`A csomag jelenleg nem támogatja ezeket a technikákat, de a szövegbesorolást egy [példajegyzetfüzettel is elkezdheti.](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)