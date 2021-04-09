---
title: Ajánlott eljárások az autoskálázáshoz
description: Az Azure-ban Web Apps, virtuálisgép-méretezési csoportokhoz és Cloud Serviceshoz használható autoskálázási minták
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 9aaf9525f2fedee67a86011e938b8e995ccfe9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100617825"
---
# <a name="best-practices-for-autoscale"></a>Ajánlott eljárások az automatikus méretezéshez
Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](../../api-management/api-management-key-concepts.md)vonatkozik.

## <a name="autoscale-concepts"></a>Alapfogalmak

* Egy erőforrásnak csak *egy* méretezési beállítása lehet
* Az autoskálázási beállításhoz egy vagy több profil tartozhat, és mindegyik profilhoz egy vagy több méretezési szabály tartozhat.
* Az autoskálázási beállítás horizontálisan méretezi a példányokat, ami a példányok számának csökkentésével és a *-ben* *történik.*
  Az autoskálázási beállítás a példányok maximális, minimális és alapértelmezett értékét jelöli.
* Az autoskálázási feladatok mindig beolvasják a kapcsolódó metrikát a méretezéshez, így ellenőrzi, hogy a kibővíthető vagy méretezhető-e a beállított küszöbérték. Megtekintheti az automatikus méretezés által méretezhető mérőszámok listáját Azure Monitor az automatikus [skálázás általános metrikái](autoscale-common-metrics.md)alapján.
* Az összes küszöbértéket egy példány szintjén számítjuk ki. Például: "az egyik példány skálázása, ha az átlagos CPU-> 80%-os, ha a példányszám értéke 2", azaz a skálázást, ha az összes példány átlagos PROCESSZORa meghaladja a 80%-ot.
* Az összes autoskálázási hiba a tevékenység naplójában van naplózva. Ezután beállíthat egy műveletnapló- [riasztást](../alerts/activity-log-alerts.md) , hogy e-mailben, SMS-ben vagy webhookon keresztül értesítést kapjon, ha van egy autoskálázási hiba.
* Hasonlóképpen, az összes sikeres méretezési műveletet a rendszer közzéteszi a tevékenység naplójában. Ezután beállíthat egy műveletnapló-riasztást, hogy a rendszer e-mailben, SMS-ben vagy webhookon keresztül értesítést kapjon, amikor sikeresek az automatikusan skálázási műveletek. Az e-mail-vagy webhook-értesítéseket úgy is konfigurálhatja, hogy a sikeres skálázási műveletekről értesítést kapjon az autoskálázási beállítás értesítések lapján.

## <a name="autoscale-best-practices"></a>Ajánlott eljárások az autoskálázáshoz

Használja az alábbi ajánlott eljárásokat az autoscale használata során.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Győződjön meg arról, hogy a maximális és minimális értékek eltérőek, és a közöttük lévő különbség elegendő

Ha olyan beállítással rendelkezik, amely esetében a minimum=2, a maximum=2, a jelenlegi példányszám pedig 2, akkor nem lesz skálázási művelet végrehajtva. Mindig legyen elegendő különbség a maximális és minimális példányszámok között, a határokat is beleértve. Az automatikus skálázás mindig ezek között a korlátok között skáláz.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>A kézi skálázás visszaállítása az automatikus skálázás minimuma és maximuma alapján történik

Ha manuálisan frissíti a példányszámot a maximális érték fölé vagy fölé, az automatikus skálázási motor automatikusan visszaméretezi a minimumot (ha van), vagy a maximumot (ha van). Például beállíthatja a tartományt 3 és 6 között. Ha egy futó példánya van, az autoskálázási motor három példányra méretezi a következő futtatáskor. Hasonlóképpen, ha manuálisan állítja be a méretezést nyolc példányra, a következő futtatáskor az autoskálázás a következő futtatáskor hat példányra fogja méretezni a méretezést.  A manuális skálázás csak akkor történik meg, ha alaphelyzetbe állítja az automatikus skálázási szabályokat is.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>A méretezési és a méretezési szabály kombinációja mindig a növekedés és a csökkenés
Ha a kombinációnak csak egy részét használja, az automatikusan végrehajtott művelet csak egyetlen irányban (vertikális felskálázással vagy a-ben) lesz végrehajtva, amíg el nem éri a profilban definiált maximális vagy minimális példányszámot. Ez nem optimális, ideális esetben azt szeretné, hogy az erőforrás a rendelkezésre állás biztosítása érdekében vertikális felskálázást biztosítson a magas kihasználtsággal. Hasonlóképpen, ha alacsony használati idő mellett szeretné lekicsinyíteni az erőforrást, így költségmegtakarítást érhet el.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Válassza a megfelelő statisztikát a diagnosztikametrikához
A diagnosztikai metrikák esetében az *átlag*, a *minimum*, a *maximum* és az *összeg* közül választhat a skálázás mérőszáma alapján. A leggyakoribb statisztika az *átlag*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>A küszöbértékeket körültekintően válassza ki az összes metrikatípushoz
Javasoljuk, hogy gondosan válasszon különböző küszöbértékeket a kibővíthető és a méretezéshez a gyakorlati helyzetek alapján.

