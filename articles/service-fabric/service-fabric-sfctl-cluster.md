---
title: Azure Service Fabric CLI – sfctl-fürt
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A fürtök kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 05132e4c7e644e681e2e4cfdedaa2b04a066ebeb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259959"
---
# <a name="sfctl-cluster"></a>sfctl-fürt
Service Fabric fürtök kiválasztása, kezelése és üzemeltetése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| kód verziói | Lekéri a Service Fabric-fürtben kiépített háló-programkódok listáját. |
| konfiguráció – verziók | Lekérdezi a Service Fabric-fürtben kiépített háló-konfigurációs verziók listáját. |
| állapotfigyelő | Lekéri egy Service Fabric-fürt állapotát. |
| manifest | A Service Fabric-fürt jegyzékfájljának beolvasása. |
| művelet – megszakítás | Egy felhasználó által okozott hiba műveletének megszakítása. |
| művelet – lista | Lekéri a megadott bemenet alapján szűrt, felhasználó által kiváltott hibák listáját. |
| rendelkezés | Egy Service Fabric-fürt programkódjának vagy konfigurációs csomagjainak kiépítése. |
| helyreállítás – System | Azt a Service Fabric-fürtöt jelzi, amelynek meg kell próbálkoznia a kvórum elvesztésekor jelenleg beragadt rendszerszolgáltatások helyreállításával. |
| jelentés – állapot | Állapotjelentés küldése a Service Fabric-fürtön. |
| Válassza | Csatlakozás egy Service Fabric-fürt végponthoz. |
| kapcsolat megjelenítése | Annak megjelenítése Service Fabric fürt, amelyhez ez a sfctl-példány csatlakozik. |
| leépítése | Egy Service Fabric-fürt programkódjának vagy konfigurációs csomagjainak kiépítése. |
| frissítés | A Service Fabric-fürt programkódjának vagy konfigurációs verziójának frissítése megkezdődött. |
| frissítés – folytatás | A fürt frissítésének áthelyezése a következő frissítési tartományba. |
| frissítés – visszaállítás | Service Fabric-fürt frissítésének visszaállítása. |
| frissítés – állapot | A fürt aktuális frissítésének előrehaladását kéri le. |
| frissítés – frissítés | Frissítse a Service Fabric-fürt frissítésének paramétereit. |

## <a name="sfctl-cluster-code-versions"></a>sfctl-fürt kódja – verziók
Lekéri a Service Fabric-fürtben kiépített háló-programkódok listáját.

A fürtben kiépített háló-kódokra vonatkozó információk listájának beolvasása. A CodeVersion paraméterrel a kimenetet igény szerint szűrheti csak az adott verzióra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Code-Version | A Service Fabric termék verziója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-config-versions"></a>sfctl-fürt konfigurációja – verziók
Lekérdezi a Service Fabric-fürtben kiépített háló-konfigurációs verziók listáját.

Lekérdezi a fürtben kiépített háló-konfigurációs verziókról szóló információkat. A ConfigVersion paraméterrel a kimenetet igény szerint szűrheti csak az adott verzióra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --config-Version | Service Fabric konfigurációs verziója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-health"></a>sfctl-fürt állapota
Lekéri egy Service Fabric-fürt állapotát.

