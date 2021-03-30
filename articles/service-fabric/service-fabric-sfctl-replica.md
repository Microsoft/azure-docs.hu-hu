---
title: Azure Service Fabric CLI – sfctl replika
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A replikák kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 3ed1ecca686471e052f07e945738ad1a49e68464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260314"
---
# <a name="sfctl-replica"></a>sfctl-replika
Kezelheti a szolgáltatási partíciókhoz tartozó replikákat.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| telepített | A Service Fabric csomóponton telepített replika részleteinek beolvasása. |
| központilag telepített – lista | Lekéri egy Service Fabric csomóponton telepített replikák listáját. |
| állapotfigyelő | Beolvas egy Service Fabric állapot-nyilvántartó szolgáltatás replikájának vagy állapot nélküli szolgáltatási példányának állapotát. |
| információ | Egy Service Fabric partíció replikájának adatait kérdezi le. |
| list | Lekérdezi a Service Fabric szolgáltatás partíciójának replikáit. |
| eltávolítása | Eltávolít egy csomóponton futó szolgáltatási replikát. |
| jelentés – állapot | Állapotjelentés küldése az Service Fabric-replikán. |
| restart | Újraindítja egy csomóponton futó megőrzött szolgáltatás szolgáltatás-replikáját. |

## <a name="sfctl-replica-deployed"></a>központilag telepített sfctl-replika
A Service Fabric csomóponton telepített replika részleteinek beolvasása.

Lekéri a Service Fabric csomóponton telepített replika részleteit. Az információ magában foglalja a szolgáltatás típusát, a szolgáltatás nevét, az aktuális szolgáltatási műveletet, az aktuális szolgáltatási művelet kezdési dátumát, a partíció AZONOSÍTÓját, a replika/példány AZONOSÍTÓját, a jelentett terhelést és egyéb információkat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A replika azonosítója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-deployed-list"></a>sfctl-replika központi telepítése – lista
Lekéri egy Service Fabric csomóponton telepített replikák listáját.

A Service Fabric csomóponton telepített replikákkal kapcsolatos információkat tartalmazó lista beolvasása. Az adatok közé tartozik a partíció azonosítója, a replika azonosítója, a replika állapota, a szolgáltatás neve, a szolgáltatástípus neve és egyéb információk. PartitionId vagy ServiceManifestName lekérdezési paraméterek használatával adja vissza az adott paraméterekhez megadott értékekkel egyező központilag telepített replikákkal kapcsolatos információkat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Partition-ID | A partíció identitása. |
| --Service-manifest-Name | Egy Service Fabric-fürtben egy alkalmazás típusának részeként regisztrált szolgáltatási jegyzékfájl neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-health"></a>sfctl replika állapota
Beolvas egy Service Fabric állapot-nyilvántartó szolgáltatás replikájának vagy állapot nélküli szolgáltatási példányának állapotát.

Egy Service Fabric replika állapotának beolvasása. A EventsHealthStateFilter használatával szűrheti a replikán jelentett állapotadatok gyűjteményét az állapot alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A replika azonosítója. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-info"></a>sfctl-replika adatai
Egy Service Fabric partíció replikájának adatait kérdezi le.

A válasz tartalmazza az azonosítót, a szerepkört, az állapotot, az állapotot, a csomópont nevét, a üzemidőt és a replika egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A replika azonosítója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-list"></a>sfctl-replika listája
Lekérdezi a Service Fabric szolgáltatás partíciójának replikáit.

Az GetReplicas-végpont adatokat ad vissza a megadott partíció replikái számára. A válasz tartalmazza az azonosítót, a szerepkört, az állapotot, az állapotot, a csomópont nevét, a üzemidőt és a replika egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-remove"></a>sfctl replika eltávolítása
Eltávolít egy csomóponton futó szolgáltatási replikát.

