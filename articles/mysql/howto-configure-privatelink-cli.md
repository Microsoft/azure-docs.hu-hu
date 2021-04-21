---
title: Private Link – Azure CLI – Azure Database for MySQL
description: Megtudhatja, hogyan konfigurálhatja a privát hivatkozást Azure Database for MySQL Azure CLI-ről
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e2fbf95dcf2d5e3447197bc71105e415cce6681e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763324"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Alkalmazáshoz való Private Link és Azure Database for MySQL a CLI használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi, hogy az Azure-Virtual Machines (virtuális gépek) privát módon kommunikáljanak a privát kapcsolat erőforrásaival. Ebből a cikkből megtudhatja, hogyan hozhat létre azure-beli virtuális gépet az Azure CLI Virtual Network és egy azure-beli privát végponttal Azure Database for MySQL-kiszolgálón.

> [!NOTE]
> A privát kapcsolat funkció csak a Azure Database for MySQL vagy memóriaoptimal általános célú tarifacsomagban lévő összes kiszolgálóhoz érhető el. Győződjön meg arról, hogy az adatbázis-kiszolgáló ezen tarifacsomagok valamelyikében található.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Mielőtt bármilyen erőforrást létrehozhat, létre kell hoznia egy erőforráscsoportot a Virtual Network. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *westeurope helyen:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Hozzon létre egy Virtual Network [az az network vnet create gombra.](/cli/azure/network/vnet) Ez a példa létrehoz egy *myVirtualNetwork* Virtual Network nevű alapértelmezett alhálózatot egy *mySubnet nevű alhálózaton:*

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Alhálózati privátvégpont-szabályzatok letiltása 
Az Azure erőforrásokat helyez üzembe egy virtuális hálózaton belüli alhálózaton, ezért létre kell hoznia vagy frissítenie kell az alhálózatot a privát végpont hálózati szabályzatának [letiltásához.](../private-link/disable-private-endpoint-network-policy.md) Frissítse a *mySubnet* nevű alhálózati konfigurációt [az az network vnet subnet update frissítéssel:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>A virtuális gép létrehozása 
Hozzon létre egy virtuális gépet az az vm create segítségével. Amikor a rendszer kéri, adjon meg egy jelszót, amely a virtuális gép bejelentkezési hitelesítő adataiként lesz használva. Ez a példa létrehoz egy *myVm nevű virtuális gépet:* 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Jegyezze fel a virtuális gép nyilvános IP-címét. Ezzel a címmel csatlakozhat a virtuális géphez az internetről a következő lépésben.

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz 
Hozzon létre Azure Database for MySQL az az mysql server create paranccsal. Ne feledje, hogy a MySQL-kiszolgáló nevének egyedinek kell lennie az Azure-ban, ezért cserélje le a zárójelben lévő helyőrző értékét a saját egyedi értékére: 

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
> Bizonyos esetekben a Azure Database for MySQL és a VNet-alhálózat eltérő előfizetésben található. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetésben regisztrálva van a **Microsoft.DBforMySQL** erőforrás-szolgáltató. További információ: [resource-manager-registration][resource-manager-portal]

## <a name="create-the-private-endpoint"></a>A privát végpont létrehozása 
Hozzon létre egy privát végpontot a MySQL-kiszolgálóhoz a Virtual Network: 

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

## <a name="configure-the-private-dns-zone"></a>A saját DNS konfigurálása 
Hozzon létre egy saját DNS zónát a MySQL-kiszolgáló tartományához, és hozzon létre társítási hivatkozást a Virtual Network. 
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
> Az ügyfél DNS-beállításaiban található teljes tartománynevet nem a konfigurált magánhálózati IP-címre oldja fel a rendszer. Az itt látható módon be kell állítania egy DNS-zónát a konfigurált teljes [tartománynévhez.](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Csatlakozzon a *myVm virtuális géphez* az internetről a következőképpen:

1. A portál keresési sávjába írja be a *myVm* szöveget.

1. Kattintson a **Csatlakozás** gombra. A Csatlakozás **gomb kiválasztása** után **megnyílik a Csatlakozás virtuális géphez** lehetőség.

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*.rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg *a downloaded.rdp fájlt.*

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a Virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a További lehetőségek Másik fiók használata lehetőséget kell  >  választania.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

1. Ha a virtuális gép asztala megjelenik, kis méretűre állíthatja, hogy visszatér a helyi asztalra.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>A MySQL-kiszolgáló privát elérése a virtuális gépről

1. A *myVM* Távoli asztal nyissa meg a PowerShellt.

2. Írja be a következő szöveget:  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`. 

    Egy ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Tesztelje a MySQL-kiszolgáló privát kapcsolati kapcsolatát bármely elérhető ügyféllel. Az alábbi példában a [MySQL Workbencht](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) használtam a művelet végrehajtásához.


4. Az **Új kapcsolat mezőben** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolat neve| Válassza ki a kívánt kapcsolat nevét.|
    | Hostname (Gazdanév) | Válassza *a mydemoserver.privatelink.mysql.database.azure.com* |
    | Felhasználónév | Adja meg a *username@servername* mySQL-kiszolgáló létrehozásakor megadott felhasználónevet. |
    | Jelszó | Adja meg a MySQL-kiszolgáló létrehozásakor megadott jelszót. |
    ||

5. Válassza a Csatlakozás lehetőséget.

6. Adatbázisok tallózása a bal oldali menüből.

7. (Nem kötelező) Hozzon létre vagy lekérdezést a MySQL-adatbázisból.

8. Zárja be a myVm távoli asztali kapcsolatát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha már nincs rá szükség, az az group delete parancs használatával eltávolíthatja az erőforráscsoportot és az összes erőforrását: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure privát végpontról](../private-link/private-endpoint-overview.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md