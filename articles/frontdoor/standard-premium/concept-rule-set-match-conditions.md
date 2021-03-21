---
title: Az Azure bejárati ajtó standard/prémium szabálykészlet egyeztetési feltételeinek beállítása
description: Ez a cikk az Azure bejárati ajtó standard/prémium szabálykészlet által elérhető különböző egyeztetési feltételek listáját tartalmazza.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4c65d0e7f80fab59ca7df4849df7117d482352c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099193"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-match-conditions"></a>Azure bejárati ajtó standard/prémium (előzetes verzió) szabályának beállítása egyeztetési feltételek

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy szabályt a Azure Portalban az első szabálykészlet alapján. Az Azure-beli bejárati ajtó standard/prémium [szabálykészlet beállításban](concept-rule-set.md)egy szabály nulla vagy több egyeztetési feltételből és egy műveletből áll. Ez a cikk részletesen ismerteti az Azure-beli előtérben szabványos/prémium szabálykészlet által használható egyezési feltételeket.

A szabály első része az egyeztetési feltétel vagy az egyeztetési feltételek halmaza. Egy szabály legfeljebb 10 egyeztetési feltételt tartalmazhat. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez meghatározott műveleteket végeztek. Ha több egyeztetési feltételt használ, az egyeztetési feltételek a és a Logic használatával vannak csoportosítva. A több értéket támogató összes egyeztetési feltétel ("szóközzel tagolt") esetén a "vagy" operátor feltételezett.

Az egyeztetési feltételt például a következőre használhatja:

