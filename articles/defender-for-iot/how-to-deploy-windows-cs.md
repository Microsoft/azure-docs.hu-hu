---
title: A C#-ügynök telepítése Windows-eszközre
description: Ismerje meg, hogyan telepítheti a Defendert a IoT agenthez a 32 bites vagy a 64-bites Windows-eszközökön.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: a4223dbc913b7b46cf73c0ed99f607fff67b7e67
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448064"
---
# <a name="deploy-a-defender-for-iot-c-based-security-agent-for-windows"></a>Defender telepítése a Windows rendszerhez készült IoT C#-alapú biztonsági ügynökhöz

Ez az útmutató ismerteti, hogyan telepítheti a Defender for IoT C#-alapú biztonsági ügynökét Windows rendszeren.

Ebből az útmutatóból a következőket tanulhatja meg:

- Telepítés
- Az üzembe helyezés ellenőrzése
- Ügynök eltávolítása
- Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Más platformokon és ügynöki Ízeknél tekintse meg [a megfelelő biztonsági ügynök kiválasztása](how-to-deploy-agent.md)című témakört.

1. Helyi rendszergazdai jogosultságok azon a gépen, amelyre telepíteni kívánja.

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés

A biztonsági ügynök telepítéséhez használja a következő munkafolyamatot:

1. Telepítse a Defender for IoT Windows C# Agent eszközt az eszközön. Töltse le a legújabb verziót a gépre a IoT [GitHub-adattárból](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Bontsa ki a csomag tartalmát, és navigáljon a/install mappára.

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
1. A következő parancs futtatásával adja hozzá a futó engedélyeket a InstallSecurityAgent parancsfájlhoz:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    Ezután futtassa a parancsot:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Például:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    További információ a hitelesítési paraméterekről: [a hitelesítés konfigurálása](concept-security-agent-authentication-methods.md).

A szkript a következő műveleteket végzi el:

* Telepíti az előfeltételeket.
* Hozzáadja a szolgáltatás felhasználóját (az interaktív bejelentkezéssel letiltva).
* Telepíti az ügynököt **rendszerszolgáltatásként**.
* A megadott hitelesítési paraméterekkel konfigurálja az ügynököt.

További segítségért használja a PowerShell Get-Help parancsát.

Get-Help példa:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Központi telepítési állapot ellenőrzése

Az ügynök központi telepítési állapotának ellenőrzéséhez futtassa a következőket:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolítása:

1. Futtassa a következő PowerShell-parancsfájlt a **-Mode** paraméterrel az **Eltávolítás** értékre.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az ügynök nem indul el, kapcsolja be a naplózást (a naplózás alapértelmezés szerint *ki van kapcsolva* ) további információk eléréséhez.

A naplózás bekapcsolása:

1. Nyissa meg a konfigurációs fájlt (General.config) a szerkesztéshez egy szabványos fájlkezelő használatával.

1. Szerkessze a következő értékeket:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > A hibaelhárítás befejezése után javasoljuk a naplózás **kikapcsolását** . A naplózás **bekapcsolásával megnő a** naplófájl mérete és az adatfelhasználás.

1. Indítsa újra az ügynököt a következő PowerShell vagy parancssor futtatásával:

    **PowerShell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   vagy

    **CMD**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. A hibával kapcsolatos további információkért tekintse át a naplófájlt. A naplófájl abban a munkakönyvtárban található, ahol a szkriptet futtatjuk. 

   Naplófájl helye: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Következő lépések

* Olvassa el a Defender for IoT szolgáltatást [– Áttekintés](overview.md)
* További információ a IoT [architektúra](architecture.md) Defender szolgáltatásáról
* A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
* A [GYIK](resources-frequently-asked-questions.md) áttekintése
* A [riasztások](concept-security-alerts.md) ismertetése