Nem javasoljuk, hogy az alábbi példához hasonló, a kifelé és a feltételekhez hasonló küszöbértékekkel rendelkező méretezési beállításokat *ne ajánljuk* :

* A példányok számának növelése 1-gyel, ha a szálak száma >= 600
* A példányok számának csökkentése 1-gyel, ha a szálak száma <= 600

Lássunk egy példát arra, hogy mi vezethet olyan viselkedéshez, amely zavarosnak tűnhet. Vegye figyelembe a következő sorozatot.

1. Tegyük fel, hogy két példány kezdődik, majd a szálak száma átlagosan 625-ra nő.
2. Egy harmadik példány hozzáadásával a méretezési folyamat kibővíthető.
3. Ezután tegyük fel, hogy az átlagos szálak száma a példányok között a 575 értékre csökken.
4. A leskálázás előtt az automatikus skálázás megkísérli megbecsülni, hogy a végső állapot milyen mértékben lesz kibővítve. Például: 575 x 3 (aktuális példányszám) = 1 725/2 (a példányok végső száma, ha le van méretezve) = 862,5 szál. Ez azt jelenti, hogy az autoskálázásnak azonnal ki kell bővíteni a méretezés után is, ha az átlagos szálak száma változatlan marad, vagy akár csak kis mennyiségre esik. Ha azonban a méretezés újra megtörtént, a teljes folyamat megismétlődik, ami végtelen hurkot eredményez.
5. Ha el szeretné kerülni ezt a helyzetet ("csapkodás"), az autoskálázás egyáltalán nem méretezhető le. Ehelyett kihagyja és újraértékeli a feltételt, amikor a szolgáltatás a következő feladatot végrehajtja. A csapkodott állapot sok embert képes megzavarni, mert az autoscale nem fog működni, ha az átlagos szál 575 volt.

A méretezés során a becslés célja, hogy elkerülje a "csapkodó" szituációkat, ahol a méretezési és a Felskálázási műveletek folyamatosan oda-vissza állnak. Tartsa szem előtt ezt a viselkedést, ha ugyanazokat a küszöbértékeket választja a Kibővítés és a esetében.

Javasoljuk, hogy a Kibővítés és a küszöbértékek között megfelelő árrést válasszon. Példaként vegye figyelembe a következő jobb szabály-kombinációt.

* A példányok számának növelésével, ha CPU% >= 80
* A példányok csökkentése 1 számmal, ha a processzor% <= 60

Ebben az esetben  

1. Tegyük fel, hogy két példányban kell kezdődnie.
2. Ha a példányok átlagos CPU-kihasználtsága 80-ra csökken, a rendszer kibővíti a külső példányok hozzáadását.
3. Feltételezzük, hogy az idő múlásával a CPU%-a 60-ra csökken.
4. Az autoscale skálázási szabálya a végleges állapotot becsüli meg, ha a méretezés be volt. Például: 60 x 3 (aktuális példányszám) = 180/2 (a példányok végső száma, ha le van méretezve) = 90. Így az autoskálázás nem méretezhető, mert azonnal el kellene végeznie a méretezést. Ehelyett kihagyja a méretezést.
5. A következő alkalommal, amikor az autoscale ellenőrzi, a processzor továbbra is 50-ra csökken. Az informatikai becslések ismét-50 x 3 példány = 150/2 példány = 75, amely a 80 kibővített küszöbértéke alá esik, ezért a rendszer sikeresen átméretezi a két példányt.

