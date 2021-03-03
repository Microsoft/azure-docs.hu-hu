---
title: Biztonságos hozzáférés hitelesítő adatai az Azure szinapszis Analytics Apache Spark társított szolgáltatásaival
description: Ez a cikk az Azure szinapszis Analytics Apache Sparkának biztonságos integrálását ismerteti más szolgáltatásokkal a társított szolgáltatások és a jogkivonat-függvénytár használatával
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: martinle
ms.reviewer: nirav
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: a588b37b270917524453419619fdad6f88f92338
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693577"
---
# <a name="secure-credentials-with-linked-services-using-the-tokenlibrary"></a>Hitelesítő adatok biztonságossá tétele a társított szolgáltatásokkal a TokenLibrary használatával

A külső forrásokból származó adatokhoz való hozzáférés gyakori minta. Ha a külső adatforrás engedélyezi a névtelen hozzáférést, akkor előfordulhat, hogy a kapcsolatot hitelesítő adatokkal, titkos kulccsal vagy kapcsolati karakterlánccal kell védenie.  

A szinapszis a Azure Active Directory (HRE) továbbítót használja alapértelmezetten az erőforrások közötti hitelesítéshez.  Ha más hitelesítő adatokat használó erőforráshoz kell csatlakoznia, közvetlenül a TokenLibrary kell használnia.  A TokenLibrary leegyszerűsíti az SAS-jogkivonatok, a HRE-tokenek, a kapcsolati karakterláncok és a társított szolgáltatásban vagy egy Azure Key Vault tárolt titkos kódok lekérésének folyamatát.

Azure Key Vault titkos kulcsainak beolvasása során javasoljuk, hogy hozzon létre egy társított szolgáltatást a Azure Key Vault.  Győződjön meg arról, hogy a szinapszis-munkaterület felügyelt szolgáltatásának identitása (MSI) rendelkezik a Azure Key Vault titkos Get jogosultságokkal.  A szinapszis hitelesíti magát a Azure Key Vault a szinapszis munkaterület felügyelt szolgáltatásának identitását használva. Ha társított szolgáltatás nélkül csatlakozik közvetlenül Azure Key Vaulthoz, akkor a felhasználó Azure Active Directory hitelesítő adatait fogja használni.

