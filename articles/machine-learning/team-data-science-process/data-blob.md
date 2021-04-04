---
title: Azure Blob-alapú adatfeldolgozás speciális elemzésekkel – csoportos adatelemzési folyamat
description: Az adatok megismerése és szolgáltatások készítése az Azure Blob Storage-ban tárolt adatokból speciális elemzések használatával.
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
ms.openlocfilehash: 5a088d5918a957036b905db9136f9b16e5f0527e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93307161"
---
# <a name="process-azure-blob-data-with-advanced-analytics"></a><a name="heading"></a>Azure blobadatok feldolgozása bővített analitikával
Ez a dokumentum az Azure Blob Storage-ban tárolt adatokkal kapcsolatos adatok feltárását és funkcióinak generálását ismerteti. 

## <a name="load-the-data-into-a-pandas-data-frame"></a>Az adatgyűjtés egy Panda-adatkeretbe
Az adatkészletek feltárásához és kezeléséhez le kell tölteni a blob-forrásból egy helyi fájlba, amelyet azután be lehet tölteni egy Panda-adatkeretbe. A következő lépéseket kell követnie ehhez az eljáráshoz:

1. Töltse le az Azure Blob adatait az alábbi Python-kóddal Blob service használatával. Cserélje le a változót az alábbi kódban az adott értékekre: 
   
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
2. Az adatok beolvasása a letöltött fájlból a pandák adatkeretbe.
   
    ```python
    #LOCALFILE is the file path    
    dataframe_blobdata = pd.read_csv(LOCALFILE)
    ```

Most már készen áll arra, hogy felderítse az adatokat, és létrehozza az adatkészlet funkcióit.

## <a name="data-exploration"></a><a name="blob-dataexploration"></a>Adatelemzés
Íme néhány példa arra, hogyan lehet az adatelemzést használni a pandák használatával:

1. Sorok és oszlopok számának vizsgálata 
   
    ```python
    print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
    ```
2. Vizsgálja meg az adatkészlet első vagy utolsó néhány sorát az alábbi módon:
   
    ```python
    dataframe_blobdata.head(10)
   
    dataframe_blobdata.tail(10)
    ```
3. Az egyes oszlopok adattípusának ellenőrzését az alábbi mintakód használatával importálja.
   
    ```python
    for col in dataframe_blobdata.columns:
        print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
    ```
4. A következőképpen tekintse meg az adathalmaz oszlopainak alapvető statisztikáit
   
    ```python
    dataframe_blobdata.describe()
    ```
5. Tekintse meg az egyes oszlopok értékeinek számát a következőképpen:
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts()
    ```
6. A hiányzó értékeket és az egyes oszlopokban lévő bejegyzések tényleges számát az alábbi mintakód alapján számítja ki.
   
    ```python
    miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
    print miss_num
    ```
7. Ha hiányoznak az adatok egy adott oszlopához tartozó értékek, a következőképpen húzhatja őket:
   
    ```python
    dataframe_blobdata_noNA = dataframe_blobdata.dropna()
    dataframe_blobdata_noNA.shape
    ```
   
   A hiányzó értékek cseréjének másik módja a Mode függvény:
   
    ```python
    dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})  
    ```      
8. Hisztogram-ábra létrehozása változó számú raktárhely használatával a változó eloszlásának ábrázolásához    
   
    ```python
    dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
   
    np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
    ```
9. Megtekintheti a változók közötti korrelációkat egy scatterplot vagy a beépített korrelációs függvény használatával.
   
    ```python
    #relationship between column_a and column_b using scatter plot
    plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
   
    #correlation between column_a and column_b
    dataframe_blobdata[['<column_a>', '<column_b>']].corr()
    ```

## <a name="feature-generation"></a><a name="blob-featuregen"></a>Szolgáltatás létrehozása
A Python használatával a következőképpen hozhatunk ki szolgáltatásokat:

### <a name="indicator-value-based-feature-generation"></a><a name="blob-countfeature"></a>Kijelző érték-alapú funkciójának generálása
A kategorikus funkciókat a következőképpen lehet létrehozni:

1. Vizsgálja meg a kategorikus oszlop eloszlását:
   
    ```python
    dataframe_blobdata['<categorical_column>'].value_counts()
    ```
2. Jelölő értékek előállítása minden egyes oszlop értékéhez
   
    ```python
    #generate the indicator column
    dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')
    ```
3. Csatlakozás a kijelző oszlophoz az eredeti adatkerettel 
   
    ```python
    #Join the dummy variables back to the original data frame
    dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)
    ```
4. Távolítsa el magát az eredeti változót:
   
    ```python
    #Remove the original column rate_code in df1_with_dummy
    dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    ```

### <a name="binning-feature-generation"></a><a name="blob-binningfeature"></a>Dobozolási szolgáltatás létrehozása
A dobozolni funkcióinak generálásához a következő lépések szükségesek:

1. Oszlopok sorrendjének hozzáadása a bin numerikus oszlophoz
   
    ```python
    bins = [0, 1, 2, 4, 10, 40]
    dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
    ```
2. Dobozolási konvertálása logikai változók sorozatából
   
    ```python
    dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    ```
3. Végül csatlakoztassa a próbabábu változóit az eredeti adatkerethez
   
    ```python
    dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)  
    ```  

## <a name="writing-data-back-to-azure-blob-and-consuming-in-azure-machine-learning"></a><a name="sql-featuregen"></a>Az Azure blobba való visszaírás és a Azure Machine Learning
Az adatok megismerése és a szükséges funkciók létrehozása után feltöltheti az adatok (minta vagy featurized) egy Azure-blobba, és felhasználhatja azokat Azure Machine Learning a következő lépések végrehajtásával: további funkciók hozhatók létre a Azure Machine Learning Studio (klasszikus) is. 

1. Az adatkeret írása helyi fájlba
   
    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```
2. Töltse fel az adatok az Azure blobba az alábbi módon:
   
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
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```
3. Most az adatok beolvashatók a blobból a Azure Machine Learning [importálási][import-data] modul használatával, ahogy az alábbi képernyőn látható:

![olvasó blob][1]

[1]: ./media/data-blob/reader_blob.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data