---
title: Azure Service Fabric CLI – sfctl alkalmazás
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. Az alkalmazások kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 880770345eb7d65850db322bd97d64c60b6681ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260871"
---
# <a name="sfctl-application"></a>sfctl-alkalmazás
Alkalmazások és alkalmazások típusainak létrehozása, törlése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| létrehozás | Létrehoz egy Service Fabric alkalmazást a megadott leírás használatával. |
| delete | Töröl egy meglévő Service Fabric alkalmazást. |
| telepített | Egy Service Fabric csomóponton telepített alkalmazás információinak beolvasása. |
| központilag telepített – állapot | Egy Service Fabric csomóponton telepített alkalmazás állapotával kapcsolatos információk beolvasása. |
| központilag telepített – lista | A Service Fabric csomóponton telepített alkalmazások listájának beolvasása. |
| állapotfigyelő | Lekéri a Service Fabric-alkalmazás állapotát. |
| információ | Beolvas egy Service Fabric alkalmazással kapcsolatos információkat. |
| list | A megadott szűrőknek megfelelő Service Fabric-fürtben létrehozott alkalmazások listájának beolvasása. |
| betöltés | Beolvas egy Service Fabric alkalmazás betöltési adatait. |
| manifest | Beolvassa az alkalmazás típusát leíró jegyzékfájlt. |
| rendelkezés | Kiépíti vagy regisztrálja Service Fabric alkalmazás típusát a fürtön a külső tárolóban található ". sfpkg" csomaggal, vagy a rendszerkép-tárolóban lévő alkalmazáscsomag használatával. |
| jelentés – állapot | Állapotjelentést küld a Service Fabric alkalmazásról. |
| típus | Beolvassa a Service Fabric-fürtben található, pontosan a megadott névvel egyező típusú alkalmazások listáját. |
| típus – lista | Beolvassa a Service Fabric-fürtben található Alkalmazásbeállítások listáját. |
| leépítése | Egy Service Fabric alkalmazás típusának eltávolítása vagy törlése a fürtből. |
| frissítés | Egy alkalmazás frissítésének megkezdése a Service Fabric fürtön. |
| frissítés – folytatás | Folytatja az alkalmazások frissítését a Service Fabric-fürtön. |
| frissítés – visszaállítás | Elindítja a Service Fabric fürtön futó alkalmazás jelenleg folyamatban lévő frissítését. |
| frissítés – állapot | Az alkalmazáson végrehajtott legújabb frissítés részleteinek beolvasása. |
| feltöltés | Service Fabric alkalmazáscsomag másolása a rendszerkép-tárolóba. |

## <a name="sfctl-application-create"></a>sfctl-alkalmazás létrehozása
Létrehoz egy Service Fabric alkalmazást a megadott leírás használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Name [kötelező] | Az alkalmazás neve, beleértve a "Fabric \: " URI-sémát. |
| --App-Type [kötelező] | Az alkalmazás-jegyzékfájlban található az alkalmazás típusa név. |
| --App-Version [kötelező] | Az alkalmazás jegyzékfájlban definiált verziója. |
| – Max csomópontok száma | Azon csomópontok maximális száma, amelyeken Service Fabric az alkalmazás kapacitása le lesz foglalva. Ez nem jelenti azt, hogy az alkalmazás szolgáltatásai az összes csomópontra lesznek helyezve. |
| --mérőszámok | Az alkalmazás kapacitása metrika leírásának JSON-kódolású listája. A metrika neveként van definiálva, amely az alkalmazás által használt egyes csomópontok készletéhez van társítva. |
| --min-csomópontok száma | Azon csomópontok minimális száma, ahol a Service Fabric az alkalmazás kapacitása le lesz foglalva. Ez nem jelenti azt, hogy az alkalmazás szolgáltatásai az összes csomópontra lesznek helyezve. |
| – paraméterek | Az alkalmazás létrehozásakor alkalmazandó, az alkalmazás paramétereinek felülbírálására szolgáló JSON-kódolású lista. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-delete"></a>sfctl-alkalmazás törlése
Töröl egy meglévő Service Fabric alkalmazást.

