---
title: Azure bejárati ajtó szabályait végrehajtó műveletek
description: Ez a cikk felsorolja az Azure bejárati szabályainak motorjának különböző műveleteit.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91569774"
---
# <a name="azure-front-door-rules-engine-actions"></a>Azure Front Door szabálymotor műveletei

A [AFD-szabályok motorjában](front-door-rules-engine.md)a szabály nulla vagy több egyeztetési feltételt és műveletet tartalmaz. Ez a cikk a AFD-szabályok motorjában használható műveletek részletes leírását tartalmazza.

Egy művelet határozza meg, hogy milyen viselkedést alkalmaz a rendszer az egyeztetési feltétel vagy az egyeztetési feltételek alapján azonosított kérelem típusára. A AFD-szabályok motorjában egy szabály legfeljebb öt műveletet tartalmazhat. Ezek közül csak az egyik útvonal-konfiguráció felülbírálási művelet (továbbítás vagy átirányítás) lehet.

A következő műveletek használhatók az Azure bejárati ajtó szabályainak motorjában.  

## <a name="modify-request-header"></a>Kérelem fejlécének módosítása

Ezzel a művelettel módosíthatja azokat a fejléceket, amelyek a forrásnak küldött kérésekben szerepelnek.

### <a name="required-fields"></a>Kötelező mezők

Művelet | HTTP-fejléc neve | Érték
-------|------------------|------
Append (Hozzáfűzés) | Ha ez a beállítás be van jelölve, és a szabály megfelel, a **fejlécben** megadott fejléc a megadott értékkel lesz hozzáadva a kéréshez. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő értékhez. | Sztring
Felülírás | Ha ez a beállítás be van jelölve, és a szabály megfelel, a **fejlécben** megadott fejléc a megadott értékkel lesz hozzáadva a kéréshez. Ha a fejléc már létezik, a megadott érték felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás a megfelelő szabályokkal lesz kiválasztva, és a szabályban megadott fejléc megtalálható, a rendszer törli a fejlécet a kérelemből. | Sztring

## <a name="modify-response-header"></a>Válasz fejlécének módosítása

Ezzel a művelettel módosíthatja azokat a fejléceket, amelyek az ügyfeleknek küldött válaszokban szerepelnek.

### <a name="required-fields"></a>Kötelező mezők

Művelet | HTTP-fejléc neve | Érték
-------|------------------|------
Append (Hozzáfűzés) | Ha ez a beállítás be van jelölve, és a szabály megfelel, a **fejlécben** megadott fejléc a megadott **értékkel** lesz hozzáadva a válaszhoz. Ha a fejléc már létezik, a rendszer hozzáfűzi az **értéket** a meglévő értékhez. | Sztring
Felülírás | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer a **fejlécben** megadott fejlécet adja hozzá a válaszhoz a megadott **érték** használatával. Ha a fejléc már létezik, az **érték** felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, és a szabály megfelel a szabályban megadott fejlécnek, a rendszer törli a fejlécet a válaszból. | Sztring

## <a name="route-configuration-overrides"></a>Útválasztó-konfigurációk felülírása 

### <a name="route-type-redirect"></a>Útvonal típusa: átirányítás

Ezzel a művelettel átirányíthatja az ügyfeleket egy új URL-címre. 

#### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Átirányítás típusa | Válassza ki a kérelmezőnek visszatérni kívánt választ: talált (302), áthelyezett (301), ideiglenes átirányítás (307) és végleges átirányítás (308).
Átirányítási protokoll | Egyeztetési kérelem, HTTP, HTTPS.
Cél gazdagép | Válassza ki annak az állomásnak a nevét, amelyre át szeretné irányítani a kérést. Hagyja üresen a bejövő gazdagép megőrzését.
Cél elérési útja | Adja meg az átirányításhoz használandó elérési utat. Hagyja üresen a bejövő elérési út megőrzését.  
Lekérdezési sztring | Adja meg az átirányítás során használt lekérdezési karakterláncot. Hagyja üresen a bejövő lekérdezési karakterlánc megőrzését. 
Cél töredéke | Adja meg az átirányítás során használandó kódrészletet. Hagyja üresen a bejövő töredék megőrzése érdekében. 