További információ: [társított szolgáltatások](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="usage"></a>Használat

### <a name="tokenlibraryhelp"></a>TokenLibrary. Help ()
Ez a függvény a TokenLibrary tartozó súgó dokumentációját jeleníti meg.

::: zone pivot = "programming-language-scala"

```scala
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
TokenLibrary.help()
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
Console.WriteLine(TokenLibrary.help());
```

::: zone-end

## <a name="tokenlibrary-for-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 TokenLibrary

#### <a name="adls-gen2-primary-storage"></a>Elsődleges tároló ADLS Gen2

Az elsődleges Azure Data Lake Storage fájljainak elérése alapértelmezés szerint Azure Active Directory továbbítót használ, és nem igényli a TokenLibrary explicit használatát.

::: zone pivot = "programming-language-scala"

```scala
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")
display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')
display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-linked-services"></a>ADLS Gen2 Storage a társított szolgáltatásokkal

A szinapszis integrált társított szolgáltatásokat biztosít a Azure Data Lake Storage Gen2hoz való csatlakozáskor.  A társított szolgáltatások úgy konfigurálhatók, hogy a **fiók kulcsa**, a **szolgáltatásnév**, a **felügyelt identitás** vagy a **hitelesítő adatok** használatával hitelesítsék magukat.

Ha a társított szolgáltatás hitelesítési módszere a **fiók kulcsa**, a társított szolgáltatás a megadott Storage-fiók kulcsával fogja hitelesíteni a hitelesítést, kérjen egy sas-kulcsot, és automatikusan alkalmazza a tárolási kérelemre a **LinkedServiceBasedSASProvider** használatával.

::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

Ha a társított szolgáltatás hitelesítési módszere **felügyelt identitás** vagy **egyszerű szolgáltatásnév**, akkor a társított szolgáltatás a felügyelt identitást vagy egyszerű szolgáltatásnevet használja a **LinkedServiceBasedTokenProvider** -szolgáltatónál.  


::: zone pivot = "programming-language-scala"

```scala
val sc = spark.sparkContext
spark.conf.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
spark.conf.set("fs.azure.account.oauth.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider") 
val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# Python code
spark.conf.set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedTokenProvider")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>')

df.show()
```

::: zone-end

#### <a name="adls-gen2-storage-without-linked-services"></a>ADLS Gen2 Storage (társított szolgáltatások nélkül)

Ha közvetlenül egy SAS-kulccsal csatlakozik ADLS Gen2 tárolóhoz, használja a **ConfBasedSASProvider** , és adja meg az SAS-kulcsot a **Spark. Storage. szinapszis. sas** konfigurációs beállításhoz.

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark

spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.ConfBasedSASProvider")
spark.conf.set("spark.storage.synapse.sas", "<SAS KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

#### <a name="adls-gen2-storage-with-azure-key-vault"></a>ADLS Gen2 Storage Azure Key Vault

Kapcsolódjon ADLS Gen2 tárterülethez Azure Key Vault titkos kulcsban tárolt SAS-token használatával.  

::: zone pivot = "programming-language-scala"

```scala
%%spark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>")

display(df.limit(10))
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
spark.conf.set("fs.azure.account.auth.type", "SAS")
spark.conf.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.AkvBasedSASProvider")
spark.conf.set("spark.storage.synapse.akv", "<AZURE KEY VAULT NAME>")
spark.conf.set("spark.storage.akv.secret", "<SECRET KEY>")

df = spark.read.csv('abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<FILE PATH>')

display(df.limit(10))
```

::: zone-end

## <a name="tokenlibrary-for-other-linked-services"></a>TokenLibrary más társított szolgáltatásokhoz

Ha más társított szolgáltatásokhoz szeretne csatlakozni, közvetlenül is meghívhatja a TokenLibrary.

#### <a name="getconnectionstring"></a>getConnectionString()

 A kapcsolati karakterlánc lekéréséhez használja a **getConnectionString** függvényt, és adja át a **társított szolgáltatás nevét**.

::: zone pivot = "programming-language-scala"

```scala
%%spark
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
%%pyspark
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
%%csharp
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

::: zone-end

#### <a name="getconnectionstringasmap"></a>getConnectionStringAsMap()

A getConnectionStringAsMap egy, a Scala és a Python szolgáltatásban elérhető segítő függvény, amely a _Key = Value_ pár adott értékeit elemzi a kapcsolódási karakterláncban, például:

_`DefaultEndpointsProtocol=https;AccountName=<ACCOUNT NAME>;AccountKey=<ACCOUNT KEY>`_

használja a **getConnectionStringAsMap** függvényt, és adja át a kulcsot az érték visszaadásához.  Példa a fenti kapcsolatok karakterláncára: 

_**TokenLibrary. getConnectionStringAsMap ("DefaultEndpointsProtocol")**_

visszatérés

**_https_**

::: zone pivot = "programming-language-scala"

```scala
// Linked services can be used for storing and retrieving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```
::: zone-end

::: zone pivot = "programming-language-python"

```python
# Linked services can be used for storing and retrieving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

::: zone-end

#### <a name="getsecret"></a>getSecret()

Azure Key Vaultból tárolt titkos kód lekéréséhez azt javasoljuk, hogy hozzon létre egy társított szolgáltatást a Azure Key Vault a szinapszis munkaterületen belül. A szinapszis munkaterület által felügyelt szolgáltatás identitását meg kell adni a **Azure Key Vault titkos** kódokra vonatkozó engedélyekkel.  A társított szolgáltatás a felügyelt szolgáltatás identitásával csatlakozik Azure Key Vault szolgáltatáshoz a titkos kulcs lekéréséhez.  Ellenkező esetben a közvetlenül a Azure Key Vaulthoz való csatlakozás a felhasználó Azure Active Directory (HRE) hitelesítő adatait fogja használni.  Ebben az esetben a felhasználónak meg kell adnia a Get Secret (titkos) engedélyeket a Azure Key Vault-ban.

`TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>" [, <LINKED SERVICE NAME>])`

Azure Key Vault titkos kulcs lekéréséhez használja a **TokenLibrary. getSecret ()** függvényt.

::: zone pivot = "programming-language-scala"

```scala
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
println(connectionString)
```

::: zone-end

::: zone pivot = "programming-language-python"

```python
import sys
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

connection_string = token_library.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>")
print(connection_string)
```

::: zone-end

::: zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.getSecret("<AZURE KEY VAULT NAME>", "<SECRET KEY>", "<LINKED SERVICE NAME>");
Console.WriteLine(connectionString);
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

- [Írás dedikált SQL-készletbe](./synapse-spark-sql-pool-import-export.md)