A törlés előtt létre kell hozni egy alkalmazást. Egy alkalmazás törlésével törlődik az adott alkalmazás részét képező összes szolgáltatás. Alapértelmezés szerint a Service Fabric a szolgáltatás-replikákat egy kecses módon fogja megpróbálni, majd törölni a szolgáltatást. Ha azonban egy szolgáltatás szabályosan zárja be a replikát, a törlési művelet hosszú időt vehet igénybe, vagy elakad. A választható ForceRemove jelzővel kihagyhatja a kecses bezárási sorozatot, és kényszerített módon törölheti az alkalmazást és annak összes szolgáltatását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
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

## <a name="sfctl-application-deployed"></a>telepített sfctl-alkalmazás
Egy Service Fabric csomóponton telepített alkalmazás információinak beolvasása.

Ez a lekérdezés a rendszeralkalmazási adatokat adja vissza, ha a megadott alkalmazás-azonosító a rendszeralkalmazáshoz tartozik. Az eredmények kiterjednek a telepített alkalmazások aktív, aktiválási és letöltési állapotára. Ehhez a lekérdezéshez a csomópont nevének a fürt egyik csomópontjának kell lennie. A lekérdezés meghiúsul, ha a megadott csomópont neve nem a fürtben lévő aktív Service Fabric csomópontokra mutat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --include-Health-State | Entitás állapotának belefoglalása. Ha ez a paraméter hamis vagy nincs megadva, akkor a visszaadott állapot "ismeretlen". Ha igaz értékre van állítva, a lekérdezés párhuzamosan fut a csomóponttal és az állapotfigyelő rendszer szolgáltatással az eredmények egyesítése előtt. Ennek eredményeképpen a lekérdezés drágább, és hosszabb időt is igénybe vehet. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-deployed-health"></a>sfctl-alkalmazás üzembe helyezése – állapot
Egy Service Fabric csomóponton telepített alkalmazás állapotával kapcsolatos információk beolvasása.

Egy Service Fabric csomóponton telepített alkalmazás állapotával kapcsolatos információk beolvasása. A EventsHealthStateFilter használatával opcionálisan szűrheti a központilag telepített alkalmazáson jelentett HealthEvent-objektumok gyűjtését az állapot alapján. A DeployedServicePackagesHealthStateFilter használatával opcionálisan szűrheti a DeployedServicePackageHealth gyermekeket az állapot alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --Node-Name [kötelező] | A csomópont neve. |
| --telepített-Service-Packages-Health-State-Filter | Lehetővé teszi a telepített szervizcsomag állapot-objektumainak szűrését az állapotuk alapján telepített alkalmazás állapotának lekérdezése miatt. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. A rendszer csak a szűrőnek megfelelő üzembe helyezett csomagokat adja vissza. Az összes telepített szervizcsomag használatával kiértékelheti az üzembe helyezett alkalmazás összesített állapotát. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az OK (2) és a figyelmeztetés (4) HealthState-értékkel rendelkező szolgáltatási csomagok állapotát adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --kizárás-Health-Statistics | Azt jelzi, hogy az állapot statisztikáját a lekérdezés eredményének részeként kell-e visszaadni. Alapértelmezés szerint hamis. A statisztikákban a gyermek entitások számának állapota ok, figyelmeztetés és hiba látható. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-deployed-list"></a>sfctl-alkalmazás üzembe helyezése – lista
A Service Fabric csomóponton telepített alkalmazások listájának beolvasása.

A Service Fabric csomóponton telepített alkalmazások listájának beolvasása. Az eredmények nem tartalmazzák a telepített rendszeralkalmazások adatait, kivéve, ha az azonosító alapján explicit módon lekérdezték. Az eredmények kiterjednek a telepített alkalmazások aktív, aktiválási és letöltési állapotára. Ehhez a lekérdezéshez a csomópont nevének a fürt egyik csomópontjának kell lennie. A lekérdezés meghiúsul, ha a megadott csomópont neve nem a fürtben lévő aktív Service Fabric csomópontokra mutat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --include-Health-State | Entitás állapotának belefoglalása. Ha ez a paraméter hamis vagy nincs megadva, akkor a visszaadott állapot "ismeretlen". Ha igaz értékre van állítva, a lekérdezés párhuzamosan fut a csomóponttal és az állapotfigyelő rendszer szolgáltatással az eredmények egyesítése előtt. Ennek eredményeképpen a lekérdezés drágább, és hosszabb időt is igénybe vehet. |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-health"></a>sfctl alkalmazás állapota
Lekéri a Service Fabric-alkalmazás állapotát.