A EventsHealthStateFilter használatával szűrheti a fürtön az állapot alapján jelentett állapotú események gyűjteményét. Hasonlóképpen, a NodesHealthStateFilter és a ApplicationsHealthStateFilter használatával szűrheti a csomópontok és a visszaadott alkalmazások gyűjteményét az összesített állapot alapján.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Applications-Health-State-Filter | Engedélyezi a fürt állapotának lekérdezése során visszaadott alkalmazás-állapot objektumok szűrését az állapotuk alapján. A paraméter lehetséges értékeibe beletartozik a tagok vagy bitenkénti műveletek által a HealthStateFilter enumerálás tagjain beszerzett egész érték. Csak a szűrőnek megfelelő alkalmazások lesznek visszaadva. A rendszer minden alkalmazást felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotadatok a jelölő-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az OK (2) és a figyelmeztetés (4) HealthState értékkel rendelkező alkalmazások állapotát adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --kizárás-Health-Statistics | Azt jelzi, hogy az állapot statisztikáját a lekérdezés eredményének részeként kell-e visszaadni. Alapértelmezés szerint hamis. A statisztikákban a gyermek entitások számának állapota ok, figyelmeztetés és hiba látható. |
| --include-System-Application-Health-Statistics | Azt jelzi, hogy az állapot statisztikájának tartalmaznia kell-e a Fabric \: /System-alkalmazás állapotának statisztikáit. Alapértelmezés szerint hamis. Ha a IncludeSystemApplicationHealthStatistics értéke TRUE (igaz), az állapot statisztikája tartalmazza a háló \: /System alkalmazáshoz tartozó entitásokat. Ellenkező esetben a lekérdezési eredmény csak a felhasználói alkalmazások esetében tartalmaz állapot-statisztikát. Az állapotfigyelő statisztikának szerepelnie kell a paraméter alkalmazására vonatkozó lekérdezési eredményben. |
| --csomópontok – állapot-állapot – szűrő | Lehetővé teszi a fürt állapotára vonatkozó lekérdezés eredményében visszaadott csomópont állapot-objektumok szűrését állapotuk alapján. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. A rendszer csak a szűrőnek megfelelő csomópontokat adja vissza. Minden csomópont az összesített állapot kiértékelésére szolgál. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotadatok a jelölő-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a csomópontok állapota az OK (2) és a figyelmeztetés (4) HealthState értékével történik.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-manifest"></a>sfctl-fürt jegyzékfájlja
A Service Fabric-fürt jegyzékfájljának beolvasása.

A Service Fabric-fürt jegyzékfájljának beolvasása. A fürtcsomópont a fürt olyan tulajdonságait tartalmazza, amelyek különböző csomópont-típusokat tartalmaznak a fürtön, a biztonsági konfigurációkon, a hibákon és a frissítési tartomány topológiáján stb. Ezek a tulajdonságok a fájl ClusterConfig.JSrészeként vannak megadva különálló fürt telepítésekor. A fürt jegyzékfájljának nagy részét azonban a Service Fabric belsőleg hozza létre más központi telepítési forgatókönyvekben (például Azure Portal használatakor). A fürt jegyzékfájljának tartalma csak tájékoztató jellegű, és a felhasználók nem várhatóan függőséget kapnak a fájl tartalmának vagy értelmezésének formátumával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl-fürt művelete – Mégse
Egy felhasználó által okozott hiba műveletének megszakítása.

A következő API-k indítják el a CancelOperation \: StartDataLoss, a StartQuorumLoss, a StartPartitionRestart és a StartNodeTransition használatával megszakított hibákkal kapcsolatos műveleteket. Ha a kényszerített érték hamis, akkor a rendszer szabályosan leállítja és törli a megadott felhasználó által okozott műveletet.  Ha a Force értéke TRUE (igaz), a parancs meg lesz szakítva, és egy belső állapot is maradhat.  A True érték megadásával körültekintően kell megadnia a kényszerített értéket. Ha ezt az API-t igaz értékre állítja, akkor nem engedélyezett, amíg ezt az API-t már nem hívták meg ugyanarra a teszt parancsra, amelynél a Force false értékre van állítva, vagy ha a test parancs már rendelkezik a OperationState. RollingBack OperationState. A pontosítás \: OperationState. RollingBack azt jelenti, hogy a rendszer a parancs végrehajtása miatt a belső rendszerállapot tisztítását végzi.  Ha a tesztelési parancs adatvesztést okoz, nem fogja visszaállítani az adatvesztést.  Ha például meghívja a StartDataLoss-t, akkor hívja meg ezt az API-t, a rendszer csak a belső állapotot fogja törölni a parancs futtatásával. Nem állítja vissza a megcélzott partíción lévő adatmennyiséget, ha a parancs elég messzire ért, hogy adatvesztést okozzon. Fontos megjegyezni \:  , hogy ha az API-t a Force = = true értékkel hívja meg, a belső állapot maradhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| – kényszerített | Azt jelzi, hogy a rendszer szabályosan visszaállítja-e a belső rendszerállapotot, és megtisztítja a felhasználó által okozott művelet végrehajtásával. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-operation-list"></a>sfctl-fürt művelete – lista
Lekéri a megadott bemenet alapján szűrt, felhasználó által kiváltott hibák listáját.

