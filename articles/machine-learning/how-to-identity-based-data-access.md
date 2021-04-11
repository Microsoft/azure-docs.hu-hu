---
title: Identitás-alapú adathozzáférés az Azure-beli tárolási szolgáltatásokhoz
titleSuffix: Machine Learning
description: Megtudhatja, hogyan használhatja az identitás-alapú adathozzáférést az Azure-beli tárolási szolgáltatásokhoz való kapcsolódáshoz Azure Machine Learning adattárral és a Python SDK Machine Learningával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210652"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Kapcsolódás a tárolóhoz identitás-alapú adathozzáférés (előzetes verzió) használatával

>[!IMPORTANT]
> A cikkben bemutatott funkciók előzetes verzióban érhetők el. A [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verziónak olyan szolgáltatásoknak kell tekinteniük, amelyek bármikor megváltozhatnak.

Ebből a cikkből megtudhatja, hogyan kapcsolódhat az Azure-beli tárolási szolgáltatásokhoz identitás-alapú adathozzáférés és Azure Machine Learning adattárolók használatával a [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro)-n keresztül.  

Az adattárolók általában hitelesítő adatokon alapuló adathozzáféréssel igazolják, hogy rendelkezik a tárolási szolgáltatás eléréséhez szükséges engedéllyel. A munkaterülethez társított [kulcstartóban](https://azure.microsoft.com/services/key-vault/) megőrzik a kapcsolati adatokat, például az előfizetés-azonosítót és a jogkivonat-hitelesítést. Amikor identitás-alapú adatelérést használó adattárat hoz létre, az Azure-fiókja ([Azure Active Directory-token](../active-directory/fundamentals/active-directory-whatis.md)) segítségével ellenőrizheti, hogy van-e engedélye a tárolási szolgáltatás elérésére. Ebben a forgatókönyvben a rendszer nem menti a hitelesítési hitelesítő adatokat. A rendszer csak a Storage-fiók adatait tárolja az adattárban. 

A hitelesítő adatokon alapuló hitelesítést, például hozzáférési kulcsokat vagy egyszerű szolgáltatásokat használó adattárolók létrehozásával kapcsolatban lásd: [Kapcsolódás az Azure-beli tárolási szolgáltatásokhoz](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Identitás-alapú adatokhoz való hozzáférés Azure Machine Learning

Két olyan forgatókönyv létezik, amelyekben Azure Machine Learning identitás-alapú adatelérést alkalmazhat. Ezek a forgatókönyvek jól illeszkednek az identitás-alapú hozzáféréshez, ha bizalmas adatokkal dolgozik, és részletesebb adathozzáférés-kezelést igényel:

- A tárolási szolgáltatások elérése
- A gépi tanulási modellek betanítása magánjellegű adattal

### <a name="accessing-storage-services"></a>A tárolási szolgáltatások elérése

A tárolási szolgáltatásokhoz Azure Machine Learning adattárakkal vagy [Azure Machine learning adatkészletekkel](how-to-create-register-datasets.md)való identitás-alapú adathozzáférésen keresztül kapcsolódhat. 

A hitelesítési hitelesítő adatait általában egy adattár tárolja, amely biztosítja, hogy jogosult legyen a tárolási szolgáltatás elérésére. Ha ezeket a hitelesítő adatokat az adattáron keresztül regisztrálja, a munkaterület-olvasó szerepkörrel rendelkező felhasználók lekérhetik azokat. A hozzáférés mértéke egyes szervezetek számára is biztonsági szempont lehet. [További információ a munkaterület-olvasó szerepkörről.](how-to-assign-roles.md#default-roles) 

Ha identitás-alapú adatelérést használ, Azure Machine Learning kéri az adathozzáférés-hitelesítéshez szükséges Azure Active Directory tokent, ahelyett, hogy megtartja a hitelesítő adatait az adattárban. Ez a megközelítés lehetővé teszi az adathozzáférés-kezelést a tárolási szinten, és bizalmasan tartja a hitelesítő adatokat. 

Ugyanez a viselkedés a következő esetekben érvényes:

* [Adatkészlet létrehozása közvetlenül a tárolási URL-címekről](#use-data-in-storage). 
* Interaktív módon dolgozhat az adatokkal a helyi számítógépen vagy a [számítási példányon](concept-compute-instance.md)Jupyter notebook használatával.

> [!NOTE]
> A hitelesítő adatokon alapuló hitelesítéssel tárolt hitelesítő adatok közé tartoznak az előfizetés-azonosítók, a közös hozzáférésű aláírás (SAS) tokenek, valamint a tárterület-hozzáférési kulcs és az egyszerű szolgáltatásnév, például az ügyfél-azonosítók és a bérlő

### <a name="model-training-on-private-data"></a>A magánjellegű adatképzések modellezése

Bizonyos gépi tanulási forgatókönyvek személyes adatokkal rendelkező képzési modelleket tartalmaznak. Ilyen esetekben az adatszakértőknek be kell futtatniuk a betanítási munkafolyamatokat a bizalmas bemeneti adatok nélkül. Ebben az esetben a betanítási számítás felügyelt identitását használja az adathozzáférés hitelesítéséhez. Ez a módszer lehetővé teszi a Storage-rendszergazdák számára a Storage blob-adatolvasók számára a hozzáférést a felügyelt identitáshoz, amelyet a betanítási feladatok a betanítási feladatok futtatására használnak. Az egyes adatszakértőknek nem kell hozzáférést biztosítaniuk. További információkért lásd: [felügyelt identitás beállítása számítási fürtön](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Egy támogatott tárolási típusú Azure Storage-fiók. Az előzetes verzióban a következő tárolási típusok támogatottak: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [1. generációs Azure Data Lake Storage](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/install).

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy [meglévőt a Python SDK-n keresztül](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Tárterület-hozzáférési engedélyek

Annak érdekében, hogy biztonságosan kapcsolódjon a Storage szolgáltatáshoz az Azure-ban, Azure Machine Learning megköveteli, hogy rendelkezzen a megfelelő adattároló eléréséhez szükséges engedéllyel.

Az identitás-alapú adathozzáférés csak a következő tárolási szolgáltatásokhoz való kapcsolódást támogatja:

* Azure Blob Storage
* 1. generációs Azure Data Lake Storage
* 2. generációs Azure Data Lake Storage
* Azure SQL Database

A tárolási szolgáltatások eléréséhez legalább [Storage blob Adatolvasó](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) -hozzáférésre van szükség. Csak a Storage-fiókok tulajdonosai [módosíthatják a hozzáférési szintet a Azure Portalon keresztül](../storage/common/storage-auth-aad-rbac-portal.md).

Ha távoli számítási célra készít modellt, a számítási identitásnak legalább a Storage-beli blob-adatolvasói szerepkört kell biztosítania a tárolási szolgáltatástól. Ismerje meg, hogyan [állíthatja be a felügyelt identitást egy számítási fürtön](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Virtuális hálózatok használata

Alapértelmezés szerint a Azure Machine Learning nem tud kommunikálni tűzfallal vagy virtuális hálózattal ellátott Storage-fiókkal.

A Storage-fiókokat konfigurálhatja úgy, hogy csak adott virtuális hálózatokon belülről engedélyezze a hozzáférést. Ehhez a konfigurációhoz további lépések szükségesek, amelyekkel biztosítható, hogy az adatmennyiség ne legyen kiszivárgott a hálózaton kívül. Ez a viselkedés ugyanaz, mint a hitelesítő adatokhoz való hozzáférés. További információ: [virtuális hálózati forgatókönyvek konfigurálása](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Amikor adattárként regisztrálja a Storage szolgáltatást az Azure-ban, automatikusan létrehozza és regisztrálja az adattárt egy adott munkaterületen. A szükséges engedélyekkel kapcsolatos útmutatásért lásd a [Storage hozzáférési engedélyeit](#storage-access-permissions) . A virtuális hálózatok mögötti adattárolóhoz való kapcsolódásról további információt a [virtuális hálózatok használata](#work-with-virtual-networks) című témakörben talál.

A következő kódban figyelje meg, hogy hiányoznak a hitelesítési paraméterek, például,, `sas_token` `account_key` `subscription_id` és az egyszerű szolgáltatásnév `client_id` . Ez a kihagyás azt jelzi, hogy Azure Machine Learning az identitás-alapú adathozzáférést fogja használni a hitelesítéshez. Az adattárolók létrehozása általában interaktív módon történik egy jegyzetfüzetben vagy a studión keresztül. Így a Azure Active Directory-token az adathozzáférés-hitelesítéshez használatos.

> [!NOTE]
> Az adattár neve csak kisbetűkből, számokból és aláhúzásokból állhat. 

### <a name="azure-blob-container"></a>Azure Blob-tároló

Az Azure Blob-tárolók adattárként való regisztrálásához használja a következőt: [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

A következő kód létrehozza az adattárat `credentialless_blob` , regisztrálja azt a `ws` munkaterületen, és hozzárendeli a `blob_datastore` változóhoz. Ez az adattár hozzáfér a `my_container_name` blob-tárolóhoz a `my-account-name` Storage-fiókban.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

A [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) használatával regisztrálja a Azure Data Lake Storage Gen1hoz csatlakozó adattárt.

A következő kód létrehozza az adattárat `credentialless_adls1` , regisztrálja azt a `workspace` munkaterületen, és hozzárendeli a `adls_dstore` változóhoz. Ez az adattár fér hozzá a `adls_storage` Azure Data Lake Storage fiókhoz.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

A [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálja a Azure Data Lake Storage Gen2hoz csatlakozó adattárt.

A következő kód létrehozza az adattárat `credentialless_adls2` , regisztrálja azt a `ws` munkaterületen, és hozzárendeli a `adls2_dstore` változóhoz. Ez az adattár fér hozzá a fájlrendszerhez `tabular` a `myadls2` Storage-fiókban.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Adattárolási szolgáltatás használata

Azt javasoljuk, hogy [Azure Machine learning adatkészleteket](how-to-create-register-datasets.md) használjon, ha a tárolóban lévő adatokat Azure Machine learning használatával használja. 

Az adatkészletek az adatokat egy lustán kiértékelt, a gépi tanulási feladatokhoz (például a képzéshez) tartozó objektumba csomagolják. Emellett az adatkészletek segítségével bármilyen formátumú fájlokat [tölthet le vagy csatlakoztathat](how-to-train-with-datasets.md#mount-vs-download) az Azure Storage-szolgáltatásokból, például az Azure Blob Storage-ból, és Azure Data Lake Storage egy számítási célra.


Az identitás-alapú adateléréssel rendelkező adatkészletek létrehozásához a következő lehetőségek állnak rendelkezésre. Ez a típusú adatkészlet-létrehozás a Azure Active Directory tokent használja az adathozzáférés-hitelesítéshez. 

*  Az adattárolók olyan elérési útjai, amelyek az identitás-alapú adathozzáférést is használják. 
<br>A következő példában `blob_datastore` már létezik, és identitás-alapú adatelérést használ.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Az adattár létrehozásának kihagyása és adatkészletek létrehozása közvetlenül a Storage URL-címekről. Ez a funkció jelenleg csak az Azure-blobokat és a Azure Data Lake Storage Gen1-és Gen2 támogatja.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Ha olyan betanítási feladatot küld, amely identitás-alapú adateléréssel létrehozott adatkészletet használ, a betanítási számítás felügyelt identitását használja az adathozzáférés hitelesítéséhez. A Azure Active Directory-token nincs használatban. Ebben az esetben ügyeljen arra, hogy a számítás felügyelt identitása legalább a Storage-Adatolvasó szerepkört kapja meg a Storage szolgáltatásban. További információ: [a felügyelt identitás beállítása a számítási fürtökön](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Következő lépések

* [Azure Machine Learning adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Betanítás adathalmazok használatával](how-to-train-with-datasets.md)
* [Adattároló létrehozása kulcs alapú adateléréssel](how-to-access-data.md)