A Service Fabric-alkalmazás egészségügyi állapotát adja vissza. A válasz az OK, a hiba vagy a figyelmeztetés állapotot jelenti. Ha az entitás nem található az állapotfigyelő tárolóban, hibaüzenetet ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --telepített-alkalmazások-állapot-állapot-szűrő | Lehetővé teszi az alkalmazás állapota lekérdezés eredményében visszaadott központilag telepített alkalmazások állapotának szűrését az állapotuk alapján. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. A rendszer csak a szűrőnek megfelelő telepített alkalmazásokat adja vissza. Az összes telepített alkalmazás az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az OK (2) és a figyelmeztetés (4) HealthState értékkel rendelkező telepített alkalmazások állapotát adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --kizárás-Health-Statistics | Azt jelzi, hogy az állapot statisztikáját a lekérdezés eredményének részeként kell-e visszaadni. Alapértelmezés szerint hamis. A statisztikákban a gyermek entitások számának állapota ok, figyelmeztetés és hiba látható. |
| --szolgáltatások – állapot – szűrő | A szolgáltatások állapota lekérdezés eredményében visszaadott szolgáltatások állapotának szűrését teszi lehetővé állapotuk alapján. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. A rendszer csak a szűrőnek megfelelő szolgáltatásokat adja vissza. Minden szolgáltatás az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a HealthState értékkel rendelkező szolgáltatások állapota (2), a figyelmeztetés (4) pedig vissza fog esni.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-info"></a>sfctl
Beolvas egy Service Fabric alkalmazással kapcsolatos információkat.

A létrehozott vagy a Service Fabric-fürtben létrehozott alkalmazással kapcsolatos adatokat adja vissza, amelyek neve megegyezik a paraméterrel megadott névvel. A válasz tartalmazza az alkalmazás nevét, típusát, állapotát, paramétereit és egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --kizárás-Application-Parameters | A jelző, amely megadja, hogy az alkalmazás paramétereinek ki lesznek-e zárva az eredményből. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-list"></a>sfctl-alkalmazások listája
A megadott szűrőknek megfelelő Service Fabric-fürtben létrehozott alkalmazások listájának beolvasása.

Beolvassa a létrehozott vagy a Service Fabric-fürtben létrehozott alkalmazások adatait, és megegyeznek a megadott szűrőkkel. A válasz tartalmazza az alkalmazás nevét, típusát, állapotát, paramétereit és egyéb részleteit. Ha az alkalmazások nem férnek el egy oldalon, a rendszer az eredmények egyik oldalát adja vissza, valamint egy folytatási tokent is, amely a következő oldal beolvasására használható. A szűrők ApplicationTypeName és ApplicationDefinitionKindFilter nem adhatók meg egyszerre.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --alkalmazás-definíció-Kind-Filter | A ApplicationDefinitionKind szűrésére szolgál, amely egy Service Fabric alkalmazás definiálásához használt mechanizmus.  <br> – Alapértelmezett – az alapértelmezett érték, amely ugyanazt a funkciót hajtja végre, mint az "összes" lehetőséget. Az érték 0.  <br> – Minden olyan szűrő, amely megfelel bármely ApplicationDefinitionKind értéknek. Az érték 65535.  <br> -ServiceFabricApplicationDescription – a ApplicationDefinitionKind érték ServiceFabricApplicationDescription rendelkező bemenettel egyező szűrő. Az érték 1.  <br> -Levélírás – a ApplicationDefinitionKind értékkel megegyező bemeneti szűrő. Az érték 2. |
| --Application-Type-Name | Az alkalmazásnak a lekérdezéshez való szűréséhez használt alkalmazásnév neve. Ez az érték nem tartalmazhatja az alkalmazás típusának verzióját. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --kizárás-Application-Parameters | A jelző, amely megadja, hogy az alkalmazás paramétereinek ki lesznek-e zárva az eredményből. |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-load"></a>sfctl alkalmazás betöltése
Beolvas egy Service Fabric alkalmazás betöltési adatait.

A létrehozott vagy a Service Fabric-fürtben létrehozott alkalmazás betöltési információit adja vissza, amelyek neve megegyezik a paraméterrel megadott névvel. A válasz tartalmazza a nevet, a minimális csomópontokat, a maximális csomópontokat, az alkalmazás által jelenleg foglalt csomópontok számát, valamint az alkalmazás terhelési metrikájának adatait.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-manifest"></a>sfctl-alkalmazás jegyzékfájlja
Beolvassa az alkalmazás típusát leíró jegyzékfájlt.

