---
title: CLI-példa – a BYOK TDE engedélyezése – Azure SQL felügyelt példány
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL felügyelt példányt úgy, hogy a PowerShell használatával megkezdje a BYOK transzparens adattitkosítás (TDE) használatát a titkosításhoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323687"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Felügyelt példányon lévő transzparens adattitkosítás kezelése a saját kulcsának használatával Azure Key Vault

Ez az Azure CLI parancsfájl-példa transzparens adattitkosítás (TDE) konfigurációját konfigurálja a felügyelt Azure SQL-példányhoz az ügyfél által felügyelt kulccsal, Azure Key Vault-kulcs használatával. Ezt gyakran nevezik Bring Your Own Key TDE-forgatókönyvnek. Ha többet szeretne megtudni az ügyfél által felügyelt kulccsal TDE, tekintse meg a [TDE bring your own Key az Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)-ben című témakört.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="prerequisites"></a>Előfeltételek

Egy meglévő felügyelt példány: Azure [SQL felügyelt példány létrehozása az Azure CLI használatával](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az SQL db](/cli/azure/sql/db) | Adatbázis-parancsok. |
| [az SQL feladatátvétel-csoport](/cli/azure/sql/failover-group) | Feladatátvételi csoport parancsai. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../../azure-sql/database/az-cli-script-samples-content-guide.md) találhat.
