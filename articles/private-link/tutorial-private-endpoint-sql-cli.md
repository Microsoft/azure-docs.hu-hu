---
title: 'Oktatóanyag: Csatlakozás Azure SQL-kiszolgálóhoz Azure privát végpont használatával – Azure CLI'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure SQL-kiszolgálót privát végponttal az Azure CLI használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: a8fafeaaf974893c9a1a71115912f2a7b019ddd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771820"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Oktatóanyag: Csatlakozás Azure SQL-kiszolgálóhoz Azure privát végpont használatával – Azure CLI

Az Azure privát végpontja az Azure-beli Private Link építőeleme. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek privát módon kommunikáljanak Private Link erőforrásokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy Azure SQL-kiszolgálót és egy privát végpontot.
> * Tesztelje az SQL Server privát végponthoz való csatlakozást.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Jelentkezzen be a Azure Portal az futtatásával ellenőrizze, hogy az előfizetése `az login` aktív-e.
* Ellenőrizze az Azure CLI verzióját egy terminál- vagy parancsablakban a parancs `az --version` futtatásával. A legújabb verzióért tekintse meg a [legújabb kibocsátási megjegyzéseket.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Ha nem a legújabb verzióval működik, frissítse a telepítést az operációs rendszer vagy a platform telepítési [útmutatója alapján.](/cli/azure/install-azure-cli)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot [az az group create gombra:](/cli/azure/group#az_group_create)

* Neve: **CreateSQLEndpointTutorial-rg.** 
* Az **eastus helyen.**

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban egy virtuális hálózatot, egy alhálózatot és egy megerősített gazdagépet fog létrehozni. 

A bástyagazdagép segítségével biztonságosan csatlakozhat a virtuális géphez a privát végpont tesztelése céljából.

Virtuális hálózat létrehozása az [az network vnet create segítségével](/cli/azure/network/vnet#az_network_vnet_create)

* **MyVNet névvel.**
* A **10.0.0.0/16 címelőtagja.**
* A **myBackendSubnet nevű alhálózat.**
* A **10.0.0.0/24 alhálózati előtag.**
* A **CreateSQLEndpointTutorial-rg** erőforráscsoportban.
* Az **eastus helye.**

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Frissítse az alhálózatot, hogy letiltsa a privát végpont hálózati szabályzatát az [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)használatával:

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy nyilvános IP-címet a megerősített gazdagéphez:

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myBastionIP névvel.**
* A **CreateSQLEndpointTutorial-rg alatt.**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy bástya-alhálózatot:

* Neve: **AzureBastionSubnet.**
* A **10.0.1.0/24 címelőtag.**
* A **myVNet virtuális hálózatban.**
* A **CreateSQLEndpointTutorial-rg erőforráscsoportban.**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Az [az network bastion create használatával](/cli/azure/network/bastion#az_network_bastion_create) hozzon létre egy megerősített gazdagépet:

* A **neve myBastionHost.**
* A **CreateSQLEndpointTutorial-rg alatt.**
* A **myBastionIP nyilvános IP-címhez van társítva.**
* A myVNet virtuális hálózathoz **van társítva.**
* Az **eastus helyen.**

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

A gazdagép üzembe helyezése eltarthat néhány Azure Bastion.

## <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Ebben a szakaszban egy virtuális gépet fog létrehozni, amely a privát végpont tesztelésére lesz használva.

Hozzon létre egy virtuális gépet [az az vm create gombra.](/cli/azure/vm#az_vm_create) Amikor a rendszer kéri, adjon meg egy jelszót, amely a virtuális gép hitelesítő adataiként lesz használva:

* A **neve myVM.**
* A **CreateSQLEndpointTutorial-rg alatt.**
* A **myVNet hálózatban.**
* A **myBackendSubnet alhálózatban.**
* Kiszolgálói lemezkép **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-an-azure-sql-server"></a>Új Azure SQL létrehozása

Ebben a szakaszban egy SQL-kiszolgálót és -adatbázist fog létrehozni.

[SQL-kiszolgáló létrehozásához](/cli/azure/sql/server#az_sql_server_create) használja az az sql server create parancsokat:

* Cserélje **\<sql-server-name>** le a helyére az egyedi kiszolgáló nevét.
* Cserélje **\<your-password>** le a helyére a jelszavát.
* A **CreateSQLEndpointTutorial-rg alatt.**
* Az **eastus régióban.**

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Az [az sql db create parancs használatával](/cli/azure/sql/db#az_sql_db_create) hozzon létre egy adatbázist:

* A **neve myDataBase.**
* A **CreateSQLEndpointTutorial-rg alatt.**
* Cserélje **\<sql-server-name>** le a helyére az egyedi kiszolgáló nevét.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a privát végpontot fogja létrehozni.

Az [az sql server list parancs használatával](/cli/azure/sql/server#az_sql_server_list) helyezze az SQL Server erőforrás-azonosítóját egy rendszerhéjváltozóba.

Az [az network private-endpoint create használatával](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) hozza létre a végpontot és a kapcsolatot:

* A **neve myPrivateEndpoint.**
* A **CreateSQLEndpointTutorial-rg erőforráscsoportban.**
* A **myVNet virtuális hálózatban.**
* A **myBackendSubnet alhálózatban.**
* Kapcsolat **myConnection névvel.**

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása

Ebben a szakaszban a privát DNS-zónát fogja létrehozni és konfigurálni [az az network private-dns zone create használatával.](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)  

A DNS-zónára mutató virtuális hálózati kapcsolatot az [az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) használatával fogja létrehozni.

Létre fog hozni egy dns-zónacsoportot [az az network private-endpoint dns-zone-group create használatával.](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)

* Zóna neve **privatelink.database.windows.net**
* A **myVNet virtuális hálózatban.**
* A **CreateSQLEndpointTutorial-rg erőforráscsoportban.**
* DNS-hivatkozás **myDNSLink névvel.**
* Társítva a **myPrivateEndpoint ponttal.**
* MyZoneGroup nevű **zónacsoport.**

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>Privát végponttal való kapcsolat tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL-kiszolgálóhoz való csatlakozáshoz a privát végponton keresztül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) 
 
2. Válassza **az Erőforráscsoportok** lehetőséget a bal oldali navigációs panelen.

3. Válassza **a CreateSQLEndpointTutorial-rg lehetőséget.**

4. Válassza **a myVM lehetőséget.**

5. A **myVM áttekintő oldalán** válassza a **Csatlakozás,** majd **a Bastion lehetőséget.**

6. Kattintson a kék **Bastion használata gombra.**

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. Csatlakozás Windows PowerShell nyissa meg a kiszolgálón a következőt: .

9. Írja be a következő szöveget: `nslookup <sqlserver-name>.database.windows.net`. Cserélje le a helyére az előző lépésekben **\<sqlserver-name>** létrehozott SQL-kiszolgáló nevét.  Az alábbihoz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    Az SQL-kiszolgáló neve **a 10.0.0.5** magánhálózati IP-címet ad vissza.  Ez a cím a korábban létrehozott virtuális hálózat alhálózatában található.


10. Telepítse [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) **myVM-et.**

11. Nyissa meg **SQL Server Management Studio.**

12. A **Connect to server (Csatlakozás kiszolgálóhoz) mezőben** adja meg vagy válassza ki a következő információkat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Server type (Kiszolgáló típusa) | Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév | Írja be **\<sql-server-name> a .database.windows.net** |
    | Hitelesítés | Válassza ki az **SQL Server-hitelesítés** lehetőséget. |
    | Felhasználónév | Adja meg a kiszolgáló létrehozása során megadott felhasználónevet |
    | Jelszó | Adja meg a kiszolgáló létrehozása során megadott jelszót |
    | Jelszó megjegyzése | Válassza az **Igen** lehetőséget. |

13. Válassza a **Kapcsolódás** lehetőséget.

14. Adatbázisok tallózása a bal oldali menüből.

15. (Nem kötelező) Adatok létrehozása vagy lekérdezése a **mysqldatabase adatbázisból.**

16. Zárja be a **myVM-hez való megerősített kapcsolatot.** 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha végzett a privát végpont, az SQL Server és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőt hozta létre:

* Virtuális hálózat és megerősített gazdagép.
* Virtuális gép.
* Azure SQL kiszolgáló privát végponttal.

A virtuális gép használatával biztonságosan tesztelte az SQL-kiszolgálóval való kapcsolatot a privát végponton keresztül.

Következő lépésként érdekelheti a Azure SQL **webalkalmazás,** amely privát kapcsolattal rendelkezik egy adatbázis-architektúra-forgatókönyvhöz, amely a virtuális hálózaton kívüli webalkalmazást egy adatbázis privát végpontjára csatlakoztatja.
> [!div class="nextstepaction"]
> [Webalkalmazás privát kapcsolattal a Azure SQL adatbázishoz](/azure/architecture/example-scenario/private-web-app/private-web-app)