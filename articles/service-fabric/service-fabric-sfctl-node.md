---
title: Azure Service Fabric CLI – sfctl csomópont
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A fürtcsomópontok kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f2cf1011db37892f71bdd06f409cec1a76463507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257164"
---
# <a name="sfctl-node"></a>sfctl-csomópont
A fürtöt alkotó csomópontok kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| Add-Configuration-paraméter-felülbírálások | Hozzáadja a konfigurációs felülbírálások listáját a megadott csomóponton. |
| Megbénít | Egy Service Fabric fürtcsomópont inaktiválása a megadott inaktiválási szándékkal. |
| engedélyezése | A jelenleg inaktivált Service Fabric fürtcsomópont aktiválása. |
| beállítások beolvasása – felülbírálások | Lekéri a konfigurációs felülbírálások listáját a megadott csomóponton. |
| állapotfigyelő | Egy Service Fabric csomópont állapotának beolvasása. |
| információ | Lekérdezi a Service Fabric fürt egy adott csomópontjának adatait. |
| list | Lekéri a Service Fabric fürt csomópontjainak listáját. |
| betöltés | Lekéri egy Service Fabric csomópont betöltési adatait. |
| konfiguráció eltávolítása – felülbírálások | Eltávolítja a konfigurációs felülbírálásokat a megadott csomóponton. |
| állapot eltávolítása | A Service Fabric értesíti arról, hogy a csomóponton megőrzött állapot véglegesen el lett távolítva vagy elveszett. |
| jelentés – állapot | Állapotjelentés küldése a Service Fabric csomóponton. |
| restart | Újraindít egy Service Fabric fürtcsomópont-csomópontot. |
| átmenet | Elindít vagy leállít egy fürtcsomópont-csomópontot. |
| átmenet – állapot | A StartNodeTransition használatával megkezdett művelet előrehaladásának beolvasása. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>sfctl Node Add-Configuration-paraméter-felülbírálások
Hozzáadja a konfigurációs felülbírálások listáját a megadott csomóponton.

Ez az API lehetővé teszi az összes meglévő konfigurációs felülbírálás hozzáadását a megadott csomóponton.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --config-paraméter-felülbírálás-lista [kötelező] | Leírás a konfigurációs felülbírálások listájának hozzáadásához. |
| --Node-Name [kötelező] | A csomópont neve. |
| – kényszerített | Konfiguráció felülbírálásának kényszerítése a megadott csomópontokon. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-disable"></a>sfctl csomópont letiltása
Egy Service Fabric fürtcsomópont inaktiválása a megadott inaktiválási szándékkal.

Egy Service Fabric fürtcsomópont inaktiválása a megadott inaktiválási szándékkal. Ha az Inaktiválás folyamatban van, az inaktiválási szándék növelhető, de nem csökkenthető (például egy felfüggesztett leképezéssel inaktivált csomópont inaktiválható az újraindítással, de nem fordítva. A csomópontok inaktiválása után bármikor újraaktiválható a csomópont aktiválása művelettel. Ha az Inaktiválás nem fejeződött be, a művelet megszakítja az inaktiválást. A leállási és a biztonsági mentést elindító csomópontot továbbra is újra kell aktiválni, mielőtt a szolgáltatások el lesznek helyezve a csomópontra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --inaktiválás-szándék | A csomópont inaktiválásának szándékát vagy okát ismerteti. A lehetséges értékek a következők. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-enable"></a>sfctl csomópont engedélyezése
A jelenleg inaktivált Service Fabric fürtcsomópont aktiválása.

Aktiválja a jelenleg inaktivált Service Fabric fürtcsomópont-csomópontot. Az aktiválást követően a csomópont ismét életképes célpont lesz az új replikák elhelyezéséhez, és a csomóponton maradó deaktivált replikák újra lesznek aktiválva.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-get-configuration-overrides"></a>sfctl-csomópont lekérése – konfiguráció – felülbírálások
Lekéri a konfigurációs felülbírálások listáját a megadott csomóponton.

Ez az API lehetővé teszi az összes meglévő konfiguráció felülbírálásának lekérését a megadott csomóponton.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-health"></a>sfctl-csomópont állapota
Egy Service Fabric csomópont állapotának beolvasása.

Egy Service Fabric csomópont állapotának beolvasása. A EventsHealthStateFilter használatával szűrheti a csomóponton jelentett állapotú események gyűjteményét az állapot alapján. Ha a név alapján megadott csomópont nem létezik az állapotfigyelő tárolóban, ez egy hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-info"></a>sfctl-csomópont adatai
Lekérdezi a Service Fabric fürt egy adott csomópontjának adatait.

A válasz tartalmazza a csomópont nevét, állapotát, AZONOSÍTÓját, állapotát, üzemidőét és egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-list"></a>sfctl csomópontok listája
Lekéri a Service Fabric fürt csomópontjainak listáját.

A válasz tartalmazza a csomópontok nevét, állapotát, AZONOSÍTÓját, állapotát, üzemidőét és egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --Node-status-Filter | Lehetővé teszi a csomópontok szűrését a NodeStatus alapján. A rendszer csak azokat a csomópontokat adja vissza, amelyek megfelelnek a megadott szűrő értékének. A szűrő értéke a következők egyike lehet.  Alapértelmezett \: alapértelmezett érték. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-load"></a>sfctl-csomópont terhelése
Lekéri egy Service Fabric csomópont betöltési adatait.

Lekérdezi egy Service Fabric csomópontjának betöltési adatait az összes olyan metrika esetében, amelyhez be van állítva a terhelés vagy a kapacitás.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-remove-configuration-overrides"></a>sfctl csomópont eltávolítása – konfiguráció – felülbírálások
Eltávolítja a konfigurációs felülbírálásokat a megadott csomóponton.

Ez az API lehetővé teszi az összes meglévő konfiguráció felülbírálásának eltávolítását a megadott csomóponton.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-remove-state"></a>sfctl csomópont eltávolítása – állapot
A Service Fabric értesíti arról, hogy a csomóponton megőrzött állapot véglegesen el lett távolítva vagy elveszett.

Ez azt jelenti, hogy a csomópont megőrzött állapotát nem lehet helyreállítani. Ez általában akkor fordul elő, ha a merevlemez törölve lett tiszta, vagy ha a lemez összeomlik. A művelet sikeres végrehajtásához a csomópontot le kell állítani. Ez a művelet lehetővé teszi, hogy Service Fabric tudja, hogy a csomóponton lévő replikák már nem léteznek, és hogy a Service Fabric ne várja meg, amíg ezek a replikák biztonsági mentést készítenek. Ne futtassa ezt a parancsmagot, ha a csomóponton lévő állapot nem lett eltávolítva, és a csomópont érintetlen állapotba kerülhet. Ha az API-t a magok csomópontjain szeretné használni, Service Fabric 6,5-től kezdődően, módosítsa a magok csomópontjait a normál (nem magok) csomópontokra, majd indítsa el ezt az API-t a csomópont állapotának eltávolításához. Ha a fürt az Azure-on fut, a vetőmag-csomópont leállása után a Service Fabric automatikusan megpróbálja módosítani a nem magot tartalmazó csomópontot. Ennek elvégzéséhez győződjön meg arról, hogy az elsődleges csomópont típusában lévő nem Seed csomópontok száma nem kevesebb, mint a lefektetett vetőmag-csomópontok száma. Ha szükséges, vegyen fel további csomópontokat az elsődleges csomópont-típusba ennek eléréséhez. Önálló fürt esetén, ha a lefelé irányuló mag csomópontja nem várható, hogy az állapota érintetlen marad, távolítsa el a csomópontot a fürtből: https \: //docs.microsoft.com/en-us/Azure/Service-Fabric/Service-Fabric-cluster-Windows-Server-Add-Remove-Nodes.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-report-health"></a>sfctl Node-jelentés – állapot
Állapotjelentés küldése a Service Fabric csomóponton.

A megadott Service Fabric csomópont állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentést és a jelentést tartalmazó tulajdonság forrásával kapcsolatos információkat. A rendszer elküldi a jelentést egy Service Fabric átjáró-csomópontnak, amely továbbítja az állapot-tárolónak. Előfordulhat, hogy a jelentést az átjáró fogadja el, de a további ellenőrzés után a Health Store elutasította. Az állapotfigyelő például elutasítja a jelentést egy Érvénytelen paraméter miatt, például egy elavult sorszámot. Ha szeretné megtekinteni, hogy a jelentés az állapotfigyelő áruházban volt-e alkalmazva, ellenőrizze, hogy a jelentés megjelenik-e az HealthEvents szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Health-Property [kötelező] | Az állapotadatok tulajdonsága. <br><br> Az entitások különböző tulajdonságokhoz tartozhatnak állapotjelentést. A tulajdonság egy karakterlánc, nem pedig rögzített enumerálás, amely lehetővé teszi, hogy a jelentéskészítő rugalmasan kategorizálja a jelentést kiváltó állapot feltételeit. A "LocalWatchdog" SourceId forrásazonosító rendelkező jelentéskészítő például nyomon követheti a csomópontok rendelkezésre álló lemezének állapotát, így az adott csomópont "AvailableDisk" tulajdonságát is jelentheti. Ugyanaz a riporter figyelheti a csomópontok kapcsolatát, így a "kapcsolat" tulajdonságot is jelentheti ugyanazon a csomóponton. Az állapotfigyelő szolgáltatásban ezek a jelentések különálló állapotadatokként lesznek kezelve a megadott csomópont esetében. A SourceId forrásazonosító együtt a tulajdonság egyedileg azonosítja az állapotadatok adatait. |
| --állapotadatok [kötelező] | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "OK", "figyelmeztetés", "hiba", "ismeretlen". |
| --Node-Name [kötelező] | A csomópont neve. |
| – forrás-azonosító [kötelező] | Az állapottal kapcsolatos adatokat létrehozó ügyfél/watchdog/rendszer összetevőt azonosító forrás neve. |
| – Leírás | Az állapotadatok leírása. <br><br> A jelentésből származó, emberi olvasásra alkalmas adatok hozzáadására szolgáló szabad szöveget jelöli. A Leírás maximális hossza 4096 karakter. Ha a megadott karakterlánc már nem érhető el, a rendszer automatikusan csonkolja. A csonkítás során a Leírás utolsó karakterei a "[csonkolt]" jelölőt tartalmazzák, a teljes karakterlánc mérete pedig 4096 karakter. A jelölő jelenléte azt jelzi, hogy a felhasználók csonkítva lettek. Vegye figyelembe, hogy a csonkítás során a Leírás kevesebb, mint 4096 karakterből áll az eredeti sztringből. |
| – azonnali | Egy jelző, amely jelzi, hogy a jelentést azonnal el kell-e juttatni. <br><br> Egy állapotjelentés érkezik egy Service Fabric Gateway-alkalmazásba, amely továbbítja az állapot-áruháznak. Ha az azonnali beállítás értéke TRUE (igaz), a rendszer azonnal elküldi a jelentést a HTTP-átjáróról az állapotfigyelő tárolóba, függetlenül a HTTP-átjáró alkalmazás által használt háló-ügyfél beállításaitól. Ez olyan kritikus fontosságú jelentések esetében hasznos, amelyeket a lehető leghamarabb el kell juttatni. Az Időzítéstől és az egyéb feltételektől függően előfordulhat, hogy a jelentés küldése továbbra is meghiúsul, például ha a HTTP-átjáró be van zárva, vagy az üzenet nem éri el az átjárót. Ha az azonnali beállítás hamis értékre van állítva, a rendszer a HTTP-átjáró állapot-ügyfélbeállítások alapján elküldi a jelentést. Ezért a HealthReportSendInterval-konfigurációnak megfelelően kötegbe kerül. Ez az ajánlott beállítás, mivel lehetővé teszi, hogy az állapot-ügyfél optimalizálja az állapot-jelentési üzeneteket az állapotfigyelő tárolóba, valamint az állapotjelentés feldolgozását. Alapértelmezés szerint a rendszer nem küldi el azonnal a jelentéseket. |
| --Remove-when-lejárt | Az érték, amely azt jelzi, hogy a jelentés törlődik-e a Health Store-ból, amikor lejár. <br><br> Ha az értéke TRUE (igaz), a rendszer eltávolítja a jelentést az állapot-áruházból a lejárat után. Ha hamis értékre van állítva, a jelentés a lejártkor hibaként lesz kezelve. A tulajdonság értéke alapértelmezés szerint hamis. Amikor az ügyfelek rendszeresen jelentést küldenek, a Eltávolításlejáratkor false (alapértelmezett) értéket kell beállítania. Így a riporter problémákba ütközik (például holtpont), és nem tud jelentést készíteni, az entitást a rendszer hiba esetén kiértékeli, amikor az állapotjelentés lejár. Ez az entitás a hiba állapotának megfelelően jelenik meg. |
| --Sequence-Number | Az állapotjelentés sorszáma numerikus karakterláncként. <br><br> A jelentés sorszámát a Health Store használja az elavult jelentések észlelésére. Ha nincs megadva, a rendszer automatikusan létrehozza a sorszámot, amikor egy jelentés hozzáadása történik. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| --TTL | Az az időtartam, ameddig ez az állapotjelentés érvényes. Ez a mező ISO8601 formátumot használ az időtartam megadásához. <br><br> Amikor az ügyfelek rendszeresen jelentést küldenek, a jelentéseknek az élettartamuk során nagyobb gyakorisággal kell elküldeniük a jelentéseket. Ha az ügyfelek áttérnek a váltásra, beállíthatja, hogy az idő a végtelen értékre legyen állítva. Ha a lejárati idő lejár, az állapottal kapcsolatos információkat tartalmazó állapotot a rendszer eltávolítja az állapotfigyelő tárolóból, ha a Eltávolításlejáratkor értéke TRUE (igaz), vagy hiba esetén kiértékelt, ha a Eltávolításlejáratkor hamis. Ha nincs megadva, a rendszer az alapértelmezett élettartamot a végtelen értékre adja. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-restart"></a>sfctl-csomópont újraindítása
Újraindít egy Service Fabric fürtcsomópont-csomópontot.

A már elindított Service Fabric fürtcsomópont újraindítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Create-Fabric-dump | A True (igaz) érték megadásával hozzon létre egy dumpot a háló csomóponti folyamatból. Ez megkülönbözteti a kis-és nagybetűket.  Alapértelmezett \: hamis érték. |
| --Node-instance-ID | A célként megadott csomópont példányának azonosítója. Ha a példány azonosítója meg van adva, a csomópont csak akkor indul újra, ha megegyezik a csomópont aktuális példányával. A "0" alapértelmezett értéke minden példány-AZONOSÍTÓnak megfelel. A példány AZONOSÍTÓját a lekérési csomópont lekérdezése használatával lehet megszerezni.  Alapértelmezett \: 0. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-transition"></a>sfctl csomópont-átmenet
Elindít vagy leállít egy fürtcsomópont-csomópontot.

Elindít vagy leállít egy fürtcsomópont-csomópontot.  A fürtcsomópont egy folyamat, nem az operációs rendszer példánya.  A csomópont elindításához adja meg a "Start" kifejezést a NodeTransitionType paraméterhez. Egy csomópont leállításához adja meg a "Leállítás" kifejezést a NodeTransitionType paraméterhez. Ez az API elindítja a műveletet – ha az API visszaadja a csomópontot, előfordulhat, hogy még nem fejeződött be az áttérés. Hívja meg a GetNodeTransitionProgress ugyanazzal a OperationId a művelet előrehaladásának lekéréséhez.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-instance-ID [kötelező] | A célként megadott csomópont csomópont-példányának azonosítója. Ezt a GetNodeInfo API-n keresztül lehet meghatározni. |
| --Node-Name [kötelező] | A csomópont neve. |
| --Node-átmenet típusa [kötelező] | A végrehajtandó áttérés típusát jelzi.  A NodeTransitionType. Start egy leállított csomópontot indít el. A NodeTransitionType. stop leállítja a csomópontot. |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Stop-időtartam-másodpercben [kötelező] | A csomópont leállításának időtartama (másodpercben).  A minimális érték 600, a maximum a 14400.  Az idő lejárta után a csomópont automatikusan biztonsági másolatot készít. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-node-transition-status"></a>sfctl-csomópont átmenete – állapot
A StartNodeTransition használatával megkezdett művelet előrehaladásának beolvasása.

Lekéri egy, a StartNodeTransition-val indított művelet állapotát a megadott OperationId használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --Node-Name [kötelező] | A csomópont neve. |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |


## <a name="next-steps"></a>Következő lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
