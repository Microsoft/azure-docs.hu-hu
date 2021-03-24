---
title: Kapcsolódás a Kafka-hoz Virtual Networks használatával – Azure HDInsight
description: Ismerje meg, hogyan kapcsolódhat közvetlenül a Kafka on HDInsight egy Azure-Virtual Networkon keresztül. Ismerje meg, hogyan csatlakozhat a Kafka-hez a fejlesztői ügyfelektől egy VPN-átjáróval vagy a helyszíni hálózatban lévő ügyfelekkel a VPN Gateway eszköz használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-python
ms.date: 03/04/2020
ms.openlocfilehash: eb97e209bc7bb9bc1a15ecadc88593e4c0573b5c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870619"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Csatlakozás az Apache Kafkához HDInsight-on egy Azure-beli virtuális hálózaton keresztül

Megtudhatja, hogyan kapcsolódhat közvetlenül a HDInsight Apache Kafkahoz egy Azure-Virtual Network keresztül. Ebből a dokumentumból megtudhatja, hogyan kapcsolódhat a Kafka-hez a következő konfigurációk használatával:

* Egy helyszíni hálózat erőforrásaiból. Ez a kapcsolat VPN-eszköz (szoftver vagy hardver) használatával jön a helyi hálózaton.
* Egy VPN-ügyfélszoftvert használó fejlesztési környezetből.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Architektúra és tervezés

A HDInsight nem teszi lehetővé a közvetlen kapcsolódást a Kafka-hez a nyilvános interneten keresztül. Ehelyett a Kafka-ügyfeleknek (termelőknek és fogyasztóknak) az alábbi módszerek egyikét kell használniuk:

* Futtassa az ügyfelet ugyanabban a virtuális hálózatban, mint a Kafka on HDInsight. Ezt a konfigurációt a [Start with apache Kafka on HDInsight](apache-kafka-get-started.md) dokumentum használja. Az ügyfél közvetlenül fut a HDInsight-fürt csomópontjain vagy ugyanazon a hálózaton lévő másik virtuális gépen.

