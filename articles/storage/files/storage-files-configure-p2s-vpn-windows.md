---
title: Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Fileshoz való használatra | Microsoft Docs
description: Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Files használatával
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6253deb53229172cd499a6aa14b8d8f19bc07b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629257"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Files-mel való használatra
A pont – hely (P2S) VPN-kapcsolattal Azure-fájlmegosztás az Azure-on kívülről is csatlakoztatható SMB-n keresztül, a 445-es port megnyitása nélkül. A pont – hely VPN-kapcsolat az Azure és az egyes ügyfelek közötti VPN-kapcsolat. Ha a P2S VPN-kapcsolatot Azure Files használatával szeretné használni, konfigurálnia kell egy P2S VPN-kapcsolatot minden olyan ügyfél számára, amelyhez csatlakozni szeretne. Ha sok ügyféllel kell csatlakoznia az Azure-fájlmegosztás számára a helyszíni hálózatból, akkor az egyes ügyfelek pont – hely kapcsolata helyett használhat helyek közötti (S2S) VPN-kapcsolatot. További információ: helyek közötti [VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md).

Javasoljuk, hogy olvassa el a [hálózatkezelési megfontolásokat az Azure-fájlmegosztás közvetlen eléréséhez](storage-files-networking-overview.md) , mielőtt folytatná a következő témakört, amely ismerteti a Azure Files számára elérhető hálózati beállítások teljes körű megvitatását.

