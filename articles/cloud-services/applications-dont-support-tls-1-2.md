---
title: A TLS 1,2-et nem támogató alkalmazások által okozott hibák elhárítása | Microsoft Docs
description: A TLS 1,2-et nem támogató alkalmazások által okozott hibák elhárítása
services: cloud-services
documentationcenter: ''
author: tanmaygore
tags: top-support-issue
ms.assetid: ''
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/16/2020
ms.author: tagore
ms.openlocfilehash: cf7746cc55e81593a1788608cced1253f295a5c4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738378"
---
# <a name="troubleshooting-applications-that-dont-support-tls-12"></a>A TLS 1,2-et nem támogató alkalmazások hibaelhárítása

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a cikk bemutatja, hogyan engedélyezheti a régebbi TLS protokollokat (TLS 1,0 és 1,1), valamint az örökölt titkosítási csomagok alkalmazását a Windows Server 2019 Cloud Service webes és feldolgozói szerepköreinek további protokolljainak támogatásához. 

Tisztában vagyunk vele, hogy a TLS 1,0 és a TLS 1,1 elavult lépéseinek elvégzése közben ügyfeleinknek a régebbi protokollokat és a titkosítási csomagokat is támogatniuk kell, amíg meg nem tervezik az elavult műveleteket.  Habár nem javasoljuk a régi értékek újbóli engedélyezését, útmutatást nyújtunk az ügyfeleknek. Javasoljuk, hogy a jelen cikkben ismertetett módosítások végrehajtása előtt értékelje a regresszió kockázatát. 

