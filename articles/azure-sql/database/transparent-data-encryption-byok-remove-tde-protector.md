---
title: A TDE-védő eltávolítása (PowerShell & Azure CLI-hez)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Megtudhatja, hogyan válaszolhat a TDE-t a TDE Azure SQL Database (BYOK) Bring Your Own Key(BYOK) Azure SQL Database vagy Azure Synapse Analytics esetén.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: f98dcdd9c1a479703c82c01b4fd240507ea355de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784461"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>A transzparens adattitkosítás (TDE) védő eltávolítása a PowerShell használatával
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Ez a témakör azt ismerteti, hogyan válaszolhat a TDE-t felhasználó által felügyelt kulcsokkal használó Azure SQL Database- vagy Azure Synapse Analytics-védelem potenciálisan feltört TDE-védelmére a Azure Key Vault – Bring Your Own Key (BYOK) támogatásban. A TDE BYOK-támogatásával kapcsolatos további információkért lásd az [áttekintő oldalt.](transparent-data-encryption-byok-overview.md)

> [!CAUTION]
> A cikkben ismertetett eljárásokat csak szélsőséges esetekben vagy tesztkörnyezetekben szabad eltennünk. Gondosan tekintse át a lépéseket, mivel az aktívan használt TDE-védők Azure Key Vault elérhetetlenné válnak az **adatbázisból.**

Ha azt gyanítják, hogy egy kulcsot feltörtek, például hogy egy szolgáltatás vagy felhasználó jogosulatlanul hozzáfért a kulcshoz, akkor a legjobb, ha törli a kulcsot.

Ne feledje, hogy a TDE-védő Key Vault-ben való törlése után legfeljebb 10 percen belül az összes titkosított adatbázis elkezd megtagadni minden kapcsolatot a megfelelő hibaüzenettel, és elérhetetlenné teszi az [állapotát.](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)

Ez az útmutató két megközelítésen megy keresztül attól függően, hogy milyen eredményt kíván elérni egy sérült incidens esetén:

- Az adatbázis elérhetetlenné Azure SQL Database /Azure Synapse Analytics **érdekében.**
- Az adatbázis elérhetetlenné Azure SQL Database /Azure Azure Synapse Analytics **számára.**

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetéssel kell lennie, és az előfizetés rendszergazdájának kell lennie
- A telepítésnek és Azure PowerShell kell lennie.
- Ez az útmutató feltételezi, hogy már használ egy kulcsot a Azure Key Vault-kulcs TDE-védőjeként egy Azure SQL Database vagy Azure Synapse. További [transzparens adattitkosítás lásd: A BYOK-támogatással](transparent-data-encryption-byok-overview.md) való ismerkedás.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Konkrét parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de minden jövőbeli fejlesztés az Az.Sql modulhoz lesz. Az AzureRM modul 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek. További információ a kompatibilitásukról: [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Az új Azure PowerShell Az modul).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Telepítés: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

* * *

## <a name="check-tde-protector-thumbprints"></a>Ellenőrizze a TDE-védő ujjlenyomatát

A következő lépések azt ismertetik, hogyan ellenőrizheti egy adott adatbázis virtuális naplófájlok (VLF) által még használt TDE-védő ujjlenyomatait.
Az adatbázis jelenlegi TDE-védő ujjlenyomata és az adatbázis-azonosító a következő futtatásával található meg:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

A következő lekérdezés visszaadja a használt VLFs-eket és A TDE-védő megfelelő ujjlenyomatát. Minden egyes ujjlenyomat más-más kulcsra hivatkozik a Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Másik lehetőségként használhatja a PowerShellt vagy az Azure CLI-t:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A **Get-AzureRmSqlServerKeyVaultKey** PowerShell-parancs biztosítja a lekérdezésben használt TDE-védő ujjlenyomatát, így láthatja, hogy mely kulcsokat kell megtartani, és mely kulcsokat kell törölni az   AKV-ban. Csak az adatbázis által már nem használt kulcsok törölhetők biztonságosan a Azure Key Vault.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az **sql server key show** PowerShell-parancs a lekérdezésben használt TDE-védő ujjlenyomatát biztosítja, így láthatja, hogy mely kulcsokat kell megtartani, és mely kulcsokat kell törölni az   AKV-ban. Csak az adatbázis által már nem használt kulcsok törölhetők biztonságosan a Azure Key Vault.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Titkosított erőforrások elérhetővé tétele

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy új kulcsot a [Key Vault.](/powershell/module/az.keyvault/add-azkeyvaultkey) Győződjön meg arról, hogy az új kulcs a potenciálisan feltört TDE-védőtől különálló kulcstartóban jön létre, mivel a hozzáférés-vezérlés tárolószinten van kiépítve.

2. Adja hozzá az új kulcsot a kiszolgálóhoz az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) és [a Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagok használatával, és frissítse a kiszolgáló új TDE-védőjeként.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. A [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával győződjön meg arról, hogy a kiszolgáló és az összes replika frissítve lett az új TDE-védőre.

   > [!NOTE]
   > Eltarthat néhány percig, hogy az új TDE-védő propagálva legyen a kiszolgálón található összes adatbázisra és másodlagos adatbázisra.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Biztonsági másolat [készítése az új kulcsról a](/powershell/module/az.keyvault/backup-azkeyvaultkey) Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Törölje a feltört kulcsot a Key Vault [a Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) parancsmag használatával.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Kulcs visszaállítása a Key Vault a [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) parancsmag használatával:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

A parancsra vonatkozó referenciáért tekintse meg az [Azure CLI kulcstartóját.](/cli/azure/keyvault/key)

1. Hozzon létre [egy új kulcsot a Key Vault.](/cli/azure/keyvault/key#az_keyvault_key_create) Győződjön meg arról, hogy az új kulcs a potenciálisan feltört TDE-védőtől különálló kulcstartóban jön létre, mivel a hozzáférés-vezérlés tárolószinten van kiépítve.

2. Adja hozzá az új kulcsot a kiszolgálóhoz, és frissítse a kiszolgáló új TDE-védőjeként.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Győződjön meg arról, hogy a kiszolgáló és az összes replika frissítve lett az új TDE-védőre.

   > [!NOTE]
   > Eltarthat néhány percig, hogy az új TDE-védő propagálva legyen a kiszolgálón található összes adatbázisra és másodlagos adatbázisra.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Biztonsági másolat készítése az új kulcsról a Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Törölje a feltört kulcsot a Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. A kulcs visszaállításához a Key Vault a jövőben.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Titkosított erőforrások elérhetetlenné válnak

1. A potenciálisan feltört kulccsal titkosított adatbázisokat hagyja el.

   Az adatbázisról és a naplófájlról a rendszer automatikusan biztonsági mentést végez, így az adatbázis időponthoz időben végzett visszaállítása bármikor (a kulcs megszabadulása után). Az aktív TDE-védő törlése előtt el kell dobni az adatbázisokat, hogy megelőzhető legyen a legutóbbi tranzakciókból legfeljebb 10 percnyi adatvesztés.

2. A TDE-védő kulcsanyagának biztonsági Key Vault.
3. Távolítsa el a potenciálisan feltört kulcsot a Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan forgathatja a kiszolgáló TDE-védőját a biztonsági követelményeknek megfelelően: A kiszolgáló transzparens adattitkosítás a [PowerShell használatával](transparent-data-encryption-byok-key-rotation.md)
- A TDE Bring Your Own Key használatának első Bring Your Own Key: A TDE bekapcsolás saját kulcs használatával Key Vault [PowerShell használatával](transparent-data-encryption-byok-configure.md)