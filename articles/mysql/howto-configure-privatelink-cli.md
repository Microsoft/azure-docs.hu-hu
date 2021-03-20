---
title: Privát hivatkozás – Azure CLI – Azure Database for MySQL
description: Ismerje meg, hogyan konfigurálhatja a Azure Database for MySQLhez készült privát hivatkozást az Azure CLI-vel
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: da6a2b97d2656d56fa2aa0e7259fba433bd7b81e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998583"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Azure Database for MySQL magánhálózati hivatkozás létrehozása és kezelése a parancssori felület használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális gépet Azure-Virtual Network és egy Azure Database for MySQL-kiszolgáló Azure-beli privát végponttal, hogyan lehet az Azure CLI használatával létrehozni.

> [!NOTE]
> A privát hivatkozás funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MySQL-kiszolgálókon érhető el. Győződjön meg arról, hogy az adatbázis-kiszolgáló ezen díjszabási szintek egyikében található.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforrások létrehozása előtt létre kell hoznia egy erőforráscsoportot a Virtual Network üzemeltetéséhez. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westeurope* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy Virtual Network az [az Network vnet Create](/cli/azure/network/vnet)paranccsal. Ez a példa egy *myVirtualNetwork* nevű alapértelmezett Virtual Network hoz létre egy *mySubnet* nevű alhálózattal:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Alhálózat magánhálózati végponti házirendjeinek letiltása 
Az Azure üzembe helyezi az erőforrásokat egy virtuális hálózaton belüli alhálózaton, ezért létre kell hoznia vagy frissítenie kell az alhálózatot a magánhálózati végpontok [hálózati házirendjeinek](../private-link/disable-private-endpoint-network-policy.md)letiltásához. Frissítsen egy *mySubnet* nevű alhálózati konfigurációt az [az Network vnet subnet Update paranccsal](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>A virtuális gép létrehozása 
Hozzon létre egy virtuális gépet az az VM Create paranccsal. Ha a rendszer kéri, adja meg a virtuális gép bejelentkezési hitelesítő adataiként használandó jelszót. Ez a példa egy *myVm* nevű virtuális gépet hoz létre: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Jegyezze fel a virtuális gép nyilvános IP-címét. Ezt a lakcímet fogja használni a virtuális géphez az internetről a következő lépésben való kapcsolódáshoz.

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz 
Hozzon létre egy Azure Database for MySQL az az MySQL Server Create paranccsal. Ne feledje, hogy a MySQL-kiszolgáló nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét zárójelek között a saját egyedi értékkel kell helyettesítenie: 

```azurecli-interactive
# Create a server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> Bizonyos esetekben a Azure Database for MySQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforMySQL** erőforrás-szolgáltatónál. További információ: [Resource-Manager-regisztráció][resource-manager-portal]

## <a name="create-the-private-endpoint"></a>A magánhálózati végpont létrehozása 
Hozzon létre egy privát végpontot a MySQL-kiszolgálóhoz a Virtual Networkban: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMySQL/servers" --query "id" -o tsv) \    
    --group-id mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>A saját DNS zóna konfigurálása 
Hozzon létre egy saját DNS zónát a MySQL-kiszolgáló tartományhoz, és hozzon létre egy társítási hivatkozást a Virtual Network. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Az ügyfél DNS-beállításában lévő teljes tartománynév nem oldható fel a magánhálózati IP-címekre konfigurálva. Az [itt](../dns/dns-operations-recordsets-portal.md)látható módon konfigurálnia kell egy DNS-zónát a beállított FQDN-hez.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a virtuális gép *myVm* az internetről a következőképpen:

1. A portál keresési sávjába írja be a *myVm* szöveget.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a *letöltött. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a   >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>A MySQL-kiszolgáló elérése a virtuális gépről

1. A *myVM* távoli asztal nyissa meg a PowerShellt.

2. Írja be a következő szöveget:  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`. 

    Egy ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Tesztelje a MySQL-kiszolgáló magánhálózati kapcsolati kapcsolatát bármely elérhető ügyfél használatával. Az alábbi példában a [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) használatával végeztem el a műveletet.


4. Az **új kapcsolatok** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolat neve| Válassza ki az Ön által választott kapcsolatok nevét.|
    | Hostname (Gazdanév) | *Mydemoserver.privatelink.mysql.database.Azure.com* kiválasztása |
    | Felhasználónév | Adja meg a felhasználónevet, *username@servername* amelyet a MySQL-kiszolgáló létrehozásakor adott meg. |
    | Jelszó | Adja meg a MySQL-kiszolgáló létrehozásakor megadott jelszót. |
    ||

5. Válassza a Csatlakozás lehetőséget.

6. A bal oldali menüben lévő adatbázisok tallózása.

7. Opcionálisan Információk létrehozása vagy lekérdezése a MySQL-adatbázisból.

8. A távoli asztali kapcsolat bezárásával myVm.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha már nincs rá szükség, az az Group delete paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private Endpoint szolgáltatásról](../private-link/private-endpoint-overview.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md