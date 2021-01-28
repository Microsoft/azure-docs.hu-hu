---
title: A Microsoft Spark segédprogramok bemutatása
description: 'Oktatóanyag: MSSparkutils az Azure szinapszis Analytics-jegyzetfüzetekben'
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: d36086052f4e5719fd17989e3326a4b5728ee3ca
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954293"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>A Microsoft Spark segédprogramok bemutatása

A Microsoft Spark Utilities (MSSparkUtils) egy beépített csomag, amely megkönnyíti a gyakori feladatok elvégzését. A MSSparkUtils használható a fájlrendszerekkel való együttműködéshez, környezeti változók beszerzéséhez és a titkokkal való munkához. A MSSparkUtils a, a és a jegyzetfüzetekben, valamint a `PySpark (Python)` `Scala` szinapszis- `.NET Spark (C#)` folyamatokban érhetők el.

## <a name="pre-requisites"></a>Előfeltételek

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2hoz való hozzáférés konfigurálása 

A szinapszis jegyzetfüzetek az Azure Active Directory (Azure AD) átmenő hozzáférését használják a ADLS Gen2-fiókok eléréséhez. A ADLS Gen2 fiók (vagy mappa) eléréséhez **blob Storage közreműködőnek** kell lennie. 

A szinapszis-folyamatok a munkaterület-identitás (MSI) használatával férnek hozzá a Storage-fiókokhoz. Ha a MSSparkUtils szeretné használni a folyamat tevékenységeiben, a munkaterület identitásának **blob Storage közreműködőnek** kell lennie ahhoz, hogy hozzáférhessen a ADLS Gen2-fiókhoz (vagy mappához).

Az alábbi lépések végrehajtásával győződjön meg arról, hogy az Azure AD és a munkaterület MSI-je hozzáfér a ADLS Gen2 fiókhoz:
1. Nyissa meg a [Azure Portal](https://portal.azure.com/) és az elérni kívánt Storage-fiókot. Navigáljon az elérni kívánt tárolóhoz.
2. Válassza ki a **hozzáférés-vezérlést (iam)** a bal oldali panelen.
3. Rendelje hozzá az **Azure ad-fiókot** és **a munkaterület-identitást** (ugyanaz, mint a munkaterület neve) a Storage-beli **blob adatközreműködői** szerepkörhöz a Storage-fiókban, ha még nincs hozzárendelve. 
4. Kattintson a **Mentés** gombra.

A következő URL-címen keresztül férhet hozzá ADLS Gen2hoz a szinapszis Spark használatával:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Azure-Blob Storage hozzáférésének konfigurálása  

A szinapszis kihasználja a **közös hozzáférésű aláírást (SAS)** az Azure Blob Storage eléréséhez. Ha el szeretné kerülni, hogy az SAS-kulcsok a kódban legyenek kitéve, javasoljuk, hogy hozzon létre egy új társított szolgáltatást a szinapszis munkaterületen az elérni kívánt Azure Blob Storage-fiókkal.

Kövesse az alábbi lépéseket egy új társított szolgáltatás Azure Blob Storage-fiókhoz való hozzáadásához:

1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).
2. Válassza a **kezelés** lehetőséget a bal oldali panelen, és válassza a **társított szolgáltatások** lehetőséget a **külső kapcsolatok** alatt.
3. Az **Azure Blob Storage** az **új társított szolgáltatás** panelen keresse meg a jobb oldalon.
4. Válassza a **Folytatás** lehetőséget.
5. Válassza ki az Azure Blob Storage fiókot a társított szolgáltatás nevének eléréséhez és konfigurálásához. Azt javasoljuk, hogy a **hitelesítési módszerhez** használja a **fiók kulcsát** .
6. Válassza a **Kapcsolódás tesztelése** lehetőséget a beállítások helyességének ellenőrzéséhez.
7. A módosítások mentéséhez válassza az első **Létrehozás** lehetőséget, majd kattintson az **összes közzététele** elemre. 

Az Azure Blob Storage a következő URL-címen keresztül férhet hozzá a szinapszis Sparkhoz:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Íme egy példa a következő kódra:

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>Azure Key Vaulthoz való hozzáférés konfigurálása

