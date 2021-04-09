---
title: CLI-szkript – kiszolgáló visszaállítása – Azure Database for MariaDB
description: Ez a minta Azure CLI-szkript bemutatja, hogyan lehet visszaállítani egy Azure Database for MariaDB-kiszolgálót és annak adatbázisait egy korábbi időpontra.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: a4fc645f43858e80eeffc6c1672f6bd4369e7290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664583"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure Database for MariaDB-kiszolgáló visszaállítása az Azure CLI használatával
Ez a CLI-parancsfájl egyetlen Azure Database for MariaDB kiszolgálót állít vissza egy korábbi időpontra.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát. Cserélje le az `az monitor` parancsokban használt előfizetés-azonosítót a saját előfizetés-azonosítójára.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az MariaDB Server Create](/cli/azure/mariadb/server#az-mariadb-server-create) | Létrehoz egy MariaDB-kiszolgálót, amely az adatbázisokat üzemelteti. |
| [az MariaDB Server Restore](/cli/azure/mariadb/server#az-mariadb-server-restore) | Visszaállít egy kiszolgálót egy biztonsági másolatból. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További parancsfájlok kipróbálása: [Azure CLI-minták a Azure Database for MariaDB](../sample-scripts-azure-cli.md)
