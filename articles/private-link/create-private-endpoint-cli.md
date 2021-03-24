---
title: Rövid útmutató – Azure Private-végpont létrehozása az Azure CLI használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy privát végpontot az Azure CLI használatával.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: f74a143859f0a6629c88f0dcb61a97697f49d0be
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889229"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Rövid útmutató: privát végpont létrehozása az Azure CLI-vel

Ismerkedjen meg az Azure Private-hivatkozással egy privát végpont használatával, amellyel biztonságosan csatlakozhat egy Azure-webalkalmazáshoz.

Ebben a rövid útmutatóban létrehoz egy privát végpontot egy Azure-webalkalmazáshoz, és üzembe helyez egy virtuális gépet a magánhálózati kapcsolat teszteléséhez.  

A magánhálózati végpontok különböző típusú Azure-szolgáltatásokhoz, például az Azure SQL-hez és az Azure Storage-hoz is létrehozhatók.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure-webalkalmazás az Azure **-** előfizetésében üzembe helyezett PremiumV2 vagy magasabb szintű app Service-csomaggal.  
    * További információkat és példákat a rövid útmutató [: ASP.net Core Webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-dotnetcore.md)című témakörben talál. 
    * A webalkalmazások és végpontok létrehozásával kapcsolatos részletes oktatóanyagért lásd [: oktatóanyag: Kapcsolódás egy webalkalmazáshoz egy Azure Private-végpont használatával](tutorial-private-endpoint-webapp-portal.md).
* Jelentkezzen be a Azure Portalba, és győződjön meg arról, hogy az előfizetése aktív a futtatásával `az login` .
* A futtatásával tekintse meg az Azure CLI-verziót egy terminálon vagy parancsablakban `az --version` . A legújabb verzióra vonatkozó megjegyzések a [legújabb kiadási megjegyzésekben](/cli/azure/release-notes-azure-cli?tabs=azure-cli)találhatók.
  * Ha nem rendelkezik a legújabb verzióval, frissítse a telepítést az [operációs rendszer vagy a platform telepítési útmutatóját](/cli/azure/install-azure-cli)követve.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az_group_create)paranccsal:

* **CreatePrivateEndpointQS-RG** névvel ellátott. 
* A **eastus** helyen.

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
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
* A **CreatePrivateEndpointQS-RG** erőforráscsoporthoz.
* A **eastus** helye.

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
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
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myBastionIP** néven.
* **CreatePrivateEndpointQS – RG**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) paranccsal hozzon létre egy megerősített alhálózatot:

* Elnevezett **AzureBastionSubnet**.
* A **10.0.1.0/24** címek előtagja.
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreatePrivateEndpointQS – RG**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Az [az Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) paranccsal hozzon létre egy megerősített gazdagépet:

* Elnevezett **myBastionHost**.
* **CreatePrivateEndpointQS – RG**.
* Nyilvános IP- **myBastionIP** társítva.
* Virtuális hálózati **myVNet** társítva.
* A **eastus** helyen.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
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
* **CreatePrivateEndpointQS – RG**.
* A hálózati **myVNet**.
* Az alhálózat **myBackendSubnet**.
* Kiszolgáló rendszerképének **Win2019Datacenter**.

```azurecli-interactive
az vm create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a privát végpontot fogja létrehozni.

Az [az WebApp List](/cli/azure/webapp#az_webapp_list) paranccsal helyezze el a korábban létrehozott webalkalmazás erőforrás-azonosítóját egy rendszerhéj-változóba.

A végpont és a Kapcsolódás az [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) paranccsal hozható létre:

* Elnevezett **myPrivateEndpoint**.
* Az erőforráscsoport **CreatePrivateEndpointQS – RG**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* **MyConnection** nevű kapcsolatok.
* A WebApp **\<webapp-resource-group-name>** .

```azurecli-interactive
id=$(az webapp list \
    --resource-group <webapp-resource-group-name> \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-id sites \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása

Ebben a szakaszban a magánhálózati DNS-zónát az [az Network Private-DNS Zone Create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)paranccsal hozhatja létre és konfigurálja.  

Az az [Network Private-DNS link vnet Create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) paranccsal hozhatja létre a DNS-zónához tartozó virtuális hálózati kapcsolatot.

Hozzon létre egy DNS-zónát az [az Network Private-Endpoint DNS-Zone-Group Create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)paranccsal.

* **Privatelink.azurewebsites.net** nevű zóna
* A virtuális hálózat **myVNet**.
* Az erőforráscsoport **CreatePrivateEndpointQS – RG**.
* **MyDNSLink** nevű DNS-hivatkozás.
* A **myPrivateEndpoint** társítva.
* **MyZoneGroup** nevű zóna.

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name "privatelink.azurewebsites.net"

az network private-dns link vnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --zone-name "privatelink.azurewebsites.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreatePrivateEndpointQS-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.azurewebsites.net" \
   --zone-name webapp
```

## <a name="test-connectivity-to-private-endpoint"></a>A magánhálózati végponthoz való kapcsolódás tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL Serverhez való kapcsolódáshoz a privát végponton keresztül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) 
 
2. Válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán.

3. Válassza a **CreatePrivateEndpointQS-RG** elemet.

4. Válassza a **myVM** lehetőséget.

5. A **myVM** áttekintés lapján válassza a **kapcsolat** , majd a **megerősített** lehetőséget.

6. Válassza a kék **használat Bastion** gombot.

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. A kapcsolat után nyissa meg a Windows PowerShellt a kiszolgálón.

9. Írja be a következő szöveget: `nslookup <your-webapp-name>.azurewebsites.net`. A helyére írja **\<your-webapp-name>** be az előző lépésekben létrehozott webalkalmazás nevét.  A következőhöz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    A rendszer a webalkalmazás neveként a **10.0.0.5** magánhálózati IP-címét adja vissza.  Ez a címe a korábban létrehozott virtuális hálózat alhálózatában található.

10. Nyissa meg az Internet Explorert a **myVM**-ben lévő megerősített kapcsolódásban.

11. Adja meg a webalkalmazás ( **https:// \<your-webapp-name> . azurewebsites.net**) URL-címét.

12. Ha még nem telepítette az alkalmazást, az alapértelmezett webalkalmazás-oldal jelenik meg:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Alapértelmezett webalkalmazás lap." border="true":::

13. A **myVM** létesített kapcsolatok lezárása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végpont és a virtuális gép használatával, az az [Group delete](/cli/azure/group#az_group_delete) paranccsal távolítsa el az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:

* Virtuális hálózat és a megerősített gazdagép.
* Virtuális gép.
* Privát végpont egy Azure-webalkalmazáshoz.

A virtuális gépet arra használta, hogy biztonságosan tesztelje a kapcsolatot a webalkalmazáshoz a privát végponton keresztül.

A privát végpontot támogató szolgáltatásokkal kapcsolatos további információkért lásd:
> [!div class="nextstepaction"]
> [Privát kapcsolat elérhetősége](private-link-overview.md#availability)