Ez az API egy Service Fabric replika hibáját szimulálja egy Service Fabric fürtből származó replika eltávolításával. Az Eltávolítás bezárja a replikát, áthelyezi a replikát a szerepkör egyikére, majd eltávolítja a fürtből a replika összes állapotinformációkat. Ez az API a replika állapotának eltávolítására szolgáló elérési utat teszteli, és az ügyféloldali API-kon keresztül szimulálja a hibajelentés állandó elérési útját. Figyelmeztetés – az API használatakor nem végeznek biztonsági ellenőrzéseket. Az API helytelen használata adatvesztéshez vezethet az állapot-nyilvántartó szolgáltatások esetében. Emellett a forceRemove jelző az ugyanabban a folyamatban üzemeltetett összes többi replikára is hatással van.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A replika azonosítója. |
| --Force-Remove | Távolítson el egy Service Fabric alkalmazást vagy szolgáltatást erőteljesen, anélkül, hogy a kecses leállítási sorozatot kellene volna átvennie. Ez a paraméter egy olyan alkalmazás vagy szolgáltatás kényszerített törlésére használható, amelynél a törlés időtúllépés miatt meggátolja a replikák kecses bezárását. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-report-health"></a>sfctl-replika jelentés – állapot
Állapotjelentés küldése az Service Fabric-replikán.

