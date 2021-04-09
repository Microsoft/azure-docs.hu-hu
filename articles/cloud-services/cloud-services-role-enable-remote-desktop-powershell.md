---
title: Szerepkörök Távoli asztalának engedélyezése a PowerShell használatával
description: Azure Cloud Service-alkalmazás konfigurálása a PowerShell használatával a távoli asztali kapcsolatok engedélyezéséhez
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5b1650edb575de8fd59ad2495dafcd628a717c02
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610399"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic-using-powershell"></a>Távoli asztali kapcsolat engedélyezése az Azure Cloud Services (klasszikus) szerepkörhöz a PowerShell használatával

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. Az alkalmazással kapcsolatos problémák elhárításához és diagnosztizálásához Távoli asztal-kapcsolatban is használhatja a szolgáltatást.

Ez a cikk azt ismerteti, hogyan engedélyezhető a távoli asztal a Cloud Service-szerepkörökön a PowerShell használatával. Tekintse meg, [hogyan telepítheti és konfigurálhatja a Azure PowerShell](/powershell/azure/) a cikkhez szükséges előfeltételekhez. A PowerShell a Távoli asztal bővítményt használja, így Távoli asztal az alkalmazás telepítése után engedélyezheti.

## <a name="configure-remote-desktop-from-powershell"></a>Távoli asztal konfigurálása a PowerShellből
A [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) parancsmag lehetővé teszi, hogy az távoli asztal a megadott szerepkörökön vagy a Cloud Service-telepítés összes szerepkörén engedélyezze. A parancsmag segítségével megadhatja a távoli asztal felhasználójának felhasználónevét és jelszavát a *hitelesítő adatok* paraméterrel, amely elfogadja a PSCredential objektumot.

Ha interaktívan használja a PowerShellt, a [Get-hitelesítőadats](/powershell/module/microsoft.powershell.security/get-credential) parancsmag meghívásával egyszerűen beállíthatja a PSCredential objektumot.

```powershell
$remoteusercredentials = Get-Credential
```

Ez a parancs egy párbeszédpanelt jelenít meg, amely lehetővé teszi, hogy biztonságos módon adja meg a távoli felhasználó felhasználónevét és jelszavát.

Mivel a PowerShell segítséget nyújt az automatizálási forgatókönyvekben, a **PSCredential** objektumot úgy is beállíthatja, hogy ne kelljen felhasználói beavatkozást végeznie. Először be kell állítania egy biztonságos jelszót. Egy egyszerű szöveges jelszó megadásával kezdődik a [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)használatával történő biztonságos karakterláncra konvertálása. Ezután a biztonságos karakterláncot egy titkosított szabványos karakterlánccá kell konvertálnia a [ConvertFrom-SecureString](/powershell/module/microsoft.powershell.security/convertfrom-securestring)használatával. Most már mentheti a titkosított szabványos karakterláncot egy fájlra a [set-Content](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176959(v=technet.10))paranccsal.

Létrehozhat egy biztonságos jelszavas fájlt is, így nem kell minden alkalommal beírnia a jelszót. Emellett a biztonságos jelszavas fájl is jobb, mint egy egyszerű szövegfájl. A következő PowerShell használatával hozzon létre egy biztonságos jelszavas fájlt:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> A jelszó beállításakor győződjön meg arról, hogy megfelel az [összetettségi követelményeknek](/previous-versions/windows/it-pro/windows-server-2003/cc786468(v=ws.10)).

Ahhoz, hogy a hitelesítő adatokat a biztonságos jelszó fájlból hozza létre, el kell olvasnia a fájl tartalmát, és vissza kell alakítania azokat egy biztonságos karakterláncra az [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)használatával.

A [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/set-azureserviceremotedesktopextension) parancsmag egy *lejárati* paramétert is elfogad, amely meghatározza azt a **dátumot** és időpontot, amikor a felhasználói fiók lejár. Például beállíthatja, hogy a fiók az aktuális dátumtól és időponttól néhány napig lejárjon.

Ez a PowerShell-példa bemutatja, hogyan állíthatja be a Távoli asztal-bővítményt egy felhőalapú szolgáltatásban:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Igény szerint megadhatja az üzembe helyezési tárolóhelyet és a Távoli asztalt engedélyező szerepköröket is. Ha ezek a paraméterek nincsenek megadva, a parancsmag lehetővé teszi a távoli asztal használatát az **éles** üzembe helyezési pont összes szerepkörén.

A Távoli asztal bővítmény társítva van egy központi telepítéshez. Ha új központi telepítést hoz létre a szolgáltatáshoz, engedélyeznie kell a Távoli asztalt az adott központi telepítésben. Ha mindig engedélyezni szeretné a távoli asztal használatát, érdemes megfontolnia a PowerShell-parancsfájlok integrálását a telepítési munkafolyamatba.

## <a name="remote-desktop-into-a-role-instance"></a>Távoli asztal egy szerepkör-példányba

A [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) parancsmag használatával a távoli asztal a felhőalapú szolgáltatás egy adott szerepkör-példányában található. Az RDP-fájl helyi letöltéséhez használhatja a *LocalPath* paramétert. Vagy a *Launch* paraméterrel közvetlenül is elindíthatja a távoli asztali kapcsolat párbeszédpanelt a Cloud Service szerepkör-példány eléréséhez.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Ellenőrizze, hogy a Távoli asztal-bővítmény engedélyezve van-e a szolgáltatáson

A [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/get-azureremotedesktopfile) parancsmag azt jeleníti meg, hogy a távoli asztal engedélyezve van vagy le van tiltva egy szolgáltatás központi telepítésén. A parancsmag visszaadja a távoli asztal felhasználójának felhasználónevét, valamint azokat a szerepköröket, amelyekhez a távoli asztal bővítmény engedélyezve van. Alapértelmezés szerint ez az üzembe helyezési ponton történik, és az átmeneti tárolóhely használata is megadható.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Távoli asztal bővítmény eltávolítása a szolgáltatásból

Ha már engedélyezte a távoli asztal bővítményt egy központi telepítésben, és frissítenie kell a távoli asztal beállításait, először távolítsa el a bővítményt. És engedélyezze újra az új beállításokkal. Ha például új jelszót szeretne beállítani a távoli felhasználói fiókhoz, vagy lejárt a fiók. Ehhez szükség van a távoli asztal bővítményt használó meglévő központi telepítések esetén. Új központi telepítések esetén egyszerűen közvetlenül is alkalmazhatja a bővítményt.

A távoli asztali bővítmény a telepítésből való eltávolításához használhatja a [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure.service/remove-azureserviceremotedesktopextension) parancsmagot. Igény szerint megadhatja azt a telepítési tárolóhelyet és szerepkört is, amelyből el szeretné távolítani a távoli asztali bővítményt.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> A bővítmény konfigurációjának teljes eltávolításához hívja meg a *Remove* parancsmagot a **UninstallConfiguration** paraméterrel.
>
> A **UninstallConfiguration** paraméter eltávolítja a szolgáltatásra alkalmazott bővítmények konfigurációját. Minden bővítmény-konfiguráció a szolgáltatás konfigurációjával van társítva. A *Remove* parancsmag meghívása anélkül, hogy a **UninstallConfiguration** leválasztja a <mark>központi telepítést</mark> a bővítmény konfigurációjától, így gyakorlatilag eltávolítja a bővítményt. A bővítmény konfigurációja azonban továbbra is hozzá van rendelve a szolgáltatáshoz.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)