---
title: Újraindítás/leállítás/indítás – Azure Portal – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan indíthatóak újra,le és indíthatóak Azure Database for MySQL az Azure CLI használatával.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509068"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Fürt újraindítása/leállítása/Azure Database for MySQL – Rugalmas kiszolgáló (előzetes verzió)

> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan indítható újra, indítható el és állíthat le rugalmas kiszolgáló az Azure CLI használatával.

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

## <a name="stop-a-running-server"></a>Futó kiszolgáló leállítása
A kiszolgáló leállításhoz futtassa a  ```az mysql flexible-server stop``` parancsot. Ha helyi környezetet [használ,](/cli/azure/config/param-persist)nem kell argumentumokat adnia.

**Használat:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Helyi környezet nélküli példa:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Példa helyi kontextussal:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Leállított kiszolgáló elindítani
Egy kiszolgáló futtatásához futtassa a  ```az mysql flexible-server start``` parancsot. Ha helyi környezetet [használ,](/cli/azure/config/param-persist)nem kell argumentumokat adnia.

**Használat:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Helyi környezet nélküli példa:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Példa helyi kontextussal:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> A kiszolgáló sikeres újraindítása után az összes felügyeleti művelet elérhetővé válik a rugalmas kiszolgáló számára.

## <a name="restart-a-server"></a>Kiszolgáló újraindítása
A kiszolgáló újraindításához futtassa a  ```az mysql flexible-server restart``` parancsot. Ha helyi környezetet [használ,](/cli/azure/config/param-persist)nem kell argumentumokat adnia.

**Használat:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Helyi környezet nélküli példa:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Példa helyi kontextussal:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> A kiszolgáló sikeres újraindítása után az összes felügyeleti művelet elérhetővé válik a rugalmas kiszolgáló számára.

## <a name="next-steps"></a>Következő lépések
- További információ a [rugalmas Azure Database for MySQL hálózatról](./concepts-networking.md)
- [Rugalmas kiszolgálói virtuális Azure Database for MySQL létrehozása és kezelése a Azure Portal.](./how-to-manage-virtual-network-portal.md)

