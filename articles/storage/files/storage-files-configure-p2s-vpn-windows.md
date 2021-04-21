---
title: Pont–hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files | Microsoft Docs
description: Pont–hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de342267292c6a93c4a1ba2eae232403ccaf9514
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785270"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Pont–hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files
Pont–hely (P2S) VPN-kapcsolattal SMB-n keresztül csatlakoztathatja az Azure-fájlmegosztásokat az Azure-ból, a 445-ös port megnyitása nélkül. A pont–hely VPN-kapcsolat egy VPN-kapcsolat az Azure és egy különálló ügyfél között. Ha P2S VPN-kapcsolatot szeretne használni a Azure Files, minden csatlakoztatni kívánó ügyfélhez konfigurálni kell egy P2S VPN-kapcsolatot. Ha sok ügyfélnek kell csatlakoznia az Azure-fájlmegosztáshoz a helyszíni hálózatról, akkor az egyes ügyfelekhez pont–hely kapcsolat helyett használhatja a hely–hely (S2S) VPN-kapcsolatot. További információ: [Configure a Site-to-Site VPN for use with Azure Files.](storage-files-configure-s2s-vpn.md)

Határozottan javasoljuk, [](storage-files-networking-overview.md) hogy olvassa el A közvetlen Azure-fájlmegosztás-hozzáférés hálózati szempontjait, mielőtt továbblép a következő cikkre, amely a szolgáltatáshoz elérhető hálózati lehetőségek teljes Azure Files.