> [!NOTE]
> A vendég operációsrendszer-család 6 kiadásának kikényszeríti a TLS 1,2-et a TLS 1,0 és 1,1 explicit módon történő letiltásával, valamint a titkosítási csomagok meghatározott készletének definiálásával. A vendég operációsrendszer-családokkal kapcsolatos további információkért lásd: [vendég operációs rendszer kiadási hírei](./cloud-services-guestos-update-matrix.md#family-6-releases)


## <a name="dropping-support-for-tls-10-tls-11-and-older-cipher-suites"></a>A TLS 1,0, a TLS 1,1 és a régebbi titkosítási csomagok támogatásának eldobása 
A legjobb titkosítást támogató elkötelezettségünk támogatásával a Microsoft bejelentette, hogy a TLS 1,0-es és 1,1-es verzióról az 2017-i júniustól kezdődően elindítják az áttelepítést.   Ez a kezdeti bejelentés óta a Microsoft bejelentette, hogy a Microsoft Edge és az Internet Explorer 11 támogatott verzióiban alapértelmezés szerint letiltjuk a Transport Layer Security (TLS) 1,0-es és 1,1-es verzióját a 2020 első felében.  Az Apple, a Google és a Mozilla hasonló hirdetményei jelzik az iparág irányát.   

További információ: [felkészülés a TLS 1,2-re a Microsoft Azure-ben](https://azure.microsoft.com/updates/azuretls12/)

## <a name="tls-configuration"></a>TLS-konfiguráció  
A Windows Server 2019 Cloud Server rendszerképet a beállításjegyzék szintjén le van tiltva a TLS 1,0 és a TLS 1,1. Ez azt jelenti, hogy a Windows ezen verziójára telepített alkalmazások és a Windows stack for TLS egyeztetés használata nem teszi lehetővé a TLS 1,0 és a TLS 1,1 kommunikációt.   

A kiszolgáló a titkosítási csomagok korlátozott készletét is tartalmazza: 

```
    TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 
    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 
    TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 
    TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 
```

## <a name="step-1-create-the-powershell-script-to-enable-tls-10-and-tls-11"></a>1. lépés: a PowerShell-szkript létrehozása a TLS 1,0 és a TLS 1,1 engedélyezéséhez 

A következő kód példaként használható olyan parancsfájl létrehozásához, amely lehetővé teszi a régebbi protokollok és titkosítási csomagok használatát. A jelen dokumentációban a szkript neve a következő lesz: **TLSsettings.ps1**. A parancsfájlt a helyi asztalon tárolja a későbbi lépésekben való egyszerű hozzáférés érdekében. 


```Powershell
# You can use the -SetCipherOrder (or -sco) option to also set the TLS cipher 
# suite order. Change the cipherorder variable below to the order you want to set on the 
# server. Setting this requires a reboot to take effect.

Param(
 [parameter(Mandatory=$false)]
 [alias("sco")]
 [switch]$SetCipherOrder)

 Function DisableRC4 {
   param ( $restart)
  $subkeys = Get-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL"
  $ciphers = $subkeys.OpenSubKey("Ciphers", $true)

  if($ciphers.SubKeyCount -eq 0) {
    $k1 = $ciphers.CreateSubKey("RC4 128/128")
    $k1.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $restart = $true
    $k2 = $ciphers.CreateSubKey("RC4 64/128")
    $k2.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $k3 = $ciphers.CreateSubKey("RC4 56/128")
    $k3.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
    $k4 = $ciphers.CreateSubKey("RC4 40/128")
    $k4.SetValue("Enabled", 0, [Microsoft.Win32.RegistryValueKind]::DWord)
  }

  $restart

}

Function Set-CryptoSetting {
  param (
    $keyindex,
    $value,
    $valuedata,
    $valuetype,
    $restart
  )

  # Check for existence of registry key, and create if it does not exist
  If (!(Test-Path -Path $regkeys[$keyindex])) {
    New-Item $regkeys[$keyindex] | Out-Null
  }

  # Get data of registry value, or null if it does not exist
  $val = (Get-ItemProperty -Path $regkeys[$keyindex] -Name $value -ErrorAction SilentlyContinue).$value

  If ($null -eq $val) {
    # Value does not exist - create and set to desired value
    New-ItemProperty -Path $regkeys[$keyindex] -Name $value -Value $valuedata -PropertyType $valuetype | Out-Null
    $restart = $True
    Write-Host "Configuring $regkeys[$keyindex]...."

  } Else {

    # Value does exist - if not equal to desired value, change it
    If ($val -ne $valuedata) {
      Set-ItemProperty -Path $regkeys[$keyindex] -Name $value -Value $valuedata
      $restart = $True
      Write-Host "Configuring $regkeys[$keyindex]..."
    }
  }

  $restart

}

$regkeys = @(
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server", #2
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Client", #4
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2",        #6
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server", #8
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0\Client", #10
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 2.0\Server",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0",        #12
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0\Client",
"HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\SSL 3.0\Server", #14
"HKLM:\SOFTWARE\Policies\Microsoft\Cryptography\Configuration\SSL\00010002"
)

Function Set-Windows10PlusCurveOrder {
    param ( $reboot)
    $desiredOrder = "NistP384;NistP256".Split(";")
    If ([Environment]::OSVersion.Version.Major -ge 10) {
        If (!(Test-Path -Path $regkeys[15])) {
            New-Item $regkeys[15] | Out-Null
            $reboot = $True
        }

        $val = (Get-Item -Path $regkeys[15] -ErrorAction SilentlyContinue).GetValue("EccCurves", $null)

        if( $null -eq $val) {
            New-ItemProperty -Path $regkeys[15] -Name EccCurves -Value $desiredOrder -PropertyType MultiString | Out-Null
            $reboot = $True

        } else {

            if ([System.String]::Join(';', $val) -ne [System.String]::Join(';', $desiredOrder)) {
                Write-Host "The original curve order ", `n, $val, `n, "needs to be updated to ", $desiredOrder
                Set-ItemProperty -Path $regkeys[15] -Name EccCurves -Value $desiredOrder
                $reboot = $True
            }
        }
    }

    $reboot

}

If ([Environment]::OSVersion.Version.Major -lt 10) {
  # This is for Windows before 10 
  Write-Host "Configuring Windows before 10..."
  $cipherorder =  "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,"
  $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,"
  $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA"

} Else {

 # this is for windows 10 or above
 Write-Host "Configuring Windows 10+..."
 $cipherorder = "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,"
 $cipherorder += "TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,"
 $cipherorder += "TLS_RSA_WITH_AES_256_GCM_SHA384,TLS_RSA_WITH_AES_128_GCM_SHA256,"
 $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,"
 $cipherorder += "TLS_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA"
}

# If any settings are changed, this will change to $True and the server will reboot
$reboot = $False

# Check for existence of registry keys (SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1, TLS 1.2), and create if they do not exist
For ($i = 0; $i -le 14; $i = $i + 1) {
  If (!(Test-Path -Path $regkeys[$i])) {
    New-Item $regkeys[$i] | Out-Null
  }
}

# Ensure SSL 2.0 disabled for client/server
$reboot = Set-CryptoSetting 10 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 10 Enabled 0 DWord $reboot
$reboot = Set-CryptoSetting 11 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 11 Enabled 0 DWord $reboot

# Ensure SSL 3.0 disabled for client/server
$reboot = Set-CryptoSetting 13 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 13 Enabled 0 DWord $reboot
$reboot = Set-CryptoSetting 14 DisabledByDefault 1 DWord $reboot
$reboot = Set-CryptoSetting 14 Enabled 0 DWord $reboot

# Ensure TLS 1.0 enabled for client/server
$reboot = Set-CryptoSetting 1 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 1 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 2 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 2 Enabled 1 DWord $reboot

# Ensure TLS 1.1 enabled for client/server
$reboot = Set-CryptoSetting 4 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 4 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 5 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 5 Enabled 1 DWord $reboot

# Ensure TLS 1.2 enabled for client/server
$reboot = Set-CryptoSetting 7 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 7 Enabled 1 DWord $reboot
$reboot = Set-CryptoSetting 8 DisabledByDefault 0 DWord $reboot
$reboot = Set-CryptoSetting 8 Enabled 1 DWord $reboot

$reboot = DisableRC4($reboot)

If ($SetCipherOrder) {
      If (!(Test-Path -Path $regkeys[15])) {
        New-Item $regkeys[15] | Out-Null
        $reboot = $True
      }

      $val = (Get-Item -Path $regkeys[15] -ErrorAction SilentlyContinue).GetValue("Functions", $null)

      if ($val -ne $cipherorder)
      {
        Write-Host "The original cipher suite order needs to be updated", `n, $val
        Set-ItemProperty -Path $regkeys[15] -Name Functions -Value $cipherorder
        $reboot = $True
      }
  }

