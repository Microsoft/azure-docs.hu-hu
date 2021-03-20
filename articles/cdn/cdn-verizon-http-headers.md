---
title: A Azure CDN Rules Engine Verizon-specifikus HTTP-fejlécei | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a Verizon-specifikus HTTP-fejlécek Azure CDN Rules Engine használatával.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343217"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>A Azure CDN Rules Engine Verizon-specifikus HTTP-fejlécei

A **Verizon** -termékek prémium szintű Azure CDN a HTTP-kérések a forráskiszolgálón való elküldésekor a jelenléti pont (POP) kiszolgáló egy vagy több fenntartott fejlécet (vagy proxy speciális fejlécet) adhat hozzá az ügyfél-kérelemben a pop-hoz. Ezek a fejlécek a normál továbbítási fejléceken kívül is megérkeztek. További információ a szabványos kérelmek fejlécéről: [kérelmek mezői](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Ha szeretné megakadályozni, hogy ezek a fenntartott fejlécek hozzá legyenek adva a Azure CDN (Content Delivery Network) POP-kérelemhez a forráskiszolgálón, létre kell hoznia egy szabályt a szabályok motor [speciális fejlécek szolgáltatásával](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) . Ebben a szabályban zárja ki az eltávolítani kívánt fejlécet a fejlécek mezőben lévő fejlécek alapértelmezett listájából. Ha engedélyezte a [hibakeresési gyorsítótár válaszának fejléceit](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm), ügyeljen arra, hogy hozzáadja a szükséges `X-EC-Debug` fejléceket. 

Ha például el szeretné távolítani a `Via` fejlécet, a szabály fejlécek mezőjében szerepelnie kell a következő fejlécek listájának: *x-Forwarded-For, x-továbbítva-proto, x-Host, x-Midgress, x-Gateway-List, x-EC-name, Host*. 

![Proxy speciális fejlécek szabálya](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A következő táblázat azokat a fejléceket ismerteti, amelyeket a Verizon CDN POP a kérelemben adhat hozzá:

Kérelem fejléce | Leírás | Példa
---------------|-------------|--------
[Keresztül](#via-request-header) | Azonosítja a kérést a forráskiszolgálón futtató POP-kiszolgálót. | HTTP/1.1 ECS (DCA/1A2B)
X – továbbított – a következőhöz: | A kérelmező IP-címét jelzi.| 10.10.10.10
X – továbbított – proto | A kérelem protokollját jelzi. | http
X-Host | Megadja a kérelem állomásnevét. | cdn.mydomain.com
X – Midgress | Azt jelzi, hogy a kérés egy másik CDN-kiszolgálón keresztül lett-e proxyn. Például egy POP-kiszolgáló – forrás védelmi kiszolgáló vagy egy POP-kiszolgáló – ADN átjárókiszolgáló. <br />Ezt a fejlécet csak akkor adja hozzá a rendszer a kéréshez, ha a midgress forgalom történik. Ebben az esetben a fejléc 1 értékre van állítva, jelezve, hogy a kérés egy további CDN-kiszolgálón keresztül lett proxyn.| 1
[Állomás](#host-request-header) | Azonosítja a gazdagépet és azt a portot, ahol a kért tartalom található. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: az ügyfél-forráshoz rendelt ADN Gateway-kiszolgálók feladatátvételi listáját azonosítja. <br />Origin Shield: az ügyfél-forráshoz rendelt Origin Shield-kiszolgálók készletét jelzi. | `icn1,hhp1,hnd1`
X-EC-_&lt; name &gt;_ | Az *x-EC* -vel kezdődő kérelmek fejlécei (például az x-EC-tag, az [x-EC-debug](cdn-http-debug-headers.md)) a CDN használatára vannak fenntartva.| WAF – éles üzem

## <a name="via-request-header"></a>Kérelem fejléce
Az `Via` alábbi szintaxissal adható meg, hogy a kérelem fejléce milyen formátumban adja meg a pop-kiszolgálót:

`Via: Protocol from Platform (POP/ID)` 

A szintaxisban használt kifejezések a következőképpen vannak meghatározva:
- Protokoll: a kérelem proxyként használt protokolljának (például HTTP/1.1) a verziószámát adja meg. 

- Platform: arra a platformra utal, amelyre a tartalmat kérték. A következő kódok érvényesek ehhez a mezőhöz: 

    Code | Platform
    -----|---------
    ECAcc | Nagyméretű HTTP
    ECS   | Kis méretű HTTP
    ECD   | Application Delivery Network (ADN)

- POP: a kérést kezelő [pop](cdn-pop-abbreviations.md) -t jelzi. 

- AZONOSÍTÓ: csak belső használatra.

### <a name="example-via-request-header"></a>Példa kérelem fejlécén keresztül

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Gazdagép-kérelem fejléce
A POP-kiszolgálók felülírják a `Host` fejlécet, ha az alábbi feltételek mindegyike teljesül:
- A kért tartalom forrása egy ügyfél-forráskiszolgáló.
- A megfelelő ügyfél-forrás HTTP-állomásfejléc-fejlécének beállítása nem üres.

A `Host` rendszer felülírja a kérelem fejlécét, hogy tükrözze a HTTP-állomásfejléc beállításban meghatározott értéket.
Ha az ügyfél forrásának HTTP-állomásfejléc beállítása üresre van állítva, akkor a `Host` kérelmező által küldött kérelem fejléce továbbítva lesz az ügyfél forrás-kiszolgálójára.

## <a name="x-gateway-list-request-header"></a>X-Gateway-List kérelem fejléce
Egy POP-kiszolgáló hozzáadja/felülírja az X-Gateway-List kérelem fejlécét, ha teljesülnek a következő feltételek valamelyike:
- A kérelem az ADN platformra mutat.
- A kérést a rendszer továbbítja egy ügyfél-forráskiszolgáló számára, amelyet a forrás pajzs funkciója véd.

