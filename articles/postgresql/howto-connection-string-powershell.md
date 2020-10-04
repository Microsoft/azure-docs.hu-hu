---
title: Kapcsolatok karakterláncának létrehozása a PowerShell-Azure Database for PostgreSQL
description: Ez a cikk egy Azure PowerShell példát mutat be a Azure Database for PostgreSQLhoz való kapcsolódáshoz használt kapcsolati karakterlánc létrehozásához.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708169"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Azure Database for PostgreSQL kapcsolódási karakterlánc létrehozása a PowerShell-lel

Ez a cikk bemutatja, hogyan lehet létrehozni egy Azure Database for PostgreSQL-kiszolgáló kapcsolódási karakterláncát. A kapcsolati karakterlánc használatával számos különböző alkalmazásból csatlakozhat egy Azure Database for PostgreSQLhoz.

## <a name="requirements"></a>Követelmények

Ez a cikk kiindulási pontként a következő útmutatóban létrehozott erőforrásokat használja:

* [Rövid útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása a PowerShell használatával](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A `Get-AzPostgreSqlConnectionString` parancsmag egy kapcsolati sztring létrehozásához használható az alkalmazások Azure Database for PostgreSQLhoz való csatlakoztatásához. A következő példa a **mydemoserver**-ból származó php-ügyfélhez tartozó kapcsolatok karakterláncát adja vissza.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

A paraméter érvényes értékei a `Client` következők:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL kiszolgáló paramétereinek testreszabása a PowerShell használatával](howto-configure-server-parameters-using-powershell.md)
