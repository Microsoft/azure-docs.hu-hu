---
title: Virtuális hálózatok létrehozása az Azure HDInsight-fürtökhöz
description: Ismerje meg, hogyan hozhat létre Azure-Virtual Network a HDInsight más felhőalapú erőforrásokhoz vagy az adatközpontban található erőforrásokhoz való összekapcsolásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/16/2020
ms.openlocfilehash: 43d57eac94cabb5c648183911e0c0bf72889946d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946066"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Virtuális hálózatok létrehozása az Azure HDInsight-fürtökhöz

Ez a cikk példákat és mintakód-mintákat tartalmaz az [Azure-beli virtuális hálózatok](../virtual-network/virtual-networks-overview.md)létrehozásához és konfigurálásához. Az Azure HDInsight-fürtökkel való használathoz. A hálózati biztonsági csoportok (NSG-EK) létrehozásával és a DNS konfigurálásával kapcsolatos részletes példákat mutatjuk be.

A virtuális hálózatok Azure HDInsight való használatával kapcsolatos háttér-információkért lásd: [virtuális hálózat megtervezése az Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>A kód mintáinak és példáinak előfeltételei

A cikkben szereplő kódok végrehajtása előtt a TCP/IP hálózatkezelés megismerése szükséges. Ha nem ismeri a TCP/IP-hálózatkezelést, akkor az éles hálózatok módosítása előtt kérjen meg valakit.

A cikkben szereplő minták egyéb előfeltételei közé tartoznak a következő elemek:

* Ha a PowerShellt használja, telepítenie kell az az [modult](/powershell/azure/).
* Ha az Azure CLI-t szeretné használni, és még nem telepítette, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

> [!IMPORTANT]  
> Ha a HDInsight a helyszíni hálózathoz Azure Virtual Network használatával történő csatlakoztatására vonatkozó útmutatót keresi, tekintse meg a [HDInsight csatlakoztatása](connect-on-premises-network.md) a helyszíni hálózati dokumentumhoz című témakört.

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Példa: hálózati biztonsági csoportok HDInsight

Az ebben a szakaszban szereplő példák bemutatják a hálózati biztonsági csoportokra vonatkozó szabályok létrehozását. A szabályok lehetővé teszik a HDInsight számára az Azure felügyeleti szolgáltatásokkal való kommunikációt. A példák használata előtt állítsa be az IP-címeket úgy, hogy azok megfeleljenek az Ön által használt Azure-régiónak. Ezeket az információkat a [HDInsight-felügyelet IP-címeiben](hdinsight-management-ip-addresses.md)találja meg.

### <a name="azure-resource-management-template"></a>Azure Resource Management-sablon

A következő erőforrás-kezelési sablon egy virtuális hálózatot hoz létre, amely korlátozza a bejövő forgalmat, de engedélyezi a HDInsight által igényelt IP-címekről érkező forgalmat. Ez a sablon egy HDInsight-fürtöt is létrehoz a virtuális hálózaton.

* [Biztonságos Azure-Virtual Network és HDInsight Hadoop-fürt üzembe helyezése](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

A következő PowerShell-szkripttel olyan virtuális hálózatot hozhat létre, amely korlátozza a bejövő forgalmat, és engedélyezi az észak-európai régió IP-címeinek forgalmát.

> [!IMPORTANT]  
> Módosítsa a és a példában szereplő IP-címeket `hdirule1` `hdirule2` úgy, hogy azok megfeleljenek az Ön által használt Azure-régiónak. Ezt az információt a [HDInsight felügyeleti IP-címei](hdinsight-management-ip-addresses.md)között találja.

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Ez a példa bemutatja, hogyan adhat hozzá olyan szabályokat, amelyek engedélyezik a bejövő forgalmat a szükséges IP-címeken. Nem tartalmaz szabályt a más forrásokból érkező bejövő hozzáférés korlátozására. Az alábbi kód bemutatja, hogyan engedélyezhető az SSH-hozzáférés az internetről:

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

A következő lépésekkel hozhat létre olyan virtuális hálózatot, amely korlátozza a bejövő forgalmat, de engedélyezi a HDInsight által igényelt IP-címekről érkező forgalmat.

1. A következő parancs használatával hozzon létre egy nevű új hálózati biztonsági csoportot `hdisecure` . Cserélje le az- `RESOURCEGROUP` t az Azure-Virtual Network tartalmazó erőforráscsoporthoz. Cserélje le `LOCATION` azt a helyet (régiót), amelyben a csoport létre lett hozva.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Miután létrejött a csoport, információt kap az új csoportról.

2. A következő paranccsal adhat hozzá olyan szabályokat az új hálózati biztonsági csoporthoz, amelyek engedélyezik a bejövő kommunikációt az 443-es porton az Azure HDInsight Health and Management szolgáltatásból. Cserélje le az értékét az `RESOURCEGROUP` Azure-Virtual Network tartalmazó erőforráscsoport nevére.

    > [!IMPORTANT]  
    > Módosítsa a és a példában szereplő IP-címeket `hdirule1` `hdirule2` úgy, hogy azok megfeleljenek az Ön által használt Azure-régiónak. Ezeket az információkat a [HDInsight-felügyelet IP-címeiben](hdinsight-management-ip-addresses.md)találja meg.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. A hálózati biztonsági csoport egyedi azonosítójának lekéréséhez használja a következő parancsot:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. A hálózati biztonsági csoport alhálózatra való alkalmazásához használja az alábbi parancsot. Cserélje le az `GUID` és az `RESOURCEGROUP` értékeket az előző lépésben visszaadott értékekre. Cserélje `VNETNAME` le `SUBNETNAME` a és a nevet a létrehozni kívánt virtuális hálózat nevére és alhálózatára.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    A parancs végrehajtása után a HDInsight telepítheti a Virtual Networkba.

Ezek a lépések csak az Azure-felhő HDInsight állapotának és kezelésének hozzáférését nyitják meg. A HDInsight-fürtnek a Virtual Networkon kívülről való bármilyen más hozzáférése le van tiltva. A virtuális hálózaton kívülről való hozzáférés engedélyezéséhez további hálózati biztonsági csoportokra vonatkozó szabályokat kell hozzáadnia.

Az alábbi kód bemutatja, hogyan engedélyezhető az SSH-hozzáférés az internetről:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Példa: DNS-konfiguráció

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Névfeloldás a virtuális hálózat és a csatlakoztatott helyszíni hálózat között

Ez a példa a következő feltételezéseket biztosítja:

* Van egy Azure-Virtual Network, amely egy VPN-átjáró használatával csatlakozik egy helyszíni hálózathoz.

* A virtuális hálózatban lévő egyéni DNS-kiszolgáló a Linux vagy a UNIX operációs rendszert futtatja.

* A [kötés](https://www.isc.org/downloads/bind/) telepítve van az egyéni DNS-kiszolgálón.

A virtuális hálózatban található egyéni DNS-kiszolgálón:

1. A virtuális hálózat DNS-utótagjának megkereséséhez használja a Azure PowerShell vagy az Azure CLI-t:

    Cserélje le a helyére a `RESOURCEGROUP` virtuális hálózatot tartalmazó erőforráscsoport nevét, majd írja be a parancsot:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. A virtuális hálózat egyéni DNS-kiszolgálóján használja a következő szöveget a fájl tartalmának megfelelően `/etc/bind/named.conf.local` :

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Cserélje le az `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` értéket a virtuális hálózatának DNS-utótagjának helyére.

    Ez a konfiguráció a virtuális hálózat DNS-utótagjának összes DNS-kérelmét az Azure rekurzív feloldóra irányítja.

1. A virtuális hálózat egyéni DNS-kiszolgálóján használja a következő szöveget a fájl tartalmának megfelelően `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Cserélje le az `10.0.0.0/16` értéket a virtuális hálózata IP-címének tartományára. Ez a bejegyzés lehetővé teszi a névfeloldási kérelmek címét ezen a tartományon belül.

    * Adja hozzá a helyszíni hálózat IP-címtartományt a `acl goodclients { ... }` szakaszhoz.  a bejegyzés lehetővé teszi a névfeloldási kérelmeket a helyszíni hálózat erőforrásaiból.
    
    * Cserélje le az értéket a `192.168.0.1` helyi DNS-kiszolgáló IP-címére. Ez a bejegyzés az összes többi DNS-kérelmet a helyszíni DNS-kiszolgálóra irányítja.

1. A konfiguráció használatához indítsa újra a kötést. Például: `sudo service bind9 restart`.

1. Feltételes továbbító hozzáadása a helyszíni DNS-kiszolgálóhoz. Konfigurálja a feltételes továbbítót úgy, hogy az 1. lépésben a DNS-utótagra vonatkozó kérelmeket küldjön az egyéni DNS-kiszolgálónak.

    > [!NOTE]  
    > A feltételes továbbító hozzáadásával kapcsolatos részletekért olvassa el a DNS-szoftver dokumentációját.

A lépések elvégzése után bármelyik hálózat erőforrásaihoz csatlakozhat teljes tartománynevek (FQDN) használatával. Most már telepítheti a HDInsight-t a virtuális hálózatba.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Névfeloldás két csatlakoztatott virtuális hálózat között

Ez a példa a következő feltételezéseket biztosítja:

* Két olyan Azure-beli virtuális hálózattal rendelkezik, amely VPN-átjáróval vagy-társítással van csatlakoztatva.

* Az egyéni DNS-kiszolgáló mindkét hálózatban a Linux vagy a UNIX operációs rendszert futtatja.

* A [kötés](https://www.isc.org/downloads/bind/) az egyéni DNS-kiszolgálókra van telepítve.

1. Mindkét virtuális hálózat DNS-utótagjának megkereséséhez használja a Azure PowerShell vagy az Azure CLI-t:

    Cserélje le a helyére a `RESOURCEGROUP` virtuális hálózatot tartalmazó erőforráscsoport nevét, majd írja be a parancsot:

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Használja a következő szöveget a fájl tartalmának az `/etc/bind/named.config.local` Egyéni DNS-kiszolgálón. Végezze el ezt a módosítást az egyéni DNS-kiszolgálón mindkét virtuális hálózaton.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Cserélje le az `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` értéket a __másik__ virtuális hálózat DNS-utótagjának helyére. Ez a bejegyzés a távoli hálózat DNS-utótagjának kéréseit az adott hálózatban lévő egyéni DNS-re irányítja.

3. A mindkét virtuális hálózatban lévő egyéni DNS-kiszolgálókon használja a következő szöveget a fájl tartalmának megfelelően `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Cserélje le az `10.0.0.0/16` és az `10.1.0.0/16` értékeket a virtuális hálózatok IP-címeinek tartományára. Ez a bejegyzés lehetővé teszi az egyes hálózatokban lévő erőforrások számára, hogy a DNS-kiszolgálókat kérik.

    A virtuális hálózatok (például microsoft.com) DNS-utótagját nem tartalmazó kérelmeket az Azure rekurzív feloldó kezeli.

4. A konfiguráció használatához indítsa újra a kötést. Például `sudo service bind9 restart` mindkét DNS-kiszolgálón.

A lépések elvégzése után a virtuális hálózat erőforrásaihoz teljes tartománynevek (FQDN) használatával kapcsolódhat. Most már telepítheti a HDInsight-t a virtuális hálózatba.

## <a name="next-steps"></a>Következő lépések

* A helyszíni hálózathoz való kapcsolódás HDInsight konfigurálásának teljes példáját lásd: [a HDInsight összekapcsolása egy helyszíni hálózattal](./connect-on-premises-network.md).
* Az Apache HBase-fürtök Azure-beli virtuális hálózatokban való konfigurálásával kapcsolatban lásd: [Apache HBase-fürtök létrehozása a HDInsight-ben az azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Az Apache HBase geo-replikáció konfigurálásával kapcsolatban lásd: [Apache HBase-fürtök replikálásának beállítása az Azure Virtual Networks](hbase/apache-hbase-replication.md)szolgáltatásban.
* Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért tekintse meg az [azure Virtual Network áttekintését](../virtual-network/virtual-networks-overview.md).

* A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md).

* A felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
