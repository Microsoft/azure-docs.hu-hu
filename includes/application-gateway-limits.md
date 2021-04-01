---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4c7c68bf690097004f2f3310a8d89ce954d7f87f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612962"
---
| Erőforrás | Korlát | Megjegyzés |
| --- | --- | --- |
| Azure Application Gateway |1 000/előfizetés | |
| Előtér-IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtér-portok |100<sup>1</sup> | |
| Háttérbeli címkészlet |100<sup>1</sup> | |
| Háttér-kiszolgálók/készlet |1200 | |
| HTTP-figyelők |200<sup>1</sup> |Legfeljebb 100 aktív figyelő, amely útválasztási forgalom. Aktív figyelők = a figyelők teljes száma – a figyelők nem aktívak.<br>Ha egy útválasztási szabályban az alapértelmezett konfiguráció a forgalom irányítására van beállítva (például egy figyelővel, egy háttér-készlettel és a HTTP-beállításokkal), akkor az is figyelőnek számít.|
| HTTP-terheléselosztási szabályok |100<sup>1</sup> | |
| Háttérbeli HTTP-beállítások |100<sup>1</sup> | |
| Átjárónkénti példányok száma |V1 SKU – 32<br>V2 SKU – 125 | |
| SSL-tanúsítványok |100<sup>1</sup> |1/HTTP-figyelő |
| SSL-tanúsítvány maximális mérete |V1 SKU – 10 KB<br>V2 SKU – 16 KB| |
| Hitelesítési tanúsítványok |100 | |
| Megbízható főtanúsítványok |100 | |
| Kérelem időkorlátja (minimum) |1 másodperc | |
| Kérelem időtúllépésének maximális száma |24 óra | |
| Helyek száma |100<sup>1</sup> |1/HTTP-figyelő |
| URL-térképek/figyelő |1 | |
| Elérésiút-alapú szabályok maximális elérési útja egy URL-címen|100||
| Átirányítási konfigurációk |100<sup>1</sup>| |
| Újraírható szabálykészlet száma |400| |
| Fejléc vagy URL-konfiguráció száma újraírható szabálykészlet alapján|40| |
| Feltételek száma újraírható szabálykészlet alapján|40| |
| Egyidejű WebSocket-kapcsolatok |Közepes átjárók 20000<br> Nagyméretű átjárók 50k| |
| URL-cím maximális hossza|32KB| |
| A fejléc maximális mérete HTTP/2 esetén |4KB| |
| Maximális fájlfeltöltés mérete, standard |2 GB | |
| Maximális fájlfeltöltés mérete WAF |V1 közepes WAF-átjárók, 100 MB<br>V1 nagyméretű WAF-átjárók, 500 MB<br>V2 WAF, 750 MB| |
| WAF-törzs mérete, fájlok nélkül|128 KB||
| Egyéni WAF-szabályok maximális száma|100||
| WAF-kizárások maximális száma Application Gateway|40||

<sup>1</sup> WAF-kompatibilis SKU esetén az erőforrások számát a 40 értékre kell korlátozni.
