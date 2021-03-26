---
title: Replikák olvasása – Azure Database for MySQL – rugalmas kiszolgáló
description: 'Ismerje meg a Azure Database for MySQL rugalmas kiszolgáló olvasási replikáit: replikák létrehozása, a replikák csatlakoztatása, a replikáció figyelése és a replikáció leállítása.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: e51b9667e3bb20a6bd463d3286888085a927f2c0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109658"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Replikák olvasása Azure Database for MySQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Az Azure Database for MySQL-rugalmas kiszolgálóban található replikák előzetes verzióban érhetők el.

A MySQL az egyik népszerű adatbázismotor a webes és mobil alkalmazások futtatásához. Számos ügyfelünk a saját online oktatási szolgáltatásaihoz, a video streaming-szolgáltatásokhoz, a digitális fizetési megoldásokhoz, az e-kereskedelmi platformokhoz, a szerencsejáték-szolgáltatásokhoz, a hírek portálokhoz, a kormányzati és az egészségügyi webhelyekhez használható. Ezek a szolgáltatások a webes vagy mobil alkalmazások forgalmának növeléséhez és méretezéséhez szükségesek.

Az alkalmazások oldalon az alkalmazás általában Java vagy PHP nyelven lett kifejlesztve, és át lett telepítve az Azure-beli virtuálisgép-méretezési csoportokon vagy az Azure App Services-on való futtatásra, vagy az Azure Kubernetes szolgáltatásban (ak) való futtatásra. A virtuálisgép-méretezési csoport, a App Service vagy az AK az alapul szolgáló infrastruktúrának megfelelően egyszerűsíti az alkalmazások skálázását azáltal, hogy azonnal kiépíti az új virtuális gépeket, és replikálja az alkalmazás állapot nélküli összetevőit, hogy azok a kérések ellátására szolgálnak, de gyakran az adatbázis a központosított állapot-nyilvántartó összetevőként végződik.

Az olvasási replika funkció lehetővé teszi az adatok replikálását egy Azure Database for MySQL rugalmas kiszolgálóról egy írásvédett kiszolgálóra. A forrás-kiszolgálóról legfeljebb **10** replikára lehet replikálni. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris naplójának (binlog) fájlpozíció-alapú replikációs technológiájával. A BinLog-replikációval kapcsolatos további tudnivalókért tekintse meg a [MySQL BinLog-replikáció áttekintése](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)című témakört.

