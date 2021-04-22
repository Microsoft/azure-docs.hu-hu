---
title: Mi az automatizált gépi tanulási folyamat? Auto ML
titleSuffix: Azure Machine Learning
description: Megtudhatja Azure Machine Learning hogyan hozhat létre automatikusan egy modellt az Ön által megadott paraméterekkel és feltételekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 6f8f775e474b47cbfd5f3b4aca8987a009a7f6e1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874134"
---
# <a name="what-is-automated-machine-learning-automl"></a>Mi az automatizált gépi tanulás (AutoML)?

Az automatizált gépi tanulás( más néven automatizált gépi tanulás vagy AutoML) a gépi tanulási modell fejlesztésének időigényes, iteratív feladatainak automatizálása. Lehetővé teszi az adattudósok, elemzők és fejlesztők számára, hogy nagy léptékben, hatékonysággal és termelékenységgel építsenek gépi tanulási modelleket a modell minőségének fenntartása mellett. Az automatizált gépi Azure Machine Learning a Microsoft [Research részlegének áttörésén alapul.](https://www.microsoft.com/research/project/automl/)

A hagyományos gépi tanulási modellfejlesztés erőforrás-igényes, és jelentős tartományi tudást és időt igényel több tucat modell előállításához és összehasonlításhoz. Az automatizált gépi tanulással felgyorsíthatja az éles használatra kész gépi tanulási modellek könnyű és hatékony előállításához szükséges időt.

## <a name="automl-in-azure-machine-learning"></a>AutoML a Azure Machine Learning

Azure Machine Learning gépi tanulási folyamat kétféle módon működik:

* A kódokkal tapasztalt ügyfelek esetében használja [Azure Machine Learning Python SDK-t.](/python/api/overview/azure/ml/intro)  Az oktatóanyag [használatának első lépése: Automatizált gépi tanulás a taxik árainak előrejelzése érdekében.](tutorial-auto-train-models.md)

* A korlátozott vagy nem tapasztalt kódú ügyfelek esetében a Azure Machine Learning stúdió: [https://ml.azure.com](https://ml.azure.com/) .  Első lépések az alábbi oktatóanyagokkal:
    * [Oktatóanyag: Besorolási modell létrehozása automatizált gépi tanulási modellel](tutorial-first-experiment-automated-ml.md)a Azure Machine Learning.
    *  [Oktatóanyag: Igény előrejelzése automatizált gépi tanulással](tutorial-automated-ml-forecast.md)


## <a name="when-to-use-automl-classify-regression--forecast"></a>Mikor használható az AutoML: osztályozás, regresszió, & előrejelzés

Automatizált gépi tanulási módszer alkalmazása, Azure Machine Learning betaníthatja és finomhangolhatja a modellt a megadott célmetrikával. Az automatizált gépi tanulás demokratizálja a gépi tanulási modell fejlesztési folyamatát, és lehetővé teszi a felhasználók számára, hogy az adattudományi szakértelmüktől függetlenül minden problémához azonosítsák a teljes gépi tanulási folyamatot.

Az iparágak adatelemzői, elemzői és fejlesztői automatizált gépi tanulási megoldásokat használhatnak a következő műveletekhez:
+ Gépi tanulási megoldások megvalósítása részletes programozási ismeretek nélkül
+ Idő és erőforrások megtakarítása
+ Az adattudomány ajánlott eljárásának kihasználása
+ Agilis problémamegoldás

### <a name="classification"></a>Osztályozás

A besorolás gyakori gépi tanulási feladat. A besorolás a felügyelt tanulás egy olyan típusa, amelyben a modellek betanításadatok használatával tanulnak, és ezeket a tanulásokat új adatokra alkalmazzák. Azure Machine Learning funkciókat kínál kifejezetten ezekre a feladatokra, például mély neurális hálózati szövegre vonatkozó funkciókat a besoroláshoz. További információ a [deaturizálási lehetőségekről.](how-to-configure-auto-features.md#featurization) 

A besorolási modellek fő célja annak előrejelzése, hogy az új adatok milyen kategóriákba sorolhatók a betanítás adataiból tanultak alapján. Gyakori besorolási példák például a csalásészlelés, a kézírás-felismerés és az objektumészlelés. További információ és példa: [Besorolási modell létrehozása automatizált gépi tanulással.](tutorial-first-experiment-automated-ml.md)

A besorolásra és az automatizált gépi tanulásra vonatkozó példákat a következő Python-jegyzetfüzetek tartalmaznak: [Fraud Detection](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)(Csalásészlelés, [Marketing-előrejelzés)](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)és [Newsgroup Data Classification (Hírcsoport adatbesorolása)](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regresszió

A besoroláshoz hasonlóan a regressziós feladatok is gyakran felügyelt tanulási feladatok. Azure Machine Learning kifejezetten ezekhez a feladatokhoz kínál [featurizálásokat.](how-to-configure-auto-features.md#featurization)

A besorolástól eltérő, ahol az előrejel jelzett kimeneti értékek kategorikusak, a regressziós modellek numerikus kimeneti értékeket jeleznek előre független előrejelzők alapján. A regresszió célja, hogy segítsen a független prediktív változók közötti kapcsolat létesítésében azzal, hogy megbecsüli, hogy egy változó milyen hatással van a többire. Ilyen például az autók ára például olyan jellemzők alapján, mint a üzemanyag-, a biztonsági minősítés stb. További információ és egy példa az automatizált gépi [tanulással történő regresszióra.](tutorial-auto-train-models.md)

Az előrejelzéseket a következő Python-jegyzetfüzetek példáiban láthatja: [CPU-teljesítmény-előrejelzés,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) 

### <a name="time-series-forecasting"></a>Idősorozat-előrejelzés

Az előrejelzések kiépítése minden üzlet szerves részét képezi, legyen szó bevételről, leltárról, értékesítésről vagy ügyféligényről. Az automatizált gépi tanulási megoldásokkal technikákat és megközelítéseket kombinálhat, és egy ajánlott, kiváló minőségű idősorozat-előrejelzést kaphat. További információ: automatizált gépi tanulás [idősorozat-előrejelzéshez.](how-to-auto-train-forecast.md) 

Az automatizált idősorozat-kísérleteket a rendszer többválgásos regressziós problémaként kezeli. A korábbi idősorozat-értékek "elfordulnak", így a regresszió további dimenzióivá válnak más előrejelzőkkel együtt. Ez a megközelítés a klasszikus idősorozat-metódusokkal ellentétben annak az előnye, hogy a betanítás során természetes módon épít be több környezeti változót és azok közötti kapcsolatot. Az automatizált gépi tanulás egyetlen, de gyakran belsőleg elágazó modellt tanul meg az adatkészlet és az előrejelzési horizont összes eleméhez. Így több adat áll rendelkezésre a modellparaméterek becsléséhez, és lehetségessé válik a ismeretlen sorozatok általánosítása.

A speciális előrejelzési konfiguráció a következőket tartalmazza:
* ünnepi észlelés és featurálás
* idősoros és DNN-tanulók (Auto-ARIMA, Feljav, ForecastTCN)
* számos modell támogatja a csoportosítást
* gördülő eredet keresztellenőrzése
* konfigurálható késések
* a gördülő ablak összesítési funkciói


Az előrejelzésekhez a regresszió és az automatizált gépi tanulás példáit a következő Python-jegyzetfüzetekben láthatja: [Értékesítési](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) [előrejelzés,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)Igény-előrejelzés és [Ital-termelés előrejelzése.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="how-automated-ml-works"></a>Az automatizált gépi tanulási folyamat működése

A betanítás Azure Machine Learning több folyamatot hoz létre párhuzamosan, amelyek különböző algoritmusokat és paramétereket próbálnak ki. A szolgáltatás a funkcióválasztásokkal párosított ML-algoritmusokkal iterál, ahol minden iteráció betanítási pontszámmal hoz létre modellt. Minél magasabb a pontszám, annál jobb a modell úgy tekinteni, hogy "illeszkedik" az adatokhoz.  Amint eléri a kísérletben meghatározott kilépési feltételeket, leáll. 

A **Azure Machine Learning** használatával az alábbi lépésekkel tervezheti meg és futtathatja az automatizált gépi tanulási betanítás kísérleteit:

1. **A megoldható gépi** tanulási probléma azonosítása: besorolás, előrejelzés vagy regresszió

1. **Válassza ki, hogy a Python SDK-t** vagy a Studio webes élményét szeretné használni: További információ a Python SDK és a Studio webes élménye közötti [paritásról.](#parity)

   * A korlátozott kódélmény érdekében próbálja ki Azure Machine Learning stúdió webes élményt a következő webhelyen: [https://ml.azure.com](https://ml.azure.com/)  
   * Python-fejlesztőknek tekintse meg az Azure Machine Learning [Python SDK-t](how-to-configure-auto-train.md) 
    
1. Adja meg a címkézett betanítás adatainak forrását és **formátumát:** Numpy-tömbök vagy Pandas-adatkeret

1. **Konfigurálja a modell betanításra** vonatkozó számítási célt, például a helyi számítógépet, a [számítási Azure Machine Learning,](how-to-set-up-training-targets.md)a távoli virtuális gépeket vagy a Azure Databricks.

1. **Konfigurálja** az automatizált gépi tanulási paramétereket, amelyek meghatározzák a különböző modelleken történő iterációk, a hiperparaméter-beállítások, a speciális előfeldolgozási/featurizálási beállítások, valamint a legjobb modell meghatározásakor megvetni szükséges metrikákat.  
1. **Küldje el a betanítás futtatását.**

1. **Az eredmények áttekintése** 

Az alábbi ábra ezt a folyamatot mutatja be. 
![Automatizált gépi tanulás](./media/concept-automated-ml/automl-concept-diagram2.png)


A naplózott futtatás adatait is [](how-to-understand-automated-ml.md) megvizsgálhatja, amelyek a futtatás során gyűjtött metrikákat tartalmaznak. A betanítás futtatása létrehoz egy Python-szerializált objektumot ( fájlt), amely tartalmazza a modellt és az adatok `.pkl` előfeldolgozását.

Bár a modell létrehozása automatizálva van, azt is megtudhatja, mennyire fontosak vagy [relevánsak](how-to-configure-auto-train.md#explain) a létrehozott modellek.



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Jellemzőkiemelés

A jellemzőkiépítés az a folyamat, amely során az adatok tartományismeretei alapján olyan funkciókat hozunk létre, amelyek segítenek a gépi tanulási algoritmusok jobb elsajátításában. A Azure Machine Learning a skálázás és a normalizálás technikáját alkalmazva segítik elő a jellemzőtervezést. Ezeket a technikákat és jellemzősítéseket együttesen jellemzősítésnek nevezzük.

Automatizált gépi tanulási kísérletek esetén a rendszer automatikusan alkalmazza a featurizálást, de az adatok alapján is testre szabható. [További információ arról, hogy mit tartalmaz a deaturizálás.](how-to-configure-auto-features.md#featurization)  

> [!NOTE]
> Az automatizált gépi tanulás jellemzősítési lépései (a funkciók normalizálása, a hiányzó adatok kezelése, a szöveg numerikusra konvertálása stb.) az alapul szolgáló modell részei lesznek. Ha a modellt előrejelzésekhez használja, a rendszer automatikusan alkalmazza a betanítás során alkalmazott deaturizálási lépéseket a bemeneti adatokra.

### <a name="automatic-featurization-standard"></a>Automatikus deaturizálás (standard)

Az algoritmusok végrehajtásához minden automatizált gépi tanulási kísérlet automatikusan skálázódik vagy normalizálja az adatokat. A modell betanítása során az alábbi skálázási vagy normalizálási technikák egyikét alkalmazza a rendszer az egyes modellekre. Megtudhatja, hogyan segít az AutoML [megelőzni a túlilledt](concept-manage-ml-pitfalls.md) és kiegyensúlyozatlan adatokat a modellekben.

|Feldolgozás &nbsp; & &nbsp; skálázása| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | A jellemzők szabványosítása az átlagos érték eltávolításával és az egység varianciájára való méretezésével  |
| [MinMaxSkalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Átalakítja a funkciókat azáltal, hogy az egyes funkciókat az oszlop minimuma és maximuma alapján skálázja  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skálázd az egyes funkciókat a maximális abszolút értékük alapján |
| [RobustSkalar (Robusztus)](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ez a skálázó a kvantilis tartományuk alapján rendelkezik funkciókkal |
| [Pem](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineáris dimenziócsökkentés az adatok egyes számszerű értékének felbontásával, hogy egy alacsonyabb dimenziós térbe kivetítve |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ez a transzformátor csonkolt egyes számérték-felbontással (SVD) végzi a lineáris dimenziócsökkentést. A PCA-val ellentétben ez a becsérték nem központosul az adatok alapján az egyes értékek felbontásának számítása előtt, ami azt jelenti, hogy hatékonyan tud dolgozni a scipy.sparse mátrixokkal |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | A rendszer minden olyan mintát (vagyis az adatmátrix minden sorát), amely legalább egy nem nulla összetevőt tartalmaz, a többi mintától függetlenül újraméretez, hogy a normája (l1 vagy l2) egy legyen |

### <a name="customize-featurization"></a>A featurizálás testreszabása

További jellemzőmérnöki technikák, például kódolás és átalakítások is elérhetők. 

Engedélyezze ezt a beállítást a következővel:

+ Azure Machine Learning stúdió: Engedélyezze **az automatikus featurizálást** a **További** konfiguráció megtekintése szakaszban [az alábbi lépésekkel.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

+ Python SDK: Adja `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` meg a értéket az [AutoMLConfig objektumban.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) További információ a [deaturizálás engedélyezéséről.](how-to-configure-auto-features.md) 

## <a name="ensemble-models"></a><a name="ensemble"></a> Együttes modellek

Az automatizált gépi tanulás támogatja az együttes modelleket, amelyek alapértelmezés szerint engedélyezve vannak. Az együttes tanulás több modell kombinálásával javítja a gépi tanulási eredményeket és a prediktív teljesítményt, szemben az egyetlen modell használatával. Az együttes iterációk a futtatás utolsó iterációiként jelennek meg. Az automatizált gépi tanulás szavazási és halmozott együttes módszereket is használ a modellek kombinálása érdekében:

* **Szavazás:** az előrejelzött osztály valószínűségek súlyozott átlaga (besorolási feladatokhoz) vagy előre jelzett regressziós célok (regressziós feladatok esetén) alapján számítja ki az előrejelzést.
* **Egymásra halmozás:** A halmozás heterogén modelleket kombinál, és az egyes modellek kimenete alapján beképz egy metamodellt. A jelenlegi alapértelmezett metamodellek a besorolási feladatokhoz a LogisticRegression, a regressziós/előrejelzési feladatokhoz pedig az ElasticNet.

A [Caruana együttes kijelölési algoritmusa](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) az együttes rendezése inicializálással annak eldöntése érdekében, hogy mely modelleket kell használni az együttesen belül. Ez az algoritmus magas szinten inicializálja az együttest legfeljebb öt olyan modellel, amelyek a legjobb egyéni pontszámmal vannak ellátva, és ellenőrzi, hogy ezek a modellek a legjobb pontszám 5%-os küszöbértékén belül vannak-e a gyenge kezdeti együttesség elkerülése érdekében. Ezután minden együttes-iterációhoz hozzáad egy új modellt a meglévő együtteshez, és kiszámítja az eredményül kapott pontszámot. Ha egy új modell javítja a meglévő együttes pontszámát, az együttes úgy frissül, hogy tartalmazza az új modellt.

Tekintse meg [az alapértelmezett](how-to-configure-auto-train.md#ensemble) együttes beállítások az automatizált gépi tanulásban való módosításának mikéntját.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Útmutató a helyi és a távoli felügyelt gépi tanulási számítási célokhoz

Az automatizált gépi tanulási webes felület mindig távoli számítási [célt használ.](concept-compute-target.md)  A Python SDK használatakor azonban helyi számítási vagy távoli számítási célt kell választania az automatizált gépi tanulási betanításhoz.

* **Helyi számítás:** A betanítás a helyi laptopon vagy a virtuális gép számításán történik. 
* **Távoli számítás:** A betanítás Machine Learning számítási fürtökön történik.  

### <a name="choose-compute-target"></a>Számítási cél kiválasztása
A számítási cél kiválasztásakor vegye figyelembe az alábbi tényezőket:

 * **Helyi** számítás kiválasztása: Ha a forgatókönyv kis adatokat és rövid betanításokat (például másodperceket vagy gyermekfutatonként néhány percet) használó kezdeti feltárásokat vagy bemutatókat tartalmaz, akkor a helyi számítógépen való betanítás jobb választás lehet.  Nincs beállítási idő, az infrastruktúra-erőforrások (a számítógép vagy a virtuális gép) közvetlenül elérhetők.
 * Távoli gépi tanulási számítási fürt **kiválasztása:** Ha nagyobb adathalmazokkal, például az éles betanítás során, hosszabb betanításokat és modelleket szeretne létrehozni, a távoli számítás sokkal jobb végpontok közötti időteljesítményt biztosít, mert párhuzamosítani fogja a betanításokat a fürt csomópontjai `AutoML` között. Távoli számítás esetén a belső infrastruktúra indítási ideje gyermekfuttatásonként körülbelül 1,5 percet, valamint további perceket ad a fürt infrastruktúrájának, ha a virtuális gépek még nem futnak.

### <a name="pros-and-cons"></a>Előnyök és hátrányok
Vegye figyelembe ezeket az előnyöket és hátrányokat, amikor a helyi és a távoli kapcsolat használatát választja.

|  | Előnyök (előnyök)  |Hátrányok (gyengék)  |
|---------|---------|---------|---------|
|**Helyi számítási cél** |  <li> Nincs környezet indítási ideje   | <li>  A funkciók részkészlete<li>  A futtatásokat nem lehet párhuzamosítani <li> A nagy méretű adatoknál ez rosszabb. <li>Nincs adatstreamelés a betanítás során <li>  Nincs DNN-alapú deaturizálás <li> Csak Python SDK |
|**Távoli gépi tanulási számítási fürtök**|  <li> A funkciók teljes készlete <li> Gyermekfutatok párhuzamosizálása <li>   Nagy adattámogatás<li>  DNN-alapú deaturizálás <li>  A számítási fürt dinamikus méretezhetősége igény szerint <li> Kód nélkül (webes felhasználói felület) is elérhető  |  <li> Fürtcsomópontok indítási ideje <li> Az egyes gyermekfuttassak indítási ideje    |

### <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása 

 A távoli számítás használatakor további funkciók érhetők el, az alábbi táblázatban látható módon. 

| Szolgáltatás                                                    | Távoli | Helyi | 
|------------------------------------------------------------|--------|-------|
| Adatstreamelés (nagy adattámogatás, legfeljebb 100 GB)          | ✓      |       | 
| DNN-BIL-based text featurization and training             | ✓      |       |
| Gpu-támogatás (betanítás és dedukencia)        | ✓      |       |
| Képbesorolás és -címkézés támogatása                  | ✓      |       |
| Auto-ARIMA- és ForecastTCN-modellek előrejelzéshez | ✓      |       | 
| Több futtatás/iteráció párhuzamosan                       | ✓      |       |
| Modellek létrehozása értelmezhetőséggel az AutoML Studio webes kezelőfelületén      | ✓      |       |
| A funkciók mérnöki testreszabása a Studio webes felhasználói felületén| ✓      |       |
| Azure ML hiperparaméterek finomhangolása                             | ✓      |       |
| Az Azure ML-folyamat munkafolyamatának támogatása                         | ✓      |       |
| Futtatás folytatása                                             | ✓      |       |
| Előrejelzések                                                | ✓      | ✓     |
| Kísérletek létrehozása és futtatása jegyzetfüzetben                    | ✓      | ✓     |
| Kísérletinformációk és -metrikák regisztrálása és vizualizációja a felhasználói felületen | ✓      | ✓     |
| Adatkorlátok                                            | ✓      | ✓     |

## <a name="many-models"></a>Számos modell 

A Több modell [megoldásgyorsító](https://aka.ms/many-models) (előzetes verzió) a Azure Machine Learning-re épül, és lehetővé teszi az automatizált gépi tanulás használatát gépi tanulási modellek több száz vagy akár ezer gépi tanulási modell betanítása, működtetása és kezelése érdekében.

Ha például az  alábbi forgatókönyvekben minden példányhoz vagy személyhez külön modellt hozunk, az jobb eredményekhez vezethet:

* Értékesítések előrejelzése az egyes áruházakhoz
* Több száz olajmező prediktív karbantartása
* A felhasználói élmény egyéni felhasználók számára való testre szabása.

<a name="parity"></a>

### <a name="experiment-settings"></a>Kísérlet beállításai 

Az alábbi beállításokkal konfigurálhatja az automatizált gépi tanulási kísérletet. 

| |A Python SDK|A Studio webes felhasználói élménye|
----|:----:|:----:
|**Adatok felosztása betanítás/ellenőrzési készletekre**| ✓|✓
|**Támogatja a gépi tanulási feladatokat: besorolás, regresszió és előrejelzés**| ✓| ✓
|**Optimalizálás elsődleges metrika alapján**| ✓| ✓
|**Támogatja az Azure ML-számítást számítási célként** | ✓|✓
|**Előrejelzési horizont, cél késések és & konfigurálása**|✓|✓
|**Kilépési feltételek beállítása** |✓|✓ 
|**Egyidejű iterációk beállítása**| ✓|✓
|**Oszlopok eltávolítása**| ✓|✓
|**Blokk-algoritmusok**|✓|✓
|**Keresztellenőrzés** |✓|✓
|**Támogatja a betanítás Azure Databricks fürtökön**| ✓|
|**Tervezett szolgáltatásnevek megtekintése**|✓|
|**A featurizálás összegzése**| ✓|
|**Az ünnepekre vonatkozó featurizáció**|✓|
|**Naplófájl részletességi szintjei**| ✓|

### <a name="model-settings"></a>Modellbeállítások

Ezek a beállítások az automatizált gépi tanulási kísérlet eredményeként alkalmazhatók a legjobb modellre.

| |A Python SDK|A Studio webes felhasználói élménye|
|----|:----:|:----:|
|**A legjobb modellregisztráció, üzembe helyezés, magyarázat**| ✓|✓|
|**A szavazási együttes & együttes modellek engedélyezése**| ✓|✓|
|**A legjobb modell megjelenítése nem elsődleges metrika alapján**|✓||
|**AZ ONNX-modell kompatibilitásának engedélyezése/letiltása**|✓||
|**A modell tesztelése** | ✓| |

### <a name="run-control-settings"></a>Futtatásvezérlési beállítások

Ezekkel a beállításokkal áttekinthet és vezérelhet kísérleti futtatásokat és azok gyermekfutatát. 

| |A Python SDK|A Studio webes felhasználói élménye|
|----|:----:|:----:|
|**Futtatás összegző táblázata**| ✓|✓|
|**Futtatás megszakítása & gyermekfuttatásokkal**| ✓|✓|
|**Védőkorlátok lekérte**| ✓|✓|
|**Folytatási & szüneteltetése**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

A Azure Machine Learning gépi tanulási folyamatokkal Python-modelleket építhet ki, és átalakítathatja ŐKET ONNX formátumra. Ha a modellek ONNX formátumban vannak, különböző platformokon és eszközökön futtathatók. További információ az [ML-modellek felgyorsításról az ONNX-szel.](concept-onnx.md)

Tekintse meg az ONNX formátumra konvertálást ebben [a Jupyter notebook-példában.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Ismerje [meg, mely algoritmusokat támogatja az ONNX.](how-to-configure-auto-train.md#select-your-experiment-type)

Az ONNX-runtime a C# nyelvet is támogatja, így a C#-alkalmazásokban automatikusan létrehozott modellt újrakódolás vagy a REST-végpontok által befutó hálózati késések nélkül használhatja. További információ az [AutoML ONNX-modellek .NET-alkalmazásokban való, az ML.NET](./how-to-use-automl-onnx-model-dotnet.md) és az ONNX-modellek deduktálásról az [ONNX runtime C# API-val.](https://github.com/plaidml/onnxruntime/blob/plaidml/docs/CSharp_API.md) 

## <a name="next-steps"></a>Következő lépések

Az AutoML-lel több erőforrás is rendelkezésre áll. 

### <a name="tutorials-how-tos"></a>Oktatóanyagok/útmutatók
Az oktatóanyagok az AutoML-forgatókönyvek bevezető jellegű példái.
+ **A kód első lépéseként kövesse** az Oktatóanyag: Regressziós modell automatikus betanítása [az Azure Machine Learning Python SDK-val.](tutorial-auto-train-models.md)

 + **Alacsony vagy kódolást** nem tartalmú élmény esetén [lásd: Oktatóanyag:](tutorial-first-experiment-automated-ml.md)Automatizált gépi tanulási besorolási modellek létrehozása Azure Machine Learning stúdió.

A cikkekben további részleteket talál arról, hogy az AutoML milyen funkciókat kínál. Példa: 

+ Az automatikus betanítás kísérleteinek beállításainak konfigurálása
    + A Azure Machine Learning stúdió kövesse [az alábbi lépéseket.](how-to-use-automated-ml-for-ml-models.md) 
    + A Python SDK-val kövesse [az alábbi lépéseket.](how-to-configure-auto-train.md)

+  Az alábbi lépésekkel megtudhatja, hogyan lehet automatikus betanításokat használni [idősorozat-adatok használatával.](how-to-auto-train-forecast.md)

### <a name="jupyter-notebook-samples"></a>Jupyter-notebookminták 

Tekintse át az automatizált gépi tanulási mintákat ismertető GitHub-jegyzetfüzet adattárában található részletes [kódmintákat és a használatot ismertető eseteket.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

### <a name="python-sdk-reference"></a>Python SDK-referencia

Az AutoML-osztály referenciadokumentációjában mélyíthatja el az SDK tervezési mintákkal és osztálysokkal [kapcsolatos szakértelmét.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 

> [!Note]
> Az automatizált gépi tanulási képességek más Microsoft-megoldásokban is elérhetők, például [ML.NET,](/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)Power BI [és](/power-bi/service-machine-learning-automated) [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
