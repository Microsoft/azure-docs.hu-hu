---
title: Rövid útmutató – Privát Azure-végpont létrehozása az Azure CLI használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre privát végpontot az Azure CLI használatával.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 5088b4e50899a2643488103ba29a7e36a7f256ea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778354"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-cli"></a>Rövid útmutató: Privát végpont létrehozása az Azure CLI használatával

A Azure Private Link használatának első lépésekéhez privát végponttal csatlakozhat biztonságosan egy Azure-webalkalmazáshoz.

Ebben a rövid útmutatóban létrehoz egy privát végpontot egy Azure-webalkalmazáshoz, és üzembe helyez egy virtuális gépet a privát kapcsolat teszteléséhez.  

A privát végpontok különböző Azure-szolgáltatásokhoz, például a Azure SQL azure storage-hoz is létre lehet hozva.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Egy **PrémiumV2** szintű vagy magasabb szintű App Service-csomaggal telepített Azure-webalkalmazás az Azure-előfizetésben.  
    * További információkért és egy példáért lásd: [Rövid útmutató: ASP.NET Core-webalkalmazás létrehozása az Azure-ban.](../app-service/quickstart-dotnetcore.md) 
    * A webalkalmazások és végpontok létrehozásáról részletes oktatóanyagért [lásd: Oktatóanyag: Csatlakozás webalkalmazáshoz privát Azure-végpont használatával.](tutorial-private-endpoint-webapp-portal.md)
