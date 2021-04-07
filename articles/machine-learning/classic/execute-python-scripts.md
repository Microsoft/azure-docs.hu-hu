---
title: 'ML Studio (klasszikus): Python-parancsfájlok végrehajtása – Azure'
description: Ismerje meg, hogyan használhatja a Python-szkriptek végrehajtása a Python-kódokat Machine Learning Studio (klasszikus) kísérletekben és webszolgáltatásokban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519983"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Python Machine learning-parancsfájlok végrehajtása Azure Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ A következőre vonatkozik:. ](../../../includes/media/aml-applies-to-skus/yes.png) A Machine Learning Studio (klasszikus) ![ nem vonatkozik a következőre:.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A Python egy értékes eszköz, amely számos adatszakértőkből álló eszközben található. Ez a szokásos gépi tanulási munkafolyamatok minden szakaszában használatos, beleértve az adatfeltárást, a szolgáltatások kinyerését, a modellek betanítását és az érvényesítést, valamint a telepítést.

Ez a cikk azt ismerteti, hogyan használható a Python-szkript végrehajtása a Python-kód használatára a Azure Machine Learning Studio (klasszikus) kísérletekben és a webszolgáltatásokban.

## <a name="using-the-execute-python-script-module"></a>A Python-parancsfájl végrehajtása modul használata

Az elsődleges felület a Pythonhoz a Studióban (klasszikus) a [Python-szkript végrehajtása][execute-python-script] modulon keresztül történik. Legfeljebb három bemenetet fogad el, és legfeljebb két kimenetet hoz létre, hasonlóan az [R-szkript végrehajtása][execute-r-script] modulhoz. A Python-kód bekerül a paraméter mezőbe egy speciális névvel ellátott belépési pont nevű függvénnyel `azureml_main` .

