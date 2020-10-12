---
title: Olvasási replikák kezelése – Azure PowerShell-Azure Database for PostgreSQL
description: Ismerje meg, hogyan állíthat be és kezelhet olvasási replikákat Azure Database for PostgreSQL a PowerShell használatával.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 26c6f70f92e4c372c0ff6afbcbb3c0bb284e2f6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704786"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Olvasási replikák létrehozása és kezelése a Azure Database for PostgreSQL a PowerShell használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet olvasási replikákat a Azure Database for PostgreSQL szolgáltatásban a PowerShell használatával. Az olvasási replikákkal kapcsolatos további tudnivalókért tekintse meg az [áttekintést](concepts-read-replicas.md).

## <a name="azure-powershell"></a>Azure PowerShell

Az olvasási replikákat a PowerShell használatával hozhatja létre és kezelheti.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Az az [PowerShell-modul](https://docs.microsoft.com/powershell/azure/install-az-ps) helyileg vagy [Azure Cloud Shell](https://shell.azure.com/) telepítve a böngészőben
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az az. PostgreSql PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Amint az az. PostgreSql PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha a PowerShell helyi használatát választja, kapcsolódjon az Azure-fiókjához a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for PostgreSQL-kiszolgálókon érhető el. Győződjön meg arról, hogy az elsődleges kiszolgáló az alábbi díjszabási szintek egyikében található.

### <a name="create-a-read-replica"></a>Olvasási replika létrehozása

A következő paranccsal hozhat létre olvasási replika-kiszolgálót:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

A `New-AzPostgreSqlServerReplica` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Az az erőforráscsoport, amelyben a replika-kiszolgáló létrejön.  |
| Name (Név) | mydemoreplicaserver | A létrehozott új replika-kiszolgáló neve. |

Egy több régióból származó olvasási replika létrehozásához használja a **Location** paramétert. Az alábbi példa egy replikát hoz létre az **USA nyugati** régiójában.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Ha többet szeretne megtudni arról, hogy mely régiókban hozhat létre replikát, látogasson el a [replika áttekintése című cikkben](concepts-read-replicas.md).

Alapértelmezés szerint az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint az elsődleges, kivéve, ha az **SKU** paraméter meg van adva.

> [!NOTE]
> Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját az elsődlegesnél egyenlő vagy nagyobb értékkel kell megőrizni, hogy a replika képes legyen lépést tartani a főkiszolgálóval.

### <a name="list-replicas-for-a-primary-server"></a>Elsődleges kiszolgáló replikáinak listázása

Egy adott elsődleges kiszolgáló összes replikájának megtekintéséhez futtassa a következő parancsot:

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

A `Get-AzMariaDReplica` parancshoz a következő paraméterek szükségesek:

| Beállítás | Példaérték | Leírás  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  Az az erőforráscsoport, amelybe a replika-kiszolgáló létre lesz hozva.  |
| ServerName | mydemoserver | Az elsődleges kiszolgáló neve vagy azonosítója. |

### <a name="delete-a-replica-server"></a>Replika-kiszolgáló törlése

Az olvasási replika kiszolgáló törlését a parancsmag futtatásával teheti meg `Remove-AzPostgreSqlServer` .

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Elsődleges kiszolgáló törlése

> [!IMPORTANT]
> Az elsődleges kiszolgáló törlése leállítja az összes másodpéldány-kiszolgálót, és maga törli az elsődleges kiszolgálót. A replikakiszolgálókból különálló kiszolgálók lesznek, amelyek az olvasási és írási műveleteket egyaránt támogatják.

Elsődleges kiszolgáló törléséhez futtathatja a `Remove-AzPostgreSqlServer` parancsmagot.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL kiszolgáló újraindítása a PowerShell-lel](howto-restart-server-powershell.md)