### <a name="route-type-forward"></a>Útvonal típusa: Forward

Ezzel a művelettel továbbíthatja az ügyfeleket egy új URL-címre. Ez a művelet az URL-címek újraírására és gyorsítótárazására szolgáló Al-műveleteket is tartalmaz. 

Mező | Leírás 
------|------------
A háttérkészlet | Válassza ki a háttér-készletet a felülbíráláshoz és a kérések kiszolgálásához, így az összes előre konfigurált háttér-készlet is megjelenik az előtérben lévő profilban. 
Továbbító protokoll | Egyeztetési kérelem, HTTP, HTTPS.
URL-átírás | Ezzel a művelettel átírhatja egy olyan kérelem elérési útját, amely útban van a forrásához. Ha engedélyezve van, tekintse meg a további szükséges mezőket.
Gyorsítótárazás | Engedélyezve, letiltva. Ha engedélyezve van, tekintse meg a következő további mezőket. 

#### <a name="url-rewrite"></a>URL-átírás

Ezzel a beállítással konfigurálhat egy opcionális **Egyéni továbbítási útvonalat** , amelyet a rendszer a háttérbe való továbbítás iránti kérelem összeállításakor használ.

Mező | Leírás 
------|------------
Egyéni továbbítási útvonal | Adja meg a kérések továbbításának elérési útját. 

#### <a name="caching"></a>Gyorsítótárazás

Ezekkel a beállításokkal szabályozhatja, hogy a fájlok hogyan legyenek gyorsítótárazva a lekérdezési karakterláncokat tartalmazó kérelmek esetében. Azt határozza meg, hogy az összes paraméter vagy a kijelölt paraméterek alapján gyorsítótárazza-e a tartalmakat. További beállításokkal írhatja felül az élettartam (TTL) értékét annak szabályozására, hogy a tartalom mennyi ideig marad a gyorsítótárban. A gyorsítótárazás műveletként való kényszerítéséhez állítsa a gyorsítótárazás mezőt "engedélyezve" értékre. A gyorsítótárazás kényszerítése esetén a következő lehetőségek jelennek meg: 

Gyorsítótár viselkedése |  Description              
---------------|----------------
Lekérdezési sztringek figyelmen kívül hagyása | Az eszköz gyorsítótárba helyezése után az összes további kérelem figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.
Minden egyedi URL gyorsítótárazása | Mindegyik egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot is, egyedi objektumként kezeli a saját gyorsítótárával.
Megadott lekérdezési karakterláncok figyelmen kívül hagyása | A "lekérdezési paraméterek" beállításban felsorolt kérelem URL-lekérdezési karakterláncai figyelmen kívül lesznek hagyva a gyorsítótárazáshoz.
Megadott lekérdezési karakterláncok belefoglalása | A "lekérdezési paraméterek" beállításban felsorolt kérelem URL-lekérdezési karakterláncai a gyorsítótárazáshoz használatosak.

További mezők |  Description 
------------------|---------------
Dinamikus tömörítés | A bejárati ajtó dinamikusan tömörítheti a tartalmat az Edge-ben, így kisebb és gyorsabb választ kaphat.
Lekérdezési paraméterek | A gyorsítótárazás alapjául szolgáló engedélyezett (vagy nem engedélyezett) paraméterek vesszővel tagolt listája.
Gyorsítótár időtartama | Gyorsítótár lejárati időtartama (nap, óra, perc, másodperc) Minden értéknek int-nek kell lennie. 

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan konfigurálhatja az első [szabályok motorját](front-door-tutorial-rules-engine.md). 
- További információ a [szabályok motorjának egyeztetési feltételeiről](front-door-rules-engine-match-conditions.md)
- További információ az [Azure bejárati ajtó szabályainak motorról](front-door-rules-engine.md)
