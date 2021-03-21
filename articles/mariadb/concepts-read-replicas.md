---
title: Replikák olvasása – Azure Database for MariaDB
description: 'Ismerkedjen meg a Azure Database for MariaDB olvasási replikákkal: régiók kiválasztása, replikák létrehozása, replikák csatlakoztatása, replikáció figyelése és replikáció leállítása.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 39547e3156a684293a0624f974a8b0930f656485
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664214"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Olvasási replikák az Azure Database for MariaDB-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MariaDB-kiszolgálóról egy csak olvasható kiszolgálóra. A forráskiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikákat a rendszer aszinkron módon frissíti a MariaDB motor bináris naplójának (BinLog) fájljának pozíció-alapú replikációs technológiájának használatával globális tranzakciós AZONOSÍTÓval (GTID). A BinLog replikálásával kapcsolatos további tudnivalókért tekintse meg a [BinLog-replikáció áttekintése](https://mariadb.com/kb/en/library/replication-overview/)című témakört.

A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for MariaDB-kiszolgálókhoz hasonló módon kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

A GTID replikálásával kapcsolatos további tudnivalókért tekintse meg a [MariaDB-replikáció dokumentációját](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések az elsődlegesre irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül a írási kapacitás terheit az elsődlegesen. Ez a funkció nem a nagy írási igényű számítási feladatokhoz ideális.

Az olvasási replika funkció aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A forrás és a replika között mérhető késleltetés történik. A replikán lévő adatértékek végül konzisztensek az elsődlegesen tárolt értékekkel. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció

A forráskiszolgáló egy másik régióban is létrehozhat egy olvasási replikát. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

A forráskiszolgáló bármely [Azure Database for MariaDB régióban](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb)elérhető.  A forráskiszolgáló tartalmazhat replikát a párosított régiójában vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replika régiók érhetők el a forrás régiójától függően.

[![Replika-régiók olvasása](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replika-régiók

A következő régiókban hozhat létre olvasási replikát, függetlenül attól, hogy hol található a forráskiszolgáló. A támogatott univerzális replika-régiók a következők:

Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-Európa, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Észak-Európa, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa

### <a name="paired-regions"></a>Párosított régiók

Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a forráskiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról szóló cikkből](../best-practices-availability-paired-regions.md).

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

Azonban a következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for MariaDB a közép-Franciaországban, Észak-és Közép-Németország középső régiójában érhető el. A párosított régiói azonban nem érhetők el.

* Egyirányú párok: egyes Azure-régiók csak egyetlen irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília és US Gov Virginia. 
   Ez azt jelenti, hogy a Nyugat-Indiában található forráskiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai forráskiszolgáló azonban nem tud replikát létrehozni Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.

## <a name="create-a-replica"></a>Replika létrehozása

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MariaDB-kiszolgálókon érhető el. Győződjön meg arról, hogy a forráskiszolgáló ezen díjszabási szintek egyikében található.

Ha a forráskiszolgáló nem rendelkezik meglévő replika-kiszolgálókkal, a forrás először a replikálás előkészítéséhez újra fog indulni.

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for MariaDB-kiszolgáló. Az új kiszolgáló a forráskiszolgálón lévő adattal van feltöltve. A létrehozási idő a forrásban lévő adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet.

> [!NOTE]
> Ha nincs beállítva tárolási riasztás a kiszolgálókon, javasoljuk, hogy tegye meg ezt a szolgáltatást. A riasztás akkor értesíti, ha egy kiszolgáló eléri a tárolási korlátot, ami hatással lesz a replikálásra.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

A létrehozáskor a replika örökli a forráskiszolgáló tűzfalszabályok szabályait. Ezt követően ezek a szabályok függetlenek a forráskiszolgálón.

A replika örökli a rendszergazdai fiókot a forráskiszolgálóról. A forráskiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a forráskiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for MariaDB-kiszolgálón tenné. Ahhoz, hogy egy **myreplica** nevű kiszolgáló rendszergazdai felhasználónevét **myadmin**, a MySQL CLI használatával kapcsolódhat a replikához:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció monitorozása

A Azure Database for MariaDB a **replikáció késését a Azure monitor másodpercben** mért metrikája biztosítja. Ez a metrika csak replikák esetében érhető el.

Ezt a metrikát a `seconds_behind_master` MariaDB parancsában elérhető metrika alapján számítjuk ki `SHOW SLAVE STATUS` .

Állítson be egy riasztást, amely tájékoztatja arról, ha a replikációs késés olyan értéket ér el, amely nem fogadható el a munkaterhelés számára.

## <a name="stop-replication"></a>Replikáció leállítása

Leállíthatja a replikációt a forrás és a replika között. Miután leállította a replikálást egy forráskiszolgáló és egy olvasási replika között, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem működik együtt a forráskiszolgálón.

Ha úgy dönt, hogy leállítja a replikálást egy replikára, az elveszíti az előző forrásra és más replikára mutató összes hivatkozást. Nincs automatikus feladatátvétel a forrás és a replika között.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](howto-read-replicas-portal.md).

## <a name="failover"></a>Feladatátvétel

Nincs automatikus feladatátvétel a forrás-és a replika-kiszolgálók között.

Mivel a replikáció aszinkron, a forrás és a replika között késés van. A késés mértékét számos tényező befolyásolja, például a forráskiszolgálón futó munkaterhelés, valamint az adatközpontok közötti késés. A legtöbb esetben a replika késése pár másodperc vagy pár perc. A tényleges replikációs késést a metrikai *replika késésének* használatával követheti nyomon, amely az egyes replikák esetében elérhető. Ez a metrika az utolsó újrajátszott tranzakció óta eltelt időt mutatja. Azt javasoljuk, hogy azonosítsa az átlagos késést úgy, hogy a replika késését egy adott időszakra figyelje. Beállíthat egy riasztást a replika késésével kapcsolatban, hogy ha az a várt tartományon kívül esik, megteheti a műveletet.

> [!Tip]
> Ha feladatátvételt végez a replikára, a forrástól a replika leválasztásakor a késés azt jelzi, hogy mekkora adatvesztés történik.

Miután úgy döntött, hogy feladatátvételt kíván replikálni egy replikára,

1. Állítsa le a replikálást a replikára.

   Ez a lépés szükséges ahhoz, hogy a replika-kiszolgáló el tudja fogadni az írásokat. Ennek a folyamatnak a részeként a replika-kiszolgáló le lesz csatolva az elsődlegesről. A replikálás leállítása után a háttérrendszer folyamata általában 2 percet vesz igénybe. A művelet következményeinek megismeréséhez tekintse meg a jelen cikk [replikálás leállítása](#stop-replication) című szakaszát.

2. Mutasson az alkalmazást a (korábbi) replikára.

   Minden kiszolgálón egyedi a kapcsolatok karakterlánca. Frissítse az alkalmazást, hogy az elsődleges helyett a (korábbi) replikára mutasson.

Miután az alkalmazás sikeresen feldolgozta az olvasásokat és az írásokat, befejezte a feladatátvételt. Az alkalmazás által tapasztalható állásidő mennyisége a probléma észlelése és a fenti 1. és 2. lépés elvégzése után függ.

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

### <a name="pricing-tiers"></a>Árképzési szintek

Az olvasási replikák jelenleg csak a általános célú és a memória optimalizált díjszabási szintjein érhetők el.

> [!NOTE]
> A replika-kiszolgáló futtatásának díja azon a régión alapul, ahol a replika-kiszolgáló fut.

### <a name="source-server-restart"></a>Forráskiszolgáló újraindítása

Ha olyan forráshoz hoz létre replikát, amely nem tartalmaz meglévő replikákat, a forrás először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

### <a name="new-replicas"></a>Új replikák

Az olvasási replika új Azure Database for MariaDB-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja

A replika az elsődleges kiszolgálóval megegyező kiszolgálói konfigurációval jön létre. A replika létrehozása után több beállítás is módosítható a forráskiszolgálóról függetlenül: számítási generáció, virtuális mag, tárterület, biztonsági másolat megőrzési ideje és MariaDB motor verziója. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> Mielőtt a forráskiszolgálói konfigurációt új értékekre frissítené, frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékekre. Ez a művelet biztosítja, hogy a replika képes legyen az elsődlegesen végrehajtott módosításokkal.

A tűzfalszabályok és a paraméterek beállításait a rendszer a forráskiszolgálóról a replikára örökli a replika létrehozásakor. Ezt követően a replika szabályai függetlenek.

### <a name="stopped-replicas"></a>Leállított replikák

Ha leállítja a replikálást egy forráskiszolgáló és egy olvasási replika között, a leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába.

### <a name="deleted-source-and-standalone-servers"></a>Törölt forrás-és önálló kiszolgálók

A forráskiszolgáló törlésekor a replikálás minden olvasási replikára leállt. Ezek a replikák automatikusan önálló kiszolgálókra válnak, és elfogadhatják az olvasási és írási műveleteket is. Maga a forráskiszolgáló törölve lett.

### <a name="user-accounts"></a>Felhasználói fiókok

A forráskiszolgálón lévő felhasználókat a rendszer replikálja az olvasási replikára. Csak olvasási replikához csatlakozhat a forráskiszolgálón elérhető felhasználói fiókok használatával.

### <a name="server-parameters"></a>Kiszolgálóparaméterek

Az adatszinkronizálás biztosítása és az esetleges adatvesztés vagy -sérülés elkerülése érdekében bizonyos kiszolgálóparaméterek zárolva vannak, hogy ne lehessen őket módosítani olvasási replikák használata során.

A következő kiszolgálói paraméterek a forrás-és a replika-kiszolgálókon is zárolva vannak:

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

A [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) paraméter zárolva van a replika-kiszolgálókon.

A fenti paraméterek egyikének a forráskiszolgálón való frissítéséhez törölje a replika-kiszolgálókat, frissítse a paraméter értékét az elsődlegesen, és hozza létre újból a replikákat.

### <a name="other"></a>Egyéb

* Replika replikájának létrehozása nem támogatott.
* A memóriában tárolt táblázatok miatt a replikák nem lesznek szinkronban. Ez a MariaDB replikációs technológia korlátozása.
* Győződjön meg arról, hogy a forráskiszolgáló táblái rendelkeznek elsődleges kulccsal. Az elsődleges kulcsok hiánya replikációs késést eredményezhet a forrás-és a replikák között.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portal használatával](howto-read-replicas-portal.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és a REST API használatával](howto-read-replicas-cli.md)