Hozzáadhat egy Azure Key Vault társított szolgáltatásként a Szinapszisban található hitelesítő adatok kezeléséhez. A következő lépésekkel adhat hozzá egy Azure Key Vaultt szinapszis-beli társított szolgáltatásként:
1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).
2. Válassza a **kezelés** lehetőséget a bal oldali panelen, és válassza a **társított szolgáltatások** lehetőséget a **külső kapcsolatok** alatt.
3. Keressen **Azure Key Vault** a jobb oldalon található **új társított szolgáltatás** panelen.
4. Válassza ki a Azure Key Vault fiókot a társított szolgáltatás nevének eléréséhez és konfigurálásához.
5. Válassza a **Kapcsolódás tesztelése** lehetőséget a beállítások helyességének ellenőrzéséhez.
6. Válassza a **Létrehozás** először lehetőséget, majd kattintson az **összes közzététele** elemre a módosítás mentéséhez. 

A szinapszis jegyzetfüzetek az Azure Active Directory (Azure AD) átmenő hozzáférését használják a Azure Key Vault eléréséhez. A szinapszis-folyamatok a munkaterület-identitás (MSI) használatával férnek hozzá Azure Key Vaulthoz. Annak érdekében, hogy a kód a jegyzetfüzetben és a szinapszis-folyamatban is működjön, javasoljuk, hogy titkos hozzáférési engedélyt adjon az Azure AD-fiókhoz és a munkaterület-identitáshoz.

