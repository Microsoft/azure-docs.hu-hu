---
title: Üzletmenet-folytonosság – Azure Database for MariaDB
description: Ismerje meg az üzletmenet folytonosságát (az időponthoz tartozó visszaállítást, az adatközpont-kimaradást, a Geo-visszaállítást) Azure Database for MariaDB szolgáltatás használatakor.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e164930736f7b93e56d9bd4187e72a9c8eb76202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98659852"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Az üzletmenet folytonosságának megismerése Azure Database for MariaDB

Ez a cikk azokat a képességeket ismerteti, amelyeket a Azure Database for MariaDB az üzletmenet folytonosságát és a vész-helyreállítást biztosítja. Megtudhatja, hogyan lehet helyreállítani az adatvesztést okozó, vagy az adatbázis és az alkalmazás elérhetetlenné válását okozó zavaró események helyreállításának lehetőségeit. Megtudhatja, mi a teendő, ha egy felhasználói vagy alkalmazáshiba hatással van az adatok integritására, egy Azure-régió leáll, vagy az alkalmazás karbantartást igényel.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Az üzletmenet folytonosságának biztosítására használható funkciók

A Azure Database for MariaDB olyan üzletmenet-folytonossági funkciókat biztosít, amelyek automatikus biztonsági mentést tartalmaznak, és lehetővé teszi a felhasználók számára a Geo-visszaállítás kezdeményezését. Mindegyik különböző tulajdonságokkal rendelkezik a becsült helyreállítási idő (ERT) és a lehetséges adatvesztés tekintetében. Ha megértette ezeket a lehetőségeket, választhat közülük, és együtt használhatja őket különböző forgatókönyvek esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreáll a zavaró esemény után – ez a helyreállítási idő célkitűzése (RTO). Meg kell ismernie a legutóbbi adatfrissítések (időintervallum) maximális mennyiségét is, ha az alkalmazás a zavaró esemény utáni helyreállítás során elveszíti az adatvesztést – ez a helyreállítási pont célkitűzése (RPO).

A következő táblázat összehasonlítja a rendelkezésre álló funkciók ERT-és RPO:

| **Képesség** | **Basic** | **Általános célú** | **Memóriaoptimalizált** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Időponthoz kötött visszaállítás biztonsági másolatból | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok | A megőrzési időtartamon belüli visszaállítási pontok |
| Geo-visszaállítás földrajzilag replikált biztonsági másolatokból | Nem támogatott | ERT < 12 h<br/>RPO < 1 óra | ERT < 12 h<br/>RPO < 1 óra |

> [!IMPORTANT]
> Ha törli a kiszolgálót, a kiszolgálón található összes adatbázis is törlődik, és nem állítható helyre. Törölt kiszolgálót nem lehet visszaállítani.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Kiszolgáló helyreállítása felhasználói vagy alkalmazáshiba miatt

A szolgáltatás biztonsági másolatai segítségével helyreállíthat egy kiszolgálót a különböző zavaró eseményekről. Előfordulhat, hogy A felhasználók véletlenül törölhetnek bizonyos adatfájlokat, és véletlenül eldobják egy fontos táblát, vagy akár el is dobják a teljes adatbázist. Előfordulhat, hogy egy alkalmazás helytelenül írja felül a megfelelő adatmennyiséget az alkalmazás hibája miatt, és így tovább.

Elvégezheti egy időponthoz való visszaállítást, hogy a kiszolgálóról egy ismert jó időpontra készítsen másolatot. Az időpontnak a kiszolgálón beállított biztonsági mentési megőrzési időtartamon belül kell lennie. Miután az adatok vissza lettek állítva az új kiszolgálóra, lecserélheti az eredeti kiszolgálót az újonnan visszaállított kiszolgálóra, vagy átmásolhatja a szükséges adatait a visszaállított kiszolgálóról az eredeti kiszolgálóra.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Helyreállítás Azure Regional adatközpont-kimaradásból

Bár ritka, mégis előfordulhat, hogy valamelyik Azure-adatközpont leáll. Leállás esetén az üzleti megszakadást okoz, amely csak néhány percet vesz igénybe, de az elmúlt órákban is tarthat.

Az egyik lehetőség, hogy megvárja, amíg a kiszolgáló ismét online állapotba kerül, amikor az adatközpont kimaradása meghalad. Ez olyan alkalmazásokhoz használható, amelyek megengedhetik maguknak, hogy a kiszolgáló bizonyos ideig offline állapotba kerüljön, például egy fejlesztési környezetben. Ha az adatközpont leáll, nem tudja, mennyi ideig tarthat a leállás, így ez a lehetőség csak akkor működik, ha egy ideig nincs szüksége a kiszolgálóra.

A másik lehetőség a Azure Database for MariaDB geo-visszaállítási funkciójának használata, amely a-kiszolgálót a Geo-redundáns biztonsági másolatok használatával állítja vissza. Ezek a biztonsági másolatok akkor is elérhetők, ha a kiszolgáló által üzemeltetett régió offline állapotban van. Ezeket a biztonsági másolatokból bármely más régióba visszaállíthatja, és a kiszolgáló ismét online állapotba helyezhető.

> [!IMPORTANT]
> A Geo-visszaállítás csak akkor lehetséges, ha a kiszolgálót geo-redundáns biztonsági mentési tárolóval kiépített.

## <a name="cross-region-read-replicas"></a>Régiók közötti olvasási replikák

Az üzleti folytonosság és a vész-helyreállítás megtervezése érdekében a tartományok közötti olvasási replikákat is használhatja. Az olvasási replikák aszinkron módon frissülnek a MariaDB bináris naplójának replikációs technológiájának használatával. További információk az olvasási replikák, az elérhető régiók és a feladatátvétel az [olvasási replikák fogalmai című cikkben](concepts-read-replicas.md)olvashatók. 

## <a name="faq"></a>GYIK
### <a name="where-does-azure-database-for-mariadb-store-customer-data"></a>Hol tárolja Azure Database for MariaDB az ügyféladatokat?
Alapértelmezés szerint a Azure Database for MariaDB nem helyezi át vagy nem tárolja az ügyféladatokat a-ben üzembe helyezett régióból. Az ügyfelek azonban igény szerint engedélyezhetik a [geo-redundáns biztonsági mentéseket](concepts-backup.md#backup-redundancy-options) , vagy létrehozhatnak [régiók közötti olvasási replikát](concepts-read-replicas.md#cross-region-replication) az adattároláshoz egy másik régióban.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Database for MariaDB automatikus biztonsági mentéséről](concepts-backup.md).
- Ismerje meg, hogyan lehet visszaállítani [a Azure Portal](howto-restore-server-portal.md) vagy [Az Azure CLI](howto-restore-server-cli.md)használatával.
- További információ a [Azure Database for MariaDB található olvasási replikáról](concepts-read-replicas.md).