A válasz karakterláncként tartalmazza az alkalmazás jegyzékfájljának XML-fájlját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-Type-Name [kötelező] | Az alkalmazás típusának neve. |
| --Application-Type-Version [kötelező] | Az alkalmazás típusának verziója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-provision"></a>sfctl alkalmazás kiépítése
Kiépíti vagy regisztrálja Service Fabric alkalmazás típusát a fürtön a külső tárolóban található ". sfpkg" csomaggal, vagy a rendszerkép-tárolóban lévő alkalmazáscsomag használatával.

Egy Service Fabric alkalmazás típusának kiosztása a fürttel. A kiépítés szükséges ahhoz, hogy új alkalmazásokat lehessen létrehozni. A kiépítési művelet végrehajtható a relativePathInImageStore által megadott alkalmazáscsomag vagy a külső ". sfpkg" URI-ja használatával. Hacsak a-External-kiépítés be van állítva, ez a parancs a rendszerkép-tároló kiépítését fogja várni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --alkalmazás-csomag-Letöltés-URI | Azon ". sfpkg" alkalmazáscsomag elérési útja, ahonnan az alkalmazáscsomag HTTP vagy HTTPS protokoll használatával tölthető le. <br><br> Csak a külső tároló üzembe helyezéséhez. Az alkalmazáscsomag tárolható egy külső tárolóban is, amely lekéréses műveletet biztosít a fájl letöltéséhez. A támogatott protokollok a HTTP és a HTTPS, és az elérési útnak engedélyeznie kell az OLVASÁSI hozzáférést. |
| --Application-Type-Build-Path | Csak a rendszerképek tárolásához. Az alkalmazáscsomag relatív elérési útja az előző feltöltési művelet során megadott rendszerkép-tárolóban. |
| --Application-Type-Name | Csak a külső tároló üzembe helyezéséhez. Az alkalmazás típusának neve az alkalmazás jegyzékfájljában található alkalmazás típusának neve. |
| --alkalmazás-típus-verzió | Csak a külső tároló üzembe helyezéséhez. Az alkalmazás típusának verziója az alkalmazás jegyzékfájljában található alkalmazás típusú verziót jelöli. |
| – külső – kiépítés | Az a hely, ahonnan az alkalmazáscsomag regisztrálható vagy üzembe helyezhető. Azt jelzi, hogy a kiépítés olyan alkalmazáscsomag esetében, amelyet korábban egy külső tárolóba töltöttek fel. Az alkalmazáscsomag a *. sfpkg kiterjesztéssel végződik. |
| --No-WAIT | Azt jelzi, hogy aszinkron módon történjen-e a kiépítés. <br><br> Ha igaz értékre van állítva, a kiépítési művelet visszaadja, ha a rendszer elfogadja a kérést, és a létesítési művelet időkorlát nélkül folytatódik. Az alapértelmezett érték a hamis. Nagyméretű alkalmazáscsomag esetén azt javasoljuk, hogy az értéket állítsa igaz értékre. |
| --időtúllépés-t | Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-report-health"></a>sfctl alkalmazás jelentése – állapot
Állapotjelentést küld a Service Fabric alkalmazásról.

A megadott Service Fabric alkalmazás állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentést és a jelentést tartalmazó tulajdonság forrásával kapcsolatos információkat. A rendszer elküldi a jelentést egy Service Fabric Gateway-alkalmazásnak, amely továbbítja az állapot-áruháznak. Előfordulhat, hogy a jelentést az átjáró fogadja el, de a további ellenőrzés után a Health Store elutasította. Az állapotfigyelő például elutasítja a jelentést egy Érvénytelen paraméter miatt, például egy elavult sorszámot. Ha szeretné megtekinteni, hogy a jelentés alkalmazva lett-e az állapotfigyelő tárolóban, kérje az alkalmazás állapotát, és ellenőrizze, hogy megjelenik-e a jelentés

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziókban a 6.0 + és a "SajátPr/App1" értéknél. |
| --Health-Property [kötelező] | Az állapotadatok tulajdonsága. <br><br> Az entitások különböző tulajdonságokhoz tartozhatnak állapotjelentést. A tulajdonság egy karakterlánc, nem pedig rögzített enumerálás, amely lehetővé teszi, hogy a jelentéskészítő rugalmasan kategorizálja a jelentést kiváltó állapot feltételeit. A "LocalWatchdog" SourceId forrásazonosító rendelkező jelentéskészítő például nyomon követheti a csomópontok rendelkezésre álló lemezének állapotát, így az adott csomópont "AvailableDisk" tulajdonságát is jelentheti. Ugyanaz a riporter figyelheti a csomópontok kapcsolatát, így a "kapcsolat" tulajdonságot is jelentheti ugyanazon a csomóponton. Az állapotfigyelő szolgáltatásban ezek a jelentések különálló állapotadatokként lesznek kezelve a megadott csomópont esetében. A SourceId forrásazonosító együtt a tulajdonság egyedileg azonosítja az állapotadatok adatait. |
| --állapotadatok [kötelező] | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "OK", "figyelmeztetés", "hiba", "ismeretlen". |
| – forrás-azonosító [kötelező] | Az állapottal kapcsolatos adatokat létrehozó ügyfél/watchdog/rendszer összetevőt azonosító forrás neve. |
| – Leírás | Az állapotadatok leírása. <br><br> A jelentésből származó, emberi olvasásra alkalmas adatok hozzáadására szolgáló szabad szöveget jelöli. A Leírás maximális hossza 4096 karakter. Ha a megadott karakterlánc már nem érhető el, a rendszer automatikusan csonkolja. A csonkítás során a Leírás utolsó karakterei a "[csonkolt]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi, hogy a felhasználók csonkítva lettek. Vegye figyelembe, hogy a csonkítás során a Leírás kevesebb, mint 4096 karakterből áll az eredeti sztringből. |
| – azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e juttatni. <br><br> Egy állapotjelentés érkezik egy Service Fabric Gateway-alkalmazásba, amely továbbítja az állapot-áruháznak. Ha az azonnali beállítás értéke TRUE (igaz), a rendszer azonnal elküldi a jelentést a HTTP-átjáróról az állapotfigyelő tárolóba, függetlenül a HTTP-átjáró alkalmazás által használt háló-ügyfél beállításaitól. Ez olyan kritikus fontosságú jelentések esetében hasznos, amelyeket a lehető leghamarabb el kell juttatni. Az Időzítéstől és az egyéb feltételektől függően előfordulhat, hogy a jelentés küldése továbbra is meghiúsul, például ha a HTTP-átjáró be van zárva, vagy az üzenet nem éri el az átjárót. Ha az azonnali beállítás hamis értékre van állítva, a rendszer a HTTP-átjáró állapot-ügyfélbeállítások alapján elküldi a jelentést. Ezért a HealthReportSendInterval-konfigurációnak megfelelően kötegbe kerül. Ez az ajánlott beállítás, mivel lehetővé teszi, hogy az állapot-ügyfél optimalizálja az állapot-jelentési üzeneteket az állapotfigyelő tárolóba, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a rendszer nem küldi el azonnal a jelentéseket. |
| --Remove-when-lejárt | Az érték, amely azt jelzi, hogy a jelentés törlődik-e a Health Store-ból, amikor lejár. <br><br> Ha az értéke TRUE (igaz), a rendszer eltávolítja a jelentést az állapot-áruházból a lejárat után. Ha hamis értékre van állítva, a jelentés a lejártkor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeresen jelentést küldenek, a Eltávolításlejáratkor false (alapértelmezett) értéket kell beállítania. Így a riporter problémákba ütközik (például holtpont), és nem tud jelentést készíteni, az entitást a rendszer hiba esetén kiértékeli, amikor az állapotjelentés lejár. Ez az entitás a hiba állapotának megfelelően jelenik meg. |
| --Sequence-Number | Az állapotjelentés sorszáma numerikus karakterláncként. <br><br> A jelentés sorszámát a Health Store használja az elavult jelentések észlelésére. Ha nincs megadva, a rendszer automatikusan létrehozza a sorszámot, amikor egy jelentés hozzáadása történik. |
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

## <a name="sfctl-application-type"></a>sfctl-alkalmazás típusa
Beolvassa a Service Fabric-fürtben található, pontosan a megadott névvel egyező típusú alkalmazások listáját.

