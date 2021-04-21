---
title: Virtuális hálózatok kezelése – Azure CLI – Azure Database for MySQL – Rugalmas kiszolgáló
description: Virtuális hálózatok létrehozása és kezelése virtuális Azure Database for MySQL – Rugalmas kiszolgáló az Azure CLI használatával
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7da8062f18d737af9d19df54863bc56c7268910c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776914"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Virtuális hálózatok létrehozása és kezelése virtuális Azure Database for MySQL – Rugalmas kiszolgáló az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for MySQL rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el

A rugalmas Azure Database for MySQL-kiszolgáló két, egymást kölcsönösen kizáró hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

- Nyilvános hozzáférés (engedélyezett IP-címek)
- Privát hozzáférés (VNet-integráció)

Ebben a cikkben a Privát hozzáféréssel **(VNet-integráció)** az Azure CLI használatával való mySQL-kiszolgáló létrehozására összpontosítunk. A *Privát hozzáférés (VNet-integráció)* segítségével üzembe helyezheti a rugalmas kiszolgálót a saját [Azure Virtual Network.](../../virtual-network/virtual-networks-overview.md) Az Azure Virtual Networks privát és biztonságos hálózati kommunikációt biztosít. A Privát hozzáférés szolgáltatásban a MySQL-kiszolgálóhoz való kapcsolódás csak a virtuális hálózaton belülre korlátozódik. További információ: Privát hozzáférés [(VNet-integráció)](./concepts-networking.md#private-access-vnet-integration).

A Azure Database for MySQL kiszolgálón csak a kiszolgáló létrehozása során helyezheti üzembe a kiszolgálót egy virtuális hálózaton és alhálózaton. Miután telepítette a rugalmas kiszolgálót egy virtuális hálózatra és alhálózatra, nem helyezheti át másik virtuális hálózatra, alhálózatra vagy nyilvános hozzáférésre *(engedélyezett IP-címekre).*

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amely a cikkben található lépések futtatására használható. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A lapot megnyithatja Cloud Shell böngészőlapon a következő megnyitásával: [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kattintson **a Másolás** gombra a kódblokkok másoláshoz, illessze be a Cloud Shell, majd az **Enter** billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az login paranccsal kell bejelentkeznie [a fiókjába.](/cli/azure/reference-index#az_login) Figyelje **meg az Id** tulajdonságot, amely az Azure-fiók előfizetés-azonosítójára vonatkozik. 

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókjában [az az account set paranccsal.](/cli/azure/account#az_account_set) Jegyezze fel  az az **login** output parancs id értékét, amely a subscription argumentum értékeként lesz használva a parancsban.  Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Rugalmas Azure Database for MySQL kiszolgáló létrehozása a cli használatával
A paranccsal privát hozzáféréssel (VNet-integrációval) hozhatja létre a rugalmas `az mysql flexible-server` *kiszolgálót.* Ez a parancs privát hozzáférést (VNet-integrációt) használ alapértelmezett kapcsolódási módszerként. Ha nem ad meg virtuális hálózatot és alhálózatot, a rendszer létrehoz egyet sem. A már meglévő virtuális hálózatot és alhálózatot is meg lehet adni az alhálózat azonosítójával. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Az alábbi példákban látható módon többféle módon hozhat létre rugalmas kiszolgálót a CLI használatával.

>[!Important]
> Ezzel a paranccsal delegálhatja az alhálózatot a **Microsoft.DBforMySQL/flexibleServers kiszolgálóra.** Ez a delegálás azt jelenti, hogy csak a rugalmas Azure Database for MySQL-kiszolgálók használhatják az alhálózatot. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható.
>

A konfigurálható [CLI-paraméterek](/cli/azure/mysql/flexible-server) teljes listájáért tekintse meg az Azure CLI referenciadokumentációját. Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

- Rugalmas kiszolgáló létrehozása alapértelmezett virtuális hálózat és alapértelmezett címelőtagú alhálózat használatával
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Hozzon létre egy rugalmas kiszolgálót a már meglévő virtuális hálózat és alhálózat használatával. Ha a megadott virtuális hálózat és alhálózat nem létezik, a rendszer létrehoz egy virtuális hálózatot és egy alapértelmezett címelőtaggal megadott alhálózatot.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Hozzon létre egy rugalmas kiszolgálót a már meglévő virtuális hálózat, alhálózat és az alhálózat-azonosító használatával. A megadott alhálózaton nem lehet más üzembe helyezett erőforrás, és ez az alhálózat delegálva lesz a **Microsoft.DBforMySQL/flexibleServers** kiszolgálóra, ha még nincs delegálva.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > A virtuális hálózatnak és az alhálózatnak ugyanabban a régióban és előfizetésben kell lennie, mint a rugalmas kiszolgálónak.
<
- Hozzon létre egy rugalmas kiszolgálót új virtuális hálózattal, nem alapértelmezett címelőtaggal és alhálózattal.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
A konfigurálható [CLI-paraméterek](/cli/azure/mysql/flexible-server) teljes listájáért tekintse meg az Azure CLI referenciadokumentációját.


## <a name="next-steps"></a>Következő lépések
- További információ a [rugalmas Azure Database for MySQL hálózatról.](./concepts-networking.md)
- [Rugalmas kiszolgálói virtuális Azure Database for MySQL létrehozása és kezelése a Azure Portal.](./how-to-manage-virtual-network-portal.md)
- További információ a [rugalmas Azure Database for MySQL virtuális hálózatról.](./concepts-networking.md#private-access-vnet-integration)