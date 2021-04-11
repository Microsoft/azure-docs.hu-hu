---
title: Azure CLI-szkript – kiszolgálói konfigurációk módosítása (PostgreSQL)
description: Ez a CLI-példaszkript felsorol minden elérhető kiszolgálókonfigurációs lehetőséget, és frissíti az egyik lehetőség értékét.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 1c3433c1f96dc32d91f1c07882f231202383e1c3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607354"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure Database for PostgreSQL-kiszolgáló konfigurációinak felsorolása és frissítése az Azure CLI használatával
Ez a CLI-példaszkript felsorolja egy Azure Database for PostgreSQL-kiszolgáló összes elérhető konfigurációs paraméterét, valamint azok engedélyezett értékeit, és beállítja a *log_retention_days* paramétert egy, az alapértelmezett értéktől eltérő értékre.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript
A példaszkriptben szerkessze a kiemelt sorokat, és adja meg bennük saját rendszergazdai felhasználónevét és jelszavát.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Felsorolja egy Azure Database for PostgreSQL-kiszolgáló konfigurációit. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Frissíti egy Azure Database for PostgreSQL-kiszolgáló konfigurációját. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | Megjeleníti egy Azure Database for PostgreSQL-kiszolgáló konfigurációját. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- Az Azure parancssori felületével kapcsolatos további információért tekintse meg az [Azure CLI dokumentációját](/cli/azure).
- További szkripteket [az Azure Database for PostgreSQL-hez készült Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- A kiszolgálóparaméterekkel kapcsolatos további információért lásd [a kiszolgálóparaméterek az Azure Portalon történő konfigurálását](../howto-configure-server-parameters-using-portal.md) ismertető szakaszt.