A kiépített vagy a Service Fabric fürtben kiépített alkalmazási típusok adatait adja vissza. Ezek az eredmények olyan típusú alkalmazások, amelyek neve pontosan egyezik a paraméterként megadott névvel, és amelyek megfelelnek a megadott lekérdezési paramétereknek. Az alkalmazás típusának nevével egyező összes verziót adja vissza a rendszer, és mindegyik verzió egyetlen alkalmazás típusúként lett visszaadva. A válasz tartalmazza az alkalmazás típusának nevét, verziószámát, állapotát és egyéb részleteit. Ez egy lapozható lekérdezés, amely azt jelenti, hogy ha nem minden alkalmazás-típus fér el egy oldalon, a rendszer az eredmények egyik oldalát adja vissza, valamint egy folytatási tokent, amely a következő oldal beolvasására használható. Ha például 10 alkalmazás típusa van, de egy oldal csak az első három alkalmazás típushoz fér hozzá, vagy ha a max results értéke 3, akkor a rendszer három értéket ad vissza. A többi eredmény eléréséhez a következő lekérdezésben a visszaadott folytatási token használatával kérje le a következő lapokat. Üres folytatási tokent ad vissza, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-Type-Name [kötelező] | Az alkalmazás típusának neve. |
| --alkalmazás-típus-verzió | Az alkalmazás típusának verziója. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --kizárás-Application-Parameters | A jelző, amely megadja, hogy az alkalmazás paramétereinek ki lesznek-e zárva az eredményből. |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-type-list"></a>sfctl-alkalmazás típusa – lista
Beolvassa a Service Fabric-fürtben található Alkalmazásbeállítások listáját.

A kiépített vagy a Service Fabric fürtben kiépített alkalmazási típusok adatait adja vissza. Egy alkalmazás típusának mindegyik verzióját egyetlen alkalmazás típusúként adja vissza a rendszer. A válasz tartalmazza az alkalmazás típusának nevét, verziószámát, állapotát és egyéb részleteit. Ez egy lapozható lekérdezés, amely azt jelenti, hogy ha nem minden alkalmazás-típus fér el egy oldalon, a rendszer az eredmények egyik oldalát adja vissza, valamint egy folytatási tokent, amely a következő oldal beolvasására használható. Ha például 10 alkalmazás típusa van, de egy oldal csak az első három alkalmazás típushoz fér hozzá, vagy ha a max results értéke 3, akkor a rendszer három értéket ad vissza. A többi eredmény eléréséhez a következő lekérdezésben a visszaadott folytatási token használatával kérje le a következő lapokat. Üres folytatási tokent ad vissza, ha nincsenek további lapok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-Type-Definition-Kind-Filter | A ApplicationTypeDefinitionKind szűrésére szolgál, amely egy Service Fabric alkalmazás típusának definiálására szolgáló mechanizmus.  <br> – Alapértelmezett – az alapértelmezett érték, amely ugyanazt a funkciót hajtja végre, mint az "összes" lehetőséget. Az érték 0.  <br> – Minden olyan szűrő, amely megfelel bármely ApplicationTypeDefinitionKind értéknek. Az érték 65535.  <br> -ServiceFabricApplicationPackage – a ApplicationTypeDefinitionKind érték ServiceFabricApplicationPackage rendelkező bemenettel egyező szűrő. Az érték 1.  <br> -Levélírás – a ApplicationTypeDefinitionKind értékkel megegyező bemeneti szűrő. Az érték 2. |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --kizárás-Application-Parameters | A jelző, amely megadja, hogy az alkalmazás paramétereinek ki lesznek-e zárva az eredményből. |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-unprovision"></a>sfctl-alkalmazás kiépítése
Egy Service Fabric alkalmazás típusának eltávolítása vagy törlése a fürtből.

Ez a művelet csak akkor hajtható végre, ha az alkalmazás összes példánya törölve lett. Ha az alkalmazás típusa nincs regisztrálva, az adott alkalmazás típusához nem hozhatók létre új alkalmazás-példányok.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-Type-Name [kötelező] | Az alkalmazás típusának neve. |
| --Application-Type-Version [kötelező] | Az alkalmazás jegyzékfájlban definiált verziója. |
| --aszinkron-paraméter | Az a jelző, amely azt jelzi, hogy a kiépítés megszüntetése aszinkron módon történik-e. Ha igaz értékre van állítva, a kiépítési művelet visszaadja, ha a rendszer elfogadja a kérést, és a kiépítés művelet időkorlát nélkül folytatódik. Az alapértelmezett érték a hamis. Azt javasoljuk azonban, hogy igaz értékre állítsa a nagyméretű, kiépített alkalmazás-csomagokat. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade"></a>sfctl-alkalmazás frissítése
Egy alkalmazás frissítésének megkezdése a Service Fabric fürtön.

