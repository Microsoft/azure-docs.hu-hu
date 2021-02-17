---
title: Belső hiba történik, amikor RDP-kapcsolattal csatlakozik az Azure Virtual Machineshoz | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani az RDP belső hibáit a Microsoft Azureban. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 5c8bd335832a950385f88f13dc31eb7f6159f831
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548131"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Belső hiba jelentkezik, ha távoli asztalon keresztül próbál csatlakozni az Azure-beli virtuális géphez

Ez a cikk olyan hibát ismertet, amely akkor fordulhat elő, amikor a Microsoft Azure virtuális géphez (VM) próbál csatlakozni.


## <a name="symptoms"></a>Hibajelenségek

Nem csatlakozhat Azure-beli virtuális géphez a távoli asztal protokoll (RDP) használatával. A kapcsolatok beragadnak a **távoli konfigurálása** szakaszba, vagy a következő hibaüzenet jelenik meg:

- Belső RDP-hiba
- Belső hiba történt
- Ez a számítógép nem csatlakoztatható a távoli számítógéphez. Próbálkozzon újra a csatlakozással. Ha a probléma továbbra is fennáll, forduljon a távoli számítógép tulajdonosához vagy a hálózati rendszergazdához.


## <a name="cause"></a>Ok

A probléma a következő okok miatt fordulhat elő:

- Lehetséges, hogy a virtuális gépet megtámadták.
- A helyi RSA titkosítási kulcsok nem érhetők el.
- A TLS protokoll le van tiltva.
- A tanúsítvány sérült vagy lejárt.

## <a name="solution"></a>Megoldás

A probléma megoldásához hajtsa végre az alábbi részekben ismertetett lépéseket. Mielőtt elkezdené, készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

### <a name="check-rdp-security"></a>RDP-biztonság keresése

Először ellenőrizze, hogy az RDP-port 3389-es hálózati biztonsági csoportja nem biztonságos (nyílt). Ha nem biztonságos, és \* a bejövő forrás IP-címeként jelenik meg, korlátozza az RDP-portot egy különös-felhasználó IP-címére, majd tesztelje az RDP-hozzáférést. Ha ez nem sikerül, hajtsa végre a következő szakaszban ismertetett lépéseket.

### <a name="use-serial-control"></a>Soros vezérlő használata

