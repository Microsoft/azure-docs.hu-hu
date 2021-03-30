---
title: Azure Service Fabric CLI – sfctl partíció
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A szolgáltatáshoz tartozó partíciók kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fff569c586548d84ed55018764363ad7f05e526d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86257087"
---
# <a name="sfctl-partition"></a>sfctl-partíció
Bármely szolgáltatás partícióinak lekérdezése és kezelése.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| adatvesztés | Ez az API a megadott partíció adatvesztését fogja okozni. |
| adatvesztés – állapot | A StartDataLoss API használatával elindított partíciós adatvesztési művelet előrehaladásának beolvasása. |
| állapotfigyelő | Lekéri a megadott Service Fabric partíció állapotát. |
| információ | Egy Service Fabric partíció információinak beolvasása. |
| list | Lekéri egy Service Fabric szolgáltatás partícióinak listáját. |
| betöltés | Lekéri a megadott Service Fabric partíció betöltési információit. |
| betöltés – alaphelyzetbe állítás | Visszaállítja egy Service Fabric partíció aktuális terhelését. |
| kvórum – veszteség | Egy adott állapot-nyilvántartó szolgáltatásbeli partíció Kvórumának elvesztését okozhatja. |
| kvórum – veszteség – állapot | Lekérdezi a kvórum elvesztése műveletének előrehaladását a StartQuorumLoss API használatával megkezdett partíción. |
| Visszaszerez | Azt jelzi, hogy a Service Fabric-fürtön olyan adott partíciót kell helyreállítani, amely jelenleg a kvórum elvesztése miatt beragadt. |
| összes helyreállítása | Azt jelzi, hogy a Service Fabric-fürtön olyan szolgáltatásokat (beleértve a rendszerszolgáltatásokat is) kell helyreállítani, amelyek jelenleg a kvórum elvesztése miatt megakadnak. |
| jelentés – állapot | Állapotjelentés küldése a Service Fabric partíción. |
| restart | Ez az API újraindítja a megadott partíció néhány vagy összes replikáját vagy példányát. |
| újraindítás állapota | A StartPartitionRestart használatával megkezdett PartitionRestart-művelet előrehaladásának beolvasása. |
| SVC-név | Lekéri egy partíció Service Fabric szolgáltatásának nevét. |

## <a name="sfctl-partition-data-loss"></a>sfctl partíciós adatvesztés
Ez az API a megadott partíció adatvesztését fogja okozni.

Elindítja a partíció OnDataLossAsync API-ját.  Ez az API a megadott partíció adatvesztését fogja okozni. Elindítja a partíció OnDataLoss API-ját. A tényleges adatvesztés a megadott Datalossmode objektum függ.
- PartialDataLoss: a rendszer csak a replikák kvórumát távolítja el, és a partícióra vonatkozóan OnDataLoss vált ki, a tényleges adatvesztés azonban a repülés közbeni replikációtól függ.  
- FullDataLoss: a rendszer eltávolítja az összes replikát, ezért az összes adatvesztés történik, és a rendszer elindítja a OnDataLoss. Ezt az API-t csak a célként megadott állapot-nyilvántartó szolgáltatással hívhatja. Az API meghívása Rendszerszolgáltatással, mivel a cél nem ajánlott.

> [!NOTE]   
> Az API meghívása után az nem vonható vissza. A CancelOperation hívása csak a végrehajtás leállítását és a belső rendszerállapot tisztítását eredményezi. Nem állítja vissza az adatvesztést, ha a parancs elég messzire ért, hogy adatvesztést okozzon. Hívja meg a GetDataLossProgress API-t ugyanazzal a OperationId, hogy az API-val megkezdett művelettel kapcsolatos információkat kérjen vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --adatvesztési mód [kötelező] | Ezt az enumerálást a StartDataLoss API adja át, amely azt jelzi, hogy milyen típusú adatvesztést okoz. |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partíciós adatvesztési állapot
A StartDataLoss API használatával elindított partíciós adatvesztési művelet előrehaladásának beolvasása.

Lekérdezi a StartDataLoss-vel elindított adatvesztési művelet állapotát a OperationId használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-health"></a>sfctl-partíció állapota
Lekéri a megadott Service Fabric partíció állapotát.

