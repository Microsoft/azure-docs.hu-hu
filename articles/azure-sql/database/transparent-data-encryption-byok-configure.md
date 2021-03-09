---
title: SQL-TDE engedélyezése Azure Key Vault
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL Database és az Azure szinapszis Analytics szolgáltatást, hogy a PowerShell vagy az Azure CLI használatával megkezdheti a titkosítást a transzparens adattitkosítás (TDE) használatával.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 610f0a9692c18afbd7bb446959b09bac14d6f629
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507619"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell és az Azure CLI: transzparens adattitkosítás engedélyezése az ügyfél által felügyelt kulccsal Azure Key Vault
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ez a cikk bemutatja, hogyan használhatja a Azure Key Vault for transzparens adattitkosítás (TDE) kulcsát a Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásban. Ha többet szeretne megtudni a Azure Key Vault Integration-Bring Your Own Key (BYOK) támogatással rendelkező TDE, látogasson el a [TDE felhasználó által felügyelt kulcsokra a Azure Key Vault](transparent-data-encryption-byok-overview.md).

> [!NOTE] 
> Az Azure SQL mostantól támogatja a felügyelt HSM-ben tárolt RSA-kulcs használatát TDE-védőként. Ez a funkció **nyilvános előzetes** verzióban érhető el. Azure Key Vault felügyelt HSM egy teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványoknak megfelelő felhőalapú szolgáltatás, amely lehetővé teszi a felhőalapú alkalmazások titkosítási kulcsainak védelmét az FIPS 140-2 3. szintű hitelesített HSM használatával. További információ a [felügyelt HSM](../../key-vault/managed-hsm/index.yml).

## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

- Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
- [Ajánlott, de nem kötelező] Hardveres biztonsági modult (HSM) vagy helyi kulcstárolót kell létrehoznia a TDE-védő kulcsfontosságú anyagának helyi másolatának létrehozásához.
- A Azure PowerShell telepítése és futtatása szükséges.
- Hozzon létre egy Azure Key Vault és egy kulcsot a TDE használatához.
  - [Útmutató a hardveres biztonsági modul (HSM) és a Key Vault használatához](../../key-vault/keys/hsm-protected-keys.md)
    - A Key vaultnak a következő tulajdonságot kell használnia a TDE:
  - a védelem eltávolítása és [törlése](../../key-vault/general/soft-delete-overview.md)
- A kulcsnak a következő attribútumokkal kell rendelkeznie a TDE való használathoz:
  - Nincs lejárati dátum
  - Nincs letiltva
  - Képes a *Get*, a *wrap Key*, a *dewrap Key* Operations művelet végrehajtására
- **(Előzetes verzió)** Felügyelt HSM-kulcs használatához kövesse a [felügyelt HSM Azure CLI-vel történő létrehozásához és aktiválásához](../../key-vault/managed-hsm/quick-create-cli.md) szükséges utasításokat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Adott parancsmagok esetén lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

