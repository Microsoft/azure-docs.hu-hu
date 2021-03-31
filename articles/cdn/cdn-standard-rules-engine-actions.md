---
title: Műveletek a standard szintű szabályok motorjában a Azure CDNhoz | Microsoft Docs
description: Az Azure Content Delivery Network (Azure CDN) Standard Rules Engine műveleteit ismertető dokumentáció.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760124"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Műveletek a standard szabályok motorban Azure CDN

Az Azure Content Delivery Network (Azure CDN) [szabványos szabályok motorjában](cdn-standard-rules-engine.md) a szabály egy vagy több egyeztetési feltételből és egy műveletből áll. Ez a cikk részletesen ismerteti a Azure CDN standard szabályok motorjában használható műveleteket.

A szabály második része egy művelet. Egy művelet határozza meg, hogy milyen viselkedést alkalmaz a rendszer az egyeztetési feltétel vagy az egyeztetési feltételek alapján azonosított kérelem típusára.

## <a name="actions"></a>Műveletek

A következő műveletek használhatók a szabványos szabályok motorjában a Azure CDNhoz. 

### <a name="cache-expiration"></a>Gyorsítótár lejárata

Ezzel a művelettel írhatja felül a végpont élettartam (TTL) értékét azon kérelmek esetében, amelyekre a szabályok megfelelnek a feltételeknek.

#### <a name="required-fields"></a>Kötelező mezők

Gyorsítótár viselkedése |  Description              
---------------|----------------
Gyorsítótár megkerülése | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer nem gyorsítótárazza a tartalmat.
Felülbírálás | Ha ez a beállítás be van jelölve, és a szabály egyezést ad meg, a rendszer felülírja a forrásból visszaadott TTL-értéket a műveletben megadott értékkel. Ez a viselkedés csak akkor lesz alkalmazva, ha a válasz gyorsítótárazható. A Cache-Control Response fejlécben a "no-cache", a "Private", a "No-Store" értékekkel a művelet nem lesz alkalmazható.
Ha hiányzik, állítsa be | Ha ez a beállítás be van jelölve, és a szabály egyezik, ha a forrástól nem tért vissza TTL-érték, a szabály az ÉLETTARTAMot a műveletben megadott értékre állítja be. Ez a viselkedés csak akkor lesz alkalmazva, ha a válasz gyorsítótárazható. A Cache-Control Response fejlécben a "no-cache", a "Private", a "No-Store" értékekkel a művelet nem lesz alkalmazható.

#### <a name="additional-fields"></a>További mezők

Nap | Óra | Percek | Másodperc
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Gyorsítótár-kulcs lekérdezési karakterlánca

Ezzel a művelettel módosíthatja a gyorsítótár-kulcsot a lekérdezési karakterláncok alapján.

#### <a name="required-fields"></a>Kötelező mezők

Működés | Description
---------|------------
Belefoglalás | Ha ez a beállítás be van jelölve, és a szabály egyezik, a paraméterekben megadott lekérdezési karakterláncok a gyorsítótár kulcsának létrehozásakor is szerepelni tudnak. 
Minden egyedi URL gyorsítótárazása | Ha ez a beállítás be van jelölve, és a szabály megfelel, minden egyedi URL-cím saját gyorsítótár-kulccsal rendelkezik. 
Exclude | Ha ez a beállítás be van jelölve, és a szabály egyezik, a paraméterekben megadott lekérdezési karakterláncok ki lesznek zárva a gyorsítótár-kulcs létrehozásakor.
Lekérdezési sztringek figyelmen kívül hagyása | Ha ez a beállítás be van jelölve, és a szabály megfelel, a lekérdezési karakterláncok nem tekintendők a gyorsítótár kulcsának létrehozásakor. 

### <a name="modify-request-header"></a>Kérelem fejlécének módosítása

Ezzel a művelettel módosíthatja azokat a fejléceket, amelyek a forrásnak küldött kérésekben szerepelnek.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | HTTP-fejléc neve | Érték
-------|------------------|------
Append (Hozzáfűzés) | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer a **fejlécben** megadott fejlécet adja hozzá a kérelemhez a megadott értékkel. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő értékhez. | Sztring
Felülírás | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer a **fejlécben** megadott fejlécet adja hozzá a kérelemhez a megadott értékkel. Ha a fejléc már létezik, a megadott érték felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, a szabály egyezik, és a szabályban megadott fejléc jelen van, a rendszer törli a fejlécet a kérelemből. | Sztring

### <a name="modify-response-header"></a>Válasz fejlécének módosítása

Ezzel a művelettel módosíthatja azokat a fejléceket, amelyek az ügyfeleknek küldött válaszokban szerepelnek.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | HTTP-fejléc neve | Érték
-------|------------------|------
Append (Hozzáfűzés) | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer a **fejlécben** megadott fejlécet adja hozzá a válaszhoz a megadott **érték** használatával. Ha a fejléc már létezik, a rendszer hozzáfűzi az **értéket** a meglévő értékhez. | Sztring
Felülírás | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer a **fejlécben** megadott fejlécet adja hozzá a válaszhoz a megadott **érték** használatával. Ha a fejléc már létezik, az **érték** felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, a szabály egyezik, és a szabályban megadott fejléc jelen van, a rendszer törli a fejlécet a válaszból. | Sztring

### <a name="url-redirect"></a>URL-átirányítás

Ezzel a művelettel átirányíthatja az ügyfeleket egy új URL-címre. 

#### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Típus | Válassza ki a kérelmezőnek visszatérni kívánt választ: talált (302), áthelyezett (301), ideiglenes átirányítás (307) és végleges átirányítás (308).
Protokoll | Egyeztetési kérelem, HTTP, HTTPS.
Hostname (Gazdanév) | Válassza ki annak az állomásnak a nevét, amelyre át szeretné irányítani a kérést. Hagyja üresen a bejövő gazdagép megőrzését.
Elérési út | Adja meg az átirányításhoz használandó elérési utat. Hagyja üresen a bejövő elérési út megőrzését.  
Lekérdezési sztring | Adja meg az átirányítás során használt lekérdezési karakterláncot. Hagyja üresen a bejövő lekérdezési karakterlánc megőrzését. 
Töredék | Adja meg az átirányítás során használandó kódrészletet. Hagyja üresen a bejövő töredék megőrzése érdekében. 

Kifejezetten ajánlott abszolút URL-címet használni. Egy relatív URL-cím használatával átirányíthatja Azure CDN URL-címeket egy érvénytelen elérési útra. 

### <a name="url-rewrite"></a>URL-átírás

Ezzel a művelettel átírhatja egy olyan kérelem elérési útját, amely útban van a forrásához.

#### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Forrás mintája | Adja meg a forrás mintát a lecserélni kívánt URL-útvonalon. Jelenleg a forrás minta előtag-alapú egyezést használ. Az összes URL-cím eléréséhez használjon egy perjelet ( **/** ) a forrás minta értékének megfelelően.
Cél | Adja meg az újraíráshoz használandó célhely elérési útját. A cél elérési útja felülírja a forrás mintát.
Páratlan elérési út megőrzése | Ha az **Igen** értékre van állítva, a rendszer a forrás minta után hátralévő elérési utat hozzáfűzi az új cél elérési úthoz. 

## <a name="next-steps"></a>Következő lépések

- [Azure CDN áttekintése](cdn-overview.md)
- [A Standard szabálymotor referenciája](cdn-standard-rules-engine-reference.md)
- [Egyeztetési feltételek a standard szabályok motorban](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
