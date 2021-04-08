---
title: Avere vFXT-fürt finomhangolása – Azure
description: Ismerje meg a vFXT-fürtök egyéni finomhangolását az Azure-hoz készült avere-vFXT, amelyekkel a támogatási képviselők is dolgozhatnak.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 5d9f81c9438cb992f81bd3e6319532d67db75552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88272382"
---
# <a name="cluster-tuning"></a>A fürtök finomhangolása

A legtöbb vFXT-fürt kihasználhatja a testreszabott teljesítmény-beállításokat. Ezek a beállítások segítenek abban, hogy a fürt a legjobban működjön az adott munkafolyamattal, adatkészlettel és eszközökkel.

Ezt a testreszabást a támogatási képviselők segítségével kell elvégezni, mivel ez olyan funkciók konfigurálását is magában foglalhatja, amelyek nem érhetők el a avere Vezérlőpultján.

Ez a szakasz néhány olyan egyéni finomhangolást ismertet, amelyet el lehet végezni.

## <a name="general-optimizations"></a>Általános optimalizálások

Ezeket a módosításokat az adatkészlet tulajdonságai vagy a munkafolyamat stílusa alapján lehet használni.

* Ha a számítási feladat írási nehéz, növelje az írási gyorsítótár méretét a 20%-os alapértéktől.
* Ha az adatkészlet sok kisméretű fájlt foglal magában, növelje a fürt gyorsítótára fájlméretének számát.
* Ha a munka két adattár közötti másolást vagy áthelyezést foglalja magában, módosítsa az adatok áthelyezéséhez használt szálak számát:
  * A sebesség növeléséhez növelheti a használt párhuzamos szálak számát.
  * Ha a háttérbeli tároló kötete túlterhelt, előfordulhat, hogy csökkentenie kell a használt párhuzamos szálak számát.
* Ha a fürt Nfsv4 névleképezője ACL-eket használó központi Filer adatait gyorsítótárazza, engedélyezze a hozzáférési mód gyorsítótárazását az egyes ügyfelek számára a fájlok engedélyezésének egyszerűsítése érdekében.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud NAS vagy Cloud Gateway-optimalizálás

A Felhőbeli NAS-vagy átjáró-forgatókönyvekben a vFXT-fürt NAS stílusú hozzáférést biztosít egy felhőalapú tárolóhoz. A vFXT-fürt és a Felhőbeli tárolók közötti nagyobb adatsebességek kihasználásához a képviselő javaslatot tehet a beállítások módosítására, hogy a rendszer a gyorsítótárból agresszíven küldje el az adatait a tárolási kötetre. Például:

* Növelje meg a fürt és a tároló közötti TCP-kapcsolatok számát

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud burst vagy hibrid WAN-optimalizálás

A felhőalapú és a hibrid tárolási WAN-optimalizálási forgatókönyv esetén a vFXT-fürt integrációt biztosít a felhő és a helyszíni hardveres tároló között. Ezek a változások hasznosak lehetnek:

* Növelje a fürt és a Core Filer között engedélyezett TCP-kapcsolatok számát
* Engedélyezze a távoli Core Filer WAN-optimalizálási beállítását (ez a beállítás használható távoli helyszíni Filer-hez vagy egy másik Azure-régióban lévő Cloud Core Filer-hoz.)
* A TCP-szoftvercsatorna pufferének méretének növeléséhez<sup>*</sup>
* A "mindig továbbítható" beállítás engedélyezése a redundánsan gyorsítótárazott fájlok csökkentése érdekében<sup>*</sup>

<sup>*</sup>Előfordulhat, hogy ezek a módosítások nem vonatkoznak minden rendszerre, a munkaterhelés és a teljesítmény igényeitől függően.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Az Azure-hoz készült avere-vFXT optimalizálásának elősegítése

Ha kapcsolatba szeretné lépni az ezen optimalizálásokkal kapcsolatos támogatási munkatársakkal, használja a [Segítség kérése a rendszerhez](avere-vfxt-open-ticket.md)című témakörben ismertetett eljárást.
