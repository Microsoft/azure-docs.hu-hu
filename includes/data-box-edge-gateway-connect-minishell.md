---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 5343653dc87325fa0702c0864f0df1456a36e304
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532682"
---
Az ügyfél operációs rendszertől függően az eszközhöz való távoli kapcsolódás eljárásai eltérőek.

### <a name="remotely-connect-from-a-windows-client"></a>Távoli kapcsolat Windows-ügyféllel

Mielőtt elkezdené, győződjön meg arról, hogy a Windows-ügyfél a Windows PowerShell 5,0-es vagy újabb verzióját futtatja.

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

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Ha a rendszer kéri, adja meg a jelszót. Használja ugyanazt a jelszót, amelyet a helyi webes felhasználói felületen való bejelentkezéshez használ. A helyi webes felhasználói felület alapértelmezett jelszava *jelszó1*. Amikor sikeresen kapcsolódott az eszközhöz a távoli PowerShell használatával, a következő minta kimenet jelenik meg:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Távoli kapcsolat Linux-ügyféllel

Azon a Linux-ügyfélen, amelyet a kapcsolódáshoz használni fog:

- [Telepítse a Linux rendszerhez készült legújabb PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) -t a githubról az SSH távelérési szolgáltatásának beszerzéséhez. 
- [Csak az `gss-ntlmssp` NTLM-modulból telepítse a csomagot](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Ubuntu-ügyfelek esetén használja a következő parancsot:
    - `sudo apt-get install gss-ntlmssp`

További információért nyissa meg a [PowerShell távoli ELJÁRÁSHÍVÁS SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core)-val című témakört.

Az alábbi lépéseket követve távolról csatlakozhat egy NFS-ügyfélről.

1. A PowerShell-munkamenet megnyitásához írja be a következőt:

    `sudo pwsh`
 
2. A távoli ügyfél használatával történő kapcsolódáshoz írja be a következőt:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Ha a rendszer kéri, adja meg az eszközre való bejelentkezéshez használt jelszót.
 
> [!NOTE]
> Ez az eljárás nem működik Mac OSon.