A EventsHealthStateFilter használatával szűrheti a szolgáltatáson jelentett állapotú események gyűjteményét az állapot alapján. A ReplicasHealthStateFilter használatával szűrheti a ReplicaHealthState objektumok gyűjteményét a partíción. Ha olyan partíciót ad meg, amely nem létezik az állapotfigyelő tárolóban, a kérelem hibát ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Events-Health-State-Filter | Engedélyezi az állapot alapján visszaadott HealthEvent-objektumok gyűjteményének szűrését. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák. Csak a szűrőnek megfelelő események lesznek visszaadva. A rendszer minden eseményt felhasznál az összesített állapot kiértékelésére. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotok a jelző-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) és a figyelmeztetés (4) HealthState értékű eseményt adja vissza.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --kizárás-Health-Statistics | Azt jelzi, hogy az állapot statisztikáját a lekérdezés eredményének részeként kell-e visszaadni. Alapértelmezés szerint hamis. A statisztikákban a gyermek entitások számának állapota ok, figyelmeztetés és hiba látható. |
| --replika-állapot-szűrő | Engedélyezi a ReplicaHealthState objektumok gyűjteményének szűrését a partíción. Az érték a tagok vagy bitenkénti műveleteiből szerezhető be a HealthStateFilter tagjain. Csak a szűrőnek megfelelő replikák lesznek visszaadva. Az összesített állapot kiértékeléséhez az összes replikát fogja használni a rendszer. Ha nincs megadva, a rendszer az összes bejegyzést visszaadja. Az állapotadatok a jelölő-alapú enumerálások, így az érték a bitenkénti "vagy" operátor használatával kapott értékek kombinációja lehet. Ha például a megadott érték 6, akkor a rendszer az összes, az OK (2) HealthState értékű eseményt és a figyelmeztetést (4) adja vissza. A paraméter lehetséges értékei a következő állapotok egyikének egészét tartalmazzák.  <br> – Alapértelmezett – alapértelmezett érték. Megfelel bármely HealthState. Az érték nulla.  <br> – Nincs – a HealthState értéknek nem megfelelő szűrő. Az adott állapotok egy adott gyűjteményében nem lehet eredményt visszaadni. Az érték 1.  <br> – Ok – a HealthState értékkel egyező bemenettel rendelkező szűrő. Az érték 2.  <br> -Figyelmeztetés – a HealthState értékkel rendelkező bemenettel egyező szűrő. Az érték 4.  <br> – Hiba – a HealthState értékű bemenettel egyező szűrő. Az érték 8.  <br> – Minden olyan szűrő, amely megfelel bármely HealthState értéknek. Az érték 65535. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-info"></a>sfctl-partíció adatai
Egy Service Fabric partíció információinak beolvasása.

A megadott partícióval kapcsolatos információk beolvasása. A válasz tartalmazza a partíció AZONOSÍTÓját, a particionálási séma adatait, a partíció által támogatott kulcsokat, az állapotot, az állapotot és a partíció egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-list"></a>sfctl-partíciók listája
Lekéri egy Service Fabric szolgáltatás partícióinak listáját.

A válasz tartalmazza a partíció AZONOSÍTÓját, a particionálási séma adatait, a partíció által támogatott kulcsokat, az állapotot, az állapotot és a partíció egyéb részleteit.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
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

## <a name="sfctl-partition-load"></a>sfctl partíció betöltése
Lekéri a megadott Service Fabric partíció betöltési információit.

Egy megadott partíció betöltésére vonatkozó adatokat ad vissza. A válasz tartalmazza a Service Fabric partícióhoz tartozó betöltési jelentések listáját. Minden jelentés tartalmazza a betöltési metrika nevét, értékét és az utolsó jelentett időt UTC szerint.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-load-reset"></a>sfctl partíciójának betöltése – alaphelyzetbe állítás
Visszaállítja egy Service Fabric partíció aktuális terhelését.

Visszaállítja egy Service Fabric partíció aktuális terhelését a szolgáltatás alapértelmezett terhelésére.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl-partíció kvóruma – veszteség
Egy adott állapot-nyilvántartó szolgáltatásbeli partíció Kvórumának elvesztését okozhatja.

Ez az API hasznos lehet egy ideiglenes kvórum elvesztése esetén a szolgáltatásban. Hívja meg a GetQuorumLossProgress API-t ugyanazzal a OperationId, hogy az API-val megkezdett művelettel kapcsolatos információkat kérjen vissza. Ez csak állapot-nyilvántartó megőrzött (HasPersistedState = = true) szolgáltatások esetében hívható meg.  Ne használja ezt az API-t állapot nélküli szolgáltatásokra vagy állapot-nyilvántartó szolgáltatásokra.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Partition-ID [kötelező] | A partíció identitása. |
| – kvórum – veszteség – időtartam [kötelező] | Az az időtartam, ameddig a partíció megőrzi a kvórum elvesztését.  Ezt másodpercben kell megadni. |
| --kvórum-Loss-mode [kötelező] | Ezt az enumerálást a StartQuorumLoss API adja át, amely azt jelzi, hogy milyen típusú kvórum elvesztését kell kiváltani. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl-partíció Kvórumának elvesztése – állapot
Lekérdezi a kvórum elvesztése műveletének előrehaladását a StartQuorumLoss API használatával megkezdett partíción.

Lekérdezi a StartQuorumLoss által elindított kvórum-elvesztési művelet állapotát a megadott OperationId használatával.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-recover"></a>sfctl partíció helyreállítása
Azt jelzi, hogy a Service Fabric-fürtön olyan adott partíciót kell helyreállítani, amely jelenleg a kvórum elvesztése miatt beragadt.

