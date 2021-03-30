---
title: Azure Data Lake Analytics kezelése a Python használatával
description: Ez a cikk azt ismerteti, hogyan használható a Python a Data Lake Analytics fiókok, adatforrások, felhasználók és & feladatok kezelésére.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/08/2018
ms.custom: devx-track-python
ms.openlocfilehash: fda09047d138e404eeca87ed3eba9bb72ff62d56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220227"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Azure Data Lake Analytics kezelése a Python használatával
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk bemutatja, hogyan kezelheti Azure Data Lake Analytics fiókokat, adatforrásokat, felhasználókat és feladatokat a Python használatával.

## <a name="supported-python-versions"></a>Támogatott Python-verziók

* A Python 64-bites verzióját használja.
* A **[Python.org downloads](https://www.python.org/downloads/)** címen található standard Python-disztribúciót is használhatja. 
* Számos fejlesztő úgy találja, hogy kényelmes az **[anaconda Python-eloszlás](https://www.anaconda.com/download/)** használatát.  
* Ez a cikk a Python 3,6-es verziójának a standard Python-disztribúcióból való használatával lett írva.

## <a name="install-azure-python-sdk"></a>Az Azure Python SDK telepítése

Telepítse a következő modulokat:

* Az **Azure-mgmt-Resource** modul más Azure-modulokat is tartalmaz Active Directoryekhez stb.
* Az **Azure-datalake-Store** modul tartalmazza a Azure Data Lake Store fájlrendszeri műveleteket. 
* Az **Azure-mgmt-datalake-Store** modul tartalmazza a Azure Data Lake Store Fiókkezelés műveleteit.
* Az **Azure-mgmt-datalake-Analytics** modul tartalmazza a Azure Data Lake Analytics műveleteket. 

Először győződjön meg arról, hogy rendelkezik a legújabb `pip` paranccsal a következő parancs futtatásával:

```console
python -m pip install --upgrade pip
```

Ez a dokumentum a használatával lett írva `pip version 9.0.1` .

A következő `pip` parancsokkal telepítheti a modulokat a parancssorból:

```console
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Új Python-szkript létrehozása

Illessze be a következő kódot a szkriptbe:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Futtassa ezt a szkriptet annak ellenőrzéséhez, hogy a modulok importálhatók-e.

## <a name="authentication"></a>Hitelesítés

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktív felhasználói hitelesítés előugró ablakban

Ez a metódus nem támogatott.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktív felhasználói hitelesítés egy eszköz kódjával

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Nem interaktív hitelesítés SPI-vel és titkos kulccsal

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Nem interaktív hitelesítés API-val és tanúsítvánnyal

Ez a metódus nem támogatott.

## <a name="common-script-variables"></a>Gyakori parancsfájl-változók

Ezeket a változókat a minták használják.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Az ügyfelek létrehozása

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Először hozzon létre egy áruházbeli fiókot.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Ezután hozzon létre egy ADLA-fiókot, amely ezt a tárolót használja.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Feladatok elküldése

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Várakozás a feladatok befejezésére

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Folyamatok és ismétlődések listázása
Attól függően, hogy a feladatokhoz van-e csatolva folyamat vagy ismétlődési metaadatok, a folyamatokat és az ismétlődéseket is listázhatja.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Számítási házirendek kezelése

A DataLakeAnalyticsAccountManagementClient objektum olyan metódusokat biztosít, amelyekkel felügyelhető a Data Lake Analytics fiók számítási házirendjeinek kezelése.

### <a name="list-compute-policies"></a>Számítási szabályzatok listázása

A következő kód lekéri egy Data Lake Analytics fiók számítási házirendjeinek listáját.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Új számítási szabályzat létrehozása

A következő kód egy új számítási szabályzatot hoz létre egy Data Lake Analytics-fiókhoz, amely a megadott felhasználó számára elérhető maximális értéket adja meg 50-re, a feladat minimális prioritását pedig 250-re állítja.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Következő lépések

- Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
- Felügyeleti feladatokhoz lásd: [Azure Data Lake Analytics kezelése Azure Portal használatával](data-lake-analytics-manage-use-portal.md).