Lekéri a megadott bemenet alapján szűrt felhasználó által okozott hibák listáját.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --állapot-szűrő | A felhasználó által kiváltott műveletek OperationState szűrésére szolgál. -65535 – az összes kiválasztása – válassza a Futtatás – 2 elemet – válassza ki a RollingBack-8 elemet – válassza a befejezve – 16 – kiválasztva a hibás – 32 – válassza a megszakítva – 64 – ForceCancelled kiválasztása lehetőséget.  Alapértelmezett \: 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |
| --Type-Filter | A felhasználó által kiváltott műveletek OperationType szűrésére szolgál. -65535 – válassza az összes – 1 – PartitionDataLoss kiválasztása lehetőséget. -2 – válassza a PartitionQuorumLoss lehetőséget. -4 – válassza a PartitionRestart lehetőséget. -8 – válassza a NodeTransition lehetőséget.  Alapértelmezett \: 65535. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-provision"></a>sfctl-fürt kiépítése
Egy Service Fabric-fürt programkódjának vagy konfigurációs csomagjainak kiépítése.

Egy Service Fabric-fürt programkódjának vagy konfigurációs csomagjainak érvényesítése és kiépítése.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --cluster-manifest-file-path | A fürt jegyzékfájljának elérési útja |
| --Code-file-path | A fürt kódjának csomagfájl elérési útja. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-recover-system"></a>sfctl-fürt helyreállítása – System
Azt a Service Fabric-fürtöt jelzi, amelynek meg kell próbálkoznia a kvórum elvesztésekor jelenleg beragadt rendszerszolgáltatások helyreállításával.

Azt a Service Fabric-fürtöt jelzi, amelynek meg kell próbálkoznia a kvórum elvesztésekor jelenleg beragadt rendszerszolgáltatások helyreállításával. Ezt a műveletet csak akkor kell végrehajtani, ha ismert, hogy a leállított replikák nem állíthatók helyre. Az API helytelen használata lehetséges adatvesztést eredményezhet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-report-health"></a>sfctl-fürt jelentése – állapot
Állapotjelentés küldése a Service Fabric-fürtön.

Állapotjelentés küldése Service Fabric fürtön. A jelentésnek tartalmaznia kell az állapotjelentést és a jelentést tartalmazó tulajdonság forrásával kapcsolatos információkat. A rendszer elküldi a jelentést egy Service Fabric átjáró-csomópontnak, amely továbbítja az állapot-tárolónak. Előfordulhat, hogy a jelentést az átjáró fogadja el, de a további ellenőrzés után a Health Store elutasította. Az állapotfigyelő például elutasítja a jelentést egy Érvénytelen paraméter miatt, például egy elavult sorszámot. Ha szeretné megtekinteni, hogy a jelentés alkalmazva lett-e az állapotfigyelő tárolóban, futtassa a GetClusterHealth parancsot, és ellenőrizze, hogy a jelentés megjelenik-e a HealthEvents szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
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

## <a name="sfctl-cluster-select"></a>sfctl-fürt kiválasztása
Csatlakozás egy Service Fabric-fürt végponthoz.

