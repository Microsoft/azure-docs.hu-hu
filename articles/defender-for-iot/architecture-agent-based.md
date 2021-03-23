---
title: Ügynök-alapú megoldás architektúrája
description: Ismerje meg az Azure Defender IoT-ügynökön alapuló architektúráját és az információáramlást.
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: b04e8fa6225aef9f3c228a44631f117fedffccff
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784883"
---
# <a name="agent-based-solution-for-device-builders"></a>Ügynökalapú megoldás eszközkészítők számára

Ez a cikk a Defender IoT-ügynökön alapuló megoldásának funkcionális rendszerarchitektúráját ismerteti. A IoT készült Azure Defender két lehetőséget kínál a környezet igényeinek, az ügynök nélküli megoldásnak a szervezeteknek és az eszközök építői számára.

## <a name="iot-hub-built-in-security"></a>Beépített biztonsági IoT hub

A IoT Defender alapértelmezés szerint engedélyezve van minden létrehozott új IoT Hub. A IoT Defender valós idejű monitorozást, ajánlásokat és riasztásokat biztosít anélkül, hogy az ügynököket minden eszközön telepíteni kellene, és a naplózott IoT Hub metaadatokon keresztül elemezzük és védelemmel ellátni a helyszíni eszközöket és a IoT hubokat. 

## <a name="defender-for-iot-micro-agent"></a>Defender a IoT Micro agenthez 

A IoT Micro Agent Defender biztonsági védelmet nyújt, és biztosítja az eszköz viselkedését. a nyers biztonsági események összegyűjtése, összesítése és elemzése az eszközökről. A nyers biztonsági események közé tartozhatnak az IP-kapcsolatok, a folyamatok létrehozása, a felhasználói bejelentkezések és egyéb fontos információk. A IoT-ügynökök védelmezője az események összesítését is kezeli, így elkerülhető a hálózati átviteli sebesség. Az ügynökök nagy mértékben testreszabhatók, így adott feladatokhoz is használhatók, például csak a leggyorsabb SLA-ban, vagy a kiterjedt biztonsági információk és környezetek nagyobb szegmensekre történő elküldésével, a magasabb szolgáltatási költségek elkerülésével.

Az eszközök ügynökei és más alkalmazásai az **Azure Send Security Message SDK** használatával biztonsági adatokat küldhetnek az Azure IoT hub-ba. Az IoT hub beolvassa ezeket az információkat, és továbbítja a Defender for IoT szolgáltatásnak.

Ha a Defender for IoT szolgáltatás engedélyezve van, a továbbított adatokon felül a IoT hub az összes belső adatmennyiségét is elküldi a IoT Defender által végzett elemzéshez. Ezek az adategységek a Felhőbeli műveleti naplókat, az eszköz identitásait és a hub konfigurációját tartalmazzák. Mindezen információk segítenek létrehozni a Defendert a IoT Analytics-folyamathoz.

A IoT Analytics-folyamat Defender a Microsoft és a Microsoft partnerei által használt különböző forrásokból származó veszélyforrások elleni intelligenciát is megkapja. A IoT teljes elemzési folyamatának védelmezője a szolgáltatásban végzett összes ügyfél-konfigurációval működik (például az egyéni riasztások és a biztonsági üzenet küldése SDK használata).

Az elemzési folyamat használatával a Defender for IoT az összes adatfolyamot ötvözi a gyakorlatban használható javaslatok és riasztások létrehozásához. A folyamat a biztonsági kutatók és szakértők által létrehozott egyéni szabályokat, valamint a szabványos eszköz viselkedésével és a kockázatelemzéstől való eltérést kereső gépi tanulási modelleket is tartalmaz.

A IoT ajánlásai és értesítései (analitikai folyamat kimenete) az egyes ügyfelek Log Analytics munkaterületére íródnak. A munkaterületen található nyers események, valamint a riasztások és javaslatok segítségével az észlelt gyanús tevékenységek pontos részleteivel részletesen felmerülő vizsgálatokat és lekérdezéseket is lehetővé tesz.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="A Micro Agent architektúrája.":::

## <a name="see-also"></a>Lásd még

[Defender for IoT – gyakori kérdések](resources-frequently-asked-questions.md)

[Rendszer-előfeltételek](quickstart-system-prerequisites.md)
