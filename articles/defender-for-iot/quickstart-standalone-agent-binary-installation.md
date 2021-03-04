---
title: A Defender telepítése a IoT Micro agenthez
titleSuffix: Azure Defender for IoT
description: Ismerje meg, hogyan telepítheti és hitelesítheti a Defender Micro Agent ügynököt.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/3/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: ccf28c47e2e1438a141e2497da70d32c1832ddb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120436"
---
# <a name="install-defender-for-iot-micro-agent"></a>A Defender telepítése a IoT Micro agenthez 

Ez a cikk azt ismerteti, hogyan telepítheti és hitelesítheti a Defender Micro Agent ügynököt.

## <a name="prerequisites"></a>Előfeltételek

A Defender for IoT modul telepítése előtt létre kell hoznia egy modul-identitást a IoT Hubban. A modul identitásának létrehozásával kapcsolatos további információkért lásd: [Defender IoT Micro Agent modul Twin ](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>A csomag telepítése

Telepítse és konfigurálja a Microsoft Package repositoryt a következő [utasítások](/windows-server/administration/linux-package-repository-for-microsoft-software)alapján. 

A Debian 9 esetében az utasítások nem tartalmazzák a felvenni kívánt tárházat, a következő parancsokkal adhatja hozzá az adattárat: 

```azurecli
curl -sSL https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add - 

sudo apt-get install software-properties-common

sudo apt-add-repository https://packages.microsoft.com/debian/9/multiarch/prod

sudo apt-get update
```

Ha a Defender Micro Agent csomagot Debian-és Ubuntu-alapú Linux-disztribúcióra szeretné telepíteni, használja a következő parancsot:

```azurecli
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>A Micro Agent hitelesítési módszerei 

Az IoT Micro Agent Defender hitelesítéséhez használt két lehetőség a következő: 

- A kapcsolatok karakterlánca. 

- Tanúsítvány.

### <a name="authenticate-using-a-connection-string"></a>Hitelesítés a kapcsolatok karakterláncának használatával

Hitelesítés egy kapcsolódási sztring használatával:

1. A következő parancs beírásával helyezzen el egy nevű fájlt, `connection_string.txt` amely tartalmazza az UTF-8-ban kódolt kapcsolatok karakterláncát `/var/defender_iot_micro_agent` :

    ```azurecli
    sudo bash -c 'echo "<connection string" > /var/defender_iot_micro_agent/connection_string.txt' 
    ```

    A `connection_string.txt` -nek most a következő elérési út helyén kell lennie `/var/defender_iot_micro_agent/connection_string.txt` .

1. Indítsa újra a szolgáltatást a következő parancs használatával:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Hitelesítés tanúsítvány használatával

Hitelesítés tanúsítvány használatával:

1. A tanúsítványok beszerzéséhez kövesse [ezeket az utasításokat](../iot-hub/iot-hub-security-x509-get-started.md).

1. Helyezze a tanúsítvány PEM-kódolású nyilvános részét, és a titkos kulcsot a Defender-ügynök könyvtárába a nevű fájlba, valamint a következő helyre: `certificate_public.pem` `certificate_private.pem` . 

1. Helyezze a megfelelő kapcsolódási karakterláncot a `connection_string.txt` fájlba. a következőhöz hasonlóan kell kinéznie a kapcsolatok karakterláncának: 

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true` 

    Ez a karakterlánc figyelmezteti a Defender ügynököt arra, hogy a tanúsítvány megadását a hitelesítéshez meg kell adni. 

1. Indítsa újra a szolgáltatást a következő parancs használatával:  

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>A telepítés ellenőrzése

A telepítés ellenőrzése:

1. Győződjön meg arról, hogy a Micro Agent megfelelően fut a következő paranccsal:  

    ```azurecli
    systemctl status defender-iot-micro-agent.service
    ```
1. Győződjön meg arról, hogy a szolgáltatás stabil, így biztos lehet benne, `active` hogy a folyamat élettartama megfelelő

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Győződjön meg arról, hogy a szolgáltatás stabil és aktív.":::
 
## <a name="testing-the-system-end-to-end"></a>A rendszerek teljes körű tesztelése 

Az eszközön egy trigger-fájl létrehozásával tesztelheti a rendszer végétől a végéig. Az trigger fájlja azt eredményezi, hogy az ügynök alapvizsgálata észleli a fájlt alapterv megsértése esetén. 

Hozzon létre egy fájlt a fájlrendszerben a következő paranccsal:

```azurecli
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```
Az alapkonfiguráció ellenőrzési hibájának ajánlása a központban, a `CceId` CIS-Debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0,0: 

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Az alapkonfiguráció érvényesítésének sikertelen ajánlása, amely a központban történik." lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Legfeljebb egy óráig engedélyezheti, hogy a javaslat megjelenjen a központban. 

## <a name="micro-agent-versioning"></a>A Micro Agent verziószámozása 

A Defender IoT Micro Agent adott verziójának telepítéséhez futtassa a következő parancsot: 

```azurecli
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Következő lépések

[A Defender Micro Agent felépítése a forráskódból](quickstart-building-the-defender-micro-agent-from-source.md)