Ha a biztonságos fürthöz csatlakozik, abszolút elérési utat kell megadnia egy tanúsítványhoz (. CRT) és a kulcsfájl (. Key), vagy egyetlen fájlhoz (. PEM). Mindkettőt ne határozza meg. Ha biztonságos fürthöz csatlakozik, akkor a HITELESÍTÉSSZOLGÁLTATÓI csomag vagy a megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok könyvtárának abszolút elérési útját is meg kell adni.  A parancs futtatása nélkül nem lehet fürthöz csatlakozni, beleértve a localhost-hoz való kapcsolódást is. Helyi fürthöz való csatlakozáshoz azonban nincs szükség explicit végpontra.  Ha önaláírt tanúsítványt vagy más, jól ismert HITELESÍTÉSSZOLGÁLTATÓ által nem aláírt tanúsítványt használ, a--CA paramétert kell átadnia, hogy biztosítsa az érvényesítést. Ha nem éles fürtön van, az ügyféloldali ellenőrzés megkerüléséhez (az önaláírt vagy nem jól ismert HITELESÍTÉSSZOLGÁLTATÓI aláírás esetén hasznos) használja a--No-ellenőrzés beállítást. Habár lehetséges, az éles fürtök esetében nem ajánlott. A tanúsítvány-ellenőrzési hiba másként is járhat.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| – HRE | Azure Active Directory használata a hitelesítéshez. |
| – CA | A HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok könyvtárának abszolút elérési útja, amelyet érvényes vagy HITELESÍTÉSSZOLGÁLTATÓI csomagként kell kezelni. Ha a CA-tanúsítványok egy könyvtárát használja, `c_rehash <directory>` akkor az OpenSSL által biztosított szolgáltatásnak először a tanúsítvány-kivonatok kiszámításához és a megfelelő szimbolikus hivatkozások létrehozásához kell futnia. Ennek segítségével ellenőrizhető, hogy a fürt által visszaadott tanúsítvány érvényes-e. |
| --CERT | Az ügyféltanúsítvány-fájl abszolút elérési útja. |
| --végpont | A fürt végpontjának URL-címe, beleértve a portot és a HTTP-vagy HTTPS-előtagot. A végpont általában úgy fog kinézni, mint a https \: //<URL-cím>\: 19080. Ha nincs megadva végpont, a rendszer alapértelmezés szerint a http \: //localhost \: 19080.  Alapértelmezett \: http- \: //localhost \: 19080. |
| – kulcs | Az ügyféltanúsítvány-kulcs fájljának abszolút elérési útja. |
| – nem – ellenőrzés | A tanúsítványok ellenőrzésének letiltása HTTPS használata esetén: \: Ez egy nem biztonságos beállítás, és nem használható éles környezetekben. |
| --PEM | Az ügyféltanúsítvány abszolút elérési útja. PEM-fájlként. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-show-connection"></a>sfctl-fürt megjelenítése – kapcsolat
Annak megjelenítése Service Fabric fürt, amelyhez ez a sfctl-példány csatlakozik.

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-unprovision"></a>sfctl-fürt kiépítése
Egy Service Fabric-fürt programkódjának vagy konfigurációs csomagjainak kiépítése.

A kód és a konfiguráció külön történő kiosztása támogatott.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Code-Version | A fürt kódjának csomagjának verziója. |
| --config-Version | A fürt jegyzékfájljának verziója. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade"></a>sfctl-fürt frissítése
A Service Fabric-fürt programkódjának vagy konfigurációs verziójának frissítése megkezdődött.