* Jelentkezzen be a Azure Portal az futtatásával ellenőrizze, hogy az előfizetése `az login` aktív-e.
* Ellenőrizze az Azure CLI verzióját egy terminál- vagy parancsablakban a parancs `az --version` futtatásával. A legújabb verzióért tekintse meg a [legújabb kibocsátási megjegyzéseket.](/cli/azure/release-notes-azure-cli?tabs=azure-cli)
  * Ha nem a legújabb verzióval működik, frissítse a telepítést az operációs rendszer vagy a platform telepítési [útmutatója alapján.](/cli/azure/install-azure-cli)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot [az az group create gombra:](/cli/azure/group#az_group_create)

* **CreatePrivateEndpointQS-rg névvel.** 
* Az **eastus helyen.**

```azurecli-interactive
az group create \
    --name CreatePrivateEndpointQS-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Virtuális hálózat és megerősített gazdagép létrehozása

Ebben a szakaszban egy virtuális hálózatot, egy alhálózatot és egy megerősített gazdagépet fog létrehozni. 

A bástyagazdagép segítségével biztonságosan csatlakozhat a virtuális géphez a privát végpont tesztelése céljából.

Virtuális hálózat létrehozása az [az network vnet create segítségével](/cli/azure/network/vnet#az_network_vnet_create)

* A **neve myVNet.**
* A **10.0.0.0/16 címelőtagja.**
* A **myBackendSubnet nevű alhálózat.**
* A **10.0.0.0/24 alhálózati előtag.**
* A **CreatePrivateEndpointQS-rg erőforráscsoportban.**
* Az **eastus helye.**

```azurecli-interactive
az network vnet create \
    --resource-group CreatePrivateEndpointQS-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

Frissítse az alhálózatot, hogy letiltsa a privát végpont hálózati szabályzatát az [az network vnet subnet update használatával:](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreatePrivateEndpointQS-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

Az [az network public-ip create használatával](/cli/azure/network/public-ip#az_network_public_ip_create) hozzon létre egy nyilvános IP-címet a megerősített gazdagéphez:

* Hozzon létre egy standard zónaredundáns nyilvános IP-címet **myBastionIP névvel.**
* A **CreatePrivateEndpointQS-rg alatt.**

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionIP \
    --sku Standard
```

Az [az network vnet subnet create használatával](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) hozzon létre egy bástya-alhálózatot:

* Neve: **AzureBastionSubnet.**
* A **10.0.1.0/24 címelőtagja.**
* A **myVNet virtuális hálózatban.**
* A **CreatePrivateEndpointQS-rg erőforráscsoportban.**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

Az [az network bastion create használatával](/cli/azure/network/bastion#az_network_bastion_create) hozzon létre egy megerősített gazdagépet:

* A **neve myBastionHost.**
* A **CreatePrivateEndpointQS-rg alatt.**
* A **myBastionIP nyilvános IP-címhez van társítva.**
* A myVNet virtuális hálózathoz **van társítva.**
* Az **eastus helyen.**

```azurecli-interactive
az network bastion create \
    --resource-group CreatePrivateEndpointQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

A gazdagép üzembe helyezése eltarthat Azure Bastion néhány percig.

## <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Ebben a szakaszban egy virtuális gépet fog létrehozni, amely a privát végpont tesztelésére lesz használva.

Hozzon létre egy virtuális gépet [az az vm create gombra.](/cli/azure/vm#az_vm_create) Amikor a rendszer kéri, adjon meg egy jelszót, amely a virtuális gép hitelesítő adataiként lesz használva:

* **MyVM névvel.**
* A **CreatePrivateEndpointQS-rg alatt.**
* A **myVNet hálózatban.**
* A **myBackendSubnet alhálózatban.**
* Kiszolgálói lemezkép **Win2019Datacenter**.

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

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a privát végpontot fogja létrehozni.

Az [az webapp list parancs](/cli/azure/webapp#az_webapp_list) használatával helyezze a korábban létrehozott webalkalmazás erőforrás-azonosítóját egy rendszerhéjváltozóba.

Az [az network private-endpoint create használatával](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) hozza létre a végpontot és a kapcsolatot:

* A **neve myPrivateEndpoint.**
* A **CreatePrivateEndpointQS-rg erőforráscsoportban.**
* A **myVNet virtuális hálózatban.**
* A **myBackendSubnet alhálózatban.**
* Kapcsolat **myConnection névvel.**
* Az Ön **\<webapp-resource-group-name>** webalkalmazása.

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

## <a name="configure-the-private-dns-zone"></a>A privát DNS-zóna konfigurálása

Ebben a szakaszban létrehozza és konfigurálja a privát [DNS-zónát az az network private-dns zone create használatával.](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create)  

A DNS-zónára mutató virtuális hálózati kapcsolatot [az az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) használatával hozhatja létre.

Létre fog hozni egy dns-zónacsoportot [az az network private-endpoint dns-zone-group create használatával.](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create)

* Zóna nevű **privatelink.azurewebsites.net**
* A **myVNet virtuális hálózatban.**
* A **CreatePrivateEndpointQS-rg erőforráscsoportban.**
* A **myDNSLink nevű DNS-hivatkozás.**
* Társítva a **myPrivateEndpoint ponttal.**
* MyZoneGroup nevű **zónacsoport.**

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

## <a name="test-connectivity-to-private-endpoint"></a>Privát végponttal való kapcsolat tesztelése

Ebben a szakaszban az előző lépésben létrehozott virtuális gépet fogja használni az SQL-kiszolgálóhoz való csatlakozáshoz a privát végponton keresztül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) 
 
2. Válassza **az Erőforráscsoportok** lehetőséget a bal oldali navigációs panelen.

3. Válassza **a LétrehozásPrivateEndpointQS-rg lehetőséget.**

4. Válassza **a myVM lehetőséget.**

5. A **myVM áttekintő oldalán** válassza a **Csatlakozás,** majd **a Bastion lehetőséget.**

6. Kattintson a kék **Bastion használata gombra.**

7. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

8. Csatlakozás Windows PowerShell nyissa meg a kiszolgálón a következőt: .

9. Írja be a következő szöveget: `nslookup <your-webapp-name>.azurewebsites.net`. Cserélje le a helyére az előző lépésekben **\<your-webapp-name>** létrehozott webalkalmazás nevét.  Az alábbihoz hasonló üzenet jelenik meg:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    A webalkalmazás neve **a 10.0.0.5** magánhálózati IP-címet ad vissza.  Ez a cím a korábban létrehozott virtuális hálózat alhálózatában található.

10. A **myVM-hez** való megerősített kapcsolaton nyissa meg Internet Explorer.

11. Adja meg a webalkalmazás URL-címét, **majd https:// \<your-webapp-name> .azurewebsites.net.**

12. Az alapértelmezett webalkalmazás-oldalt fogja kapni, ha az alkalmazás még nem lett üzembe állítva:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Alapértelmezett webalkalmazás-oldal." border="true":::

13. Zárja be a **myVM-hez való kapcsolódást.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végpont és a virtuális gép használatával, az [az group delete](/cli/azure/group#az_group_delete) parancs használatával távolítsa el az erőforráscsoportot és az összes erőforrását:

```azurecli-interactive
az group delete \
    --name CreatePrivateEndpointQS-rg
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:

* Virtuális hálózat és megerősített gazdagép.
* Virtuális gép.
* Azure-webalkalmazás privát végpontja.

A virtuális gép használatával biztonságosan tesztelte a webalkalmazással való kapcsolatot a privát végponton keresztül.

A privát végpontot támogató szolgáltatásokkal kapcsolatos további információkért lásd:
> [!div class="nextstepaction"]
> [Private Link rendelkezésre állás](private-link-overview.md#availability)
