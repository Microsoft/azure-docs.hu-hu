---
title: Virtuális Azure Database for MySQL visszaállítása – Rugalmas kiszolgáló az Azure CLI használatával
description: Ez a cikk bemutatja, hogyan hajthatja végre a visszaállítási műveleteket Azure Database for MySQL az Azure CLI használatával.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509067"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Virtuális kiszolgáló időponthoz időben Azure Database for MySQL – Rugalmas kiszolgáló Azure CLI-val


> [!IMPORTANT]
> Azure Database for MySQL – A rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk lépésenként beható eljárásokat tartalmaz a rugalmas kiszolgálókon biztonsági másolatok használatával, időponthoz időben való helyreállításhoz.

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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Kiszolgáló visszaállítása biztonsági másolatból egy új kiszolgálóra

A következő parancs futtatásával visszaállíthat egy kiszolgálót egy legkorábbi meglévő biztonsági másolatra.

**Használat**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Példa:** Kiszolgáló visszaállítása ebből a biztonsági mentési ```2021-03-03T13:10:00Z``` pillanatképből.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
A visszaállításhoz szükséges idő a kiszolgálón tárolt adatok méretétől függ.

## <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása
A visszaállítás befejezése után a következő feladatokat kell végrehajtania, hogy a felhasználók és az alkalmazások ismét el tudjanak indulni:

- Ha az új kiszolgálót az eredeti kiszolgáló cseréjére szánta, irányítsa át az ügyfeleket és az ügyfélalkalmazásokat az új kiszolgálóra
- Győződjön meg arról, hogy a megfelelő VNet-szabályok vannak bevetve a felhasználók számára a csatlakozáshoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy a megfelelő bejelentkezések és adatbázisszintű engedélyek meg vannak biztosítva
- Riasztások konfigurálása az újonnan visszaállított kiszolgálónak megfelelően

## <a name="next-steps"></a>Következő lépések
További információ az [üzletmenet-folytonosságról](concepts-business-continuity.md)