Érvényesítse a megadott frissítési paramétereket, és kezdje meg a Service Fabric-fürt programkódjának vagy konfigurációs verziójának frissítését, ha a paraméterek érvényesek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Health-Map | A hiba kiemelése előtt JSON-kódolású szótár az alkalmazás neve és a maximális százalékos érték nem megfelelő. |
| --App-Type-Health-Map | A hiba kiemelése előtt JSON-kódolású, az alkalmazásnév és a maximális százalékos arányt tartalmazó szótár. |
| --Code-Version | A fürt kódjának verziója. |
| --config-Version | A fürtkonfiguráció verziója. |
| --Delta-Health-kiértékelés | A különbözeti állapot kiértékelésének engedélyezése az egyes frissítési tartományok befejezése után az abszolút állapot kiértékelése helyett. |
| --Delta-sérült-csomópontok | A csomópontok állapotának maximálisan megengedett százalékos aránya a fürt frissítéseinél.  Alapértelmezett \: 10. <br><br> A különbözetet a csomópontok állapota a frissítés elején, a csomópontok állapota pedig az állapot kiértékelésének időpontjában méri. Az ellenőrzés a frissítési tartomány összes frissítésének befejezése után történik, így biztosítva, hogy a fürt globális állapota elfogadható korlátokon belül legyen. |
| – hiba – művelet | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "visszaállítás", "Manual". |
| --kényszerített újraindítás | A folyamatokat a rendszer a frissítés során kényszeríti újra, még akkor is, ha a kód verziószáma nem változott. <br><br> A frissítés csak a konfigurációt vagy az adatértéket módosítja. |
| --állapot-pipa-újrapróbálkozás | Az állapot-ellenőrzések elvégzésére irányuló kísérletek közötti időtartam, ha az alkalmazás vagy a fürt állapota nem kifogástalan. |
| --állapot-passzolás – stabil | Az az időtartam, ameddig az alkalmazásnak vagy a fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományba kerül. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --állapot-pipa-várakozás | Az a várakozási idő, ameddig a frissítési tartomány befejezése után meg kell várni az állapot-ellenőrzési folyamat megkezdése előtt. |
| --replika-set-pipa-időtúllépés | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megelőzésére szolgáló maximális időtartam, ha váratlan problémák merülnek fel. <br><br> Ha ez az időkorlát lejár, a frissítési tartomány feldolgozása a rendelkezésre állás elvesztésével kapcsolatos problémáktól függetlenül folytatódni fog. Az időtúllépés az egyes frissítési tartományok elején alaphelyzetbe áll. Az érvényes értékek 0 és 42949672925 között vannak. |
| – működés közbeni frissítés – üzemmód | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "figyelt".  Alapértelmezett \: UnmonitoredAuto. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| – nem megfelelő állapotú alkalmazások | A nem kifogástalan állapotú alkalmazások maximálisan megengedett százaléka a hiba jelentése előtt. <br><br> Ha például engedélyezni szeretné, hogy az alkalmazások 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték azt jelenti, hogy az alkalmazások maximálisan tolerálható hányada sérült, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem kifogástalan állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy a nem kifogástalan állapotú alkalmazásokat a fürtben lévő összes alkalmazás példánya fölé osztja, kivéve azokat az alkalmazásokat, amelyek nem szerepelnek a ApplicationTypeHealthPolicyMap. A számítások egy kis mennyiségű alkalmazás meghibásodását okozják. |
| – nem megfelelő állapotú csomópontok | A nem kifogástalan állapotú csomópontok megengedett százalékos aránya a hiba jelentése előtt. <br><br> Ha például engedélyezni szeretné, hogy a csomópontok 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték a csomópontok maximálisan megengedett százalékos arányát jelöli, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem megfelelő állapotú csomópont van, az állapot figyelmeztetésként lesz kiértékelve. A százalékos arányt úgy számítjuk ki, hogy a nem kifogástalan állapotú csomópontok számát a fürtben lévő csomópontok teljes száma fölé osztja. A számítások egy kis számú csomóponton fellépő meghibásodást okoznak. A nagyméretű fürtökben egyes csomópontok mindig le-vagy kimaradnak a javításokhoz, ezért ezt a százalékos arányt úgy kell konfigurálni, hogy eltűri ezt. |
| --upgrade-domain-Delta-sérült-csomópontok | A frissítési tartományi csomópontok maximálisan engedélyezett százalékos értéke a fürt frissítése során engedélyezett.  Alapértelmezett érték \: 15. <br><br> A különbözetet a frissítési tartomány csomópontjainak állapota a frissítés elején, valamint az állapot kiértékelésének időpontjában a frissítési tartomány csomópontjainak állapota alapján méri. Az ellenőrzés akkor történik meg, ha minden frissítési tartomány frissítésének befejezése befejeződött az összes befejezett frissítési tartományon, hogy a frissítési tartományok állapota elfogadható korlátokon belül legyen. |
| – frissítés – tartomány – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a FailureAction végrehajtása előtt kell végrehajtani. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| – frissítés – időtúllépés | Az a időtartam, ameddig a teljes frissítésnek a FailureAction végrehajtása előtt el kell végeznie. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --Figyelmeztetés – hiba | Azt jelzi, hogy a figyelmeztetések a hibákkal azonos súlyossággal vannak-e kezelve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl-fürt frissítése – folytatás
A fürt frissítésének áthelyezése a következő frissítési tartományba.

