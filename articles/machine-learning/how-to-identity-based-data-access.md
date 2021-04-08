---
title: Identitás-alapú adathozzáférés az Azure-beli tárolási szolgáltatásokhoz
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható az identitás-alapú adathozzáférés az Azure-beli tárolási szolgáltatásokhoz való kapcsolódáshoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520013"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Kapcsolódás a tárolóhoz identitás-alapú adathozzáféréssel (előzetes verzió)

>[!IMPORTANT]
> A cikkben ismertetett funkciók előzetes verzióban érhetők el, és a [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verziónak minősülő funkcióknak tekintendők, amelyek bármikor megváltozhatnak.

Ebből a cikkből megtudhatja, hogyan kapcsolódhat az Azure-beli Storage-szolgáltatásokhoz identitás-alapú adathozzáféréssel és Azure Machine Learning adattárakkal a [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/intro)használatával.  

Az adattárolók általában hitelesítő adatokon alapuló adathozzáféréssel igazolják, hogy rendelkezik a tárolási szolgáltatás eléréséhez szükséges engedéllyel. A munkaterülethez társított [Key Vault](https://azure.microsoft.com/services/key-vault/) a kapcsolati adatokat, például az előfizetés-azonosítót és a jogkivonat-hitelesítést is megőrzik. Amikor identitás-alapú adatelérést használó adattárat hoz létre, az Azure-bejelentkezés ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) segítségével ellenőrizheti, hogy van-e engedélye a tárolási szolgáltatás elérésére. Ebben az esetben a rendszer nem menti a hitelesítő adatokat, és csak a Storage-fiók adatait tárolja az adattárban. 

A hitelesítő adatokon alapuló hitelesítést használó adattárolók létrehozásához, például hozzáférési kulcsokhoz vagy egyszerű szolgáltatásokhoz, tekintse [meg a Kapcsolódás a Storage szolgáltatásokhoz az Azure](how-to-access-data.md)-ban című témakört.

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Identitás-alapú adatokhoz való hozzáférés Azure Machine Learning

Az identitás-alapú adathozzáférések Azure Machine Learning két területen alkalmazhatók. Különösen, ha bizalmas adatokkal dolgozik, és részletesebb adathozzáférés-kezelést igényel. 

1. A tárolási szolgáltatások elérése.
1. A gépi tanulási modellek személyes adattal való betanítása.

### <a name="accessing-storage-services"></a>A tárolási szolgáltatások elérése

A tárolási szolgáltatásokhoz Azure Machine Learning adattárakkal vagy [Azure Machine learning adatkészletekkel](how-to-create-register-datasets.md)való identitás-alapú adathozzáférésen keresztül kapcsolódhat. 

A hitelesítési hitelesítő adatokat általában egy adattár tárolja, amely biztosítja, hogy jogosult legyen a tárolási szolgáltatás elérésére. Amikor ezek a hitelesítő adatok regisztrálva vannak az adattárolókban, a munkaterület- *olvasó* szerepkörrel rendelkező felhasználók lekérhetik azokat – ami biztonsági szempontból fontos lehet néhány szervezet számára. [További információ a munkaterület- *olvasó* szerepkörről](how-to-assign-roles.md#default-roles). 

Ha identitás-alapú adatelérést használ, Azure Machine Learning kéri az adathozzáférés-hitelesítésre vonatkozó Azure Active Directory tokent, ahelyett, hogy megtartja a hitelesítő adatait az adattárban. Amely lehetővé teszi az adathozzáférés-kezelést a tárolási szinten, és bizalmasan tartja a hitelesítő adatokat. 

Ugyanez a viselkedés akkor is érvényes, ha

* [Adatkészlet létrehozása közvetlenül a tárolási URL-címekről](#use-data-in-storage). 
* Interaktív módon dolgozhat az adatokkal a helyi gépen vagy [számítási példányon](concept-compute-instance.md)Jupyter notebookon keresztül.

> [!NOTE]
> A hitelesítő adatokon alapuló hitelesítéssel tárolt hitelesítő adatok többek között a következők: előfizetés-azonosító, közös hozzáférésű aláírás (SAS) tokenek, tároló-hozzáférési kulcsok és egyszerű szolgáltatásnév-információk, például ügyfél-azonosító és bérlői azonosító.

### <a name="model-training-on-private-data"></a>A magánjellegű adatképzések modellezése

Bizonyos gépi tanulási forgatókönyvek személyes adatokkal rendelkező képzési modelleket tartalmaznak. Ilyen esetekben az adatszakértőknek be kell futtatniuk a betanítási munkafolyamatokat a bizalmas bemeneti adatokkal való érintkezés nélkül. Ebben az esetben a betanítási számítás felügyelt identitását használja az adathozzáférés hitelesítéséhez. Így a Storage-rendszergazdák megadhatják a **Storage-blob Adatolvasói** hozzáférését ahhoz a felügyelt identitáshoz, amelyet a képzési számítási szolgáltatás a betanítási feladatok futtatására használ az egyes adatszakértők helyett. Ismerje meg, hogyan [állíthatja be a felügyelt identitást a számítási feladatokhoz](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Egy támogatott tárolási típusú Azure Storage-fiók. Az előzetes verzióban a következő tárolási típusok támogatottak. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [1. generációs Azure Data Lake](../data-lake-store/index.yml)
    - [2. generációs Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/install).

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy [meglévőt a Python SDK-n keresztül](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Tárterület-hozzáférési engedélyek

Annak érdekében, hogy biztonságosan kapcsolódjon a Storage szolgáltatáshoz az Azure-ban, Azure Machine Learning megköveteli, hogy rendelkezzen a megfelelő adattároló eléréséhez szükséges engedéllyel.

Az identitás-alapú adathozzáférés csak a következő tárolási szolgáltatásokhoz való kapcsolódást támogatja:

* Azure Blob Storage
* 1. generációs Azure Data Lake
* 2. generációs Azure Data Lake
* Azure SQL-adatbázis

Ezeknek a tárolási szolgáltatásoknak a eléréséhez a Storage-beli **blob-Adatolvasó** eléréséhez minimálisan szükséges. További információ a [Storage blob-Adatolvasóról](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Csak a Storage-fiókok tulajdonosai [módosíthatják a hozzáférési szintet a Azure Portalon keresztül](../storage/common/storage-auth-aad-rbac-portal.md).

Ha a modellt egy távoli számítási célra szeretné betanítani, a számítási identitást legalább a Storage szolgáltatásban található **blob-Adatolvasó** szerepkörrel kell megadni. Ismerje meg, hogyan [állíthatja be a felügyelt identitást a számítási feladatokhoz](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Virtuális hálózatok használata

Alapértelmezés szerint a Azure Machine Learning nem tud kommunikálni tűzfallal vagy virtuális hálózaton belül található Storage-fiókkal.

A Storage-fiókok úgy konfigurálhatók, hogy csak adott virtuális hálózatokról engedélyezzenek hozzáférést, amelyhez további konfigurációk szükségesek, amelyekkel biztosítható, hogy az adatok nem szivárognak ki a hálózaton kívül. Ez ugyanaz, mint a hitelesítő adatokhoz való hozzáférés, a [szükséges konfigurációkra és a virtuális hálózati forgatókönyvek alkalmazására vonatkozó](how-to-access-data.md#virtual-network)tudnivalók. 

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Amikor adattárként regisztrálja a Storage szolgáltatást az Azure-ban, automatikusan létrehozza és regisztrálja az adattárt egy adott munkaterületen. Tekintse át a következő szakaszt: [tárterület-hozzáférési engedélyek](#storage-access-permissions) a szükséges engedélyekkel kapcsolatos útmutatásért, valamint a [virtuális hálózat használata](#work-with-virtual-networks) a virtuális hálózatok mögötti adattárolóhoz való kapcsolódással kapcsolatos részletekért.

A következő kódban figyelje meg, hogy nincs-e hitelesítő paraméter, például,, `sas_token` `account_key` `subscription_id` vagy egyszerű szolgáltatásnév `client_id` . Ez a mulasztás azt jelzi, hogy Azure Machine Learning identitás-alapú adathozzáférést használ a hitelesítéshez. Mivel az adattárolók létrehozása általában interaktív módon történik egy jegyzetfüzetben vagy a studión keresztül, a Azure Active Directory tokent használja az adathozzáférés-hitelesítéshez.

> [!NOTE]
> Az adattár neve csak kisbetűkből, számokból és aláhúzásokból állhat. 

### <a name="azure-blob-container"></a>Azure Blob-tároló

Az Azure Blob-tárolók adattárként való regisztrálásához használja a következőt: [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

A következő kód létrehozza és regisztrálja az `credentialless_blob` adattárt a `ws` munkaterületen, és hozzárendeli a változóhoz `blob_datastore` . Ez az adattár hozzáfér a `my_container_name` blob-tárolóhoz a `my-account-name` Storage-fiókban.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>1. generációs Azure Data Lake Storage

Az 1. generációs Azure Data Lake Storage (1. ADLS) adattárat a [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) használatával regisztrálja az Azure DataLake 1. generációs tárolóhoz csatlakozó adattárt.

A következő kód létrehozza és regisztrálja az `credentialless_adls1` adattárt a `workspace` munkaterületen, és hozzárendeli a változóhoz `adls_dstore` . Ez az adattár fér hozzá a `adls_storage` Azure Data Lake Store Storage-fiókhoz.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>2. generációs Azure Data Lake Storage

Azure Data Lake Storage 2. generációs (ADLS Gen 2) adattár esetében a [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálja az Azure DataLake 2. generációs tárolóhoz csatlakozó adattárt.

A következő kód létrehozza és regisztrálja az `credentialless_adls2` adattárt a `ws` munkaterületen, és hozzárendeli a változóhoz `adls2_dstore` . Ez az adattár fér hozzá a fájlrendszerhez `tabular` a `myadls2` Storage-fiókban.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Adattárolási szolgáltatás használata

[Azure Machine learning adatkészletek](how-to-create-register-datasets.md) használata ajánlott módszer a Storage-ban tárolt adatainak Azure Machine learning használatával történő kezeléséhez. 

Az adatkészletek a gépi tanulási feladatokhoz, például a képzéshez egy lustán kiértékelt adatforrásba csomagolják az adatokat. Emellett az adatkészletekkel az Azure Storage-szolgáltatásokból, például az Azure Blob Storage-ból és az Azure-beli adattavakból is [letölthet vagy csatlakoztathat](how-to-train-with-datasets.md#mount-vs-download) fájlokat a számítási célra.


Az **identitás-alapú adateléréssel rendelkező adatkészletek létrehozásához** a következő lehetőségek állnak rendelkezésre. Ez a típusú adatkészlet-létrehozás a Azure Active Directory tokent alkalmazza az adathozzáférés hitelesítéséhez. 

*  Az adattárolók olyan elérési útjai, amelyek az identitás-alapú adathozzáférést is használják. 
<br>A következő példában `blob_datastore` korábban identitás-alapú adathozzáférés használatával hozták létre.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Az adattár létrehozásának kihagyása és adatkészletek létrehozása közvetlenül a Storage URL-címekről. Ez a funkció jelenleg csak az Azure-blobokat és az 1. és 2. Azure Data Lake Storage generációkat támogatja.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Ha azonban olyan betanítási feladatot küld, amely identitás-alapú adateléréssel létrehozott adatkészletet** használ, a betanítási számítás felügyelt identitását használja az Adathozzáférési hitelesítéshez a Azure Active Directory token helyett. Ebben a forgatókönyvben gondoskodjon arról, hogy a számítás felügyelt identitása a Storage szolgáltatásban legalább **Storage blob-Adatolvasói** szerepkört biztosítson. Ismerje meg, hogyan [állíthatja be a felügyelt identitást a számítási feladatokhoz](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre egy Azure Machine learning-adatkészletet](how-to-create-register-datasets.md).
* [Adatkészletek betanítása](how-to-train-with-datasets.md).
* [Hozzon létre egy adattárolót a kulcs alapú adateléréssel](how-to-access-data.md).