Ezt a műveletet csak akkor kell végrehajtani, ha ismert, hogy a leállított replikák nem állíthatók helyre. Az API helytelen használata lehetséges adatvesztést eredményezhet.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-recover-all"></a>sfctl Partition Recover-all
Azt jelzi, hogy a Service Fabric-fürtön olyan szolgáltatásokat (beleértve a rendszerszolgáltatásokat is) kell helyreállítani, amelyek jelenleg a kvórum elvesztése miatt megakadnak.

Ezt a műveletet csak akkor kell végrehajtani, ha ismert, hogy a leállított replikák nem állíthatók helyre. Az API helytelen használata lehetséges adatvesztést eredményezhet.

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

## <a name="sfctl-partition-report-health"></a>sfctl partíciós jelentés – állapot
Állapotjelentés küldése a Service Fabric partíción.

A megadott Service Fabric partíció állapotának jelentése. A jelentésnek tartalmaznia kell az állapotjelentést és a jelentést tartalmazó tulajdonság forrásával kapcsolatos információkat. A rendszer elküldi a jelentést egy Service Fabric átjáró-partícióra, amely továbbítja az állapot-tárolónak. Előfordulhat, hogy a jelentést az átjáró fogadja el, de a további ellenőrzés után a Health Store elutasította. Az állapotfigyelő például elutasítja a jelentést egy Érvénytelen paraméter miatt, például egy elavult sorszámot. Ha szeretné megtekinteni, hogy a jelentés az állapot-áruházban volt-e alkalmazva, ellenőrizze, hogy a jelentés megjelenik-e az események szakaszban.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Health-Property [kötelező] | Az állapotadatok tulajdonsága. <br><br> Az entitások különböző tulajdonságokhoz tartozhatnak állapotjelentést. A tulajdonság egy karakterlánc, nem pedig rögzített enumerálás, amely lehetővé teszi, hogy a jelentéskészítő rugalmasan kategorizálja a jelentést kiváltó állapot feltételeit. A "LocalWatchdog" SourceId forrásazonosító rendelkező jelentéskészítő például nyomon követheti a csomópontok rendelkezésre álló lemezének állapotát, így az adott csomópont "AvailableDisk" tulajdonságát is jelentheti. Ugyanaz a riporter figyelheti a csomópontok kapcsolatát, így a "kapcsolat" tulajdonságot is jelentheti ugyanazon a csomóponton. Az állapotfigyelő szolgáltatásban ezek a jelentések különálló állapotadatokként lesznek kezelve a megadott csomópont esetében. A SourceId forrásazonosító együtt a tulajdonság egyedileg azonosítja az állapotadatok adatait. |
| --állapotadatok [kötelező] | A lehetséges értékek a következők lehetnek: \: "Érvénytelen", "OK", "figyelmeztetés", "hiba", "ismeretlen". |
| --Partition-ID [kötelező] | A partíció identitása. |
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

## <a name="sfctl-partition-restart"></a>sfctl partíció újraindítása
Ez az API újraindítja a megadott partíció néhány vagy összes replikáját vagy példányát.

Ez az API hasznos a feladatátvétel teszteléséhez. Állapot nélküli szolgáltatás partíciójának célzásához a Restartpartitionmode objektum AllReplicasOrInstances kell lennie. Hívja meg a GetPartitionRestartProgress API-t ugyanazzal a OperationId, hogy megszerezze a folyamatot.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --restart-Partition-mode [kötelező] | Adja meg, hogy mely partíciókat kell újraindítani. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-restart-status"></a>sfctl partíció újraindítása – állapot
A StartPartitionRestart használatával megkezdett PartitionRestart-művelet előrehaladásának beolvasása.

Lekérdezi a StartPartitionRestart által a megadott OperationId használatával indított PartitionRestart előrehaladását.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Operation-ID [kötelező] | Az API hívását azonosító GUID.  Ezt a rendszer átadja a megfelelő GetProgress API-nak. |
| --Partition-ID [kötelező] | A partíció identitása. |
| --Service-ID [kötelező] | A szolgáltatás identitása. Ez az azonosító általában a szolgáltatás teljes neve a "Fabric \: " URI-séma nélkül. Az 6,0-es verziótól kezdődően a hierarchikus nevek a " \~ " karakterrel vannak tagolva. Ha például a szolgáltatás neve "Fabric \: /MyApp/App1/svc1", a szolgáltatás identitása "SajátPr \~ App1 svc1" lesz a \~ korábbi verziók 6.0 + és "SajátPr/App1/svc1" értékében. |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-partition-svc-name"></a>sfctl Partition SVC-név
Lekéri egy partíció Service Fabric szolgáltatásának nevét.

Lekéri a szolgáltatás nevét a megadott partícióhoz. A rendszer 404-es hibát ad vissza, ha a partíció azonosítója nem létezik a fürtben.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Partition-ID [kötelező] | A partíció identitása. |
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
