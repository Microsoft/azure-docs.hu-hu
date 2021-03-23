---
title: Biztonsági ügynök hitelesítése (előzetes verzió)
description: A Micro Agent hitelesítés két lehetséges módszerrel hajtható végre.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779239"
---
# <a name="micro-agent-authentication-methods-preview"></a>A Micro Agent hitelesítési módszerei (előzetes verzió)

Két lehetőség van a IoT Micro Agent Defender általi hitelesítésre: 

- Kapcsolati sztring 

- Tanúsítvány 

## <a name="authentication-using-a-connection-string"></a>Hitelesítés a kapcsolatok karakterláncának használatával 

A kapcsolódási karakterlánc használatához hozzá kell adnia egy fájlt, amely az UTF-8 kódolású kapcsolódási karakterláncot használja a Defender-ügynök címtárában egy nevű fájlban `connection_string.txt` . Példa:

```azurecli
echo “<connection string>” > connection_string.txt 
```

Ha ezt megtette, ezt a parancsot használva újra kell indítania a szolgáltatást.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Hitelesítés tanúsítvány használatával 


Hitelesítés végrehajtása tanúsítvány használatával: 

1. Helyezze a tanúsítvány PEM-kódolású nyilvános részét a Defender-ügynök könyvtárába a nevű fájlban `certificate_public.pem` .
1. Helyezze a PEM-kódolású titkos kulcsot a Defender-ügynök könyvtárába a nevű fájlban `certificate_private.pem` .
1. Helyezze el a megfelelő kapcsolattípus-karakterláncot egy nevű fájlba `connection_string.txt` . Példa:

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Ez a művelet azt jelzi, hogy a Defender-ügynöknek a hitelesítéshez meg kell adni a tanúsítványt. 

1. Indítsa újra a szolgáltatást a következő kód használatával: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Győződjön meg arról, hogy a Micro Agent megfelelően fut 

1. Futtassa az alábbi parancsot: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Ellenőrizze, hogy a szolgáltatás stabil-e, és hogy az **aktív** -e, és hogy a folyamat ideje megfelelő-e: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Gondoskodjon arról, hogy a szolgáltatás stabil legyen, és ellenőrizze, hogy aktív-e.":::

## <a name="next-steps"></a>Következő lépések

Győződjön meg róla, hogy a [biztonsági helyzet – CIS-teljesítményteszt](concept-security-posture.md).
