---
title: Virtuális hálózatok kezelése – Azure CLI – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Virtuális hálózatok létrehozása és kezelése Azure Database for PostgreSQL-rugalmas kiszolgálóhoz az Azure CLI használatával
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 2bc7bbd7a50b5771d794fbf35844311e3deddbbd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226973"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Virtuális hálózatok létrehozása és kezelése Azure Database for PostgreSQL-rugalmas kiszolgálóhoz az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL – a rugalmas kiszolgáló kétféle kölcsönösen kizárólagos hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

* Nyilvános hozzáférés (engedélyezett IP-címek)
* Privát hozzáférés (VNet-integráció)

Ebben a cikkben a PostgreSQL-kiszolgáló és a **privát hozzáférés (VNet-integráció)** Azure CLI használatával történő létrehozását fogjuk összpontosítani. A *privát hozzáféréssel (VNet-integrációval)* a rugalmas kiszolgáló üzembe helyezhető saját Azure- [Virtual Network](../../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network privát és biztonságos hálózati kommunikációt biztosít. A privát hozzáférésben a PostgreSQL-kiszolgálóhoz való csatlakozás csak a virtuális hálózaton belülre korlátozódik. További tudnivalókért tekintse meg a [privát hozzáférés (VNet-integráció)](./concepts-networking.md#private-access-vnet-integration)című témakört.

Azure Database for PostgreSQL – rugalmas kiszolgáló esetén a kiszolgálót csak virtuális hálózatra és alhálózatra lehet telepíteni a kiszolgáló létrehozása során. A rugalmas kiszolgáló virtuális hálózatra és alhálózatra történő telepítése után nem helyezhető át másik virtuális hálózatra, alhálózatra vagy *nyilvános elérésre (engedélyezett IP-címek)*.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amellyel a cikkben ismertetett lépéseket futtathatja. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngésző lapon is megnyithatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd válassza az **ENTER billentyűt** a futtatásához.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az [login](/cli/azure/reference-index#az-login) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account#az-account-set) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Azure Database for PostgreSQL rugalmas kiszolgáló létrehozása a parancssori felület használatával
A `az postgres flexible-server` paranccsal hozhatja létre a rugalmas kiszolgálót a *privát hozzáféréssel (VNet-integráció)*. Ez a parancs a privát hozzáférést (VNet-integráció) használja alapértelmezett kapcsolati módszerként. Ha nincs megadva, a rendszer létrehoz egy virtuális hálózatot és egy alhálózatot. A már meglévő virtuális hálózatot és alhálózatot is megadhatja alhálózati azonosító használatával. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Az alábbi példákban látható módon létrehozhat egy rugalmas kiszolgálót a CLI használatával.

>[!Important]
> Ennek a parancsnak a használatával delegálja az alhálózatot a **Microsoft. DBforPostgreSQL/flexibleServers**. Ez a delegálás azt jelenti, hogy csak Azure Database for PostgreSQL rugalmas kiszolgálók használhatják ezt az alhálózatot. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható.
>
Tekintse meg az Azure CLI dokumentációját <!--FIXME --> a konfigurálható CLI-paraméterek teljes listájához. Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

- Rugalmas kiszolgáló létrehozása alapértelmezett virtuális hálózattal, az alapértelmezett előtaggal rendelkező alhálózat használatával
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Hozzon létre egy rugalmas kiszolgálót, amely már meglévő virtuális hálózatot és alhálózatot használ. Ha a megadott virtuális hálózat és alhálózat nem létezik, akkor a virtuális hálózat és az alhálózat alapértelmezett előtaggal lesz létrehozva.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Hozzon létre egy rugalmas kiszolgálót, amely már meglévő virtuális hálózatot, alhálózatot és alhálózati azonosítót használ. A megadott alhálózathoz nem tartozhat más erőforrás, és az alhálózat delegálva lesz a **Microsoft. DBforPostgreSQL/flexibleServers**, ha még nincs delegálva.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > A virtuális hálózatnak és az alhálózatnak ugyanabban a régióban és előfizetésben kell lennie, mint a rugalmas kiszolgálónak.

- Rugalmas kiszolgáló létrehozása új virtuális hálózattal, az alhálózat nem alapértelmezett előtaggal
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
A konfigurálható CLI-paraméterek teljes listájáért tekintse meg az Azure CLI [dokumentációját](/cli/azure/postgres/flexible-server) .

## <a name="next-steps"></a>Következő lépések
- További információ a [Azure Database for PostgreSQL-rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md).
- [Azure Database for PostgreSQL rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése Azure Portal használatával](./how-to-manage-virtual-network-portal.md).
- További információ a [Azure Database for PostgreSQL-rugalmas kiszolgáló virtuális hálózatáról](./concepts-networking.md#private-access-vnet-integration).