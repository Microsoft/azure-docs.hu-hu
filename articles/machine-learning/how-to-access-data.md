---
title: Kapcsolódás az Azure Storage Services szolgáltatásához
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az adattárakat az Azure Storage-szolgáltatásokhoz való biztonságos kapcsolódásra Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 11/03/2020
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 78b7bab204a08b474ea3c5cf5c2f7735c019a9c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519928"
---
# <a name="connect-to-storage-services-on-azure"></a>Kapcsolódás az Azure Storage Services szolgáltatásához

Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure-beli adattárolási szolgáltatásokhoz Azure Machine Learning adattárral és a [PYTHON SDK](/python/api/overview/azure/ml/intro)-val Azure Machine learning.

Az adattárolók biztonságosan csatlakoznak a Storage szolgáltatáshoz az Azure-ban anélkül, hogy hitelesítő adatokat kellene megadnia, és az eredeti adatforrásának integritását kockáztatja. A kapcsolati adatokat, például az előfizetési AZONOSÍTÓját és a jogkivonat-engedélyezést a munkaterülethez társított [Key Vault](https://azure.microsoft.com/services/key-vault/) tárolják, így biztonságosan hozzáférhet a tárolóhoz anélkül, hogy a parancsfájlokban rögzített kódokat kellene megtennie. [Ezekhez az Azure Storage-megoldásokhoz kapcsolódó adattárakat](#matrix)is létrehozhat.

Ha szeretné megismerni, hogy az adattárolók hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférésről](concept-data.md#data-workflow) szóló cikket.

Az alacsony kódú élményért lásd: az adattárolók [létrehozása és regisztrálása a Azure Machine learning Studio](how-to-connect-data-ui.md#create-datastores)használatával.

>[!TIP]
> Ez a cikk azt feltételezi, hogy a tárolási szolgáltatáshoz hitelesítő adatokon alapuló hitelesítési hitelesítő adatokkal szeretne csatlakozni, például egy egyszerű szolgáltatásnév vagy egy közös hozzáférésű aláírás (SAS) token. Ne feledje, hogy ha a hitelesítő adatok regisztrálva vannak az adattárolókban, a munkaterület- *olvasó* szerepkörrel rendelkező felhasználók beolvashatják ezeket a hitelesítő adatokat. [További információ a munkaterület- *olvasó* szerepkörről.](how-to-assign-roles.md#default-roles) <br><br>Ha ez problémát jelent, Ismerje meg, hogyan [csatlakozhat a Storage-szolgáltatásokhoz identitás-alapú hozzáféréssel](how-to-identity-based-data-access.md). <br><br>Ez a funkció egy [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verzió, és bármikor megváltozhat. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

- Egy [támogatott tárolási típusú](#matrix)Azure Storage-fiók.

- A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro).

- Egy Azure Machine Learning-munkaterület.
  
  [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md) , vagy használjon egy meglévőt a Python SDK-n keresztül. 

    Importálja a `Workspace` és az `Datastore` osztályt, és töltse be az előfizetési adatokat a fájlból `config.json` a függvény használatával `from_config()` . Ez alapértelmezés szerint az aktuális könyvtárban keresi a JSON-fájlt, de megadhat egy elérésiút-paramétert is, amely a fájlra mutat `from_config(path="your/file/path")` .

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    Amikor létrehoz egy munkaterületet, az Azure Blob-tároló és az Azure-fájlmegosztás automatikusan adattárként lesz regisztrálva a munkaterületen. Ezek neve `workspaceblobstore` és `workspacefilestore` , illetve. A a `workspaceblobstore` munkaterület-összetevők és a Machine learning-kísérletek naplófájljainak tárolására szolgál. Az **alapértelmezett adattárként** is be van állítva, és nem törölhető a munkaterületről. A a `workspacefilestore` [számítási példányon](./concept-compute-instance.md#accessing-files)keresztül engedélyezve lévő jegyzetfüzetek és R-parancsfájlok tárolására szolgál.
    
    > [!NOTE]
    > Azure Machine Learning Designer automatikusan létrehoz egy **azureml_globaldatasets** nevű adattárat, amikor megnyit egy mintát a tervező kezdőlapján. Ez az adattár csak minta adatkészleteket tartalmaz. A bizalmas adatokhoz való hozzáféréshez **ne** használja ezt az adattárt.

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>Támogatott adattárolási szolgáltatások típusai

Az adattárolók jelenleg támogatják a kapcsolódási adatok tárolását a következő mátrixban felsorolt tárolási szolgáltatásokhoz. 

> [!TIP]
> A nem **támogatott tárolási megoldások esetében**, valamint az adatforgalomnak a ml-kísérletek során történő megtakarítása érdekében [Helyezze át az adatait](#move) egy támogatott Azure Storage-megoldásba. 

| Tárolási &nbsp; típus | Hitelesítés &nbsp; típusa | [Azure &nbsp; Machine &nbsp; learning Studio](https://ml.azure.com/) | [Az Azure &nbsp; Machine &nbsp; learning &nbsp; Python SDK](/python/api/overview/azure/ml/intro) |  [Azure &nbsp; Machine &nbsp; learning parancssori felület](reference-azure-machine-learning-cli.md) | [Azure &nbsp; Machine &nbsp; learning – &nbsp; REST API](/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Azure &nbsp; blob &nbsp; Storage](../storage/blobs/storage-blobs-overview.md)| Fiókkulcs <br> SAS-jogkivonat | ✓ | ✓ | ✓ |✓ |✓
[Azure- &nbsp; fájlmegosztás &nbsp;](../storage/files/storage-files-introduction.md)| Fiókkulcs <br> SAS-jogkivonat | ✓ | ✓ | ✓ |✓|✓
[Azure &nbsp; Data Lake &nbsp; Storage, &nbsp; 1. generációs](../data-lake-store/index.yml)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; Data Lake &nbsp; Storage, &nbsp; 2. generációs](../storage/blobs/data-lake-storage-introduction.md)| Szolgáltatásnév| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; SQL &nbsp; Database](../azure-sql/database/sql-database-paas-overview.md)| SQL-hitelesítés <br>Szolgáltatásnév| ✓ | ✓ | ✓ |✓|
[Azure &nbsp; PostgreSQL](../postgresql/overview.md) | SQL-hitelesítés| ✓ | ✓ | ✓ |✓|
[MySQL-hez készült Azure- &nbsp; adatbázis &nbsp; &nbsp;](../mysql/overview.md) | SQL-hitelesítés|  | ✓* | ✓* |✓*|
[Databricks &nbsp; fájlrendszer &nbsp;](/azure/databricks/data/databricks-file-system)| Nincs hitelesítés | | ✓** | ✓ ** |✓** |

\*A MySQL-t csak a folyamat [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) támogatja<br />
\*\*A Databricks csak a folyamat [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep) támogatott


### <a name="storage-guidance"></a>Storage – útmutató

Javasoljuk, hogy hozzon létre egy adattárt egy [Azure Blob-tárolóhoz](../storage/blobs/storage-blobs-introduction.md). Mind a standard, mind a Premium Storage elérhető a Blobok számára. Habár a Premium Storage drágább, a gyorsabb átviteli sebesség növelheti a képzések sebességét, különösen akkor, ha egy nagyméretű adathalmazra van betanítva. További információ a Storage-fiókok költségéről: az [Azure díjszabási kalkulátora](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service).

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) az Azure Blob Storage-ra épül, és nagyvállalati Big Data elemzésre lett tervezve. Data Lake Storage Gen2 alapvető része egy [hierarchikus névtér](../storage/blobs/data-lake-storage-namespace.md) hozzáadása a blob Storage-hoz. A hierarchikus névtér objektumokat vagy fájlokat rendez a címtárak hierarchiájában a hatékony adateléréshez.

## <a name="storage-access-and-permissions"></a>Tárterület-hozzáférés és engedélyek

Az Azure Storage szolgáltatáshoz való biztonságos kapcsolódás biztosításához Azure Machine Learning megköveteli, hogy rendelkezzen a megfelelő adattároló-tároló eléréséhez szükséges engedéllyel. Ez a hozzáférés az adattár regisztrálásához használt hitelesítési hitelesítő adatoktól függ. 

### <a name="virtual-network"></a>Virtuális hálózat 

Alapértelmezés szerint a Azure Machine Learning nem tud kommunikálni tűzfallal vagy virtuális hálózaton belül található Storage-fiókkal. Ha az adattároló-fiók egy **virtuális hálózaton** található, további konfigurációs lépések szükségesek ahhoz, hogy Azure Machine learning hozzáférjen az adataihoz. 

> [!NOTE]
> Ez az útmutató az [identitás-alapú adathozzáférés (előzetes verzió) használatával létrehozott](how-to-identity-based-data-access.md)adattárolók esetében is érvényes. 

A **PYTHON SDK-felhasználók** számára, hogy a számítási célra szolgáló oktatóanyagon keresztül hozzáférjenek az adatokhoz, a számítási célnak a tároló ugyanazon virtuális hálózatán és alhálózatán belül kell lennie.  

**Azure Machine learning Studio-felhasználók számára** számos funkció támaszkodik az adatok adatkészletből való beolvasására. például adatkészlet-előnézetek, profilok és automatizált gépi tanulás. Ahhoz, hogy ezek a funkciók a virtuális hálózatok mögötti tárolóval működjenek, a [Studióban egy munkaterület által felügyelt identitás](how-to-enable-studio-virtual-network.md) használatával Azure Machine learning a virtuális hálózaton kívülről is elérheti a Storage-fiókot. 

A Azure Machine Learning a virtuális hálózaton kívüli ügyfelektől érkező kéréseket is fogadhat. Annak biztosítása érdekében, hogy a szolgáltatástól adatokat kérő entitás biztonságos legyen, [állítsa be az Azure Private-hivatkozást a munkaterületre](how-to-configure-private-link.md).

### <a name="access-validation"></a>Hozzáférés ellenőrzése

**A kezdeti adattár-létrehozási és regisztrációs folyamat részeként** Azure Machine learning automatikusan ellenőrzi, hogy a mögöttes tárolási szolgáltatás létezik-e, és hogy a felhasználó által megadott rendszerbiztonsági tag (Felhasználónév, szolgáltatásnév vagy sas-jogkivonat) hozzáfér-e a megadott tárolóhoz.

Az **adattár létrehozása után** ezt az ellenőrzést csak olyan metódusok hajtják végre, amelyek hozzáférést igényelnek a mögöttes tárolóhoz, **nem** minden alkalommal, amikor az adattár-objektumokat lekérik. Például az ellenőrzés akkor történik meg, ha le szeretné tölteni a fájlokat az adattárból. Ha azonban csak módosítani szeretné az alapértelmezett adattárat, akkor az érvényesítés nem történik meg.

A mögöttes tárolási szolgáltatáshoz való hozzáférésének hitelesítéséhez megadhatja a fiók kulcsát, a közös hozzáférési aláírások (SAS) jogkivonatait vagy a szolgáltatásnevet a `register_azure_*()` létrehozni kívánt adattár-típus megfelelő metódusában. A [tárolási típus mátrixa](#matrix) felsorolja az egyes adattár-típusoknak megfelelő támogatott hitelesítési típusokat.

Megtalálhatja a fiók kulcsát, az SAS-tokent és az egyszerű szolgáltatásnév adatait a [Azure Portal](https://portal.azure.com).

* Ha a hitelesítéshez fiókot vagy SAS-tokent szeretne használni, válassza ki a **Storage-fiókok** lehetőséget a bal oldali ablaktáblán, és válassza ki a regisztrálni kívánt Storage-fiókot. 
  * Az **áttekintő** oldal olyan információkat tartalmaz, mint például a fióknév, a tároló és a fájlmegosztás neve. 
      1. A fiókok kulcsai lapon lépjen a **hozzáférési kulcsok** elemre a **Beállítások** ablaktáblán. 
      1. SAS-tokenek esetén a beállítások ablaktáblán válassza a **közös hozzáférésű aláírások** **lehetőséget** .

* Ha egy egyszerű szolgáltatásnév használatát tervezi hitelesítésre, lépjen a **Alkalmazásregisztrációk** , és válassza ki a használni kívánt alkalmazást. 
    * A megfelelő **áttekintő** oldal tartalmazni fogja a szükséges információkat, például a bérlő azonosítóját és az ügyfél-azonosítót.

> [!IMPORTANT]
> * Ha módosítania kell egy Azure Storage-fiók (fiók vagy SAS-token) hozzáférési kulcsait, akkor ügyeljen arra, hogy szinkronizálja az új hitelesítő adatokat a munkaterülettel és a hozzá csatlakoztatott adattárakkal. Útmutató [a frissített hitelesítő adatok szinkronizálásához](how-to-change-storage-access-key.md). 
### <a name="permissions"></a>Engedélyek

Az Azure Blob Container és Azure Data Lake 2. generációs tárolók esetében ellenőrizze, hogy a hitelesítési hitelesítő adatok rendelkeznek-e a **Storage blob Adatolvasóval** . További információ a [Storage blob-Adatolvasóról](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). A fiók SAS-tokenje alapértelmezés szerint nem rendelkezik engedélyekkel. 
* Az adatok **olvasási hozzáféréséhez** a hitelesítő adatoknak legalább listáját és olvasási engedélyeket kell tartalmazniuk a tárolók és objektumok számára. 

* Az **adatírások eléréséhez** írási és hozzáadási engedélyekre is szükség van.

<a name="python"></a>

## <a name="create-and-register-datastores"></a>Adattárolók létrehozása és regisztrálása

Ha az Azure Storage-megoldást adattárként regisztrálja, automatikusan létrehozza és regisztrálja az adattárt egy adott munkaterületen. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

Ebben a szakaszban példákat talál egy adattár létrehozására és regisztrálására a Python SDK-val a következő tárolási típusoknál. A példákban szereplő paraméterek az adattár létrehozásához és regisztrálásához **szükséges paraméterek** .

* [Azure Blob-tároló](#azure-blob-container)
* [Azure-fájlmegosztás](#azure-file-share)
* [2. generációs Azure Data Lake Storage](#azure-data-lake-storage-generation-2)

 Más támogatott tárolási szolgáltatásokhoz tartozó adattárolók létrehozásához tekintse meg a [megfelelő `register_azure_*` módszerek dokumentációját](/python/api/azureml-core/azureml.core.datastore.datastore#methods).

Ha alacsony szintű felhasználói élményt szeretne, tekintse meg az [Azure Machine learning Studióval való kapcsolódás](how-to-connect-data-ui.md)az adatkapcsolathoz című témakört.
>[!IMPORTANT]
> Ha törli a regisztrációt, és újra regisztrálja az adattárolót ugyanazzal a névvel, és a művelet meghiúsul, előfordulhat, hogy a munkaterület Azure Key Vault nem rendelkezik a helyreállítható törléssel. Alapértelmezés szerint a Soft-delete engedélyezve van a munkaterület által létrehozott Key Vault-példányon, de ez nem engedélyezhető, ha meglévő kulcstartót használt, vagy pedig az október 2020. előtt létrehozott munkaterülettel rendelkezik. További információ a helyreállítható törlés engedélyezéséről: [a meglévő kulcstartó bekapcsolásának bekapcsolása]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault).

> [!NOTE]
> Az adattár neve csak kisbetűkből, számokból és aláhúzásokból állhat. 

### <a name="azure-blob-container"></a>Azure Blob-tároló

Az Azure Blob-tárolók adattárként való regisztrálásához használja a következőt: [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

A következő kód létrehozza és regisztrálja az `blob_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a `my-container-name` blob-tárolóhoz a `my-account-name` Storage-fiókban a megadott fiók-hozzáférési kulcs használatával. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

### <a name="azure-file-share"></a>Azure-fájlmegosztás

Azure-fájlmegosztás adattárként való regisztrálásához használja a következőt: [`register_azure_file_share()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) . 

A következő kód létrehozza és regisztrálja az `file_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a `my-fileshare-name` fájlmegosztást a `my-account-name` Storage-fiókban a megadott fiók-hozzáférési kulcs használatával. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

### <a name="azure-data-lake-storage-generation-2"></a>2. generációs Azure Data Lake Storage

Egy Azure Data Lake Storage 2. generációs (ADLS Gen 2) adattár esetében a [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) használatával regisztrálja az Azure DataLake Gen 2 tárolóhoz csatlakoztatott hitelesítő adatokat az [egyszerű szolgáltatás engedélyeivel](../active-directory/develop/howto-create-service-principal-portal.md).  

Az egyszerű szolgáltatás használatához [regisztrálnia](../active-directory/develop/app-objects-and-service-principals.md) kell az alkalmazást, és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) vagy a hozzáférés-vezérlési listák (ACL) használatával kell megadnia az egyszerű szolgáltatás adatelérését. További információ a [2. generációs ADLS-hez beállított hozzáférés-vezérlésről](../storage/blobs/data-lake-storage-access-control-model.md). 

A következő kód létrehozza és regisztrálja az `adlsgen2_datastore_name` adattárt a `ws` munkaterületen. Ez az adattár hozzáfér a fájlrendszerhez a `test` `account_name` Storage-fiókban a megadott egyszerű szolgáltatás hitelesítő adataival. Tekintse át a [tárolási hozzáférési & engedélyek](#storage-access-and-permissions) szakaszt a virtuális hálózati forgatókönyvekkel kapcsolatos útmutatásért és a szükséges hitelesítő adatok megkereséséhez. 

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```



## <a name="create-datastores-with-other-azure-tools"></a>Adattárolók létrehozása más Azure-eszközökkel
Az adattárolók a Python SDK-val és a Studióval való létrehozása mellett Azure Resource Manager sablonokat vagy a Azure Machine Learning VS Code bővítményt is használhatja. 

<a name="arm"></a>
### <a name="azure-resource-manager"></a>Azure Resource Manager

Az adattárolók létrehozásához több sablon is [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) használható.

További információ a sablonok használatáról: [Azure Resource Manager sablon használata munkaterületek létrehozásához Azure Machine learning számára](how-to-create-workspace-template.md).

### <a name="vs-code-extension"></a>VS Code-bővítmény

Ha az adattárolókat a Azure Machine Learning VS Code bővítménnyel szeretné létrehozni és felügyelni, látogasson el a [vs Code erőforrás-kezelési útmutató](how-to-manage-resources-vscode.md#datastores) című témakörre, ahol további információt talál.
<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>Adattárolók használata

Az adattár létrehozása után [hozzon létre egy Azure Machine learning adatkészletet](how-to-create-register-datasets.md) az adataival való interakcióhoz. Az adatkészletek a gépi tanulási feladatokhoz, például a képzéshez egy lustán kiértékelt adatforrásba csomagolják az adatokat. 

Az adatkészletek segítségével bármilyen formátumú fájlokat [tölthet le vagy csatlakoztathat](how-to-train-with-datasets.md#mount-vs-download) az Azure Storage-szolgáltatásokból a számítási célra szolgáló modell betanításához. [További információ az adatkészletekkel rendelkező ml-modellek betanításáról](how-to-train-with-datasets.md).

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>Adattárolók beolvasása a munkaterületről

Az aktuális munkaterületen regisztrált adott adattár beszerzéséhez használja a [`get()`](/python/api/azureml-core/azureml.core.datastore%28class%29#get-workspace--datastore-name-) statikus metódust az `Datastore` osztályban:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
Egy adott munkaterülethez regisztrált adattárolók listájának lekéréséhez használhatja a (z) [`datastores`](/python/api/azureml-core/azureml.core.workspace%28class%29#datastores) tulajdonságot egy munkaterület-objektumon:

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

A munkaterület alapértelmezett adattárának beszerzéséhez használja ezt a sort:

```Python
datastore = ws.get_default_datastore()
```
Az alapértelmezett adattár a következő kóddal is módosítható. Ez a képesség csak az SDK-n keresztül támogatott. 

```Python
 ws.set_default_datastore(new_default_datastore)
```

## <a name="access-data-during-scoring"></a>Az adathozzáférés a pontozás során

A Azure Machine Learning számos módszert kínál a modellek pontozási célra való használatára. A módszerek némelyike nem biztosít hozzáférést az adattárolóhoz. A következő táblázat segítségével megtudhatja, hogy mely módszerek lehetővé teszik az adattárolók elérését a pontozás során:

| Metódus | Adattár-hozzáférés | Description |
| ----- | :-----: | ----- |
| [Kötegelt előrejelzés](./tutorial-pipeline-batch-scoring-classification.md) | ✔ | Előrejelzések készítése aszinkron módon nagy mennyiségű adattal. |
| [Webszolgáltatás](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése webszolgáltatásként. |
| [Azure IoT Edge modul](how-to-deploy-and-where.md) | &nbsp; | Modellek üzembe helyezése IoT Edge eszközökön. |

Olyan helyzetekben, amikor az SDK nem biztosít hozzáférést az adattárolók számára, létrehozhat egyéni kódokat a megfelelő Azure SDK használatával az adateléréshez. A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) például egy ügyféloldali kódtár, amely a blobokban vagy fájlokban tárolt adateléréshez használható.

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>Az adatáthelyezés támogatott Azure Storage-megoldásokra

Azure Machine Learning támogatja az Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database és Azure Database for PostgreSQL adatainak elérését. Ha nem támogatott tárterületet használ, javasoljuk, hogy [Azure Data Factory és az alábbi lépések](../data-factory/quickstart-create-data-factory-copy-data-tool.md)segítségével helyezze át az adatait a támogatott Azure Storage-megoldásokra. Az adatforgalom a támogatott tárolóba való áthelyezésével megtakaríthatja a kimenő adatforgalom költségeit a gépi tanulási kísérletek során. 

Azure Data Factory a hatékony és rugalmas adatátvitelt több mint 80 előre összeépített összekötővel biztosítja, felár nélkül. Ezek az összekötők közé tartoznak az Azure adatszolgáltatások, a helyszíni adatforrások, az Amazon S3 és a vöröseltolódás, valamint a Google BigQuery.

## <a name="next-steps"></a>Következő lépések

* [Azure Machine learning-adatkészlet létrehozása](how-to-create-register-datasets.md)
* [Modell betanítása](how-to-set-up-training-targets.md)
* [Modell üzembe helyezése](how-to-deploy-and-where.md)