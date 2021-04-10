---
title: Kiszolgáló paramétereinek konfigurálása – Azure PowerShell-Azure Database for PostgreSQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatás paramétereit Azure Database for PostgreSQL a PowerShell használatával.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6e9bf55aa46e2206928a5f93285a4001dc731f64
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604294"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Azure Database for PostgreSQL kiszolgáló paramétereinek testreszabása a PowerShell használatával

Egy Azure Database for PostgreSQL kiszolgáló konfigurációs paramétereit a PowerShell használatával listázhatja, megjelenítheti és frissítheti. A motor konfigurációjának egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. PostgreSql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Amint az az. PostgreSql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása

A kiszolgáló összes módosítható paraméterének és értékének listázásához futtassa a `Get-AzPostgreSqlConfiguration` parancsmagot.

Az alábbi példa felsorolja a kiszolgáló **mydemoserver** tartozó kiszolgálói konfigurációs paramétereket az erőforráscsoport **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Az egyes felsorolt paraméterek definícióját lásd: a [környezeti változókról](https://www.postgresql.org/docs/12/libpq-envars.html)szóló PostgreSQL-hivatkozás.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek

Egy adott konfigurációs paraméter részleteinek megjelenítéséhez futtassa a `Get-AzPostgreSqlConfiguration` parancsmagot, és adja meg a **Name** paramétert.

Ez a példa a **lassú \_ lekérdezési \_ napló** kiszolgálójának konfigurációs paraméterének részleteit jeleníti meg a kiszolgáló **mydemoserver** az erőforráscsoport **myresourcegroup** alatt.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása

Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a PostgreSQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja a `Update-AzPostgreSqlConfiguration` parancsmagot.

A **lassú \_ lekérdezési \_ napló** kiszolgáló-konfigurációs paraméterének frissítése a kiszolgáló **mydemoserver** az erőforráscsoport **myresourcegroup** területen.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A tároló automatikus növekedése Azure Database for PostgreSQL-kiszolgálón a PowerShell használatával](howto-auto-grow-storage-powershell.md).