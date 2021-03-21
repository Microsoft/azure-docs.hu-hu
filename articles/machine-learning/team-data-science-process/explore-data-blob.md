---
title: Az Azure Blob Storageban található adatelemzés a Panda-Team adatelemzési folyamattal
description: Az Azure Blob-tárolóban tárolt, a pandák Python-csomaggal tárolt adatelemzések.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 855998b887f1d446ee8d196ff4628e066cb5d675
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98805672"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Az Azure Blob Storageban található adatfelfedezések és a pandák

Ez a cikk bemutatja, hogyan vizsgálhatja meg az Azure Blob-tárolóban tárolt, a [pandák](https://pandas.pydata.org/) Python-csomaggal tárolt adatelemzést.

Ez a feladat a [csoportos adatelemzési folyamat](overview.md)egyik lépése.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik a következővel:

* Létrehozott egy Azure Storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* Az adatait egy Azure Blob Storage-fiókban tárolta. Ha útmutatásra van szüksége, olvassa el az [adatok áthelyezése az Azure Storage-ba és az-ból](../../storage/common/storage-choose-data-transfer-solution.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Az adatgyűjtés egy Panda DataFrame
Az adatkészletek feltárásához és kezeléséhez először le kell töltenie a blob-forrásból egy helyi fájlba, amelyet aztán egy Panda DataFrame lehet betölteni. A következő lépéseket kell követnie ehhez az eljáráshoz:

1. Töltse le az Azure Blob adatait az alábbi Python-mintakód használatával Blob service segítségével. Cserélje le a változót a következő kódban az adott értékekre:

    ```python
    from azure.storage.blob import BlockBlobService
    import pandas as pd
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))
    ```

1. Az adatok beolvasása egy Panda DataFrame a letöltött fájlból.

    ```python
    # LOCALFILE is the file path
    dataframe_blobdata = pd.read_csv(LOCALFILENAME)
    ```

Most már készen áll arra, hogy felderítse az adatokat, és létrehozza az adatkészlet funkcióit.

## <a name="examples-of-data-exploration-using-pandas"></a><a name="blob-dataexploration"></a>Példák az adatfeltárásra a pandák használatával
Íme néhány példa arra, hogyan lehet az adatelemzést használni a pandák használatával:

1. **Sorok és oszlopok számának** vizsgálata

    ```python
    print('the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape)
    ```

1. **Vizsgálja** meg a következő adatkészlet első vagy utolsó néhány **sorát** :

    ```python
    dataframe_blobdata.head(10)

    dataframe_blobdata.tail(10)
    ```

1. Az egyes oszlopok **adattípusának** ellenőrzését az alábbi mintakód használatával importálja.

    ```python
    for col in dataframe_blobdata.columns:
        print(dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype)
    ```

1. A következőképpen tekintse meg az adathalmaz oszlopainak **alapvető statisztikáit**

    ```python
    dataframe_blobdata.describe()
    ```

1. Tekintse meg az egyes oszlopok értékeinek számát a következőképpen:

    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```

1. A hiányzó értékeket és az egyes oszlopokban lévő bejegyzések tényleges számát az alábbi mintakód alapján **számítja** ki.

    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print(miss_num)
    ```

1. Ha **hiányoznak** az adatok egy adott oszlopához tartozó értékek, a következőképpen húzhatja őket:

    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```

    A hiányzó értékek cseréjének másik módja a Mode függvény:

    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna(
        {'<column_name>': dataframe_blobdata['<column_name>'].mode()[0]})
    ```

1. **Hisztogram** -ábra létrehozása változó számú raktárhely használatával a változó eloszlásának ábrázolásához

    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```

1. Megtekintheti a változók közötti **korrelációkat** egy scatterplot vagy a beépített korrelációs függvény használatával.

    ```python
    # relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

    # correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```