* Csatlakoztasson egy magánhálózati hálózatot, például a helyszíni hálózatot a virtuális hálózathoz. Ez a konfiguráció lehetővé teszi, hogy a helyszíni hálózatban lévő ügyfelek közvetlenül működjenek a Kafka használatával. A konfiguráció engedélyezéséhez hajtsa végre a következő feladatokat:

  1. Hozzon létre egy virtuális hálózatot.
  2. Hozzon létre egy VPN-átjárót, amely helyek közötti konfigurációt használ. A dokumentumban használt konfiguráció egy VPN Gateway-eszközhöz csatlakozik a helyszíni hálózaton.
  3. Hozzon létre egy DNS-kiszolgálót a virtuális hálózaton.
  4. Konfigurálja a DNS-kiszolgáló közötti továbbítást az egyes hálózatokban.
  5. Hozzon létre egy Kafka-t a HDInsight-fürtön a virtuális hálózaton.

     További információkért tekintse meg a [kapcsolódás Apache Kafka a helyszíni hálózatról](#on-premises) című szakaszt.

* Az egyes gépeket VPN-átjáró és VPN-ügyfél használatával csatlakoztatható a virtuális hálózathoz. A konfiguráció engedélyezéséhez hajtsa végre a következő feladatokat:

  1. Hozzon létre egy virtuális hálózatot.
  2. Hozzon létre egy VPN-átjárót, amely pont – hely konfigurációt használ. Ez a konfiguráció Windows-és MacOS-ügyfelekkel is használható.
  3. Hozzon létre egy Kafka-t a HDInsight-fürtön a virtuális hálózaton.
  4. A Kafka konfigurálása az IP-hirdetésekhez. Ez a konfiguráció lehetővé teszi, hogy az ügyfél a tartománynevek helyett a Broker IP-címein keresztül kapcsolódjon.
  5. Töltse le és használja a VPN-ügyfelet a fejlesztői rendszeren.

     További információkért lásd a [csatlakozás Apache Kafka VPN-ügyféllel](#vpnclient) című szakaszt.

     > [!WARNING]  
     > Ez a konfiguráció csak fejlesztési célokra ajánlott, a következő korlátozások miatt:
     >
     > * Minden ügyfélnek VPN-ügyfélszoftver használatával kell kapcsolódnia.
     > * A VPN-ügyfél nem továbbítja a névfeloldási kérelmeket a virtuális hálózatnak, ezért az IP-címzést kell használnia a Kafka-vel való kommunikációhoz. Az IP-kommunikációhoz további konfigurációra van szükség a Kafka-fürtön.

A virtuális hálózat HDInsight használatával kapcsolatos további információkért lásd: [virtuális hálózat megtervezése Azure HDInsight-fürtökhöz](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a> Kapcsolódás Apache Kafka helyszíni hálózatról

A helyszíni hálózattal kommunikáló Kafka-fürt létrehozásához kövesse a [HDInsight összekapcsolása](./../connect-on-premises-network.md) a helyszíni hálózati dokumentumhoz című témakör lépéseit.

> [!IMPORTANT]  
> A HDInsight-fürt létrehozásakor válassza ki a __Kafka__ -fürt típusát.

Ezek a lépések a következő konfigurációt hozzanak létre:

* Azure Virtual Network
* Helyek közötti VPN-átjáró
* Azure Storage-fiók (HDInsight által használt)
* Kafka on HDInsight

Annak ellenőrzéséhez, hogy a Kafka-ügyfél tud-e csatlakozni a fürthöz a helyszíni környezetből, hajtsa végre a [példa: Python-ügyfél](#python-client) című szakasz lépéseit.

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a> Csatlakozás Apache Kafka VPN-ügyféllel

A jelen szakaszban ismertetett lépések segítségével hozza létre a következő konfigurációt:

* Azure Virtual Network
* Pont – hely típusú VPN-átjáró
* Azure Storage-fiók (HDInsight által használt)
* Kafka on HDInsight

1. Kövesse az [önaláírt tanúsítványok használata pont – hely kapcsolatokra vonatkozó](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentum lépéseit. Ez a dokumentum hozza létre az átjáróhoz szükséges tanúsítványokat.

2. Nyisson meg egy PowerShell-parancssort, és az alábbi kód használatával jelentkezzen be az Azure-előfizetésbe:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. A következő kód használatával hozhat létre konfigurációs adatokat tartalmazó változókat:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Az Azure-erőforráscsoport és a virtuális hálózat létrehozásához használja a következő kódot:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > A folyamat elvégzése több percet is igénybe vehet.

5. Az Azure Storage-fiók és a blob-tároló létrehozásához használja a következő kódot:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. A HDInsight-fürt létrehozásához használja a következő kódot:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > A folyamat elvégzése körülbelül 15 percet vesz igénybe.

### <a name="configure-kafka-for-ip-advertising"></a>A Kafka konfigurálása az IP-hirdetéshez

Alapértelmezés szerint az Apache Zookeeper a Kafka-ügynökök tartománynevét adja vissza az ügyfeleknek. Ez a konfiguráció nem működik a VPN-ügyfélszoftverrel, mivel nem használhatja a névfeloldást a virtuális hálózatban lévő entitások esetében. Ehhez a konfigurációhoz a következő lépésekkel konfigurálhatja a Kafka-t, hogy a tartománynevek helyett IP-címeket Hirdessen:

1. Egy böngészőben nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net`. Cserélje le a helyére a `CLUSTERNAME` Kafka nevét a HDInsight-fürtön.

    Ha a rendszer kéri, használja a fürt HTTPS-felhasználónevét és-jelszavát. Megjelenik a fürt Ambari webes felhasználói felülete.

2. A Kafka-adatok megtekintéséhez válassza a __Kafka__ lehetőséget a bal oldali listából.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png" alt-text="Szolgáltatások listája a Kafka kiemelésével" border="true":::

3. A Kafka-konfiguráció megtekintéséhez válassza a __konfigurációk__ lehetőséget a felső középső listából.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png" alt-text="Apache Ambari Services-konfiguráció" border="true":::

4. A __Kafka-env__ konfiguráció megkereséséhez írja be a `kafka-env` jobb felső sarokban található __szűrő__ mezőt.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png" alt-text="Kafka-konfiguráció, Kafka-env" border="true":::

5. Ha a Kafka-t IP-címekre szeretné reklámozni, adja hozzá a következő szöveget a __Kafka-env-template__ mező aljához:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A Kafka által figyelt felület konfigurálásához írja be a `listeners` jobb felső sarokban található __szűrő__ mezőt.

7. Ha úgy szeretné beállítani a Kafka-t, hogy az összes hálózati adaptert figyelje, módosítsa a __figyelők__ mező értékét a következőre: `PLAINTEXT://0.0.0.0:9092` .

8. A konfigurációs módosítások mentéséhez használja a Save ( __Mentés__ ) gombot. Adjon meg egy szöveges üzenetet, amely leírja a módosításokat. A módosítások mentése után válassza __az OK gombot__ .

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png" alt-text="Apache Ambari-mentési konfiguráció" border="true":::

9. Ha meg szeretné akadályozni a Kafka újraindításakor fellépő hibákat, használja a __szolgáltatási műveletek__ gombot, és válassza a __karbantartási mód bekapcsolása__ lehetőséget. A művelet befejezéséhez kattintson az OK gombra.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png" alt-text="Szolgáltatási műveletek, a karbantartás bekapcsolásával" border="true":::

10. A Kafka újraindításához használja az __Újraindítás__ gombot, és válassza az __összes érintett újraindítása__ lehetőséget. Erősítse meg az újraindítást, majd használja az __OK__ gombot a művelet befejeződése után.

    :::image type="content" source="./media/apache-kafka-connect-vpn-gateway/restart-required-button.png" alt-text="Újraindítás gomb az összes érintett Kiemelt újraindítással" border="true":::

11. A karbantartási mód letiltásához használja a __szolgáltatási műveletek__ gombot, és válassza a __karbantartási mód kikapcsolása__ lehetőséget. A művelet befejezéséhez kattintson **az OK gombra** .

### <a name="connect-to-the-vpn-gateway"></a>Csatlakozás a VPN-átjáróhoz

A VPN-átjáróhoz való csatlakozáshoz használja a [pont – hely kapcsolati dokumentum konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) a __Csatlakozás az Azure-hoz__ című szakaszát.

## <a name="example-python-client"></a><a id="python-client"></a> Példa: Python-ügyfél

A Kafka-kapcsolat ellenőrzéséhez kövesse az alábbi lépéseket egy Python-gyártó és-fogyasztó létrehozásához és futtatásához:

1. A következő módszerek egyikével kérheti le a Kafka-fürt csomópontjainak teljes tartománynevét (FQDN) és IP-címét:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Ez a szkript feltételezi, hogy a `$resourceGroupName` virtuális hálózatot tartalmazó Azure-erőforráscsoport neve.

    Mentse a visszaadott adatokat a következő lépésekben való használatra.

2. A következő paranccsal telepítheti a [Kafka-Python-](https://kafka-python.readthedocs.io/) ügyfelet:

    ```bash
    pip install kafka-python
    ```

3. Az alábbi Python-kóddal küldheti el az adatgyűjtést a Kafka számára:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Cserélje le a `'kafka_broker'` bejegyzéseket a szakasz 1. lépésében visszaadott címekre:

   * Ha __szoftveres VPN-ügyfelet__ használ, cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok IP-címére.

   * Ha engedélyezte a névfeloldást __egy egyéni DNS-kiszolgálón__, cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok teljes tartománynevére.

     > [!NOTE]
     > Ez a kód elküldi a karakterláncot `test message` a témakörnek `testtopic` . A Kafka HDInsight alapértelmezett konfigurációja a témakör létrehozása, ha nem létezik.

4. A Kafka üzeneteinek lekéréséhez használja a következő Python-kódot:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Cserélje le a `'kafka_broker'` bejegyzéseket a szakasz 1. lépésében visszaadott címekre:

    * Ha __szoftveres VPN-ügyfelet__ használ, cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok IP-címére.

    * Ha engedélyezte a névfeloldást __egy egyéni DNS-kiszolgálón__, cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok teljes tartománynevére.

## <a name="next-steps"></a>Következő lépések

A HDInsight virtuális hálózattal való használatával kapcsolatos további információkért tekintse meg a [virtuális hálózat központi telepítésének megtervezése az Azure HDInsight-fürtökhöz](../hdinsight-plan-virtual-network-deployment.md) című dokumentumot.

Az Azure-Virtual Network pont – hely típusú VPN-átjáróval való létrehozásával kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Pont – hely kapcsolat konfigurálása a Azure Portal használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Pont – hely kapcsolat konfigurálása Azure PowerShell használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

A HDInsight-Apache Kafka használatáról a következő dokumentumokban talál további információt:

* [Ismerkedés a HDInsight Apache Kafkaával](apache-kafka-get-started.md)
* [Tükrözés Apache Kafka HDInsight használatával](apache-kafka-mirroring.md)
