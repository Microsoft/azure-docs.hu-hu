---
title: Azure CLI-szkript – kiszolgálói konfigurációk módosítása (PostgreSQL)
description: Ez a CLI-példaszkript felsorol minden elérhető kiszolgálókonfigurációs lehetőséget, és frissíti az egyik lehetőség értékét.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 722db134d6cd5287a0eddee844ccbf6b6ef98a10
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709041"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure Database for PostgreSQL-kiszolgáló konfigurációinak felsorolása és frissítése az Azure CLI használatával
Ez a CLI-példaszkript felsorolja egy Azure Database for PostgreSQL-kiszolgáló összes elérhető konfigurációs paraméterét, valamint azok engedélyezett értékeit, és beállítja a *log_retention_days* paramétert egy, az alapértelmezett értéktől eltérő értékre.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi futtatását választja, akkor ehhez a cikkhez az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ellenőrizze a verziót az `az --version` parancs futtatásával. Az Azure CLI telepítéséhez vagy verziófrissítéséhez lásd: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

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
