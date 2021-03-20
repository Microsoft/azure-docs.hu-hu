---
title: Kiszolgáló paramétereinek konfigurálása – Azure PowerShell-Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatás paramétereit Azure Database for MariaDB a PowerShell használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ace6306bec4c79cbce0a1572360db1acd2cea97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662457"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Kiszolgáló paramétereinek konfigurálása Azure Database for MariaDB a PowerShell használatával

Egy Azure Database for MariaDB kiszolgáló konfigurációs paramétereit a PowerShell használatával listázhatja, megjelenítheti és frissítheti. A motor konfigurációjának egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható.

>[!Note]
> A kiszolgálóparaméterek a kiszolgáló szintjén frissíthetők globálisan. Használja az [Azure CLI-t](./howto-configure-server-parameters-cli.md), a [PowerShellt](./howto-configure-server-parameters-using-powershell.md) vagy az [Azure Portalt](./howto-server-parameters.md).

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása

A kiszolgáló összes módosítható paraméterének és értékének listázásához futtassa a `Get-AzMariaDbConfiguration` parancsmagot.

Az alábbi példa felsorolja a kiszolgáló **mydemoserver** tartozó kiszolgálói konfigurációs paramétereket az erőforráscsoport **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Az egyes felsorolt paraméterek definícióját a [kiszolgálói rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)MariaDB-hivatkozás szakasza tartalmazza.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek

Egy adott konfigurációs paraméter részleteinek megjelenítéséhez futtassa a `Get-AzMariaDbConfiguration` parancsmagot, és adja meg a **Name** paramétert.

Ez a példa a **lassú \_ lekérdezési \_ napló** kiszolgálójának konfigurációs paraméterének részleteit jeleníti meg a kiszolgáló **mydemoserver** az erőforráscsoport **myresourcegroup** alatt.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása

Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MariaDB-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja a `Update-AzMariaDbConfiguration` parancsmagot.

A **lassú \_ lekérdezési \_ napló** kiszolgáló-konfigurációs paraméterének frissítése a kiszolgáló **mydemoserver** az erőforráscsoport **myresourcegroup** területen.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A tároló automatikus növekedése Azure Database for MariaDB-kiszolgálón a PowerShell használatával](howto-auto-grow-storage-powershell.md).