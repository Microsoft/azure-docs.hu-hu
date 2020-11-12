---
title: Replikák olvasása – Azure Database for MySQL
description: 'Ismerkedjen meg a Azure Database for MySQL olvasási replikákkal: régiók kiválasztása, replikák létrehozása, replikák csatlakoztatása, replikáció figyelése és replikáció leállítása.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 730b634f23599c5eef8c4c6c988820ae5e4fa9c8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535112"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Olvasási replikák az Azure Database for MySQL-ben

Az olvasási replikával adatokat replikálhat egy Azure Database for MySQL-kiszolgálóról egy csak olvasható kiszolgálóra. A forráskiszolgálóról legfeljebb öt replikára másolhatja az adatokat. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (binlog) fájlpozíció-alapú replikációs technológiájával. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört.

A replikák olyan új kiszolgálók, amelyeket a rendszeres Azure Database for MySQL-kiszolgálókhoz hasonló módon kezel. Az egyes olvasási replikák esetében a virtuális mag és a Storage szolgáltatásban a kiépített számítási kapacitást a GB/hó értékben számítjuk fel.

Ha többet szeretne megtudni a MySQL-replikációs funkciókról és problémákról, tekintse meg a [MySQL-replikáció dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Elfogultság – ingyenes kommunikáció
>
> A Microsoft sokféle és befogadó környezetet támogat. Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz. Az [elfogultság nélküli kommunikációhoz használható Microsoft-stílus útmutatója](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) ezt a kizáró szót ismeri fel. A szó a jelen cikkben a konzisztencia miatt használatos, mert jelenleg a szoftverben megjelenő szó. Ha a szoftver frissítve lett a szó eltávolítására, a rendszer a cikket úgy frissíti, hogy az legyen az igazítás.
>

## <a name="when-to-use-a-read-replica"></a>Mikor használjon olvasási replikát

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási számítási feladatok a replikákba izolálhatók, míg az írási feladatok a főkiszolgálóra irányíthatók.

Gyakori forgatókönyv, hogy a BI-és analitikai munkaterhelések az olvasási replikát használják adatforrásként a jelentéskészítéshez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül az írási kapacitás terheit a főkiszolgálón. Ez a funkció nem a nagy írási igényű számítási feladatokhoz ideális.

Az olvasási replika funkció MySQL aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A forrás és a replika között mérhető késleltetés történik. A replikán lévő adatok végül konzisztensek maradnak a főkiszolgálón lévő adatokkal. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="cross-region-replication"></a>Régiók közötti replikáció
A forráskiszolgáló egy másik régióban is létrehozhat egy olvasási replikát. A régiók közötti replikáció hasznos lehet olyan forgatókönyvek esetén, mint például a vész-helyreállítási tervezés vagy az adatok közelebb hozása a felhasználókhoz.

A forráskiszolgáló bármely [Azure Database for MySQL régióban](https://azure.microsoft.com/global-infrastructure/services/?products=mysql)elérhető.  A forráskiszolgáló tartalmazhat replikát a párosított régiójában vagy az univerzális replika régiókban. Az alábbi képen látható, hogy mely replika régiók érhetők el a forrás régiójától függően.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Replika-régiók olvasása":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Univerzális replika-régiók
A következő régiókban hozhat létre olvasási replikát, függetlenül attól, hogy hol található a forráskiszolgáló. A támogatott univerzális replika-régiók a következők:

Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-Európa, Kelet-Ázsia, USA keleti régiója, USA 2. keleti régiója, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Észak-Európa, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, Nyugat-Európa

### <a name="paired-regions"></a>Párosított régiók
Az univerzális replika régión kívül egy olvasási replikát is létrehozhat a forráskiszolgáló Azure párosított régiójában. Ha nem ismeri a régió pár elemét, többet is megtudhat az [Azure párosított régiókról szóló cikkből](../best-practices-availability-paired-regions.md).

Ha régiók közötti replikákat használ a vész-helyreállítási tervezéshez, javasoljuk, hogy a többi régió helyett a párosított régióban hozza létre a replikát. A párosított régiók elkerülik az egyidejű frissítéseket, és rangsorolják a fizikai elkülönítést és az adattárolást.  

Azonban a következő szempontokat kell figyelembe venni: 

* Regionális elérhetőség: Azure Database for MySQL a közép-Franciaországban, Észak-és Közép-Németország középső régiójában érhető el. A párosított régiói azonban nem érhetők el.
    
* Egyirányú párok: egyes Azure-régiók csak egyetlen irányban vannak párosítva. Ezek a régiók közé tartoznak a Nyugat-India, Dél-Brazília és US Gov Virginia. 
   Ez azt jelenti, hogy a Nyugat-Indiában található forráskiszolgáló létrehozhat egy replikát Dél-Indiában. A dél-indiai forráskiszolgáló azonban nem tud replikát létrehozni Nyugat-Indiában. Ennek az az oka, hogy Nyugat-India másodlagos régiója Dél-India, de Dél-India másodlagos régiója nem Nyugat-India.

## <a name="create-a-replica"></a>Replika létrehozása

> [!IMPORTANT]
> Az olvasási replika funkció csak a általános célú vagy a memória optimalizált árképzési szintjein Azure Database for MySQL-kiszolgálókon érhető el. Győződjön meg arról, hogy a forráskiszolgáló ezen díjszabási szintek egyikében található.

Ha a forráskiszolgáló nem rendelkezik meglévő replika-kiszolgálókkal, a forrás először a replikálás előkészítéséhez újra fog indulni.

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for MySQL-kiszolgáló. Az új kiszolgáló a forráskiszolgálón lévő adattal van feltöltve. A létrehozási idő a forrásban lévő adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet. A replika-kiszolgáló mindig ugyanabban az erőforráscsoportban és előfizetésben jön létre, mint a forráskiszolgálón. Ha egy másik erőforráscsoporthoz vagy egy másik előfizetéshez szeretne replikát létrehozni, akkor [a replika-kiszolgálót a létrehozás után helyezheti át](../azure-resource-manager/management/move-resource-group-and-subscription.md) .

Minden replika engedélyezve van a tárterület [automatikus növelésére](concepts-pricing-tiers.md#storage-auto-grow). Az automatikus növekedés funkció lehetővé teszi, hogy a replika megőrizze az általa replikált adatmennyiséget, és megakadályozza a tárolási hibák miatti megszakítást a replikálásban.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

A létrehozáskor a replika örökli a forráskiszolgáló tűzfalszabályok szabályait. Ezt követően ezek a szabályok függetlenek a forráskiszolgálón.

A replika örökli a rendszergazdai fiókot a forráskiszolgálóról. A forráskiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a forráskiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for MySQL-kiszolgálón tenné. Ahhoz, hogy egy **myreplica** nevű kiszolgáló rendszergazdai felhasználónevét **myadmin** , a MySQL CLI használatával kapcsolódhat a replikához:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció monitorozása

A Azure Database for MySQL a **replikáció késését a Azure monitor másodpercben** mért metrikája biztosítja. Ez a metrika csak replikák esetében érhető el. Ezt a mérőszámot a `seconds_behind_master` MySQL parancsában elérhető metrika alapján számítjuk ki `SHOW SLAVE STATUS` . Állítson be egy riasztást, amely tájékoztatja arról, ha a replikációs késés olyan értéket ér el, amely nem fogadható el a munkaterhelés számára.

Ha megtekinti a replikálás nagyobb késését, tekintse meg a [replikációs késés hibaelhárítása](howto-troubleshoot-replication-latency.md) és a lehetséges okok megismerése című témakört.

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

Ha úgy döntött, hogy feladatátvételt kíván replikálni egy replikára, 

1. A replika replikálásának leállítása<br/>
   Ez a lépés szükséges ahhoz, hogy a replika-kiszolgáló el tudja fogadni az írásokat. Ennek a folyamatnak a részeként a replika kiszolgáló leválasztása a főkiszolgálóról történik. Miután elindította a replikálást, a háttérrendszer-folyamat általában 2 percet vesz igénybe. A művelet következményeinek megismeréséhez tekintse meg a jelen cikk [replikálás leállítása](#stop-replication) című szakaszát.
    
2. Az alkalmazás átirányítása a (korábbi) replikára<br/>
   Minden kiszolgálón egyedi a kapcsolatok karakterlánca. Frissítse az alkalmazást, hogy a főkiszolgáló helyett a (korábbi) replikára mutasson.
    
Miután az alkalmazás sikeresen feldolgozta az olvasásokat és az írásokat, befejezte a feladatátvételt. Az alkalmazás által tapasztalható állásidő mennyisége a probléma észlelése és a fenti 1. és 2. lépés elvégzése után függ.

## <a name="global-transaction-identifier-gtid"></a>Globális tranzakció-azonosító (GTID)

A globális tranzakció-azonosító (GTID) a forráskiszolgáló minden véglegesített tranzakciójának egyedi azonosítója, és alapértelmezés szerint ki van kapcsolva Azure Database for MySQLban. A GTID a 5,7-es és a 8,0-es verziókban támogatott, és csak a 16 TB-nál több tárolót támogató kiszolgálókon. Ha többet szeretne megtudni a GTID és annak használatáról a replikációban, tekintse meg a MySQL [replikációját a GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) dokumentációjában.

A MySQL két típusú tranzakciót támogat: GTID-tranzakciókat (azonosított GTID) és névtelen tranzakciókat (nincs GTID lefoglalva)

A GTID konfigurálásához a következő kiszolgálói paraméterek érhetők el: 

|**Kiszolgáló paraméter**|**Leírás**|**Alapértelmezett érték**|**Értékek**|
|--|--|--|--|
|`gtid_mode`|Azt jelzi, hogy a GTIDs a tranzakciók azonosítására szolgálnak-e. Az üzemmódok közötti változások csak egy lépéssel állíthatók be növekvő sorrendben (pl. `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: Az új és a replikációs tranzakcióknak névtelennek kell lenniük <br> `OFF_PERMISSIVE`: Az új tranzakciók névtelenek. A replikált tranzakciók lehetnek névtelenek vagy GTID tranzakciók. <br> `ON_PERMISSIVE`: Az új tranzakciók GTID-tranzakciók. A replikált tranzakciók lehetnek névtelenek vagy GTID tranzakciók. <br> `ON`: Az új és a replikált tranzakcióknak is GTID tranzakcióknak kell lenniük.|
|`enforce_gtid_consistency`|Kikényszeríti a GTID egységességét azáltal, hogy csak azokat az utasításokat engedélyezi, amelyek tranzakciós szempontból biztonságos módon naplózhatók. Ezt az értéket a GTID- `ON` replikáció engedélyezése előtt kell beállítani. |`OFF`|`OFF`: Az összes tranzakció megsértheti a GTID konzisztenciáját.  <br> `ON`: Egyetlen tranzakció sem engedélyezett a GTID konzisztenciájának megsértése érdekében. <br> `WARN`: Az összes tranzakció megsértheti a GTID konzisztenciáját, de a rendszer figyelmeztetést generál. | 

> [!NOTE]
> Ha a GTID engedélyezve van, nem kapcsolhatja vissza. Ha ki kell kapcsolnia a GTID-t, forduljon az ügyfélszolgálathoz. 

A GTID engedélyezéséhez és a konzisztencia-viselkedés konfigurálásához frissítse a `gtid_mode` és a `enforce_gtid_consistency` kiszolgálói paramétereket a [Azure Portal](howto-server-parameters.md), az [Azure CLI](howto-configure-server-parameters-using-cli.md)vagy a [PowerShell](howto-configure-server-parameters-using-powershell.md)használatával.

Ha a GTID engedélyezve van a forráskiszolgálón ( `gtid_mode` = on), az újonnan létrehozott replikák GTID is engedélyezve lesznek, és GTID-replikációt is használhatnak. A replikáció konzisztenciájának megőrzése érdekében nem lehet frissíteni `gtid_mode` a forrás-vagy a replika-kiszolgáló (k) ra.

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

### <a name="pricing-tiers"></a>Tarifacsomagok

Az olvasási replikák jelenleg csak a általános célú és a memória optimalizált díjszabási szintjein érhetők el.

> [!NOTE]
> A replika-kiszolgáló futtatásának díja azon a régión alapul, ahol a replika-kiszolgáló fut.

### <a name="source-server-restart"></a>Forráskiszolgáló újraindítása

Ha olyan forráshoz hoz létre replikát, amely nem tartalmaz meglévő replikákat, a forrás először újraindul, hogy felkészüljön a replikálásra. Ezt vegye figyelembe, és hajtsa végre ezeket a műveleteket egy leállási időszakon belül.

### <a name="new-replicas"></a>Új replikák

Az olvasási replika új Azure Database for MySQL-kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre.

### <a name="replica-configuration"></a>Replika konfigurációja

A replikát ugyanazzal a kiszolgáló-konfigurációval hozza létre a rendszer, mint a főkiszolgálót. A replika létrehozása után több beállítás is módosítható a forráskiszolgálóról függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentés megőrzési ideje. Az árképzési szint külön is módosítható, kivéve az alapszintű csomagból vagy abból.

> [!IMPORTANT]
> Mielőtt a forráskiszolgálói konfigurációt új értékekre frissítené, frissítse a replika konfigurációját az értékekkel egyenlő vagy nagyobb értékekre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a főkiszolgálón végrehajtott módosításokkal.

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
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

A [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paraméter zárolva van a replika-kiszolgálókon. 

A fenti paraméterek egyikének a forráskiszolgálón való frissítéséhez törölje a replika-kiszolgálókat, frissítse a paraméter értékét a főkiszolgálón, majd hozza létre újra a replikákat.

### <a name="gtid"></a>GTID

A GTID a következő címen támogatott:
- MySQL verziók 5,7 és 8,0 
- A 16 TB-nál több tárterületet támogató kiszolgálók. A 16 TB-os tárterületet támogató régiók teljes listájáért tekintse meg a [díjszabási szintet](concepts-pricing-tiers.md#storage) ismertető cikket. 

A GTID alapértelmezés szerint ki van kapcsolva. Ha a GTID engedélyezve van, nem kapcsolhatja vissza. Ha ki kell kapcsolnia a GTID-t, forduljon az ügyfélszolgálathoz. 

Ha a GTID engedélyezve van a forráskiszolgálón, az újonnan létrehozott replikák GTID is engedélyezve lesznek, és GTID-replikációt is használhatnak. A replikáció konzisztenciájának megőrzése érdekében nem lehet frissíteni `gtid_mode` a forrás-vagy a replika-kiszolgáló (k) ra.

### <a name="other"></a>Egyéb

- Replika replikájának létrehozása nem támogatott.
- A memóriában tárolt táblázatok miatt a replikák nem lesznek szinkronban. Ez a MySQL-replikációs technológia korlátozása. További információt a [MySQL dokumentációjában talál](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) .
- Győződjön meg arról, hogy a forráskiszolgáló táblái rendelkeznek elsődleges kulccsal. Az elsődleges kulcsok hiánya replikációs késést eredményezhet a forrás-és a replikák között.
- A MySQL- [dokumentációban](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) található MySQL-replikálási korlátozások teljes listájának áttekintése

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portal használatával](howto-read-replicas-portal.md)
- Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI és a REST API használatával](howto-read-replicas-cli.md)