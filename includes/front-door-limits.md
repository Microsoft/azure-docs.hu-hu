---
title: fájlbefoglalás
description: fájlbefoglalás
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334773"
---
| Erőforrás | Korlát |
| --- | --- |
| Azure bejárati ajtó erőforrásai/előfizetés | 100 |
| Előtér-gazdagépek, amelyeken erőforrásként egyéni tartományok szerepelnek | 500 |
| Útválasztási szabályok/erőforrás | 500 |
| Háttérbeli készletek/erőforrás | 50 |
| Háttérbeli végpontok | 100 |
| Útválasztási szabálynak megfelelő elérésiút-minták | 25 |
| URL-címek egyetlen gyorsítótár-kiürítési hívásban | 100 |
| Egyéni webalkalmazási tűzfalszabályok szabályai házirend szerint | 100 |
| Webalkalmazási tűzfal házirendje előfizetés alapján | 100 |
| Webalkalmazási tűzfal egyéni szabályoknak megfelelően | 10 |
| Webalkalmazási tűzfal IP-címeinek egyeztetési feltétele | 600 |
| Webalkalmazási tűzfal karakterláncának egyeztetési feltétel értéke | 10 |
| Webalkalmazási tűzfal karakterláncának egyezési értéke | 256 |
| Webalkalmazási tűzfal a törzs paraméterének neve után | 256 |
| Webalkalmazási tűzfal HTTP-fejlécének neve hossz | 256 |
| Webalkalmazási tűzfal cookie-nevének hossza | 256 |
| Webalkalmazási tűzfal HTTP-kérés törzsének mérete megvizsgálva | 128 KB |
| Webalkalmazási tűzfal – egyéni válasz törzsének hossza | 2 KB |

### <a name="timeout-values"></a>Időtúllépési értékek
#### <a name="client-to-front-door"></a>Ügyfélről a bejárat felé
* A bejárati ajtó 61 másodperces üresjárati TCP-kapcsolati időtúllépéssel rendelkezik.

#### <a name="front-door-to-application-back-end"></a>Az alkalmazás háttérbe zárása
* Ha a válasz egy darabokra épülő válasz, a rendszer a 200 értéket adja vissza, ha az első adathalmaz érkezik.
* A HTTP-kérésnek a háttérbe való továbbítása után a bejárati ajtó 30 másodpercig várakozik a háttér első csomagjának. Ezután egy 503-es hibát ad vissza az ügyfélnek. Ez az érték konfigurálható az API mező sendRecvTimeoutSeconds keresztül.
    * Gyorsítótárazási forgatókönyvek esetén ez az időtúllépés nem konfigurálható, így ha egy kérés gyorsítótárazva van, és a rendszer a bejárati ajtótól vagy a háttértől több mint 30 másodpercet vesz igénybe, akkor a rendszer 504-es hibát ad vissza az ügyfélnek. 
* Miután az első csomag érkezett a háttérből, a bejárati ajtó 30 másodpercig várakozik üresjárati időkorlátban. Ezután egy 503-es hibát ad vissza az ügyfélnek. Ez az időtúllépési érték nem konfigurálható.
* A back-end TCP-munkamenet időtúllépése 90 másodperc.

### <a name="upload-and-download-data-limit"></a>Adatkorlát feltöltése és letöltése

|  | Darabolásos átvitel kódolásával (TÁBLAKIFEJEZÉSEK) | HTTP-darabolás nélkül |
| ---- | ------- | ------- |
| **Letöltés** | A letöltési méretnek nincs korlátja. | A letöltési méretnek nincs korlátja. |
| **Feltöltés** |    Nincs korlát, amíg az egyes TÁBLAKIFEJEZÉSEK-feltöltések száma kevesebb, mint 2 GB. | A méret nem lehet nagyobb 2 GB-nál. |

### <a name="other-limits"></a>Egyéb korlátok
* URL-cím maximális mérete – 8 192 bájt – a nyers URL-cím maximális hosszát határozza meg (séma + állomásnév + port + Path + lekérdezési karakterlánc az URL-cím)
* Lekérdezési karakterlánc maximális mérete – 4 096 bájt – a lekérdezési karakterlánc maximális hosszát adja meg bájtban kifejezve.
* A HTTP-válasz fejlécének maximális mérete az állapot mintavételi URL-címéből – 4 096 bájt – az állapot-mintavételek összes válasz fejlécének maximális hosszát adja meg. 
