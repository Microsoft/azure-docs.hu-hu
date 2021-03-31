---
title: X-EC-hibakeresés HTTP-fejlécek Azure CDN Rules Engine-hez | Microsoft Docs
description: Az X-EC-debug hibakeresési gyorsítótár-kérelem fejléce további információkat tartalmaz a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek a Verizon-specifikusak.
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
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 4154c6a1e739f935022271e7a101f39d3ee5c500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343020"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EC-hibakeresés a HTTP-fejlécek Azure CDN Rules Engine-hez
A hibakeresési gyorsítótár-kérelem fejléce `X-EC-Debug` További információkat nyújt a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek a **Verizon-termékek Azure CDN Premium** kiadására vonatkoznak.

## <a name="usage"></a>Használat
A POP-kiszolgálóknak a felhasználónak küldött válasz `X-EC-Debug` csak a következő feltételek teljesülése esetén tartalmazza a fejlécet:

- A [hibakeresési gyorsítótár-válasz fejlécek funkció](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) engedélyezve van a szabályok motorban a megadott kéréshez.
- A megadott kérelem meghatározza a hibakeresési gyorsítótár válaszának azon fejléceit, amelyek szerepelni fognak a válaszban.

## <a name="requesting-debug-cache-information"></a>Hibakeresési gyorsítótár adatainak kérése
A megadott kérelemben szereplő alábbi irányelvek segítségével definiálhatja a válaszban szerepeltetni kívánt hibakeresési gyorsítótár-információkat:

Kérelem fejléce | Leírás |
---------------|-------------|
X-EC-hibakeresés: x-EC-cache | [Gyorsítótár állapotának kódja](#cache-status-code-information)
X-EC-Debug: x-EC-cache-Remote | [Gyorsítótár állapotának kódja](#cache-status-code-information)
X-EC-hibakeresés: x-EC-pipa-gyorsítótárazható | [Gyorsítótárazható](#cacheable-response-header)
X-EC-Debug: x-EC-cache-Key | [Gyorsítótár – kulcs](#cache-key-response-header)
X-EC-Debug: x-EC-cache-State | [Gyorsítótár állapota](#cache-state-response-header)

### <a name="syntax"></a>Syntax

A hibakeresési gyorsítótár válaszának fejléceit a következő fejléc és a kérésben megadott irányelvek alapján kérheti le:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Minta X-EC-hibakeresési fejléc

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Gyorsítótár-állapotkód adatai
Az X-EC-debug válasz fejléce képes azonosítani a kiszolgálót, és hogyan kezeli a választ a következő irányelvek alapján:

Fejléc | Description
-------|------------
X-EC-hibakeresés: x-EC-cache | Ezt a fejlécet akkor kell jelenteni, ha a tartalom a CDN-en keresztül van átirányítva. Azonosítja a kérelmet teljesítő POP-kiszolgálót.
X-EC-Debug: x-EC-cache-Remote | Ezt a fejlécet csak akkor kell jelenteni, ha a kért tartalom a forrásként szolgáló védelmi kiszolgálón vagy az ADN-átjáró kiszolgálón lett gyorsítótárazva.

### <a name="response-header-format"></a>Válasz fejlécének formátuma

Az X-EC-debug fejléc a gyorsítótár állapotkód-információit a következő formátumban jelzi:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

A fenti válasz fejlécének szintaxisában használt kifejezések a következőképpen vannak meghatározva:
- StatusCode: azt jelzi, hogyan kezelte a kért tartalmat a CDN, amely egy gyorsítótár-állapotkód alapján van megjelenítve.
    
    Előfordulhat, hogy a rendszer a TCP_DENIED állapotkódot a NONE érték helyett, ha Token-Based hitelesítés miatt megtagadja a jogosulatlan kérelmeket. Ugyanakkor a rendszer továbbra is használja a NONE állapotkódot a gyorsítótár-állapotjelentések vagy a nyers naplófájlok megtekintésekor.

- Platform: arra a platformra utal, amelyre a tartalmat kérték. A következő kódok érvényesek ehhez a mezőhöz:

    Code  | Platform
    ------| --------
    ECAcc | Nagyméretű HTTP
    ECS   | Kis méretű HTTP
    ECD   | Alkalmazásszolgáltatási hálózat (ADN)

- POP: a kérést kezelő [pop](cdn-pop-abbreviations.md) -t jelzi. 

### <a name="sample-response-headers"></a>Példa válasz fejlécekre

A következő minta fejlécek a kérelem gyorsítótár-állapotkód információit tartalmazzák:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Gyorsítótárazható válasz fejléce
A `X-EC-Debug: x-ec-check-cacheable` Válasz fejléce azt jelzi, hogy a kért tartalom gyorsítótárazva lett-e.

Ez a válasz fejléce nem jelzi, hogy a gyorsítótárazás megtörtént-e. Ehelyett azt jelzi, hogy a kérelem alkalmas-e a gyorsítótárazásra.

### <a name="response-header-format"></a>Válasz fejlécének formátuma

A `X-EC-Debug` Válasz fejléce azt jelenti, hogy egy kérelem gyorsítótárazva lett-e a következő formátumban:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

A fenti válasz fejlécében használt kifejezés a következőképpen van definiálva:

Érték  | Leírás
-------| --------
IGEN    | Azt jelzi, hogy a kért tartalom gyorsítótárazásra alkalmas volt.
NO     | Azt jelzi, hogy a kért tartalom nem alkalmas a gyorsítótárazásra. Ez az állapot az alábbi okok egyike miatt lehet: <br /> -Customer-Specific konfiguráció: a fiókhoz tartozó konfiguráció megakadályozza, hogy a pop-kiszolgálók gyorsítótárazzák az eszközöket. Például a szabályok motorja megakadályozhatja, hogy egy eszköz gyorsítótárazva legyen, ha engedélyezi a gyorsítótár megkerülése funkciót a megfelelő kérésekhez.<br /> – Gyorsítótár-válasz fejlécei: a kért objektum Cache-Control és a fejlécek lejárta megakadályozza a POP-kiszolgálók gyorsítótárazását.
ISMERETLEN | Azt jelzi, hogy a kiszolgálók nem tudták felmérni, hogy a kért eszköz gyorsítótárazható-e. Ez az állapot általában akkor fordul elő, ha a kérést jogkivonat-alapú hitelesítés miatt megtagadja a rendszer.

### <a name="sample-response-header"></a>Példa válasz fejlécére

Az alábbi válasz-fejléc azt jelzi, hogy sikerült-e gyorsítótárazni a kért tartalmat:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key válasz fejléce
A `X-EC-Debug: x-ec-cache-key` Válasz fejléce a kért tartalomhoz társított fizikai gyorsítótár-kulcsot jelzi. A fizikai gyorsítótár-kulcs egy olyan útvonalból áll, amely a gyorsítótárazás céljára azonosítja az eszközt. Más szóval a kiszolgálók a gyorsítótár-kulcs által meghatározott elérési úttal összhangban keresik meg egy adott eszköz gyorsítótárazott verzióját.

Ez a fizikai gyorsítótár-kulcs egy dupla továbbítási perjel (//) karakterrel kezdődik, amelyet a tartalom kéréséhez használt protokoll (HTTP vagy HTTPS) követ. Ezt a protokollt követi a kért eszköz relatív elérési útja, amely a tartalom-hozzáférési ponttal kezdődik (például _/000001/_).

Alapértelmezés szerint a HTTP *-platformok szabványos gyorsítótár* használatára vannak konfigurálva, ami azt jelenti, hogy a gyorsítótárazási mechanizmus figyelmen kívül hagyja a lekérdezési karakterláncokat. Ez a típusú konfiguráció megakadályozza, hogy a gyorsítótár-kulcs a lekérdezési karakterlánc adatait is tartalmazza.

Ha egy lekérdezési karakterláncot rögzít a gyorsítótár-kulcsban, azt a rendszer a kivonatának megfelelőre konvertálja, majd beszúrja a kért objektum neve és a hozzá tartozó fájlkiterjesztés közé (például: Asset &lt; hash Value &gt; . html).

### <a name="response-header-format"></a>Válasz fejlécének formátuma

A `X-EC-Debug` Válasz fejléce a fizikai gyorsítótár-kulcs információit a következő formátumban jelenti:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Példa válasz fejlécére

A következő minta válasz fejléce a kért tartalom fizikai gyorsítótár-kulcsát jelzi:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Gyorsítótár állapotának válaszának fejléce
A `X-EC-Debug: x-ec-cache-state` Válasz fejléce a kért tartalom gyorsítótárbeli állapotát jelzi a kérelem időpontjában.

### <a name="response-header-format"></a>Válasz fejlécének formátuma

A `X-EC-Debug` Válasz fejléce a gyorsítótár állapotának adatait a következő formátumban jelenti:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

A fenti válasz fejlécének szintaxisában használt kifejezések a következőképpen vannak meghatározva:

- MASeconds: a kért tartalom Cache-Control fejlécében megadott Max-Age (másodpercben) érték.

- MATimePeriod: átalakítja a Max-Age értéket (azaz MASeconds) egy nagyobb egység (például nap) közelítő megfelelő értékére. 

- UnixTime: a kért tartalom gyorsítótár-időbélyegét jelzi UNIX-időben (más néven POSIX Time vagy UNIX EPOCH). A gyorsítótár-időbélyeg azt a kezdő dátumot és időpontot jelzi, amelyből a rendszer az adott eszköz ÉLETTARTAMát számítja ki. 

    Ha a forráskiszolgáló nem használ harmadik féltől származó HTTP-gyorsítótárazási kiszolgálót, vagy ha az adott kiszolgáló nem adja vissza az öregségi válasz fejlécét, akkor a gyorsítótár időbélyege mindig az eszköz lekérésének vagy újraellenőrzésének dátuma/időpontja lesz. Ellenkező esetben a POP-kiszolgálók a Age (kor) mezőt fogják használni az eszköz TTL-értékének kiszámításához a következő módon: lekérés/RevalidateDateTime-kor.

- DDD, DD MMM éééé óó: PP: SS GMT: a kért tartalom gyorsítótár-időbélyegét jelzi. További információkért tekintse meg a fenti UnixTime-kifejezést.

- CASeconds: a gyorsítótár időbélyegzője óta eltelt másodpercek számát jelzi.

- RTSeconds: azt jelzi, hogy hány másodpercig marad a gyorsítótárazott tartalom frissnek. Ez az érték a következőképpen számítható ki: RTSeconds = Max-Age-cache Age.

- RTTimePeriod: a fennmaradó TTL-értéket (azaz RTSeconds) átalakítja egy nagyobb egység (például nap) közelítő megfelelő értékére.

- ExpiresSeconds: a válasz fejlécében megadott dátum/idő előtt hátralévő másodpercek számát jelzi `Expires` . Ha a válasz `Expires` fejléce nem szerepel a válaszban, akkor a kifejezés értéke *none*.

### <a name="sample-response-header"></a>Példa válasz fejlécére

A következő minta válasz fejléce a kért tartalom gyorsítótár-állapotát jelzi a kérelem időpontjában:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

