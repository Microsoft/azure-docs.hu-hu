---
title: Biztonsági ügynök helyi konfigurációja (C)
description: Ismerje meg, hogyan használható a Defender a C-hez készült Agent helyi konfigurációkhoz.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 161bc008076de2bba62cd65c6c9bb106f648aa47
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493237"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>A LocalConfiguration.json fájl ismertetése – C-ügynök

A IoT biztonsági ügynök Defender a helyi konfigurációs fájl konfigurációit használja.
A biztonsági ügynök egyszer beolvassa a konfigurációt az ügynök indításakor.
A helyi konfigurációs fájlban található konfiguráció hitelesítési konfigurációt és más ügynökkel kapcsolatos konfigurációkat tartalmaz.
A fájl a "kulcs-érték" párokban található konfigurációkat JSON-jelöléssel tartalmazza, a konfigurációk pedig az ügynök telepítésekor kerülnek feltöltésre.

Alapértelmezés szerint a fájl a következő helyen található:/var/ASCIoTAgent/LocalConfiguration.json

A konfigurációs fájl módosításai az ügynök újraindításakor lépnek életbe.

## <a name="security-agent-configurations-for-c"></a>A C biztonsági ügynök konfigurációi

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| Ügynökazonosító | GUID | Az ügynök egyedi azonosítója |
| TriggerdEventsInterval | ISO8601 karakterlánc | Az aktivált események gyűjtésének ütemező időköze |
| ConnectionTimeout | ISO8601 karakterlánc | Túllépte az időkorlátot a IoThub való kapcsolódás előtt. |
| Hitelesítés | JsonObject | Hitelesítési konfiguráció. Ez az objektum tartalmazza az IoTHub-hitelesítéshez szükséges összes információt |
| Identitás | "DPS", "SecurityModule", "eszköz" | Hitelesítési identitás – DPS if hitelesítés a DPS-n keresztül történik, SecurityModule, ha a hitelesítés a Defender-IoT-Micro-agentcredentials vagy az eszköz használatával történik, ha hitelesítés történik az eszköz hitelesítő adataival |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | a hitelesítő felhasználói titok – válassza a SasToken lehetőséget, ha a titok használata szimmetrikus kulcs, válassza az önaláírt tanúsítvány lehetőséget, ha a titok egy önaláírt tanúsítvány  |
| FilePath | Fájl elérési útja (karakterlánc) | A hitelesítési titkot tartalmazó fájl elérési útja |
| HostName | sztring | Az Azure IoT hub állomásneve. általában <My-hub>. azure-devices.net |
| DeviceId | sztring | Az eszköz azonosítója (az Azure IoT Hubban regisztrálva) |
| DPS | JsonObject | DPS-hez kapcsolódó konfigurációk |
| IDScope | sztring | A DPS azonosító hatóköre |
| Regisztrációban | sztring  | DPS-eszköz regisztrációs azonosítója |
| Naplózás | JsonObject | Ügynök-naplózó kapcsolódó konfigurációk |
| SystemLoggerMinimumSeverity | 0 <= szám <= 4 | a/var/log/syslog (0 a legalacsonyabb súlyosságú) üzeneteket a rendszer naplózza. |
| DiagnosticEventMinimumSeverity | 0 <= szám <= 4 | a rendszer a súlyossági szinttel egyenlő vagy annál nagyobb naplózási üzeneteket küld diagnosztikai eseményként (a 0 a legalacsonyabb súlyosságú). |

## <a name="security-agent-configurations-code-example"></a>Biztonsági ügynök konfigurációjának kódja – példa

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

- Olvassa el a Defender for IoT szolgáltatást [– Áttekintés](overview.md)
- További információ a IoT [architektúra](architecture.md) Defender szolgáltatásáról
- A Defender engedélyezése a IoT [szolgáltatáshoz](quickstart-onboard-iot-hub.md)
- Olvassa el a Defender for IoT szolgáltatást [– Gyakori kérdések](resources-frequently-asked-questions.md)
- Ismerje meg, hogyan érheti el a [nyers biztonsági információkat](how-to-security-data-access.md)
- [Javaslatok](concept-recommendations.md) ismertetése
- Biztonsági [riasztások](concept-security-alerts.md) ismertetése
