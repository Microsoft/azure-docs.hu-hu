---
title: TDE-védő eltávolítása (PowerShell-& az Azure CLI-vel)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Ismerje meg, hogy miként reagálhat az Azure SQL Database vagy az Azure szinapszis Analytics potenciálisan sérült TDE-oltalmazóra a TDE használatával a saját kulcs (BYOK) támogatásával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 31298be4d50c7f562e2e2b9adbf889b165b197e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461867"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Transzparens adattitkosítás (TDE)-védő eltávolítása a PowerShell használatával
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Ez a témakör azt ismerteti, hogyan lehet reagálni az olyan Azure SQL Database vagy Azure szinapszis-elemzések potenciálisan sérült TDE, amelyek Azure Key Vault-Bring Your Own Key (BYOK) támogatásban lévő ügyfél által felügyelt kulcsokkal használják a TDE-t. Ha többet szeretne megtudni a TDE BYOK-támogatásáról, tekintse meg az [Áttekintés oldalt](transparent-data-encryption-byok-overview.md).

> [!CAUTION]
> Az ebben a cikkben leírt eljárásokat csak szélsőséges esetekben vagy tesztelési környezetekben lehet elvégezni. Tekintse át a lépéseket körültekintően, mivel a Azure Key Vault aktívan használt TDE-védők törlése az **adatbázis elérhetetlenné válását** eredményezi.

Ha egy kulcs gyanúja fennáll, hogy egy adott szolgáltatás vagy felhasználó jogosulatlanul hozzáfért a kulcshoz, akkor érdemes törölni a kulcsot.

Ne feledje, hogy ha a TDE-védőt Key Vaultban törli, akár 10 percen belül, minden titkosított adatbázis megtagadja az összes kapcsolatot a megfelelő hibaüzenettel, és nem [érhető](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)el az állapota.

Ez a útmutató a feltört incidensek válasza után a kívánt eredménytől függően két megközelítést mutat be:

- Az adatbázisok Azure SQL Database/Azure szinapszis Analyticsben való **elérhetetlenné** tétele.
- Az adatbázisok Azure SQL Database/Azure Azure szinapszis Analyticsben való elérhetetlenné tétele nem **érhető** el.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetéssel kell rendelkeznie, és az előfizetés rendszergazdájának kell lennie
- A Azure PowerShell telepítése és futtatása szükséges.
- Ez a útmutató azt feltételezi, hogy már használ egy kulcsot Azure Key Vault TDE-védőként egy Azure SQL Database vagy egy Azure szinapszis számára. További információ: [TRANSZPARENS ADATTITKOSÍTÁS BYOK-támogatással](transparent-data-encryption-byok-overview.md) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Adott parancsmagok esetén lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

A telepítéshez lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>TDE-védő ujjlenyomatai megfelelnek keresése

Az alábbi lépések azt ismertetik, hogyan ellenőrizheti a TDE Protector ujjlenyomatai megfelelnek egy adott adatbázis virtuális naplófájljai (VLF) által még használatban.
Az adatbázis aktuális TDE-oltalmazójának ujjlenyomata, és az adatbázis-azonosító a következő futtatásával érhető el:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

A következő lekérdezés visszaadja a VLFs és a TDE-védő megfelelő ujjlenyomatai megfelelnek. Mindegyik különböző ujjlenyomat a Azure Key Vault (AKV) különböző kulcsaira utal:

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Alternatív megoldásként használhatja a PowerShellt vagy az Azure CLI-t is:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A **Get-AzureRmSqlServerKeyVaultKey PowerShell-** parancs   Megadja a LEKÉRDEZÉSben használt TDE-védő ujjlenyomatát, így láthatja, hogy mely kulcsokat kell megőrizni, és mely kulcsokat kell törölni a AKV-ben. Csak az adatbázis által már nem használt kulcsokat lehet biztonságosan törölni Azure Key Vaultból.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

A PowerShell-parancs az **SQL Server Key show**   biztosítja a LEKÉRDEZÉSben használt TDE-védő ujjlenyomatát, így láthatja, hogy mely kulcsokat kell megőrizni, és mely kulcsokat kell törölni a AKV-ben. Csak az adatbázis által már nem használt kulcsokat lehet biztonságosan törölni Azure Key Vaultból.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Az elérhető titkosított erőforrások megőrzése

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hozzon létre egy [új kulcsot a Key Vaultban](/powershell/module/az.keyvault/add-azkeyvaultkey). Győződjön meg arról, hogy az új kulcs egy különálló kulcstartóban jön létre a potenciálisan feltört TDE-védőtől, mert a hozzáférés-vezérlést a tároló szintjén kell kiépíteni.

2. Adja hozzá az új kulcsot a kiszolgálóhoz az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) és a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmaggal, és frissítse azt a kiszolgáló új TDE-oltalmazójának használatával.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Győződjön meg arról, hogy a kiszolgáló és a replikák frissítve lettek az új TDE-védőhöz a [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával.

   > [!NOTE]
   > Eltarthat néhány percig, amíg az új TDE-védő propagálni képes a kiszolgáló által használt összes adatbázisba és másodlagos adatbázisba.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Készítsen [biztonsági másolatot az új kulcsról](/powershell/module/az.keyvault/backup-azkeyvaultkey) Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Törölje a feltört kulcsot Key Vault a [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) parancsmag használatával.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. A kulcs visszaállítása Key Vaultre a későbbiekben a [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) parancsmag használatával:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

A parancsokra vonatkozó hivatkozásokat az [Azure CLI](/cli/azure/keyvault/key)kulcstartója tartalmazza.

1. Hozzon létre egy [új kulcsot a Key Vaultban](/cli/azure/keyvault/key#az-keyvault-key-create). Győződjön meg arról, hogy az új kulcs egy különálló kulcstartóban jön létre a potenciálisan feltört TDE-védőtől, mert a hozzáférés-vezérlést a tároló szintjén kell kiépíteni.

2. Adja hozzá az új kulcsot a kiszolgálóhoz, és frissítse azt a kiszolgáló új TDE-védelmezője.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Győződjön meg arról, hogy a kiszolgáló és az összes replika frissítve lett az új TDE-védőre.

   > [!NOTE]
   > Eltarthat néhány percig, amíg az új TDE-védő propagálni képes a kiszolgáló által használt összes adatbázisba és másodlagos adatbázisba.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Készítsen biztonsági másolatot az új kulcsról Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Törölje a feltört kulcsot a Key Vaultból.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Kulcs visszaállítása a Key Vault a jövőben.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>A titkosított erőforrások elérhetetlenné tétele

1. A potenciálisan feltört kulccsal titkosított adatbázisok eldobása.

   A rendszer automatikusan biztonsági másolatot készít az adatbázisról és a naplófájlokról, így az adatbázis adott időpontban történő visszaállítása bármikor elvégezhető (feltéve, hogy megadja a kulcsot). Az adatbázisokat el kell dobni az aktív TDE-védő törlése előtt, hogy megakadályozza a legutóbbi tranzakciók akár 10 perces elvesztését.

2. Biztonsági másolat készítése a TDE-védő fő anyagáról Key Vaultban.
3. A potenciálisan feltört kulcs eltávolítása Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan forgathatja el egy kiszolgáló TDE-védelmezőjét a biztonsági követelmények teljesítése érdekében: [a transzparens adattitkosítás Protector elforgatása a PowerShell használatával](transparent-data-encryption-byok-key-rotation.md)
- Ismerkedés a Bring Your Own Key támogatásával a TDE-hez: a [TDE bekapcsolása a saját kulcsával Key Vault a PowerShell használatával](transparent-data-encryption-byok-configure.md)