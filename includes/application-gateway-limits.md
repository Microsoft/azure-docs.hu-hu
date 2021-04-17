---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 1166585c7291c4fe0d78cbc9540e3f08f985db6c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107589991"
---
| Erőforrás | Korlát | Megjegyzés |
| --- | --- | --- |
| Azure Application Gateway |Előfizetésenként 1000 | |
| Előoldali IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előoldali portok |100<sup>1</sup> | |
| Háttércímkészletek |100<sup>1</sup> | |
| Háttérkiszolgálók készletenként |1200 | |
| HTTP-figyelők |200<sup>1</sup> |Legfeljebb 100 aktív figyelő lehet, amelyek a forgalmat irányítók. Aktív figyelők = a figyelők teljes száma – a figyelők nem aktívak.<br>Ha egy útválasztási szabályon belüli alapértelmezett konfiguráció a forgalom átirányításához van beállítva (például listenerrel, háttérkészletekkel és HTTP-beállításokkal rendelkezik), akkor az is listenernek számít.|
| HTTP-terheléselosztási szabályok |100<sup>1</sup> | |
| Háttér-HTTP-beállítások |100<sup>1</sup> | |
| Átjárónkénti példányok száma |V1 termékváltozat – 32<br>V2 termékváltozat – 125 | |
| SSL-tanúsítványok |100<sup>1</sup> |HTTP- figyelőnként 1 |
| SSL-tanúsítvány maximális mérete |V1 termékváltozat – 10 KB<br>V2 termékváltozat – 16 KB| |
| Hitelesítési tanúsítványok |100 | |
| Megbízható főtanúsítványok |100 | |
| Kérés időkorlátja minimális |1 másodperc | |
| Kérelmek maximális időtúllépése |24 óra | |
| Helyek száma |100<sup>1</sup> |HTTP- figyelőnként 1 |
| Figyelőnkénti URL-térképek |1 | |
| Elérésiút-alapú szabályok maximális száma URL-térképenként|100||
| Átirányítási konfigurációk |100<sup>1</sup>| |
| Újraírási szabálykészletek száma |400| |
| A fejléc- vagy URL-konfigurációk száma újraírási szabálykészletenként|40| |
| Feltételek száma újraírási szabálykészletenként|40| |
| Egyidejű WebSocket-kapcsolatok |Közepes átjárók 20k<sup>2</sup><br> Nagyméretű átjárók 50k<sup>2</sup>| |
| URL-cím maximális hossza|32 KB| |
| A HTTP/2 fejléc maximális mérete |16 KB| |
| Fájlfeltöltés maximális mérete, Standard |2 GB | |
| Maximális fájlfeltöltési méret WAF |V1 közepes WAF-átjárók, 100 MB<br>V1 nagy WAF-átjárók, 500 MB<br>V2 WAF, 750 MB| |
| WAF-törzs méretkorlátja fájlok nélkül|128 KB||
| Egyéni WAF-szabályok maximálisa|100||
| WAF-kizárások maximális száma Application Gateway|40||

<sup>1</sup> WaF-kompatibilis SKUs esetén az erőforrások számát 40-re kell korlátozni.

<sup>2</sup> A korlát Application Gateway erőforrásonként nem Application Gateway példányonként.
