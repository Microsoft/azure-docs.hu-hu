---
title: A Linux C Agent telepítése & üzembe helyezése
description: Ismerje meg, hogyan telepítheti és telepítheti a Defender for IoT C-alapú biztonsági ügynököt Linux rendszeren
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: f7f6b28070600ae53242181934ea903a486c25b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103494563"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>A Defender üzembe helyezése a IoT C-alapú biztonsági ügynökének Linuxra

Ez az útmutató ismerteti, hogyan telepítheti és telepítheti a Defender for IoT C-alapú biztonsági ügynökét Linux rendszeren.

- Telepítés
- Az üzembe helyezés ellenőrzése
- Ügynök eltávolítása
- Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Más platformokon és ügynöki Ízeknél tekintse meg [a megfelelő biztonsági ügynök kiválasztása](how-to-deploy-agent.md)című témakört.

1. A biztonsági ügynök telepítéséhez helyi rendszergazdai jogosultságok szükségesek azon a gépen, amelyet telepíteni kíván (sudo).

1. [Hozzon létre egy Defender-IoT-Micro-Agent](quickstart-create-security-twin.md) eszközt az eszközhöz.

## <a name="installation"></a>Telepítés

A biztonsági ügynök telepítéséhez és telepítéséhez használja a következő munkafolyamatot:

1. Töltse le a legújabb verziót a gépére a [githubról](https://aka.ms/iot-security-github-c).

1. Bontsa ki a csomag tartalmát, és navigáljon a _/src/Installation_ mappára.

1. A következő parancs futtatásával adja hozzá a futó engedélyeket a **InstallSecurityAgent parancsfájlhoz** :

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ezután futtassa a következőt:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   A hitelesítési paraméterekkel kapcsolatos további információkért tekintse [meg a hitelesítés konfigurálása](concept-security-agent-authentication-methods.md) című témakört.

Ez a szkript a következő függvényt hajtja végre:

1. Telepíti az előfeltételeket.

1. Szolgáltatásbeli felhasználó (interaktív bejelentkezéssel letiltva) hozzáadásával.

1. Az ügynököt **démonként** telepíti, feltételezi, hogy az eszköz a szolgáltatás felügyeletéhez használt **rendszert** használja.

1. Konfigurálja az ügynököt a megadott hitelesítési paraméterekkel.

További segítségért futtassa a parancsfájlt a – Súgó paraméterrel:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Ügynök eltávolítása

Az ügynök eltávolításához futtassa a szkriptet a – uninstall paraméterrel:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Hibaelhárítás

A telepítés állapotának ellenőrzéséhez futtassa a következőket:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Defender for IoT szolgáltatást [– Áttekintés](overview.md)
- További információ a IoT [architektúra](architecture.md) Defender szolgáltatásáról
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- [Biztonsági riasztások](concept-security-alerts.md) ismertetése
