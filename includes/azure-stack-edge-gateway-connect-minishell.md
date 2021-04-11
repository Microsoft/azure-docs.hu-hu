---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081047"
---
Az ügyfél operációs rendszertől függően az eszközhöz való távoli kapcsolódás eljárásai eltérőek.

### <a name="remotely-connect-from-a-windows-client"></a>Távoli kapcsolat Windows-ügyféllel


#### <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

- A Windows-ügyfél Windows PowerShell 5,0-es vagy újabb verzióját futtatja.
- A Windows-ügyfél rendelkezik az eszközön telepített csomópont-tanúsítványhoz tartozó aláíró lánctal (főtanúsítvánnyal). Részletes útmutatás: a [tanúsítvány telepítése a Windows-ügyfélen](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).
- A `hosts` Windows-ügyfélen található fájl a `C:\Windows\System32\drivers\etc` csomópont-tanúsítványnak megfelelő bejegyzést tartalmaz a következő formátumban:

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    Íme egy példa a `hosts` fájlra:
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>Részletes lépések

Az alábbi lépéseket követve távolról csatlakozhat a Windows-ügyfelekről.

1. Futtasson egy Windows PowerShell-munkamenetet rendszergazdaként.
2. Győződjön meg arról, hogy a Windows távfelügyeleti szolgáltatása fut az ügyfélen. A parancssorba írja be a következőt:

    `winrm quickconfig`

    További információ: a Rendszerfelügyeleti [webszolgáltatások telepítése és konfigurálása](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Rendeljen hozzá egy változót az eszköz IP-címéhez.

    $ip = "<device_ip>"

    Cserélje le az `<device_ip>` eszközt az eszköz IP-címére.

4. Az eszköz IP-címének az ügyfél megbízható gazdagépek listájához való hozzáadásához írja be a következő parancsot:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Indítsa el a Windows PowerShell-munkamenetet az eszközön:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    Ha a megbízhatósági kapcsolattal kapcsolatos hiba jelenik meg, akkor ellenőrizze, hogy az eszközre feltöltött csomópont-tanúsítvány aláíró lánca is telepítve van-e az eszközhöz hozzáférő ügyfélen.

    Ha nem használja a tanúsítványokat (javasoljuk, hogy használja a tanúsítványokat!), a munkamenet-beállítások használatával kihagyhatja ezt az ellenőrzést: `-SkipCACheck -SkipCNCheck -SkipRevocationCheck` .

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > Ha a `-UseSSL` kapcsolót használja, a távoli eljáráshívás *https*-kapcsolaton keresztül történik. Javasoljuk, hogy mindig *https* -kapcsolatot használjon a PowerShell használatával történő távoli kapcsolódáshoz. 

6. Ha a rendszer kéri, adja meg a jelszót. Használja ugyanazt a jelszót, amelyet a helyi webes felhasználói felületen való bejelentkezéshez használ. A helyi webes felhasználói felület alapértelmezett jelszava *jelszó1*. Amikor sikeresen kapcsolódott az eszközhöz a távoli PowerShell használatával, a következő minta kimenet jelenik meg:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> A jelenlegi kiadásban csak Windows-ügyfélen keresztül kapcsolódhat az eszköz PowerShell-felületéhez. A `-UseSSL` beállítás nem működik a Linux-ügyfelekkel.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->