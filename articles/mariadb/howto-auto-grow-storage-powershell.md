---
title: Tárterület automatikus növekedése – Azure PowerShell – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a PowerShell használatával Azure Database for MariaDBban.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87438c406ea2b31942003d9211cf5e285628f15d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665157"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>Tároló automatikus növekedése Azure Database for MariaDB-kiszolgálón a PowerShell használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló [elérje a tárolási korlátot](concepts-pricing-tiers.md#reaching-the-storage-limit) , és csak olvasható legyen. A 100 GB vagy kevesebb kiépített tárterülettel rendelkező kiszolgálók esetében a méret 5 GB-kal nő, ha a szabad terület 10% alá esik. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a méret 5%-kal nő, ha a szabad terület 10 GB alatti. A maximális tárolási korlátok a [Azure Database for MariaDB díjszabási szintjeinek](concepts-pricing-tiers.md#storage)tárolási szakaszában megadott módon érvényesek.

> [!IMPORTANT]
> Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

## <a name="enable-mariadb-server-storage-auto-grow"></a>A MariaDB-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növelésének engedélyezése a meglévő kiszolgálókon a következő paranccsal:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Olvasási replikák létrehozása és kezelése a Azure Database for MariaDB a PowerShell használatával](howto-read-replicas-powershell.md).