A soros konzollal vagy a virtuális [gép offline állapotba](#repair-the-vm-offline) állításával csatlakoztassa a virtuális gép operációsrendszer-lemezét egy helyreállítási virtuális géphez.

A kezdéshez kapcsolódjon a [soros konzolhoz, és nyisson meg egy PowerShell-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális gépen, lépjen a [virtuális gép kijavítása offline](#repair-the-vm-offline) szakaszra.

#### <a name="step-1-check-the-rdp-port"></a>1. lépés: az RDP-port keresése

1. Egy PowerShell-példányban a [netstat](/windows-server/administration/windows-commands/netstat) használatával győződjön meg arról, hogy a 3389-es portot más alkalmazások használják-e:

    ```powershell
    Netstat -anob |more
    ```

2. Ha a Termservice.exe 3389 portot használ, folytassa a 2. lépéssel. Ha a Termservice.exeon kívül más szolgáltatás vagy alkalmazás 3389 portot használ, kövesse az alábbi lépéseket:

    1. Állítsa le a szolgáltatást az 3389 szolgáltatást használó alkalmazáshoz:

        ```powershell
        Stop-Service -Name <ServiceName> -Force
        ```

    2. A Terminal szolgáltatás elindítása:

        ```powershell
        Start-Service -Name Termservice
        ```

2. Ha az alkalmazás nem állítható le, vagy ha ez a módszer nem vonatkozik Önre, módosítsa az RDP portját:

    1. Módosítsa a portot:

        ```powershell
        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice -Force

        Start-Service -Name Termservice
        ```

    2. Állítsa be a tűzfalat az új porthoz:

        ```powershell
        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>
        ```

    3. [Frissítse a hálózati biztonsági csoportot az új portra](../../virtual-network/network-security-groups-overview.md) a Azure Portal RDP-porton.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>2. lépés: a megfelelő engedélyek beállítása az RDP önaláírt tanúsítványhoz

1. Egy PowerShell-példányban futtassa a következő parancsokat egyenként az RDP önaláírt tanúsítvány megújításához:

    ```powershell
    Import-Module PKI

    Set-Location Cert:\LocalMachine 

    $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 

    Remove-Item -Path $RdpCertThumbprint

    Stop-Service -Name "SessionEnv"

    Start-Service -Name "SessionEnv"
    ```

2. Ha ezzel a módszerrel nem tudja megújítani a tanúsítványt, próbálja meg távolról megújítani az RDP önaláírt tanúsítványát:

    1. Egy olyan működő virtuális gépről, amely a problémát okozó virtuális géphez kapcsolódik, írja be az **MMC** parancsot a **Futtatás** mezőbe a Microsoft Management Console megnyitásához.
    2. A **fájl** menüben kattintson a **beépülő modul hozzáadása/eltávolítása** elemre, válassza a **tanúsítványok** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
    3. Válassza a **számítógépfiókok** lehetőséget, válasszon **másik számítógépet**, majd adja hozzá a probléma virtuális gép IP-címét.
    4. Nyissa meg a **távoli Desktop\Certificates** mappát, kattintson a jobb gombbal a tanúsítványra, majd válassza a **Törlés** lehetőséget.
    5. A soros konzolon a PowerShell-példányon indítsa újra a Távoli asztal konfigurációs szolgáltatást:

        ```powershell
        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"
        ```

3. Állítsa vissza a következő mappára vonatkozó engedélyt.

    ```powershell
    remove-module psreadline 

    md c:\temp

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 

    takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 

    Restart-Service TermService -Force
    ```

4. Indítsa újra a virtuális gépet, majd próbálja meg elindítani a Távoli asztal a virtuális géphez való kapcsolódást. Ha a hiba továbbra is fennáll, folytassa a következő lépéssel.

#### <a name="step-3-enable-all-supported-tls-versions"></a>3. lépés: az összes támogatott TLS-verzió engedélyezése

Az RDP-ügyfél a TLS 1,0 protokollt használja alapértelmezett protokollként. Ez azonban a TLS 1,1-re módosítható, amely az új standard lesz. Ha a TLS 1,1 le van tiltva a virtuális gépen, akkor a-kapcsolatok sikertelenek lesznek.

1. Egy CMD-példányban engedélyezze a TLS protokollt:

    ```console
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

2. Ha meg szeretné akadályozni, hogy az AD-házirend felülírja a módosításokat, állítsa le ideiglenesen a csoportházirend frissítését:

    ```console
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
    ```

3. Indítsa újra a virtuális gépet, hogy a módosítások érvénybe lépnek. Ha a probléma megoldódott, futtassa a következő parancsot a csoportházirend újbóli engedélyezéséhez:

    ```console
    sc config gpsvc start= auto sc start gpsvc

    gpupdate /force
    ```

    Ha a módosítás visszaállt, az azt jelenti, hogy a vállalati tartományban van egy Active Directory szabályzat. Ezt a házirendet úgy kell módosítania, hogy elkerülje a probléma ismételt előfordulását.

### <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy helyreállítási virtuális géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](./troubleshoot-recovery-disks-portal-windows.md).
2. Miután az operációsrendszer-lemezt csatlakoztatta a helyreállítási virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3. Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.

#### <a name="enable-dump-log-and-serial-console"></a>A memóriakép és a soros konzol engedélyezése

A memóriakép és a soros konzol engedélyezéséhez futtassa az alábbi szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. cserélje le a meghajtóbetűjelet a virtuális gép megfelelő értékére.

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>A következő mappa engedélyeinek alaphelyzetbe állítása

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa az alábbi parancsfájlt. Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. cserélje le a meghajtóbetűjelet a virtuális gép megfelelő értékére.

    ```console
    Md F:\temp

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt

    takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
    ```

#### <a name="enable-all-supported-tls-versions"></a>Az összes támogatott TLS-verzió engedélyezése

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**), és futtassa a következő parancsokat. A következő parancsfájl azt feltételezi, hogy az illesztőprogram betűjele a csatolt operációsrendszer-lemezhez van hozzárendelve. F. cserélje le a meghajtóbetűjelet a virtuális gép megfelelő értékére.
2. Győződjön meg arról, hogy a TLS engedélyezve van:

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO
    ```

3. Ha a kulcs nem létezik, vagy az értéke **0**, engedélyezze a protokollt a következő parancsfájlok futtatásával:

    ```console
    REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

4. NLA engedélyezése:

    ```console
    REM Enable NLA

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
    ```

5. [Válassza le az operációsrendszer-lemezt, és hozza létre újra a virtuális gépet](./troubleshoot-recovery-disks-portal-windows.md), majd győződjön meg arról, hogy a probléma megoldódott-e.
