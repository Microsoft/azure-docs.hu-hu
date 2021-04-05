---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516994"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Mit lehet elérni egy fürttel?

Egy Event Hubs-fürt esetében a betöltés és az adatfolyam mennyisége a különböző tényezőktől, például a termelőktől, a fogyasztóktól, a betöltés és a feldolgozástól, valamint sok más tényezőtől függ. 

Az alábbi táblázat a tesztelés során elért teljesítményteszt-eredményeket mutatja be:

| Hasznos adat alakzat | Fogadók | Bejövő sávszélesség| Bejövő üzenetek | Kimenő sávszélesség | Kimenő üzenetek | Teljes TUs | TUs/CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB kötegek | 2 | 400 MB/s | 400k üzenetek/mp | 800 MB/s | 800k üzenetek/mp | 400 TUs | 100 TUs | 
| 10x10KB kötegek | 2 | 666 MB/s | 66.6 üzenet/mp | 1,33 GB/s | 133k üzenetek/mp | 666 TUs | 166 TUs |
| 6x32KB kötegek | 1 | 1,05 GB/s | 34k üzenetek/mp | 1,05 GB/s | 34k üzenetek/mp | 1000 TUs | 250 TUs |

A tesztelés során a rendszer a következő feltételeket használta:

- A dedikált rétegbeli Event Hubs-fürt négy kapacitású egységgel (ke) lett használva. 
- A betöltéshez használt Event hub 200 partíciót tartalmazott. 
- A betöltött adatok az összes partíciótól kapott két fogadó alkalmazástól érkeztek.

### <a name="can-i-scale-updown-my-cluster"></a>Felskálázás a fürtön?

A létrehozást követően a fürtök számlázása legalább 4 órányi használat után történik. Az önkiszolgáló élmény előzetes kiadásában egy [támogatási kérést](https://ms.portal.azure.com/#create/Microsoft.Support) küldhet a Event Hubs csapatnak a **technikai**  >  **kvóta** kérése alatt, hogy a fürt fel-vagy leskálázásával bővítse  >  **vagy kicsinyítse a dedikált fürtöt** . Akár 7 napig is eltarthat a fürt skálázására irányuló kérelem teljesítése. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Hogyan működik a Geo-DR a fürttel?

Egy dedikált rétegbeli fürthöz tartozó névteret egy dedikált rétegbeli fürt egy másik névterével is megadhat. Nem javasoljuk, hogy egy dedikált szintű névteret a standard ajánlat névterével párosítson, mert az átviteli sebesség korlátja inkompatibilis lesz, és hibákat eredményezhet. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Áttelepíthetem a standard névtereket, hogy egy dedikált rétegbeli fürthöz tartozzanak?
Jelenleg nem támogatunk egy automatizált áttelepítési folyamatot, amely az Event hub-adatok standard névtérből egy Dedikáltra történő áttelepítését végzi. 
