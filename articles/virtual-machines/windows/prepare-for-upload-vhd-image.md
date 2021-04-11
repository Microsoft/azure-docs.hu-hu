---
title: Windows rendszerű virtuális merevlemez előkészítése az Azure-ba való feltöltésre
description: Útmutató Windows VHD-k vagy VHDX előkészítéséhez az Azure-ba való feltöltéshez
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 573f97c7f592186173b13ea592d151ee291b8249
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967965"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Windows rendszerű VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez

Mielőtt feltöltötte a Windows rendszerű virtuális gépet (VM) a helyszínről az Azure-ba, elő kell készítenie a virtuális merevlemezt (VHD vagy VHDX). Az Azure támogatja az 1. és a 2. generációs virtuális gépeket, amelyek VHD-fájlformátumban vannak, és amelyek rögzített méretű lemezzel rendelkeznek. Az 1. generációs virtuális gépeken az operációsrendszer-VHD számára engedélyezett maximális méret 2 TB.

Átalakíthat egy VHDX-fájlt a VHD-be, konvertálhat egy dinamikusan bővülő lemezt egy rögzített méretű lemezre, de nem módosíthatja a virtuális gép generációját. További információkért lásd: [1. vagy 2. generációs virtuális gép létrehozása a Hyper-V-ben](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) , valamint a [2. generációs virtuális gépek támogatása az Azure](../generation-2.md)-ban.