A Key Vault kapcsolatos részletekért tekintse meg a [Key Vault PowerShell-utasításokat](../../key-vault/secrets/quick-create-powershell.md) , valamint a [Key Vault a PowerShell használatával történő törlését](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Azure Active Directory (Azure AD) identitásának kiosztása a kiszolgálóhoz

Ha rendelkezik meglévő [kiszolgálóval](logical-servers.md), a következő paranccsal adhat hozzá egy Azure Active Directory (Azure ad) identitást a kiszolgálóhoz:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Ha kiszolgálót hoz létre, használja a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancsmagot a tag-Identity paranccsal az Azure ad-identitás hozzáadásához a kiszolgáló létrehozásakor:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault engedélyek megadása a kiszolgálónak

A [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag használatával biztosíthatja a kiszolgáló hozzáférését a Key vaulthoz, mielőtt a TDE-hoz tartozó kulcsot használ.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
Ha egy felügyelt HSM-kiszolgálón szeretne engedélyeket hozzáadni a kiszolgálóhoz, adja hozzá a "felügyelt HSM titkosítási szolgáltatás titkosítása" helyi RBAC szerepkört a kiszolgálóhoz. Ez lehetővé teszi a kiszolgáló számára, hogy a felügyelt HSM kulcsait lekéri a Get, a wrap Key és a dewrap Key műveletekre.
[Útmutatás a kiszolgáló-hozzáférés felügyelt HSM-hez való kiépítési folyamatához](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adja hozzá a Key Vault kulcsot a kiszolgálóhoz, és állítsa be a TDE-védőt.

- A Key Vault AZONOSÍTÓjának lekéréséhez használja a [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) parancsmagot.
- Az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) parancsmag használatával adja hozzá a kulcsot a Key Vault a-kiszolgálóhoz.
- A [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmag használatával állítsa be a kulcsot TDE-védőként az összes kiszolgálói erőforráshoz.
- A [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával ellenőrizze, hogy a TDE-védő a kívánt módon lett-e konfigurálva.

> [!NOTE]
> **(Előzetes verzió)** Felügyelt HSM-kulcsok esetén használja a PowerShell az az. SQL 2.11.1 verzióját.

> [!NOTE]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

> [!TIP]
> Példa a Key Vault KeyId:<br/>https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
> Példa a felügyelt HSM-ből származó KeyId:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE bekapcsolása

A TDE bekapcsolásához használja a [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmagot.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Most, hogy az adatbázis vagy az adattárház TDE engedélyezve van a Key Vaultban lévő titkosítási kulccsal.

## <a name="check-the-encryption-state-and-encryption-activity"></a>A titkosítási állapot és a titkosítási tevékenység keresése

A [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) használatával lekérheti a titkosítási állapotot és a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) , és megtekintheti az adatbázis vagy az adatraktár titkosítási folyamatát.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI (2,0-es vagy újabb verzió) szükséges verziójának telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz tekintse meg [Az Azure platformfüggetlen Command-Line Interface 2,0 telepítése és konfigurálása](/cli/azure/install-azure-cli)című témakört.

A Key Vault kapcsolatos részletekért tekintse meg a [Key Vault kezelése a cli 2,0 használatával](../../key-vault/general/manage-with-cli2.md) és a parancssori felülettel [történő Key Vault-törlés használata](../../key-vault/general/key-vault-recovery.md)című témakört.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Azure AD-identitás kiosztása a kiszolgálóhoz

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Tartsa meg a "principalID" a kiszolgáló létrehozásával, a Key Vault-engedélyek hozzárendeléséhez használt objektumazonosító a következő lépésben

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault engedélyek megadása a kiszolgálónak

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Tartsa meg az új kulcs kulcs-URI-JÁT vagy keyID a következő lépéshez, például: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adja hozzá a Key Vault kulcsot a kiszolgálóhoz, és állítsa be a TDE-védőt.

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

## <a name="turn-on-tde"></a>TDE bekapcsolása

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Most, hogy az adatbázis vagy az adattárház TDE van engedélyezve a felhasználó által felügyelt titkosítási kulccsal Azure Key Vaultban.

## <a name="check-the-encryption-state-and-encryption-activity"></a>A titkosítási állapot és a titkosítási tevékenység keresése

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- A TDE kikapcsolásához használja a [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmagot.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- A [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmag használatával adja vissza a kiszolgálóhoz hozzáadott Key Vault kulcsok listáját.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- A [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) használatával távolítson el egy Key Vault kulcsot a kiszolgálóról.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Az adatbázis általános beállításai: [az SQL](/cli/azure/sql).

- A tár legfontosabb beállításai: [az SQL Server Key](/cli/azure/sql/server/key).

- A TDE beállításai: [az SQL Server TDE-Key](/cli/azure/sql/server/tde-key) és [az SQL db TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Hibaelhárítás

Ha probléma merül fel, ellenőrizze a következőket:

- Ha a kulcstartó nem található, győződjön meg róla, hogy a megfelelő előfizetésben van.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Ha az új kulcs nem adható hozzá a kiszolgálóhoz, vagy az új kulcs nem frissíthető TDE-védőként, ellenőrizze a következőket:
   - A kulcs nem rendelkezhet lejárati dátummal
   - A kulcsnak engedélyezve kell lennie a *Get*, a *wrap Key* és a *dewrap Key* műveletnek.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan forgathatja el egy kiszolgáló TDE-védelmezőjét a biztonsági követelményeknek való megfelelés érdekében: [a transzparens adattitkosítás Protector elforgatása a PowerShell használatával](transparent-data-encryption-byok-key-rotation.md).
- Biztonsági kockázat esetén Ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: [távolítson el egy potenciálisan feltört kulcsot](transparent-data-encryption-byok-remove-tde-protector.md).
