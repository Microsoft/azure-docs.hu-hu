---
title: Auditnaplók és lassú lekérdezési naplók konfigurálása az Azure CLI-Azure Database for MySQL – Rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja és férhet hozzá a lassú lekérdezési naplókhoz Azure Database for MySQL rugalmas kiszolgálón az Azure CLI-ről.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509087"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Lassú lekérdezési naplók konfigurálása Azure Database for MySQL – Rugalmas kiszolgáló az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

A cikk bemutatja, hogyan konfigurálhatja [a rugalmas](concepts-slow-query-logs.md) MySQL-kiszolgáló lassú lekérdezési naplóit az Azure CLI használatával. 

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

## <a name="configure-slow-query-logs"></a>Lassú lekérdezési naplók konfigurálása

>[!IMPORTANT]
> Javasoljuk, hogy csak a naplózási célokhoz szükséges eseménytípusokat és felhasználókat naplózhatja, hogy a kiszolgáló teljesítményét ne befolyásolja jelentősen.

Engedélyezze és konfigurálja a kiszolgáló lassú lekérdezési naplóit.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Következő lépések
- Tudnivalók a [lassú lekérdezési naplókról](concepts-slow-query-logs.md)
