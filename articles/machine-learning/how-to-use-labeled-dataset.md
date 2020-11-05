---
title: Adathalmazok létrehozása és feltárása címkékkel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan exportálhat adatfeliratokat a Azure Machine Learning címkéző projektjeiből, és hogyan használhatja őket gépi tanulási feladatokhoz.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: 05535f47b212c3f72256dd8b2b8e3923c309a08c
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356365"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Azure Machine Learning adatkészlet létrehozása és feltárása címkékkel

Ebből a cikkből megtudhatja, hogyan exportálhatja az adatfeliratokat egy Azure Machine Learning adatcímkéző projektből, és hogyan töltheti be azokat népszerű formátumba, például egy Panda dataframe az adatfeltáráshoz vagy egy Torchvision adatkészlethez a képátalakításhoz. 

## <a name="what-are-datasets-with-labels"></a>A címkékkel rendelkező adatkészletek 

A címkével ellátott adatkészletek használatával Azure Machine Learning adatkészleteket. A címkézett adatkészletek adott adatkészlet-típusai csak Azure Machine Learning adatcímkéző projektek kimenetében jönnek létre. Hozzon létre egy adatcímkéző projektet a [következő lépésekkel](how-to-create-labeling-projects.md). A Machine Learning támogatja a képbesoroláshoz a többcímkés vagy a többosztályos, valamint az objektum-azonosítókat a kötött mezőkkel együtt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree) .
* A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), vagy a [Azure Machine learning studióhoz](https://ml.azure.com/)való hozzáférés.
    * Az [Azure--beli-adathalmaz-adatkészlet](/python/api/azureml-contrib-dataset/?preserve-view=true&view=azure-ml-py) csomagjának telepítése
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).
* Hozzáférés egy Azure Machine Learning adatcímkéző projekthez. Ha nem rendelkezik címkéző projekttel, hozzon létre egyet [ezekkel a lépésekkel](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Adatfeliratok exportálása 

Amikor elvégez egy adatcímkéző projektet, exportálhatja a címke adatait egy címkéző projektből. Ezzel lehetővé teszi, hogy rögzítse az adatokat és a hozzájuk tartozó címkéket is, és exportálja őket [kókusz formátumban](http://cocodataset.org/#format-data) vagy Azure Machine learning adatkészletként. Használja az **Exportálás** gombot a címkéző projekt **Project Details (projekt részletei** ) lapján.

### <a name="coco"></a>COCO 

 A kókusz-fájl a Azure Machine Learning munkaterület alapértelmezett blob-tárolójában jön létre az *export/Coco* mappában lévő mappában. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning adatkészlet

Az exportált Azure Machine Learning adatkészletet a Azure Machine Learning Studio **adatkészletek** szakaszában érheti el. Az adatkészlet **részletei** lap a Pythonból elérhető feliratok eléréséhez is tartalmaz mintakód-kódot.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Címkézett adatkészletek megismerése

Betöltheti a címkével ellátott adatkészleteket egy Panda dataframe vagy Torchvision-adatkészletbe a népszerű nyílt forráskódú kódtárak kihasználása érdekében az adatok feltárásához, valamint a képek átalakításához és betanításához a PyTorch biztosított könyvtárakat.

### <a name="pandas-dataframe"></a>Panda dataframe

A címkével ellátott adatkészleteket egy Panda dataframe is betöltheti a [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) osztály metódusával `azureml-contrib-dataset` . Telepítse az osztályt a következő rendszerhéj-paranccsal: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>A azureml. a névterek gyakran változnak, ahogy dolgozunk a szolgáltatás tökéletesítésén. Ennek megfelelően az ebben a névtérben található bármit előzetes verziónak kell tekinteni, és a Microsoft nem támogatja teljes mértékben.

A Azure Machine Learning a következő fájlkezelési beállításokat kínálja a fájl-adatfolyamok számára a pandák dataframe való átalakításkor.
* Letöltés: töltse le az adatfájlokat egy helyi elérési útra.
* Csatlakoztatás: az adatfájlok csatlakoztatása csatlakoztatási ponthoz. A Mount csak a Linux-alapú számítási feladatokhoz használható, beleértve a Azure Machine Learning notebook VM-et és a Azure Machine Learning számítási feladatait.

A következő kódban az `animal_labels` adatkészlet egy korábban a munkaterületre mentett címkéző projekt kimenete.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision adatkészletek

Címkézett adatkészleteket is betölthet a Torchvision adatkészletbe a [to_torchvision ()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-torchvision--) metódussal a `azureml-contrib-dataset` osztályból is. Ennek a módszernek a használatához telepítenie kell a [PyTorch](https://pytorch.org/) . 

A következő kódban az `animal_labels` adatkészlet egy korábban a munkaterületre mentett címkéző projekt kimenete.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az [adathalmazt a címkék jegyzetfüzettel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) a teljes betanítási minta megtekintéséhez.