Ha szükséges, végezze el a fürt kódjának vagy a konfiguráció frissítésének lépéseit a következő frissítési tartományra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --upgrade-domain [kötelező] | A fürt következő frissítési tartománya. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl-fürt frissítése – visszaállítás
Service Fabric-fürt frissítésének visszaállítása.

Service Fabric-fürt programkódjának vagy konfigurációs frissítésének visszaállítása.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl-fürt frissítése – állapot
A fürt aktuális frissítésének előrehaladását kéri le.

Lekéri a fürt folyamatos frissítésének aktuális állapotát. Ha jelenleg nincs folyamatban lévő frissítés, szerezze be az előző fürt frissítésének utolsó állapotát.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl-fürt frissítése – frissítés
Frissítse a Service Fabric-fürt frissítésének paramétereit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Health-Map | A hiba kiemelése előtt JSON-kódolású szótár az alkalmazás neve és a maximális százalékos érték nem megfelelő. |
| --App-Type-Health-Map | A hiba kiemelése előtt JSON-kódolású, az alkalmazásnév és a maximális százalékos arányt tartalmazó szótár. |
| --Delta-Health-kiértékelés | A különbözeti állapot kiértékelésének engedélyezése az egyes frissítési tartományok befejezése után az abszolút állapot kiértékelése helyett. |
| --Delta-sérült-csomópontok | A csomópontok állapotának maximálisan megengedett százalékos aránya a fürt frissítéseinél.  Alapértelmezett \: 10. <br><br> A különbözetet a csomópontok állapota a frissítés elején, a csomópontok állapota pedig az állapot kiértékelésének időpontjában méri. Az ellenőrzés a frissítési tartomány összes frissítésének befejezése után történik, így biztosítva, hogy a fürt globális állapota elfogadható korlátokon belül legyen. |
| – hiba – művelet | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "visszaállítás", "Manual". |
| --kényszerített újraindítás | A folyamatokat a rendszer a frissítés során kényszeríti újra, még akkor is, ha a kód verziószáma nem változott. <br><br> A frissítés csak a konfigurációt vagy az adatértéket módosítja. |
| --állapot-pipa-újrapróbálkozás | Az állapot-ellenőrzések elvégzésére irányuló kísérletek közötti időtartam, ha az alkalmazás vagy a fürt állapota nem kifogástalan. |
| --állapot-passzolás – stabil | Az az időtartam, ameddig az alkalmazásnak vagy a fürtnek kifogástalan állapotban kell maradnia, mielőtt a frissítés a következő frissítési tartományba kerül. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --állapot-pipa-várakozás | Az a várakozási idő, ameddig a frissítési tartomány befejezése után meg kell várni az állapot-ellenőrzési folyamat megkezdése előtt. |
| --replika-set-pipa-időtúllépés | A frissítési tartomány feldolgozásának letiltására és a rendelkezésre állás elvesztésének megelőzésére szolgáló maximális időtartam, ha váratlan problémák merülnek fel. <br><br> Ha ez az időkorlát lejár, a frissítési tartomány feldolgozása a rendelkezésre állás elvesztésével kapcsolatos problémáktól függetlenül folytatódni fog. Az időtúllépés az egyes frissítési tartományok elején alaphelyzetbe áll. Az érvényes értékek 0 és 42949672925 között vannak. |
| – működés közbeni frissítés – üzemmód | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "UnmonitoredAuto", "UnmonitoredManual", "figyelt".  Alapértelmezett \: UnmonitoredAuto. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| – nem megfelelő állapotú alkalmazások | A nem kifogástalan állapotú alkalmazások maximálisan megengedett százaléka a hiba jelentése előtt. <br><br> Ha például engedélyezni szeretné, hogy az alkalmazások 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték azt jelenti, hogy az alkalmazások maximálisan tolerálható hányada sérült, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem kifogástalan állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy a nem kifogástalan állapotú alkalmazásokat a fürtben lévő összes alkalmazás példánya fölé osztja, kivéve azokat az alkalmazásokat, amelyek nem szerepelnek a ApplicationTypeHealthPolicyMap. A számítások egy kis mennyiségű alkalmazás meghibásodását okozják. |
| – nem megfelelő állapotú csomópontok | A nem kifogástalan állapotú csomópontok megengedett százalékos aránya a hiba jelentése előtt. <br><br> Ha például engedélyezni szeretné, hogy a csomópontok 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték a csomópontok maximálisan megengedett százalékos arányát jelöli, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem megfelelő állapotú csomópont van, az állapot figyelmeztetésként lesz kiértékelve. A százalékos arányt úgy számítjuk ki, hogy a nem kifogástalan állapotú csomópontok számát a fürtben lévő csomópontok teljes száma fölé osztja. A számítások egy kis számú csomóponton fellépő meghibásodást okoznak. A nagyméretű fürtökben egyes csomópontok mindig le-vagy kimaradnak a javításokhoz, ezért ezt a százalékos arányt úgy kell konfigurálni, hogy eltűri ezt. |
| --upgrade-domain-Delta-sérült-csomópontok | A frissítési tartományi csomópontok maximálisan engedélyezett százalékos értéke a fürt frissítése során engedélyezett.  Alapértelmezett érték \: 15. <br><br> A különbözetet a frissítési tartomány csomópontjainak állapota a frissítés elején, valamint az állapot kiértékelésének időpontjában a frissítési tartomány csomópontjainak állapota alapján méri. Az ellenőrzés akkor történik meg, ha minden frissítési tartomány frissítésének befejezése befejeződött az összes befejezett frissítési tartományon, hogy a frissítési tartományok állapota elfogadható korlátokon belül legyen. |
| – frissítés – tartomány – időtúllépés | Az egyes frissítési tartományok befejezésének időtartamát a FailureAction végrehajtása előtt kell végrehajtani. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --upgrade-Kind | A lehetséges értékek közé tartozik az \: "Érvénytelen", a "Rolling", a "Rolling_ForceRestart".  Alapértelmezett \: működés. |
| – frissítés – időtúllépés | Az a időtartam, ameddig a teljes frissítésnek a FailureAction végrehajtása előtt el kell végeznie. <br><br> A rendszer először egy ISO 8601 időtartamot jelölő sztringként értelmezi. Ha ez nem sikerül, a rendszer az ezredmásodpercek teljes számát jelölő számként értelmezi. |
| --Figyelmeztetés – hiba | Azt jelzi, hogy a figyelmeztetések a hibákkal azonos súlyossággal vannak-e kezelve. |

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
