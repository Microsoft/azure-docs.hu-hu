---
title: Főverzió frissítése Azure Database for MySQL – egyetlen kiszolgálón
description: Ez a cikk azt ismerteti, hogyan frissíthető a Azure Database for MySQL-Single Server főverziója
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 62faaed3672f721b26587d1bca3ddb0947f733e7
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220836"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Főverzió frissítése Azure Database for MySQL egyetlen kiszolgálón

> [!NOTE]
> Ez a cikk a _Slave_ kifejezésre mutató hivatkozásokat tartalmaz, amelyek egy kifejezés, amelyet a Microsoft már nem használ. Ha a rendszer eltávolítja a kifejezést a szoftverből, a rendszer eltávolítja azt a cikkből.
>

> [!IMPORTANT]
> Az Azure Database for MySQL egyetlen kiszolgálójának főverziójának frissítése nyilvános előzetes verzióban érhető el.

Ez a cikk azt ismerteti, hogyan frissítheti a MySQL főverzióját helyben, Azure Database for MySQL egyetlen kiszolgálón.

Ez a funkció lehetővé teszi, hogy az ügyfelek a MySQL 5,6-kiszolgálókat a MySQL 5,7-re helyben, az adatmozgatás nélkül, vagy az alkalmazás-kapcsolódási sztringek megváltozásának szükségességére kattintva végezzék.

