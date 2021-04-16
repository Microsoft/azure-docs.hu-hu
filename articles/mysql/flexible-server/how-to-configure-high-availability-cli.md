---
title: Zónaredundáns magas rendelkezésre állás kezelése – Azure CLI – Azure Database for MySQL rugalmas kiszolgáló
description: Ez a cikk azt ismerteti, hogyan konfigurálható a zónaredundáns magas rendelkezésre állás Azure Database for MySQL rugalmas kiszolgálón az Azure CLI használatával.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509088"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Zónaredundáns magas rendelkezésre állás kezelése rugalmas Azure Database for MySQL Azure CLI használatával

> [!NOTE]
> Azure Database for MySQL rugalmas kiszolgáló nyilvános előzetes verzióban érhető el. 

Ez a cikk azt ismerteti, hogyan engedélyezheti vagy tilthatja le a zónaredundáns magas rendelkezésre állású konfigurációt a rugalmas kiszolgálón a kiszolgáló létrehozásakor. A kiszolgáló létrehozása után is letilthatja a zónaredundáns magas rendelkezésre állást. A zónaredundáns magas rendelkezésre állás kiszolgáló létrehozása után való engedélyezése nem támogatott.

A magas rendelkezésre állású funkció fizikailag elkülönített elsődleges és készenléti replikát biztosít különböző zónákban. További információért tekintse meg [a magas rendelkezésre állás fogalmait dokumentációt.](./concepts/../concepts-high-availability.md) A magas rendelkezésre állás engedélyezése vagy letiltása nem módosítja a többi beállítást, például a VNET-konfigurációt, a tűzfalbeállításokat és a biztonsági másolatok megőrzését. A magas rendelkezésre állás letiltása nem befolyásolja az alkalmazás kapcsolatát és működését.

> [!IMPORTANT]
> A zónaredundáns magas rendelkezésre állás korlátozott régiókban érhető el. Tekintse át a támogatott [régiókat itt.](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) 

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

## <a name="enable-high-availability-during-server-creation"></a>Magas rendelkezésre állás engedélyezése a kiszolgáló létrehozása során
Csak általános célú vagy memóriaoptimalált, magas rendelkezésre állású tarifacsomagokkal hozhat létre kiszolgálót. A magas rendelkezésre állást csak a létrehozási idő alatt engedélyezheti egy kiszolgálón.

**Használat:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Példa**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>A magas rendelkezésre állás letiltása

A magas rendelkezésre állást az [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) paranccsal tilthatja le. Vegye figyelembe, hogy a magas rendelkezésre állás letiltása csak akkor támogatott, ha a kiszolgáló magas rendelkezésre állású volt. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Példa**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Következő lépések

-   További tudnivalók az [üzletmenet folytonosságával kapcsolatban](./concepts-business-continuity.md)
-   Tudnivalók a [zónaredundáns magas rendelkezésre állásról](./concepts-high-availability.md)
