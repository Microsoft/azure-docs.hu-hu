---
title: A Linux megismerése
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan hajthat végre több gyakori adatelemzési feladatot a Linux Data Science Virtual Machine használatával.
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 09/17/2020
ms.openlocfilehash: 42136d0d58dbc318aab0e111fcef46f80751ca88
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517671"
---
# <a name="data-science-with-an-ubuntu-data-science-virtual-machine-in-azure"></a>Adatelemzés Ubuntu-Data Science Virtual Machine az Azure-ban

Ez az útmutató bemutatja, hogyan végezheti el számos gyakori adatelemzési feladatot az Ubuntu Data Science Virtual Machine (DSVM) használatával. Az Ubuntu DSVM az Azure-ban elérhető virtuálisgép-rendszerkép, amely az adatok elemzéséhez és a gépi tanuláshoz gyakran használt eszközök gyűjteményével van előtelepítve. A legfontosabb szoftver-összetevők az [Ubuntu Data Science Virtual Machine](./dsvm-ubuntu-intro.md)kiépítésében vannak részletezve. A DSVM-rendszerkép megkönnyíti az adatelemzés percek alatt történő megkezdését anélkül, hogy mindegyik eszközt külön kell telepítenie és konfigurálnia. Ha szükséges, egyszerűen méretezheti a DSVM, és leállíthatja, ha nincs használatban. A DSVM-erőforrás rugalmas és költséghatékony.

