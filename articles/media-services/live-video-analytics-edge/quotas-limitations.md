---
title: Élő videó-elemzés IoT Edge kvóták és korlátozások – Azure
description: Ez a cikk az élő videók elemzését ismerteti IoT Edge kvóták és korlátozások alapján.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 68c7b91bb1051348b5a8e52f841d443894f0a632
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400524"
---
# <a name="quotas-and-limitations"></a>Kvóták és korlátozások

Ez a cikk a IoT Edge modul élő videós elemzésének kvótáit és korlátozásait sorolja fel.

## <a name="maximum-period-of-disconnected-use"></a>Leválasztott használat maximális időtartama

Az Edge-modul képes fenntartani az internetkapcsolat ideiglenes elvesztését. Ha a modul több mint 36 órával továbbra is megszakadt, akkor a rendszer inaktiválja az összes futó gráf-példányt. Minden további közvetlen metódus hívása le lesz tiltva.

Az Edge-modul operatív állapotba való folytatásához vissza kell állítania az internetkapcsolatot, hogy a modul sikeresen kommunikálni tudjon az Azure Media Service-fiókkal.

## <a name="maximum-number-of-graph-instances"></a>Graph-példányok maximális száma

A (GraphInstanceSet-on keresztül létrehozott) modulokban legfeljebb 1000 gráf-példány támogatott.

## <a name="maximum-number-of-graph-topologies"></a>Gráf-topológiák maximális száma

Legfeljebb 50 gráf-topológia támogatott a (z) GraphTopologySet-on keresztül létrehozott modulokban.

## <a name="limitations-on-graph-topologies-at-preview"></a>A Graph-topológiák korlátozásai az előzetes verzióban

Az előzetes kiadásban a különböző csomópontokra vonatkozó korlátozások is csatlakoztathatók a Media Graph-topológiában.

* RTSP-forrás
   * Graph-topológia esetében csak egy RTSP-forrás engedélyezett.
* Mozgásészlelési processzor
   * Közvetlenül az RTSP-forrástól kell, hogy legyen.
   * HTTP-vagy gRPC-bővítményi processzor esetén nem használható.
* Signal Gate processzor
   * Közvetlenül az RTSP-forrástól kell, hogy legyen.
* Eszköz fogadója 
   * Közvetlenül az RTSP-forrás vagy a Signal Gate-processzor között kell lennie.
* File mosogató
   * Közvetlenül a Signal Gate-processzorról kell, hogy legyen.
   * Nem lehet közvetlenül a HTTP-vagy gRPC-bővítmény processzora vagy a mozgásérzékelő processzora.
* IoT Hub fogadó
   * Nem lehet közvetlenül a IoT Hub forrásának vége.

## <a name="limitations-on-media-service-operations-at-preview"></a>A Media Service-műveletekre vonatkozó korlátozások előzetes verzióban

Az előzetes kiadás időpontjában a IoT Edge Live Video Analytics nem támogatja a következőket:

* A Media Service-fiók egyik előfizetésből a másikba való migrálása megszakítás nélkül.
* Több Storage-fiók is használható a Media Service-fiókkal.
* Az egyszerű szolgáltatásnév adatait dinamikusan, újraindítás nélkül módosíthatja a modul kívánt tulajdonságaiban.

Csak az RTSP protokollt támogató IP-kamerákat lehet használni. Az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőségi termékek](https://www.onvif.org/conformant-products) lapon találja. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.

Emellett ezeket a kamerákat a H. 264 videó és az AAC hang használatára kell beállítani. Más kodekek jelenleg nem támogatottak. 

## <a name="next-steps"></a>Következő lépések

[Áttekintés](overview.md)
