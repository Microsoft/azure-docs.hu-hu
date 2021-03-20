---
title: Peering Service – gyakori kérdések
titleSuffix: Azure
description: Peering Service – gyakori kérdések
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75775471"
---
# <a name="peering-service---faqs"></a>Peering Service – gyakori kérdések

Az alábbi információkat általános kérdésekre is áttekintheti.

**A szolgáltatók a meglévő közvetlen együttműködéssel támogatják a Microsoftot a partneri szolgáltatás támogatásához?**

Igen, a szolgáltatók kihasználhatják a meglévő PNI a társas szolgáltatás támogatásához. A társ-szolgáltatási PNI a rendelkezésre állást igényli a HA. Ha a meglévő PNI már rendelkezik a sokféleséggel, akkor nincs szükség új infrastruktúrára. Ha a meglévő PNI sokféleségre van szükség, akkor kibővíthető lehet.

**Használhatnak-e a szolgáltatók új közvetlen társ-összevonást a Microsofttal a partneri szolgáltatás támogatásához?**

Igen, ez is lehetséges. A Microsoft a szolgáltatóval együttműködve új közvetlen társat hoz létre a partneri szolgáltatás támogatásához.  

**Miért van szükség a közvetlen társításra a társ-szolgáltatás támogatásához?**

Az egyik elsődleges illesztőprogram a partneri szolgáltatás mögött az, hogy kapcsolatot biztosít a Microsoft online szolgáltatások egy jól csatlakoztatott SP-n keresztül. A PNI mindig a Gbps-tartományba tartoznak, ezért alapvető építőelemek a szolgáltató és a Microsoft közötti nagy átviteli sebességű kapcsolatokhoz.

**Mik azok a sokszínűségi követelmények, amelyekkel a közszolgálati szolgáltatás támogatja a közvetlen munkatársait?**

A PNI támogatnia kell A helyi redundanciát és a Geo-redundanciát. A helyi redundancia két különböző elérési úttal van definiálva egy adott partneri helyen. A Geo-redundancia megköveteli, hogy a szállító további kapcsolódást biztosítson egy másik Microsoft Edge-helyen abban az esetben, ha az elsődleges hely meghibásodik. A rövid meghibásodás időtartama esetén a szállító a biztonsági mentési helyen keresztül irányíthatja a forgalmat.

**A szolgáltató már kínál SLA-t és nagyvállalati szintű internetet, miben különbözik az ajánlat?**

Egyes szolgáltatók SLA-t és nagyvállalati szintű internetet kínálnak a hálózatuk részéről. A szolgáltatásban a Microsoft a hálózat Microsoft-részén kínál SLA-ajánlati forgalmat. A társ-szolgáltatási ügyfél kiválasztásával teljes körű SLA-t kap. Az internetszolgáltató a helyükről a Microsoft Edge-re az ISP-hálózaton lévő SLA-t is lefedi. A Microsoft Edge és a végfelhasználók számára a Microsoft globális hálózatában található SLA-t már a Microsoft tárgyalja.

**Ha egy szolgáltató már a Microsofttal társait használja a PNI-ben, akkor milyen módosításokra van szükség a társ-kezelő szolgáltatás támogatásához?**

* A szoftver megváltoztatja a társ-szolgáltatási felhasználót és a hozzá tartozó forgalmat. Előfordulhat, hogy az útválasztási házirend módosításait a rendszer a legközelebbi Microsoft Edge-en keresztül a partneri szolgáltatás kapcsolatain keresztül cseréli át a felhasználó forgalmát.
* Győződjön meg arról, hogy a kapcsolat helyi redundancia és geo-redundancia van.
