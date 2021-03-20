---
title: 'Oktatóanyag: egyetlen AD-erdő integrálása az Azure-ba a PHS használatával'
description: Bemutatja, hogyan lehet hibrid identitás-környezetet beállítani a jelszó-kivonatolási szinkronizálás használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82dcc633f0f2bb13cb84f80873117f9eb94a24f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96176120"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Oktatóanyag: egyetlen AD-erdő integrálása a jelszó-kivonatolási szinkronizálással (PHS)

![Létrehozás](media/tutorial-password-hash-sync/diagram.png)

A következő oktatóanyag végigvezeti a hibrid identitási környezet létrehozásán a jelszó-kivonatolási szinkronizálás használatával.  Ezt a környezetet ezután tesztelésre vagy a hibrid identitás működésének megismerésére használhatja.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek
- [Hyper-V-](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) t futtató számítógép.  Ezt a [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) rendszerű számítógépeken javasolt elvégezni.
- Egy [külső hálózati adapter](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) , amely lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- A Windows Server 2016 másolata

> [!NOTE]
> Ez az oktatóanyag PowerShell-parancsfájlokat használ, hogy a leggyorsabb idő alatt létre tudja hozni az oktatóanyag-környezetet.  Mindegyik parancsfájl a parancsfájlok elején deklarált változókat használ.  A változókat a környezetnek megfelelően módosítani lehet.
>
>A használt szkriptek a Azure AD Connect telepítése előtt létrehoznak egy általános Active Directory környezetet.  Ezek minden oktatóanyaghoz relevánsak.
>
> Az oktatóanyagban használt PowerShell-parancsfájlok példányai [itt](https://github.com/billmath/tutorial-phs)érhetők el a githubon.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az első teendő, hogy a hibrid identitás-környezet felépítésének és működtetésének megkezdéséhez hozzon létre egy virtuális gépet, amelyet a helyszíni Active Directory-kiszolgálóként fog használni.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi parancsfájlt.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Az operációs rendszer központi telepítésének befejezése
A virtuális gép létrehozásának befejezéséhez be kell fejeznie az operációs rendszer telepítését.

1. Hyper-V kezelője, kattintson duplán a virtuális gépre
2. Kattintson a Start gombra.
3. A rendszer felszólítja, hogy a CD-ről vagy DVD-ről történő rendszerindításhoz nyomja le bármelyik billentyűt. Ugorjon erre.
4. A Windows Server Start up képernyőn válassza ki a nyelvet, és kattintson a **tovább** gombra.
5. Kattintson a **Telepítés most** lehetőségre.
6. Adja meg a licenckulcs, és kattintson a **tovább** gombra.
7. Jelölje be a * * Elfogadom a licencfeltételeket, majd kattintson a **tovább** gombra.
8. Válassza az **Egyéni: csak a Windows telepítése (speciális) lehetőséget.**
9. Kattintson a **tovább** gombra
10. Miután a telepítés befejeződött, indítsa újra a virtuális gépet, jelentkezzen be, és futtassa a Windows-frissítéseket, hogy a virtuális gép a legnaprakészebb legyen.  Telepítse a legújabb frissítéseket.

## <a name="install-active-directory-prerequisites"></a>Active Directory Előfeltételek telepítése
Most, hogy van egy virtuális gép, a Active Directory telepítése előtt végre kell hajtani néhány dolgot.  Éppen ezért át kell neveznie a virtuális gépet, meg kell adnia egy statikus IP-címet és a DNS-adatokat, és telepítenie kell a Távoli kiszolgálófelügyelet eszközeit.   Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi parancsfájlt.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Windows Server AD-környezet létrehozása
Most, hogy létrehozta a virtuális gépet, és átnevezték, és statikus IP-címmel rendelkezik, a Active Directory Domain Services telepítését és konfigurálását is elvégezheti.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi parancsfájlt.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD-felhasználó létrehozása
Most, hogy rendelkezünk a Active Directory-környezettel, tesztelési fiókra van szükségünk.  Ez a fiók a helyszíni AD-környezetben jön létre, majd szinkronizálva lesz az Azure AD-vel.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi parancsfájlt.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Azure AD-bérlő létrehozása
Most létre kell hozni egy Azure AD-bérlőt, hogy szinkronizálni lehessen a felhasználókat a felhőben.  Új Azure AD-bérlő létrehozásához tegye a következőket.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. Válassza ki a **plusz ikont (+)** és keresse meg az **Azure Active Directoryt**.
3. Válassza ki az **Azure Active Directoryt** a keresési eredmények közül.
4. Válassza a **Létrehozás** lehetőséget.</br>
![Képernyőkép, amely bemutatja, hogyan hozhat létre egy Azure AD-bérlőt.](media/tutorial-password-hash-sync/create1.png)</br>
5. Adja meg a **szervezet nevét** a **kezdeti tartománynevet**. Ezután kattintson a **Létrehozás** elemre. Ezzel létrejön a címtár.
6. Miután ez befejeződik, kattintson az **ide** hivatkozásra a címtár kezeléséhez.

## <a name="create-a-global-administrator-in-azure-ad"></a>Globális rendszergazda létrehozása az Azure AD-ben
Most, hogy rendelkezünk egy Azure AD-Bérlővel, globális rendszergazdai fiókot hozunk létre.  Ezzel a fiókkal lehet létrehozni az Azure AD Connector-fiókot Azure AD Connect telepítés közben.  Az Azure AD Connector-fiók az információk Azure AD-be való írásához használatos.   A globális rendszergazdai fiók létrehozásához tegye a következőket.

1.  A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.</br>
![Képernyőfelvétel: a kezelés szakaszban kiválasztott felhasználói lehetőség, amelyben globális rendszergazda hozható létre az Azure AD-ben.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Válassza a **Minden felhasználó**, majd az **+ Új felhasználó** lehetőséget.
3.  Adjon meg egy nevet és egy felhasználónevet ennek a felhasználónak. Ez lesz a bérlő globális rendszergazdája. A **címtárbeli szerepkört** a **globális rendszergazdára** is módosítani kívánja. Megjelenítheti az ideiglenes jelszót is. Ha elkészült, kattintson a **Létrehozás** gombra.</br>
![Képernyőfelvétel: az Azure AD-ben globális rendszergazda létrehozásakor kiválasztott létrehozás gomb látható.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Ha ez befejeződik, nyisson meg egy új webböngészőt, és jelentkezzen be a myapps.microsoft.com-be az új globális rendszergazdai fiókkal és az ideiglenes jelszóval.
5. Módosítsa a globális rendszergazda jelszavát úgy, hogy megjegyezzen.

## <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect letöltése és telepítése
Most itt az ideje, hogy letöltse és telepítse Azure AD Connect.  A telepítés után az expressz telepítéssel fogunk futni.  Tegye a következőket:

1. [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) letöltése
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. Az Express settings (Gyorsbeállítások) képernyőn kattintson a **Use express settings** (Gyorsbeállítások használata) lehetőségre.</br>  
![Képernyőkép, amely az expressz beállítások képernyőt és az expressz beállítások használata gombot jeleníti meg.](media/tutorial-password-hash-sync/express1.png)</br>
5. A Kapcsolódás az Azure AD-hoz képernyőn adja meg az Azure AD globális rendszergazdájának felhasználónevét és jelszavát. Kattintson a **Tovább** gombra.  
6. A Connect to AD DS (Csatlakozás az AD DS szolgáltatáshoz) képernyőn adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. Kattintson a **Tovább** gombra.  
7. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
8. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
9. A telepítés befejezése után jelentkezzen ki, majd jelentkezzen be újra, mielőtt a Synchronization Service Manager vagy a szinkronizációs szabály szerkesztőjét használja.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>A felhasználók létrehozásának és szinkronizálásának ellenőrzése
Most ellenőrizzük, hogy a helyszíni címtárban található felhasználók szinkronizálva lettek-e, és már léteznek-e az Azure AD-bérlőben.  Vegye figyelembe, hogy ez eltarthat néhány óráig.  A felhasználók szinkronizálásának ellenőrzéséhez tegye a következőket.


1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza a **Azure Active Directory**
3. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4. Ellenőrizze, hogy megjelenik-e az új felhasználók a bérlőben</br>
![Szinkronizálási](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés az egyik felhasználóval

1. Nyissa meg a következő címet a böngészőben: [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy olyan felhasználói fiókkal, amely az új bérlőben lett létrehozva.  A következő formátumban kell bejelentkeznie: ( user@domain.onmicrosoft.com ). Ugyanazt a jelszót használja, amelyet a felhasználó a helyszíni bejelentkezéshez használ.</br>
   ![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)</br>

Ezzel sikeresen beállított egy hibrid identitási környezetet, amellyel tesztelheti és megismerheti az Azure által kínált lehetőségeket.

## <a name="next-steps"></a>Következő lépések


- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Jelszó-kivonat szinkronizálása](how-to-connect-password-hash-synchronization.md)|