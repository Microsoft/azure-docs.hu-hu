---
title: Terheléselosztás több IP-konfiguráción – Azure CLI
titleSuffix: Azure Load Balancer
description: Ebben a cikkben megismerheti az elsődleges és másodlagos IP-konfigurációk terheléselosztását az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: a2916f28be0b45eec6e9c1a85c0b8db3fb611381
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417858"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Terheléselosztás több IP-konfiguráción a PowerShell használatával

> [!div class="op_single_selector"]
> * [Portál](load-balancer-multiple-ip.md)
> * [Parancssori felület](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

Ez a cikk azt ismerteti, hogyan használható a Azure Load Balancer több IP-címmel a másodlagos hálózati adapteren (NIC). Ebben a forgatókönyvben két, Windows rendszerű virtuális gépet használunk, amelyek mindegyike elsődleges és másodlagos hálózati adapterrel rendelkezik. A másodlagos hálózati adapterek mindegyike két IP-konfigurációval rendelkezik. Mindegyik virtuális gép contoso.com és fabrikam.com egyaránt üzemelteti a webhelyeket. Minden webhely a másodlagos hálózati adapter egyik IP-konfigurációjáról van kötve. A Azure Load Balancer használatával két előtér-IP-címet teszünk közzé, amelyek közül az egyik az egyes webhelyekhez, a forgalom elosztása a webhely megfelelő IP-konfigurációjához. Ez a forgatókönyv ugyanazt a portszámot használja mindkét előtérben, valamint a háttérbeli készlet IP-címei között.

![LB-forgatókönyv képe](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Több IP-konfiguráció terheléselosztásának lépései

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kövesse az alábbi lépéseket a jelen cikkben ismertetett forgatókönyv megvalósításához:

1. Az Azure PowerShell telepítése. Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/) (Az Azure PowerShell telepítése és konfigurálása).
2. Hozzon létre egy erőforráscsoportot a következő beállítások használatával:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    További információ: az [erőforráscsoport létrehozásának](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json)2. lépése.

3. [Hozzon létre egy rendelkezésre állási készletet](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) , amely tartalmazza a virtuális gépeket. Ehhez a forgatókönyvhöz használja a következő parancsot:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Kövesse a [Windows rendszerű virtuális gép létrehozása](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) című cikk 3 – 5. lépését a virtuális gép egyetlen hálózati adapterrel való létrehozásának előkészítéséhez. Hajtsa végre az 6,1-es lépést, és a 6,2-es lépés helyett használja a következőt:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Ezután [hozzon létre egy Windows rendszerű virtuális gép](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fazure%2fload-balancer%2ftoc.json) 6,3 – 6,8. lépéseit.

5. Adjon hozzá egy második IP-konfigurációt az egyes virtuális gépekhez. Kövesse a [több IP-cím társítása virtuális gépekhez](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) című cikk utasításait. Használja a következő konfigurációs beállításokat:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    A másodlagos IP-konfigurációkat nem kell a nyilvános IP-címekkel társítani az oktatóanyag céljára. Szerkessze a parancsot a nyilvános IP-társítási rész eltávolításához.

6. Fejezze be a cikk 4 – 6. lépését a VM2. Ha ezt megteszi, ne felejtse el lecserélni a virtuális gép nevét a VM2. Vegye figyelembe, hogy a második virtuális GÉPHEZ nem szükséges virtuális hálózatot létrehoznia. A használati eset alapján lehetséges, hogy nem hoz létre új alhálózatot.

7. Hozzon létre két nyilvános IP-címet, és tárolja azokat a megfelelő változókban az alábbiak szerint:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Hozzon létre két előtér-IP-konfigurációt:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Hozza létre a háttér-címkészlet, a mintavétel és a terheléselosztási szabályokat:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Miután létrehozta ezeket az erőforrásokat, hozza létre a terheléselosztó:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Adja hozzá a második háttér-címkészletet és az előtér-IP-konfigurációt az újonnan létrehozott Load Balancerhez:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. Az alábbi parancsok lekérik a hálózati adaptereket, majd az összes másodlagos hálózati adapter IP-konfigurációját a terheléselosztó háttér-címkészlet részére is felveszik:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Végezetül konfigurálnia kell a DNS-erőforrásrekordokat úgy, hogy az a Load Balancer megfelelő előtérbeli IP-címére mutasson. A tartományokat Azure DNS-ban üzemeltetheti. A Azure DNS és a Load Balancer használatával kapcsolatos további információkért lásd: a [Azure DNS használata más Azure-szolgáltatásokkal](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Következő lépések
- További információ az Azure terheléselosztási szolgáltatásainak az Azure-beli [terheléselosztási szolgáltatások használatával](../traffic-manager/traffic-manager-load-balancing-azure.md)történő összevonásáról.
- Ismerje meg, hogyan használhatja a különböző típusú naplókat az Azure-ban a [Azure Load Balancer Azure monitor naplófájljaiban](../load-balancer/load-balancer-monitor-log.md)található Load Balancer kezeléséhez és hibakereséséhez.