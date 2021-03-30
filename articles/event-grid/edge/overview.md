---
title: Event vezérelt architektúrák az Edge-ben – Azure Event Grid on IoT Edge
description: A modulok, peremhálózati eszközök és a felhő közötti továbbítási eseményekhez használja a Azure Event Grid modulként IoT Edge.
ms.topic: overview
ms.date: 07/08/2020
ms.openlocfilehash: 82a68f6ab32d8ad18c3af506c810b01d12cf794d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171516"
---
# <a name="what-is-azure-event-grid-on-azure-iot-edge"></a>Mi a Azure IoT Edge Azure Event Grid?
A Event Grid on IoT Edge a Azure Event Grid hatékonyságát és rugalmasságát teszi lehetővé a peremhálózat számára. Témákat hozhat létre, eseményeket tehet közzé, és előfizethet több célhelyre, függetlenül attól, hogy ugyanazon az eszközön, a többi peremhálózati eszközön vagy a felhőben található modulok.

Ahogy a felhőben, a Event Grid a IoT Edge modulban a nagy léptékű események útválasztását, szűrését és megbízható kézbesítését kezeli. Az események szűrésével biztosíthatja, hogy csak a megfelelő eseményeket küldje el a rendszer a különböző eseménykezelőknek speciális sztring, numerikus és logikai szűrők használatával. Az újrapróbálkozási logika ellenőrzi, hogy az esemény eléri-e a célhelyet, még akkor is, ha a közzétételkor nem érhető el. Lehetővé teszi, hogy a Event Grid IoT Edge hatékony tárolási és továbbítási mechanizmusként használja.

A Event Grid on IoT Edge a CloudEvents 1.0-s és az egyéni esemény-sémákat is támogatja. Emellett ugyanazokat a pub/sub-szemantikaokat támogatja, mint a felhőben Event Grid a könnyű együttműködési képesség érdekében.

Ez a cikk áttekintést nyújt a IoT Edge Azure Event Gridról. A modul peremén való használatának lépésenkénti lépéseiért lásd: [közzététel, helyi előfizetés az eseményekre](pub-sub-events-webhook-local.md). 

![Event Grid a források és kezelők IoT Edge modelljében](../media/edge-overview/functional-model.png)

Ez a rendszerkép néhány módszert mutat be a Event Grid IoT Edgeon való használatára, és nem a támogatott funkciók átfogó listája.

## <a name="when-to-use-event-grid-on-iot-edge"></a>Mikor kell használni a Event Gridt a IoT Edge

A Event Grid on IoT Edge egy könnyen használható és megbízható eseményvezérelt modellt biztosít a peremhálózat és a felhő között.

A Event Grid on IoT Edge egy szimmetrikus futtatókörnyezeti felülettel rendelkezik az Azure Cloud Service-hez, így bármikor használhatja ugyanazokat az eseményeket és API-hívásokat. Legyen szó akár a felhőben lévő pub/sub-ról, akár a két IoT Edge, akár a kettőről, a Event Gridon.

A modulok közötti egyszerű munkafolyamatok elindításához használja a IoT Edge Event Grid. Például hozzon létre egy témakört, és tegye közzé a "Storage blob created" eseményeket a Storage-modulból a témakörbe. Mostantól egy vagy több függvényt vagy egyéni modult is előfizethet az adott témakörre.

Bővítse funkcióit az Edge-eszközök között. Ha blob-modulbeli eseményeket tesz közzé, és az Edge-eszközök által használt számítási teljesítményt szeretné használni, hozzon létre több eszközre szóló előfizetést.

Végül kapcsolódjon a felhőhöz. Ha a blob-modul eseményeit rendszeresen szinkronizálni szeretné a felhőbe, használja a felhőben elérhető nagyobb számítási kapacitást, vagy a feldolgozott adatok elküldésével hozzon létre további felhőalapú szolgáltatás-előfizetéseket.

A Event Grid on IoT Edge rugalmas és megbízható leválasztott esemény-architektúrát biztosít.

## <a name="event-sources"></a>Eseményforrások

A felhőhöz hasonlóan a Event Grid on IoT Edge lehetővé teszi a modulok közötti közvetlen integrációt az Event vezérelt architektúrák létrehozásához. Az események jelenleg a következő helyről küldhetők el Event Grid IoT Edge:

* Azure Blob Storage az IoT Edge-ben
* CloudEvents-források
* Egyéni modulok & tárolók HTTP POST használatával

## <a name="event-handlers"></a>Eseménykezelők

A Event Grid on IoT Edge úgy van felépítve, hogy a kívánt helyre küldjön eseményeket. Jelenleg a következő célhelyek támogatottak:

* Egyéb modulok, például a IoT Hub, a functions és az egyéni modulok
* Egyéb peremhálózati eszközök
* Webhookok
* Azure Event Grid Cloud Service
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák
* Tárolási üzenetsorok

## <a name="supported-environments"></a>Támogatott környezetek
Jelenleg a Windows 64 bites, a Linux 64 bites és az ARM 32-bites környezetek támogatottak.

## <a name="concepts"></a>Alapelvek

A Azure Event Grid öt fogalommal rendelkezik, amelyek lehetővé teszik az első lépéseket:

* **Események** – mi történt.
* **Eseményforrás** – az esemény bekövetkezett helye.
* **Témakörök** – az a végpont, ahol a kiadó eseményeket küld.
* **Esemény-előfizetések** – az események továbbítására szolgáló végpont vagy beépített mechanizmus, esetenként több kezelőhöz. Az előfizetéseket a kezelők is használják a bejövő események intelligens szűrésére.
* **Eseménykezelők** – az eseményre redolgozó alkalmazás vagy szolgáltatás.

## <a name="cost"></a>Költségek

A nyilvános előzetes verzióban a Event Grid on IoT Edge ingyenes.

## <a name="issues"></a>Problémák
A Event Grid IoT Edge használatával kapcsolatos esetleges problémák jelentése a következő helyen: [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues) .

## <a name="next-steps"></a>Következő lépések

* [Közzététel, előfizetés helyi eseményekre](pub-sub-events-webhook-local.md)
* [Közzététel, előfizetés a Felhőbeli eseményekre](pub-sub-events-webhook-cloud.md)
* [Események továbbítása Event Grid felhőbe](forward-events-event-grid-cloud.md)
* [Események továbbítása a IoTHub](forward-events-iothub.md)
* [Reagálás Blob Storage-eseményekre helyben](react-blob-storage-events-locally.md)