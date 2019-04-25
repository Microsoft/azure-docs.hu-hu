---
title: 'PowerShell és CLI: Az SQL TDE - az Azure Key Vault engedélyezése – saját kulcs – Azure SQL Database használata |} A Microsoft Docs'
description: Egy Azure SQL Database és az adatraktár használatához a transzparens adattitkosítás (TDE) az inaktív titkosítási konfigurálása a PowerShell vagy parancssori felület használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60330873"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell és CLI: Transzparens adattitkosítás engedélyezése az Azure Key Vault az ügyfél által felügyelt kulccsal

Ez a cikk végigvezeti a transzparens adattitkosítás (TDE) egy SQL-adatbázis vagy adatraktár Azure Key Vaultban lévő kulcsot használ. További tudnivalók az Azure Key Vault-integráció - Bring Your Own Key (BYOK) támogatása, a TDE [ügyfél által felügyelt kulcsok Azure Key vaultban TDE](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>PowerShell előfeltételei

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

- Azure-előfizetés és a lehet az előfizetés-rendszergazda.
- [Opcionális de javasolt] Rendelkezik egy hardveres biztonsági modul (HSM) vagy a helyi kulcs létrehozásához a TDE-Védőhöz megosztottkulcs-anyag helyi másolatát tárolja.
- Azure PowerShell telepítenie és futtatnia kell rendelkeznie. 
- Hozzon létre egy Azure Key Vault és a kulcs TDE használatára.
  - [Key vault PowerShell-utasítások](../key-vault/key-vault-overview.md)
  - [Egy hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasítások](../key-vault/key-vault-hsm-protected-keys.md)
    - A key vaultban kell rendelkeznie a TDE használható a következő tulajdonság:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](../key-vault/key-vault-soft-delete-powershell.md) 
- A kulcs TDE használható a következő attribútumokkal kell rendelkeznie:
   - Lejárati dátum nélküli
   - Nincs letiltva
   - Sikerült elvégezni *első*, *kulcs becsomagolása*, *kulcs kicsomagolása* műveletek

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>1. lépés Az Azure AD identity rendelhet hozzá a kiszolgálóhoz 

Ha rendelkezik egy meglévő kiszolgálóra, használja az Azure AD-identitás hozzáadása a kiszolgálóhoz a következő:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

A kiszolgáló létrehozásakor használja a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) címkével parancsmag-identitás hozzáadása egy Azure AD identity kiszolgáló létrehozása során:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2. lépés A kiszolgálóhoz a Key Vault-engedélyek megadása

Használja a [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmagot, hogy a kiszolgáló hozzáférést biztosítani a kulcs előtt származó kulcsot használ a TDE-tároló.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3. lépés A Key Vault-kulcs hozzáadása a kiszolgálóhoz, és állítsa be a TDE-Védőhöz

- Használja a [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) parancsmag használatával adja hozzá a kulcsot a Key Vault a kiszolgálón.
- Használja a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot, hogy a kulcs állítja be a TDE-védőhöz, az összes kiszolgáló-erőforráshoz.
- Használja a [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmaggal győződjön meg arról, hogy a TDE-védőhöz helyesen lett-e konfigurálva.

> [!Note]
> A kulcstároló nevét és a kulcs nevét együttes hossza nem lehet 94 karakternél.
> 

>[!Tip]
>Példa kulcsazonosító a Key Vaultból: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>4. lépés Kapcsolja be a TDE 

Használja a [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) kapcsolja be a TDE-parancsmagot.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Az adatbázis, sem az adattárházra már TDE engedélyezve van a titkosítási kulcsot a Key Vaultban.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5. lépés Ellenőrizze a titkosítási állapot és a titkosítás tevékenység

Használja a [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) a titkosítás állapotának és a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) az adatbázis titkosítási folyamatának ellenőrzéséhez vagy Data warehouse-bA.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Egyéb hasznos PowerShell-parancsmagok

- Használja a [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) kapcsolja ki a TDE-parancsmagot.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Használja a [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmag a listát a Key Vault-kulcsok hozzáadni a kiszolgálóhoz.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Használja a [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) a Key Vault-kulcs eltávolítása a kiszolgálóról.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Hibaelhárítás

Ellenőrizze a következőket a probléma akkor fordul elő, ha:
- Ha a key vault nem található, ellenőrizze, hogy használja-e a megfelelő előfizetést használ a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmagot.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Ha a kiszolgáló nem adható hozzá az új kulcsot, vagy az új kulcs nem lehet frissíteni a TDE-Védőhöz, ellenőrizze a következőket:
   - A kulcs nem rendelkezhet egy lejárati dátuma
   - A kulcsnak rendelkeznie kell a *első*, *kulcs becsomagolása*, és *kulcs kicsomagolása* engedélyezett műveletek.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogy a TDE-Védőhöz, egy kiszolgáló biztonsági követelmények ahhoz, hogy rotálása: [A transzparens adattitkosítási védelmi modulra vonatkozó PowerShell használatával elforgatása](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Esetén a biztonsági kockázatot jelent megtudhatja, hogyan távolítsa el a potenciálisan veszélyeztetett TDE-Védőhöz: [Egy esetleg feltört kulcs eltávolítására](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Parancssori felület előfeltételei

- Azure-előfizetés és a lehet az előfizetés-rendszergazda.
- [Opcionális de javasolt] Rendelkezik egy hardveres biztonsági modul (HSM) vagy a helyi kulcs létrehozásához a TDE-Védőhöz megosztottkulcs-anyag helyi másolatát tárolja.
- Parancssori felület 2.0-s vagy újabb verziója. Telepítse a legújabb verziót, és csatlakozzon az Azure-előfizetéshez, lásd: [telepítése és konfigurálása az Azure többplatformos parancssori felület 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Hozzon létre egy Azure Key Vault és a kulcs TDE használatára.
  - [CLI 2.0 használatával a Key Vault felügyelete](../key-vault/key-vault-manage-with-cli2.md)
  - [Egy hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasítások](../key-vault/key-vault-hsm-protected-keys.md)
    - A key vaultban kell rendelkeznie a TDE használható a következő tulajdonság:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](../key-vault/key-vault-soft-delete-cli.md) 
- A kulcs TDE használható a következő attribútumokkal kell rendelkeznie:
   - Lejárati dátum nélküli
   - Nincs letiltva
   - Sikerült elvégezni *első*, *kulcs becsomagolása*, *kulcs kicsomagolása* műveletek
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>1. lépés Kiszolgáló létrehozása az Azure AD-identitás
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>A kiszolgáló létrehozása "principalID" megtartása, hanem az objektumazonosító, használja a következő lépésben a key vault-engedélyek hozzárendelése
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>2. lépés A logikai sql Server Key Vault-engedélyek megadása
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Tartsa a kulcs URI-t vagy Kulcsazonosító az új kulcs a következő lépéshez, például: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3. lépés A Key Vault-kulcs hozzáadása a kiszolgálóhoz, és állítsa be a TDE-Védőhöz
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> A kulcstároló nevét és a kulcs nevét együttes hossza nem lehet 94 karakternél.
> 

  
## <a name="step-4-turn-on-tde"></a>4. lépés Kapcsolja be a TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Az adatbázis, sem az adattárházra most már TDE engedélyezve van az Azure Key Vaultban felhasználó által kezelt titkosítási kulccsal rendelkezik.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5. lépés Ellenőrizze a titkosítási állapot és a titkosítás tevékenység

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL-CLI referenciák

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