* Kérelmek szűrése adott IP-cím, ország vagy régió alapján.
* Kérelmek szűrése fejléc-információk alapján.
* A mobileszközök vagy asztali eszközök kéréseinek szűrése.
* A kérelem fájljának neve és a fájl kiterjesztése alapján szűri a kérelmeket.
* A kérelem URL-címe, protokoll, elérési út, lekérdezési karakterlánc, post ARG stb. alapján szűri a kérelmeket.

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A következő egyeztetési feltételek használhatók az Azure bejárati ajtó standard/prémium szabályainak beállításakor:

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
Sztring | [Operátorok listája](#operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs

## <a name="query-string"></a>Lekérdezési sztring

A megadott lekérdezési karakterlánc paramétert tartalmazó kérelmeket azonosítja. Ez a paraméter olyan értékre van beállítva, amely megfelel egy adott mintának. A kérelem URL-címében szereplő lekérdezési karakterlánc paraméterei (például **paraméter = érték**) határozzák meg, hogy ez a feltétel teljesül-e. Ez a megfeleltetési feltétel a lekérdezési karakterlánc paraméterét azonosítja a nevével, és egy vagy több értéket fogad el a paraméter értékeként.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Lekérdezési sztring | Eset átalakítása
---------|--------------|---------------
[Operátorok listája](#operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs

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

* CIDR-jelölés használata.
* Több IP-cím és IP-címterület esetén a "vagy a" logika működik.
    * **IPv4-példa**: Ha a *1.2.3.4* és a *10.20.30.40* két IP-címet ad hozzá, akkor a feltételt a rendszer a 1.2.3.4 vagy a 10.20.30.40 címről érkező kérelmek esetén egyezteti.
    * **IPv6-példa**: ha a *1:2:3:4:5:6:7:8* -es és a *10:20:30:40:50:60:70:80*-as két IP-címet ad hozzá, akkor a feltételt akkor kell egyeztetni, ha a 1:2:3:4:5:6:7:8-tól vagy 10:20:30:40:50:60:70:80-tól érkező összes kérelem
* Az IP-címterület szintaxisa az alapszintű IP-cím, amelyet egy perjel és az előtag mérete követ. Például:
    * **IPv4-példa**: a *5.5.5.64/26* a 5.5.5.64-en keresztül a 5.5.5.127-on keresztül érkező kérésekre illeszkedik.
    * **IPv6-példa**: a *1:2:3:/48* a 1:2:3:0:0:0:0:0 és 1:2:3 közötti címekről érkező összes kérelemre illeszkedik: FFFF: FFFF: FFFF: FFFF: FFFF.

## <a name="request-body"></a>A kérés törzse

A kérések törzsében megjelenő megadott szöveg alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | A kérés törzse | Eset átalakítása
---------|--------------|---------------
[Operátorok listája](#operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs

## <a name="request-header"></a>Kérelem fejléce

A kérelemben megadott fejlécet használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Fejléc neve | Operátor | Fejléc értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Operátorok listája](#operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs

## <a name="request-method"></a>Kérelem metódusa

A megadott kérési módszert használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Támogatott értékek
---------|----------------
Egyenlő, nem egyenlő | LETÖLTÉS, KÖZZÉTÉTEL, PUT, TÖRLÉS, FEJ, BEÁLLÍTÁSOK, NYOMKÖVETÉS

#### <a name="key-information"></a>Legfontosabb információk

Csak a GET kérési módszer tud gyorsítótárazott tartalmat előállítani az Azure-beli bejárati ajtón. Minden más kérelmezési módszer a hálózaton keresztül történik.

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
[Operátorok listája](#operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs

#### <a name="key-information"></a>Legfontosabb információk

A szabály feltételének használatakor ügyeljen arra, hogy a protokoll információit tartalmazza. Például: *https://www . \<yourdomain\> . com*.

## <a name="request-file-extension"></a>Fájl kiterjesztésének kérése

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fájlkiterjesztést a kérelem URL-címében található fájlnévben.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Mellék | Eset átalakítása
---------|-----------|---------------
[Operátorok listája](#operator-list)  | Karakterlánc, int | Kisbetűs, nagybetűs

#### <a name="key-information"></a>Legfontosabb információk

A bővítményhez ne adjon meg egy kezdő időszakot; a *. html* helyett például *HTML* -t használjon.

## <a name="request-file-name"></a>Kérelem fájljának neve

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fájlnevet a kérelmező URL-címében.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Fájlnév | Eset átalakítása
---------|-----------|---------------
[Operátorok listája](#operator-list)| Karakterlánc, int | Kisbetűs, nagybetűs

## <a name="request-path"></a>Kérés útvonala

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott elérési utat a kérelem URL-címében.

#### <a name="required-fields"></a>Kötelező mezők

Operátor | Érték | Eset átalakítása
---------|-------|---------------
[Operátorok listája](#operator-list) | Karakterlánc, int | Kisbetűs, nagybetűs

## <a name="operator-list"></a><a name = "operator-list"></a>Operátorok listája

Azok a szabályok, amelyek a normál operátorok listájából fogadnak értékeket, a következő operátorok érvényesek:

* Bármelyik
* Egyenlő
* Contains
* Kezdete
* Végződik
* Kisebb, mint
* Kisebb vagy egyenlő
* Nagyobb, mint
* Nagyobb vagy egyenlő
* Nem
* Nem tartalmazza
* Nem kezdődik
* Nem végződik
* Nem kisebb, mint
* Nem kisebb vagy egyenlő
* Nem nagyobb, mint
* Nem nagyobb vagy egyenlő
* Reguláris kifejezés

A (z) *vagy* annál *kisebb* numerikus operátorok esetében a felhasznált összehasonlítás a hosszon alapul. Az egyeztetési feltételben szereplő értéknek olyan egész számnak kell lennie, amely megegyezik az összehasonlítani kívánt hosszsal.

## <a name="regular-expression"></a>Reguláris kifejezés

A regex nem támogatja a következő műveleteket:

* Alkifejezések Backreferences és rögzítése
* Tetszőleges nulla szélességű kijelentések
* Alrutin-hivatkozások és rekurzív mintázatok
* Feltételes minták
* A vezérlési műveletek visszautasítása
* A \C egybájtos irányelve
* A \R sortörési egyeztetési direktíva
* A Match reset utasítás \K kezdete
* Képfeliratok és beágyazott kód
* Atomic grouping és a birtokosi számszerűsítő

## <a name="next-steps"></a>Következő lépések

* További információ a [szabálykészlet beállításáról](concept-rule-set.md).
* Ismerje meg, hogyan [konfigurálhatja az első szabályokat](how-to-configure-rule-set.md).
* További információ a [szabálykészlet műveleteiről](concept-rule-set-actions.md).
