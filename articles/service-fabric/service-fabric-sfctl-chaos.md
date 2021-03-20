---
title: Azure Service Fabric CLI – sfctl káosz
description: Ismerje meg a sfctl, az Azure Service Fabric parancssori felületét. A káosz kezelésére szolgáló parancsok listáját tartalmazza.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260835"
---
# <a name="sfctl-chaos"></a>sfctl-káosz
Indítsa el, állítsa le és jelentse a Chaos test Service-t.

## <a name="subgroups"></a>Alcsoportok
|Alcsoport|Description|
| --- | --- |
| [menetrend](service-fabric-sfctl-chaos-schedule.md) | A Chaos-ütemterv beolvasása és beállítása. |
## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
| események | Beolvassa a Chaos-események következő szegmensét a folytatási jogkivonat vagy az időtartomány alapján. |
| get | A káosz állapotának beolvasása. |
| start | Elindítja a káoszt a fürtben. |
| állj | Leállítja a káoszt, ha fut a fürtben, és leállított állapotba helyezi a Chaos-ütemtervet. |

## <a name="sfctl-chaos-events"></a>sfctl Chaos-események
Beolvassa a Chaos-események következő szegmensét a folytatási jogkivonat vagy az időtartomány alapján.

A Chaos-események következő szegmensének beszerzéséhez megadhatja a Continuationtoken argumentumot használja. A Chaos-események új szegmensének megkezdéséhez megadhatja az időtartományt a StartTimeUtc és a EndTimeUtc használatával. A Continuationtoken argumentumot használja és az időtartomány nem adható meg ugyanabban a hívásban. Több mint 100 Chaos-esemény esetén a rendszer több szegmensben adja vissza a káosz eseményeit, ahol egy szegmens nem tartalmaz több mint 100 Chaos-eseményt, és a következő szegmens beszerzéséhez meghívja ezt az API-t a folytatási jogkivonattal.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --Folytatás-token | A folytatási jogkivonat paraméter az eredmények következő készletének beszerzésére szolgál. Egy nem üres értékkel rendelkező folytatási token szerepel az API válaszában, ha a rendszer eredményei nem illeszkednek egyetlen válaszhoz. Ha ezt az értéket átadja a következő API-hívásnak, az API az eredmények következő készletét adja vissza. Ha nincs további eredmény, akkor a folytatási jogkivonat nem tartalmaz értéket. A paraméter értéke nem lehet URL-kódolású. |
| – befejezési idő – UTC | Az az időtartomány záró időpontját jelképező Windows-fájl, amelyre vonatkozóan létrejön egy Chaos-jelentés. A részletekért forduljon a [DateTime. ToFileTimeUtc metódushoz](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --max-results | A lapozható lekérdezések részeként visszaadott eredmények maximális száma. Ez a paraméter a visszaadott eredmények számának felső határát határozza meg. A visszaadott eredmények a megadott maximális eredményeknél kisebbek lehetnek, ha nem férnek hozzá az üzenethez, mint a konfigurációban definiált maximális üzenet méretére vonatkozó korlátozások. Ha a paraméter értéke nulla vagy nincs megadva, a lapozható lekérdezés a visszaadott üzenetben szereplő lehető legtöbb eredményt tartalmazza. |
| --Start-time-UTC | A Windows-fájl azon időtartományának kezdési időpontját jelképező idő, amelynél a Chaos-jelentés készül. A részletekért forduljon a [DateTime. ToFileTimeUtc metódushoz](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) . |
| --időtúllépés-t | A kiszolgáló időtúllépése másodpercben a művelet végrehajtására. Ez az időkorlát azt az időtartamot adja meg, ameddig az ügyfélnek várnia kell, amíg a kért művelet befejeződik. A paraméter alapértelmezett értéke 60 másodperc.  Alapértelmezett \: 60. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-get"></a>sfctl Chaos Get
A káosz állapotának beolvasása.

A káosz állapotának lekérése, amely azt jelzi, hogy a káosz fut-e, a káosz futtatásához használt Chaos-paramétereket és a Chaos-ütemterv állapotát.

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

## <a name="sfctl-chaos-start"></a>sfctl Chaos – első lépések
Elindítja a káoszt a fürtben.

Ha a káosz még nem fut a fürtben, elindul a káosz az átadott Chaos-paraméterekkel. Ha a káosz már fut a híváskor, a hívás sikertelen lesz, FABRIC_E_CHAOS_ALREADY_RUNNING hibakód. További részletekért tekintse meg a [Service Fabric-fürtök vezérelt káosz indukálása](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) című cikket.

### <a name="arguments"></a>Argumentumok

|Argumentum|Description|
| --- | --- |
| --App-Type-Health-Policy-Map | Szótár (kulcs/érték) JSON kódolású tömbje, amely nem kifogástalan állapotú alkalmazásokat biztosít adott alkalmazási típusokhoz. Mindegyik szótári bejegyzés az alkalmazás típusának nevét és egészét adja meg, amely a megadott MaxPercentUnhealthyApplications százalékos arányának értékeléséhez használt százalékértéket jelöli. <br><br> Egy olyan térképet határoz meg, amely a nem kifogástalan állapotú alkalmazásokat egy adott alkalmazás típusára vonatkozóan határozza meg. A fürt állapotának kiértékelése során az alkalmazás típusa állapot házirend-leképezése az egyes alkalmazások típusának leírására használható. A térképen szereplő alkalmazások típusai a térképen megadott százalékos arányban vannak kiértékelve, és nem a fürt állapot-házirendjében definiált globális MaxPercentUnhealthyApplications. A térképen megadott alkalmazások típusai nem számítanak bele az alkalmazások globális készletéből. Ha például egy típus egyes alkalmazásai kritikus fontosságúak, akkor a fürt rendszergazdája hozzáadhat egy bejegyzést az adott alkalmazáshoz tartozó térképhez, és hozzárendelheti a 0%-os értéket (nem tűri el a hibákat). Az összes többi alkalmazás 20%-ra kiértékelhető úgy, hogy a MaxPercentUnhealthyApplications több ezer alkalmazás-példányon is eltűrik. Az alkalmazás típusának állapotára vonatkozó házirend-leképezés csak akkor használható, ha a fürt jegyzékfájlja lehetővé teszi az alkalmazás típus állapotának kiértékelését a HealthManager/EnableApplicationTypeHealthEvaluation konfigurációs bejegyzésének használatával. <br><br> Példa JSON-kódolású karakterláncra: [{ \" kulcs \" : \" háló:/szavazás \" , \" érték \" : \" 0 \" }] |
| --Chaos-Target-Filter | JSON-kódolású szótár két karakterlánc típusú kulccsal. A két kulcs a Chaostargetfilter és a ApplicationInclusionList. Mindkét kulcs értékei a sztringek listája. chaos_target_filter meghatározza az összes szűrőt a megadott Káoszi hibákhoz, például csak bizonyos csomópont-típusokat, vagy csak bizonyos alkalmazások meghibásodását. <br><br> Ha chaos_target_filter nincs használatban, a Chaos hibát jelzett a fürt összes entitása esetében. Ha chaos_target_filter van használatban, a káosz csak azokat az entitásokat okozta, amelyek megfelelnek a chaos_target_filter specifikációnak. A Chaostargetfilter és a ApplicationInclusionList csak a Union szemantikai használatát teszi lehetővé. Nem lehet megadni a Chaostargetfilter és a ApplicationInclusionList metszetét. Például nem adható meg "az alkalmazás hibája csak akkor, ha az adott csomópont típusa van." Ha egy entitást a Chaostargetfilter vagy a ApplicationInclusionList tartalmaz, az entitás nem zárható ki a ChaosTargetFilter használatával. Ha a applicationX nem jelenik meg a ApplicationInclusionList-ben, néhány Chaos iterációs applicationX hibát okozhat, mert az a Chaostargetfilter részét képező nodeTypeY csomópontján történik. Ha a Chaostargetfilter és a ApplicationInclusionList egyaránt üres, akkor egy ArgumentException kerül. A különböző típusú hibák (csomópont újraindítása, kód újraindítása, replika eltávolítása, replika újraindítása, elsődleges áthelyezése és másodlagos áthelyezés) engedélyezve vannak ezen csomópont-típusok csomópontjain. Ha a csomópont típusa (Say NodeTypeX) nem jelenik meg a Chaostargetfilter, akkor a csomópont-szintű hibák (például a NodeRestart) soha nem lesznek engedélyezve a NodeTypeX csomópontjainál, de a kód-és replika-hibák továbbra is engedélyezhetők a NodeTypeX, ha a ApplicationInclusionList egy adott alkalmazás a NodeTypeX csomópontján történik. Ezen a listán legfeljebb 100 csomópont típusú név szerepelhet, így a szám növeléséhez a MaxNumberOfNodeTypesInChaosEntityFilter konfigurálásához szükség van egy konfigurációs frissítésre. Az alkalmazások szolgáltatásaihoz tartozó összes replika a replika hibáira (a replika újraindítására, a replika eltávolítására, az elsődleges áthelyezésre és a másodlagos áthelyezésre) irányul. A káosz csak akkor indítható el, ha a kód csak az alkalmazások replikáit tárolja. Ha egy alkalmazás nem jelenik meg ezen a listán, akkor is hibás lehet az egyes Chaos-iterációkban, ha az alkalmazás egy csomópont típusú csomóponton végződik, amely a Chaostargetfilter része. Ha azonban a applicationX az elhelyezési korlátozásokon keresztül nodeTypeY, és a applicationX hiányzik a ApplicationInclusionList, és a nodeTypeY hiányzik a chaostargetfilter, akkor a applicationX soha nem fog hibát okozni. Ebben a listában legfeljebb 1000 alkalmazás neve szerepelhet a MaxNumberOfApplicationsInChaosEntityFilter-konfigurációhoz szükséges konfigurációk frissítéséhez. |
| --környezet | A JSON kódolású leképezése (karakterlánc, karakterlánc) típusú kulcs-érték párok. A Térkép használatával rögzítheti a káosz futtatásával kapcsolatos információkat. Nem lehet több mint 100 ilyen pár, és mindegyik sztring (kulcs vagy érték) legfeljebb 4095 karakter hosszúságú lehet. Ezt a leképezést a Chaos Run indítója állítja be, hogy opcionálisan tárolja a környezetet az adott futtatásról. |
| – letiltás-Move-replika – hibák | Letiltja az elsődleges áthelyezést és a másodlagos hibák áthelyezését. |
| --Max-cluster-stabilizáció | Az a maximális időtartam, ameddig az összes fürt entitása stabil és kifogástalan lesz.  Alapértelmezett \: 60. <br><br> A Chaos iterációkban hajtja végre az egyes iterációk állapotát, és ellenőrzi a fürt entitásait. Az érvényesítés során, ha egy fürtcsomópont nem stabil és kifogástalan állapotú a MaxClusterStabilizationTimeoutInSeconds belül, a Chaos egy érvényesítési sikertelen eseményt hoz létre. |
| --Max-egyidejű hibák | Az egyidejű hibák maximális száma iteráció alapján. A káosz ismétléseket hajt végre, és két egymást követő iterációt egy ellenőrzési fázis választja el egymástól. Minél nagyobb a párhuzamosság, annál agresszívebb a hibák injekciója – ami összetettebb adatsorozatokat eredményez a hibák kijavítása érdekében. Javasoljuk, hogy a 2. vagy 3. értékkel kezdjen, és körültekintően járjon el.  Alapértelmezett \: 1. |
| --Max-százalék-sérült-alkalmazások | A fürt állapotának a káoszban való kiértékelése során a hibák bejelentése előtt a nem kifogástalan állapotú alkalmazások maximálisan megengedett százalékos aránya. <br><br> A nem kifogástalan állapotú alkalmazások maximálisan megengedett százaléka a hiba jelentése előtt. Ha például engedélyezni szeretné, hogy az alkalmazások 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték azt jelenti, hogy az alkalmazások maximálisan tolerálható hányada sérült, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem kifogástalan állapotú alkalmazás van, az állapot figyelmeztetésként lesz kiértékelve. Ezt úgy számítja ki, hogy a nem kifogástalan állapotú alkalmazásokat a fürtben lévő összes alkalmazás példánya fölé osztja, kivéve azokat az alkalmazásokat, amelyek nem szerepelnek a ApplicationTypeHealthPolicyMap. A számítások egy kis mennyiségű alkalmazás meghibásodását okozják. Az alapértelmezett százalék nulla. |
| --Max-százalék-sérült-csomópontok | A fürt állapotának a káoszban való kiértékelése során a hiba bejelentése előtt a nem kifogástalan állapotú csomópontok maximálisan megengedett százalékos aránya. <br><br> A nem kifogástalan állapotú csomópontok megengedett százalékos aránya a hiba jelentése előtt. Ha például engedélyezni szeretné, hogy a csomópontok 10%-a nem kifogástalan állapotú legyen, ez az érték 10. A százalékos érték a csomópontok maximálisan megengedett százalékos arányát jelöli, mielőtt a fürt hibásnak minősül. Ha a százalékos arányt figyelembe veszi, de legalább egy nem megfelelő állapotú csomópont van, az állapot figyelmeztetésként lesz kiértékelve. A százalékos arányt úgy számítjuk ki, hogy a nem kifogástalan állapotú csomópontok számát a fürtben lévő csomópontok teljes száma fölé osztja. A számítások egy kis számú csomóponton fellépő meghibásodást okoznak. Az alapértelmezett százalék nulla. A nagyméretű fürtökben egyes csomópontok mindig le-vagy kimaradnak a javításokhoz, ezért ezt a százalékos arányt úgy kell konfigurálni, hogy eltűri ezt. |
| --futtatási idő | Teljes idő (másodpercben), ameddig a rendszer a káoszt az automatikus leállítás előtt futtatja. A maximálisan megengedett érték 4 294 967 295 (System. UInt32. MaxValue).  Alapértelmezett \: 4294967295. |
| --időtúllépés-t | Alapértelmezett \: 60. |
| – várakozási idő – hibák | Az egymást követő hibák közötti várakozási idő (másodpercben) egyetlen iteráción belül.  Alapértelmezett érték: \: 20. <br><br> Minél nagyobb az érték, annál kisebb a hibák közötti átfedés, és egyszerűbb a fürt által áthaladó állapot-váltások folyamata. Az ajánlásnak 1 és 5 közötti értékkel kell kezdődnie, és körültekintően kell eljárnia. |
| – várakozási idő – iterációk | A káosz két egymást követő ismétlése között eltelt idő (másodpercben). Minél nagyobb az érték, annál alacsonyabb a hibák befecskendezési sebessége.  Alapértelmezett érték: \: 30. |
| --Figyelmeztetés – hiba | Azt jelzi, hogy a figyelmeztetések a hibákkal azonos súlyossággal vannak-e kezelve. |

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Description|
| --- | --- |
| – hibakeresés | A naplózás részletességének növelésével megjelenítheti az összes hibakeresési naplót. |
| --Help-h | A súgó üzenet megjelenítése és kilépés. |
| --output-o | Kimeneti formátum.  Engedélyezett értékek: \: JSON, jsonc, Table, TSV.  Alapértelmezett \: JSON. |
| – lekérdezés | JMESPath lekérdezési karakterlánca \:További információkat és példákat a http//jmespath.org/című témakörben talál. |
| --verbose | A naplózás részletességének fokozása. A--hibakeresés a teljes hibakeresési naplókhoz. |

## <a name="sfctl-chaos-stop"></a>sfctl káosz leállítása
Leállítja a káoszt, ha fut a fürtben, és leállított állapotba helyezi a Chaos-ütemtervet.

Leállítja a káoszt új hibák végrehajtásával. A repülés közbeni hibák a befejezésig továbbra is végre lesznek hajtva. A jelenlegi Chaos-ütemterv leállított állapotba kerül. Az ütemterv leállítását követően a leállított állapotban marad, és nem használható a Chaos új futtatásainak megadására. Az ütemezés folytatásához meg kell adni egy új Chaos-ütemezést.

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


## <a name="next-steps"></a>Következő lépések
- [Állítsa](service-fabric-cli.md) be a Service Fabric CLI-t.
- Megtudhatja, hogyan használhatja a Service Fabric CLI-t a [minta-parancsfájlok](./scripts/sfctl-upgrade-application.md)használatával.
