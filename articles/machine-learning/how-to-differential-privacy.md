---
title: Differenciális adatvédelem – SmartNoise (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan alkalmazhat differenciált adatvédelmi gyakorlatot Azure Machine Learning modellekhez a SmartNoise nyílt forráskódú kódtárak használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 62a002569696da4ef18e7bd967f027eb8247ef65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98681404"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Különbözeti adatvédelem használata Azure Machine Learningban (előzetes verzió)

Ismerje meg, hogyan alkalmazhat differenciált adatvédelmi gyakorlatot Azure Machine Learning modellekhez a SmartNoise Python nyílt forráskódú kódtárak használatával.

A különbözeti adatvédelem az adatvédelem arany szabvány szerinti definíciója. Az adatvédelem ezen definícióját betartó rendszerek erős garanciát biztosítanak az adatújjáépítési és-azonosítási támadások széles körére, beleértve a kiegészítő információkat tartalmazó ellenfelek támadásait is. További információ a [differenciált adatvédelem működéséről](./concept-differential-privacy.md).


## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>SmartNoise Python-kódtárak telepítése

### <a name="standalone-installation"></a>Önálló telepítés

A kódtárak elosztott Spark-fürtökből való működésre készültek, és ugyanúgy telepíthetők, mint bármely más csomag.

Az alábbi utasítások azt feltételezik, hogy a `python` és a `pip` parancsok le vannak képezve a és a rendszerre `python3` `pip3` .

A pip használatával telepítse a [SmartNoise Python-csomagokat](https://pypi.org/project/opendp-smartnoise/).

`pip install opendp-smartnoise`

A csomagok telepítésének ellenőrzéséhez nyisson meg egy Python-parancssort, és írja be a következőt:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

Ha az importálás sikeres, a rendszer telepíti a kódtárakat, és készen áll a használatra.

### <a name="docker-image-installation"></a>Docker-rendszerkép telepítése

SmartNoise-csomagokat is használhat a Docker segítségével.

Kérje le a `opendp/smartnoise` rendszerképet, hogy a Docker-tároló belsejében a Spark, a Jupyter és a mintakód részét képező kódtárat használja.

```sh
docker pull opendp/smartnoise:privacy
```

Ha kihúzta a rendszerképet, indítsa el a Jupyter-kiszolgálót:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Ez egy Jupyter-kiszolgálót indít el a porton `8989` a `localhost` jelszóval `pass@word99` . Ha a fenti parancssor használatával indítja el a tárolót a névvel `smartnoise-privacy` , a Jupyter-kiszolgálón egy bash-terminált is megnyithat a következő futtatásával:

```sh
docker exec -it smartnoise-run bash
```

A Docker-példány törli az összes állapotot leállítás után, így elveszíti a futó példányban létrehozott jegyzetfüzeteket. Ennek orvoslásához kötést köthet egy helyi mappa csatlakoztatásához a tárolóhoz a indításakor:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

A *saját jegyzetfüzetek* mappájában létrehozott jegyzetfüzetek a helyi fájlrendszerben lesznek tárolva.

## <a name="perform-data-analysis"></a>Adatok elemzésének végrehajtása

A differentially privát kiadásának előkészítéséhez ki kell választania egy adatforrást, egy statisztikát és néhány adatvédelmi paramétert, amely az adatvédelem szintjét jelzi.  

Ez a példa a kaliforniai nyilvános használati adatokra (PUMS) hivatkozik, amelyek az állampolgárok demográfiai adataihoz tartozó anonim rekordokat jelképezik:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

Ebben a példában az átlagot és a kor eltérését számítjuk ki.  A `epsilon` rendszer összesen 1,0 (epszilon adatvédelmi paramétert használunk, amely a számítási feladatokhoz tartozó két mennyiségre kiterjedő adatvédelmi költségvetést terjeszti. További információ az [adatvédelmi mérőszámokról](concept-differential-privacy.md#differential-privacy-metrics).

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Az eredmények az alábbihoz hasonló módon néznek ki:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Ebben a példában néhány fontos tudnivalót láthat.  Először az `Analysis` objektum egy adatfeldolgozási gráfot jelöl.  Ebben a példában az átlag és a variancia kiszámítása ugyanabból a forrás-csomópontból történik.  Azonban olyan összetettebb kifejezéseket is megadhat, amelyek tetszőleges módon kombinálják a bemeneteket a kimenetekkel.

Az elemzési gráf tartalmazza `data_upper` a és a `data_lower` metaadatokat, és meghatározza a korok alsó és felső határát.  Ezeknek az értékeknek a használatával pontosan kalibrálhatja a zajt a differenciált adatvédelem biztosítása érdekében.  Ezeket az értékeket a kiugró vagy a hiányzó értékek kezelésére is használják.

Végül az Analysis Graph nyomon követi az összes fordított adatvédelmi költségvetést.

A könyvtár használatával összetettebb elemzési diagramokat hozhat létre, több mechanizmussal, statisztikával és segédprogram-funkcióval:

| Statisztika    | Mechanizmusok | Segédprogramok  |
| ------------- |------------|------------|
| Darabszám         | Gauss   | Típuskonverzió       |
| Hisztogram     | Lévő értékek mértani  | Befogó   |
| Középérték          | Laplace    | Digitalizálni   |
| Kvantilisek     |            | Szűrő     |
| Sum           |            | Beszámítás |
| Variancia/szórás |      | Átalakítás  |

További részletekért tekintse meg az [adatelemzési jegyzetfüzetet](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb) .

## <a name="approximate-utility-of-differentially-private-releases"></a>A differentially privát kiadásainak hozzávetőleges segédprogramja

Mivel a differenciált adatvédelem a zaj kalibrálásával működik, az adatvédelmi kockázattól függően a kiadások segédprogramja eltérő lehet.  Általában az egyes személyek elleni védelemhez szükséges zaj elhanyagolható, mivel a mintavételezési méretek nagy méretűek, de az eredmény az, hogy egyetlen személyt céloznak meg.  Az elemzők megtekinthetik a kiadás pontossági információit annak meghatározására, hogy a kiadás milyen hasznos:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

A művelet eredményének a következőhöz hasonlóan kell kinéznie:

```text
Age accuracy is: 0.2995732273553991
```

Ez a példa a fenti középértéket számítja ki, és a `get_accuracy` függvény használatával kéri a pontosságot 0,05-kor `alpha` . Az `alpha` 0,05%-os az 95%-os intervallumot jelöli, amely a közzétett érték az idő 95%-ában megadott pontossági határokon fog esni.  Ebben a példában a jelentett pontosság 0,3, ami azt jelenti, hogy a felszabadított érték a 0,6-es szélességi intervallumon belül lesz, az idő 95%-ában.  Nem helyes ennek az értéknek a meggondolása, mert a kiadott érték a jelzett pontossági tartományon kívül esik a megadott sebességgel `alpha` , és a tartományon kívüli értékek bármelyik irányban kívül lehetnek.

Az elemzők különböző értékekre lekérhetik, `get_accuracy` `alpha` hogy szűkebb vagy szélesebb körű megbízhatósági időközöket kapjanak, anélkül, hogy további adatvédelmi költségekkel kellene számolniuk.

## <a name="generate-a-histogram"></a>Hisztogram létrehozása

A beépített `dp_histogram` függvény differentially saját hisztogramokat hoz létre a következő adattípusok bármelyikében:

- Folytonos változó, amelyben a számok készletét raktárhelyekre kell osztani
- Egy logikai vagy dichotóm változó, amely csak két értéket vehet igénybe
- Egy kategorikus változó, amely sztringként enumerált különböző kategóriákat mutat be.

Íme egy példa a `Analysis` folytonos változó hisztogramhoz tartozó raktárhelyek megadására:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Mivel a rendszer a hisztogram-raktárhelyek között különállóan particionálja az egyéneket, az adatvédelmi költségek csak egyszer szerepelnek a hisztogramon, még akkor is, ha a hisztogram sok raktárhelyet tartalmaz.

További információt a hisztogramok [jegyzetfüzetben](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb)talál.

## <a name="generate-a-covariance-matrix"></a>Szórási mátrix létrehozása

A SmartNoise három különböző funkciót kínál a `dp_covariance` működéséhez:

- Két vektor közötti eltérés
- Mátrix szórási mátrixa
- Egy pár mátrix összevetésének mátrixa

Az alábbi példa egy Skaláris szórásos számítást mutat be:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

További információ: a [szórásos jegyzetfüzet](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a [SmartNoise-minta notebookokkal](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis).