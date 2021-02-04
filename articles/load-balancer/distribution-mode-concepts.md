---
title: Azure Load Balancer terjesztési módok
description: Ismerkedjen meg a Azure Load Balancer különböző terjesztési módjaival.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551316"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer terjesztési módok

A Azure Load Balancer két terjesztési módot támogat a terheléselosztásos alkalmazásokhoz való útválasztási kapcsolatok esetén:

* Kivonatoló alapú
* Forrás-IP-affinitás

## <a name="hash-based"></a>Kivonatoló alapú

Az Azure Load Balancer alapértelmezett eloszlási módja az ötrekordos kivonat. 

A rekord a következőkből áll:
* **Forrás IP-címe**
* **Forrásport**
* **Cél IP-címe**
* **Célport**
* **Protokoll típusa**

A kivonat használatával képezhető le a forgalom az elérhető kiszolgálókra. Az algoritmus csak a szállítási munkameneten belül nyújt stickiet. Az azonos munkamenetben lévő csomagokat ugyanahhoz az adatközponti IP-címhez irányítja a rendszer, amely a terheléselosztási végpont mögött található. Amikor az ügyfél egy új munkamenetet indít el ugyanabból a forrás IP-címről, a forrásport megváltozik, és a forgalom egy másik adatközpont-végpontra lép.

![Öt rekordos kivonatoló terjesztési mód](./media/distribution-mode-concepts/load-balancer-distribution.png)

A kivonat-alapú mód egyetlen konfigurációs típussal rendelkezik:

* **Nincs (kivonat-alapú)** – azt határozza meg, hogy egy adott ügyfél egymást követő kérelmeit bármely virtuális gép kezelheti.

## <a name="source-ip-affinity"></a>Forrás-IP-affinitás

Ezt az elosztási módot munkamenet-affinitásnak vagy ügyfél-IP-cím affinitásnak is nevezik. A mód egy kétrekordos (forrás IP-cím és cél IP-cím) vagy három rekordos (forrás IP-cím, cél IP-cím és protokolltípus) kivonatot használ a forgalom az elérhető kiszolgálókra történő leképezéséhez. 

A forrás IP-affinitás használatával az azonos ügyfélszámítógépről indított kapcsolatok ugyanarra az adatközpont-végpontra kerülnek.

Az alábbi ábrán két rekordos konfiguráció látható. Figyelje meg, hogyan futnak a két rekord a terheléselosztó használatával az 1. virtuális gépen (VM1). A VM1 ezt követően biztonsági mentést készít a VM2 és a VM3.

![Kétrekordos munkamenet-affinitás terjesztési módja](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

A forrás IP-affinitás mód két konfigurációs típussal rendelkezik:

* **Ügyfél IP-címe (forrás IP-affinitás 2-rekord)** – azt határozza meg, hogy ugyanazon ügyfél IP-címéről érkező egymást követő kérelmeket ugyanaz a virtuális gép fogja kezelni.
* **Ügyfél IP-címe és protokollja (forrás IP-affinitás 3 – rekord)** – azt határozza meg, hogy az azonos ügyfél IP-címe és a protokoll kombinációja egymást követő kérelmeket ugyanazzal a virtuális géppel fogja kezelni.

## <a name="use-cases"></a>Használati esetek

Forrás IP-affinitás az ügyfél IP-címével és protokollal (forrás IP-kapcsolat 3 – rekord) a Azure Load Balancer és Távoli asztali átjáró (RD-átjáró) közötti inkompatibilitást oldja meg. 

Egy másik használati eset a média feltöltése. Az adatfeltöltés UDP-n keresztül történik, de a vezérlő síkja a TCP protokollon keresztül érhető el:

* Az ügyfél TCP-munkamenetet indít el a terheléselosztási nyilvános címről, és egy adott DIP-re irányítja. A csatorna aktív marad a kapcsolatok állapotának figyeléséhez.
* Az azonos ügyfélszámítógépről származó új UDP-munkamenet ugyanarra a terheléselosztásos nyilvános végpontra van elindítva. A kapcsolódás ugyanahhoz a DIP-végponthoz van irányítva, mint a korábbi TCP-kapcsolatok. A médiafájlok feltöltése nagy átviteli sebességgel végezhető, miközben a vezérlési csatornát a TCP protokollon keresztül is megtarthatja.

> [!NOTE]
> Ha egy elosztott terhelésű készlet egy virtuális gép eltávolításával vagy hozzáadásával változik, a rendszer újraszámítja az ügyfelek kéréseinek eloszlását. A meglévő ügyfelektől érkező új kapcsolatok nem függhet ugyanazon a kiszolgálón. Emellett a forrás IP-affinitás elosztási módja a forgalom egyenlőtlen elosztását is okozhatja. A proxy mögött futó ügyfelek egyetlen egyedi ügyfélalkalmazásként jelenhetnek meg.


## <a name="next-steps"></a>Következő lépések

A Azure Load Balancer elosztási módjának konfigurálásával kapcsolatos további információkért lásd: [Azure Load Balancer elosztási módjának konfigurálása](load-balancer-distribution-mode.md).