Ebben az útmutatóban elemezzük a [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészletet. A Spambase a levélszemét vagy a sonka (nem levélszemét) jelölésű e-mailek halmaza. A Spambase az e-mailek tartalmával kapcsolatos statisztikát is tartalmaz. A statisztikáról az útmutató későbbi részében olvashat.

## <a name="prerequisites"></a>Előfeltételek

A Linux-DSVM használatához a következő előfeltételek szükségesek:

* **Azure-előfizetés**. Azure-előfizetés beszerzéséhez tekintse [meg még ma az ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/)című témakört.

* [**Ubuntu Data Science Virtual Machine**](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). További információ a virtuális gép kiépítéséről: [az Ubuntu Data Science Virtual Machine kiépítése](./release-notes.md).
* A [**X2Go**](https://wiki.x2go.org/doku.php) egy nyílt Xfce-munkamenettel telepítette a számítógépre. További információ: [a X2Go-ügyfél telepítése és konfigurálása](dsvm-ubuntu-intro.md#x2go).
* Ha gördülékenyebb görgetést kíván, a DSVM Firefox böngészőben kapcsolja be a `gfx.xrender.enabled` jelölőt `about:config` . [További információ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). A beállítást is érdemes megfontolni `mousewheel.enable_pixel_scrolling` `False` . [További információ](https://support.mozilla.org/questions/981140).

## <a name="download-the-spambase-dataset"></a>A spambase adatkészlet letöltése

A [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészlet egy viszonylag kis mennyiségű adat, amely 4 601-példákat tartalmaz. Az adatkészlet kényelmes méretet jelent a DSVM egyes főbb funkcióinak bemutatására, mivel az erőforrásokra vonatkozó követelmények szerények maradnak.

> [!NOTE]
> Ez az útmutató egy D2 v2 méretű Linux DSVM (Ubuntu 18,04 Edition) készült. Az ebben a bemutatóban bemutatott eljárások elvégzéséhez használhatja ezt a méretet DSVM.

Ha több tárolóhelyre van szüksége, további lemezeket hozhat létre, és csatolhatja azokat a DSVM. A lemezek állandó Azure Storage-t használnak, így az adatmegőrzés akkor is megmarad, ha a kiszolgálót átméretezés vagy leállítás miatt újra kiépítik. Lemez hozzáadásához és a DSVM való csatlakoztatásához hajtsa végre a [lemez hozzáadása Linux rendszerű virtuális géphez](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakör lépéseit. A lemezek hozzáadásának lépései az Azure CLI-t használják, amely már telepítve van a DSVM. A lépéseket teljes mértékben magáról a DSVM hajthatja végre. A tárterület növelésének másik lehetősége a [Azure Files](../../storage/files/storage-how-to-use-files-linux.md)használata.

Az adatletöltéshez nyisson meg egy terminál ablakot, majd futtassa a következő parancsot:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

A letöltött fájlnak nincs fejlécsora. Hozzon létre egy másik fájlt, amelynek van fejléce. Futtassa ezt a parancsot a megfelelő fejlécekkel rendelkező fájl létrehozásához:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Ezután fűzze össze a két fájlt:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Az adatkészlet többféle statisztikai adattípussal rendelkezik az egyes e-mailekhez:

* Az olyan oszlopok, mint a **Word \_ freq \_ _Word_** , az e-mailben szereplő *szavak százalékos* arányát jelölik. Ha például a **Word \_ freq \_ make** értéke **1**, akkor az e-mailben szereplő összes szó 1%- *a.*
* Oszlopok, például *a char* **\_ freq \_ _char_** az e-mailben szereplő összes karakter százalékos arányát jelzi.
* a **tőke \_ futtatásának \_ \_ leghosszabb hossza** a nagybetűk sorozatának leghosszabb hosszúsága.
* a **tőke \_ futtatásának \_ \_ átlagos** hossza a nagybetűk összes sorozatának átlagos hossza.
* a **tőke \_ futtatásának \_ hossza \_ összesen** érték a nagybetűk összes sorozatának teljes hosszát adja meg.
* a **Levélszemét** azt jelzi, hogy az e-mail levélszemétnek minősül-e (1 = levélszemét, 0 = nem levélszemét).

## <a name="explore-the-dataset-by-using-r-open"></a>Az adatkészlet megismerése az R Open használatával

Vizsgáljuk meg az adatok áttekintését, és végezzünk el néhány alapszintű gépi tanulást az R használatával. A DSVM a [Microsoft R Open](https://mran.revolutionanalytics.com/open/) előre telepítve van. Az előtelepített R-verzióban a többszálas matematikai kódtárak jobb teljesítményt nyújtanak, mint az egyszálas verziók. Az R Open a megismételhetőséget is biztosítja a CRAN Package adattár pillanatképének használatával.

Az útmutatóban használt kódrészletek példányainak beszerzéséhez használja a git-t az Azure-Machine-learning-adatelemzési tárház klónozásához. A git előre telepítve van a DSVM. A git parancssorában futtassa a következőt:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Nyisson meg egy terminál ablakot, és indítson el egy új R-munkamenetet az R interaktív konzolon. Használhatja a RStudio is, amely előre telepítve van a DSVM.

Az adatimportálás és a környezet beállítása:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Az egyes oszlopokra vonatkozó összegző statisztika megtekintése:

```R
summary(data)
```

Az egyes adatnézetek esetében:

```R
str(data)
```

Ez a nézet az egyes változók típusát és az adatkészlet első néhány értékét jeleníti meg.

A **Levélszemét** oszlop egész számként lett beolvasva, de valójában egy kategorikus változó (vagy faktor). A típusának beállítása:

```R
data$spam <- as.factor(data$spam)
```

A feltárt elemzések elvégzéséhez használja a [ggplot2](https://ggplot2.tidyverse.org/) csomagot, amely egy népszerű, az R-hez készült, a DSVM előre telepített gráf-könyvtár. A korábban megjelenő összegző adatok alapján a felkiáltójelek gyakoriságára vonatkozó összefoglaló statisztikák szerepelnek. A következő parancsok futtatásával ábrázoljuk ezeket a gyakoriságokat:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Mivel a nulla sáv elferdíti a mintaterületet, töröljük a következőket:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Az 1. számú, érdekesnek tűnik. Nézzük meg, hogy csak az alábbi adatértékek szerepelnek:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Ezután bontsa ki a spam és a Ham:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Ezeknek a példáknak a segítségével hasonló mintaterületeket készíthet, és más oszlopokban is megismerheti az adatelemzést.

## <a name="train-and-test-a-machine-learning-model"></a>Gépi tanulási modell betanítása és tesztelése

Több gépi tanulási modellt is betanítunk, hogy az adatkészletben lévő e-maileket a levélszemét vagy a sonka segítségével osztályozzák. Ebben a szakaszban egy döntési famodellt és egy véletlenszerű erdő modellt fogunk betanítani. Ezt követően teszteljük az előrejelzések pontosságát.

> [!NOTE]
> A következő kódban használt *rpart* -csomag (rekurzív particionálás és regressziós fák) már telepítve van a DSVM.

Először ossza fel az adatkészletet a képzési készletekbe és a tesztelési készletekbe:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Ezután hozzon létre egy döntési fát az e-mailek besorolásához:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Az eredmény a következő:

![A létrehozott döntési fa diagramja](./media/linux-dsvm-walkthrough/decision-tree.png)

A következő kóddal állapíthatja meg, hogy milyen jól teljesíti a betanítási készletet:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Annak meghatározása, hogy milyen jól hajtja végre a tesztelési készleten:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Próbáljon ki egy véletlenszerű erdő modellt is. A véletlenszerű erdők számos döntési fát vezetnek be, és olyan osztályt mutatnak be, amely az összes egyéni döntési fában lévő besorolások módját képezi. Nagyobb teljesítményű gépi tanulási megközelítést biztosítanak, mivel a döntési fa modelljének overfit egy képzési adathalmazt.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Mélyreható tanulási útmutatók és útmutatók

A keretrendszeren alapuló minták mellett átfogó útmutatók is elérhetők. Ezek a forgatókönyvek megkönnyítik a mélyreható tanulási alkalmazások fejlesztését olyan tartományokban, mint például a képek és a szövegek és a nyelvek megismerése.

- [Neurális hálózatok futtatása különböző keretrendszerek között](https://github.com/ilkarman/DeepLearningFrameworks): átfogó útmutató, amely bemutatja, hogyan telepíthet át programkódot egyik keretrendszerről a másikra. Azt is bemutatja, hogyan lehet összehasonlítani a modelleket és a futásidejű teljesítményt a keretrendszerek között. 

- [Útmutató a képeken belüli termékek észlelésére szolgáló teljes körű megoldás](https://github.com/Azure/cortana-intelligence-product-detection-from-images)létrehozásához: a képészlelés egy olyan technika, amely képes objektumokat megkeresni és osztályozni a képeken belül. A technológiának lehetősége van arra, hogy hatalmas előnyökkel jutalmazza a sok valós üzleti tartományát. A kiskereskedők például használhatják ezt a technikát annak meghatározására, hogy az ügyfél melyik terméket választotta fel a polcról. Ezek az információk segítenek a termékek leltározásának kezelésében. 

- [Mély tanulás a hanghoz](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): ez az oktatóanyag azt mutatja be, hogyan lehet betanítani a mély tanulási modellt a Hangesemények észlelésére a [városi hangokat tartalmazó adatkészletben](https://urbansounddataset.weebly.com/). Az oktatóanyag áttekintést nyújt a hangadatokkal való munkavégzésről.

- [Szöveges dokumentumok besorolása](https://github.com/anargyri/lstm_han): Ez a bemutató azt mutatja be, hogyan lehet két különböző neurális hálózati architektúrát felépíteni és betanítani: a hierarchikus hálózati és hosszú távú memória (LSTM). Ezek a neurális hálózatok a kerasz API-t használják a mélyreható tanuláshoz a szöveges dokumentumok besorolásához. A kerasz a legelterjedtebb mély tanulási keretrendszerek: Microsoft Cognitive Toolkit, TensorFlow és theano.

## <a name="other-tools"></a>Egyéb eszközök

A többi szakaszban bemutatjuk, hogyan használhatja a Linux DSVM telepített eszközöket. A következő eszközöket tárgyaljuk:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL és mókus SQL
* Azure Synapse Analytics (korábban SQL DW)

### <a name="xgboost"></a>XGBoost

A [XGBoost](https://xgboost.readthedocs.org/en/latest/) gyorsan és precízen növelt faszerkezetes megvalósítást biztosít.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

A XGBoost Pythonból vagy parancssorból is hívható.

### <a name="python"></a>Python

Python-fejlesztés esetén a 3,5-es és a 2,7-es anaconda Python-disztribúció a DSVM van telepítve.

> [!NOTE]
> Az anaconda eloszlása magában foglalja a [Conda](https://conda.pydata.org/docs/index.html). A Conda használatával olyan egyéni Python-környezeteket hozhat létre, amelyek különböző verziókkal vagy csomagokkal vannak telepítve.

Ismerkedjen meg néhány spambase adatkészlettel, és osztályozza az e-maileket a Scikit támogató vektoros gépekkel:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Előrejelzések készítése:

```Python
clf.predict(X.ix[0:20, :])
```

Egy Azure Machine Learning-végpont közzétételének bemutatásához hozzon egy alapszintű modellt. A korábban az R-modell közzétételekor használt három változót fogjuk használni:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

### <a name="jupyterhub"></a>JupyterHub

A DSVM anaconda-eloszlása egy Jupyter Notebook, egy többplatformos környezettel rendelkezik a Python, az R vagy a Julia-kód és-elemzés megosztásához. A Jupyter Notebook a JupyterHub keresztül érhető el. Jelentkezzen be a helyi Linux-felhasználónevével és-jelszavával a következő címen \<DSVM DNS name or IP address\> : https://: 8000/. A JupyterHub összes konfigurációs fájlja megtalálható a/etc/jupyterhub.

> [!NOTE]
> Ha a Python Package Managert (a paranccsal) szeretné használni a `pip` jelenlegi kernel egyik Jupyter Notebookjában, használja ezt a parancsot a Code (kód) cellában:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Ha a Conda-telepítőt (a `conda` parancs használatával) szeretné használni az aktuális kernel egy Jupyter notebook, akkor ezt a parancsot kell használnia a kód cellájában:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Több minta jegyzetfüzet már telepítve van a DSVM:

* Python-jegyzetfüzetek – példa:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
* Példa R-jegyzetfüzetre:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> A Júlia nyelve a Linux DSVM parancssorában is elérhető.

### <a name="rattle"></a>Rattle

A [csörgő](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *a* analitikus *t* OOL *t* o *L* keres *E* asily) egy grafikus R-eszköz az adatbányászathoz. A Rattle olyan intuitív kezelőfelülettel rendelkezik, amely megkönnyíti az adatterhelést, az elemzést és az átalakítást, valamint a modellek kiépítését és értékelését. [Csörgő: az R adatbányászati grafikus felhasználói felülete](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) egy olyan bemutatót biztosít, amely bemutatja a csörgő funkcióit.

Telepítse és indítsa el a csörgőt a következő parancsok futtatásával:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Nem kell telepítenie a csörgőt a DSVM. Előfordulhat azonban, hogy a rendszer megkéri a további csomagok telepítését a csörgő megnyitásakor.

A csörgő egy tabulátor-alapú felületet használ. A lapok többsége megfelel a [csoportos adatelemzési folyamat](../team-data-science-process/index.yml)lépéseinek, például az adat betöltésének vagy az adatfelderítésnek. Az adatelemzési folyamat balról jobbra halad a lapokon. Az utolsó lapon a csörgő által futtatott R-parancsok naplója szerepel.

Az adatkészlet betöltése és konfigurálása:

1. A fájl betöltéséhez válassza az **adatlapot.**
1. Válassza ki a **fájlnév** melletti választót, majd válassza az **spambaseHeaders.**-adatelemet.
1. A fájl betöltéséhez. Válassza a **végrehajtás** lehetőséget. Az egyes oszlopok összegzését kell látnia, beleértve az azonosított adattípust is. legyen szó bemenetről, célhoz vagy más típusú változóról; és az egyedi értékek száma.
1. A csörgő helyesen azonosította a **Levélszemét** oszlopot célként. Válassza ki a **Levélszemét** oszlopot, majd állítsa a **Target adattípus** értéket **Categoric** értékre.

Az adatelemzés:

1. Kattintson a **Tallózás** fülre.
1. Ha szeretné megtekinteni a változó típusokkal és néhány összefoglaló statisztikával kapcsolatos információkat, válassza az **Összefoglalás**  >  **végrehajtás** lehetőséget.
1. Ha meg szeretné tekinteni az egyes változókkal kapcsolatos egyéb statisztikai adatokat, válassza a további lehetőségek, például a **Leírás** vagy az **alapvető** beállítások lehetőséget.

A **Tallózás** lapon éleslátó ábrákat is létrehozhat. Az adathisztogram ábrázolása:

1. Válassza a **disztribúciók** lehetőséget.
1. **Word_freq_remove** és **word_freq_you** esetében válassza a **hisztogram** lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget. Mindkét sűrűséget egyetlen Graph-ablakban kell látnia, ahol egyértelmű, hogy _a szó sokkal gyakrabban jelenik meg az_ e-mailekben, mint az _Eltávolítás_.

A **korrelációs** mintaterületek is érdekesek. Egy mintaterület létrehozásához:

1. A **Típus mezőben** válassza a **korreláció** lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget.
1. A csörgő figyelmezteti, hogy legfeljebb 40 változót javasol. A mintaterület megtekintéséhez válassza az **Igen** lehetőséget.

Vannak érdekes összefüggések, amelyek a következők: a _technológia_ szorosan összefügg a _HP_ -vel és a _laborokkal_, például. Emellett a _650_ -as rendszer is szorosan összefügg, mivel az adatkészletek adományozóinak körzetszáma 650.

A szavak közötti korrelációk numerikus értékei a **Tallózás** ablakban érhetők el. Fontos megjegyezni, hogy például a _technológia_ negatívan összefügg a _saját és a_ _pénzével_.

A csörgő átalakíthatja az adatkészletet, hogy kezelni tudja a gyakori problémákat. Például átméretezheti a szolgáltatásokat, kiszámíthatja a hiányzó értékeket, kezelheti a kiugró elemeket, és eltávolíthatja a hiányzó adatokat tartalmazó változókat vagy megjegyzéseket. A csörgő a megfigyelések és változók közötti társítási szabályokat is képes azonosítani. Ezek a lapok nem szerepelnek ebben a bevezető bemutatóban.

A csörgő a fürt elemzését is képes futtatni. Kizárunk néhány funkciót, hogy a kimenet könnyebben olvasható legyen. Az **adatok** lapon válassza a **figyelmen kívül hagyása** az egyes változók mellett jelölőnégyzetet az alábbi 10 elem kivételével:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Térjen vissza a **fürt** lapra. Válassza a **KMeans** lehetőséget, majd állítsa a **fürtök számát** **4**-re. Válassza a **Végrehajtás** lehetőséget. Az eredmények a kimenet ablakban jelennek meg. Az egyik fürt a _George_ és a _HP_ nagy gyakorisággal rendelkezik, és valószínűleg egy legitim üzleti e-mail-cím.

Alapszintű döntés faszerkezetű gépi tanulási modell létrehozása:

1. Válassza a **modell** fület,
1. A **típushoz** válassza a **fa** elemet.
1. Az **Execute (végrehajtás** ) lehetőség kiválasztásával megjelenítheti a faszerkezetet szöveges formában a kimeneti ablakban.
1. A **Rajzolás** gomb megnyomásával megtekintheti a grafikus verziót. A döntési fa hasonlít a korábban a rpart használatával beszerzett fához.

A Rattle hasznos funkciója, hogy több gépi tanulási módszert is futtathat, és gyorsan kiértékelheti azokat. A lépések a következők:

1. A **Típus mezőben** válassza az **összes** lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget.
1. Ha a csörgő befejezi a futást, kiválaszthat bármilyen **típusú** értéket, például a **SVM**, és megtekintheti az eredményeket.
1. Az ellenőrzési csoport modelljeinek teljesítményét az **értékelés** lapon is összehasonlíthatja. A **hiba-mátrix** kiválasztása például megjeleníti a zűrzavar mátrixot, a teljes hibát és az átlagos osztály-hibát az ellenőrzési készlet egyes modelljeinél. Emellett a ROC-görbék, az érzékenységi elemzések és más típusú modellek kiértékelései is megtekinthetők.

Amikor elkészült a modellek létrehozásával, a **napló** lapon megtekintheti a csörgő által a munkamenet során futtatott R-kódot. Válassza az **Exportálás** gombot a mentéshez.

> [!NOTE]
> A csörgő jelenlegi kiadása hibát tartalmaz. Ha módosítani szeretné a parancsfájlt, vagy a használatával szeretné később megismételni a lépéseket, be kell szúrnia egy karaktert a napló **#** *exportálása* előtt... a napló szövegében.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL és mókus SQL

A DSVM a PostgreSQL telepítve van. A PostgreSQL egy kifinomult, nyílt forráskódú, összehasonlítható adatbázis. Ebben a szakaszban bemutatjuk, hogyan tölthető be a spambase adatkészlet a PostgreSQL-be, majd hogyan kérdezhető le.

Az adatok betöltése előtt engedélyeznie kell a jelszó-hitelesítést a localhost-ból. Egy parancssorból futtassa az alábbi parancsot:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

A konfigurációs fájl aljának közelében több sor is található, amelyek részletezik az engedélyezett kapcsolatokat:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Módosítsa az **IPv4 helyi kapcsolatok** vonalat úgy, hogy az **MD5** -et használja az **ident** helyett, így bejelentkezhet a Felhasználónév és a jelszó használatával:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Ezután indítsa újra a PostgreSQL szolgáltatást:

```Bash
sudo systemctl restart postgresql
```

A *psql* (a PostgreSQL interaktív terminálja) elindításához a beépített postgres-felhasználóként futtassa ezt a parancsot:

```Bash
sudo -u postgres psql
```

Hozzon létre egy új felhasználói fiókot a bejelentkezéshez használt Linux-fiók felhasználónevével. Jelszó létrehozása:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Jelentkezzen be a psql:

```Bash
psql
```

Importálja az új adatbázisba:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Most Ismerkedjen meg az adataival, és futtasson néhány lekérdezést a mókus SQL használatával, amely egy grafikus eszköz, amellyel a JDBC-illesztőn keresztül használhatja az adatbázisokat.

Első lépésként nyissa meg a mókus SQL **alkalmazást az alkalmazások** menüben. Az illesztőprogram beállítása:

1. Válassza a **Windows**-  >  **illesztőprogramok megtekintése** lehetőséget.
1. Kattintson a jobb gombbal a **PostgreSQL** elemre, és válassza az **illesztőprogram módosítása** lehetőséget.
1. Válassza a **további osztály elérési útja**  >  **Hozzáadás** lehetőséget.
1. A **fájlnév** mezőben adja meg a **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar** nevet.
1. Válassza a **Megnyitás** lehetőséget.
1. Válassza az **illesztőprogramok listázása** lehetőséget. Az **Osztálynév** mezőben válassza a **org. PostgreSQL. Driver** elemet, majd kattintson **az OK gombra**.

A helyi kiszolgálóhoz való kapcsolódás beállítása:

1. Válassza a **Windows**  >  **View aliasok lehetőséget.**
1. Az **+** új alias létrehozásához kattintson a gombra. Az új alias neve mezőbe írja be a **Levélszemét-adatbázist**. 
1. Az **illesztőprogram** esetében válassza a **PostgreSQL** lehetőséget.
1. Állítsa be az URL-címet a **JDBC: PostgreSQL://localhost/spam** értékre.
1. Adja meg a felhasználónevét és a jelszavát.
1. Válassza az **OK** lehetőséget.
1. A **kapcsolódási** ablak megnyitásához kattintson duplán a **Levélszemét-adatbázis** aliasára.
1. Válassza a **Kapcsolódás** lehetőséget.

Lekérdezések futtatása:

1. Válassza az **SQL** lapot.
1. Az **SQL** lap tetején található lekérdezés mezőben adjon meg egy alapszintű lekérdezést, például: `SELECT * from data;` .
1. A lekérdezés futtatásához nyomja le a CTRL + ENTER billentyűkombinációt. Alapértelmezés szerint a mókus SQL a lekérdezés első 100 sorát adja vissza.

Az adatelemzéshez számos további lekérdezés is futtatható. Hogyan változik például *a szó* gyakorisága a levélszemét és a sonka között?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Vagy milyen jellemzőkkel rendelkezik az e-mailek gyakran tartalmaznak *3D*-ket?

```SQL
SELECT * from data order by word_freq_3d desc;
```

A legtöbb olyan e-mail-cím, amelynek a *3D-s* előfordulása látszólag levélszemét. Ezek az információk hasznosak lehetnek az e-mailek besorolására szolgáló prediktív modell létrehozásához.

Ha a PostgreSQL-adatbázisban tárolt adataival szeretne gépi tanulást végezni, érdemes lehet [MADlib](https://madlib.incubator.apache.org/)használni.

### <a name="azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (korábban SQL DW)

Az Azure szinapszis Analytics egy felhőalapú, kibővíthető adatbázis, amely nagy mennyiségű, egymással rokon és nem rokon adatmennyiséget képes feldolgozni. További információ: [Mi az az Azure szinapszis Analytics?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Az adatraktárhoz való kapcsolódáshoz és a tábla létrehozásához futtassa a következő parancsot egy parancssorból:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

A Sqlcmd parancssorban futtassa a következő parancsot:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Másolja az adatfájlokat a BCP használatával:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> A letöltött fájl a Windows stílusú sorok végződéseit tartalmazza. A BCP eszköz a Unix stílusú sorok befejezését várja. A BCP megadásához használja az-r jelzőt.

Ezt követően a lekérdezés a Sqlcmd használatával:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

A mókus SQL használatával is lekérdezheti. Kövesse a PostgreSQL-hez hasonló lépéseket a SQL Server JDBC-illesztő használatával. A JDBC-illesztő a/usr/share/Java/jdbcdrivers/sqljdbc42.jar mappában található.