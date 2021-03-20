---
title: Tárterület automatikus növekedése – Azure PowerShell – Azure Database for MySQL
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a PowerShell használatával Azure Database for MySQLban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8c9f6f66e484fbd9ebe5c15934936d6e5c59073
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542031"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Tároló automatikus növekedése Azure Database for MySQL-kiszolgálón a PowerShell használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A tárterület automatikus növekedése megakadályozza, hogy a kiszolgáló [elérje a tárolási korlátot](./concepts-pricing-tiers.md#reaching-the-storage-limit) , és csak olvasható legyen. A 100 GB vagy kevesebb kiépített tárterülettel rendelkező kiszolgálók esetében a méret 5 GB-kal nő, ha a szabad terület 10% alá esik. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a méret 5%-kal nő, ha a szabad terület 10 GB alatti. A maximális tárolási korlátok a [Azure Database for MySQL díjszabási szintjeinek](./concepts-pricing-tiers.md#storage)tárolási szakaszában megadott módon érvényesek.

> [!IMPORTANT]
> Ne feledje, hogy a tárterület csak akkor méretezhető, ha nem.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MySql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Amint az az. MySql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával.

## <a name="enable-mysql-server-storage-auto-grow"></a>A MySQL-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növelésének engedélyezése a meglévő kiszolgálókon a következő paranccsal:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Olvasási replikák létrehozása és kezelése a Azure Database for MySQL a PowerShell használatával](howto-read-replicas-powershell.md).