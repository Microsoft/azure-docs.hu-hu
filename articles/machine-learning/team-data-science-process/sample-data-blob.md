---
title: Mintaadatok az Azure Blob Storage-ban – csoportos adatelemzési folyamat
description: Az Azure Blob Storage-ban tárolt mintavételi adatai programozott módon történő letöltésével, majd a Pythonban írt eljárások használatával történő mintavételezéssel.
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
ms.openlocfilehash: 04528d28e9f54710cd0a63372e32b099c2e07fb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86026168"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Adatmintavétel Azure blobtárolóban

Ez a cikk az Azure Blob Storage-ban tárolt mintavételi adataira vonatkozik, ha programozott módon letölti, majd a Pythonban írt eljárásokkal mintavételezést végez.

**Miért érdemes felvenni az adatait?**
Ha az elemezni kívánt adatkészlet nagy méretű, általában egy jó ötlet, hogy lerövidítse az adatokat, hogy csökkentse azt kisebb, de reprezentatív és felügyelhető méretre. A mintavétel megkönnyíti az adatmegismerést, a feltárást és a funkciók mérnöki felépítését. A Cortana Analytics-folyamat feladata az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípusának engedélyezése.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

## <a name="download-and-down-sample-data"></a>Az adatminta letöltése és leállása
1. Töltse le az Azure Blob Storage-ból származó adatait az alábbi Python-kód Blob service használatával: 

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    LOCALFILENAME= <local_file_name>        
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    #download from blob
    t1=time.time()
    blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    t2=time.time()
    print(("It takes %s seconds to download "+blobname) % (t2 - t1))
    ```

2. Adatok beolvasása egy Panda-adatkeretbe a fent letöltött fájlból.

    ```python
    import pandas as pd

    #directly ready from file on disk
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

3. Lefelé – a következő módon kell mintát venni az adataival `numpy` `random.choice` :

    ```python
    # A 1 percent sample
    sample_ratio = 0.01 
    sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]
    ```

Most már dolgozhat a fenti adatkerettel, és az egyszázalékos mintát is használhatja a további feltáráshoz és a funkciók létrehozásához.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Adatok feltöltése és beolvasása Azure Machine Learning
Az alábbi mintakód segítségével leállíthatja az adatmintavételezést, és közvetlenül a Azure Machine Learningban is használhatja:

1. Az adatkeret írása helyi fájlba

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Töltse fel a helyi fájlt egy Azure-blobba a következő mintakód használatával:

    ```python
    from azure.storage.blob import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading to the blob:"+ BLOBNAME)
    ```

3. Olvassa el az Azure Blob adatait az alábbi képen látható Azure Machine Learning [importálási adatok](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) használatával:

![olvasó blob](./media/sample-data-blob/reader_blob.png)

