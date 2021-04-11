---
title: 'Oktatóanyag: Kapcsolódás Azure SQL Serverhez Azure-beli privát végpont használatával – Azure CLI'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy privát végponttal rendelkező Azure SQL Servert az Azure CLI használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: a5562c5f40a321f5737fea73f6d7964b402953cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889212"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>Oktatóanyag: Kapcsolódás Azure SQL Serverhez Azure-beli privát végpont használatával – Azure CLI

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára, hogy magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot és egy megerősített gazdagépet.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy Azure SQL-kiszolgálót és egy magánhálózati végpontot.
> * Az SQL Server magánhálózati végpontjának kapcsolatának tesztelése.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Jelentkezzen be a Azure Portalba, és győződjön meg arról, hogy az előfizetése aktív a futtatásával `az login` .
* A futtatásával tekintse meg az Azure CLI-verziót egy terminálon vagy parancsablakban `az --version` . A legújabb verzióra vonatkozó megjegyzések a [legújabb kiadási megjegyzésekben](/cli/azure/release-notes-azure-cli?tabs=azure-cli)találhatók.
  * Ha nem rendelkezik a legújabb verzióval, frissítse a telepítést az [operációs rendszer vagy a platform telepítési útmutatóját](/cli/azure/install-azure-cli)követve.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az_group_create)paranccsal:

* **CreateSQLEndpointTutorial-RG** névvel ellátott. 
* A **eastus** helyen.

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban létrehoz egy virtuális hálózatot, alhálózatot és egy megerősített gazdagépet. 

A megerősített gazdagép a magánhálózati végpont teszteléséhez a virtuális géphez való biztonságos kapcsolódást fogja használni.

Virtuális hálózat létrehozása az [az Network vnet Create](/cli/azure/network/vnet#az_network_vnet_create)

* Elnevezett **myVNet**.
* A **10.0.0.0/16** címnek az előtagja.
* **MyBackendSubnet** nevű alhálózat.
* A **10.0.0.0/24** alhálózati előtag.
* A **CreateSQLEndpointTutorial-RG** erőforráscsoporthoz.
* A **eastus** helye.

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Frissítse az alhálózatot a privát végpont hálózati házirendjeinek letiltásához az az [Network vnet subnet Update paranccsal](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CreateSQLEndpointTutorial – RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

Az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal hozzon létre egy megerősített alhálózatot:

* Elnevezett **AzureBastionSubnet**.
* A **10.0.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreateSQLEndpointTutorial – RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**.
* **CreateSQLEndpointTutorial – RG**.
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Az Azure Bastion-gazdagép üzembe helyezése néhány percet is igénybe vehet.

## <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális gépet, amely a privát végpont tesztelésére szolgál.

Hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm#az_vm_create)paranccsal. Ha a rendszer kéri, adja meg a virtuális gép hitelesítő adataiként használandó jelszót:

* Elnevezett **myVM**.
* **CreateSQLEndpointTutorial – RG**.
* A hálózati **myVNet**.
* Az alhálózat **myBackendSubnet**.
* Kiszolgáló rendszerképének **Win2019Datacenter**.

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

## <a name="create-an-azure-sql-server"></a>Azure SQL-kiszolgáló létrehozása

Ebben a szakaszban egy SQL Servert és egy adatbázist fog létrehozni.

SQL Server létrehozásához használja [az az SQL Server Create](/cli/azure/sql/server#az_sql_server_create) paranccsal:

* Cserélje le **\<sql-server-name>** az nevet az egyedi kiszolgálónévre.
* Cserélje le a **\<your-password>** jelszót a jelszóra.
* **CreateSQLEndpointTutorial – RG**.
* A **eastus** régióban.

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

Adatbázis létrehozása [az az SQL db Create](/cli/azure/sql/db#az_sql_db_create) paranccsal:

* Elnevezett **myDataBase**.
* **CreateSQLEndpointTutorial – RG**.
* Cserélje le **\<sql-server-name>** az nevet az egyedi kiszolgálónévre.

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a privát végpontot fogja létrehozni.

Az az [SQL Server List](/cli/azure/sql/server#az_sql_server_list) paranccsal helyezze el az SQL Server erőforrás-azonosítóját egy rendszerhéj-változóba.

A végpont és a Kapcsolódás az [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) paranccsal hozható létre:

* Elnevezett **myPrivateEndpoint**.
* Az erőforráscsoport **CreateSQLEndpointTutorial – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* **MyConnection** nevű kapcsolatok.

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

## <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása

Ebben a szakaszban a magánhálózati DNS-zónát az [az Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)paranccsal hozhatja létre és konfigurálja.  

Az az [Network Private-DNS link vnet Create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) paranccsal hozhatja létre a DNS-zónához tartozó virtuális hálózati kapcsolatot.

Hozzon létre egy DNS-zónát az [az Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)paranccsal.

* **Privatelink.database.Windows.net** nevű zóna
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreateSQLEndpointTutorial – RG**.
* **MyDNSLink** nevű DNS-hivatkozás.
* A **myPrivateEndpoint** társítva.
* **MyZoneGroup** nevű zóna.

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

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL Serverhez való kapcsolódáshoz a privát végponton keresztül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) 
 
2. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

3. Válassza a **CreateSQLEndpointTutorial-RG** elemet.

4. Válassza a **myVM** lehetőséget.

5. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

6. Válassza a kék **használat Bastion** gombot.

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

9. Írja be a következő szöveget: `nslookup <sqlserver-name>.database.windows.net`. A helyére írja **\<sqlserver-name>** be az előző lépésben létrehozott SQL Server nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    A rendszer a **10.0.0.5** magánhálózati IP-címét adja vissza az SQL-kiszolgáló nevéhez.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.


10. Telepítse a [SQL Server Management Studiot](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) a **myVM**.

11. Nyissa meg **SQL Server Management Studio**.

12. A **Kapcsolódás a kiszolgálóhoz** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Server type (Kiszolgáló típusa) | Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév | Adja meg a **\<sql-server-name> . database.Windows.net** |
    | Hitelesítés | Válassza ki az **SQL Server-hitelesítés** lehetőséget. |
    | Felhasználónév | Adja meg a kiszolgáló létrehozásakor megadott felhasználónevet |
    | Jelszó | Adja meg a kiszolgáló létrehozásakor megadott jelszót |
    | Jelszó megjegyzése | Válassza az **Igen** lehetőséget. |

13. Válassza a **Kapcsolódás** lehetőséget.

14. Az adatbázisok tallózása a bal oldali menüben.

15. Opcionálisan Információk létrehozása vagy lekérdezése a **mysqldatabase**.

16. A **myVM** felé irányuló megerősített kapcsolódás lezárása. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha végzett a magánhálózati végpont, az SQL Server és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket hozta létre:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Azure SQL Server magánhálózati végponttal.

A virtuális gépet arra használta, hogy biztonságosan tesztelje a kapcsolatot az SQL Serverhez a magánhálózati végponton keresztül.

A következő lépésként a webalkalmazás az Azure SQL Database architektúra-forgatókönyvhöz való **privát kapcsolattal** is érdekli, amely a virtuális hálózaton kívül egy webalkalmazást kapcsol össze egy adatbázis privát végpontján.
> [!div class="nextstepaction"]
> [Webalkalmazás az Azure SQL Database szolgáltatáshoz való privát kapcsolattal](/azure/architecture/example-scenario/private-web-app/private-web-app)