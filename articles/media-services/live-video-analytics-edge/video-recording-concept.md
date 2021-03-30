---
title: Videofelvétel – Azure
description: A CCTV kamerákhoz készült videó-felügyeleti rendszer kontextusában a videó rögzítése arra a folyamatra utal, amely videót készít a kamerákból, és rögzíti a mobil-és böngésző-alkalmazásokban való megtekintésre. A videofelvételek a folyamatos videofelvételek és az eseményvezérelt videofelvételek alapján is kategorizálható.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: c03472f577e67d5b2705a2ebe3f53e85e0849819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84260945"
---
# <a name="video-recording"></a>Videófelvétel

A CCTV kamerákhoz készült videó-felügyeleti rendszer kontextusában a videó rögzítése arra a folyamatra utal, amely videót készít a kamerákból, és rögzíti a mobil-és böngésző-alkalmazásokban való megtekintésre. A videofelvételek a folyamatos videofelvételek és az eseményvezérelt videofelvételek alapján is kategorizálható. 

## <a name="continuous-video-recording"></a>Folyamatos videófelvétel  

A folyamatos videofelvétel (CVR) arra a folyamatra utal, amely folyamatosan rögzíti a videó forrásaként rögzített összes videót. A CVR biztosítja, hogy az összes videó elérhető legyen (a [rögzítési házirend](#recording-policy)által diktálva) egy későbbi időpontban történő elemzéshez és/vagy naplózáshoz.

## <a name="event-based-video-recording"></a>Eseményalapú videófelvétel  

Az Event-based video Recording (EVR) az esemény által aktivált videó rögzítési folyamatára utal. Előfordulhat, hogy a szóban forgó esemény a videó-jel feldolgozása miatt vagy egy független forrásból (például egy ajtó-érzékelőből) származik. Az Event-based video Recording (EVR) szolgáltatás megtakarítást eredményezhet, azonban további összetevőket is igényel, amelyek előidézik az eseményeket, és elindítják a videofelvételt (egy előre meghatározott házirend alapján). Más szóval a EVR további döntéseket kell hoznia a videó rögzítését kiváltó eseményekről, valamint a videofelvételekhez kapcsolódó szabályzatról (más néven rögzítési szabályzat). Egy házirend például a következőhöz hasonló lehet: a videó rögzítése 2 percig az esemény időpontja előtt 30 másodperctől kezdődően. Előfordulhat, hogy a szóban forgó események a kamera általi feldolgozás miatt is származhatnak. Például több kamera támogatja a mozgásészlelési jel esemény létrehozását, ha a kamera nézőpontjában egy előre konfigurált zónán belül mozgás észlelhető. Az események a videónak egy másik eszközön történő feldolgozásával is létrehozhatók, amely rögzíti a videót a kamerából, és az egyszerű képfeldolgozási technikák vagy kifinomult gépi tanulási modellek használatával elemzi azt. 

## <a name="choosing-recording-modes"></a>Felvételi módok kiválasztása  

A CVR vagy a EVR használatának megválasztása az üzleti céloktól függ. A IoT Edge élő videó-elemzések platform-képességeket biztosítanak mind a CVR, mind a EVR. További információt a [CVR](continuous-video-recording-concept.md) és a [EVR](event-based-video-recording-concept.md) forgatókönyv cikkeiben talál.

## <a name="recording-policy"></a>Házirend rögzítése  

A rögzítési házirend azokra a szabályzatokra vonatkozik, amelyek megírják a rögzítés kezdő/leállítási idejét (EVR esetén), a rögzítés időtartamát és a rögzítés törlését. A szabályzatok rögzítése lehetővé teszi, hogy az üzleti követelmények alapján kiegyensúlyozza a tárolási költségeket. A CVR és a EVR közötti adatrögzítési házirendek eltérőek. A CVR esetében a rögzítési házirend határozza meg, hogy hány napon belül kell tárolni a videót (például az elmúlt 7 napban). A EVR esetében a rögzítési házirend határozza meg, hogy mikor kezdődjön és fejeződik be a rögzítés, a videó időtartamával együtt. További információt a [CVR](continuous-video-recording-concept.md) és a [EVR](event-based-video-recording-concept.md) forgatókönyv cikkeiben talál.

## <a name="next-steps"></a>Következő lépések

* [Mozgás észlelése, videóklipek készítése az Azure Media Servicesbe](detect-motion-record-video-clips-media-services-quickstart.md)
* [Mozgás észlelése, videoklipek rögzítése az Edge-eszközökön](detect-motion-record-video-clips-edge-devices-quickstart.md)

