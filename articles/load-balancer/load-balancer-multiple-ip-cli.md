---
title: Terheléselosztás több IP-konfiguráción az Azure CLI használatával
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogyan rendelhet hozzá több IP-címet egy virtuális géphez az Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: asudbring
ms.custom: seodec18, devx-track-azurecli
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: d928a86f958e54552f5cc8d030b0fb74fa3bd3c2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214507"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-azure-cli"></a>Terheléselosztás több IP-konfiguráción az Azure CLI használatával

Ez a cikk azt ismerteti, hogyan használható a Azure Load Balancer több IP-címmel a másodlagos hálózati adapteren (NIC). Ebben a forgatókönyvben két, Windows rendszerű virtuális gépet használunk, amelyek mindegyike elsődleges és másodlagos hálózati adapterrel rendelkezik. A másodlagos hálózati adapterek mindegyike két IP-konfigurációval rendelkezik. Mindegyik virtuális gép contoso.com és fabrikam.com egyaránt üzemelteti a webhelyeket. Minden webhely a másodlagos hálózati adapter egyik IP-konfigurációjáról van kötve. A Azure Load Balancer használatával két előtér-IP-címet teszünk közzé, amelyek közül az egyik az egyes webhelyekhez, a forgalom elosztása a webhely megfelelő IP-konfigurációjához. Ez a forgatókönyv ugyanazt a portszámot használja mindkét előtérben, valamint a háttérbeli készlet IP-címei között.

![LB-forgatókönyv képe](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Több IP-konfiguráció terheléselosztásának lépései

A jelen cikkben ismertetett forgatókönyv megvalósításához hajtsa végre a következő lépéseket:

1. [Telepítse és konfigurálja az Azure CLI](/cli/azure/install-azure-cli) -t a csatolt cikkben leírt lépéseket követve, és jelentkezzen be az Azure-fiókjába.
2. [Hozzon létre egy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-group) *contosofabrikam* nevű erőforráscsoportot a következő módon:

    ```azurecli
    az group create contosofabrikam westcentralus
    ```

3. [Hozzon létre egy rendelkezésre állási készletet](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set) a két virtuális gép számára. Ehhez a forgatókönyvhöz használja a következő parancsot:

    ```azurecli
    az vm availability-set create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. [Hozzon létre egy](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) *myVNet* nevű virtuális hálózatot és egy *mySubnet* nevű alhálózatot:

    ```azurecli
    az network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus --subnet-name MySubnet --subnet-prefix 10.0.0.0/24

    ```

5. [Hozza létre a](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) terheléselosztó nevű *mylb*:

    ```azurecli
    az network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. Hozzon létre két dinamikus nyilvános IP-címet a terheléselosztó előtér-IP-konfigurációjában:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    az network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. Hozza létre a két előtér-IP-konfigurációt, a *contosofe* és a *fabrikamfe* -t:

    ```azurecli
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    az network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. Hozza létre a háttérbeli címkészlet- *contosopool* és *fabrikampool*, [a mintavételi](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  -  *http*-t és a terheléselosztási szabályokat – *HTTPc* és *HTTPf*:

    ```azurecli
    az network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    az network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    az network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. A következő parancs futtatásával ellenőrizze, hogy [a terheléselosztó helyesen lett-e](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json) létrehozva:

    ```azurecli
    az network lb show --resource-group contosofabrikam --name mylb
    ```

10. [Hozzon létre egy nyilvános IP-címet](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), egy *myPublicIp* és egy [Storage-FIÓKOT](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json)az első virtuális gép VM1 *mystorageaccont1* a következőképpen:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. [Hozza létre a hálózati adaptereket](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-nic) a VM1 számára, és adjon hozzá egy második IP-konfigurációt, a *VM1-ipconfig2*, és [hozza létre a virtuális gépet a](../virtual-machines/linux/create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-vm) következőképpen:

    ```azurecli
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic1 --ip-config-name NIC1-ipconfig1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM1Nic2 --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM1Nic2 --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-names VM1Nic1,VM1Nic2  --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. Ismételje meg a 10-11 lépést a második virtuális géphez:

    ```azurecli
    az network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    az storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount2
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic1
    az network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name VM2Nic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    az network nic ip-config create --resource-group contosofabrikam --nic-name VM2Nic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    az vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-names VM2Nic1,VM2Nic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availability-set myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. Végezetül konfigurálnia kell a DNS-erőforrásrekordokat úgy, hogy az a Load Balancer megfelelő előtérbeli IP-címére mutasson. A tartományokat Azure DNS-ban üzemeltetheti. A Azure DNS és a Load Balancer használatával kapcsolatos további információkért lásd: a [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Következő lépések
- További információ az Azure terheléselosztási szolgáltatásainak az Azure-beli [terheléselosztási szolgáltatások használatával](../traffic-manager/traffic-manager-load-balancing-azure.md)történő összevonásáról.
- Megtudhatja, hogyan használhatja a különböző típusú naplókat az Azure-ban a [Azure Load Balancer log Analyticsben](../load-balancer/load-balancer-monitor-log.md)lévő Load Balancer kezeléséhez és hibakereséséhez.