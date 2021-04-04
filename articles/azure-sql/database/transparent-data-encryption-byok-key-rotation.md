---
title: TDE-védő elforgatása (PowerShell-& az Azure CLI-vel)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Megtudhatja, hogyan forgathatja el az Azure-beli kiszolgálók transzparens adattitkosítás (TDE) védelmezőjét a PowerShell és az Azure CLI használatával Azure SQL Database és az Azure szinapszis Analytics által használt kiszolgálón.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 37931aee24e2dbcab03bca400d58f236601c87e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93321428"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>A transzparens adattitkosítás (TDE) oltalmazó elforgatása
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Ez a cikk a Azure Key Vault-ból származó TDE-védőt használó [kiszolgálók](logical-servers.md) fő rotációs módját ismerteti. A logikai TDE-védő egy kiszolgáló felé történő elforgatása azt jelenti, hogy a kiszolgálón lévő adatbázisokat védő új aszimmetrikus kulcsra vált. A kulcs elforgatása egy online művelet, amely csak néhány másodpercig tart, mivel ez csak a teljes adatbázis visszafejtését és újratitkosítását végzi el.

Ez az útmutató két lehetőséget tárgyal a TDE-védő a kiszolgálón való elforgatására.

> [!NOTE]
> A kulcsfontosságú rotációk előtt folytatni kell a szüneteltetett dedikált SQL-készletet az Azure szinapszis Analyticsben.

> [!IMPORTANT]
> Egy rollover után ne törölje a kulcs korábbi verzióit. A kulcsok átadásakor a rendszer bizonyos adatokat továbbra is titkosít az előző kulcsokkal, például a régebbi adatbázis-biztonsági mentésekkel.

## <a name="prerequisites"></a>Előfeltételek

- Ez a útmutató azt feltételezi, hogy Ön már használ egy kulcsot Azure Key Vault TDE-védőként Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatáshoz. Lásd: [TRANSZPARENS ADATTITKOSÍTÁS BYOK-támogatással](transparent-data-encryption-byok-overview.md).
- A Azure PowerShell telepítése és futtatása szükséges.
- [Ajánlott, de nem kötelező] Először a hardveres biztonsági modulban (HSM) vagy a helyi Key Store-ban hozza létre a TDE-védő fő anyagát, és importálja Azure Key Vault. További információért kövesse a [hardveres biztonsági modul (HSM) használatának utasításait és Key Vault](../../key-vault/general/overview.md) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Adott parancsmagok esetén lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

A telepítéshez lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Kézi kulcs elforgatása

A manuális kulcs elforgatása az alábbi parancsokkal adhat hozzá egy teljesen új kulcsot, amely az új kulcs neve vagy egy másik kulcstartó alá is kerülhet. Ezzel a módszerrel a magas rendelkezésre állás és a Geo-Dr forgatókönyvek támogatásához ugyanazt a kulcsot kell hozzáadni a különböző kulcstartóhoz.

> [!NOTE]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)és a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

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

Használja az az [kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create), [az SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)és [az az SQL Server TDE-Key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) parancsokat.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE-védő mód váltása

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Ha a TDE-védőt a Microsoft által felügyelt BYOK módba szeretné váltani, használja a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Ha a TDE-védőt a BYOK módból a Microsoft által felügyelt értékre szeretné váltani, használja a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi példák az [az SQL Server TDE-Key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)használatát használják.

- Ha a TDE-védőt a Microsoft által felügyelt BYOK módba szeretné váltani,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Ha a TDE-védőt a BYOK módból a Microsoft által felügyelt értékre szeretné váltani,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Következő lépések

- Biztonsági kockázat esetén Ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: [távolítson el egy potenciálisan feltört kulcsot](transparent-data-encryption-byok-remove-tde-protector.md).

- Ismerkedés a Azure Key Vault integrációval és a Bring Your Own Key támogatásával a TDE-hez: a [saját kulcsának bekapcsolása a Key Vault a PowerShell használatával](transparent-data-encryption-byok-configure.md).