![Python parancsfájl-modul végrehajtása](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Python-kód minta a modul paraméter mezőjében](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Bemeneti paraméterek

A Python-modul bemenetei pandák DataFrames jelennek meg. A `azureml_main` függvény legfeljebb két opcionális pandák DataFrames-t fogad el paraméterként.

A bemeneti portok és a függvények paramétereinek megfeleltetése a pozíció:

- Az első csatlakoztatott bemeneti port a függvény első paraméterére van leképezve.
- A második bemenet (ha csatlakoztatva van) a függvény második paraméterére van leképezve.
- A harmadik bemenet [további Python-modulok importálására](#import-modules)szolgál.

Alább láthatók a részletes szemantika arról, hogy a bemeneti portok hogyan lesznek leképezve a `azureml_main` függvény paramétereinek.

![Bemeneti portok konfigurációjának és a létrejövő Python-aláírásnak a táblázata](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Kimeneti visszatérési értékek

A `azureml_main` függvénynek egy Python- [sorozatba](https://docs.python.org/2/c-api/sequence.html) (például egy rekord, lista vagy NumPy tömbbe) csomagolt, egyetlen pandák DataFrame kell visszaadnia. A rendszer az első elemet adja vissza a modul első kimeneti portjához. A modul második kimeneti portja a [vizualizációk](#visualizations) esetében használatos, és nem igényel visszatérési értéket. Ez a séma alább látható.

![Bemeneti portok hozzárendelése paraméterekhez és visszatérési érték a kimeneti porthoz](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Bemeneti és kimeneti adattípusok fordítása

A Studio-adatkészletek nem egyeznek a Panda DataFrames. Ennek eredményeképpen a Studio (klasszikus) bemeneti adatkészletei a pandák DataFrame konvertálódnak, a kimeneti DataFrames pedig a Studio (klasszikus) adatkészletekre lesznek konvertálva. Az átalakítási folyamat során a következő fordításokat is elvégzik:

 **Python-adattípus** | **Studio-fordítási eljárás** |
| --- | --- |
| Karakterláncok és numerikus számok| Lefordítva |
| Panda ' NA ' | Lefordítva "hiányzó érték" |
| Indexelő vektorok | Támogatott |
| Nem karakterláncos oszlopnevek | Oszlop nevének meghívása `str` |
| Ismétlődő oszlopnevek | Adja hozzá a numerikus utótagot: (1), (2), (3) és így tovább.

**A Python-64 függvény összes bemeneti adatkeretének értéke a 0 és az 1 közötti számú numerikus index.*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Meglévő Python parancsfájl-modulok importálása

A Python végrehajtásához használt háttér a [anaconda](https://www.anaconda.com/distribution/), egy széles körben használt tudományos Python-disztribúción alapul. Az adat-központú számítási feladatokban használt leggyakoribb Python-csomagok 200-es közelségbe kerül. A Studio (klasszikus) jelenleg nem támogatja az olyan csomagkezelő rendszerek használatát, mint a PIP vagy a Conda a külső könyvtárak telepítéséhez és kezeléséhez.  Ha a további kódtárak beépítésének szükségességét tapasztalja, használja a következő forgatókönyvet útmutatóként.

Gyakori használati eset a meglévő Python-szkriptek beépítése a Studio (klasszikus) kísérletekbe. A [Python-szkript végrehajtása][execute-python-script] modul egy, a harmadik bemeneti porton található Python-modulokat tartalmazó zip-fájlt fogad el. A fájlt a végrehajtási keretrendszer kibontja a futtatókörnyezetben, és a rendszer hozzáadja a tartalmat a Python-tolmács könyvtári elérési útjához. A `azureml_main` belépési pont függvény ezután közvetlenül importálhatja ezeket a modulokat. 

Tegyük fel például, hogy a fájl Hello.py egy egyszerű "Helló, világ" függvényt tartalmaz.

![Felhasználó által definiált függvény a Hello.py fájlban](./media/execute-python-scripts/figure4.png)

Ezután létrehozunk egy Hello.py tartalmazó Hello.zip fájlt:

![Felhasználó által definiált Python-kódot tartalmazó Zip-fájl](./media/execute-python-scripts/figure5.png)

Töltse fel a zip-fájlt adatkészletként a studióba (klasszikus). Ezután hozzon létre és futtasson egy olyan kísérletet, amely a Python-kódot használja a Hello.zip fájlban úgy, hogy a következő képen látható módon csatolja a **Python-parancsfájl végrehajtása** modul harmadik bemeneti portjához.

![Példa a kísérletre Hello.zip bemenetként egy Python parancsfájl-modul végrehajtásához](./media/execute-python-scripts/figure6a.png)

![Felhasználó által definiált Python-kód zip-fájlként feltöltve](./media/execute-python-scripts/figure6b.png)

A modul kimenete azt mutatja, hogy a zip-fájl kicsomagolása megtörtént, és a függvény futtatása megtörtént `print_hello` .

![Felhasználó által definiált függvényt megjelenítő modul kimenete](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Azure Storage-Blobok elérése

Az Azure Blob Storage-fiókban tárolt adatai a következő lépésekkel érhetők el:

1. Töltse le helyileg a [Pythonhoz készült Azure Blob Storage csomagot](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) .
1. Töltse fel a zip-fájlt a Studio (klasszikus) munkaterületre adatkészletként.
1. Hozza létre a BlobService objektumot a `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. A tárolási **konfiguráció** beállítása lapon **szükséges biztonságos átvitel** letiltása

![A Azure Portal szükséges biztonságos átvitel letiltása](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Végrehajtott Python-parancsfájlok

A pontozási kísérletekben használt összes [Python parancsfájl][execute-python-script] -modult webszolgáltatásként való közzétételkor kell meghívni. Az alábbi képen például egy pontozási kísérlet jelenik meg, amely egy adott Python-kifejezés kiértékelésére szolgáló kódot tartalmaz.

![Webszolgáltatások Studio-munkaterülete](./media/execute-python-scripts/figure3a.png)

![Python-pandák kifejezés](./media/execute-python-scripts/python-script-with-python-pandas.png)

A kísérletből létrehozott webszolgáltatás a következő műveleteket végzi el:

1. Python-kifejezés készítése bemenetként (karakterláncként)
1. Python-kifejezés küldése a Python-tolmácsnak
1. Egy olyan táblát ad vissza, amely a kifejezést és a kiértékelt eredményt is tartalmazza.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Vizualizációk használata

A MatplotLib használatával létrehozott ábrákat a [Python-szkript végrehajtásával][execute-python-script]adhatja vissza. A mintaterületek azonban nem lesznek automatikusan átirányítva az R-t használó képekhez. Így a felhasználónak explicit módon mentenie kell bármilyen mintaterületet a PNG-fájlokba.

Lemezképek MatplotLib való létrehozásához a következő lépéseket kell végrehajtania:

1. Váltson a háttérre az alapértelmezett QT-alapú megjelenítőtől a "AGG" értékre.
1. Hozzon létre egy új Figure objektumot.
1. A tengely beszerzése és az összes ábra készítése.
1. Mentse az ábrát PNG-fájlba.

Ez a folyamat az alábbi, a pandák scatter_matrix függvény használatával létrehozott pontdiagram-ábrákat mutatja be.

![MatplotLib-számok képekre mentésére szolgáló kód](./media/execute-python-scripts/figure-v1-8.png)

![Az ábrák megjelenítéséhez kattintson a megjelenítés egy Python parancsfájl-modulban elemre.](./media/execute-python-scripts/figure-v2-9a.png)

![Minták ábrázolása a Python-kód használatával](./media/execute-python-scripts/figure-v2-9b.png)

Több adatot is vissza lehet adni, ha más lemezképbe menti őket. A Studio (klasszikus) futtatókörnyezet minden rendszerképet felvesz, és összefűzi őket a vizualizációhoz.

## <a name="advanced-examples"></a>Speciális példák

A Studióban (klasszikus) telepített anaconda-környezet olyan gyakori csomagokat tartalmaz, mint például a NumPy, a SciPy és a Scikits-Learn. Ezeket a csomagokat hatékonyan lehet használni az adatfeldolgozáshoz a Machine learning-folyamatokban.

Például az alábbi kísérlet és szkript szemlélteti a Scikits-Learnban szereplő Ensemble-tanulók használatát, hogy számításba lehessen venni az adathalmazok fontossági pontszámait. A pontszámok a felügyelt funkciók kiválasztásához használhatók, mielőtt bekerülnek egy másik modellbe.

Itt látható a Python-függvény, amely a fontossági pontszámok kiszámítására szolgál, és a pontszámok alapján rendezi a szolgáltatásokat:

![Funkciók rangsorolása pontszámok szerint](./media/execute-python-scripts/figure8.png)

A következő kísérlet ezután kiszámítja és visszaadja az "Pima Indian diabétesz" adatkészletben szereplő funkciók fontossági pontjait Azure Machine Learning Studio (klasszikus):

![Kísérletezzen a Pima indiai cukorbetegség-adathalmazban található funkciókkal a Python használatával](./media/execute-python-scripts/figure9a.png)

![A Python parancsfájl-végrehajtási modul kimenetének vizualizációja](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Korlátozások

A [Python-szkript végrehajtása][execute-python-script] modul jelenleg a következő korlátozásokkal rendelkezik:

### <a name="sandboxed-execution"></a>Homokozóban történő végrehajtás

A Python-futtatókörnyezet jelenleg nem engedélyezett, és nem teszi lehetővé a hálózat vagy a helyi fájlrendszer elérését állandó módon. Minden helyileg mentett fájl el van különítve, és a modul befejeződése után törlődik. A Python-kód nem fér hozzá a legtöbb, a gépen futtatott könyvtárhoz, a kivétel pedig az aktuális könyvtár és alkönyvtárai.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>A kifinomult fejlesztési és hibakeresési támogatás hiánya

A Python-modul jelenleg nem támogatja az olyan IDE-szolgáltatásokat, mint az IntelliSense és a hibakeresés. Továbbá, ha a modul futási ideje meghiúsul, a teljes Python stack nyomkövetés elérhető. Ezt azonban a modul kimeneti naplójában kell megtekinteni. Jelenleg a Python-parancsfájlok fejlesztését és hibakeresését javasoljuk egy olyan környezetben, mint a IPython, majd a kód importálása a modulba.

### <a name="single-data-frame-output"></a>Egyetlen adatkeret kimenete

A Python belépési pontja csak egy adatkeret kimenetként való visszaküldésére engedélyezett. Jelenleg nem lehetséges olyan tetszőleges Python-objektumokat visszaadni, mint például a betanított modellek közvetlenül a Studio (klasszikus) futtatókörnyezethez. A [végrehajtási R-szkripthez][execute-r-script]hasonlóan, amely azonos korlátozással rendelkezik, számos esetben lehetséges, hogy az objektumokat egy byte-tömbbe írja, majd egy adatkereten belül visszaadja.

### <a name="inability-to-customize-python-installation"></a>A Python-telepítés testreszabásának lehetősége

Jelenleg az egyéni Python-modulok hozzáadásának egyetlen módja a korábban ismertetett zip-fájl mechanizmuson keresztül. Habár ez a kis modulok esetében is lehetséges, nehézkes a nagyméretű modulok (különösen a natív DLL-eket tartalmazó modulok) vagy nagy számú modul használata esetén.

## <a name="next-steps"></a>Következő lépések

További információ: [Python fejlesztői központ](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script