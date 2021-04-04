---
title: Kiszolgáló újraindítása – Azure PowerShell – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MariaDB kiszolgálót a PowerShell használatával.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14cde5b1302c46b819bb7d841fb5b84a43c580c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664868"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Azure Database for MariaDB kiszolgáló újraindítása a PowerShell-lel

Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for MariaDB-kiszolgálót. Előfordulhat, hogy a kiszolgáló karbantartás miatt újra kell indítania a kiszolgálót, ami rövid kimaradást okoz a művelet során.

A kiszolgáló újraindítása le van tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.

Az újraindítás befejezéséhez szükséges idő a MariaDB helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. MariaDb PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Amint az az. MariaDb PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Kiszolgáló újraindítása

Indítsa újra a kiszolgálót a következő paranccsal:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for MariaDB-kiszolgáló létrehozása a PowerShell használatával](quickstart-create-mariadb-server-database-using-azure-powershell.md)