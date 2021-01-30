---
title: A vendég operációs rendszer tűzfala szolgáltatás letiltása az Azure-beli virtuális gépen | Microsoft Docs
description: Megtudhatja, hogyan lehet megkerülni a hibaelhárítási módszereket olyan helyzetekben, amikor egy vendég operációs rendszer tűzfala részlegesen vagy teljes forgalomban végzi a virtuális gép forgalmát.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 74d06d3d4aaa0d76b80257d2148fb62f71c3fdb0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093195"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>A vendég operációs rendszer tűzfalának letiltása az Azure-beli virtuális gépen

Ez a cikk olyan helyzetekben nyújt olyan helyzeteket, amikor azt gyanítja, hogy a vendég operációs rendszer tűzfala részleges vagy teljes forgalmat végez a virtuális gépen (VM). Ez akkor fordulhat elő, ha a rendszer szándékosan módosította az RDP-kapcsolatokat okozó tűzfalat.

## <a name="solution"></a>Megoldás

A cikkben ismertetett folyamat célja, hogy megkerülő megoldásként lehessen használni, hogy a valós probléma kijavítására koncentráljon, amely a tűzfalszabályok helyes beállítását mutatja be. A Microsoft ajánlott eljárása, hogy a Windows tűzfal összetevője engedélyezve legyen. A tűzfalszabályok konfigurálásának módja a szükséges virtuális géphez való hozzáférés szintjétől függ.

### <a name="online-solutions"></a>Online megoldások 

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton található másik virtuális gépen is elérhető, akkor a másik virtuális gép használatával is elvégezheti ezeket a megoldásokat.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>1. enyhítés: egyéni parancsfájl-kiterjesztés vagy futtatási parancs funkció

Ha rendelkezik működő Azure-ügynökkel, használhatja az [Egyéni szkriptek bővítményét](../extensions/custom-script-windows.md) vagy a [futtatási parancsok](../windows/run-command.md) szolgáltatást (csak Resource Manager-alapú virtuális gépeken) a következő parancsfájlok távoli futtatásához.

> [!Note]
> * Ha a tűzfal helyileg van beállítva, futtassa a következő parancsfájlt:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Ha a tűzfal egy Active Directory házirenden keresztül van beállítva, a következő parancsfájlt használhatja az ideiglenes hozzáféréshez. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' -name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   A házirend újbóli alkalmazása után azonban a távoli munkamenetből kell kiindulnia. A probléma állandó kijavítása a számítógépen alkalmazott szabályzat módosítása.

#### <a name="mitigation-2-remote-powershell"></a>2. enyhítés: távoli PowerShell

1.  Kapcsolódjon egy olyan virtuális GÉPHEZ, amely az RDP-kapcsolat használatával nem elérhető virtuális géppel azonos virtuális hálózaton található.

2.  Nyisson meg egy PowerShell-konzolablak ablakát.

3.  Futtassa az alábbi parancsot:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Ha a tűzfal egy Csoportházirend objektumon keresztül van beállítva, előfordulhat, hogy ez a metódus nem működik, mert a parancs csak a helyi beállításjegyzékbeli bejegyzéseket módosítja. Ha egy házirend van érvényben, akkor felülbírálja ezt a változást. 

#### <a name="mitigation-3-pstools-commands"></a>3. enyhítés: PSTools parancsok

1.  A hibaelhárítási virtuális gépen töltse le a [PsTools](/sysinternals/downloads/pstools).

2.  Nyisson meg egy CMD-példányt, majd a DIP használatával férhet hozzá a virtuális géphez.

3.  Futtassa az alábbi parancsot:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>4. mérséklés: távoli beállításjegyzék 

A [Távoli beállításjegyzék](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/314837)használatához kövesse az alábbi lépéseket.

1.  A hibaelhárítási virtuális gépen indítsa el a Beállításszerkesztőt, majd nyissa meg a **file**  >  **Kapcsolódás hálózati beállításjegyzéket**.

2.  Nyissa meg a *célszámítógép*\SYSTEM ágat, és határozza meg a következő értékeket:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Indítsa újra a szolgáltatást. Mivel ez a távoli beállításjegyzék használatával nem hajtható végre, a távoli szolgáltatási konzolt kell használnia.

4.  Nyissa meg a **Services. msc** egy példányát.

5.  Kattintson a **szolgáltatások (helyi)** elemre.

6.  Válassza **a Kapcsolódás másik számítógéphez** lehetőséget.

7.  Adja meg a probléma virtuális gép **magánhálózati IP-címét (dip)** .

8.  Indítsa újra a helyi tűzfal házirendjét.

9.  Próbálja meg újra a virtuális gépet a helyi számítógépről RDP-kapcsolaton keresztül.

### <a name="offline-solutions"></a>Offline megoldások 

Ha olyan helyzet áll fenn, amelyben a virtuális gépet bármilyen módon nem érheti el, az egyéni szkriptek bővítménye sikertelen lesz, és a rendszerlemezen keresztüli közvetlen munkavégzéssel OFFLINE módban kell működnie. Ehhez kövesse az alábbi lépéseket:

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.

3.  Győződjön meg arról, hogy a lemez online állapotban van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjelet.

4.  A módosítások elvégzése előtt hozzon létre egy másolatot a \Windows\System32\Config mappából abban az esetben, ha a módosítások visszaállítására van szükség.

5.  A hibaelhárítási virtuális gépen indítsa el a Beállításszerkesztőt (regedit.exe). 

6.  Ebben a hibaelhárítási eljárásban a struktúrákat BROKENSYSTEM és BROKENSOFTWARE-ként csatlakoztatjuk.

7.  Jelölje ki a HKEY_LOCAL_MACHINE kulcsot, majd válassza a fájl > betöltési struktúra lehetőséget a menüből.

8.  Keresse meg a \windows\system32\config\SYSTEM fájlt a csatolt rendszerlemezen.

9.  Nyisson meg egy rendszergazda jogú PowerShell-példányt, majd futtassa a következő parancsokat:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gépet](troubleshoot-recovery-disks-portal-windows.md).

11. Győződjön meg arról, hogy a probléma megoldódott-e.
