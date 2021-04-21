---
title: Azure CLI-szkript – Méretezés és monitor Azure Database for PostgreSQL
description: Azure CLI-szkriptminta – Azure Database for PostgreSQL-kiszolgáló méretezése más teljesítményszintre a mérőszámok lekérdezése után.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 56fd88ab658e59cccb14a35559d1793bc3ad1aa0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778427"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Egyetlen PostgreSQL-kiszolgáló monitorozása és méretezése az Azure CLI-vel
Ez a CLI-példaszk szkript egyetlen kiszolgáló számítási és tárolási Azure Database for PostgreSQL a metrikák lekérdezése után. A számítás felfelé vagy lefelé is skálázható. A tárterület csak felskálásos. 

> [!IMPORTANT] 
> A tárterületet csak felfelé lehet skálázni, lefelé nem.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript
Frissítse a szkriptet az előfizetés azonosítójával.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás a szkript futtatása után. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Szkript ismertetése
Ez a szkript a következő táblában leírt parancsokat használja:

| **Parancs** | **Megjegyzések** |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Létrehoz egy PostgreSQL-kiszolgálót, amelyen az adatbázisok futnak. |
| [az postgres server update](/cli/azure/postgres/server#az_postgres_server_update) | Frissíti a PostgreSQL-kiszolgáló tulajdonságait. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Listázza az erőforrások metrikaértékét. |
| [az group delete](/cli/azure/group) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for PostgreSQL és tárolásról](../concepts-pricing-tiers.md)
- További szkripteket [az Azure Database for PostgreSQL-hez készült Azure CLI-példák](../sample-scripts-azure-cli.md) között találhat.
- További információ az [Azure CLI-ről](/cli/azure)
