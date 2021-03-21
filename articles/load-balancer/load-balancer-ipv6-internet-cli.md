---
title: Nyilvános terheléselosztó létrehozása IPv6-Azure CLI-vel
titleSuffix: Azure Load Balancer
description: Ezzel a képzési útvonallal megkezdheti a nyilvános Load Balancer és az IPv6 Azure CLI használatával történő létrehozását.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, Dual stack, nyilvános IP-cím, natív IPv6, mobil, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 714fb92e8757fed6e11a09528b60cda3e945175e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218893"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Nyilvános Load Balancer létrehozása IPv6-tal az Azure CLI használatával

>[!NOTE] 
>Ez a cikk egy bevezető IPv6-szolgáltatást ismertet, amely lehetővé teszi, hogy az alapszintű Load Balancer IPv4-és IPv6-kapcsolatot is biztosítson. A teljes körű IPv6-kapcsolat mostantól elérhető az [IPv6 for Azure virtuális hálózatok](../virtual-network/ipv6-overview.md) , amely integrálja az IPv6-kapcsolatot a virtuális hálózatokkal, és olyan kulcsfontosságú funkciókat tartalmaz, mint például az IPv6 hálózati biztonsági csoportra vonatkozó szabályok, az IPv6 felhasználói útválasztás, az IPv6 alapszintű és a standard szintű terheléselosztás, valamint egyebek.  Az IPv6 az Azure virtuális hálózatok az Azure-beli IPv6-alkalmazások esetében ajánlott szabvány. Lásd: [IPv6 az Azure VNET PowerShell üzembe helyezéséhez](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A Load Balancer magas rendelkezésre állást biztosít azáltal, hogy a bejövő forgalmat a Cloud Services vagy a Virtual Machines egy terheléselosztó készletében lévő, kifogástalan állapotú szolgáltatási példányok között osztja ki. A terheléselosztó több porton vagy több IP-címen, vagy mindkettőn is bemutathatja ezeket a szolgáltatásokat.

## <a name="example-deployment-scenario"></a>Példa telepítési forgatókönyv

Az alábbi ábrán az ebben a cikkben ismertetett példa sablonnal üzembe helyezett terheléselosztási megoldás látható.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Ebben az esetben a következő Azure-erőforrásokat hozza létre:

* Két virtuális gép (VM)
* Virtuális hálózati adapter minden virtuális GÉPHEZ, amelyhez IPv4-és IPv6-címek vannak hozzárendelve
* Nyilvános terheléselosztó IPv4-és IPv6-alapú nyilvános IP-címmel
* A két virtuális gépet tartalmazó rendelkezésre állási csoport
* Két terheléselosztási szabály a nyilvános VIP-címek a privát végpontokra való leképezéséhez

## <a name="deploy-the-solution-by-using-azure-cli"></a>A megoldás üzembe helyezése az Azure CLI használatával

A következő lépések bemutatják, hogyan hozható létre nyilvános terheléselosztó az Azure CLI használatával. A CLI használatával egyenként létrehozhatja és konfigurálhatja az egyes objektumokat, majd összeállíthatja őket egy erőforrás létrehozásához.

Terheléselosztó üzembe helyezéséhez hozza létre és konfigurálja a következő objektumokat:

* **Előtér-IP-konfiguráció**: nyilvános IP-címeket tartalmaz a bejövő hálózati forgalomhoz.
* **Háttérbeli címkészlet**: hálózati adaptereket (NIC) tartalmaz ahhoz, hogy a virtuális gépek hálózati forgalmat fogadjanak a terheléselosztó számára.
* **Terheléselosztási szabályok**: olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját képezik le a háttérbeli címkészlet egy portjára.
* **Bejövő NAT-szabályok**: olyan hálózati címfordítási (NAT) szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját képezik le egy portra a háttérbeli címkészlet adott virtuális gépe számára.
* **Mintavételek: olyan** állapot-mintavételt tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állásának vizsgálatára szolgálnak a háttérbeli címkészlet esetében.

## <a name="set-up-azure-cli"></a>Az Azure CLI beállítása

Ebben a példában az Azure CLI-eszközöket egy PowerShell-parancsablakban futtatja. Az olvashatóság és az újrafelhasználás javítása érdekében a PowerShell parancsfájl-kezelési funkcióit használja, nem az Azure PowerShell-parancsmagokat.

1. [Telepítse és konfigurálja az Azure CLI](/cli/azure/install-azure-cli) -t a csatolt cikkben leírt lépéseket követve, és jelentkezzen be az Azure-fiókjába.

2. PowerShell-változók beállítása az Azure CLI-parancsokkal való használathoz:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Erőforráscsoport, terheléselosztó, virtuális hálózat és alhálózatok létrehozása

1. Hozzon létre egy erőforráscsoportot:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Terheléselosztó létrehozása:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Virtuális hálózat létrehozása:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Ebben a virtuális hálózaton hozzon létre két alhálózatot:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Nyilvános IP-címek létrehozása az előtér-készlethez

1. A PowerShell változók beállítása:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Hozzon létre egy nyilvános IP-címet az előtér-IP-készlethez:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > A terheléselosztó a nyilvános IP-cím tartományának címkéjét használja teljes tartománynévként (FQDN). Ez a klasszikus üzembe helyezés változása, amely a Cloud Service-nevet használja a terheléselosztó FQDN-ként.
    >
    > Ebben a példában a teljes tartománynév *contoso09152016.southcentralus.cloudapp.Azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Előtér-és háttér-készletek létrehozása

Ebben a szakaszban a következő IP-készleteket hozza létre:
* A terheléselosztó bejövő hálózati forgalmát fogadó előtér-IP-készlet.
* A háttérbeli IP-készlet, amelyben az előtér-készlet elküldi a terheléselosztásos hálózati forgalmat.

1. A PowerShell változók beállítása:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Hozzon létre egy előtér-IP-készletet, és társítsa azt az előző lépésben létrehozott nyilvános IP-címhez és a terheléselosztó szolgáltatáshoz.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>A mintavétel, a NAT-szabályok és a terheléselosztó szabályainak létrehozása

Ez a példa a következő elemeket hozza létre:

* A 80-es TCP-porthoz való kapcsolódást megvizsgáló mintavételi szabály.
* Egy NAT-szabály, amely az 3389-es porton bejövő összes forgalmat lefordítja RDP-re a 3389-es portra.\*
* Egy NAT-szabályt, amely a 3391-es porton bejövő összes forgalmat lefordítja a 3389-es portra a távoli asztali protokoll (RDP) számára.\*
* Egy terheléselosztó-szabályt, amely a 80-as porton bejövő összes forgalmat a 80-es porton a háttér-készletben lévő címekre kiegyenlíti.

\* A NAT-szabályok a terheléselosztó mögött egy adott virtuálisgép-példányhoz vannak társítva. Az 3389-es portra érkező hálózati forgalmat a rendszer a NAT-szabályhoz társított virtuális gépre és portra továbbítja. A NAT-szabályhoz meg kell adnia egy protokollt (UDP vagy TCP). Mindkét protokoll nem rendelhető hozzá ugyanahhoz a porthoz.

1. A PowerShell változók beállítása:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Hozza létre a mintavételt.

    Az alábbi példa egy olyan TCP-mintavételt hoz létre, amely 15 másodpercenként 80-as TCP-porthoz való kapcsolódást ellenőrzi. Két egymást követő hiba után a háttér-erőforrás nem elérhetőként van megjelölve.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Hozzon létre olyan bejövő NAT-szabályokat, amelyek engedélyezik az RDP-kapcsolatokat a háttérbeli erőforrásokkal:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Hozzon létre olyan terheléselosztó-szabályokat, amelyek a kérést elfogadó előtértől függően különböző háttérbeli portokra küldik a forgalmat.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Győződjön meg a beállításokról:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Várt kimenet:

    ```output
    info:    Executing command network lb show
    info:    Looking up the load balancer "myIPv4IPv6Lb"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
    data:    Name                            : myIPv4IPv6Lb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : southcentralus
    data:    Provisioning state              : Succeeded
    data:
    data:    Frontend IP configurations:
    data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
    data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
    data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
    data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
    data:
    data:    Probes:
    data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
    data:    -------------------  ------------------  --------  ----  ----  --------  -----
    data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
    data:
    data:    Backend Address Pools:
    data:    Name             Provisioning state
    data:    ---------------  ------------------
    data:    BackendPoolIPv4  Succeeded
    data:    BackendPoolIPv6  Succeeded
    data:
    data:    Load Balancing Rules:
    data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
    data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
    data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
    data:
    data:    Inbound NAT Rules:
    data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
    data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
    data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
    info:    network lb show
    ```

## <a name="create-nics"></a>Hálózati adapterek létrehozása

Hozzon létre hálózati adaptereket, és társítsa őket a NAT-szabályokhoz, a terheléselosztó szabályaihoz és a mintavételekhez.

1. A PowerShell változók beállítása:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Hozzon létre egy hálózati adaptert az egyes háttérrendszer számára, és adjon hozzá egy IPv6-konfigurációt:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>A háttérbeli virtuálisgép-erőforrások létrehozása és az egyes hálózati adapterek csatlakoztatása

Virtuális gépek létrehozásához rendelkeznie kell egy Storage-fiókkal. A terheléselosztáshoz a virtuális gépeknek egy rendelkezésre állási csoport tagjának kell lenniük. A virtuális gépek létrehozásával kapcsolatos további információkért lásd: Azure-beli [virtuális gép létrehozása a PowerShell használatával](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. A PowerShell változók beállítása:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Ebben a példában a virtuális gépek felhasználónevét és jelszavát titkosítatlan szövegként használják. Ügyeljen arra, hogy a hitelesítő adatok titkosítatlan módon történő használatakor megfelelő legyen. A hitelesítő adatok PowerShellben való kezelésének biztonságosabb módja: [`Get-Credential`](/powershell/module/microsoft.powershell.security/get-credential) parancsmag.

2. A rendelkezésre állási csoport létrehozása:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Hozza létre a virtuális gépeket a társított hálózati adapterekkel:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```