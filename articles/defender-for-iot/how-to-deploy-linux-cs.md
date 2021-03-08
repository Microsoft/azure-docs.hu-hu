---
title: Telepítés & Linux C#-ügynök telepítése
description: Ismerje meg, hogyan telepítheti és telepítheti a Defender for IoT C#-alapú biztonsági ügynököt Linux rendszeren
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
ms.openlocfilehash: ec26cd0b66b6d08d8bd686d8f3e5ad8011df10b1
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449764"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>A Defender üzembe helyezése a IoT C#-alapú biztonsági ügynökének Linuxra

Ez az útmutató ismerteti, hogyan telepítheti és telepítheti a Defender for IoT C#-alapú biztonsági ügynökét Linux rendszeren.

Ebből az útmutatóból a következőket tanulhatja meg:

- Telepítés
- Az üzembe helyezés ellenőrzése
- Ügynök eltávolítása
- Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Más platformokon és ügynöki Ízeknél tekintse meg [a megfelelő biztonsági ügynök kiválasztása](how-to-deploy-agent.md)című témakört.

1. A biztonsági ügynök telepítéséhez helyi rendszergazdai jogosultságok szükségesek azon a gépen, amelyre telepíteni kívánja a szolgáltatást.

1. [Hozzon létre egy biztonsági modult](quickstart-create-security-twin.md) az eszközhöz.

## <a name="installation"></a>Telepítés

A biztonsági ügynök üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Töltse le a legújabb verziót a gépére a [githubról](https://aka.ms/iot-security-github-cs).

1. Bontsa ki a csomag tartalmát, és navigáljon a _/install_ mappára.

1. Futó engedélyek hozzáadása a **InstallSecurityAgent parancsfájlhoz** futtatásával `chmod +x InstallSecurityAgent.sh`

1. Ezután futtassa a következő parancsot a **legfelső szintű jogosultságokkal**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   További információ a hitelesítési paraméterekről: [a hitelesítés konfigurálása](concept-security-agent-authentication-methods.md).

Ez a szkript a következő műveleteket hajtja végre:

- Telepíti az előfeltételeket.

- Szolgáltatásbeli felhasználó (interaktív bejelentkezéssel letiltva) hozzáadásával.

- Az ügynököt **démonként** telepíti, feltételezi, hogy az eszköz a klasszikus üzemi modellhez tartozó **rendszert** használja.

- Úgy konfigurálja a **sudoers** -t, hogy az ügynök elvégezzen bizonyos feladatokat root-ként.

- A megadott hitelesítési paraméterekkel konfigurálja az ügynököt.

További segítségért futtassa a parancsfájlt a – Súgó paraméterrel: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolításához futtassa a parancsfájlt az – u paraméterrel: `./InstallSecurityAgent.sh -u` .

> [!NOTE]
> Az eltávolítás nem távolítja el a telepítés során telepített hiányzó előfeltételeket.

## <a name="troubleshooting"></a>Hibaelhárítás

1. A telepítés állapotának ellenőrzéséhez futtassa a következőket:

    `systemctl status ASCIoTAgent.service`

1. Naplózás engedélyezése.
   Ha az ügynök nem indul el, a naplózás bekapcsolásával további információkat érhet el.

   A naplózás bekapcsolása:

   1. Nyissa meg a konfigurációs fájlt szerkesztésre bármely Linux-szerkesztőben:

        `vi /var/ASCIoTAgent/General.config`

   1. Szerkessze a következő értékeket:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       A **logFilePath** érték konfigurálható.

       > [!NOTE]
       > A hibaelhárítás befejezése után javasoljuk a naplózás **kikapcsolását** . A naplózás **bekapcsolásával megnő a** naplófájl mérete és az adatfelhasználás.

   1. Indítsa újra az ügynököt a futtatásával:

       `systemctl restart ASCIoTAgent.service`

   1. A hibával kapcsolatos további információkért tekintse meg a naplófájlt.

       A naplófájl helye: `/var/ASCIoTAgent/IotAgentLog.log`

       Módosítsa a fájl helyének elérési útját a 2. lépésben a **logFilePath** választott név szerint.

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Defender for IoT szolgáltatást [– Áttekintés](overview.md)
- További információ a IoT [architektúra](architecture.md) Defender szolgáltatásáról
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- A [riasztások](concept-security-alerts.md) ismertetése