> [!Note]
> * A főverzió frissítése csak a MySQL 5,6-ből a MySQL 5,7-re való frissítés főverziójának frissítése esetén érhető el.
> * A kiszolgáló a frissítési művelet során nem lesz elérhető. Ezért javasoljuk, hogy a tervezett karbantartási időszak alatt végezzen frissítéseket. Az [olvasási replika használatával a mysql 5,6-ről a mysql 5,7-re a minimális állásidő főverziójának frissítése végezhető el.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Főverzió frissítése a MySQL 5,6-ből a MySQL 5,7-re Azure Portal használatával

Az alábbi lépéseket követve elvégezheti a MySQL 5,6-kiszolgáló Azure-adatbázisának nagyobb verziófrissítését Azure Portal

> [!IMPORTANT]
> Javasoljuk, hogy először a kiszolgáló visszaállított példányán végezze el a frissítést, nem pedig közvetlenül az éles környezetben. Lásd: [az időponthoz tartozó visszaállítás végrehajtása](howto-restore-server-portal.md#point-in-time-restore).

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL 5,6-kiszolgálót.

2. Az **Áttekintés** lapon kattintson a **frissítés** gombra az eszköztáron.

3. A **frissítés** szakaszban kattintson az **OK** gombra az Azure Database for MySQL 5,6-kiszolgáló frissítéséhez a 5,7 Serverre.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – áttekintés – frissítés":::

4. Egy értesítés megerősíti, hogy a frissítés sikeres volt.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Nagyobb verziófrissítés a MySQL 5,6-ről a MySQL 5,7-re az Azure CLI használatával

Az alábbi lépéseket követve elvégezheti a MySQL 5,6-kiszolgáló Azure-adatbázisának verziófrissítését az Azure CLI használatával

> [!IMPORTANT]
> Javasoljuk, hogy először a kiszolgáló visszaállított példányán végezze el a frissítést, nem pedig közvetlenül az éles környezetben. Lásd: [az időponthoz tartozó visszaállítás végrehajtása](howto-restore-server-cli.md#server-point-in-time-restore).

1. Telepítse az [Azure CLI-t a Windowshoz](/cli/azure/install-azure-cli) , vagy használja az Azure CLI-t a [Azure Cloud Shell](../cloud-shell/overview.md) a frissítési parancsok futtatásához. 
 
   Ehhez a frissítéshez az Azure CLI 2.16.0 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. Futtassa az az version parancsot a telepített verzió és a függő kódtárak megkereséséhez. A legújabb verzióra az az upgrade paranccsal frissíthet.

2. A bejelentkezés után futtassa az az [MySQL Server upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) parancsot:

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   A parancssorban megjelenik a "-Running" üzenet. Miután ez az üzenet már nem jelenik meg, a verziófrissítés befejeződött.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Főverzió frissítése a MySQL 5,6-ről a MySQL 5,7-re az olvasási replika Azure Portal használatával

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL 5,6 olvasási replika kiszolgálót.

2. Az **Áttekintés** lapon kattintson a **frissítés** gombra az eszköztáron.

3. A **frissítés** szakaszban kattintson az **OK** gombra az Azure Database for MySQL 5,6 frissítéséhez, és olvassa el a replika-kiszolgálót a 5,7-kiszolgálóra.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL – áttekintés – frissítés":::

4. Egy értesítés megerősíti, hogy a frissítés sikeres volt.

5. Az **Áttekintés** lapon győződjön meg róla, hogy az Azure Database for MySQL olvasási replika-kiszolgáló verziója 5,7.

6. Most nyissa meg az elsődleges kiszolgálót, és [végezze el a verziófrissítést](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) .

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>A MySQL 5,6-ből a MySQL 5,7-re való minimális állásidő főverziójának frissítése olvasási replikák használatával

A MySQL 5,6-ről a MySQL 5,7-re minimális állásidőt lehet frissíteni, ha olvasási replikákat használ. Az ötlet a kiszolgáló olvasási replikájának frissítése a 5,7-es verzióra, és később az alkalmazás feladatátvétele az olvasási replikára mutat, és új elsődlegesnek kell lennie.

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL 5,6.

2. Hozzon létre egy [olvasási replikát](https://docs.microsoft.com/azure/mysql/concepts-read-replicas#create-a-replica) az elsődleges kiszolgálóról.

3. [Frissítse az olvasási replikát](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) az 5,7-es verzióra.

4. Miután megerősítette, hogy a replika-kiszolgáló a 5,7-es verzión fut, állítsa le az alkalmazást az elsődleges kiszolgálóhoz való csatlakozásra.
 
5. Ellenőrizze a replikálás állapotát, és győződjön meg arról, hogy a replika az elsődlegesen van-e, így az összes adatok szinkronban vannak, és gondoskodjon arról, hogy az elsődlegesen ne történjen új művelet.

   A [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) replikálási állapot megtekintéséhez hívja meg a parancsot a replika kiszolgálóján.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Ha az állapota `Slave_IO_Running` és `Slave_SQL_Running` a értéke "igen", és a "0" értékre van állítva `Seconds_Behind_Master` , a replikálás jól működik. `Seconds_Behind_Master` azt jelzi, hogy a replika milyen későn van. Ha az érték nem "0", az azt jelenti, hogy a replika frissítéseket dolgoz fel. Ha a megerősítés `Seconds_Behind_Master` értéke "0", akkor a replikáció leállítása biztonságos.

6. A [replikáció leállításával](https://docs.microsoft.com/azure/mysql/howto-read-replicas-portal#stop-replication-to-a-replica-server)előléptetheti az olvasási replikát az elsődlegesre.

7. Mutasson az alkalmazást az új elsődleges (korábbi replika) kiszolgálóra, amely a 5,7-es kiszolgálót futtatja. Minden kiszolgálón egyedi a kapcsolatok karakterlánca. Frissítse az alkalmazást, hogy a forrás helyett a (korábbi) replikára mutasson.

> [!Note]
> Ez a forgatókönyv csak a 4., 5. és 6. lépésben lesz állásidő.


## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Mikor fog ez a frissítés a GA verzióban, mivel a MySQL v 5.6 üzemi környezetben frissíteni kell a szolgáltatást?

A szolgáltatáshoz tartozó GA a MySQL v 5.6-os nyugdíjazás előtt lett tervezve. Azonban a szolgáltatás készen áll a gyártásra, és az Azure teljes mértékben támogatja, így biztosnak kell lennie az Ön környezetében. Ajánlott eljárásként javasoljuk, hogy először futtassa és tesztelje a kiszolgáló visszaállított példányán, hogy a frissítés során megbecsülje a állásidőt, és az alkalmazás-kompatibilitási teszt végrehajtása előtt futtassa az alkalmazást az éles környezetben. További információ: az [időponthoz kapcsolódó visszaállítás végrehajtása](howto-restore-server-portal.md#point-in-time-restore) a kiszolgáló egy adott időpontban történő másolásának létrehozásához. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>A kiszolgáló leállását okozhatja, és ha igen, mennyi ideig tart?

Igen, a kiszolgáló a frissítési folyamat során nem lesz elérhető, ezért javasoljuk, hogy ezt a műveletet a tervezett karbantartási időszakban hajtsa végre. A becsült állásidő az adatbázis méretétől, a kiépített tárolási mérettől, valamint az adatbázis tábláinak számától függ. A frissítési idő közvetlenül arányos a kiszolgálón lévő táblák számával. Az alapszintű SKU-kiszolgálók frissítése várhatóan hosszabb időt vesz igénybe, mivel a standard szintű tárolási platformon van. A kiszolgáló-környezet leállásának becsléséhez azt javasoljuk, hogy először hajtsa végre a frissítést a kiszolgáló visszaállított példányán.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Meg kell jegyezni, hogy a replika-kiszolgálón még nem támogatott. Mit jelent a konkrét?

A replika-kiszolgálók jelenleg nem támogatják a főverziók frissítését, ami azt jelenti, hogy a replikálásban részt vevő kiszolgálókhoz nem kell futtatnia (forrás-vagy replika-kiszolgáló). Ha tesztelni szeretné a replikálásban részt vevő kiszolgálók frissítését, mielőtt hozzáadja a replika támogatását a frissítési funkcióhoz, a következő lépéseket javasoljuk:

1. A tervezett karbantartás során [állítsa le a replikálást, és törölje a replika kiszolgálót](howto-read-replicas-portal.md) a név és az összes konfigurációs információ (a tűzfalbeállítások, a kiszolgálói paraméterek konfigurálása, ha az eltér a forráskiszolgálón) rögzítése után.
2. A forráskiszolgáló frissítésének elvégzése.
3. Hozzon létre egy új olvasási replika-kiszolgálót az 1. lépésben rögzített névvel és konfigurációs beállításokkal. Az új replika-kiszolgáló automatikusan a v 5.7-es verzióra kerül, miután a forráskiszolgáló frissítve lett a v 5.7-es verziójára.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Mi történik, ha nem választjuk a MySQL v 5.6-kiszolgáló frissítését a 2021. február 5. előtt?

Továbbra is futtathatja a MySQL v 5.6-kiszolgálót a korábban leírtak szerint. Az Azure **soha nem fogja** végrehajtani a kényszerített frissítést a kiszolgálón. A [Azure Database for MySQL verziószámozási szabályzatában](concepts-version-policy.md) ismertetett korlátozások azonban érvényesek lesznek.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure Database for MySQL verziószámozási szabályzatáról](concepts-version-policy.md).
