---
title: CLI-példa a naplózásra és az összetett veszélyforrások elleni védelemre – Azure SQL Database
description: Példa az Azure CLI-szkriptre a naplózás és az összetett veszélyforrások elleni védelem konfigurálásához egy Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 6791691be976e541c6400e89dde8f892fe50c6dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336173"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>A CLI használata SQL Database naplózás és az összetett veszélyforrások elleni védelem konfigurálásához

Ez az Azure CLI parancsfájl-példa SQL Database naplózási és komplex veszélyforrások elleni védelmet konfigurálja.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Leírás |
|---|---|
| [az SQL db audit-Policy](/cli/azure/sql/db/audit-policy) | Beállítja egy adatbázis naplózási szabályzatát. |
| [az SQL db Threat-Policy](/cli/azure/sql/db/threat-policy) | Egy összetett veszélyforrások elleni védelmi szabályzatot állít be egy adatbázison. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../../azure-sql/database/az-cli-script-samples-content-guide.md) találhat.
