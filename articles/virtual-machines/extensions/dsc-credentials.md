---
title: Hitelesítő adatok továbbítása az Azure-ba a kívánt állapot-konfigurációval
description: Ismerje meg, hogyan továbbíthatja biztonságosan a hitelesítő adatokat az Azure Virtual Machines szolgáltatásba a PowerShell desired State Configuration (DSC) használatával.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.openlocfilehash: 6817dd6baacd835b7d433177ff18af1238ee44a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560076"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Hitelesítő adatok továbbítása az Azure DSCExtension-kezelőhöz

Ez a cikk az Azure kívánt State Configuration (DSC) bővítményét ismerteti. A DSC bővítmény kezelőjének áttekintését lásd: [Az Azure desired State Configuration Extension kezelő bemutatása](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Hitelesítő adatok továbbítása

A konfigurációs folyamat részeként felhasználói fiókokat, hozzáférési szolgáltatásokat kell beállítania, vagy telepítenie kell egy programot egy felhasználói környezetben. Ezen dolgok végrehajtásához hitelesítő adatokat kell megadnia.

A DSC használatával lehet beállítani a paraméteres konfigurációkat. A paraméteres konfigurációban a hitelesítő adatok átkerülnek a konfigurációba, és biztonságosan tárolódnak a. MOF fájlban. Az Azure-bővítmény kezelője leegyszerűsíti a hitelesítő adatok kezelését a tanúsítványok automatikus felügyeletének biztosításával.

A következő DSC-konfigurációs szkript létrehoz egy helyi felhasználói fiókot a megadott jelszóval:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Fontos, hogy a konfiguráció részeként belefoglalja a **localhost csomópontot** . A bővítmény kezelője kifejezetten a **Node localhost** utasítást keresi. Ha ez az utasítás hiányzik, a következő lépések nem működnek. Fontos továbbá a typecast **[PsCredential]** belefoglalása is. Az adott típus aktiválja a bővítményt a hitelesítő adatok titkosításához.

A szkript közzététele az Azure Blob Storage-ban:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Az Azure DSC-bővítmény beállításához és a hitelesítő adatok megadásához:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>A hitelesítő adatok biztonságossá tétele

A kód futtatása hitelesítő adatokat kér. A hitelesítő adatok megadása után a rendszer röviden tárolja a memóriában. Ha a hitelesítő adatok a **set-AzVMDscExtension** parancsmaggal lettek közzétéve, a hitelesítő adatokat a rendszer HTTPS-kapcsolaton keresztül továbbítja a virtuális géphez. A virtuális gépen az Azure a helyi virtuális gép tanúsítványával tárolja a lemezen titkosított hitelesítő adatokat. A hitelesítő adatokat a rendszer röviden visszafejti a memóriában, majd újra titkosítja, hogy átadja a DSC-nek.

Ez a folyamat eltér [a bővítmény-kezelő nélküli biztonságos konfigurációk használatával](/powershell/scripting/dsc/pull-server/securemof). Az Azure-környezet lehetővé teszi, hogy biztonságos módon továbbítsa a konfigurációs adatokat a tanúsítványokon keresztül. A DSC bővítmény kezelőjének használatakor nem kell **$CertificatePath** vagy **$CertificateID** /  **$thumbprint** bejegyzést megadnia a **ConfigurationData**-ben.

## <a name="next-steps"></a>Következő lépések

- Bevezetés az [Azure DSC bővítmény kezelőjébe](dsc-overview.md).
- Vizsgálja [meg a DSC-bővítmény Azure Resource Manager sablonját](dsc-template.md).
- A PowerShell DSC-vel kapcsolatos további információkért nyissa meg a [PowerShell Dokumentációs központját](/powershell/scripting/dsc/overview/overview).
- Ha további funkciókat szeretne kezelni a PowerShell DSC használatával, és további DSC-erőforrásokhoz, keresse fel a [PowerShell-galériát](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).