Ellenőrzi a megadott alkalmazás-frissítési paramétereket, és megkezdi az alkalmazás frissítését, ha a paraméterek érvényesek. Vegye figyelembe, hogy a frissítés leírása lecseréli a meglévő alkalmazás leírását. Ez azt jelenti, hogy ha a paraméterek nincsenek megadva, a rendszer felülírja az alkalmazások meglévő paramétereit az üres paraméterek listával. Ez azt eredményezi, hogy az alkalmazás a paraméterek alapértelmezett értékét használja az alkalmazás jegyzékfájljában.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. <br><br> Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --alkalmazás-verzió [kötelező] | Az alkalmazás frissítéséhez használt célalkalmazás-típus verziója (az alkalmazás jegyzékfájljában található). |
| – paraméterek [kötelező] | Az alkalmazás frissítésekor alkalmazandó felülbírálások JSON-kódolású listája. |
| --default-Service-Health-Policy | Az alapértelmezés szerint a szolgáltatástípus állapotának kiértékeléséhez használt állapotfigyelő JSON-kódolású specifikációja. |
| – hiba – művelet | Az a művelet, amelyet akkor kell végrehajtani, ha a figyelt frissítés figyelési házirendet vagy állapotházirend megsértését tapasztalja. |
| --kényszerített újraindítás | A folyamatok kényszerített újraindítása a frissítés során, még akkor is, ha a kód verziószáma nem változott. |
| --állapot-ellenőrzési-újrapróbálkozás-időtúllépés | Az állapot-ellenőrzések elvégzésére irányuló kísérletek közötti időtartam, ha az alkalmazás vagy a fürt állapota nem kifogástalan.  Alapértelmezett \: PT0H10M0S. |
| --állapot-ellenőrzési-stabil-időtartam | Az az időtartam, ameddig az alkalmazásnak vagy a fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományba kerül.  Alapértelmezett \: PT0H2M0S. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --állapot-ellenőrzési várakozás időtartama | Az a várakozási idő, ameddig a frissítési tartomány befejezése után meg kell várni az állapot-ellenőrzési folyamat megkezdése előtt.  Alapértelmezett \: 0. |
| --Max-sérült-alkalmazások | A nem kifogástalan állapotú központilag telepített alkalmazások megengedett százalékos aránya. 0 és 100 közötti számként jelenik meg. |
| – üzemmód | A működés közbeni frissítés során az állapot figyelésére szolgáló mód.  Alapértelmezett \: UnmonitoredAuto. |
| --replika-set-pipa-időtúllépés | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megelőzésére szolgáló maximális időtartam, ha váratlan problémák merülnek fel. Másodpercben mérve. |
| --szolgáltatás-állapot-házirend | A JSON-kódolású leképezés a szolgáltatástípus-állapotra vonatkozó házirend-típus alapján. Alapértelmezés szerint a Térkép üres. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| – frissítés – tartomány – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a FailureAction végrehajtása előtt kell végrehajtani.  Alapértelmezett \: p10675199dt02h48m 05.4775807 s. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| – frissítés – időtúllépés | Az a időtartam, ameddig a teljes frissítésnek a FailureAction végrehajtása előtt el kell végeznie.  Alapértelmezett \: p10675199dt02h48m 05.4775807 s. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --Figyelmeztetés – hiba | Azt jelzi, hogy a figyelmeztetések a hibákkal azonos súlyossággal vannak-e kezelve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl-alkalmazás frissítése – folytatás
Folytatja az alkalmazások frissítését a Service Fabric-fürtön.

A nem figyelt manuális Service Fabric az alkalmazás frissítését folytatja. Service Fabric egyszerre egy frissítési tartományt frissít. A nem figyelt manuális frissítésekhez, miután Service Fabric befejezte a frissítési tartományt, megvárja, hogy hívja meg ezt az API-t, mielőtt továbblép a következő frissítési tartományra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --upgrade-domain-name [kötelező] | Annak a frissítési tartománynak a neve, amelyben a frissítés folytatása folyamatban van. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl-alkalmazás frissítése – visszaállítás
Elindítja a Service Fabric fürtön futó alkalmazás jelenleg folyamatban lévő frissítését.

