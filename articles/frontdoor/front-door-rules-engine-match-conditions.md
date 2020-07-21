---
title: Azure bejárati ajtó szabályainak motorja egyeztetési feltételek
description: Ez a cikk az Azure bejárati szabályainak motorjában elérhető különböző egyeztetési feltételek listáját tartalmazza.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 311914078f8169a3b48b5559ed58a690c29be83a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512163"
---
# <a name="azure-front-door-rules-engine-match-conditions"></a>Azure Front Door szabálymotor egyezési feltételei

A [AFD-szabályok motorjában](front-door-rules-engine.md) a szabály nulla vagy több egyeztetési feltételt és egy műveletet tartalmaz. Ez a cikk részletes leírást tartalmaz a AFD-szabályok motorjában használható egyeztetési feltételekről.

A szabály első része az egyeztetési feltétel vagy az egyeztetési feltételek halmaza. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez definiált műveletek vannak végrehajtva. Ha több egyeztetési feltételt használ, az egyeztetési feltételek a és a Logic használatával vannak csoportosítva. A több értéket támogató összes egyeztetési feltétel esetében (lásd az alábbi "szóközzel tagolt"), a "vagy" operátort feltételezi.

Az egyeztetési feltételt például a következőre használhatja:

- Kérelmek szűrése adott IP-cím, ország vagy régió alapján.
- Kérelmek szűrése fejléc-információk alapján.
- A mobileszközök vagy asztali eszközök kéréseinek szűrése.

A következő egyeztetési feltételek használhatók az Azure bejárati szabályainak motorjában.  

## <a name="device-type"></a>Eszköz típusa 

A mobileszköz vagy asztali eszköz által küldött kérelmeket azonosítja.  

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | Mobil, asztali

## <a name="post-argument"></a>Bejegyzés argumentuma

A kérésben használt POST kérelem metódusa által megadott argumentumok alapján azonosítja a kérelmeket. 

#### <a name="required-fields"></a>Kötelező mezők

Argumentum neve | Operátor | Argumentum értéke | Eset átalakítása
--------------|----------|----------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

## <a name="query-string"></a>Lekérdezési sztring

A megadott lekérdezési karakterlánc paramétert tartalmazó kérelmeket azonosítja. Ez a paraméter olyan értékre van beállítva, amely megfelel egy adott mintának. A kérelem URL-címében szereplő lekérdezési karakterlánc paraméterei (például **paraméter = érték**) határozzák meg, hogy ez a feltétel teljesül-e. Ez a megfeleltetési feltétel a lekérdezési karakterlánc paraméterét azonosítja a nevével, és egy vagy több értéket fogad el a paraméter értékeként.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Lekérdezési sztring | Eset átalakítása
---------|--------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

## <a name="remote-address"></a>Távoli címek

A kérelmező helye vagy IP-címe alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|-----------------
Földrajzi egyezés | Országkód
IP-egyeztetés | IP-cím (szóközzel tagolt)
Nem földrajzi egyezés | Országkód
Nem IP-egyeztetés | IP-cím (szóközzel tagolt)

#### <a name="key-information"></a>Legfontosabb információk

- CIDR-jelölés használata.
- Több IP-cím és IP-címterület megadásához használjon egyetlen helyet az értékek között:
  - **IPv4-példa**: a *1.2.3.4 10.20.30.40* a 1.2.3.4 vagy a 10.20.30.40 címről érkező összes kérésnek megfelel.
  - **IPv6-példa**: a *1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80* a 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 címről érkező kérelmekre illeszkedik.
- Az IP-címterület szintaxisa az alapszintű IP-cím, amelyet egy perjel és az előtag mérete követ. Például:
  - **IPv4-példa**: a *5.5.5.64/26* a 5.5.5.64-en keresztül a 5.5.5.127-on keresztül érkező kérésekre illeszkedik.
  - **IPv6-példa**: a *1:2:3:/48* a 1:2:3:0:0:0:0:0 – 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF címen megjelenő kérelmekre illeszkedik.

## <a name="request-body"></a>A kérés törzse

A kérések törzsében megjelenő megadott szöveg alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | A kérés törzse | Eset átalakítása
---------|--------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

## <a name="request-header"></a>Kérelem fejléce

A kérelemben megadott fejlécet használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Fejléc neve | Operátor | Fejléc értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

## <a name="request-method"></a>Kérelem metódusa

A megadott kérési módszert használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | LETÖLTÉS, KÖZZÉTÉTEL, PUT, TÖRLÉS, FEJ, BEÁLLÍTÁSOK, NYOMKÖVETÉS

#### <a name="key-information"></a>Legfontosabb információk

- Csak a GET kérési módszer tud gyorsítótárazott tartalmat előállítani az Azure-beli bejárati ajtón. Minden más kérelmezési módszer a hálózaton keresztül történik. 

## <a name="request-protocol"></a>Kérelem protokollja

Azokat a kérelmeket azonosítja, amelyek a megadott protokollt használják.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | HTTP, HTTPS

## <a name="request-url"></a>URL-cím kérése

A megadott URL-címnek megfelelő kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | URL-cím kérése | Eset átalakítása
---------|-------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

#### <a name="key-information"></a>Legfontosabb információk

- A szabály feltételének használatakor ügyeljen arra, hogy a protokoll információit tartalmazza. Például: * https://www . \<yourdomain\> . com*.

## <a name="request-file-extension"></a>Fájl kiterjesztésének kérése

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fájlkiterjesztést a kérelem URL-címében található fájlnévben.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Mellék | Eset átalakítása
---------|-----------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

#### <a name="key-information"></a>Legfontosabb információk

- A bővítményhez ne adjon meg egy kezdő időszakot; a *. html*helyett például *HTML* -t használjon.

## <a name="request-file-name"></a>Kérelem fájljának neve

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fájlnevet a kérelmező URL-címében.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Fájlnév | Eset átalakítása
---------|-----------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

#### <a name="key-information"></a>Legfontosabb információk

- Több fájlnév megadásához az ENTER billentyű lenyomásával válassza el az egyes fájlnevek nevét. 

## <a name="request-path"></a>Kérés útvonala

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott elérési utat a kérelem URL-címében.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Érték | Eset átalakítása
---------|-------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs, Trim, szóköz eltávolítása, URL-kódolás, URL-cím dekódolása

## <a name="standard-operator-list"></a>Szabványos operátorok listája

Azok a szabályok, amelyek a normál operátorok listájából fogadnak értékeket, a következő operátorok érvényesek:

- Bármely
- Egyenlő 
- Contains 
- Kezdete 
- Végződik 
- Kisebb
- Kisebb vagy egyenlő
- Nagyobb
- Nagyobb vagy egyenlő
- Nem
- Nem tartalmazza
- Nem kezdődik 
- Nem végződik 
- Nem kisebb, mint
- Nem kisebb vagy egyenlő
- Nem nagyobb, mint
- Nem nagyobb vagy egyenlő

A (z) *vagy*annál *kisebb* numerikus operátorok esetében a felhasznált összehasonlítás a hosszon alapul. Ebben az esetben az egyeztetési feltételben szereplő értéknek olyan egész számnak kell lennie, amelynek meg kell egyeznie az összehasonlítani kívánt hosszsal. 


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan állíthatja be az első [szabályok motorjának konfigurációját](front-door-tutorial-rules-engine.md). 
- További tudnivalók a [szabályok motor műveleteiről](front-door-rules-engine-actions.md)
- További információ az [Azure bejárati ajtó szabályainak motorról](front-door-rules-engine.md)