$reboot = Set-Windows10PlusCurveOrder $reboot

If ($reboot) {
  # Randomize the reboot timing since it could be run in a large cluster.
  $tick = [System.Int32]([System.DateTime]::Now.Ticks % [System.Int32]::MaxValue)
  $rand = [System.Random]::new($tick)
  $sec = $rand.Next(30, 600)
  Write-Host "Rebooting after", $sec, " second(s)..."
  Write-Host  "shutdown.exe /r /t $sec /c ""Crypto settings changed"" /f /d p:2:4"
  shutdown.exe /r /t $sec /c "Crypto settings changed" /f /d p:2:4

} Else {

  Write-Host "Nothing get updated."
}
```

## <a name="step-2-create-a-command-file"></a>2. lépés: hozzon létre egy parancsfájlt 

Hozzon létre egy **RunTLSSettings. cmd** nevű cmd-fájlt az alábbi paranccsal. A parancsfájlt a helyi asztalon tárolja a későbbi lépésekben való egyszerű hozzáférés érdekében. 

```cmd
SET LOG_FILE="%TEMP%\StartupLog.txt"
SET EXECUTE_PS1=0

IF "%ComputeEmulatorRunning%" == "" (
       SET EXECUTE_PS1=1
)

IF "%ComputeEmulatorRunning%" == "false" (
       SET EXECUTE_PS1=1
) 