Az Azure-beli virtuális gépek támogatási szabályzatával kapcsolatos információkért lásd: [Microsoft Server szoftveres támogatás Azure-beli virtuális gépekhez](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> A cikkben szereplő utasítások a következőkre vonatkoznak:
>
> - A Windows Server 2008 R2 és újabb Windows Server operációs rendszerek 64 bites verziója. Az 32 bites operációs rendszerek Azure-ban való futtatásával kapcsolatos információkért lásd: [a 32 bites operációs rendszerek támogatása az Azure-beli virtuális gépeken](https://support.microsoft.com/help/4021388/).
> - Ha a számítási feladatok áttelepítéséhez vész-helyreállítási eszközt használ, például Azure Site Recovery vagy Azure Migrate, akkor ez a folyamat továbbra is szükséges a vendég operációs rendszeren, hogy az áttelepítés előtt előkészítse a rendszerképet.

## <a name="system-file-checker"></a>Rendszerfájl-ellenőrzési

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>A Windows rendszerfájl-ellenőrzési segédprogram futtatása az operációs rendszer rendszerképének általánosítása előtt

A rendszerfájl-ellenőrző (SFC) a Windows rendszerfájljainak ellenőrzéséhez és lecseréléséhez használatos.

> [!IMPORTANT]
> A cikkben szereplő példák futtatásához emelt szintű PowerShell-munkamenetet használjon.

Futtassa az SFC parancsot:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Az SFC-vizsgálat befejeződése után telepítse a Windows-frissítéseket, és indítsa újra a számítógépet.

## <a name="set-windows-configurations-for-azure"></a>Windows-konfigurációk beállítása az Azure-hoz

> [!NOTE]
> Az Azure platform egy ISO-fájlt csatlakoztat a DVD-ROM-hoz, amikor egy Windows rendszerű virtuális gépet egy általánosított rendszerképből hoznak létre. Emiatt a DVD-ROM-ot engedélyezni kell az operációs rendszerben az általánosított rendszerképben. Ha ez a beállítás le van tiltva, a Windows rendszerű virtuális gép elakad a beépített felhasználói élményben (OOBE).

1. Távolítsa el a statikus állandó útvonalakat az útválasztási táblában:

   - Az útválasztási táblázat megtekintéséhez futtassa a parancsot `route.exe print` .
   - Keresse meg az **adatmegőrzési útvonalak** szakaszt. Állandó útvonal esetén a `route.exe delete` parancs használatával távolítsa el azt.

1. Távolítsa el a WinHTTP proxyt:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Ha a virtuális gépnek egy adott proxyval kell dolgoznia, vegyen fel egy proxy-kivételt az Azure IP-címére ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)), hogy a virtuális gép csatlakozni tud az Azure-hoz:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. A DiskPart megnyitása:

   ```powershell
   diskpart.exe
   ```

   A lemez SAN-házirendjének beállítása a következőre [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Állítsa be a Windows egyezményes világidő (UTC) időpontját. Állítsa be a Windows időszolgáltatás **W32Time** indítási típusát is **automatikusra**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Állítsa be az energiagazdálkodási profilt nagy teljesítményre:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Győződjön meg arról, hogy a **temp** és a **tmp** környezeti változó az alapértelmezett értékekre van beállítva:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```
1. A régebbi operációs rendszerekkel rendelkező virtuális gépek esetében (Windows Server 2012 R2 vagy Windows 8,1 vagy újabb) ellenőrizze, hogy telepítve vannak-e a legújabb Hyper-V integrációs összetevő-szolgáltatások. További információ: [a Hyper-V integrációs összetevőinek frissítése a Windows rendszerű virtuális géphez](https://support.microsoft.com/topic/hyper-v-integration-components-update-for-windows-virtual-machines-8a74ffad-576e-d5a0-5a2f-d6fb2594f990).

> [!NOTE]
> Olyan forgatókönyv esetén, amelyben a virtuális gépeket egy vész-helyreállítási megoldással kell beállítani a helyszíni VMware-kiszolgáló és az Azure között, a Hyper-V Integration Component Services nem használható. Ha ez a helyzet, vegye fel a kapcsolatot a VMware-támogatással a virtuális gép Azure-ba való migrálása és a VMware-kiszolgáló közös üzemeltetése érdekében.

## <a name="check-the-windows-services"></a>Windows-szolgáltatások ellenőrzése

Győződjön meg arról, hogy a következő Windows-szolgáltatások mindegyike a Windows alapértelmezett értékre van beállítva. Ezek a szolgáltatások a virtuális gépek közötti kapcsolat biztosításához szükséges minimumok. Az indítási beállítások megadásához futtassa a következő példát:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>A távoli asztal beállításjegyzék-beállításainak frissítése

Győződjön meg arról, hogy a következő beállítások megfelelően vannak konfigurálva a táveléréshez:

> [!NOTE]
> Ha futtatásakor hibaüzenet jelenik meg `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` , nyugodtan figyelmen kívül hagyhatja. Ez azt jelenti, hogy a tartomány nem egy Csoportházirend objektumon keresztül állítja be a konfigurációt.

1. A RDP protokoll (RDP) engedélyezve van:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. Az RDP-port helyesen van beállítva az alapértelmezett 3389-as port használatával:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Amikor üzembe helyez egy virtuális gépet, az alapértelmezett szabályok jönnek létre a 3389-es porthoz. A portszám módosításához tegye azt, hogy a virtuális gép üzembe helyezése az Azure-ban megtörténik.

1. A figyelő minden hálózati adaptert figyel:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Az RDP-kapcsolatok hálózati szintű hitelesítési (NLA) módjának konfigurálása:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. A Keep-Alive érték beállítása:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Az újracsatlakozási beállítások megadása:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Az egyidejű kapcsolatok számának korlátozása:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Távolítsa el az RDP-figyelőhöz kötött összes önaláírt tanúsítványt:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Ez a kód biztosítja, hogy a virtuális gép üzembe helyezése közben is csatlakozhat. Ezeket a beállításokat a virtuális gép Azure-beli üzembe helyezése után is áttekintheti.

1. Ha a virtuális gép egy tartomány része, ellenőrizze a következő házirendeket, és győződjön meg arról, hogy az előző beállítások nem állnak vissza.

    |                 Cél                  |                                                                            Szabályzat                                                                            |                           Érték                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | Az RDP engedélyezve van                        | Számítógép konfigurációja \ Settings\Administrative Templates\Components\Remote Desktop Windows-összetevők asztali munkamenet Host\Connections         | Távolról való kapcsolódás engedélyezése a felhasználók számára a Távoli asztal használatával    |
    | NLA csoportházirend                      | Settings\Administrative Templates\Components\Remote asztali Windows-összetevők asztali munkamenet biztonság csomópontban található                                                    | A távelérés felhasználói hitelesítésének megkövetelése a NLA használatával |
    | Életben tartási beállítások                   | Számítógép konfigurációja \ Settings\Administrative \ Windows-összetevők \ Windows-összetevők Desktop Windows-összetevők asztali munkamenet Host\Connections | Életben tartási kapcsolatok intervallumának konfigurálása                   |
    | Beállítások újracsatolása                    | Számítógép konfigurációja \ Settings\Administrative \ Windows-összetevők \ Windows-összetevők Desktop Windows-összetevők asztali munkamenet Host\Connections | Automatikus újrakapcsolás                                    |
    | Korlátozott számú kapcsolattípus | Számítógép konfigurációja \ Settings\Administrative \ Windows-összetevők \ Windows-összetevők Desktop Windows-összetevők asztali munkamenet Host\Connections | Kapcsolatok számának korlátozása                                |

## <a name="configure-windows-firewall-rules"></a>A Windows tűzfal szabályainak konfigurálása

1. A Windows tűzfal bekapcsolása a három profilra (tartomány, standard és nyilvános):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. A következő példa futtatásával engedélyezheti a WinRM számára a három tűzfal-profilt (tartomány, privát és nyilvános), és engedélyezheti a távoli PowerShell-szolgáltatást:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Az RDP-forgalom engedélyezéséhez engedélyezze a következő tűzfalszabályok használatát:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Engedélyezze a fájl-és nyomtatómegosztás szabályát, hogy a virtuális gép válaszolni tudjon a ping kérelmekre a virtuális hálózaton belül:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Hozzon létre egy szabályt az Azure platform Network számára:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Ha a virtuális gép egy tartomány része, ellenőrizze az alábbi Azure AD-házirendeket, és győződjön meg arról, hogy az előző beállítások nem állnak vissza.

    |                 Cél                 |                                                                         Szabályzat                                                                          |                  Érték                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | A Windows tűzfal profiljainak engedélyezése | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Domain Profile\Windows tűzfal   | Az összes hálózati kapcsolat biztosítása         |
    | RDP engedélyezése                           | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Domain Profile\Windows tűzfal   | Bejövő Távoli asztal kivételek engedélyezése |
    |                                      | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Standard Profile\Windows tűzfal | Bejövő Távoli asztal kivételek engedélyezése |
    | ICMP engedélyezése – v4                       | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Domain Profile\Windows tűzfal   | ICMP-kivételek engedélyezése                   |
    |                                      | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Standard Profile\Windows tűzfal | ICMP-kivételek engedélyezése                   |

## <a name="verify-the-vm"></a>A virtuális gép ellenőrzése

Győződjön meg arról, hogy a virtuális gép kifogástalan, biztonságos, és az RDP elérhető:

1. Győződjön meg arról, hogy a lemez kifogástalan és konzisztens állapotban van, ellenőrizze a következő virtuális gép újraindításának lemezét:

   ```powershell
   chkdsk.exe /f
   ```

   Győződjön meg arról, hogy a jelentés tiszta és kifogástalan állapotú lemezt jelenít meg.

1. Adja meg a rendszerindítási konfigurációs adatok (BCD) beállításait.

   ```powershell
   cmd

   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200

   exit
   ```

1. A memóriakép naplója hasznos lehet a Windows összeomlási problémáinak elhárításához. Memóriakép-gyűjtemény engedélyezése:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Győződjön meg arról, hogy a Windows Management Instrumentation (WMI) tárház konzisztens:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Ha a tárház sérült, tekintse meg a [WMI: adattárház sérülését](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484)ismertető témakört.

1. Győződjön meg arról, hogy egyetlen másik alkalmazás sem használja az 3389-es portot. Ez a port az Azure-beli RDP szolgáltatáshoz használatos. A virtuális gépen használt portok megtekintéséhez futtassa a következőt `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Tartományvezérlőhöz tartozó Windows virtuális merevlemez feltöltése:

   - A lemez előkészítéséhez kövesse [ezeket a további lépéseket](https://support.microsoft.com/kb/2904015) .

   - Győződjön meg arról, hogy ismeri a Címtárszolgáltatások helyreállító módjának (CÍMTÁRSZOLGÁLTATÁSOK helyreállító módjának) jelszavát abban az esetben, ha a virtuális gépet a Címtárszolgáltatások helyreállító módjában szeretné elindítani. További információt a [Címtárszolgáltatások helyreállító módjának beállítása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11))című témakörben talál.

1. Győződjön meg arról, hogy ismeri a beépített rendszergazdai fiókot és jelszót. Előfordulhat, hogy alaphelyzetbe kívánja állítani a helyi rendszergazda aktuális jelszavát, és a fiók használatával bejelentkezhet a Windowsba az RDP-kapcsolaton keresztül. Ezt a hozzáférési engedélyt a "bejelentkezés engedélyezése Távoli asztali szolgáltatások" Csoportházirend objektum vezérli. Az objektum megtekintése a Helyicsoportházirend-szerkesztőban:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Ellenőrizze az alábbi Azure AD-szabályzatokat, és győződjön meg arról, hogy nem blokkolja az RDP-hozzáférést:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Ellenőrizze az alábbi Azure AD-szabályzatot, és győződjön meg arról, hogy nem távolítják el a szükséges hozzáférési fiókokat:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   A szabályzatnak a következő csoportokat kell kilistáznia:

   - Rendszergazdák

   - Backup-operátorok

   - Mindenkinek

   - Felhasználók

1. Indítsa újra a virtuális gépet, és győződjön meg arról, hogy a Windows továbbra is kifogástalan állapotú, és az RDP-kapcsolaton keresztül érhető el. Ezen a ponton érdemes lehet létrehozni egy virtuális gépet a helyi Hyper-V-kiszolgálón, hogy a virtuális gép teljesen elindul. Ezután ellenőrizze, hogy a virtuális gép RDP-kapcsolaton keresztül is elérhető-e.

1. Távolítsa el a további Transport Driver Interface (TDI) szűrőket. Távolítsa el például a TCP-csomagokat vagy a további tűzfalakat elemző szoftvereket.

1. Távolítson el minden olyan külső gyártótól származó szoftvert vagy illesztőprogramot, amely fizikai összetevőkhöz vagy más virtualizációs technológiához kapcsolódik.

### <a name="install-windows-updates"></a>Windows-frissítések telepítése

> [!NOTE]
> A virtuális gépek üzembe helyezése során a véletlen újraindítás elkerülése érdekében javasoljuk, hogy végezze el az összes Windows Update-telepítést, és ellenőrizze, hogy nincs-e függőben lévő újraindítás. Ennek egyik módja az összes Windows-frissítés telepítése és a virtuális gép újraindítása az Azure-ba való áttelepítés előtt. </br><br>
>Ha az operációs rendszer (Sysprep) általánosítását is el kell végeznie, frissítenie kell a Windowst, és a Sysprep parancs futtatása előtt újra kell indítania a virtuális gépet.

Ideális esetben a gépet frissíteni kell a *javítási szintre*, ha ez nem lehetséges, ellenőrizze, hogy telepítve vannak-e a következő frissítések. A legújabb frissítések beszerzéséhez tekintse meg a Windows Update History Pages: Windows [10 és a Windows server 2019](https://support.microsoft.com/help/4000825), a [Windows 8,1, valamint a Windows Server 2012 R2](https://support.microsoft.com/help/4009470) és a Windows [7 sp1 és a Windows Server 2008 R2 SP1 verziót](https://support.microsoft.com/help/4009469).

<br />

|        Összetevő        |     Bináris     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8,1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Tárolás                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 – KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Network (Hálózat)                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Mag                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Távoli asztali szolgáltatások | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 – KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Biztonság                | MS17 – 010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Ha el szeretné kerülni a virtuális gépek kiépítés közbeni véletlen újraindítását, javasoljuk, hogy győződjön meg arról, hogy az összes Windows Update telepítés befejeződött, és hogy nincsenek függőben lévő frissítések. Ennek egyik módja, ha a parancs futtatása előtt telepíti az összes lehetséges Windows-frissítést és újraindítást `sysprep.exe` .

## <a name="determine-when-to-use-sysprep"></a>A Sysprep használatának időpontjának meghatározása

A rendszer-előkészítő eszköz ( `sysprep.exe` ) egy olyan folyamat, amelyet futtathat egy Windows-telepítés alaphelyzetbe állításához.
A Sysprep az összes személyes információ eltávolításával és számos összetevő alaphelyzetbe állításával "kifogyott" élményt biztosít.

Általában a futtatásával `sysprep.exe` létrehozhat egy sablont, amelyből számos más, adott konfigurációval rendelkező virtuális gép üzembe helyezhető. A sablont *általánosított rendszerképnek* nevezzük.

Ha csak egy virtuális gépet szeretne létrehozni egy lemezről, nem kell a Sysprept használnia. Ehelyett a virtuális gépet egy *speciális rendszerképből* is létrehozhatja. A virtuális gépek speciális lemezről történő létrehozásával kapcsolatos információkért lásd:

- [Virtuális gép létrehozása specializált lemezből](create-vm-specialized.md)
- [Virtuális gép létrehozása speciális VHD-lemezről](./create-vm-specialized-portal.md)

Általánosított rendszerkép létrehozásához a Sysprep eszközt kell futtatnia. További információ [: a Sysprep használata: Bevezetés](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Nem minden Windows-alapú számítógépre telepített szerepkör vagy alkalmazás támogatja az általánosított rendszerképeket. Az eljárás használata előtt győződjön meg arról, hogy a Sysprep támogatja a számítógép szerepkörét. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

Különösen a Sysprep megköveteli, hogy a meghajtók teljes mértékben visszafejtve legyenek a végrehajtás előtt. Ha engedélyezte a titkosítást a virtuális gépen, tiltsa le a Sysprep futtatása előtt.


### <a name="generalize-a-vhd"></a>Virtuális merevlemez általánosítása

>[!NOTE]
> Miután `sysprep.exe` a következő lépésekben futtatta a parancsot, kapcsolja ki a virtuális gépet. Ne kapcsolja vissza, amíg létre nem hoz egy rendszerképet az Azure-ban.

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
1. Futtasson egy PowerShell-munkamenetet rendszergazdaként.
1. Törölje a Panther könyvtárat (C:\Windows\Panther).
1. Módosítsa a könyvtárat a következőre: `%windir%\system32\sysprep` . Ez után futtassa a `sysprep.exe` parancsot.
1. A **rendszer-előkészítő eszköz** párbeszédpanelen jelölje be a **rendszerszintű felhasználói élmény (OOBE) megadása** jelölőnégyzetet, és győződjön meg arról, hogy az **általánosítás** jelölőnégyzet be van jelölve.

    ![Rendszerelőkészítő eszköz](media/prepare-for-upload-vhd-image/syspre.png)
1. A **leállítási beállítások** területen válassza a **Leállítás** lehetőséget.
1. Válassza az **OK** lehetőséget.
1. A Sysprep befejeződése után állítsa le a virtuális gépet. Ne használja az **Újraindítás** lehetőséget a virtuális gép leállításához.

Most már készen áll a virtuális merevlemez feltöltésére. A virtuális gépek általánosított lemezről történő létrehozásával kapcsolatos további információkért lásd: [általánosított virtuális merevlemez feltöltése és használata új virtuális gép létrehozásához az Azure-ban](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Az egyéni *unattend.xml* -fájlok nem támogatottak. Bár támogatjuk a **additionalUnattendContent** tulajdonságot, amely csak korlátozott támogatást biztosít a [Microsoft-Windows-rendszerhéj-telepítési](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) beállításoknak az Azure-beli kiépítési ügynök által használt *unattend.xml* -fájlhoz való hozzáadásához. Használhatja például a [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) -t a FirstLogonCommands és a LogonCommands hozzáadásához. További információ: [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>A virtuális lemez átalakítása rögzített méretű VHD-re

Az ebben a szakaszban található módszerek egyikének használatával átalakíthatja és átméretezheti a virtuális lemezt az Azure szükséges formátumával:

1. A virtuális merevlemez átalakításának vagy átméretezési folyamatának futtatása előtt biztonsági másolatot készíthet a virtuális gépről.

1. Győződjön meg arról, hogy a Windows VHD megfelelően működik a helyi kiszolgálón. Az Azure-ba való konvertálás vagy az Azure-ba való feltöltés előtt javítsa ki a virtuális gépen található hibákat.

1. Alakítsa át a virtuális lemezt rögzített típusra.

1. A virtuális lemez átméretezése az Azure-követelmények teljesítéséhez:

   1. Az Azure-beli lemezeken az 1 MiB-hez igazított virtuális méretnek kell szerepelnie. Ha a VHD 1 MiB-töredék, akkor át kell méreteznie a lemezt egy több MiB-re. Azok a lemezek, amelyek egy MiB-töredékek, hibákat okoznak, amikor lemezképeket hoznak létre a feltöltött virtuális merevlemezről. A méret ellenőrzéséhez használhatja a PowerShell [Get-VHD](/powershell/module/hyper-v/get-vhd) parancsmagot a "size" érték megjelenítéséhez, amelynek az Azure-ban az 1 MIB többszörösének kell lennie, a "filesize" pedig a "size" értéket, a VHD-lábléchez pedig 512 bájtot.
   
   1. Az 1. generációs virtuális géppel rendelkező operációs rendszer VHD-je számára engedélyezett maximális méret 2 048 GiB (2 TiB), 
   1. Az adatlemezek maximális mérete 32 767 GiB (32 TiB).

> [!NOTE]
> - Ha egy Windows operációsrendszer-lemezt készít elő a rögzített lemezzé való átalakítás után, és szükség esetén átméretezi, hozzon létre egy virtuális gépet, amely a lemezt használja. Kezdjen hozzá, és jelentkezzen be a virtuális gépre, és folytassa a cikk részeit a feltöltéshez való felkészülés befejezéséhez.  
> - Ha olyan adatlemezt készít elő, amely ebben a szakaszban leáll, és továbbra is feltöltheti a lemezt.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>A lemez átalakítása a Hyper-V kezelőjével

1. Nyissa meg a Hyper-V kezelőjét, és a bal oldalon válassza ki a helyi számítógépet. A számítógép lista fölötti menüben válassza a **művelet**  >  **lemez szerkesztése** lehetőséget.
1. A **virtuális merevlemez keresése** lapon válassza ki a virtuális lemezt.
1. A **művelet kiválasztása** lapon válassza a **Konvertálás**  >  **tovább** lehetőséget.
1. A VHDX konvertálásához válassza a **VHD**  >  **tovább** lehetőséget.
1. Dinamikusan bővülő lemezről történő konvertáláshoz válassza a **rögzített méret**  >  **tovább** lehetőséget.
1. Keresse meg és válassza ki az új VHD-fájl mentésének elérési útját.
1. Válassza a **Befejezés** gombot.

### <a name="use-powershell-to-convert-the-disk"></a>A lemez konvertálása a PowerShell használatával

A virtuális lemezt a PowerShell [Convert-VHD](/powershell/module/hyper-v/convert-vhd) parancsmagjának használatával alakíthatja át. Ha a parancsmag telepítésével kapcsolatos információkra van szüksége, tekintse meg [a Hyper-V szerepkör telepítése](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)című témakört.

Az alábbi példa átalakítja a lemezt a VHDX-ről a VHD-re. Emellett átalakítja a lemezt egy dinamikusan bővülő lemezről a rögzített méretű lemezre.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

Ebben a példában a **görbe** értékét cserélje le a konvertálni kívánt virtuális merevlemez elérési útjára. Cserélje le a **DestinationPath** értékét a konvertált lemez új elérési útjára és nevére.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>A lemez átméretezése a Hyper-V kezelőjével

1. Nyissa meg a Hyper-V kezelőjét, és a bal oldalon válassza ki a helyi számítógépet. A számítógép lista fölötti menüben válassza a **művelet**  >  **lemez szerkesztése** lehetőséget.
1. A **virtuális merevlemez keresése** lapon válassza ki a virtuális lemezt.
1. A **művelet kiválasztása** lapon válassza a következő **kibontása** lehetőséget  >  .
1. A **virtuális merevlemez keresése** lapon adja meg az új méretet a GIB > **következőben**.
1. Válassza a **Befejezés** gombot.

### <a name="use-powershell-to-resize-the-disk"></a>A lemez átméretezése a PowerShell használatával

A virtuális lemezek átméretezhetők a PowerShell [átméretezés-VHD](/powershell/module/hyper-v/resize-vhd) parancsmagjának használatával. Ha a parancsmag telepítésével kapcsolatos információkra van szüksége, tekintse meg [a Hyper-V szerepkör telepítése](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)című témakört.

Az alábbi példa átméretezi a lemezt a 100,5 MiB-ről a 101 MiB-re, hogy megfeleljen az Azure-igazítási követelményeknek.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

Ebben a példában a **görbe** értékét cserélje le az átméretezni kívánt virtuális merevlemez elérési útjára. Cserélje le a **SizeBytes** értékét a lemez új méretére bájtban.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertálás VMware VMDK lemez formátumból

Ha a Windows rendszerű virtuálisgép-rendszerkép [VMDK fájlformátumban](https://en.wikipedia.org/wiki/VMDK)van, akkor a [Azure Migrate](../../migrate/server-migrate-overview.md) segítségével ÁTalakíthatja a VMDK, és feltöltheti azt az Azure-ba.

## <a name="complete-the-recommended-configurations"></a>A javasolt konfigurációk végrehajtása

A következő beállítások nem érintik a VHD feltöltését. Javasoljuk azonban, hogy konfigurálja azokat.

- Telepítse az [Azure Virtual Machine Agent ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789). Ezután engedélyezheti a virtuálisgép-bővítményeket. A virtuálisgép-bővítmények a legtöbb kritikus funkciót implementálják, amelyeket érdemes használni a virtuális gépekhez. Szüksége lesz a bővítményekre, például a jelszavak alaphelyzetbe állításához vagy az RDP konfigurálásához. További információ: az [Azure Virtual Machine Agent áttekintése](../extensions/agent-windows.md).
- Miután létrehozta a virtuális gépet az Azure-ban, javasoljuk, hogy a teljesítmény növelése érdekében helyezze el a lapozófájlt az *ideiglenes meghajtó kötetén* . A fájl elhelyezését az alábbiak szerint állíthatja be:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Ha egy adatlemez csatlakozik a virtuális géphez, az ideiglenes meghajtó kötetének betűjele általában *D*. Ez a megjelölés eltérő lehet a beállításaitól és a rendelkezésre álló meghajtók számától függően.

  - Javasoljuk, hogy tiltsa le a víruskereső szoftver által biztosított parancsfájl-blokkolók letiltását. Előfordulhat, hogy az új virtuális gép lemezképből való üzembe helyezése során a Windows kiépítési ügynök parancsfájljait nem zavarja és blokkolja.

## <a name="next-steps"></a>Következő lépések

- [Windowsos virtuálisgép-rendszerkép feltöltése az Azure-ba Resource Manager-alapú üzembe helyezésekhez](upload-generalized-managed.md)
- [Az Azure Windows VM aktiválási problémáinak elhárítása](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
