---
title: Tárterület automatikus növekedése – Azure PowerShell – Azure Database for PostgreSQL
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a PowerShell használatával Azure Database for PostgreSQLban.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bc4655ce6cd572183cd92e1c8b2ac10e613ebd8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489965"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Tároló automatikus növekedése Azure Database for PostgreSQL-kiszolgálón a PowerShell használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló [elérje a tárolási korlátot](./concepts-pricing-tiers.md#reaching-the-storage-limit) , és csak olvasható legyen. A 100 GB vagy kevesebb kiépített tárterülettel rendelkező kiszolgálók esetében a méret 5 GB-kal nő, ha a szabad terület 10% alá esik. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a méret 5%-kal nő, ha a szabad terület 10 GB alatti. A maximális tárolási korlátok a [Azure Database for PostgreSQL díjszabási szintjeinek](./concepts-pricing-tiers.md#storage)tárolási szakaszában megadott módon érvényesek.

> [!IMPORTANT]
> Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. PostgreSql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Amint az az. PostgreSql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

## <a name="enable-postgresql-server-storage-auto-grow"></a>A PostgreSQL-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növelésének engedélyezése a meglévő kiszolgálókon a következő paranccsal:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Olvasási replikák létrehozása és kezelése a Azure Database for PostgreSQL a PowerShell használatával](howto-read-replicas-powershell.md).