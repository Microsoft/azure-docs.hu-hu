---
title: Defender-IoT-Micro-Agent az Azure RTOS – áttekintés
description: További információ a Defender-IoT-Micro-Agent Azure RTOS támogatásáról és megvalósításáról az Azure Defender for IoT részeként.
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 8f162cc7e284abc9f1fdc08a10e62369855017c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785903"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>Áttekintés: Defender for IoT Defender – IoT-Micro-Agent Azure RTOS (előzetes verzió)

Az Azure Defender for IoT Micro modul átfogó biztonsági megoldást nyújt az Azure RTOS-t használó eszközökhöz. A valós idejű operációs rendszer (RTOS) eszközein a gyakori fenyegetések és a lehetséges kártékony tevékenységek lefedettségét biztosítja. Az Azure RTOS mostantól a beépített Azure IoT Defender-IoT-Micro-Agent szolgáltatással rendelkezik.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="A Defender vizualizációja az Azure RTOS IoT.":::


Az Azure RTOS Micro-modulja a következő funkciókat kínálja:

- Kártevő hálózati tevékenységek észlelése
- Egyéni riasztáson alapuló eszköz viselkedésének viszonyítási
- Továbbfejlesztett eszköz biztonsági higiénia

## <a name="detect-malicious-network-activities"></a>Kártékony hálózati tevékenységek észlelése

A rendszer figyeli az egyes eszközök bejövő és kimenő hálózati tevékenységeit. A támogatott protokollok a következők: TCP, UDP és ICMP az IPv4-ben és az IPv6-on. A IoT Defender a Microsoft Threat Intelligence-hírcsatornán vizsgálja meg az egyes hálózati tevékenységeket. A hírcsatorna valós időben frissül, és világszerte több millió egyedi veszélyforrást észlelt.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Az eszköz viselkedésének viszonyítási egyéni riasztások alapján

A viszonyítási lehetővé teszi az eszközök fürtözését a biztonsági csoportokba, és meghatározza az egyes csoportok várható viselkedését. Mivel a IoT-eszközök általában jól meghatározott és korlátozott helyzetekben működnek, egyszerűen létrehozhat egy alapkonfigurációt, amely meghatározza a várt viselkedést paraméterek használatával. Az alaptervtől való eltérés riasztást indít el.

## <a name="improve-your-device-security-hygiene"></a>Az eszköz biztonsági higiéniájának javítása

A IoT által biztosított ajánlott infrastruktúra-Defender segítségével ismereteket és elemzéseket kaphat a környezetében felmerülő problémákról, amelyek hatással vannak az eszközök biztonsági helyzetére, és károsíthatják azokat. A gyenge IoT biztonsági helyzetek lehetővé tehetik a lehetséges támadások sikerességét, ha változatlan marad. A biztonságot mindig a leggyengébb kapcsolat méri a szervezeten belül.

## <a name="get-started-protecting-azure-rtos-devices"></a>Az Azure RTOS-eszközök védelmének megkezdése

Az Azure RTOS készült Defender-IoT-Micro-Agent ingyenes letöltést biztosít az eszközei számára. A IoT Cloud Service Defender egy 30 napos próbaidőszakot biztosít az Azure-előfizetések esetében. Első lépésként töltse le a [Defender-IoT-Micro-Agent Azure RTOS](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md). 

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta az Azure RTOS készült Defender-IoT-Micro-agentet. Ha többet szeretne megtudni a Defender-IoT-Micro-agentről és az első lépésekről, tekintse meg a következő cikkeket:

- [Azure RTOS IoT Defender – IoT-Micro-Agent fogalmak](concept-rtos-security-module.md)
- [Gyors útmutató: Azure RTOS IoT Defender – IoT-Micro-Agent](quickstart-azure-rtos-security-module.md)