IF %EXECUTE_PS1% EQU 1 (
       echo "Invoking TLSsettings.ps1 on Azure service at %TIME% on %DATE%" >> %LOG_FILE% 2>&1       
       PowerShell -ExecutionPolicy Unrestricted %~dp0TLSsettings.ps1 -sco  >> %LOG_FILE% 2>&1
) ELSE (
       echo "Skipping TLSsettings.ps1 invocation on emulated environment" >> %LOG_FILE% 2>&1       
)    

EXIT /B %ERRORLEVEL%

```

## <a name="step-3-add-the-startup-task-to-the-roles-service-definition-csdef"></a>3. lépés: az indítási feladat hozzáadása a szerepkör Service Definition (csdef) szolgáltatásához 

Adja hozzá a következő kódrészletet a meglévő szolgáltatási definíciós fájlhoz. 

```
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
```

Íme egy példa, amely a feldolgozói szerepkört és a webes szerepkört is megjeleníti. 

```
<?xmlversion="1.0" encoding="utf-8"?> 
<ServiceDefinitionname="CloudServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
    <WebRolename="WebRole1" vmsize="Standard_D1_v2"> 
        <Sites> 
            <Sitename="Web"> 
                <Bindings> 
                    <Bindingname="Endpoint1" endpointName="Endpoint1"/> 
                </Bindings> 
            </Site> 
        </Sites> 
        <Startup> 
            <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
            </Task> 
        </Startup> 
        <Endpoints> 
            <InputEndpointname="Endpoint1" protocol="http" port="80"/> 
        </Endpoints> 
    </WebRole> 
<WorkerRolename="WorkerRole1" vmsize="Standard_D1_v2"> 
    <Startup> 
        <Task executionContext="elevated" taskType="simple" commandLine="RunTLSSettings.cmd"> 
        </Task> 
    </Startup> 
</WorkerRole> 
</ServiceDefinition> 
```

## <a name="step-4-add-the-scripts-to-your-cloud-service"></a>4. lépés: a szkriptek hozzáadása a felhőalapú szolgáltatáshoz 

1) A Visual Studióban kattintson a jobb gombbal a webszerepkörre vagy a WorkerRole
2) Válassza a **Hozzáadás** lehetőséget
3) **Meglévő elem** kijelölése
4) A Fájlkezelőben navigáljon az asztalra, ahol a **TLSsettings.ps1** és a **RunTLSSettings. cmd** fájlt tárolta 
5) Válassza ki a két fájlt a Cloud Services projekthez való hozzáadáshoz

## <a name="step-5-enable-copy-to-output-directory"></a>5. lépés: a másolás engedélyezése a kimeneti könyvtárba

Annak biztosítása érdekében, hogy a szkriptek feltöltése a Visual studióból leküldhető összes frissítéssel megtörténjen, a *Másolás a kimeneti könyvtárba* beállításnak *mindig a másolásra* kell állítania

1) A webrole vagy a WorkerRole alatt kattintson a jobb gombbal a RunTLSSettings. cmd fájlra.
2) **Tulajdonságok** kiválasztása
3) A Tulajdonságok lapon módosítsa a *Másolás a kimeneti könyvtárba* a *mindig* lehetőséget.
4) **TLSsettings.ps1** lépéseinek megismétlése

## <a name="step-6-publish--validate"></a>6. lépés: &-ellenőrzés közzététele

Most, hogy a fenti lépések befejeződik, tegye közzé a frissítést a meglévő Cloud Service-ben. 

Az [SSLLabs](https://www.ssllabs.com/) használatával ellenőrizheti a végpontok TLS-állapotát 