A cikk részletesen beállítja a pont–hely VPN konfigurálás lépéseit Windows rendszeren (Windows-ügyfél és Windows Server), hogy közvetlenül a helyszínen csatlakoztassa az Azure-fájlmegosztásokat. Ha vpn-en keresztül Azure File Sync forgalmat, tekintse meg a proxy- Azure File Sync tűzfalbeállítások [konfigurálását.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Előfeltételek
- A modul legújabb Azure PowerShell. Az operációs rendszer telepítésével kapcsolatos további Azure PowerShell az [Install the Azure PowerShell module](/powershell/azure/install-az-ps) and select your operating system (A Azure PowerShell modul telepítése és az operációs rendszer kiválasztása). Ha inkább Windows rendszeren szeretné használni az Azure CLI-t, akkor előfordulhat, hogy az alábbi utasítások a Azure PowerShell.

- Egy Azure-fájlmegosztás, amely a helyszínen csatlakoztatható. Az Azure-fájlmegosztások tárfiókokba vannak telepítve, amelyek olyan megosztott tárolókészletet képviselő felügyeleti szerkezetek, amelyekben több fájlmegosztást, valamint más tárolási erőforrásokat, például blobtárolókat vagy üzenetsorokat helyezhet üzembe. Az Azure-fájlmegosztások és -tárfiókok üzembe helyezéséről további információt az [Azure-fájlmegosztások létrehozása részében olvashat.](storage-how-to-create-file-share.md)

- A helyszíni csatlakoztatáshoz csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók privát végpontja. További információ a privát végpontok létrehozásáról: [Hálózati Azure Files konfigurálása.](storage-files-networking-endpoints.md?tabs=azure-powershell) 

## <a name="deploy-a-virtual-network"></a>Virtuális hálózat üzembe helyezése
Ahhoz, hogy az Azure-fájlmegosztást és más Azure-erőforrásokat a helyszínről pont–hely VPN-en keresztül el tudja elérni, létre kell hoznia egy virtuális hálózatot vagy virtuális hálózatot. Az automatikusan létrehozott P2S VPN-kapcsolat egy híd a helyszíni Windows-gép és az Azure-beli virtuális hálózat között.

A következő PowerShell létrehoz egy Azure-beli virtuális hálózatot három alhálózattal: egyet a tárfiók szolgáltatásvégpontjának, egyet a tárfiók privát végpontjának, amely a helyszíni tárfiók eléréséhez szükséges anélkül, hogy egyéni útválasztást hoz létre a tárfiók módosítható nyilvános IP-címéhez, és egyet a VPN-szolgáltatást szolgáltató virtuális hálózati átjáróhoz. 

Ne felejtse el lecserélni a , és értékeket a `<region>` `<resource-group>` `<desired-vnet-name>` környezetének megfelelő értékekre.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Főtanúsítvány létrehozása VPN-hitelesítéshez
Ahhoz, hogy hitelesíteni tudja a helyszíni Windows-gépek VPN-kapcsolatait a virtuális hálózat eléréséhez, két tanúsítványt kell létrehoznia: egy főtanúsítványt, amelyet a rendszer biztosít a virtuálisgép-átjárónak, valamint egy ügyféltanúsítványt, amely a főtanúsítvánnyal lesz aláírva. Az alábbi PowerShell létrehozza a főtanúsítványt; az ügyfél tanúsítványa az Azure virtuális hálózati átjáró létrehozása után jön létre az átjáróból származó információkkal. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Virtuális hálózati átjáró üzembe helyezése
Az Azure virtuális hálózati átjáró az a szolgáltatás, amelyhez a helyszíni Windows-gépek csatlakozni fognak. A szolgáltatás telepítéséhez két alapvető összetevőre van szükség: egy nyilvános IP-címre, amely azonosítja az átjárót az ügyfelek számára, bárhol is vannak a világon, valamint egy korábban létrehozott főtanúsítványra, amely az ügyfelek hitelesítésére lesz használva.

Ne felejtse el `<desired-vpn-name-here>` lecserélni a helyére az erőforrásokhoz megfelelő nevet.

> [!Note]  
> Az Azure-beli virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás üzembe helyezése közben ez a PowerShell-szkript letiltja az üzembe helyezés befejezését. Ez a várható eredmény.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Ügyfél-tanúsítvány létrehozása
Az ügyfél tanúsítványa a virtuális hálózati átjáró URI-jára van létrehozva. Ez a tanúsítvány a korábban létrehozott főtanúsítvánnyal van aláírva.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>A VPN-ügyfél konfigurálása
Az Azure-beli virtuális hálózati átjáró létrehoz egy letölthető csomagot a helyszíni Windows-gépen a VPN-kapcsolat inicializálásához szükséges konfigurációs fájlokkal. A VPN-kapcsolatot a Windows 10/Windows Server 2016+ [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) szolgáltatásával konfiguráljuk. Ez a csomag végrehajtható csomagokat is tartalmaz, amelyek szükség esetén konfigurálják az örökölt Windows VPN-ügyfelet. Ez az útmutató alwayson VPN-t használ az örökölt Windows VPN-ügyfél helyett, mivel az Always On VPN-ügyfél lehetővé teszi a végfelhasználók számára az Azure VPN-hez való csatlakozást vagy a kapcsolat bontását anélkül, hogy rendszergazdai jogosultsága lenne a gépükre. 

A következő szkript telepíti a virtuális hálózati átjárón való hitelesítéshez szükséges ügyfél-tanúsítványt, majd letölti és telepíti a VPN-csomagot. Ne felejtse el `<computer1>` lecserélni a és `<computer2>` a értékeket a kívánt számítógépekre. Ezt a szkriptet annyi gépen futtathatja, amennyit csak szeretne, ha további PowerShell-munkameneteket ad hozzá a `$sessions` tömbhöz. A használathoz használt fióknak rendszergazdának kell lennie az egyes gépeken. Ha ezen gépek egyike az a helyi gép, amelyről a szkriptet futtatja, akkor emelt szintű PowerShell-munkamenetből kell futtatnia a szkriptet. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása
Most, hogy beállította a pont–hely VPN-t, csatlakoztathatja vele az Azure-fájlmegosztást a beállított számítógépeken a PowerShell használatával. Az alábbi példa a megosztás csatlakoztatását, a megosztás gyökérkönyvtárának felsorolását követően igazolja, hogy a megosztás valóban csatlakoztatva van, és leválasztja a megosztást. Sajnos nem lehet állandó módon csatlakoztatni a megosztást a PowerShell-eltoláson keresztül. Az állandó csatlakoztatáshoz lásd: [Azure-fájlmegosztás használata Windows rendszeren.](storage-how-to-use-files-windows.md) 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Lásd még
- [A közvetlen Azure-fájlmegosztás-hozzáférés hálózati szempontjai](storage-files-networking-overview.md)
- [Pont–hely (P2S) VPN konfigurálása Linux rendszeren az Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Pont–hely (S2S) VPN konfigurálása az Azure Files](storage-files-configure-s2s-vpn.md)