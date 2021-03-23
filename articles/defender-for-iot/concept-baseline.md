---
title: Alapterv és egyéni ellenőrzések
description: Ismerje meg az Azure Defender IoT alapkonfigurációjának koncepcióját.
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b8b9d62918e40262da6b3df48d0fece842e050f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779358"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure Defender a IoT alapkonfigurációhoz és egyéni ellenőrzésekhez

Ez a cikk a Defender for IoT alapkonfigurációját ismerteti, és összefoglalja az alapkonfiguráció egyéni ellenőrzésekhez kapcsolódó összes tulajdonságot.

## <a name="baseline"></a>Alapkonfiguráció

Az alapkonfiguráció minden eszköz esetében szabványos viselkedést hoz létre, és megkönnyíti a szokatlan viselkedést vagy a várt normáktól való eltérést.

## <a name="baseline-custom-checks"></a>Alapkonfiguráció egyéni ellenőrzése

Az alapkonfiguráció egyéni ellenőrzése az eszközhöz tartozó **Identity Twin** használatával egyéni ellenőrzési listát hoz létre az egyes eszközök alapértékei számára.

## <a name="setting-baseline-properties"></a>Alapterv tulajdonságainak beállítása

1. A IoT Hub keresse meg és válassza ki a módosítani kívánt eszközt.

1. Kattintson az eszközre, majd kattintson a **azureiotsecurity** modulra.

1. Kattintson a **modul Identity Twin** elemre.

1. Töltse fel az alapkonfiguráció **Egyéni ellenőrzési** fájlját az eszközre.

1. Adja hozzá az alapkonfiguráció tulajdonságait a Defender-IoT-Micro-agenthez, majd kattintson a **Mentés** gombra.

### <a name="baseline-custom-check-file-example"></a>Példa alapterv egyéni ellenőrzési fájlra

Az alapkonfiguráció egyéni ellenőrzésének konfigurálása:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Alapterv egyéni ellenőrzési tulajdonságai

| Name| Állapot | Érvényes értékek| Alapértelmezett értékek| Leírás |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Kötelező: igaz |Érvényes értékek: **Boolean** |Alapértelmezett érték: **false** |A magas prioritású üzenetek küldése előtti maximális időtartam.|
|baselineCustomChecksFilePath |Kötelező: igaz|Érvényes értékek: **karakterlánc**, **Null** |Alapértelmezett érték: **Null** |Az eredeti XML-konfiguráció teljes elérési útja|
|baselineCustomChecksFileHash |Kötelező: igaz|Érvényes értékek: **karakterlánc**, **Null** |Alapértelmezett érték: **Null** |`sha256sum` az XML-konfigurációs fájlból. További információkért használja az [sha256sum-referenciát](https://linux.die.net/man/1/sha256sum) . |

További alapkonfigurációk áttekintéséhez tekintse meg az [Egyéni Alapterv – 1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) . és az [Egyéni alapterv – 2. példát](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Következő lépések

- A [nyers biztonsági adataihoz](how-to-security-data-access.md) való hozzáférés
- [Eszköz vizsgálata](how-to-investigate-device.md)
- A [biztonsági javaslatok](concept-recommendations.md) megismerése és megismerése
- A [biztonsági riasztások](concept-security-alerts.md) megismerése és megismerése
