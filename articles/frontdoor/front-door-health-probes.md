---
title: Azure bejárati ajtó – háttér állapotának monitorozása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure bejárati ajtaja hogyan figyeli a háttérrendszer állapotát
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449241"
---
# <a name="health-probes"></a>Állapotminták

Az egyes háttérrendszer-környezetek állapotának és közelségének meghatározásához az egyes bejárati környezetek időnként egy szintetikus HTTP/HTTPS-kérést küldenek minden konfigurált háttérrendszer számára. A bejárati ajtó ezeket a válaszokat a mintavétel alapján határozza meg az ügyfelek kéréseinek továbbításához szükséges legjobb háttér-erőforrások meghatározásához. 

> [!WARNING]
> Mivel a bejárati ajtó számos peremhálózati környezettel rendelkezik globálisan, a háttérrendszer állapotának mintavételi mennyisége meglehetősen magas – 25 kérés percenként, akár 1200 kérelem percenként, a beállított állapottól függően. Az alapértelmezett, 30 másodperces mintavételi gyakorisággal a háttérbeli mintavételi mennyiségnek percenként 200 kérésnek kell lennie.

## <a name="supported-protocols"></a>Támogatott protokollok

A bejárati ajtó támogatja a mintavételek HTTP-vagy HTTPS-protokollon keresztüli küldését. A rendszer a mintavételeket ugyanazokon az ügyfélkérelmek átirányítására konfigurált, nem felülbírálható TCP-portokon keresztül küldi el.

## <a name="supported-http-methods-for-health-probes"></a>Az állapot-mintavételek támogatott HTTP-módszerei

A bejárati ajtó a következő HTTP-metódusokat támogatja az állapot-mintavételek küldéséhez:

1. **Beolvasás:** A GET metódus azt jelenti, hogy a kérelem-URI azonosítja az összes információt (egy entitás formájában).
2. **Head:** A HEAD metódus megegyeznek azzal a kivétellel, hogy a kiszolgálón nem lehet visszaadni a válaszban szereplő üzenet törzsét. Az új előtérben lévő profilok esetében alapértelmezés szerint a mintavételi módszer a következő: HEAD.

> [!NOTE]
> A háttérbeli terhelések és a költségek csökkentése érdekében a bejárati ajtó javasolja az állapot-mintavételek HEAD kérelmének használatát.

## <a name="health-probe-responses"></a>Állapot-mintavételi válaszok

| Válaszok  | Leírás | 
| ------------- | ------------- |
| Állapot meghatározása  |  A 200 OK állapotkód azt jelzi, hogy a háttér állapota Kifogástalan. Minden más hibát jelez. Ha bármilyen okból (a hálózati meghibásodást is beleértve) nem érkezik érvényes HTTP-válasz a mintavételhez, a mintavétel meghibásodásnak számít.|
| Mérési késés  | A késés az az idő, amelyet a rendszer közvetlenül a mintavételi kérelem elküldése előtt mért, amikor a válasz utolsó bájtját megkaptuk. Minden kérelemhez új TCP-kapcsolatot használunk, így ez a mérés nem torzítja a meglévő, meleg kapcsolatokkal rendelkező háttérrendszer felé.  |

## <a name="how-front-door-determines-backend-health"></a>A háttérrendszer állapotának meghatározása

Az Azure bejárati ajtó ugyanazt a három lépésből álló folyamatot használja az összes algoritmusban az állapot meghatározásához.

1. A letiltott háttérrendszer kizárása.

2. Állapot-mintavételi hibákkal rendelkező hátterek kizárása:
    * Ezt a kijelölést az utolsó _n_ Health mintavételi válasz alapján kell megtekinteni. Ha legalább _x_ állapota Kifogástalan, a rendszer kifogástalannak tekinti a hátteret.

    * _n_ konfigurálva van a SampleSize tulajdonságának módosításával a terheléselosztási beállításokban.

    * az _x_ konfigurálva van a SuccessfulSamplesRequired tulajdonság módosításával a terheléselosztási beállításokban.

3. A háttér-készletben lévő, kifogástalan teljesítményű háttérrendszer esetében a bejárati ajtó emellett a mértékeket is méri, és fenntartja az egyes háttérrendszer késését (az oda-és visszaút idejét).


## <a name="complete-health-probe-failure"></a>Állapot-mintavételi hiba befejezése

Ha az állapot-mintavétel egy háttér-készlet minden egyes munkafelületén meghiúsul, akkor a bevezető ajtó az összes háttérrendszer állapotát figyelembe veszi, és egy ciklikus multiplexelés eloszlásban irányítja át a forgalmat.

Ha bármelyik háttér Kifogástalan állapotba kerül, a bejárati ajtó a normál terheléselosztási algoritmust fogja folytatni.

## <a name="disabling-health-probes"></a>Állapot-mintavétel letiltása

Ha a háttér-készletben egyetlen háttérrel rendelkezik, letilthatja az alkalmazás-háttér terhelését csökkentő állapot-mintavételt. Még ha több háttérrel is rendelkezik a háttér-készletben, de csak az egyikük engedélyezett állapotban van, letilthatja az állapot-mintavételt.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