A cikk részletesen ismerteti, hogyan konfigurálhat egy pont – hely típusú VPN-t Windows rendszeren (Windows-ügyfél és Windows Server) az Azure-fájlmegosztás közvetlen helyszíni csatlakoztatásához. Ha VPN-en keresztül szeretné átirányítani Azure File Sync forgalmat, tekintse meg a [Azure file Sync proxy-és tűzfalbeállítások konfigurálása](storage-sync-files-firewall-and-proxy.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek
- A Azure PowerShell modul legújabb verziója. A Azure PowerShell telepítésével kapcsolatos további információkért lásd: [a Azure PowerShell modul telepítése](/powershell/azure/install-az-ps) és az operációs rendszer kiválasztása. Ha inkább az Azure CLI-t szeretné használni a Windowson, akkor az alábbi utasításokat azonban a Azure PowerShell ismerteti.

- Egy Azure-fájlmegosztás, amelyet a helyszínen kíván csatlakoztatni. Az Azure-fájlmegosztás üzembe helyezése a Storage-fiókokban történik, amelyek olyan felügyeleti szerkezetek, amelyek olyan megosztott tárolót képviselnek, amelyben több fájlmegosztás, valamint más tárolási erőforrások, például blob-tárolók vagy várólisták helyezhetők üzembe. Az Azure-fájlmegosztás és a Storage-fiókok Azure- [fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című részében olvashat bővebben.

- Az Azure-fájlmegosztás a helyszínen csatlakoztatni kívánt részét tartalmazó Storage-fiókhoz tartozó magánhálózati végpont. További információ a privát végpontok létrehozásáról: [Azure Files hálózati végpontok konfigurálása](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Virtuális hálózat üzembe helyezése
Ha egy pont – hely típusú VPN-kapcsolaton keresztül szeretné elérni az Azure-fájlmegosztást és más Azure-erőforrásokat, létre kell hoznia egy virtuális hálózatot vagy egy VNet. A automatikusan létrehozandó P2S VPN-kapcsolat a helyszíni Windows-gép és az Azure-beli virtuális hálózat közötti híd.

A következő PowerShell egy három alhálózattal rendelkező Azure-beli virtuális hálózatot hoz létre: egyet a Storage-fiók szolgáltatási végpontján, egyet a Storage-fiókja privát végpontján, amely a helyi Storage-fiókhoz való hozzáféréshez szükséges, anélkül, hogy egyéni útválasztást kellene létrehozni a Storage-fiók nyilvános IP-címéhez, és egyet a VPN szolgáltatást biztosító virtuális hálózati átjáróhoz. 

Ne felejtse el lecserélni a `<region>` `<resource-group>` `<desired-vnet-name>` környezetet a megfelelő értékekre.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Főtanúsítvány létrehozása a VPN-hitelesítéshez
Ahhoz, hogy a helyszíni Windows-gépek VPN-kapcsolatai hitelesítve legyenek a virtuális hálózathoz való hozzáféréshez, két tanúsítványt kell létrehoznia: egy főtanúsítványt, amelyet a rendszer a virtuálisgép-átjáró számára biztosít, valamint egy ügyféltanúsítványt, amely a főtanúsítvánnyal lesz aláírva. A következő PowerShell létrehozza a főtanúsítványt; az ügyféltanúsítvány akkor jön létre, ha az Azure-beli virtuális hálózati átjárót az átjáró információi alapján hozza létre. 

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
Az Azure-beli virtuális hálózati átjáró az a szolgáltatás, amellyel a helyszíni Windows-gépek csatlakozni fognak. A szolgáltatás üzembe helyezéséhez két alapvető összetevő szükséges: egy nyilvános IP-cím, amely azonosítja az átjárót az ügyfelek számára, bárhol is legyenek a világon, valamint egy korábban létrehozott főtanúsítvány, amelyet az ügyfelek hitelesítéséhez fog használni.

Ne felejtse el lecserélni `<desired-vpn-name-here>` az ehhez az erőforrásokhoz hasonló nevet.

> [!Note]  
> Az Azure-beli virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás üzembe helyezése közben ez a PowerShell-parancsfájl letiltja a központi telepítés befejeződését. Ez a várható eredmény.

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

## <a name="create-client-certificate"></a>Ügyféltanúsítvány létrehozása
Az ügyféltanúsítványt a virtuális hálózati átjáró URI-ja hozza létre. Ez a tanúsítvány a korábban létrehozott főtanúsítvánnyal van aláírva.

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
Az Azure Virtual Network Gateway egy letölthető csomagot hoz létre a helyi Windows-gépen a VPN-kapcsolat inicializálásához szükséges konfigurációs fájlokkal. A VPN-kapcsolat a Windows 10/Windows Server 2016 + rendszer [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) funkciójának használatával lesz konfigurálva. Ez a csomag olyan végrehajtható csomagokat is tartalmaz, amelyek az örökölt Windows VPN-ügyfelet fogják konfigurálni, ha szükséges. Ez az útmutató always on VPN-t használ, nem pedig az örökölt Windows VPN-ügyfelet, mint az Always On VPN-ügyfél lehetővé teszi a végfelhasználók számára az Azure VPN-hez való csatlakozást/leválasztást anélkül, hogy rendszergazdai jogosultságokkal 

A következő parancsfájl telepíti a hitelesítéshez szükséges ügyféltanúsítványt a virtuális hálózati átjárón, letölti és telepíti a VPN-csomagot. Ne felejtse el lecserélni `<computer1>` és `<computer2>` használni a kívánt számítógépeket. Ezt a szkriptet tetszőleges számú gépen futtathatja, ha további PowerShell-munkameneteket ad hozzá a `$sessions` tömbhöz. Az Ön által használt fióknak rendszergazdának kell lennie az egyes gépeken. Ha ezen gépek egyike a parancsfájlt futtató helyi gép, a parancsfájlt emelt szintű PowerShell-munkamenetből kell futtatnia. 

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
Most, hogy beállította a pont – hely VPN-t, felhasználhatja az Azure-fájlmegosztás csatlakoztatására a PowerShell használatával telepített számítógépeken. A következő példa felcsatolja a megosztást, lelistázza a megosztás gyökérkönyvtárát, és igazolja, hogy a megosztás valóban csatlakoztatva van, és a megosztás leválasztása megtörténik. Sajnos a megosztást nem lehet állandó módon csatlakoztatni a PowerShell távelérési szolgáltatásához. A folyamatos csatlakoztatáshoz tekintse meg [Az Azure-fájlmegosztás használata a Windowsban](storage-how-to-use-files-windows.md)című témakört. 

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
- [Hálózati megfontolások az Azure fájlmegosztás közvetlen eléréséhez](storage-files-networking-overview.md)
- [Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Files-vel való használatra](storage-files-configure-p2s-vpn-linux.md)
- [Helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md)