> [!NOTE]
> Ha az automatikus skálázási motor észleli a leválasztást a példányok megadására való skálázás eredményeképpen, azt is meg kell próbálni, hogy az aktuális szám és a cél száma között más példányokra lehessen méretezni a méretezést. Ha ezen a tartományon belül nem történik meg a csapkodás, az autoskálázás folytatja az új célként megadott méretezési műveletet.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>A speciális metrikák küszöbértékeinek skálázásakor megfontolandó szempontok
 A speciális mérőszámok, például a Storage vagy a Service Bus üzenetsor hossza metrika esetén a küszöbérték az aktuális példányszámban elérhető üzenetek átlagos száma. Körültekintően válassza ki a metrika küszöbértékét.

Tegyük fel, hogy egy példát mutatunk be, hogy jobban megértse a viselkedést.

* A példányok számának növelésével a tárolási várólista üzeneteinek száma >= 50
* A példányok számának csökkentése, ha a Storage üzenetsor-üzenetek száma <= 10

Vegye figyelembe a következő sorozatot:

1. Két tárolási várólista-példány létezik.
2. Az üzenetek folyamatosan jelennek meg, és a Storage-várólista áttekintésekor a 50-es számú összeg olvasható. Feltételezheti, hogy az automatikus skálázásnak el kell indítania egy kibővíthető műveletet. Vegye figyelembe azonban, hogy a példányok száma továbbra is 50/2 = 25 üzenet. Így a méretezés nem történik meg. Az első kibővítés előtt a tárolási várólistán a teljes üzenetek számát 100-re kell állítani.
3. Ezután tegyük fel, hogy az üzenetek teljes száma eléri a 100 értéket.
4. A rendszer egy külső tárolási üzenetsor-példányt ad hozzá egy kibővítő művelet miatt.  A következő kibővíthető művelet addig nem fog történni, amíg az üzenetsor teljes száma eléri a 150-as értéket, mert a 150/3 = 50.
5. Most a várólistán lévő üzenetek száma kisebb lesz. Három példány esetében az első skálázási művelet akkor történik meg, ha az összes várólistában lévő összes üzenet legfeljebb 30, mert 30/3 = 10 üzenet, amely a skálázási küszöbértéket adja meg.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Megfontolandó szempontok, ha az automatikus skálázáshoz több profil van konfigurálva
Egy autoskálázási beállításban kiválaszthatja az alapértelmezett profilt, amelyet a rendszer mindig az ütemezett vagy az időfüggés nélkül alkalmaz, vagy megadhat egy ismétlődő profilt vagy profilt egy dátummal és időtartománnyal rendelkező rögzített időszakhoz.

Ha az autoskálázási szolgáltatás feldolgozza őket, mindig a következő sorrendben ellenőrzi:

1. Rögzített dátum profil
2. Ismétlődő profil
3. Alapértelmezett ("mindig") profil

Ha a profil feltétele teljesül, az autoskálázás nem jelöli meg az alatta lévő következő profil-feltételt. Az autoscale egyszerre csak egy profilt dolgoz fel. Ez azt jelenti, hogy ha egy alacsonyabb szintű profilból is szeretne felvenni egy feldolgozási feltételt, ezeket a szabályokat is fel kell vennie az aktuális profilba.

Tekintsük át a példát:

Az alábbi képen egy, a minimális példányszám = 2 és a maximális példányszám = 10 alapértelmezett profillal rendelkező autoskálázási beállítás látható. Ebben a példában a szabályok úgy vannak konfigurálva, hogy kibővítsék, ha a várólistában lévő üzenetek száma nagyobb, mint 10, és a skálán, ha a várólistában lévő üzenetek száma kevesebb, mint három. Így az erőforrás már két és tíz példány között is méretezhető.

Emellett a Hétfőhöz ismétlődő profilok is be vannak állítva. Ez a minimum példányok esetében van beállítva = 3 és a maximális példányszám = 10. Ez azt jelenti, hogy hétfőn, az első alkalommal végzett autoskálázás ellenőrzi ezt az állapotot, ha a példányszám kettő, az új minimum háromra méretezhető. Amíg az autoskálázás továbbra is megkeresi ezt a profilt (hétfő), az csak a profilhoz konfigurált CPU-alapú kibővített/-szabályokat dolgozza fel. Jelenleg nem vizsgálja meg a várólista hosszát. Ha azonban azt is szeretné, hogy a várólista hosszára vonatkozó feltétel be legyen jelölve, ezeket a szabályokat az alapértelmezett profilból is fel kell vennie a hétfő profiljában.

