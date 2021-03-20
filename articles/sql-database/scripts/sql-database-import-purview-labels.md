---
title: Azure SQL-adatai osztályozása az Azure-beli hatáskörébe címkék használatával
description: A besorolás importálása az Azure hatáskörébe a Azure SQL Database és az Azure Synpase Analytics szolgáltatásban
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714901"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Azure SQL-adatai osztályozása az Azure-beli hatáskörébe címkék használatával
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

Ez a dokumentum ismerteti, hogyan adhat hozzá Azure-beli hatáskörébe címkéket a Azure SQL Database és az Azure szinapszis Analytics (korábbi nevén SQL DW) használatával.

## <a name="create-an-application"></a>Alkalmazás létrehozása

1. A Azure Portal nyissa meg a **Azure Active Directory**.
2. A **kezelés** területen válassza az **alkalmazás regisztrálása** lehetőséget.
3. Hozzon létre egy új Azure Active Directory alkalmazást az **új alkalmazás** lehetőség kiválasztásával.
4. Adja meg az alkalmazás nevét, és válassza a **regisztráció** lehetőséget.
5. Az alkalmazás létrehozása után nyissa meg a **tanúsítványok & a titkokat** a **kezelő** alatt.
6. Hozzon létre egy új ügyfél-titkos kulcsot az **új ügyfél titkos** kulcsának kiválasztásával az **ügyfél titkai** területen.
7. Adjon hozzá egy leírást az ügyfél titkos kódjához, válasszon ki egy lejárati időszakot, és válassza a **Hozzáadás** lehetőséget.
8. Tartsa meg a jövőbeli használat **értékét** .

   > [!NOTE]
   > Miután bezárta az oldalt, az érték maszkolásra kerül. Ha visszatér az oldalra, nem tudja lekérni az ügyfél titkos kulcsának értékét. Új ügyfél-titkos kulcsot kell kiállítania.

9. Lépjen vissza az újonnan létrehozott alkalmazás áttekintés lapjára, és másolja a következő értékeket a jövőbeli használatra:
    1. Alkalmazás (ügyfél) azonosítója
    1. Címtár (bérlő) azonosítója

## <a name="provide-permissions-to-the-application"></a>Engedélyek megadása az alkalmazás számára

1. A Azure Portal keresse meg a **hatáskörébe tartozó fiókokat**.
2. Válassza ki azt a hatáskörébe tartozó fiókot, amelyben az SQL-adatbázisok és a szinapszis besorolása megtörténik.
3. Nyissa meg a **hozzáférés-vezérlés (iam)** elemet, majd válassza a **Hozzáadás** lehetőséget.

4. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.
5. A **szerepkör** szakaszban keresse meg a **hatáskörébe tartozó adatolvasót** , és válassza ki.
6. A **Select (kiválasztás** ) szakaszban keresse meg a korábban létrehozott alkalmazást, válassza ki, majd kattintson a **Save (Mentés**) gombra.

## <a name="extract-the-classification-from-azure-purview"></a>A besorolás kibontása az Azure hatáskörébe

1. Nyissa meg a hatáskörébe tartozó fiókját, és a kezdőlapon keresse meg az Azure SQL Database vagy az Azure szinapszis Analyticset, ahová a címkéket másolni szeretné.
2. Másolja a qualifiedName a **Tulajdonságok** területen, és tartsa későbbi használatra.
3. Nyissa meg a PowerShell-rendszerhéjt.

4. Másolja az alábbi parancsfájlok egyikét az SQL-eszköz típusa (Azure SQL Database vagy az Azure szinapszis) alapján.
5. Adja meg a paramétereket a fent másolt értékekkel:

   a. $TenantID: 1. szakasz, 9. lépés
   
   b. $ClientID: 1. szakasz, 9. lépés
   
   c. $SecretID: 1. szakasz, 8. lépés
   
   d. $purviewAccountName: 2. szakasz, 2. lépés
   
   e. $sqlDatabaseName: 3. szakasz, 2. lépés

6. Másolja a szkript kimenetét későbbi használatra.

### <a name="for-azure-sql-database"></a>Azure SQL Database

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Az Azure szinapszis Analytics szolgáltatáshoz

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>A T-SQL-parancs futtatása az SQL-eszközön

1. Az Ön által választott eszközzel csatlakozhat a Azure SQL Databasehoz vagy az Azure Szinapszishoz.
2. Futtassa az előző szakaszból másolt T-SQL-parancsot.

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További információ az Azure-beli hatáskörébe kapcsolatban: az Azure-beli [hatáskörébe tartozó dokumentáció](../../purview/index.yml).