---
title: Deep learning & AI-keretrendszerek
titleSuffix: Azure Data Science Virtual Machine
description: Elérhető mély tanulási keretrendszerek és eszközök az Azure Data Science Virtual Machineon.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 43a8004b6cdfe2e074304569b83360101784a87e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568001"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Mély tanulási és AI-keretrendszerek az Azure Data Science VM
Az alábbi lista mély tanulási keretrendszerek a DSVM.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Ubuntu 16.04    |
| Hogyan van konfigurálva/telepítve a DSVM?  | A Cafe telepítve van a-ben `/opt/caffe` .   A minták a következők: `/opt/caffe/examples` .|
| Futtatás      | a X2Go használatával jelentkezzen be a virtuális gépre, majd indítson el egy új terminált, és adja meg a következőt:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Megnyílik egy új böngészőablak, amely minta jegyzetfüzeteket használ. A bináris fájlok a/opt/caffe/BUILD/install/bin.-ben vannak telepítve<br/><br/>A Cafe telepített verziójának a Python 2,7-es verziójára van szüksége, és a Python 3,6 nem működik, amely alapértelmezés szerint aktiválva van. A Python 2,7-re való váltáshoz futtassa a parancsot `source activate root` az anaconda-környezetre való váltáshoz.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | |
| Támogatott DSVM-kiadások      | Ubuntu 16.04     |
| Hogyan van konfigurálva/telepítve a DSVM?  | A Caffe2 a [Python 2,7 (root) Conda-környezetben van telepítve. |
| Futtatás      | Terminal: a Python elindítása és az Caffe2 importálása. <br/> * JupyterHub: [kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a Caffe2 könyvtárat, és keresse meg a minta jegyzetfüzeteket. Egyes jegyzetfüzetekhez szükséges, hogy a Caffe2-gyökér be legyen állítva a Python-kódban; /opt/caffe2. megadása |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 5,2 |
| Támogatott DSVM-kiadások      | Ubuntu 16.04    |
| Hogyan van konfigurálva/telepítve a DSVM?  | A chainer a Python 3,5-es verzióra van telepítve. |
| Futtatás      | Terminál: aktiválja a Python 3,5 környezetet, futtassa a parancsot `python` , majd `import chainer` . <br/> * JupyterHub: [kapcsolódjon a JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a láncolt könyvtárat a minta jegyzetfüzetek kereséséhez.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA, cuDNN, NVIDIA illesztőprogram](https://developer.nvidia.com/cuda-toolkit)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 10.0.130|
| Támogatott DSVM-kiadások      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04  |
| Hogyan van konfigurálva/telepítve a DSVM?  |az _NVIDIA-SMI_ a rendszer elérési útján érhető el.  |
| Futtatás      | Nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa az _NVIDIA-SMI_ parancsot. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 0.16.1|
| Támogatott DSVM-kiadások      | Ubuntu 18.04<br> Ubuntu 16.04   |
| Hogyan van konfigurálva/telepítve a DSVM?  | A Horovod a Python 3,5-es verziójában van telepítve |
| Futtatás      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="keras"></a>[Keras](https://keras.io/)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 2.2.4 |
| Támogatott DSVM-kiadások      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04   |
| Hogyan van konfigurálva/telepítve a DSVM?  | A kerasz a Windows Python 3,6-es verziójában, Linuxon pedig Python 3,5-ben van telepítve |
| Futtatás      | Aktiválja a megfelelő környezetet a terminálon, majd futtassa a Pythont. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](/cognitive-toolkit/)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 2.5.1 |
| Támogatott DSVM-kiadások      | Windows 2016 <br> Ubuntu 16.04   |
| Hogyan van konfigurálva/telepítve a DSVM?  | A CNTK a Python 3,6-es verzióban van telepítve a [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) -ben és a Python 3,5-on [Linux](./dsvm-tools-languages.md#python-linux-edition)rendszeren) |
| Futtatás      | Terminál: aktiválja a megfelelő környezetet, és futtassa a Pythont. <br/>Jupyter: kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)-hez, majd nyissa meg a CNTK könyvtárat a mintákhoz. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.3.0 |
| Támogatott DSVM-kiadások      | Windows 2016 <br> Ubuntu 16.04    |
| Hogyan van konfigurálva/telepítve a DSVM?  | A MXNet `C:\dsvm\tools\mxnet` Windows és Ubuntu rendszerre van telepítve `/dsvm/tools/mxnet` . A Python-kötések a [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) -es Python 3,6-es verziójában, a [Linuxon](./dsvm-tools-languages.md#python-linux-edition)pedig a Python 3,5-ban vannak telepítve, az R-kötések pedig az Ubuntu DSVM is szerepelnek. |
| Futtatás      | Terminál: aktiválja a megfelelő Conda-környezetet, majd futtassa a parancsot `import mxnet` . <br/>Jupyter: kapcsolódjon a [Jupyter](provision-vm.md#access-the-dsvm) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)-hez, majd nyissa meg a `mxnet` minták könyvtárát. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.0.1 |
| Támogatott DSVM-kiadások      | Windows 2016 <br> Ubuntu 16.04    |
| Hogyan van konfigurálva/telepítve a DSVM?  | A MXNet Model Server a Python 3,6-es verzióra van telepítve a [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) és a Python 3,5 [Linux](./dsvm-tools-languages.md#python-linux-edition)rendszeren) |
| Futtatás      | Terminal: futtassa `sudo systemctl stop jupyterhub` a parancsot, hogy először leállítsa a JupyterHub szolgáltatást, mert mindkettő ugyanazt a portot figyeli. Ezután aktiválja a megfelelő Conda-környezetet, és futtassa a parancsot `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia rendszerkezelési felület (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) |  |
| Támogatott DSVM-kiadások      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016<br> Ubuntu 16.04   |
| Mi ez? | NVIDIA-eszköz a GPU-tevékenységek lekérdezéséhez |
| Hogyan van konfigurálva/telepítve a DSVM?  | `nvidia-smi` a rendszer elérési útja. |
| Futtatás      | Egy **GPU-val rendelkező** virtuális gépen nyisson meg egy parancssort (Windows rendszeren) vagy egy terminált (Linuxon), majd futtassa a parancsot `nvidia-smi` . |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.2.0 (Ubuntu 16,04), 1.7.1 (Ubuntu 18,04, Windows 2019) |
| Támogatott DSVM-kiadások      | Windows Server 2019<br>Ubuntu 18.04<br> Ubuntu 16.04 |
| Hogyan van konfigurálva/telepítve a DSVM?  | A [Python 3,7](dsvm-tools-languages.md#python-linux-edition) -es verzióra van telepítve a py37_pytorch Conda környezetben. A minta Jupyter jegyzetfüzeteket tartalmaz, és a minták a/dsvm/Samples/pytorch. |
| Futtatás      | Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): kapcsolódjon, majd nyissa meg a PyTorch könyvtárat a mintákhoz.  |

## <a name="tensorflow"></a>[TensorFlow](https://www.tensorflow.org/)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.13 |
| Támogatott DSVM-kiadások      | Windows Server 2019<br>Ubuntu 18.04<br> Windows 2016 <br> Ubuntu 16.04 |
| Hogyan van konfigurálva/telepítve a DSVM?  | Telepítve a Python 3,5-es verziójában [Linux](dsvm-tools-languages.md#python-linux-edition) és Python 3,6 rendszeren [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Futtatás      | Terminál: aktiválja a megfelelő környezetet, majd futtassa a Pythont. <br/> * Jupyter: kapcsolódjon a [Jupyter](provision-vm.md) vagy a [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine), majd nyissa meg a TensorFlow könyvtárat a mintákhoz.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.12 |
| Támogatott DSVM-kiadások      | Ubuntu 16.04 |
| Hogyan van konfigurálva/telepítve a DSVM?  | tensorflow_model_server a terminálon érhető el. |
| Futtatás      |  A minták [online](https://www.tensorflow.org/tfx/guide/serving)elérhetők.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

| Kategória | Érték |
| ------------- | ------------- |
| Támogatott verzió (k) | 1.0.3 |
| Támogatott DSVM-kiadások      | Ubuntu 16.04 |
| Hogyan van konfigurálva/telepítve a DSVM?  |A theano a Python 2,7 (_root_) és a Python 3,5 (_py35_) környezetben van telepítve. |
| Futtatás      |  Terminál: aktiválja a kívánt Python-verziót (root vagy py35), futtassa a Pythont, majd importálja a theano.<br/>* Jupyter: válassza ki a Python 2,7 vagy a 3,5 kernelt, majd importálja a theano.  <br/>A legújabb matematikai kernel-függvénytári (MKL-) hibák megkerüléséhez először a következőképpen kell beállítania a MKL-szálat:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |
