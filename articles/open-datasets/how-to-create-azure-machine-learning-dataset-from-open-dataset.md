---
title: Adatkészletek létrehozása az Azure Open-adatkészletekkel
titleSuffix: Azure Open Datasets
description: Megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészletet az Azure Open adatkészletekről.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: dd1440cd7bda8d40a81290cd9f633264b9641dc5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502552"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Azure Machine Learning adatkészletek létrehozása az Azure Open-adatkészletből

Ebből a cikkből megtudhatja, hogyan hozhatja ki a kurátori adatokat a helyi vagy távoli gépi tanulási kísérletekben [Azure Machine learning](../machine-learning/overview-what-is-azure-ml.md) adatkészletekkel és az [Azure Open-adatkészletekkel](./index.yml). 

[Azure Machine learning adatkészlet](../machine-learning/how-to-create-register-datasets.md)létrehozásával létrehoz egy hivatkozást az adatforrás helyére, valamint a metaadatok másolatát. Mivel az adathalmazok kiértékelése a lustán történik, és az adatokat a meglévő helyükön maradják,
* További tárolási költségek nem merülnek fel.
* Ne kockáztatja véletlenül az eredeti adatforrások módosítását. 
* Javítsa a ML-munkafolyamatok teljesítményének sebességét.

Annak megismeréséhez, hogy az adatkészletek hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférés adatai](../machine-learning/concept-data.md#data-workflow) című cikket.

Az Azure Open-adatkészletek olyan nyilvános adatkészletek, amelyeket felhasználhat a prediktív megoldások bővítésére és pontosságuk javítására használható forgatókönyv-specifikus funkciók hozzáadására. Tekintse meg a nyilvános tartományba tartozó [adatkészletek katalógusát](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) , amely segíthet a gépi tanulási modellek képzésében, például:

* [Weather](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [népszámlálás](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [ünnepek](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [közbiztonság](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

A nyílt adatkészletek a felhőben vannak Microsoft Azure, és a [Azure Machine learning PYTHON SDK](#create-datasets-with-the-sdk) és a [Azure Machine learning Studióban](#create-datasets-with-the-studio)is szerepelnek.


## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez a következők szükségesek:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Egy [Azure Machine learning munkaterület](../machine-learning/how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](/python/api/overview/azure/ml/install), amely magában foglalja a `azureml-datasets` csomagot is.

    * Hozzon létre egy [Azure Machine learning számítási példányt](../machine-learning/how-to-create-manage-compute-instance.md), amely egy teljesen konfigurált és felügyelt fejlesztői környezet, amely integrált jegyzetfüzeteket és már telepített SDK-t tartalmaz.

    **OR**

    * Saját Python-környezetében dolgozhat, és saját maga is telepítheti az SDK-t [ezekkel az utasításokkal](/python/api/overview/azure/ml/install).

> [!NOTE]
> Egyes adatkészlet-osztályok függőségekkel rendelkeznek a [azureml-adatelőkészítés](/python/api/azureml-dataprep/) csomagon, amely csak a 64 bites Python rendszerrel kompatibilis. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) és CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Adatkészletek létrehozása az SDK-val

Ha Azure Machine Learning adatkészleteket szeretne létrehozni az Azure Open adatkészlet-osztályaival a Python SDK-ban, győződjön meg arról, hogy telepítette a csomagot a következővel: `pip install azureml-opendatasets` . Minden különálló adatkészletet a saját osztálya képvisel az SDK-ban, és bizonyos osztályok Azure Machine Learningként [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)vagy mindkettőként érhetők el. Az osztályok teljes listáját a [dokumentációban](/python/api/azureml-opendatasets/azureml.opendatasets) találja `opendatasets` .

Bizonyos osztályokat lekérhet a `opendatasets` vagy rendszerbe `TabularDataset` `FileDataset` , amely lehetővé teszi a fájlok közvetlen kezelését és/vagy letöltését. Más osztályok **csak** a `get_tabular_dataset()` `get_file_dataset()` `Dataset` Python SDK osztályának vagy funkcióinak használatával szerezhetnek be adatkészletet.

A következő kód azt mutatja, hogy a MNIST `opendatasets` osztály a vagy a értéket tudja visszaadni `TabularDataset` `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

Ebben a példában a cukorbetegség `opendatasets` osztály csak a-ban érhető el `TabularDataset` , ezért a használata `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Adatkészletek regisztrálása

Regisztrálja Azure Machine Learning adatkészletet a munkaterületén, így megoszthatja őket másokkal, és újból felhasználhatja őket a munkaterületen végzett kísérletek során. Amikor egy megnyitott adatkészletből létrehozott Azure Machine Learning adatkészletet regisztrál, az adatok letöltése nem történik meg azonnal, de az adatok a kéréskor (például a betanítás során) egy központi tárolóhelyről lesznek elérhetők.

Az adatkészletek munkaterülethez való regisztrálásához használja a [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#register-workspace--name--description-none--tags-none--create-new-version-false-) metódust. 

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Adatkészletek létrehozása a Studióval

Létrehozhat Azure Machine Learning adatkészleteket az Azure Open-adatkészletekről a [Azure Machine learning Studióval](https://ml.azure.com), egy összevont webes felülettel, amely magában foglalja a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyveket végezhet az összes képzettségi szint adatelemző szakemberek számára.

> [!Note]
> A Azure Machine Learning Studióval létrehozott adatkészletek automatikusan regisztrálva lesznek a munkaterületen.

1. A munkaterületen válassza az **adatkészletek** lapot az **eszközök** területen. Az **adatkészlet létrehozása** legördülő menüben válassza a **megnyitott adatkészletek** lehetőséget.

    ![Adatkészlet megnyitása a felhasználói felületen](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Válasszon ki egy adatkészletet a csempe kiválasztásával. (A keresősáv használatával szűrheti a szűrőt.) Válassza a **tovább** lehetőséget.

    ![Adatkészlet kiválasztása](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Válassza ki azt a nevet, amelyben regisztrálni kívánja az adatkészletet, és opcionálisan szűrheti az adatokat az elérhető szűrők használatával. Ebben az esetben a **munkaszüneti** adatkészletek esetében az időszakot egy évig, az országkód pedig csak az Egyesült Államokban szűri. Az adatok részletezéséhez tekintse [meg az Azure Open adatkészletek katalógusát](https://azure.microsoft.com/services/open-datasets/catalog) , például a mezők leírását és a dátumtartomány értékét. Válassza a **Létrehozás** lehetőséget.

    ![Adatkészlet-paraméterek beállítása és adatkészlet létrehozása](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Az adatkészlet mostantól elérhető a munkaterületen az **adatkészletek** területen. Azt ugyanúgy használhatja, mint a létrehozott többi adatkészletet.


## <a name="access-datasets-for-your-experiments"></a>A kísérletek hozzáférési adatkészletei

Az adatkészleteket a gépi tanulási kísérletekben használhatja a ML-modellek betanításához. [További információ az adatkészletek betanításáról](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A nyílt adatkészletek funkcióinak példái és bemutatói a [következő](samples.md)példákat mutatják be.

## <a name="next-steps"></a>Következő lépések

* [Az első ml-modell betanítása](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Adatkészletek betanítása](../machine-learning/how-to-train-with-datasets.md).

* [Hozzon létre egy Azure Machine learning-adatkészletet](../machine-learning/how-to-create-register-datasets.md).
