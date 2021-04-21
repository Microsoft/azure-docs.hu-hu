---
title: TDE-védő elforgatása (PowerShell & Azure CLI-hez)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Megtudhatja, hogyan forgathatja transzparens adattitkosítás (TDE) védőt egy Azure-beli kiszolgálóhoz, amelyet az Azure SQL Database és Azure Synapse Analytics a PowerShell és az Azure CLI használatával használ.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 67bcd8597314530f26481ef840644ffbc056b033
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777562"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>A transzparens adattitkosítás (TDE) védő elforgatása
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Ez a cikk egy [](logical-servers.md) TDE-védőt használó kiszolgáló kulcsrotációját ismerteti a Azure Key Vault. A kiszolgáló logikai TDE-védője váltása egy új aszimmetrikus kulcsra való váltást jelent, amely a kiszolgálón található adatbázisokat védi. A kulcsrotáció online művelet, és csak néhány másodpercet kell igénybe vennie, mivel ez csak az adatbázis adattitkosítási kulcsának visszafejtéséhez és újratitkosításához szükséges, nem a teljes adatbázishoz.

Ez az útmutató két lehetőséget tárgyal a TDE-védő a kiszolgálón való váltogatása érdekében.

> [!NOTE]
> A kulcsrotáció előtt Azure Synapse Analytics egy szüneteltetett dedikált SQL-készletet a Azure Synapse Analytics kell folytatni.

> [!IMPORTANT]
> Ne törölje a kulcs korábbi verzióit a visszaállítás után. A kulcsok áteredt adatai továbbra is titkosítva vannak az előző kulcsokkal, például az adatbázisok régebbi biztonsági másolatai.

## <a name="prerequisites"></a>Előfeltételek

- Ez az útmutató feltételezi, hogy már használ egy kulcsot a Azure Key Vault kulcsát TDE-védőként a Azure SQL Database vagy Azure Synapse Analytics. Lásd: [transzparens adattitkosítás BYOK-támogatással való támogatással.](transparent-data-encryption-byok-overview.md)
- A telepítésnek és Azure PowerShell kell lennie.
- [Ajánlott, de nem kötelező] Először hozza létre a TDE-védő kulcsanyagát egy hardveres biztonsági modulban (HSM) vagy helyi kulcstárolóban, majd importálja a kulcsanyagot a Azure Key Vault. További információért kövesse a hardveres biztonsági modul [(HSM)](../../key-vault/general/overview.md) használatának Key Vault útmutatóját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Konkrét parancsmagokért lásd: [AzureRM.Sql.](/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de minden jövőbeli fejlesztés az Az.Sql modulra lesz kihozva. Az AzureRM modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban található parancsok argumentumai jelentősen megegyeznek. További információ a kompatibilitásukról: [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Az új Azure PowerShell Az modul).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Telepítés: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>Manuális kulcsrotáció

A manuális kulcsrotáció a következő parancsokat használja egy teljesen új kulcs hozzáadásához, amely egy új kulcsnév vagy akár egy másik kulcstartó alatt is lehet. Ennek a megközelítésnek a használata támogatja ugyanazon kulcs különböző kulcstartókhoz való hozzáadását a magas rendelkezésre állás és a geo-dr forgatókönyvek támogatása érdekében.

> [!NOTE]
> A kulcstartó nevének és kulcsnevének együttes hossza nem haladhatja meg a 94 karaktert.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja az [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), és [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagokat.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja az [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create), az sql server key [create](/cli/azure/sql/server/key#az_sql_server_key_create), és az az sql [server tde-key set](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set) parancsot.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE-védő üzemmód váltása

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- A Microsoft által felügyelt TDE-védő BYOK módra váltásához használja a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- A TDE-védő BYOK módról Microsoft által felügyeltre váltásához használja a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi példák az [az sql server tde-key set parancs használatát használják.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

- A TDE-védő Microsoft által felügyeltről BYOK módra váltása:

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- A TDE-védő BYOK módról Microsoft által felügyeltre váltása:

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Következő lépések

- Biztonsági kockázat esetén ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: Egy potenciálisan [feltört kulcs eltávolítása.](transparent-data-encryption-byok-remove-tde-protector.md)

- A TDE Azure Key Vault és Bring Your Own Key használatának első lépések: Kapcsolja be a TDE-t a saját kulcsával a Key Vault [PowerShell használatával.](transparent-data-encryption-byok-configure.md)