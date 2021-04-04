---
title: 'Oktatóanyag: egyetlen AD-erdős környezet összevonása az Azure-ba | Microsoft Docs'
description: Bemutatja, hogyan lehet hibrid identitás-környezetet beállítani az összevonás használatával.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4410708905610562feb15804277021950eb1edeb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96176290"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>Oktatóanyag: egyetlen AD-erdős környezet összevonása a felhőbe

![Létrehozás](media/tutorial-federation/diagram.png)

A következő oktatóanyag végigvezeti a hibrid identitási környezetek összevonással történő létrehozásán.  Ezt a környezetet ezután tesztelésre vagy a hibrid identitás működésének megismerésére használhatja.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek
- [Hyper-V-](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) t futtató számítógép.  Ezt a [Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy [Windows Server 2016](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) rendszerű számítógépeken javasolt elvégezni.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- - Egy [külső hálózati adapter](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) , amely lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel.
- A Windows Server 2016 másolata
- Egy ellenőrizhető [egyéni tartomány](../../active-directory/fundamentals/add-custom-domain.md)

> [!NOTE]
> Ez az oktatóanyag PowerShell-parancsfájlokat használ, hogy a leggyorsabb idő alatt létre tudja hozni az oktatóanyag-környezetet.  Mindegyik parancsfájl a parancsfájlok elején deklarált változókat használ.  A változókat a környezetnek megfelelően módosítani lehet.
>
>A használt szkriptek a Azure AD Connect telepítése előtt létrehoznak egy általános Active Directory környezetet.  Ezek minden oktatóanyaghoz relevánsak.
>
> Az oktatóanyagban használt PowerShell-parancsfájlok példányai [itt](https://github.com/billmath/tutorial-phs)érhetők el a githubon.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az első teendő, hogy a hibrid identitás-környezet felépítésének és működtetésének megkezdéséhez hozzon létre egy virtuális gépet, amelyet a helyszíni Active Directory-kiszolgálóként fog használni.  

>[!NOTE]
>Ha még nem futtatott parancsfájlt a PowerShellben a gazdagépen, a parancsfájlok futtatása előtt futtatnia kell a parancsot `Set-ExecutionPolicy remotesigned` , és igent kell mondania a PowerShellben.

Tegye a következőket:

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
7. Győződjön **meg arról, hogy elfogadom a licencfeltételeket** , és kattintson a **tovább** gombra.
8. Válassza az **Egyéni: csak a Windows telepítése (speciális) lehetőséget.**
9. Kattintson a **tovább** gombra
10. Miután a telepítés befejeződött, indítsa újra a virtuális gépet, jelentkezzen be, és futtassa a Windows-frissítéseket, hogy a virtuális gép a legnaprakészebb legyen.  Telepítse a legújabb frissítéseket.

## <a name="install-active-directory-pre-requisites"></a>Active Directory Előfeltételek telepítése
Most, hogy van egy virtuális gép, a Active Directory telepítése előtt végre kell hajtani néhány dolgot.  Éppen ezért át kell neveznie a virtuális gépet, meg kell adnia egy statikus IP-címet és a DNS-adatokat, és telepítenie kell a Távoli kiszolgálófelügyelet eszközeit.   Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa `Set-ExecutionPolicy remotesigned` az Igen lehetőséget az összes [A] értékre.  Nyomja le az Enter billentyűt.
3. Futtassa az alábbi parancsfájlt.

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
$DomainNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
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

## <a name="create-a-certificate-for-ad-fs"></a>Tanúsítvány létrehozása AD FShoz
Most létrehozunk egy TLS/SSL-tanúsítványt, amelyet a AD FS fog használni.  Ez egy önaláírt tanúsítvány lesz, és csak tesztelési célokra használható.  A Microsoft nem javasolja, hogy éles környezetben ne használjon önaláírt tanúsítványt. Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi parancsfájlt.

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
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

## <a name="add-the-custom-domain-name-to-your-directory"></a>Az egyéni tartománynév hozzáadása a címtárhoz
Most, hogy van egy bérlőnk és egy globális rendszergazda, hozzá kell adnia az egyéni tartományt, hogy az Azure ellenőrizni tudja.  Tegye a következőket:

1. Lépjen vissza a [Azure Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a **minden felhasználó** panel bezárásához.
2. A bal oldalon válassza az **Egyéni tartománynevek** elemet.
3. Válassza az **egyéni tartomány hozzáadása** lehetőséget.</br>
![Képernyőfelvétel: az egyéni tartomány hozzáadása gomb kiemelve.](media/tutorial-federation/custom1.png)</br>
4. Az **Egyéni tartománynevek** mezőben adja meg az egyéni tartomány nevét a mezőbe, majd kattintson a **tartomány hozzáadása** lehetőségre.
5. Az Egyéni tartománynév képernyőn TXT-vagy MX-információkkal fog ellátni.  Ezeket az adatokat hozzá kell adni a tartományhoz tartozó tartományregisztráló DNS-adataihoz.  Ezért a tartományregisztráló a tartomány DNS-beállításaiban adja meg a TXT vagy az MX információt.  Ez lehetővé teszi, hogy az Azure ellenőrizze a tartományt.  Ez akár 24 órát is igénybe vehet az Azure számára az ellenőrzéshez.  További információt az [egyéni tartomány hozzáadása](../../active-directory/fundamentals/add-custom-domain.md) dokumentációban talál.</br>
![Képernyőkép, amely megjeleníti a TXT-vagy MX-adatok hozzáadásának helyét.](media/tutorial-federation/custom2.png)</br>
6. A hitelesítés ellenőrzéséhez kattintson az ellenőrzés gombra.</br>
![Az ellenőrzés után a sikeres ellenőrző üzenetet bemutató képernyőkép.](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Azure AD Connect letöltése és telepítése
Most itt az ideje, hogy letöltse és telepítse Azure AD Connect.  A telepítés után az expressz telepítéssel fogunk futni.  Tegye a következőket:

1. [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) letöltése
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. Az expressz beállítások képernyőn kattintson a **Testreszabás** elemre.  
5. A szükséges összetevők telepítése képernyőn. Kattintson az **Install** (Telepítés) gombra.  
6. A felhasználó bejelentkezési képernyőjén válassza az **összevonás AD FS** lehetőséget, majd kattintson a **tovább** gombra.
![Képernyőkép, amely bemutatja, hol válassza ki a AD FS összevonást.](media/tutorial-federation/fed1.png)

1. A Kapcsolódás az Azure AD-hoz képernyőn adja meg a fent létrehozott globális rendszergazda felhasználónevét és jelszavát, majd kattintson a **tovább** gombra.
2. A címtárak összekapcsolása képernyőn kattintson a **könyvtár hozzáadása** lehetőségre.  Ezután válassza az **új ad-fiók létrehozása** lehetőséget, és adja meg a Contoso\Rendszergazda felhasználónevét és jelszavát, majd kattintson **az OK** gombra.
3. Kattintson a **Tovább** gombra.
4. Az Azure AD bejelentkezési konfiguráció képernyőjén válassza a Folytatás lehetőséget **anélkül, hogy az összes UPN-utótagot egyeztetni kívánja a tartományokhoz** , és kattintson a **Tovább gombra.**
5. A tartomány és szervezeti egység szűrése képernyőn kattintson a **tovább** gombra.
6. A felhasználók egyedi azonosítása képernyőn kattintson a **tovább** gombra.
7. A felhasználók és eszközök szűrése képernyőn kattintson a **tovább** gombra.
8. A választható szolgáltatások képernyőn kattintson a **tovább** gombra.
9. A tartományi rendszergazda hitelesítő adatai lapon adja meg a Contoso\rendszergazda felhasználónevét és jelszavát, majd kattintson a **Tovább gombra.**
10. A AD FS Farm képernyőjén ellenőrizze, hogy be van-e jelölve az **új AD FS Farm konfigurálása** elem.
11. Jelölje be **az összevonási kiszolgálókon telepített tanúsítvány használata** jelölőnégyzetet, majd kattintson a **Tallózás** gombra.
12. A keresőmezőbe írja be a DC1 kifejezést, és válassza ki azt, amikor az megtalálható.  Kattintson az **OK** gombra.
13. A **tanúsítványfájl** legördülő listából válassza ki a **ADFS.contoso.com** a fent létrehozott tanúsítványt.  Kattintson a **Tovább** gombra.
![Képernyőkép, amely megjeleníti a létrehozott tanúsítványfájl kiválasztásának helyét.](media/tutorial-federation/fed2.png)

1. A AD FS-kiszolgáló képernyőn kattintson a **Tallózás** gombra, és írja be a DC1 kifejezést a keresőmezőbe, és válassza ki, ha megtalálható.  Kattintson az **OK** gombra.  Kattintson a **Tovább** gombra.
![Összevonás](media/tutorial-federation/fed3.png)

1. A webalkalmazás-proxy kiszolgálók képernyőn kattintson a **tovább** gombra.
2. A AD FS szolgáltatásfiók képernyőn adja meg a Contoso\rendszergazda felhasználónevét és jelszavát, majd kattintson a **Tovább gombra.**
3. Az Azure AD-tartomány képernyőn válassza ki az ellenőrzött egyéni tartományt a legördülő menüből, és kattintson a **tovább** gombra.
4. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
5. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
6. A telepítés befejezése után jelentkezzen ki, majd jelentkezzen be újra, mielőtt a Synchronization Service Manager vagy a szinkronizációs szabály szerkesztőjét használja.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>A felhasználók létrehozásának és szinkronizálásának ellenőrzése
Most ellenőrizzük, hogy a helyszíni címtárban található felhasználók szinkronizálva lettek-e, és már léteznek-e az Azure AD-bérlőben.  Vegye figyelembe, hogy ez eltarthat néhány óráig.  A felhasználók szinkronizálásának ellenőrzéséhez tegye a következőket.


1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. A bal oldalon válassza a **Azure Active Directory**
3. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4. Ellenőrizze, hogy megjelenik-e az új felhasználók a bérlői ![ szinkronizálásban](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Bejelentkezés az egyik felhasználóval

1. Nyissa meg a következő címet a böngészőben: [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Jelentkezzen be egy olyan felhasználói fiókkal, amely az új bérlőben lett létrehozva.  A következő formátumban kell bejelentkeznie: ( user@domain.onmicrosoft.com ). Ugyanazt a jelszót használja, amelyet a felhasználó a helyszíni bejelentkezéshez használ.
   ![Ellenőrzés](media/tutorial-password-hash-sync/verify1.png)

Ezzel sikeresen beállított egy hibrid identitási környezetet, amellyel tesztelheti és megismerheti az Azure által kínált lehetőségeket.

## <a name="next-steps"></a>Következő lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
