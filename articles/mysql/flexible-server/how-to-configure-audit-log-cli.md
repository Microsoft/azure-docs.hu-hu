---
title: Auditnaplók konfigurálása az Azure CLI-Azure Database for MySQL – Rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja és férhet hozzá az auditnaplókhoz Azure Database for MySQL rugalmas kiszolgálón az Azure CLI-ről.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509063"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Auditnaplók konfigurálása és elérése Azure Database for MySQL – Rugalmas kiszolgáló az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan konfigurálhatja [a](concepts-audit-logs.md) rugalmas MySQL-kiszolgáló auditnaplóit az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- Telepítse vagy frissítse az Azure CLI-t a legújabb verzióra. Lásd: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)
-  Jelentkezzen be az Azure-fiókba [az az login paranccsal.](/cli/azure/reference-index#az-login) Figyelje meg **az id** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** vonatkozik.

    ```azurecli-interactive
    az login
    ````

- Ha több előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben létre szeretné hozni a kiszolgálót az ```az account set``` paranccsal.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Hozzon létre egy rugalmas MySQL-kiszolgálót, ha még nem hozott létre egyet az ```az mysql flexible-server create``` paranccsal.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-audit-logging"></a>Naplózás konfigurálása

>[!IMPORTANT]
> Javasoljuk, hogy csak a naplózási célokhoz szükséges eseménytípusokat és felhasználókat naplózhatja, hogy a kiszolgáló teljesítményét ne befolyásolja jelentősen.

Naplónaplózás engedélyezése és konfigurálása.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Következő lépések
- További információ az [auditnaplókról](concepts-audit-logs.md)