A megadott Service Fabric replika állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentést és a jelentést tartalmazó tulajdonság forrásával kapcsolatos információkat. A rendszer elküldi a jelentést egy Service Fabric átjáró replikájának, amely továbbítja az állapot-tárolónak. Előfordulhat, hogy a jelentést az átjáró fogadja el, de a további ellenőrzés után a Health Store elutasította. Az állapotfigyelő például elutasítja a jelentést egy Érvénytelen paraméter miatt, például egy elavult sorszámot. Ha szeretné megtekinteni, hogy a jelentés alkalmazva lett-e az állapotfigyelő tárolóban, futtassa a replika állapotának beolvasása szakaszt, és ellenőrizze, hogy a jelentés megjelenik-e a

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Health-Property [kötelező] | Az állapotadatok tulajdonsága. <br><br> Az entitások különböző tulajdonságokhoz tartozhatnak állapotjelentést. A tulajdonság egy karakterlánc, nem pedig rögzített enumerálás, amely lehetővé teszi, hogy a jelentéskészítő rugalmasan kategorizálja a jelentést kiváltó állapot feltételeit. A "LocalWatchdog" SourceId forrásazonosító rendelkező jelentéskészítő például nyomon követheti a csomópontok rendelkezésre álló lemezének állapotát, így az adott csomópont "AvailableDisk" tulajdonságát is jelentheti. Ugyanaz a riporter figyelheti a csomópontok kapcsolatát, így a "kapcsolat" tulajdonságot is jelentheti ugyanazon a csomóponton. Az állapotfigyelő szolgáltatásban ezek a jelentések különálló állapotadatokként lesznek kezelve a megadott csomópont esetében. A SourceId forrásazonosító együtt a tulajdonság egyedileg azonosítja az állapotadatok adatait. |
| --állapotadatok [kötelező] | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "OK", "figyelmeztetés", "hiba", "ismeretlen". |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A partíció identitása. |
| – forrás-azonosító [kötelező] | Az állapottal kapcsolatos adatokat létrehozó ügyfél/watchdog/rendszer összetevőt azonosító forrás neve. |
| – Leírás | Az állapotadatok leírása. <br><br> A jelentésből származó, emberi olvasásra alkalmas adatok hozzáadására szolgáló szabad szöveget jelöli. A Leírás maximális hossza 4096 karakter. Ha a megadott karakterlánc már nem érhető el, a rendszer automatikusan csonkolja. A csonkítás során a Leírás utolsó karakterei a "[csonkolt]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi, hogy a felhasználók csonkítva lettek. Vegye figyelembe, hogy a csonkítás során a Leírás kevesebb, mint 4096 karakterből áll az eredeti sztringből. |
| – azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e juttatni. <br><br> Egy állapotjelentés érkezik egy Service Fabric Gateway-alkalmazásba, amely továbbítja az állapot-áruháznak. Ha az azonnali beállítás értéke TRUE (igaz), a rendszer azonnal elküldi a jelentést a HTTP-átjáróról az állapotfigyelő tárolóba, függetlenül a HTTP-átjáró alkalmazás által használt háló-ügyfél beállításaitól. Ez olyan kritikus fontosságú jelentések esetében hasznos, amelyeket a lehető leghamarabb el kell juttatni. Az Időzítéstől és az egyéb feltételektől függően előfordulhat, hogy a jelentés küldése továbbra is meghiúsul, például ha a HTTP-átjáró be van zárva, vagy az üzenet nem éri el az átjárót. Ha az azonnali beállítás hamis értékre van állítva, a rendszer a HTTP-átjáró állapot-ügyfélbeállítások alapján elküldi a jelentést. Ezért a HealthReportSendInterval-konfigurációnak megfelelően kötegbe kerül. Ez az ajánlott beállítás, mivel lehetővé teszi, hogy az állapot-ügyfél optimalizálja az állapot-jelentési üzeneteket az állapotfigyelő tárolóba, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a rendszer nem küldi el azonnal a jelentéseket. |
| --Remove-when-lejárt | Az érték, amely azt jelzi, hogy a jelentés törlődik-e a Health Store-ból, amikor lejár. <br><br> Ha az értéke TRUE (igaz), a rendszer eltávolítja a jelentést az állapot-áruházból a lejárat után. Ha hamis értékre van állítva, a jelentés a lejártkor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeresen jelentést küldenek, a Eltávolításlejáratkor false (alapértelmezett) értéket kell beállítania. Így a riporter problémákba ütközik (például holtpont), és nem tud jelentést készíteni, az entitást a rendszer hiba esetén kiértékeli, amikor az állapotjelentés lejár. Ez az entitás a hiba állapotának megfelelően jelenik meg. |
| --Sequence-Number | Az állapotjelentés sorszáma numerikus karakterláncként. <br><br> A jelentés sorszámát a Health Store használja az elavult jelentések észlelésére. Ha nincs megadva, a rendszer automatikusan létrehozza a sorszámot, amikor egy jelentés hozzáadása történik. |
| --Service-Kind | A szolgáltatási replika (állapot nélküli vagy állapot-nyilvántartó) típusa, amelyre az állapotot jelenteni kell. A következő lehetséges értékek: \: "állapot nélküli", "állapot-nyilvántartó".  Alapértelmezett \: állapot-nyilvántartó |
| --időtúllépés-t | Alapértelmezett \: 60. |
| --TTL | Az az időtartam, ameddig ez az állapotjelentés érvényes. Ez a mező ISO8601 formátumot használ az időtartam megadásához. <br><br> Amikor az ügyfelek rendszeresen jelentést küldenek, a jelentéseknek az élettartamuk során nagyobb gyakorisággal kell elküldeniük a jelentéseket. Ha az ügyfelek áttérnek a váltásra, beállíthatja, hogy az idő a végtelen értékre legyen állítva. Ha a lejárati idő lejár, az állapottal kapcsolatos információkat tartalmazó állapotot a rendszer eltávolítja az állapotfigyelő tárolóból, ha a Eltávolításlejáratkor értéke TRUE (igaz), vagy hiba esetén kiértékelt, ha a Eltávolításlejáratkor hamis. Ha nincs megadva, a rendszer az alapértelmezett élettartamot a végtelen értékre adja. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-replica-restart"></a>sfctl-replika újraindítása
Újraindítja egy csomóponton futó megőrzött szolgáltatás szolgáltatás-replikáját.

Újraindítja egy csomóponton futó megőrzött szolgáltatás szolgáltatás-replikáját. Figyelmeztetés – az API használatakor nem végeznek biztonsági ellenőrzéseket. Az API helytelen használata az állapot-nyilvántartó szolgáltatások rendelkezésre állásának elvesztéséhez vezethet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --replika-azonosító [kötelező] | A replika azonosítója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