A replikák olyan új kiszolgálók, amelyeket a forrás Azure Database for MySQL a rugalmas kiszolgálókhoz hasonlóan kezel. Az egyes olvasási replikák számlázási díjait a virtuális mag és a Storage-ban, GB/hó-ban kiépített számítási számítások alapján számoljuk fel. További információt a [díjszabás](./concepts-compute-storage.md#pricing) tartalmaz.

Ha többet szeretne megtudni a MySQL-replikációs funkciókról és problémákról, tekintse meg a [MySQL-replikáció dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, azt a cikkből távolítjuk el.
>

## <a name="common-use-cases-for-read-replica"></a>Olvasási replika gyakori használati esetei

Az olvasási replika funkcióval javítható a nagy olvasási igényű számítási feladatok teljesítménye és méretezése. Az olvasási munkaterhelések elkülöníthetők a replikákkal, míg az írási munkaterhelések a forráshoz irányíthatók.

Gyakori forgatókönyvek:

* Az alkalmazásból érkező olvasási munkaterhelések skálázása Lightweight- [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) , például az alkalmazásból a replikák beolvasására szolgáló, az alkalmazásból érkező olvasási lekérdezések horizontális felskálázásához
* A BI-vagy analitikus jelentéskészítési munkaterhelések adatforrásként használhatják a jelentéskészítéshez használható olvasási replikákat.
* A IoT és a gyártási forgatókönyv esetében, ahol a telemetria-adatok bekerülnek a MySQL-adatbázismotorbe, míg több olvasási replika van használatban az adatok jelentéséhez.

Mivel a replikák csak olvashatók, nem csökkentik közvetlenül az írási kapacitással kapcsolatos terheket a forráson. Ez a funkció nem a nagy írási igényű számítási feladatokhoz ideális.

Az olvasási replika funkció MySQL aszinkron replikálást használ. A funkció nem a szinkron replikációs forgatókönyvek esetében jelent meg. A forrás és a replika között mérhető késleltetés történik. A replikán lévő adatforrások végül konzisztensek maradnak a forráson lévő értékekkel. Használja ezt a szolgáltatást olyan számítási feladatokhoz, amelyek alkalmasak erre a késésre.

## <a name="create-a-replica"></a>Replika létrehozása

Ha a forráskiszolgáló nem rendelkezik meglévő replika-kiszolgálókkal, a forrás először a replikálás előkészítéséhez újra fog indulni.

A replika létrehozása munkafolyamat indításakor létrejön egy üres Azure Database for MySQL-kiszolgáló. Az új kiszolgáló a forráskiszolgálón lévő adattal van feltöltve. A létrehozási idő a forrásban lévő adatok mennyiségétől és az utolsó heti teljes biztonsági mentés óta eltelt idővel függ. Az idő néhány perctől akár több órára is terjedhet.

> [!NOTE]
> Az olvasási replikák ugyanazzal a kiszolgáló-konfigurációval jönnek létre, mint a forrás. A replika-kiszolgáló konfigurációja a létrehozása után módosítható. A replika-kiszolgáló mindig ugyanabban az erőforráscsoporthoz, ugyanazon a helyen és előfizetésben jön létre, mint a forráskiszolgálón. Ha egy másik erőforráscsoporthoz vagy egy másik előfizetéshez szeretne replikát létrehozni, akkor [a replika-kiszolgálót a létrehozás után helyezheti át](../../azure-resource-manager/management/move-resource-group-and-subscription.md) . Azt javasoljuk, hogy a replika-kiszolgáló konfigurációját a forrásnál egyenlő vagy nagyobb értékkel kell megőrizni, hogy a replika képes legyen lépést tartani a forrással.

Megtudhatja, hogyan [hozhat létre olvasási replikát a Azure Portalban](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Kapcsolódás replikához

A létrehozáskor a replika örökli a forráskiszolgáló csatlakozási módszerét. A replika kapcsolati metódusa nem módosítható. Ha például a forráskiszolgáló **privát hozzáféréssel rendelkezik (VNet-integráció)** , akkor a replika nem lehet **nyilvános hozzáférésben (engedélyezett IP-címek)**.

A replika örökli a rendszergazdai fiókot a forráskiszolgálóról. A forráskiszolgáló összes felhasználói fiókja replikálódik az olvasási replikára. Csak olvasási replikához csatlakozhat a forráskiszolgálón elérhető felhasználói fiókok használatával.

A replikához a hostname és egy érvényes felhasználói fiók használatával kapcsolódhat, ahogy azt egy normál Azure Database for MySQL rugalmas kiszolgálón tenné. Ahhoz, hogy egy **myreplica** nevű kiszolgáló rendszergazdai felhasználónevét **myadmin**, a MySQL CLI használatával kapcsolódhat a replikához:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

A parancssorba írja be a felhasználói fiókhoz tartozó jelszót.

## <a name="monitor-replication"></a>Replikáció monitorozása

Azure Database for MySQL rugalmas kiszolgáló biztosítja a **replikálás késését a Azure monitor másodpercben** mért metrikájában. Ez a metrika csak replikák esetében érhető el. Ezt a mérőszámot a `seconds_behind_master` MySQL parancsában elérhető metrika alapján számítjuk ki `SHOW SLAVE STATUS` . Állítson be egy riasztást, amely tájékoztatja arról, ha a replikációs késés olyan értéket ér el, amely nem fogadható el a munkaterhelés számára.

Ha megtekinti a replikálás nagyobb késését, tekintse meg a [replikációs késés hibaelhárítása](./../howto-troubleshoot-replication-latency.md) és a lehetséges okok megismerése című témakört.

## <a name="stop-replication"></a>Replikáció leállítása

Leállíthatja a replikációt a forrás és a replika között. Miután leállította a replikálást egy forráskiszolgáló és egy olvasási replika között, a replika önálló kiszolgáló lesz. Az önálló kiszolgálóban található, a replikálás leállítása parancs elindításának időpontjában a replikán elérhető adatértékek. Az önálló kiszolgáló nem működik együtt a forráskiszolgálón.

Ha úgy dönt, hogy leállítja a replikálást egy replikára, az elveszíti az előző forrásra és más replikára mutató összes hivatkozást. Nincs automatikus feladatátvétel a forrás és a replika között.

> [!IMPORTANT]
> Az önálló kiszolgáló nem hozható létre újra replikába.
> Mielőtt leállítja a replikálást egy olvasási replikán, győződjön meg arról, hogy a replika rendelkezik a szükséges összes adattal.

Megtudhatja, hogyan [állíthatja le a replikálást egy replikára](how-to-read-replicas-portal.md).

## <a name="failover"></a>Feladatátvétel

Nincs automatikus feladatátvétel a forrás-és a replika-kiszolgálók között.

Az olvasási replikák az olvasási igényű számítási feladatok skálázására szolgálnak, és nem alkalmasak a kiszolgálók magas rendelkezésre állási igényeinek kielégítésére. Nincs automatikus feladatátvétel a forrás-és a replika-kiszolgálók között. Az olvasási replika replikálásának leállítása az olvasási írási módban való online állapotba állítása az a módszer, amellyel ez a manuális feladatátvétel történik.

Mivel a replikáció aszinkron, a forrás és a replika között késés van. A késés mértékét számos tényező befolyásolja, például a forráskiszolgálón futó munkaterhelés, valamint az adatközpontok közötti késés. A legtöbb esetben a replika késése pár másodperc vagy pár perc. A tényleges replikációs késést a metrikai *replika késésének* használatával követheti nyomon, amely az egyes replikák esetében elérhető. Ez a metrika az utolsó újrajátszott tranzakció óta eltelt időt mutatja. Azt javasoljuk, hogy azonosítsa az átlagos késést úgy, hogy a replika késését egy adott időszakra figyelje. Beállíthat egy riasztást a replika késésével kapcsolatban, hogy ha az a várt tartományon kívül esik, megteheti a műveletet.

> [!Tip]
> Ha feladatátvételt végez a replikára, a forrástól a replika leválasztásakor a késés azt jelzi, hogy mekkora adatvesztés történik.

Miután eldöntötte, hogy feladatátvételt szeretne replikálni a replikán:

1. A replika replikálásának leállítása<br/>
   Ez a lépés szükséges ahhoz, hogy a replika-kiszolgáló el tudja fogadni az írásokat. A folyamat részeként a replika-kiszolgáló le lesz csatolva a forrástól. A replikálás leállítása után a háttérrendszer folyamata általában 2 percet vesz igénybe. A művelet következményeinek megismeréséhez tekintse meg a jelen cikk [replikálás leállítása](#stop-replication) című szakaszát.

2. Az alkalmazás átirányítása a (korábbi) replikára<br/>
   Minden kiszolgálón egyedi a kapcsolatok karakterlánca. Frissítse az alkalmazást, hogy a forrás helyett a (korábbi) replikára mutasson.

Miután az alkalmazás sikeresen feldolgozta az olvasásokat és az írásokat, befejezte a feladatátvételt. Az alkalmazás által tapasztalható állásidő mennyisége a probléma észlelése és a fenti 1. és 2. lépés elvégzése után függ.

## <a name="considerations-and-limitations"></a>Megfontolandó szempontok és korlátozások

| Eset | Korlátozás/megfontolás |
|:-|:-|
| Replika a kiszolgálón a következő zónával: redundáns HA engedélyezve | Nem támogatott |
| Régiók közötti olvasási replikáció | Nem támogatott |
| Díjszabás | A replika-kiszolgáló futtatásának díja azon a régión alapul, ahol a replika-kiszolgáló fut |
| Forráskiszolgáló újraindítása | Ha olyan forráshoz hoz létre replikát, amely nem tartalmaz meglévő replikákat, a forrás először újraindul, hogy felkészüljön a replikálásra. Vegye figyelembe ezt a műveletet, és hajtsa végre ezeket a műveleteket egy leállási időszakban |
| Új replikák | Az olvasási replika új Azure Database for MySQL rugalmas kiszolgálóként jön létre. Egy meglévő kiszolgálót nem lehet replikába készíteni. Egy másik olvasási replika replikája nem hozható létre. |
| Replika konfigurációja | A replika a forrással megegyező kiszolgáló-konfiguráció használatával jön létre. A replika létrehozása után több beállítás is módosítható a forráskiszolgálóról függetlenül: számítási generáció, virtuális mag, tárterület és biztonsági mentés megőrzési ideje. A számítási szintet szintén egymástól függetlenül lehet módosítani.<br> <br> **FONTOS**  <br> -A forráskiszolgáló konfigurációjának új értékekre frissítése előtt frissítse a replika konfigurációját az értékekre. Ez a művelet biztosítja, hogy a replika összhangban lehessen a forráson végrehajtott módosításokkal. <br/> A kapcsolati módszert és a paraméter beállításait a rendszer a forráskiszolgálóról a replikára örökli a replika létrehozásakor. Ezt követően a replika szabályai függetlenek. |
| Leállított replikák | Ha leállítja a replikálást egy forráskiszolgáló és egy olvasási replika között, a leállított replika önálló kiszolgáló lesz, amely fogadja az olvasásokat és az írásokat is. Az önálló kiszolgáló nem hozható létre újra replikába. |
| Törölt forrás-és önálló kiszolgálók | A forráskiszolgáló törlésekor a replikálás minden olvasási replikára leállt. Ezek a replikák automatikusan önálló kiszolgálókra válnak, és elfogadhatják az olvasási és írási műveleteket is. Maga a forráskiszolgáló törölve lett. |
| Felhasználói fiókok | A forráskiszolgálón lévő felhasználókat a rendszer replikálja az olvasási replikára. Csak olvasási replikához csatlakozhat a forráskiszolgálón elérhető felhasználói fiókok használatával. |
| Kiszolgálóparaméterek | Az adatszinkronizálás biztosítása és az esetleges adatvesztés vagy -sérülés elkerülése érdekében bizonyos kiszolgálóparaméterek zárolva vannak, hogy ne lehessen őket módosítani olvasási replikák használata során. <br> A következő kiszolgálói paraméterek a forrás-és a replika-kiszolgálókon is zárolva vannak:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> A [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) paraméter zárolva van a replika-kiszolgálókon. <br> Ha frissíteni szeretné a fenti paraméterek egyikét a forráskiszolgálón, törölje a replika-kiszolgálókat, frissítse a paraméter értékét a forráson, majd hozza létre újra a replikákat. |
| Egyéb | – Replika replikájának létrehozása nem támogatott. <br> A memóriában tárolt táblák miatt előfordulhat, hogy a replikák nem lesznek szinkronban. Ez a MySQL-replikációs technológia korlátozása. További információt a [MySQL dokumentációjában talál](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) . <br>– Győződjön meg arról, hogy a forráskiszolgáló táblái rendelkeznek elsődleges kulccsal. Az elsődleges kulcsok hiánya replikációs késést eredményezhet a forrás-és a replikák között.<br>– A MySQL- [dokumentációban](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) megtekintheti a MySQL-replikációra vonatkozó korlátozások teljes listáját |

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat a Azure Portal használatával](how-to-read-replicas-portal.md)
* Ismerje meg, hogyan [hozhat létre és kezelhet olvasási replikákat az Azure CLI használatával](how-to-read-replicas-cli.md)