Hasonlóképpen, amikor az autoscale visszavált az alapértelmezett profilra, először ellenőrzi, hogy teljesülnek-e a minimális és a maximális feltételek. Ha az idő alatt a példányok száma 12, akkor az az alapértelmezett profil számára engedélyezett maximális értéket (10).

![az autoskálázás beállításai](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Megfontolandó szempontok, ha egy profilon belül több szabály van konfigurálva

Egyes esetekben előfordulhat, hogy egy profilban több szabályt kell beállítania. A következő autoskálázási szabályokat használja az autoskálázási motor, ha több szabály van beállítva.

A kibővített, az *autoskálázás fut*, ha bármely szabály teljesül.
A *méretezési* szolgáltatásban az autoskálázás megköveteli az összes szabály teljesítését.

A bemutatóhoz tegyük fel, hogy a következő négy autoskálázási szabály van:

* Ha a CPU < 30%-ot, az 1. skálázással
* Ha a memória < a 50%-ot, a skálázást 1-re
* Ha CPU-> 75%-os, kibővített 1
* Ha a memória > 75%-ot, akkor 1

Ezután a következő történik:

* Ha a CPU értéke 76%, és a memória értéke 50%, akkor kibővítjük.
* Ha a CPU értéke 50%, és a memória értéke 76%, akkor kibővítjük.

Ha viszont a CPU értéke 25%, és a memória 51%-os, az autoskálázás **nem** méretezhető. A skálázáshoz a PROCESSZORnak 29%-os és 49%-os memóriával kell rendelkeznie.

### <a name="always-select-a-safe-default-instance-count"></a>Mindig válasszon biztonságos alapértelmezett példányszámot
Az alapértelmezett példányszám azért fontos, mert az autoscale méretezi a szolgáltatást az adott számra, ha a mérőszámok nem érhetők el. Ezért válassza ki a számítási feladatok számára biztonságos alapértelmezett példányszámot.

### <a name="configure-autoscale-notifications"></a>Automatikus skálázással kapcsolatos értesítések konfigurálása
Ha a következő feltételek bármelyike teljesül, a rendszer közzéteszi az automatikusan a tevékenység naplóját:

* A skálázási művelettel kapcsolatos autoskálázás.
* Az autoskálázási szolgáltatás sikeresen befejezte a méretezési műveletet.
* Az autoskálázási szolgáltatás nem végez méretezési műveletet.
* A mérőszámok nem érhetők el az autoskálázási szolgáltatás számára a méretezési döntések elvégzéséhez.
* A mérőszámok újra elérhetők (helyreállítás) a méretezési döntések elvégzéséhez.
* Az autoscale észleli a feltörést, és megszakítja a méretezési kísérletet. Ebben az esetben a napló típusát fogja látni `Flapping` . Ha ezt látja, gondolja át, hogy a küszöbértékek túl keskenyek-e.
* Az autoscale észleli a csapkodás, de továbbra is képes a méretezésre. Ebben az esetben a napló típusát fogja látni `FlappingOccurred` . Ha ezt látja, az autoskálázási motor megpróbálta méretezni a méretezést (például 4 példányról 2-re), de azt állapította meg, hogy ez csapkodás okozta. Ehelyett az autoskálázási motor különböző számú példányra (például 2.3 példány használatával) méretezhető, ami már nem okoz problémát, így az adott számú példányra méretezhető.

Az autoskálázási motor állapotának figyeléséhez használhat egy műveletnapló-riasztást is. Íme néhány példa arra, hogy [hozzon létre egy műveletnapló-riasztást az előfizetésben lévő összes autoskálázási motor műveleteinek figyelésére](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) , vagy [hozzon létre egy műveletnapló riasztást az előfizetésben lévő, illetve Felskálázási műveletek összes sikertelen, méretezési méretezésének figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

A műveletnapló értesítésein kívül az e-mail-vagy webhook-értesítéseket is konfigurálhatja, hogy a sikeres skálázási műveletekről értesítést kapjon az autoskálázási beállítás értesítések lapján.

## <a name="next-steps"></a>Következő lépések
- [Hozzon létre egy műveletnapló-riasztást az előfizetésben lévő összes autoskálázási motor műveleteinek figyeléséhez.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Eseménynapló-riasztás létrehozása az összes sikertelen, az előfizetésen belüli és a vertikális Felskálázási műveletek figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