Elindítja az aktuális alkalmazás frissítését az előző verzióra. Ez az API csak az új verzióra továbbítandó aktuális folyamatban lévő frissítés visszaállítására használható. Ha az alkalmazás jelenleg nem frissül, használja a StartApplicationUpgrade API-t a kívánt verzióra való frissítéshez, beleértve az előző verzióra történő visszaállítást is.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upgrade-status"></a>sfctl-alkalmazás frissítése – állapot
Az alkalmazáson végrehajtott legújabb frissítés részleteinek beolvasása.

Információt ad vissza az alkalmazás legújabb frissítésének állapotáról, valamint az alkalmazás állapotával kapcsolatos hibák elhárítását segítő részletekről.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Application-ID [kötelező] | Az alkalmazás identitása. Ez általában az alkalmazás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például az alkalmazás neve "Fabric \: /MyApp/App1", az alkalmazás identitása "SajátPr App1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-application-upload"></a>sfctl alkalmazás feltöltése
Service Fabric alkalmazáscsomag másolása a rendszerkép-tárolóba.

A csomag összes fájljának feltöltési folyamata opcionálisan megjeleníthető. A feltöltési folyamat el lesz küldve `stderr` .

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| – elérési út [kötelező] | Helyi alkalmazáscsomag elérési útja. |
| – tömörítés | Csak Service Fabric alkalmazás-csomagokra érvényes. Hozzon létre egy új mappát, amely tartalmazza a tömörített alkalmazáscsomag vagy az alapértelmezett helyet, vagy a tömörített hely paraméter által megadott helyet, majd töltse fel az újonnan létrehozott mappát. <br><br> Ha már van olyan tömörített fájl, amelyet a sfctl hozott létre, a rendszer felülírja, ha ez a jelző be van állítva. Ha a könyvtár nem alkalmazáscsomag, a rendszer hibaüzenetet küld. Ha már tömörített alkalmazáscsomag van, a mappa a következőképpen lesz átmásolva:. Alapértelmezés szerint az újonnan létrehozott tömörített alkalmazáscsomag törlése sikeres feltöltés után megtörténik. Ha a feltöltés nem sikerült, manuálisan törölje a tömörített csomagot szükség szerint. A törlés nem távolít el üres dirs, amelyek akkor hozhatók létre, ha a tömörített Location paraméter nem létező címtárakra hivatkozik. |
| --tömörített – hely | A tömörített alkalmazáscsomag üzembe helyezésének helye. <br><br> Ha nincs megadva hely, a tömörített csomag egy sfctl_compressed_temp nevű újonnan létrehozott mappába kerül, amely a Path argumentumban megadott szülő könyvtár alatt található. Ha például a Path argumentum értéke C \: /FolderA/AppPkg, akkor a tömörített csomag hozzá lesz adva a c \: /Foldera/sfctl_compressed_temp/apppkg. |
| --lemezképtárolóba-string | A cél rendszerkép-tároló az alkalmazáscsomag feltöltéséhez.  Alapértelmezett \: háló \: lemezképtárolóba. <br><br> A fájl helyére való feltöltéshez indítsa el ezt a paramétert a "file" értékkel \: . Ellenkező esetben az értéknek a rendszerkép-tárolóhoz tartozó kapcsolatok karakterláncának kell lennie, például az alapértelmezett értéknek. |
| --Keep-Compressed | Azt határozza meg, hogy a létrehozott tömörített csomag sikeresen feltölthető-e a feltöltés befejezésére. <br><br> Ha nincs beállítva, akkor a sikeres befejezés után a rendszer törli a tömörített alkalmazáscsomag-csomagokat. Ha a feltöltés sikertelen volt, akkor az alkalmazáscsomag mindig a kimeneti könyvtárban marad a feltöltéshez. |
| --show-Progress | Nagyméretű csomagok esetén a fájlfeltöltés előrehaladásának megjelenítése. |
| --időtúllépés-t | A teljes időtúllépés másodpercben. A feltöltés sikertelen lesz, és a feltöltés időtúllépési időtartamának lejárta után hibaüzenetet ad vissza. Ez az időkorlát a teljes alkalmazáscsomag érvényes, és az egyes fájlok időtúllépése a hátralévő időtúllépési időtartammal egyenlő lesz. Az időtúllépés nem tartalmazza az alkalmazáscsomag tömörítéséhez szükséges időt.  Alapértelmezett \: 300. |

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
