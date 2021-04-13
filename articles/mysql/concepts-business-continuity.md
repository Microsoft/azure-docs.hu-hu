---
title: Üzletmenet-folytonosság – Azure Database for MySQL
description: Ismerje meg az üzletmenet folytonosságát (az időponthoz tartozó visszaállítást, az adatközpont-kimaradást, a Geo-visszaállítást) Azure Database for MySQL szolgáltatás használatakor.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e58508e561a33a73e6f6752fc12bc5938b8d7488
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309781"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---single-server"></a>Az üzletmenet folytonosságának áttekintése Azure Database for MySQL – egyetlen kiszolgálóval

Ez a cikk azokat a képességeket ismerteti, amelyeket a Azure Database for MySQL az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Megtudhatja, hogyan lehet helyreállítani az adatvesztést okozó, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozó zavaró események helyreállításának lehetőségeit. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosítására használható funkciók

Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után – ez a helyreállítási idő célkitűzése (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális mennyiségét is, ha az alkalmazás a zavaró esemény utáni helyreállítás során elveszíti az adatvesztést – ez a helyreállítási pont célkitűzése (RPO).

Azure Database for MySQL egyetlen kiszolgáló biztosítja az üzletmenet folytonosságát és a vész-helyreállítási funkciókat, amelyek tartalmazzák a Geo-redundáns biztonsági mentéseket, és lehetővé teszi a Geo-visszaállítást, valamint az olvasási replikák üzembe helyezését egy másik régióban. Mindegyiknek különböző jellemzői vannak a helyreállítási időhöz és a lehetséges adatvesztéshez. A [geo-visszaállítás](concepts-backup.md) funkcióval egy új kiszolgáló jön létre, amely egy másik régióból replikált biztonsági mentési adatok használatával történik. A helyreállításhoz és helyreállításhoz szükséges teljes idő az adatbázis méretétől és a helyreállítható naplók mennyiségétől függ. A kiszolgáló létrehozásának teljes ideje néhány perctől néhány órára változhat. [Olvasási replikák](concepts-read-replicas.md)esetén az elsődleges tranzakciós naplók aszinkron módon lesznek továbbítva a replikára. Ha egy elsődleges adatbázis meghibásodása miatt egy zóna-vagy egy régió szintű hiba miatt nem sikerül átadni a replikát, rövidebb RTO és kevesebb adatvesztést biztosít.

> [!NOTE]
> Az elsődleges és a replika közötti késés a helyek közötti késéstől, a továbbított adatmennyiségtől, valamint az elsődleges kiszolgáló írási feladatának legfontosabb szintjétől függ. A nehéz írási feladatok jelentős késést okozhatnak. 
>
> Az olvasási replikák esetében használt replikáció aszinkron jellege miatt **nem ajánlott** magas rendelkezésre állású (ha) megoldásnak tekinteni, mivel a magasabb lemaradások magasabb RTO és RPO jelenthetnek. Csak olyan munkaterhelések esetén, ahol a késés a munkaterhelés csúcsán és a nem csúcsidőben is kisebb, akkor a replikák beolvasása alternatív megoldás lehet. Ellenkező esetben az olvasási replikák valódi olvasási méretezést biztosítanak a kész nagy számítási feladatokhoz és a (vész-helyreállítási) DR-forgatókönyvekhez.

A következő táblázat összehasonlítja a RTO és a RPO **jellemző számítási feladatok** forgatókönyvét:

| **Képesség** | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belüli visszaállítási pontok <br/> RTO – változó <br/>RPO < 15 perc| A megőrzési időtartamon belüli visszaállítási pontok <br/> RTO – változó <br/>RPO < 15 perc | A megőrzési időtartamon belüli visszaállítási pontok <br/> RTO – változó <br/>RPO < 15 perc |
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | Nem támogatott | RTO – változó <br/>RPO < 1 óra | RTO – változó <br/>RPO < 1 óra |
| Olvasási replikák | RTO – perc * <br/>RPO < 5 perc * | RTO – perc * <br/>RPO < 5 perc *| RTO – perc * <br/>RPO < 5 perc *|

 \* Bizonyos esetekben a RTO és a RPO **is sokkal magasabb** lehet, többek között a helyek közötti késéstől, a továbbítandó adatok mennyiségétől, valamint az elsődleges adatbázis-írási munkaterheléstól függően.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása felhasználói vagy alkalmazáshiba miatt

A szolgáltatás biztonsági másolatai segítségével helyreállíthat egy kiszolgálót a különböző zavaró eseményekről. Előfordulhat, hogy A felhasználók véletlenül törölhetnek bizonyos adatfájlokat, és véletlenül eldobják egy fontos táblát, vagy akár el is dobják a teljes adatbázist. Előfordulhat, hogy egy alkalmazás helytelenül írja felül a megfelelő adatmennyiséget az alkalmazás hibája miatt, és így tovább.

Elvégezheti egy időponthoz való visszaállítást, hogy a kiszolgálóról egy ismert jó időpontra készítsen másolatot. Az időpontnak a kiszolgálón beállított biztonsági mentési megőrzési időtartamon belül kell lennie. Miután az adatok vissza lettek állítva az új kiszolgálóra, lecserélheti az eredeti kiszolgálót az újonnan visszaállított kiszolgálóra, vagy átmásolhatja a szükséges adatait a visszaállított kiszolgálóról az eredeti kiszolgálóra.

> [!IMPORTANT]
> A törölt kiszolgálók csak a biztonsági másolatok törlését követő **öt napon** belül állíthatók vissza. Az adatbázis biztonsági mentése csak a kiszolgálót üzemeltető Azure-előfizetésből érhető el és állítható vissza. Az eldobott kiszolgálók visszaállításához tekintse meg a [dokumentált lépéseket](howto-restore-dropped-server.md). A kiszolgálói erőforrások, a telepítés után a véletlen törlés vagy a váratlan módosítások elleni védelem érdekében a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](../azure-resource-manager/management/lock-resources.md).

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Helyreállítás Azure Regional adatközpont-kimaradásból

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzleti megszakadást okoz, amely csak néhány percet vesz igénybe, de az elmúlt órákban is tarthat.

