---
title: 'Rövid útmutató: Virtuális Synapse SQL létrehozása az Azure CLI használatával'
description: Gyorsan létrehozhat egy Synapse SQL-készletet egy kiszolgálószintű tűzfalsokkal az Azure CLI használatával.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 532c06cc49f9fddab09c6678ee5fff9fe32347cb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565933"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Rövid útmutató: Virtuális Synapse SQL létrehozása az Azure CLI használatával

Hozzon létre Synapse SQL készletet (adattárházat) a Azure Synapse Analytics Azure CLI használatával.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Első lépések

Ezekkel a parancsokkal jelentkezzen be az Azure-ba, és állítson be egy erőforráscsoportot.

1. Ha helyi telepítést használ, futtassa [az az login](/cli/azure/reference-index#az_login) parancsot az Azure-ba való bejelentkezéshez:

   ```azurecli
   az login
   ```

1. Ha szükséges, az [az account set paranccsal](/cli/azure/account#az_account_set) válassza ki az előfizetését:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa [az az group create parancsot](/cli/azure/group#az_group_create) egy erőforráscsoport létrehozásához:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Hozzon létre [egy logikai SQL-kiszolgálót](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) [az az sql server create paranccsal:](/cli/azure/sql/server#az_sql_server_create)

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre [egy kiszolgálószintű tűzfalszabályt.](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) A kiszolgálószintű tűzfalszabályok lehetővé teszik, hogy egy külső alkalmazás, például az SQL Server Management Studio vagy az SQLCMD segédprogram csatlakozzon egy SQL-készlethez az SQL-készlet szolgáltatás tűzfalán keresztül.

Futtassa [az az sql server firewall-rule create parancsot](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) egy tűzfalszabály létrehozásához:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

Ebben a példában a tűzfal csak más Azure-erőforrások számára van megnyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

> [!NOTE]
> Az SQL-végpontok az 1433-as porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, előfordulhat, hogy a hálózati tűzfal nem engedélyezette a kimenő forgalmat az 1433-as porton keresztül. Ha igen, csak akkor tud csatlakozni a kiszolgálóhoz, ha az IT-részleg megnyitja az 1433-as portot.
>

## <a name="create-and-manage-your-sql-pool"></a>AZ SQL-készlet létrehozása és kezelése

Hozza létre az SQL-készletet. Ez a példa a DW100c szolgáltatási célt használja, amely az SQL-készlet alacsonyabb költségű kiindulási pontja.

> [!NOTE]
> Szüksége van egy korábban létrehozott munkaterületre. További információ: [Rövid útmutató: Azure Synapse-munkaterület létrehozása az Azure CLI-val.](../quickstart-create-workspace-cli.md)

Használja az [az synapse sql pool create parancsot](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) az SQL-készlet létrehozásához:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

További információ a paraméterbeállításokról: [az synapse sql pool.](/cli/azure/ext/synapse/synapse/sql/pool)

Az SQL-készleteket az [az synapse sql pool list](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list) paranccsal láthatja:

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Meglévő [készlet frissítéséhez használja az az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) parancsot:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

A készlet [szüneteltetése az az synapse sql pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) paranccsal:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Szüneteltetett készletet az [az synapse sql pool resume paranccsal](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) indít el:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Meglévő SQL-készlet eltávolításához használja [az az synapse sql pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete) parancsot:

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül.

> [!TIP]
> Ha azt tervezi, hogy a későbbi rövid útmutató oktatóanyagokkal dolgozik tovább, akkor ne takarítsa meg az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, az [az group delete](/cli/azure/group#az_group_delete) paranccsal törölheti a rövid útmutatóban létrehozott összes erőforrást.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Létrehozott egy SQL-készletet, létrehozott egy tűzfalszabályt, és csatlakozott az SQL-készlethez. További információért folytassa az Adatok betöltése [AZ SQL-készletbe című cikkel.](./load-data-from-azure-blob-storage-using-copy.md)