A következő lépésekkel titkos hozzáférést biztosíthat a munkaterület-identitáshoz:
1. Nyissa meg a [Azure Portal](https://portal.azure.com/) és az elérni kívánt Azure Key Vault. 
2. Válassza ki a **hozzáférési házirendeket** a bal oldali panelen.
3. Válassza a **hozzáférési házirend hozzáadása** lehetőséget: 
    - Válassza a **kulcs, a titok, & a Tanúsítványkezelő** konfigurációs sablonként lehetőséget.
    - Válassza ki az **Azure ad-fiókot** és **a munkaterület identitását** (ugyanaz, mint a munkaterület neve) a rendszerbiztonsági tag kiválasztása lapon, vagy győződjön meg arról, hogy az már hozzá van rendelve. 
4. Válassza a **kiválasztás** és **Hozzáadás** lehetőséget.
5. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.  

## <a name="file-system-utilities"></a>Fájlrendszer segédprogramok

`mssparkutils.fs` a különböző fájlrendszerek, például a Azure Data Lake Storage Gen2 (ADLS Gen2) és az Azure Blob Storage használatához biztosít segédprogramokat. Győződjön meg arról, hogy megfelelően konfigurálja a [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) és az [Azure Blob Storage](#configure-access-to-azure-blob-storage) elérését.

Az elérhető módszerek áttekintéséhez futtassa az alábbi parancsokat:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Eredmények:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Fájlok listázása
Egy könyvtár tartalmának listázása.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Fájl tulajdonságainak megtekintése
A fájl tulajdonságait adja vissza, beleértve a fájlnevet, a fájl elérési útját, a fájl méretét, valamint azt, hogy a könyvtár és a fájl.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Új könyvtár létrehozása

Létrehozza a megadott könyvtárat, ha az nem létezik, és a szükséges szülő könyvtárakat.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Fájl másolása

Egy fájl vagy könyvtár másolása. Támogatja a másolást a fájlrendszerek között.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Fájl előnézetének tartalma

Az adott fájl első "maxBytes" bájtjait adja vissza UTF-8 kódolású karakterláncként.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Fájl áthelyezése

Áthelyez egy fájlt vagy könyvtárat. Támogatja a különböző fájlrendszerek közötti áthelyezést.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Fájl írása

Egy fájlba írja az adott karakterláncot, amely UTF-8-ban van kódolva.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Tartalom hozzáfűzése fájlba

Hozzáfűzi az adott karakterláncot egy fájlhoz, UTF-8 kódolással.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Fájl vagy könyvtár törlése

Eltávolít egy fájlt vagy könyvtárat.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Hitelesítő adatok segédprogramok

A MSSparkUtils hitelesítő adatok segédprogram segítségével lekérheti a társított szolgáltatások hozzáférési jogkivonatait, és kezelheti Azure Key Vaultban a titkokat. 

A következő parancs futtatásával tekintse át az elérhető módszerek áttekintését:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Eredmény beolvasása:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Jogkivonat beolvasása

Egy adott közönséghez tartozó Azure AD-tokent ad vissza, név (nem kötelező). Az alábbi táblázat felsorolja az összes elérhető célközönség-típust: 

|Célközönség típusa|Célközönség kulcsa|
|--|--|
|Célközönség feloldási típusa|Célközönség|
|Storage-célközönség erőforrása|Storage|
|Adatraktár célközönségének erőforrása|DW|
|Célközönség erőforrásának Data Lake|'AzureManagement'|
|Tár célközönségének erőforrása|Data Lake Store|
|Az Azure OSSDB célközönségének erőforrása|'AzureOSSDB'|
|Azure szinapszis-erőforrás|Szinapszis|
|Erőforrás Azure Data Factory|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Jogkivonat ellenőrzése

Igaz értéket ad vissza, ha a jogkivonat nem járt le.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Kapcsolati karakterlánc vagy a társított szolgáltatás hitelesítő adatainak beolvasása

A társított szolgáltatáshoz tartozó kapcsolati karakterláncot vagy hitelesítő adatokat adja vissza. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Titok beolvasása a munkaterület identitásával

Azure Key Vault titkot ad vissza egy adott Azure Key Vault neve, titkos neve és társított szolgáltatás neve számára a munkaterület-identitás használatával. Győződjön meg arról, hogy megfelelően konfigurálja a hozzáférést [Azure Key Vault](#configure-access-to-azure-key-vault) .

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Titkos kód beolvasása felhasználói hitelesítő adatok használatával

Azure Key Vault titkot ad vissza egy adott Azure Key Vault neve, titkos neve és társított szolgáltatás neve számára a felhasználói hitelesítő adatok használatával. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>Titkos kód létrehozása a munkaterület identitásával

Azure Key Vault titkot helyez el egy adott Azure Key Vault neve, titkos neve és társított szolgáltatás neve számára a munkaterület-identitás használatával. Győződjön meg arról, hogy megfelelően konfigurálja a hozzáférést [Azure Key Vault](#configure-access-to-azure-key-vault) .

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>Titkos kód létrehozása a munkaterület identitásával

Azure Key Vault titkot helyez el egy adott Azure Key Vault neve, titkos neve és társított szolgáltatás neve számára a munkaterület-identitás használatával. Győződjön meg arról, hogy megfelelően konfigurálja a hozzáférést [Azure Key Vault](#configure-access-to-azure-key-vault) .

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>Titkos kód használata felhasználói hitelesítő adatokkal

Azure Key Vault titkos kulcsot helyez el egy adott Azure Key Vault neve, titkos neve és társított szolgáltatás neve számára a felhasználói hitelesítő adatok használatával. 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>Titkos kód használata felhasználói hitelesítő adatokkal

Azure Key Vault titkos kulcsot helyez el egy adott Azure Key Vault neve, titkos neve és társított szolgáltatás neve számára a felhasználói hitelesítő adatok használatával. 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>Környezeti segédeszközök 

Futtassa az alábbi parancsokat az elérhető módszerek áttekintéséhez:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Eredmény beolvasása:
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Felhasználónév beolvasása

Az aktuális felhasználónevet adja vissza.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Felhasználói azonosító lekérése

Az aktuális felhasználói azonosítót adja vissza.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Munkakör-azonosító beolvasása

A AZONOSÍTÓJÚ feladatot adja vissza.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Munkaterület nevének beolvasása

A munkaterület nevét adja vissza.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Készlet nevének lekérése

A Spark-készlet nevét adja vissza.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Fürt AZONOSÍTÓjának beolvasása

Az aktuális fürt AZONOSÍTÓját adja vissza.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

- [Tekintse meg a szinapszis-minta jegyzetfüzeteket](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Rövid útmutató: Apache Spark-készlet létrehozása az Azure szinapszis Analyticsben webes eszközök használatával](../quickstart-apache-spark-notebook.md)
- [Az Azure szinapszis Analytics Apache Spark](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
