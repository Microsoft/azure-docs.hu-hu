---
title: 'Gyors útmutató: szinapszis SQL-készlet létrehozása az Azure CLI-vel'
description: Gyorsan létrehozhat egy szinapszis SQL-készletet egy kiszolgálói szintű tűzfalszabály használatával az Azure CLI-vel.
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.custom: azure-synapse
ms.openlocfilehash: c17bfc06e76bad7730707530001ebeccea539c37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602264"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Gyors útmutató: szinapszis SQL-készlet létrehozása az Azure CLI-vel

Hozzon létre egy szinapszis SQL-készletet (adattárház) az Azure szinapszis Analyticsben az Azure CLI használatával.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Első lépések

Az alábbi parancsokkal jelentkezhet be az Azure-ba, és beállíthat egy erőforráscsoportot.

1. Ha helyi telepítést használ, futtassa az az [login](/cli/azure/reference-index#az_login) parancsot az Azure-ba való bejelentkezéshez:

   ```azurecli
   az login
   ```

1. Ha szükséges, használja az az [Account set](/cli/azure/account#az_account_set) parancsot az előfizetés kiválasztásához:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Futtassa az az [Group Create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Hozzon létre egy [logikai SQL-kiszolgálót](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) az az [SQL Server Create](/cli/azure/sql/server#az_sql_server_create) parancs használatával:

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy [kiszolgálói szintű tűzfalszabály-szabályt](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A kiszolgálói szintű tűzfalszabályok lehetővé teszik egy külső alkalmazás, például SQL Server Management Studio vagy a SQLCMD segédprogram számára az SQL-készlethez való kapcsolódást az SQL Pool szolgáltatás tűzfala segítségével.

Futtassa az az [SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) parancsot egy tűzfalszabály létrehozásához:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

Ebben a példában a tűzfal csak más Azure-erőforrások számára van megnyitva. A külső csatlakozási lehetőségek engedélyezéséhez módosítsa az IP-címet egy, az Ön környezetének megfelelő címre. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

> [!NOTE]
> Az SQL-végpontok a 1433-es porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, lehetséges, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, akkor nem fog tudni csatlakozni a kiszolgálóhoz, ha az informatikai részleg megnyitja a 1433-es portot.
>

## <a name="create-and-manage-your-sql-pool"></a>SQL-készlet létrehozása és kezelése

Hozza létre az SQL-készletet. Ez a példa a DW100c-t használja a szolgáltatás céljára, amely az SQL-készlet alacsonyabb díjas kiindulási pontja.

> [!NOTE]
> Egy korábban létrehozott munkaterületre van szüksége. További információ: gyors útmutató [: Azure szinapszis-munkaterület létrehozása az Azure CLI-vel](../quickstart-create-workspace-cli.md).

Az SQL-készlet létrehozásához használja az az [szinapszis SQL Pool Create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) parancsot:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

További információ a paraméter beállításairól: [az szinapszis SQL Pool](/cli/azure/ext/synapse/synapse/sql/pool).

Az SQL-készleteket az az [szinapszis SQL Pool List](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list) parancs használatával tekintheti meg:

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Egy meglévő készlet frissítéséhez használja az az [szinapszis SQL Pool Update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) parancsot:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

A készlet szüneteltetéséhez használja az az [szinapszis SQL Pool pause](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) parancsot:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

A szüneteltetett készlet indításához használja az az [szinapszis SQL Pool Resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) parancsot:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Meglévő SQL-készlet eltávolításához használja az az [szinapszis SQL Pool delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete) parancsot:

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató erre a rövid útmutatóra épül.

> [!TIP]
> Ha azt tervezi, hogy továbbra is a későbbi gyors útmutatók használatával dolgozik, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a folytatást, az az [Group delete](/cli/azure/group#az_group_delete) paranccsal törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ezzel létrehozott egy SQL-készletet, létrehozott egy tűzfalszabályet, és csatlakoztatta az SQL-készlethez. További információért folytassa az [SQL-készletbe való betöltéssel](./load-data-from-azure-blob-storage-using-copy.md) foglalkozó cikket.