---
title: Virtuális hálózatok kezelése – Azure CLI – Azure Database for PostgreSQL – Rugalmas kiszolgáló
description: Virtuális hálózatok létrehozása és kezelése Azure Database for PostgreSQL – Rugalmas kiszolgáló az Azure CLI használatával
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0a4bf648551be723007b0d8856fe0857896aad94
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778390"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Virtuális hálózatok létrehozása és kezelése Azure Database for PostgreSQL – Rugalmas kiszolgáló az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – A rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL – A rugalmas kiszolgáló kétféle, egymást kölcsönösen kizáró hálózati kapcsolati módszert támogat a rugalmas kiszolgálóhoz való csatlakozáshoz. A két lehetőség a következő:

* Nyilvános hozzáférés (engedélyezett IP-címek)
* Privát hozzáférés (VNet-integráció)

Ebben a cikkben a privát hozzáféréssel **(VNet-integrációval)** és az Azure CLI-vel való postgreSQL-kiszolgáló létrehozására összpontosítunk. A *Privát hozzáférés (VNet-integráció)* segítségével üzembe helyezheti rugalmas kiszolgálóját a saját [Azure-Virtual Network.](../../virtual-network/virtual-networks-overview.md) Az Azure Virtual Networks privát és biztonságos hálózati kommunikációt biztosít. A Privát hozzáférés területen a PostgreSQL-kiszolgáló kapcsolatai csak a virtuális hálózaton belülre korlátozódnak. További információért lásd: Privát hozzáférés [(VNet-integráció)](./concepts-networking.md#private-access-vnet-integration).

A Azure Database for PostgreSQL – Rugalmas kiszolgálón csak a kiszolgáló létrehozása során helyezheti üzembe a kiszolgálót egy virtuális hálózaton és alhálózaton. Miután telepítette a rugalmas kiszolgálót egy virtuális hálózatra és alhálózatra, nem helyezheti át másik virtuális hálózatra, alhálózatra vagy nyilvános hozzáférésre *(engedélyezett IP-címekre).*

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

A [Azure Cloud Shell](../../cloud-shell/overview.md) egy ingyenes interaktív felület, amely a cikkben található lépések futtatására használható. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A böngészőlapot megnyithatja Cloud Shell böngészőlapon is, ha megnyitja a következőt: [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kattintson **a Másolás** gombra a kódblokkok másoláshoz, illessze be Cloud Shell, majd az **Enter** billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Előfeltételek

Az az login paranccsal be kell jelentkeznie [a fiókjába.](/cli/azure/reference-index#az_login) Jegyezze fel **az Id** (Azonosító) tulajdonságot, amely **az** Azure-fiók előfizetés-azonosítójára vonatkozik.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókjában [az az account set paranccsal.](/cli/azure/account#az_account_set) Jegyezze fel  az az **login** output parancs id értékét, amely a subscription argumentum értékeként lesz használva a parancsban.  Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetését az [az account list használatával használhatja.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Rugalmas Azure Database for PostgreSQL létrehozása a CLI használatával
A paranccsal létrehozhatja a rugalmas kiszolgálót `az postgres flexible-server` privát *hozzáféréssel (VNet-integrációval).* Ez a parancs a Privát hozzáférés (VNet-integráció) lehetőséget használja alapértelmezett kapcsolódási módszerként. Ha nem ad meg virtuális hálózatot és alhálózatot, a rendszer létrehoz egyet sem. A már meglévő virtuális hálózatot és alhálózatot az alhálózat azonosítójával is meg lehet adni. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Az alábbi példákban látható módon számos lehetőség áll rendelkezésre a rugalmas kiszolgáló CLI használatával való létrehozására.

>[!Important]
> Ezzel a paranccsal delegálhatja az alhálózatot a **Microsoft.DBforPostgreSQL/flexibleServers kiszolgálóra.** Ez a delegálás azt jelenti, Azure Database for PostgreSQL rugalmas kiszolgálók csak ezt az alhálózatot használhatja. Az alhálózatra semmilyen más típusú Azure-erőforrás nem delegálható.
>
Tekintse meg az Azure CLI referenciadokumentációját <!--FIXME --> a konfigurálható CLI-paraméterek teljes listájához. Az alábbi parancsokban például megadhatja az erőforráscsoportot is.

- Rugalmas kiszolgáló létrehozása az alapértelmezett virtuális hálózat és az alapértelmezett címelőtaggal megadott alhálózat használatával
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Hozzon létre egy rugalmas kiszolgálót a már meglévő virtuális hálózat és alhálózat használatával. Ha a megadott virtuális hálózat és alhálózat nem létezik, akkor létrejön a virtuális hálózat és az alapértelmezett címelőtaggal megadott alhálózat.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Hozzon létre egy rugalmas kiszolgálót a már meglévő virtuális hálózattal, alhálózattal és az alhálózat azonosítójával. A megadott alhálózaton nem lehet más erőforrás telepítve, és ez az alhálózat delegálva lesz a **Microsoft.DBforPostgreSQL/flexibleServers** szolgáltatásba, ha még nincs delegálva.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > A virtuális hálózatnak és az alhálózatnak ugyanabban a régióban és előfizetésben kell lennie, mint a rugalmas kiszolgálónak.

- Rugalmas kiszolgáló létrehozása új virtuális hálózat és nem alapértelmezett címelőtagú alhálózat használatával
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
A konfigurálható [CLI-paraméterek](/cli/azure/postgres/flexible-server) teljes listájáért tekintse meg az Azure CLI referenciadokumentációját.

## <a name="next-steps"></a>Következő lépések
- További információ a [rugalmas Azure Database for PostgreSQL hálózatról.](./concepts-networking.md)
- [Rugalmas kiszolgálói virtuális Azure Database for PostgreSQL](./how-to-manage-virtual-network-portal.md)létrehozása és kezelése a Azure Portal.
- További információ a [Azure Database for PostgreSQL – rugalmas kiszolgálói virtuális hálózatról.](./concepts-networking.md#private-access-vnet-integration)