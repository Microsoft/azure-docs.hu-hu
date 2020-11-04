---
title: Kapcsolatok karakterláncának létrehozása a PowerShell-Azure Database for MySQL
description: Ez a cikk egy Azure PowerShell példát mutat be a Azure Database for MySQLhoz való kapcsolódáshoz használt kapcsolati karakterlánc létrehozásához.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: fcba366a0322c3c1b5c6dcdf0fc3571646053fad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337183"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Azure Database for MySQL kapcsolódási karakterlánc létrehozása a PowerShell-lel

Ez a cikk bemutatja, hogyan lehet létrehozni egy Azure Database for MySQL-kiszolgáló kapcsolódási karakterláncát. A kapcsolati karakterlánc használatával számos különböző alkalmazásból csatlakozhat egy Azure Database for MySQLhoz.

## <a name="requirements"></a>Követelmények

Ez a cikk kiindulási pontként a következő útmutatóban létrehozott erőforrásokat használja:

* [Rövid útmutató: Azure Database for MySQL-kiszolgáló létrehozása a PowerShell használatával](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A `Get-AzMySqlConnectionString` parancsmag egy kapcsolati sztring létrehozásához használható az alkalmazások Azure Database for MySQLhoz való csatlakoztatásához. A következő példa a **mydemoserver** -ból származó php-ügyfélhez tartozó kapcsolatok karakterláncát adja vissza.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

A paraméter érvényes értékei a `Client` következők:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Database for MySQL kiszolgáló paramétereinek testreszabása a PowerShell használatával](howto-configure-server-parameters-using-powershell.md)