Az egyik lehetőség, hogy megvárja, amíg a kiszolgáló ismét online állapotba kerül, amikor az adatközpont kimaradása meghalad. Ez olyan alkalmazásokhoz használható, amelyek megengedhetik maguknak, hogy a kiszolgáló bizonyos ideig offline állapotba kerüljön, például egy fejlesztési környezetben. Ha az adatközpont leáll, nem tudja, mennyi ideig tarthat a leállás, így ez a lehetőség csak akkor működik, ha egy ideig nincs szüksége a kiszolgálóra.

## <a name="geo-restore"></a>Georedundáns visszaállítás

A Geo-visszaállítási szolgáltatás visszaállítja a kiszolgálót a Geo-redundáns biztonsági másolatok használatával. A biztonsági mentéseket a kiszolgáló [párosított régiójában](../best-practices-availability-paired-regions.md)tárolja a rendszer. Ezek a biztonsági másolatok akkor is elérhetők, ha a kiszolgáló által üzemeltetett régió offline állapotban van. Ezeket a biztonsági másolatokból bármely más régióba visszaállíthatja, és a kiszolgáló ismét online állapotba helyezhető. További információ a Geo-visszaállításról a [biztonsági mentési és visszaállítási fogalmakról szóló cikkből](concepts-backup.md).

> [!IMPORTANT]
> A Geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót geo-redundáns biztonsági mentési tárolóval kiépített. Ha szeretné, hogy a helyileg redundáns biztonsági mentést a meglévő kiszolgálókon, akkor a meglévő kiszolgáló mysqldump használatával készítsen memóriaképet, és állítsa vissza egy, a Geo-redundáns biztonsági mentéssel konfigurált, újonnan létrehozott kiszolgálóra.

## <a name="cross-region-read-replicas"></a>Régiók közötti olvasási replikák

Az üzleti folytonosság és a vész-helyreállítás megtervezése érdekében a tartományok közötti olvasási replikákat is használhatja. Az olvasási replikák aszinkron módon frissülnek a MySQL bináris naplójának replikációs technológiájának használatával. További információk az olvasási replikák, az elérhető régiók és a feladatátvétel az [olvasási replikák fogalmai című cikkben](concepts-read-replicas.md)olvashatók. 

## <a name="faq"></a>GYIK

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Hol tárolja Azure Database for MySQL az ügyféladatokat?
Alapértelmezés szerint a Azure Database for MySQL nem helyezi át vagy nem tárolja az ügyféladatokat a-ben üzembe helyezett régióból. Az ügyfelek azonban igény szerint engedélyezhetik a [geo-redundáns biztonsági mentéseket](concepts-backup.md#backup-redundancy-options) , vagy létrehozhatnak [régiók közötti olvasási replikát](concepts-read-replicas.md#cross-region-replication) az adattároláshoz egy másik régióban.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Database for MySQL automatikus biztonsági mentéséről](concepts-backup.md).
- Ismerje meg, hogyan lehet visszaállítani [a Azure Portal](howto-restore-server-portal.md) vagy [Az Azure CLI](howto-restore-server-cli.md)használatával.
- További információ a [Azure Database for MySQL található olvasási replikáról](concepts-read-replicas.md).
