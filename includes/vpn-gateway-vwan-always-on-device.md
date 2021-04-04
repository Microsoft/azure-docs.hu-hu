---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 34e841a5f17d589c4fbef54a4a8674a99ac6c640
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025481"
---
Az eszköz-alagút sikeres létrehozásához az alábbi követelményeknek kell teljesülniük:

* Az eszköznek olyan tartományhoz csatlakoztatott számítógépnek kell lennie, amely a Windows 10 Enterprise vagy az Education 1809-es vagy újabb verzióját futtatja.
* Az alagút csak a Windows beépített VPN-megoldásához állítható be, és a IKEv2 használatával a számítógép-Tanúsítványos hitelesítéssel van létrehozva.
* Eszközönként csak egy eszköz-alagút állítható be.

1. Telepítse az Ügyféltanúsítványok szolgáltatást a Windows 10-es ügyfélgépre a [pont – hely VPN-ügyfél](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) használatával. A tanúsítványnak a helyi számítógép tárolójába kell esnie.
1. Hozzon létre egy VPN-profilt, és konfigurálja az eszköz-alagutat a helyi rendszerfiók környezetében [ezen utasítások](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)használatával.

### <a name="configuration-example-for-device-tunnel"></a>Az eszköz-alagút konfigurációs példája

Miután konfigurálta a virtuális hálózati átjárót, és telepítette az ügyféltanúsítványt a Windows 10-ügyfél helyi számítógép tárolójába, az alábbi példákkal konfigurálhatja az ügyfél-eszköz alagutat:

1. Másolja az alábbi szöveget, és mentse ***devicecert.ps1ként***.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Másolja a következő szöveget, és mentse ***VPNProfile.xml** _ néven ugyanabba a mappába, amelyben a _ * devicecert.ps1 * *. Szerkessze a következő szöveget a környezetének megfelelően.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Töltse le a **PsExec** -t a [Sysinternals](/sysinternals/downloads/psexec) -ból, és bontsa ki a fájlokat a **C:\PSTools**.
1. A rendszergazda parancssorból indítsa el a PowerShellt a következő parancs futtatásával:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![A képernyőképen egy parancssori ablak látható, amely a PowerShell 64 bites verzióját indítja el.](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. A PowerShellben váltson arra a mappára, ahol **devicecert.ps1** és **VPNProfile.xml** található, és futtassa a következő parancsot:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![A képernyőképen egy PowerShell-ablak látható, amely a MachineCertTest futtatását futtatja a devicesert parancsfájl használatával.](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Futtassa a **Rasphone**.

   ![A képernyőképen egy Futtatás párbeszédpanel jelenik meg, amelyen a Rasphone ki van választva.](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Keresse meg a **MachineCertTest** bejegyzést, és kattintson a **kapcsolat** elemre.

   ![A képernyőfelvétel egy hálózati kapcsolat párbeszédpanelt jelenít meg a kiválasztott MachineCertTest és egy csatlakozás gombbal.](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Ha a kapcsolatok sikeresek, indítsa újra a számítógépet. Az alagút automatikusan fog összekapcsolást.