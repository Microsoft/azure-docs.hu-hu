---
title: Privát hivatkozás – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan konfigurálhat privát hivatkozást az Azure Database for PostgreSQL-Single Serverhez az Azure CLI-ből
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3452bfee1e9228926bb687d1b9dc7fb26dfff85a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642174"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Privát hivatkozás létrehozása és kezelése Azure Database for PostgreSQL – egyetlen kiszolgáló számára a parancssori felület használatával

A privát végpont az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a Virtual Machines (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal. Ebből a cikkből megtudhatja, hogyan hozhat létre virtuális gépet Azure-Virtual Network és egy Azure Database for PostgreSQL önálló Azure-végponttal rendelkező Azure CLI használatával.

> [!NOTE]
> A privát hivatkozás funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for PostgreSQL-kiszolgálókon érhető el. Győződjön meg arról, hogy az adatbázis-kiszolgáló ezen díjszabási szintek egyikében található.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

- Egy [Azure Database for PostgreSQL-kiszolgáló és-adatbázis](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verzióját kell használnia. A telepített verziójának megkereséséhez futtassa a parancsot `az --version` . További információ: az [Azure CLI telepítése](/cli/azure/install-azure-cli) a telepítéshez vagy a frissítéshez.

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

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – egyetlen kiszolgáló létrehozása 
Hozzon létre egy Azure Database for PostgreSQL az az postgres Server Create paranccsal. Ne feledje, hogy a PostgreSQL-kiszolgáló nevének egyedinek kell lennie az Azure-ban, ezért a helyőrző értékét cserélje le a fent használt saját egyedi értékekre: 

```azurecli-interactive
# Create a server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

## <a name="create-the-private-endpoint"></a>A magánhálózati végpont létrehozása 
Hozzon létre egy privát végpontot a PostgreSQL-kiszolgálóhoz a Virtual Networkban: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforPostgreSQL/servers" --query "id" -o tsv) \    
    --group-id postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>A saját DNS zóna konfigurálása 
Hozzon létre egy saját DNS zónát a PostgreSQL-kiszolgáló tartományhoz, és hozzon létre egy társítási hivatkozást a Virtual Network. 

```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> Az ügyfél DNS-beállításaiban található teljes tartománynevet nem a konfigurált magánhálózati IP-címre oldja fel a rendszer. Az [itt](../dns/dns-operations-recordsets-portal.md)látható módon konfigurálnia kell egy DNS-zónát a beállított FQDN-hez.

> [!NOTE]
> Bizonyos esetekben a Azure Database for PostgreSQL és a VNet különböző előfizetésekben találhatók. Ezekben az esetekben a következő konfigurációkat kell biztosítania:
> - Győződjön meg arról, hogy mindkét előfizetés regisztrálva van a **Microsoft. DBforPostgreSQL** erőforrás-szolgáltatónál. További információkért tekintse meg az [erőforrás-szolgáltatók](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

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

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>A PostgreSQL-kiszolgáló elérése a virtuális gépről

1. A *myVM* távoli asztal nyissa meg a PowerShellt.

2. Írja be a következő szöveget:  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

   Egy ehhez hasonló üzenet jelenik meg:

   ```azurepowershell
   Server:  UnKnown
   Address:  168.63.129.16
   Non-authoritative answer:
   Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
   Address:  10.1.3.4
   ```

3. A PostgreSQL-kiszolgáló magánhálózati kapcsolati kapcsolatának tesztelése bármely elérhető ügyfél használatával. Az alábbi példa az [Azure adatstudiót](/sql/azure-data-studio/download) használja a művelet elvégzésére.

4. Az **új kapcsolatok** területen adja meg vagy válassza ki az alábbi adatokat:

   | Beállítás | Érték |
   | ------- | ----- |
   | Server type (Kiszolgáló típusa)| Válassza a **PostgreSQL** lehetőséget.|
   | Kiszolgálónév| *Mydemopostgresserver.privatelink.postgres.database.Azure.com* kiválasztása |
   | Felhasználónév | Adja meg username@servername a PostgreSQL-kiszolgáló létrehozásakor megadott felhasználónevet. |
   |Jelszó |Adja meg a PostgreSQL-kiszolgáló létrehozásakor megadott jelszót. |
   |SSL|Válassza a **kötelező** lehetőséget.|
   ||

5. Válassza a Csatlakozás lehetőséget.

6. A bal oldali menüben lévő adatbázisok tallózása.

7. Opcionálisan Információk létrehozása vagy lekérdezése a postgreSQL-kiszolgálóról.

8. A távoli asztali kapcsolat bezárásával myVm.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha már nincs rá szükség, az az Group delete paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Következő lépések
- További információ az [Azure Private Endpoint szolgáltatásról](../private-link/private-endpoint-overview.md)
