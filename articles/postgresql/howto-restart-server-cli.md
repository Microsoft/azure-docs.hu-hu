---
title: Kiszolgáló újraindítása – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-egy kiszolgálót az Azure CLI használatával
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e812b7872dd4d41d9a6cb87d75403524106c5981
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706864"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Azure Database for PostgreSQL újraindítása – egyetlen kiszolgáló az Azure CLI használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.
 
> [!NOTE] 
> Az újraindítás befejezéséhez szükséges idő a PostgreSQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt. Az ellenőrzőpontok gyakoriságát is érdemes lehet emelni. Az ellenőrzőpontokkal kapcsolatos paraméterek értékeit is beállíthatja, beleértve a következőket: `max_wal_size` . Azt is javasoljuk, hogy `CHECKPOINT` a-kiszolgáló újraindítása előtt futtassa a parancsot.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez:
- Hozzon létre egy [Azure Database for PostgreSQL-kiszolgálót](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="restart-the-server"></a>Kiszolgáló újraindítása

Indítsa újra a kiszolgálót a következő paranccsal:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>További lépések

Útmutató [Paraméterek beállításához a Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
