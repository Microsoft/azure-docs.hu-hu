---
title: Felügyelt identitás engedélyezése a Azure Automation (előzetes verzió)
description: Ez a cikk bemutatja, hogyan állíthat be felügyelt identitást Azure Automation fiókokhoz.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514801"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Felügyelt identitás engedélyezése a Azure Automation (előzetes verzió)

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást egy Azure Automation-fiókhoz, és hogyan használhatja más erőforrások eléréséhez. A felügyelt identitások és a felügyelt identitások Azure Automation [lásd:](automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Előfeltételek

- Azure-fiók és -előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). A felügyelt identitásnak és a runbook által az identitás használatával felügyelt cél Azure-erőforrásoknak is ugyanabban az Azure-előfizetésben kell lennie.

- A fiókmodulok Azure Automation verziója. Ez jelenleg 1.6.0. (Erről a verzióról az [Az.Automation 1.6.0-s](https://www.powershellgallery.com/packages/Az.Automation/1.6.0) verziójában talál további információt.)

- Egy Azure-erőforrás, amely az Automation-runbookból kíván hozzáférni. Ennek az erőforrásnak meg kell határoznia egy szerepkört a felügyelt identitáshoz, amely segít az Automation-runbooknak hitelesíteni az erőforráshoz való hozzáférést. Szerepkörök hozzáadásához a megfelelő Azure AD-bérlőben az erőforrás tulajdonosának kell lennie.

- Ha felügyelt identitással szeretne hibrid feladatokat végrehajtani, frissítse a hibrid runbook-feldolgozót a legújabb verzióra. A minimálisan szükséges verziók a következőek:

   - Windows hibrid runbook-feldolgozó: 7.3.1125.0-s verzió
   - Linux hibrid runbook-feldolgozó: 1.7.4.0-s verzió

## <a name="enable-system-assigned-identity"></a>Rendszer által hozzárendelt identitás engedélyezése

>[!IMPORTANT]
>Az új Automation fiókszintű identitás felülbírálja az összes korábbi, vm-szintű, rendszer által hozzárendelt identitást (amelyek leírása a [Runbook-hitelesítés](/automation-hrw-run-runbooks#runbook-auth-managed-identities)felügyelt identitásokkal való használatát ismerteti). Ha olyan Azure-beli virtuális gépeken futtat hibrid feladatokat, amelyek egy virtuális gép rendszer által hozzárendelt identitásával férnek hozzá a runbook-erőforrásokhoz, akkor a hibrid feladatokhoz az Automation-fiókidentitás lesz használva. Ez azt jelenti, hogy a meglévő feladat végrehajtására hatással lehet, ha az Automation-fiók ügyfél által kezelt kulcsok (CMK) funkcióját használja.<br/><br/>Ha továbbra is használni szeretné a virtuális gép felügyelt identitását, ne engedélyezze az Automation-fiókszintű identitást. Ha már engedélyezte, letilthatja az Automation-fiók felügyelt identitását. Lásd: [Azure Automation fiók felügyelt identitásának letiltása.](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation)

A rendszer által hozzárendelt identitások Azure Automation kétféleképpen használhatja. Használhatja a Azure Portal, vagy az Azure REST API.

>[!NOTE]
>A felhasználó által hozzárendelt identitások még nem támogatottak.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Rendszer által hozzárendelt identitás engedélyezése a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Lépjen az Automation-fiókjához, és válassza az **Identitás lehetőséget** a **Fiókbeállítások alatt.**

1. Állítsa a **Rendszer által hozzárendelt** beállítást **Be,** majd nyomja le a **Mentés gombot.** Amikor a rendszer megerősítést kér, válassza az **Igen lehetőséget.**

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Rendszer által hozzárendelt identitás engedélyezése a Azure Portal.":::

Az Automation-fiók most már használhatja a rendszer által hozzárendelt identitást, amely a Azure Active Directory (Azure AD) szolgáltatásban van regisztrálva, és egy objektumazonosító képviseli.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Felügyelt identitás objektumazonosítója.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>Rendszer által hozzárendelt identitás engedélyezése a REST API

A következő hívással konfigurálhat egy rendszer által hozzárendelt felügyelt identitást az Automation-fiókhoz REST API használatával.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

A kérés törzse
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Tulajdonság (JSON) | Érték | Leírás|
|----------|-----------|------------|
| principalid (rendszerbiztonsági tag) | \<principal-ID\> | Az Automation-fiókot az Azure AD-bérlőben képviselő felügyelt identitás szolgáltatásnév-objektumának globálisan egyedi azonosítója (GUID). Ez a GUID néha "objektumazonosítóként" vagy objectID-ként jelenik meg. |
| tenantid (bérlőazonosító) | \<Azure-AD-tenant-ID\> | A globálisan egyedi azonosító (GUID), amely azt az Azure AD-bérlőt jelöli, amelyben az Automation-fiók most tag. Az Azure AD-bérlőn belül a szolgáltatásnév neve megegyezik az Automation-fiók nevével. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Identitás hozzáférésének adjuk az Azure-erőforrásokhoz egy jogkivonat beszerzését

Az Automation-fiókok a felügyelt identitásuk használatával jogkivonatokat kaphatnak az Azure AD által védett egyéb erőforrásokhoz való hozzáféréshez, például Azure Key Vault. Ezek a jogkivonatok nem az alkalmazás egy adott felhasználóját képviselik. Ehelyett az erőforráshoz hozzáférő alkalmazást képviselik. Ebben az esetben például a jogkivonat egy Automation-fiókot képvisel.

Mielőtt használhatta volna a rendszer által hozzárendelt felügyelt identitást a hitelesítéshez, állítson be hozzáférést az identitáshoz ahhoz az Azure-erőforráshoz, ahol használni tervezi az identitást. A feladat végrehajtásához rendelje hozzá a megfelelő szerepkört ehhez az identitáshoz a cél Azure-erőforráson.

Ez a példa Azure PowerShell mutatja be, hogyan rendelheti hozzá az előfizetés közreműködői szerepkörét a cél Azure-erőforráshoz. A Közreműködő szerepkört példaként használjuk, és előfordulhat, hogy az Ön esetében nincs is szükség rá.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Hozzáférés hitelesítése felügyelt identitással

Miután engedélyezi a felügyelt identitást az Automation-fiókjához, és identitás-hozzáférést ad a célerőforráshoz, megadhatja ezt az identitást a runbookban a felügyelt identitást támogató erőforrásokhoz. Az identitások támogatásához használja az Az parancsmag `Connect-AzAccount` parancsmagot. Lásd: [Connect-AzAccount a](/powershell/module/az.accounts/Connect-AzAccount) PowerShell-referenciában.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Ha a szervezet továbbra is az elavult AzureRM-parancsmagokat használja, használhatja a következőt: `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Hozzáférési jogkivonat létrehozása Azure-parancsmagok használata nélkül

HTTP-végpontok esetén győződjön meg a következőkről.
- A metaadatfejlécnek jelen kell lennie, és "true" (igaz) értéknek kell lennie.
- A kéréssel együtt egy erőforrást is át kell adnunk egy GET kérés lekérdezési paramétereként és űrlapadatokként egy POST-kéréshez.
- Az X-IDENTITY-HEADER értékét a hibrid runbook-IDENTITY_HEADER környezeti változó értékére kell állítani. 
- A Post kérés tartalomtípusának "application/x-www-form-urlencoded" típusúnak kell lennie. 

### <a name="sample-get-request"></a>GET-kérésminta

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>POST-kérésminta
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Minta runbookok felügyelt identitás használatával

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Minta runbook egy SQL-adatbázis Azure-parancsmagok használata nélküli eléréséhez

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Minta runbook egy kulcstartó Azure-parancsmagokkal való eléréséhez

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Python-minta runbook jogkivonat lekért használatához
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Következő lépések

- Ha le kell tiltania egy felügyelt identitást, tekintse meg a Azure Automation felügyelt identitás [(előzetes verzió) letiltását.](disable-managed-identity-for-automation.md)

- A fiókok biztonságának Azure Automation [Automation-fiókhitelesítés áttekintését.](automation-security-overview.md)