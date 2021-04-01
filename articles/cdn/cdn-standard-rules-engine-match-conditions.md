---
title: A Azure CDN Standard Rules Engine feltételeinek egyeztetése | Microsoft Docs
description: Dokumentáció az Azure Content Delivery Network (Azure CDN) Standard Rules Engine feltételeinek egyeztetéséhez.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "81259926"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>A standard szintű szabályok motorjában szereplő feltételek egyeztetése Azure CDN

Az Azure Content Delivery Network (Azure CDN) [szabványos szabályok motorjában](cdn-standard-rules-engine.md) a szabály egy vagy több egyeztetési feltételből és egy műveletből áll. Ez a cikk részletes leírást tartalmaz a Azure CDN szabványos szabályok motorjában használható egyezési feltételekről.

A szabály első része az egyeztetési feltétel vagy az egyeztetési feltételek halmaza. A Azure CDN Standard Rules (alapszabályok) motorjában minden szabály legfeljebb négy egyeztetési feltételt tartalmazhat. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez definiált műveletek vannak végrehajtva. Ha több egyeztetési feltételt használ, az egyeztetési feltételek a és a Logic használatával vannak csoportosítva.

Az egyeztetési feltételt például a következőre használhatja:

- Kérelmek szűrése adott IP-cím, ország vagy régió alapján.
- Kérelmek szűrése fejléc-információk alapján.
- A mobileszközök vagy asztali eszközök kéréseinek szűrése.

## <a name="match-conditions"></a>Egyezési feltételek

A következő egyeztetési feltételek használhatók a standard szabályok motorjában a Azure CDN számára. 

### <a name="device-type"></a>Eszköz típusa 

A mobileszköz vagy asztali eszköz által küldött kérelmeket azonosítja.  

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | Mobil, asztali

### <a name="http-version"></a>HTTP-verzió

A kérések HTTP-verziója alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | 2,0, 1,1, 1,0, 0,9, összes

### <a name="request-cookies"></a>Cookie-k kérése

A kérelmeket a bejövő kérelemben található cookie-információk alapján azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Cookie neve | Operátor | Cookie értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

#### <a name="key-information"></a>Legfontosabb információk

- A cookie-nevek megadásakor nem használhat helyettesítő karaktereket (beleértve a csillagokat ( \* )). a cookie nevének pontos nevét kell használnia.
- Ennek a egyeztetési feltételnek a példánya esetében csak egyetlen cookie-nevet adhat meg.
- A cookie-nevek összehasonlítása a kis-és nagybetűk megkülönböztetése nélkül történik.
- Több cookie-érték megadásához használjon egyetlen helyet az egyes cookie-értékek között. 
- A cookie-értékek kihasználhatják a helyettesítő karakteres értékeket.
- Ha nincs megadva helyettesítő karakter, a rendszer csak a pontos egyezést elégíti ki az egyeztetési feltételnek. Például az "érték" a "value" kifejezésnek felel meg, de nem "érték1". 

### <a name="post-argument"></a>Bejegyzés argumentuma

A kérésben használt POST kérelem metódusa által megadott argumentumok alapján azonosítja a kérelmeket. 

#### <a name="required-fields"></a>Kötelező mezők

Argumentum neve | Operátor | Argumentum értéke | Eset átalakítása
--------------|----------|----------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="query-string"></a>Lekérdezési sztring

A megadott lekérdezési karakterlánc paramétert tartalmazó kérelmeket azonosítja. Ez a paraméter olyan értékre van beállítva, amely megfelel egy adott mintának. A kérelem URL-címében szereplő lekérdezési karakterlánc paraméterei (például **paraméter = érték**) határozzák meg, hogy ez a feltétel teljesül-e. Ez a megfeleltetési feltétel a lekérdezési karakterlánc paraméterét azonosítja a nevével, és egy vagy több értéket fogad el a paraméter értékeként.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Lekérdezési sztring | Eset átalakítása
---------|--------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="remote-address"></a>Távoli címek

A kérelmező helye vagy IP-címe alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|-----------------
Bármelyik | N/A
Földrajzi egyezés | Országkód
IP-egyeztetés | IP-cím (szóközzel tagolt)
Nem | N/A
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

### <a name="request-body"></a>A kérés törzse

A kérések törzsében megjelenő megadott szöveg alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | A kérés törzse | Eset átalakítása
---------|--------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="request-header"></a>Kérelem fejléce

A kérelemben megadott fejlécet használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Fejléc neve | Operátor | Fejléc értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="request-method"></a>Kérelem metódusa

A megadott kérési módszert használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | LETÖLTÉS, KÖZZÉTÉTEL, PUT, TÖRLÉS, FEJ, BEÁLLÍTÁSOK, NYOMKÖVETÉS

#### <a name="key-information"></a>Legfontosabb információk

- A Azure CDNban csak a GET Request metódus hozhatja meg a gyorsítótárazott tartalmat. Minden más kérelmezési módszer a hálózaton keresztül történik. 

### <a name="request-protocol"></a>Kérelem protokollja

Azokat a kérelmeket azonosítja, amelyek a megadott protokollt használják.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | HTTP, HTTPS

### <a name="request-url"></a>URL-cím kérése

A megadott URL-címnek megfelelő kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | URL-cím kérése | Eset átalakítása
---------|-------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

#### <a name="key-information"></a>Legfontosabb információk

- A szabály feltételének használatakor ügyeljen arra, hogy a protokoll információit tartalmazza. Például: *https://www . \<yourdomain\> . com*.

### <a name="url-file-extension"></a>URL-fájlkiterjesztés

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fájlkiterjesztést a kérelem URL-címében található fájlnévben.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Mellék | Eset átalakítása
---------|-----------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

#### <a name="key-information"></a>Legfontosabb információk

- A bővítményhez ne adjon meg egy kezdő időszakot; a *. html* helyett például *HTML* -t használjon.

### <a name="url-file-name"></a>URL-fájl neve

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fájlnevet a kérelmező URL-címében.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Fájlnév | Eset átalakítása
---------|-----------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

#### <a name="key-information"></a>Legfontosabb információk

- Több fájl nevének megadásához külön szóközzel válassza el az egyes fájlneveket. 

### <a name="url-path"></a>URL-cím elérési útja

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott elérési utat a kérelem URL-címében.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Érték | Eset átalakítása
---------|-------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

#### <a name="key-information"></a>Legfontosabb információk

- A fájlnév érték kihasználhatja a helyettesítő karakteres értékeket. Például az egyes fájlnevek mintázata egy vagy több csillagból (*) állhat, ahol minden csillag egy vagy több karakterből áll.

## <a name="reference-for-rules-engine-match-conditions"></a>A szabályok motorjának egyeztetési feltételei

### <a name="standard-operator-list"></a>Szabványos operátorok listája

Azok a szabályok, amelyek a normál operátorok listájából fogadnak értékeket, a következő operátorok érvényesek:

- Bármelyik
- Egyenlő 
- Contains 
- Kezdete 
- Végződik 
- Kisebb, mint
- Kisebb vagy egyenlő
- Nagyobb, mint
- Nagyobb vagy egyenlő
- Nem
- Nem tartalmazza
- Nem kezdődik 
- Nem végződik 
- Nem kisebb, mint
- Nem kisebb vagy egyenlő
- Nem nagyobb, mint
- Nem nagyobb vagy egyenlő

A (z) *vagy* annál *kisebb* numerikus operátorok esetében a felhasznált összehasonlítás a hosszon alapul. Ebben az esetben az egyeztetési feltételben szereplő értéknek olyan egész számnak kell lennie, amelynek meg kell egyeznie az összehasonlítani kívánt hosszsal. 

## <a name="next-steps"></a>Következő lépések

- [Azure CDN áttekintése](cdn-overview.md)
- [A Standard szabálymotor referenciája](cdn-standard-rules-engine-reference.md)
- [Műveletek a standard szabályok motorban](cdn-standard-rules-engine-actions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
